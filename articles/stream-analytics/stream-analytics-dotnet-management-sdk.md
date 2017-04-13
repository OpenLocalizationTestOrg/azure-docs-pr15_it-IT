<properties
    pageTitle="Gestione .NET SDK per Analitica flusso | Microsoft Azure"
    description="Guida introduttiva a flusso Analitica gestione .NET SDK. Informazioni su come configurare ed eseguire i processi analitica: creare un progetto, input, output e le trasformazioni."
    keywords=".NET SDK, analitica API"
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


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestione .NET SDK: Configurare ed eseguire i processi analitica utilizzando l'API di Azure flusso Analitica per .NET

Informazioni su come configurare un processi analitica Esegui tramite l'API di Analitica flusso per .NET mediante gestione .NET SDK. Configurare un progetto, creare inizio, trasformazioni e le origini di input e di output e interrompere processi. Per i processi di analitica, è possibile trasmettere dati dallo spazio di archiviazione Blob o da un hub di evento.

Vedere la [documentazione di riferimento management relativa all'API di Analitica flusso per .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure flusso Analitica è un servizio completamente gestito fornendo elaborazione dell'evento bassa latenza, elevata disponibilità, scalabilità e complesse sopra il flusso di dati nel cloud. Flusso Analitica consente agli utenti di configurare il flusso di processi per analizzare i flussi di dati e consente di unità vicino analitica in tempo reale.  


## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- Installare Visual Studio 2012 o 2013.
- Scaricare e installare [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Creare un gruppo di risorse Azure nell'abbonamento. Di seguito è riportato uno script di PowerShell Azure. Per informazioni di Azure PowerShell, vedere [installare e configurare Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Configurare un'origine di input e di destinazione di output da usare. Per ulteriori istruzioni vedere [Aggiungere input](stream-analytics-add-inputs.md) per configurare un input di esempio e [Aggiungere output](stream-analytics-add-outputs.md) per l'output di un esempio di configurazione.


## <a name="set-up-a-project"></a>Configurare un progetto

Per creare un processo analitica utilizzare l'API di Analitica flusso per .NET, è necessario configurare il progetto.

1. Creare un'applicazione di console di Visual Studio c# .NET.
2. Nella Console di gestione pacchetti, eseguire i comandi seguenti per installare i pacchetti NuGet. Il primo è Azure flusso Analitica gestione .NET SDK. Il secondo è il client di Azure Active Directory che verrà utilizzato per l'autenticazione.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Aggiungere la sezione **appSettings** seguente al file App:

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Sostituire i valori per **SubscriptionId** e **ActiveDirectoryTenantId** con l'abbonamento Azure e ID del tenant. È possibile ottenere questi valori eseguendo il seguente cmdlet di PowerShell Azure:

        Get-AzureAccount

5. Aggiungere le seguenti istruzioni **using** al file di origine (Program.cs) nel progetto:

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Aggiungere un metodo di autenticazione supporto:

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


## <a name="create-a-stream-analytics-management-client"></a>Creare un client di gestione Analitica flusso

Un oggetto **StreamAnalyticsManagementClient** consente di gestire il processo e i componenti di processo, ad esempio input, output e trasformazione.

Aggiungere il codice seguente all'inizio del metodo **principali** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Valore della variabile **resourceGroupName** deve essere uguale al nome del gruppo di risorse è stato creato o scelto le operazioni preliminari.

Per automatizzare l'aspetto della presentazione di credenziali di creazione del processo, fare riferimento a [un'identità di servizio Gestione risorse Azure di autenticazione](../resource-group-authenticate-service-principal.md).

Le ultime sezioni di questo articolo presuppongono che questo codice si trova all'inizio del metodo **Main** .

## <a name="create-a-stream-analytics-job"></a>Creare un processo di flusso Analitica

Il codice seguente crea un processo di flusso Analitica sotto il gruppo di risorse che sono state definite. Si aggiungerà una input, output e trasformazione al processo in un secondo momento.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Creare un'origine di input flusso Analitica

Il codice seguente crea un'origine di input flusso Analitica con il tipo di origine di input blob e serializzazione CSV. Per creare un'origine di input hub eventi, utilizzare **EventHubStreamInputDataSource** anziché **BlobStreamInputDataSource**. Analogamente, è possibile personalizzare il tipo di serializzazione di origine di input.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Origini di input, da archiviazione Blob o un hub evento sono collegate a un processo specifico. Per utilizzare la stessa origine di input per diversi processi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.


## <a name="test-a-stream-analytics-input-source"></a>Verifica di un'origine di input flusso Analitica

Il metodo **TestConnection** verifica se il processo di flusso Analitica è in grado di connettersi all'origine di input e su altri aspetti specifici per il tipo di origine di input. Ad esempio nell'origine input blob che è stato creato in precedenza, il metodo verifica che il nome dell'account di archiviazione e coppia può essere utilizzato per connettersi all'account di archiviazione, nonché verificare che il contenitore specificato esista.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Creare un flusso Analitica di output di destinazione

Creazione di una destinazione di output è molto simile alla creazione di un'origine di input flusso Analitica. Come origini di input, destinazioni di output sono collegate a un processo specifico. Per utilizzare la stessa destinazione di output per diversi processi, è necessario chiamare nuovamente il metodo e specificare un nome diverso.

Il codice seguente crea una destinazione di output (database di SQL Azure). È possibile personalizzare il tipo di dati di destinazione di output e/o il tipo di serializzazione.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Verificare una destinazione di output Analitica flusso

Una destinazione di output flusso Analitica è installato anche il metodo **TestConnection** per la verifica delle connessioni.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Creare una trasformazione Analitica flusso

Il codice seguente crea una trasformazione flusso Analitica con la query "selezionare * dall'Input" e specifica per assegnare un'unità di flusso del processo di flusso Analitica. Per ulteriori informazioni sulla regolazione unità di trasmissione, vedere [processi scala Azure flusso Analitica](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Come input e di output, un trasformazioni è associato anche a processo Analitica flusso specifico a cui è stato creato in.

## <a name="start-a-stream-analytics-job"></a>Avviare un processo di flusso Analitica
Dopo aver creato un processo di flusso Analitica e il controllo output degli e trasformazione, è possibile avviare il processo chiamando il metodo **Start** .

Nell'esempio seguente viene impostato un processo di flusso Analitica con un'ora di inizio output personalizzato a 12 dicembre 2012 12:12:12 di avvio del codice UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Interrompere un processo di flusso Analitica
È possibile interrompere un processo flusso Analitica in esecuzione chiamando il metodo **Stop** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminare un processo di flusso Analitica
Il metodo **Delete** eliminerà il processo, nonché le risorse secondari sottostante, inclusi controllo, output degli e trasformazione del processo.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Ottenere supporto
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

Sono state eseguite le nozioni di base dell'uso di .NET SDK per creare ed eseguire processi analitica. Per ulteriori informazioni, vedere gli articoli seguenti:

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Flusso azure Analitica gestione .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
