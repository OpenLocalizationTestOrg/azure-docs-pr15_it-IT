<properties
    pageTitle="Analizzare i registri diagnostici Azure con registro Analitica | Microsoft Azure"
    description="Registro Analitica leggere i log da Azure servizi che scrivere Azure registri diagnostici per l'archiviazione in formato JSON blob."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analizzare i registri diagnostici Azure utilizzando Analitica Log

Registro Analitica può raccogliere i registri per i seguenti servizi Azure in cui scrivere archiviazione blob nel formato JSON [Azure log diagnostici](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) :

+ Automazione (Preview)
+ Archivio chiave (Preview)
+ Gateway di applicazioni (Preview)
+ Gruppo di sicurezza di rete (Preview)

Nelle sezioni seguenti vengono scorrere tramite PowerShell per:

+ Configurare Analitica Log per raccogliere i registri dallo spazio di archiviazione per ogni risorsa  
+ Attivare la soluzione Analitica Log per il servizio di Azure

Prima Analitica Log è possibile raccogliere dati per le risorse, è necessario attivare diagnostica Windows Azure. Utilizzare la `Set-AzureRmDiagnosticSetting` cmdlet per attivare la registrazione.

Vedere gli articoli seguenti per ulteriori informazioni su come attivare la registrazione diagnostica:

+ [Archivio di chiave](../key-vault/key-vault-logging.md)
+ [Gateway di applicazioni](../application-gateway/application-gateway-diagnostics.md)
+ [Gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md)

Questa documentazione include anche informazioni dettagliate su:

+ Risoluzione dei problemi di raccolta dati
+ Interruzione della raccolta di dati

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurare Analitica Log per raccogliere i registri diagnostici Azure

Raccogliere i log per i servizi e abilitare la soluzione visualizzare i registri vengono eseguite mediante gli script di PowerShell.

Nell'esempio seguente consente di accedere a tutte le risorse supportate

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Per alcune risorse, è possibile eseguire la configurazione precedente dal portale di Azure seguendo i passaggi illustrati in [Panoramica di Azure registri diagnostici](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurare Analitica Log per raccogliere i registri diagnostici Azure

Sono disponibili un modulo di script di PowerShell che consente di esportare due cmdlet per agevolare la configurazione Analitica Log:

1. `Add-AzureDiagnosticsToLogAnalyticsUI`viene richiesto di input ed è in grado di configurare le configurazioni semplice
2. `Add-AzureDiagnosticsToLogAnalytics`accetta le risorse per controllare come input e quindi Configura Analitica Log  

### <a name="pre-requisites"></a>Prerequisiti

1. Azure PowerShell con versione 1.0.8 o versioni successive di cmdlet approfondimenti operativi.
  - [Come installare e configurare PowerShell Azure](../powershell-install-configure.md)
  - Verificare la versione di cmdlet:`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Registrazione diagnostica è configurata per la risorsa Azure che si desidera eseguire il monitoraggio. Usare `Set-AzureRmDiagnosticSetting` o fare riferimento a [Usare Log Analitica per raccogliere dati da account Azure dello spazio di archiviazione](log-analytics-azure-storage.md) per la procedura attivare la diagnostica.
3. Un'area di lavoro [Analitica Log](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. Il modulo AzureDiagnosticsAndLogAnalytics PowerShell
  - Scaricare il modulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) da una raccolta di PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Opzione 1: Eseguire gli script di configurazione interattivi

Aprire PowerShell ed eseguire:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Utente è visualizzato un elenco di opzioni disponibili e assegnato una richiesta di effettuare una selezione.
Viene chiesto di effettuare selezioni per ognuna delle operazioni seguenti:

+ Tipi di risorse (servizi di Windows Azure) per raccogliere i registri da
+ Istanze delle risorse per raccogliere i registri da
+ Area di lavoro Analitica log per raccogliere i dati

Dopo l'esecuzione di script, verrà visualizzato record nel Log Analitica circa 30 minuti dopo la creazione di nuovi dati diagnostici sono allo spazio di archiviazione. Se i record non sono disponibili dopo questa volta fare riferimento alla sezione sulla risoluzione dei problemi.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opzione 2: Creare un elenco delle risorse e passare i cmdlet di configurazione

È possibile creare un elenco delle risorse che dispongono di diagnostica Windows Azure abilitata e quindi passare le risorse per il cmdlet di configurazione.

È possibile visualizzare informazioni aggiuntive sul cmdlet eseguendo `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Per trovare altri dettagli su OMS [Dei cmdlet di PowerShell Analitica Log](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Se risorse e area di lavoro si trovano in varie sottoscrizioni di Azure, passare a altra in base alle esigenze di utilizzo`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Dopo l'esecuzione di script, verrà visualizzato record nel Log Analitica circa 30 minuti dopo la creazione di nuovi dati diagnostici sono allo spazio di archiviazione. Se i record non sono disponibili dopo questa volta fare riferimento alla sezione sulla risoluzione dei problemi.  

>[AZURE.NOTE] La configurazione non è visibile nel portale di Azure. È possibile verificare la configurazione utilizzando il `Get-AzureRmOperationalInsightsStorageInsight` cmdlet.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Interruzione Analitica Log dalla raccolta registri diagnostici Azure

Per eliminare la configurazione di Analitica Log per una risorsa, utilizzare la `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Risoluzione dei problemi di configurazione per Azure registri diagnostici

*Problema*

Quando si configura una risorsa che si sta connettendo ad archiviazione classico, viene visualizzato l'errore seguente:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Risoluzione*

Accedere all'API per il modello di distribuzione classica con`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Risoluzione dei problemi di raccolta di dati di Azure registri diagnostici

Se non si visualizzano dati per la risorsa Azure nel Log Analitica, è possibile usare le procedure di risoluzione dei problemi seguenti:

+ Verificare i dati a account di archiviazione
+ Verificare che sia abilitata la soluzione Analitica Log per il servizio
+ Verificare che i Log Analitica sia configurato per leggere dallo spazio di archiviazione
+ Aggiornare la chiave account lo spazio di archiviazione

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Verificare dati scorre per l'account di archiviazione

È possibile controllare se dati scorre nell'account di archiviazione con uno strumento che consente l'esplorazione di archiviazione Azure (ad esempio Visual Studio) o tramite PowerShell.

Per trovare l'Account di archiviazione la risorsa è configurata per accedere per l'utilizzo di PowerShell seguente:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Il contenitore di spazio di archiviazione utilizzato dalla diagnostica Azure ha un nome con un formato di:  

`insights-logs-<Category>`

Fare riferimento all' [uso dei cmdlet di spazio di archiviazione per verificare un contenitore per i dati più recenti](../storage/storage-powershell-guide-full.md) per ulteriori informazioni su come visualizzare il contenuto dell'account di archiviazione.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Verificare che sia abilitata la soluzione Analitica Log per il servizio

Se si usa `Add-AzureDiagnosticsToLogAnalyticsUI`, la soluzione Log Analitica corretta automaticamente è abilitata per l'utente.

Per verificare se è abilitata una soluzione, eseguire PowerShell seguente:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Se la soluzione non è attivata, è possibile abilitare l'utilizzo di PowerShell seguente:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Per trovare il nome della soluzione per abilitare per ogni tipo di risorsa, usare PowerShell seguente (questa variabile è disponibile dopo aver importato il modulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Verificare che i Log Analitica sia configurato per leggere dallo spazio di archiviazione

Se si aggiungono ulteriori risorse di Azure, è necessario attivare la registrazione diagnostica per poterli e configurare Analitica Log per loro.
Per verificare quali risorse e gli account di archiviazione Analitica Log è configurato per raccogliere i registri per usare PowerShell seguente:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Aggiornare la chiave di spazio di archiviazione

Se si cambia la chiave per l'account di archiviazione, la configurazione di registro Analitica anche deve essere aggiornato con la nuova chiave.
È possibile aggiornare la configurazione di registro Analitica con una nuova chiave tramite PowerShell seguente:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Per trovare il nome di comprendere l'archiviazione, usare il `Get-AzureRmOperationalInsightsStorageInsight` cmdlet, come illustrato negli esempi precedenti.

## <a name="next-steps"></a>Passaggi successivi

- [Archiviazione blob di utilizzo per IIS e di archiviazione per gli eventi](log-analytics-azure-storage-iis-table.md) per leggere i log per Azure servizi tale diagnostica di scrittura per lo spazio di archiviazione tabella o ai registri IIS scritti nell'archiviazione blob.
- [Abilitare le soluzioni](log-analytics-add-solutions.md) per approfondire i dati.
- [Utilizzare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.
