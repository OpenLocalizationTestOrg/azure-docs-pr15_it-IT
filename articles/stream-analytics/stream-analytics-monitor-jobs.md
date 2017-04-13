<properties
    pageTitle="Monitorare i processi nel flusso Analitica a livello di programmazione | Microsoft Azure"
    description="Informazioni su come eseguire il monitoraggio a livello di programmazione processi flusso Analitica creati tramite API REST, Azure SDK o Powershell."
    keywords=".NET monitor, monitor processi, monitoraggio app"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Creare un monitor processi flusso Analitica
 In questo articolo viene illustrato come attivare il monitoraggio di un processo di flusso Analitica. Analitica flusso processi creati tramite API REST, Azure SDK o Powershell eseguire non ha il monitoraggio attivato per impostazione predefinita.  È possibile attivare manualmente questa nel portale di Azure, passare alla pagina di monitoraggio del processo e fare clic sul pulsante Abilita o è possibile automatizzare il processo eseguendo la procedura descritta in questo articolo. I dati di monitoraggio verranno visualizzato nella scheda "Monitor" nel portale di Azure per il processo di flusso Analitica.

![monitor processi scheda processi](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- Visual Studio 2012 o 2013.
- Scaricare e installare [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Un processo Analitica flusso esistente che deve essere di monitoraggio.

## <a name="setup-a-project"></a>Configurazione di un progetto

1.  Creare un'applicazione console Visual Studio c# .net.
2.  Nella Console di gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è Azure flusso Analitica gestione .NET SDK. Il secondo è il Monitor Azure SDK che verrà utilizzato per abilitare il monitoraggio. L'ultimo è il client di Azure Active Directory che verrà utilizzato per l'autenticazione.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Aggiungere la sezione appSettings seguente al file app.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Sostituire i valori per *SubscriptionId* e *ActiveDirectoryTenantId* con l'abbonamento Azure e ID del tenant. È possibile ottenere questi valori eseguendo il seguente cmdlet di PowerShell:

    ```
    Get-AzureAccount
    ```
4.  Aggiungere le seguenti istruzioni using al file di origine (Program.cs) nel progetto.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Aggiungere un metodo di supporto di autenticazione.

        public static string GetAuthorizationHeader()
            {
                AuthenticationResult result = null;
                var thread = new Thread(() =>
                {
                    try
                    {
                        var context = new AuthenticationContext(
                            ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                            ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                        result = context.AcquireToken(
                            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                            clientId: ConfigurationManager.AppSettings["AsaClientId"],
                            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                            promptBehavior: PromptBehavior.Always);
                    }
                    catch (Exception threadEx)
                    {
                        Console.WriteLine(threadEx.Message);
                    }
                });

                thread.SetApartmentState(ApartmentState.STA);
                thread.Name = "AcquireTokenThread";
                thread.Start();
                thread.Join();

                if (result != null)
                {
                    return result.AccessToken;
                }

                throw new InvalidOperationException("Failed to acquire token");
        }

## <a name="create-management-clients"></a>Creazione di gestione dei clienti
Il codice seguente configurerà le necessarie variabili e i client di gestione.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Attivare il monitoraggio per un processo Analitica flusso esistente

Il codice seguente consentirà il monitoraggio di un processo Analitica flusso **esistente** . La prima parte del codice esegue una richiesta GET in base al servizio flusso Analitica per recuperare le informazioni sul processo di flusso Analitica specifico. Viene utilizzata la proprietà di "Id" (recuperata dalla richiesta GET) come parametro per il metodo di caricamento nella seconda metà del codice che invia un caricamento richiedere al servizio approfondimenti per abilitare il monitoraggio del processo di flusso Analitica.

> [AZURE.WARNING]
> Se dopo avere attivato il monitoraggio di un processo di flusso Analitica diverso, tramite il portale Azure o a livello di programmazione tramite il seguente codice, **si consiglia di specificare lo stesso nome di account di archiviazione che l'utente quando è stato attivato in precedenza monitoraggio.**
>
> Account di archiviazione è collegato all'area è stato creato il processo di flusso Analitica in, non specificamente per l'operazione.
>
> Tutti Analitica flusso del processo e tutte le altre risorse Azure in tale area stesso condividano l'account di archiviazione per archiviare dati monitoraggio. Se si specifica un account di archiviazione diverso, è possibile che conseguenze impreviste di controllo dei processi flusso Analitica e/o altre risorse Azure.
>
> Il nome dell'account di archiviazione utilizzato per sostituire ```“<YOUR STORAGE ACCOUNT NAME>”``` sotto deve essere un account di archiviazione è nello stesso abbonamento Analitica flusso di lavoro si desidera abilitare il monitoraggio delle.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
