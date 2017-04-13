<properties
    pageTitle="Usare PowerShell per creare e configurare un'area di lavoro Analitica Log | Microsoft Azure"
    description="Registrare i dati ottimali Analitica da server le in locale o nel cloud infrastruttura. È possibile raccogliere dati computer dallo spazio di archiviazione Azure quando generato da diagnostica Windows Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Gestire i Log Analitica tramite PowerShell

È possibile utilizzare [cmdlet di PowerShell Analitica Log] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) eseguire varie funzioni in Log Analitica dalla riga di comando o all'interno di uno script.  Esempi di attività che è possibile eseguire con PowerShell:

+ Creare un'area di lavoro
+ Aggiungere o rimuovere una soluzione
+ Importare ed esportare ricerche salvate
+ Creare un gruppo di computer
+ Attivare la raccolta dei registri di IIS dal computer con installato l'agente di Windows
+ Raccogliere contatori da computer Linux e Windows
+ Raccogliere eventi da Registro di sistema in un computer Linux 
+ Raccogliere eventi da registri eventi di Windows
+ Raccogliere i registri eventi personalizzati
+ Aggiungere l'agente di analitica log una macchina virtuale Azure
+ Configurare analitica log indicizzare i dati raccolti mediante diagnostica Windows Azure


Questo articolo offre due esempi di codice che illustrano alcune funzioni che è possibile eseguire da PowerShell.  È possibile fare riferimento a [riferimento sui cmdlet di PowerShell Analitica Log] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) per altre funzioni.

> [AZURE.NOTE] Registro Analitica è stato chiamato in precedenza informazioni approfondite operativa, perché è il nome utilizzato nei cmdlet.

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare PowerShell con l'area di lavoro Log Analitica, è necessario disporre di:

+ Un abbonamento Azure, e 
+ L'area di lavoro di Azure Log Analitica collegato al proprio abbonamento Azure.

Se creata un'area di lavoro OMS, ma non ancora collegarla a un abbonamento a Azure è possibile creare il collegamento:

+ Nel portale di Azure
+ Nel portale di OMS o 
+ Utilizzare il cmdlet Get-AzureRmOperationalInsightsLinkTargets e AzureRmOperationalInsightsWorkspace di nuovo.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Creare e configurare un'area di lavoro Analitica Log

Nell'esempio di script seguente viene illustrato come:

1.  Creare un'area di lavoro
2.  Elencare le soluzioni disponibili
3.  Aggiungere le soluzioni nell'area di lavoro
4.  Ricerche nella importazione salvata
5.  Ricerche di esportazione salvata
6.  Creare un gruppo di computer
7.  Attivare la raccolta dei registri di IIS dal computer con installato l'agente di Windows
8.  Raccogliere contatori delle prestazioni del disco logico da computer Linux (% Inodes utilizzato; Gratuito megabyte; % Utilizzato uno spazio. Trasferimenti disco/sec; Lettura disco/sec; Scrittura su disco/secondo)
9.  Raccogliere gli eventi di registro di sistema da computer Linux
10. Raccogliere eventi errori e avvisi il registro eventi applicazione dal computer Windows
11. Raccogliere contatore delle prestazioni MB di memoria disponibili da computer Windows
12. Raccogliere un log personalizzato 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configurazione Log Analitica indicizzare diagnostica Windows Azure 

Per senza agenti il monitoraggio delle risorse Azure, è necessario disporre di diagnostica Windows Azure abilitato e configurato per la scrittura di un account di archiviazione le risorse. Registro Analitica possa essere configurati per raccogliere i registri dagli account di archiviazione. Risorse che è necessario eseguire la configurazione precedente per include:

+ Servizi cloud classico (ruoli web e di lavoro)
+ Cluster tessuti Service
+ Gruppi di sicurezza di rete
+ Tasto archivi e 
+ Gateway per applicazioni

È anche possibile usare PowerShell per configurare un'area di lavoro Analitica Log in una sottoscrizione Azure per raccogliere i registri dagli abbonamenti Azure diversi.

Nell'esempio seguente viene illustrato come:

1.  Elencare i account esistenti dello spazio di archiviazione e le posizioni che Log Analitica indicizza dati da
2.  Creare una configurazione per leggere da un account di archiviazione
3.  Aggiornare la configurazione appena creata indicizzare i dati da altri percorsi
4.  Eliminare la configurazione appena creata

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Passaggi successivi

- [Cmdlet per la revisione Log Analitica PowerShell](http://msdn.microsoft.com/library/mt188224.aspx) per ulteriori informazioni sull'utilizzo di PowerShell per la configurazione di registro Analitica.

