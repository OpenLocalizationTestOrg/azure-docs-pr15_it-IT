<properties
    pageTitle="Azure monitoraggio procedura dettagliata API REST | Microsoft Azure"
    description="Come le richieste di autenticazione e usare l'API REST monitoraggio di Azure."
    authors="mcollier, rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure monitoraggio procedura dettagliata API REST
In questo articolo viene illustrato come eseguire l'autenticazione in modo che il codice può utilizzare il [Riferimento all'API REST di Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

API del Monitor Azure consente di recuperare a livello di programmazione le definizioni metriche predefiniti disponibili (il tipo di unità di misura metriche, ad esempio il tempo di CPU, le richieste e così via), granularità e i valori metrici. Dopo avere recuperato, salvare i dati in un archivio di dati separato, ad esempio Database SQL Azure, DocumentDB o Lake di dati di Azure. Da qui un'ulteriore analisi può essere eseguita in base alle esigenze.

Oltre a funzionare con diverse metriche coordinate, come illustrato di seguito in questo articolo, l'API del Monitor consente visualizzare un elenco di regole di avviso, Visualizza i registri di attività e molto altro ancora. Per un elenco completo delle operazioni disponibili, vedere la [Guida di riferimento API REST di Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Azure monitoraggio delle richieste di autenticazione

Il primo passaggio consiste nel autenticare la richiesta.

Tutte le attività eseguite l'API del Monitor Azure utilizzano il modello di autenticazione Manager delle risorse di Azure. Di conseguenza, tutte le richieste devono essere autenticate con Azure Active Directory (Azure Active Directory). Un approccio per l'autenticazione dell'applicazione client consiste nel creare un annuncio Azure principale del servizio e recuperare il token di autenticazione (JWT). Script di esempio seguente viene illustrata la creazione di un servizio di Azure Active Directory principale tramite PowerShell. Per una panoramica più dettagliata, fare riferimento alla documentazione [sull'uso di Azure PowerShell per creare un servizio principale per accedere alle risorse](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). È anche possibile [creare un principio servizio tramite il portale Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Per eseguire una query API del Monitor Azure, l'applicazione client deve utilizzare il capitale servizio creato in precedenza per eseguire l'autenticazione. Nell'esempio seguente script di PowerShell Mostra un approccio, [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) per aiutare a ottenere l'autenticazione JWT token. Token JWT viene passato come parte di un parametro di autorizzazione HTTP nelle convocazioni all'API REST Monitor Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Una volta completato il passaggio di configurazione di autenticazione, è quindi possibile eseguire query rispetto all'API REST di Monitor Azure. Esistono due query utili:

1. Elencare le definizioni metriche per una risorsa

2. Recuperare i valori delle metriche


## <a name="retrieve-metric-definitions"></a>Recuperare le definizioni metriche
>[AZURE.NOTE] Per recuperare le definizioni metriche tramite l'API REST di Azure Monitor, utilizzare "2016-03-01" con la versione dell'API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Per un'App di logica di Azure, le definizioni metriche verrebbe visualizzato simile alla schermata riportata di seguito:

![ALT "Visualizza JSON della risposta definizione metriche".](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Per ulteriori informazioni, vedere la documentazione per [elencare le definizioni metriche per una risorsa in API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperare i valori delle metriche
Una volta le definizioni di metriche disponibili sono note, è possibile recuperare i valori delle metriche correlati. Utilizzo nome "value" dell'unità di misura metriche (non ' localizedValue') per tutte le richieste di filtro (ad esempio, recuperare i dati metriche 'CpuTime' e 'Richiede'). Se non viene specificato alcun filtro, viene restituito l'unità di misura metriche predefinito.

>[AZURE.NOTE] Per recuperare valori metriche tramite l'API REST di Azure Monitor, utilizzare "2016-06-01" con la versione dell'API.

**Metodo**: GET

**Richiedere URI**: https://management.azure.com/subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/_{nomi di provider di risorse}_/_{tipo di risorsa}_//providers/microsoft.insights/metrics?$filter=_{nome di risorsa}__{filtro}_& versione api =_{apiVersion}_

Ad esempio, per recuperare i dati metriche RunsSucceeded per l'intervallo di tempo specifico e per un livello di tempo di 1 ora, la richiesta dovrebbero essere come indicato di seguito:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Il risultato sarà visualizzato simile all'esempio seguente schermata:

![ALT "Risposta JSON con valore metrico tempo medio di risposta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Per recuperare più punti di dati o aggregazione, aggiungere i nomi di definizione metriche e tipi di aggregazione per il filtro, come illustrato nell'esempio seguente:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Utilizzare ARMClient
In alternativa all'utilizzo di PowerShell (come illustrato sopra), consiste nell'usare [ARMClient](https://github.com/projectkudu/ARMClient) sul proprio computer Windows. ARMClient gestisce automaticamente l'autenticazione di Azure Active Directory (e token JWT risultante). Di seguito sono utilizzo di ARMClient per il recupero dei dati metrici:

1. Installare [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/ARMClient).

2. In una finestra, digitare _armclient.exe login_. Questo viene richiesto di accedere a Azure.

3. Tipo di _armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Tipo di _armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT "ARMClient Using per l'uso con il monitoraggio Azure API REST"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Recuperare l'ID risorsa
Tramite l'API REST possibile molto utile per comprendere le definizioni disponibili metriche, granularità e i valori correlati. Queste informazioni sono utile quando si utilizza la [Raccolta di gestione di Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Per il codice precedente, l'ID risorsa da utilizzare è il percorso completo per la risorsa Azure desiderata. Ad esempio, per eseguire una query su un'App Web di Azure, dovrebbero essere ID risorsa:

*/Subscriptions/{Subscription-ID}/resourceGroups/{Resource-Group-name}/providers/Microsoft.Web/Sites/{Site-Name}/*

Nell'elenco seguente sono riportati alcuni esempi di formati di ID delle risorse per diverse risorse Azure:

* **IoT Hub** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.Devices/IotHubs/_{nome di hub iot}_

* **Pool SQL flessibile** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.Sql/servers/_{pool db}_/elasticpools/_{nome pool sql}_

* **Database SQL (v12)** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.Sql/servers/_{nome server}_/databases/_{nome database}_

* **Servizio Bus** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.ServiceBus/_{spazio dei nomi}_/_{servicebus nome}_

* **Macchine Virtuali scala set** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{macchine virtuali nome}_

* **Macchine virtuali** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.Compute/virtualMachines/_{macchine virtuali nome}_

* **Hub evento** - /subscriptions/_{id abbonamento}_/resourceGroups/_{nome gruppo risorsa}_/providers/Microsoft.EventHub/namespaces/_{eventhub spazio dei nomi}_


Sono disponibili soluzioni alternative per il recupero di ID delle risorse, incluso l'utilizzo di Esplora risorse Azure, visualizzare la risorsa desiderata nel portale di Azure e tramite PowerShell o CLI Azure.

### <a name="azure-resource-explorer"></a>Esplora risorse Azure
Per trovare l'ID di risorsa per la risorsa desiderata, una soluzione utile consiste nell'usare lo strumento di [Esplora risorse Azure](https://resources.azure.com) . Passare alla risorsa desiderata e quindi osservare l'ID indicato, come illustrato nella schermata riportata di seguito:

![ALT "Risorsa Azure Esplora risorse"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portale di Azure
ID risorsa può anche essere ottenuto dal portale di Azure. A tale scopo, passare alla risorsa desiderata e quindi scegliere Proprietà. ID risorsa viene visualizzato in e il proprietà, come illustrato nella schermata seguente:

![ALT "ID risorsa visualizzato e il proprietà nel portale di Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>PowerShell Azure
ID risorsa può essere recuperato tramite anche dei cmdlet di PowerShell Azure. Ad esempio, per ottenere l'ID di risorsa per un'App Web di Azure, eseguire il cmdlet Get-AzureRmWebApp, come illustrato nella schermata riportata di seguito:

![ALT "ID risorsa ottenuto tramite PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>CLI Azure
Per recuperare l'ID risorsa usando CLI Azure, eseguire il comando 'azure Web App Mostra' che specifica il '-json' opzione, come illustrato nella schermata seguente:

![ALT "ID risorsa ottenuto tramite PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Recuperare i dati del Registro di attività
Oltre a lavorare con le definizioni metriche e i valori correlati, è anche possibile recuperare le informazioni supplementari interessanti relativi alle risorse Azure. Ad esempio, è possibile eseguire query sui dati [registro attività](https://msdn.microsoft.com/library/azure/dn931934.aspx) . Nell'esempio seguente viene illustrato come utilizzare l'API REST Monitor Azure eseguire query sui dati log attività all'interno di un intervallo di date specifico per una sottoscrizione Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Passaggi successivi
* Esaminare la [Panoramica del controllo](monitoring-overview.md).
* Visualizzare le [metriche di supportati con Azure Monitor](monitoring-supported-metrics.md).
* Esaminare il [Riferimento all'API REST di Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Esaminare la [raccolta gestione Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
