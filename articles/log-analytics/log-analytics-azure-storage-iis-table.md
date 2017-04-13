<properties
    pageTitle="Uso archiviazione blob per lo spazio di archiviazione IIS e una tabella per gli eventi | Microsoft Azure"
    description="Registro Analitica possibile leggere i log per i servizi di Azure scritto diagnostica allo spazio di archiviazione tabella o i registri di IIS scritti nell'archiviazione blob."
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
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Utilizzo di archiviazione blob per IIS e lo spazio di archiviazione tabella per gli eventi

Registro Analitica possibile leggere i log per i servizi seguenti scrivere diagnostica allo spazio di archiviazione tabella o ai registri IIS scritti nell'archiviazione blob:

+ Servizio tessuti cluster (Preview)
+ Macchine virtuali
+ Ruoli di Web/lavoro

Prima Analitica Log è possibile raccogliere dati per le risorse, è necessario attivare diagnostica Windows Azure.

Dopo aver diagnostica è attivata, è possibile utilizzare il portale di Azure o PowerShell configurare Analitica Log per raccogliere i registri.

Diagnostica Windows Azure è un'estensione Azure che consente di raccogliere dati di diagnostica da un ruolo di lavoro, un ruolo web o una macchina virtuale in esecuzione in Azure. I dati archiviati in un account di archiviazione Azure e quindi possono essere raccolti dal Log Analitica.

Per Analitica Log raccogliere i registri di diagnostica di Azure, i registri devono essere nei seguenti percorsi:

| Tipo di log | Tipo di risorsa | Posizione |
| -------- | ------------- | -------- |
| Registri di IIS | Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro | registri di iis tampone (archiviazione Blob) |
| Registro di sistema | Macchine virtuali | LinuxsyslogVer2v0 (tabella lo spazio di archiviazione) |
| Servizio tessuti operativi eventi | Nodi tessuti servizio | WADServiceFabricSystemEventTable |
| Servizio tessuti attore affidabile eventi | Nodi tessuti servizio | WADServiceFabricReliableActorEventTable |
| Servizio tessuti Service affidabile eventi | Nodi tessuti servizio | WADServiceFabricReliableServiceEventTable |
| Registri eventi di Windows | Nodi tessuti servizio <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro | WADWindowsEventLogsTable (spazio di archiviazione tabella) |
| Registri di Windows ETW | Nodi tessuti servizio <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro | WADETWEventTable (spazio di archiviazione tabella) |

>[AZURE.NOTE] I registri di IIS da siti Web di Azure non sono attualmente supportati.

Per macchine virtuali, si ha la possibilità di installazione dell' [agente Analitica Log](log-analytics-azure-vm-extension.md) al computer virtuale per abilitare le informazioni supplementari. Oltre alla possibilità di analizzare i registri di IIS e registri eventi, è possibile eseguire ulteriori analisi incluso il rilevamento delle modifiche di configurazione, valutazione SQL e la valutazione di aggiornamento.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Abilitare diagnostica Windows Azure in un computer virtuale per registro eventi e IIS registro insieme

Utilizzare la procedura seguente per abilitare diagnostica Windows Azure in un computer virtuale per la raccolta log registro eventi e IIS tramite il portale di Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Per abilitare diagnostica Windows Azure in una macchina virtuale con il portale di Azure

1. Installare l'agente di macchine Virtuali quando si crea una macchina virtuale. Se la macchina virtuale esiste già, verificare che l'agente di macchine Virtuali sia già installato.
    - Nel portale di Azure, passare alla macchina virtuale, selezionare **Configurazione facoltativa**e quindi **diagnostica** e **lo stato** è impostato su **On**.

    Al termine, la macchina virtuale ha l'estensione di Azure diagnostica installato e in esecuzione. Questa estensione è responsabile per la raccolta di dati di diagnostica.

2. Attivare il monitoraggio e configurare il registro degli eventi su una macchina virtuale esistente. È possibile abilitare diagnostica a livello di macchine Virtuali. Per attivare la diagnostica e quindi configurare la registrazione degli eventi, procedere come segue:
    1. Selezionare la macchina virtuale.
    2. Fare clic su **monitoraggio**.
    3. Fare clic su **diagnostica**.
    4. Impostare lo **stato** su **attivato**.
    5. Selezionare ogni registro di diagnostica che si desidera raccogliere.
    7. Fare clic su **OK**.

Utilizzo di PowerShell Azure è possibile specificare con maggiore precisione gli eventi che vengono scritte allo spazio di archiviazione Azure. Fare riferimento alla [raccolta dati tramite Diagnostica Windows Azure scritta in archiviazione tabella o un log IIS scrivere blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Abilitare diagnostica Windows Azure in un ruolo Web per insieme di eventi e log IIS

[Come per abilitare diagnostica in un servizio Cloud](../cloud-services/cloud-services-dotnet-diagnostics.md) per vedere i passaggi generali sull'attivazione di diagnostica Windows Azure. Le istruzioni seguenti usare queste informazioni e personalizzarlo per l'utilizzo con registro Analitica.

Con diagnostica Windows Azure attivata:

- I registri di IIS sono archiviati per impostazione predefinita, i dati di registro trasferiti nell'intervallo di trasferimento scheduledTransferPeriod.
- I registri eventi di Windows non vengono trasferite per impostazione predefinita.

### <a name="to-enable-diagnostics"></a>Per attivare la diagnostica

Per attivare i registri eventi di Windows, oppure per modificare il scheduledTransferPeriod, configurare la diagnostica Azure utilizzando il file di configurazione XML (diagnostics.wadcfg), come illustrato nella [passaggio 4: creare il file di configurazione di diagnostica e installare l'estensione](../cloud-services/cloud-services-dotnet-diagnostics.md)

File di configurazione di esempio seguente raccoglie registri IIS e tutti gli eventi da registri applicazioni e sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assicurarsi che il ConfigurationSettings specifica un account di archiviazione, come illustrato nell'esempio seguente:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Nel portale di Azure il dashboard di account di archiviazione in gestire i tasti di scelta vengono trovati i valori di **nome account** e **AccountKey** . Il protocollo per la stringa di connessione deve essere **https**.

Dopo la configurazione di diagnostica aggiornata viene applicata al servizio cloud e sta scrivendo diagnostica lo spazio di archiviazione di Azure, si è pronti per configurare Log Analitica.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usare il portale di Azure per raccogliere i registri dallo spazio di archiviazione di Azure

È possibile utilizzare il portale di Azure per configurare Analitica Log per raccogliere i registri per i servizi di Azure seguenti:

+ Servizio tessuti cluster
+ Macchine virtuali
+ Ruoli di Web/lavoro

Nel portale di Azure, passare all'area di lavoro Analitica Log e le seguenti operazioni:

1. Fare clic su *registri di account di archiviazione*
2. Fare clic su *Aggiungi* attività
3. Selezionare l'account di archiviazione che contiene i registri di diagnostica
  - Questo account può essere un account di archiviazione classico o un account di archiviazione di Manager delle risorse di Azure
4. Selezionare il tipo di dati che si desidera raccogliere i registri per
  - Le opzioni disponibili sono i registri di IIS; In caso contrario. Registro di sistema (Linux); ETW registri; Servizio tessuti eventi
5. Il valore di origine viene inserito automaticamente in base al tipo di dati e non può essere modificato
6. Fare clic su OK per salvare la configurazione

Ripetere i passaggi da 2 a 6 per gli account di spazio di archiviazione aggiuntivo e tipi di dati che si desidera Analitica Log per la raccolta.

In 30 minuti, è possibile visualizzare dati provenienti da account di archiviazione in Log Analitica. Visualizzare solo i dati che scritti allo spazio di archiviazione dopo la configurazione viene applicata. Registro Analitica non legge i dati esistenti dall'account di archiviazione.

>[AZURE.NOTE] Il portale non convalida che l'origine è presente nell'account di archiviazione o se sono scritti nuovi dati.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Abilitare diagnostica Windows Azure in un computer virtuale per registro eventi e IIS registro insieme tramite PowerShell

Per usare PowerShell per leggere diagnostica Windows Azure scritta per l'archiviazione tabella, usare i passaggi di [Configurazione Log Analitica indicizzare diagnostica Windows Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) .

Utilizzo di PowerShell Azure è possibile specificare con maggiore precisione gli eventi che vengono scritte allo spazio di archiviazione Azure.
Per ulteriori informazioni, vedere [Attivazione di diagnostica in macchine virtuali di Azure](../virtual-machines-dotnet-diagnostics.md).

È possibile attivare e aggiornare diagnostica Windows Azure tramite il seguente script di PowerShell.
Utilizzare questo script con una configurazione personalizzata della registrazione.
Modificare lo script per impostare l'account di archiviazione, il nome del servizio e il nome del computer virtuale.
Lo script utilizza cmdlet per macchine virtuali classiche.

Esaminare il seguente script di esempio, copiarlo, modificarlo in base alle esigenze, salvare l'esempio in un file di script di PowerShell e quindi eseguire lo script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Passaggi successivi

- [Usare JSON file in archiviazione blob](log-analytics-azure-storage-json.md) per leggere i log da Azure servizi tale diagnostica di scrittura per l'archiviazione blob in formato JSON.
- [Abilitare le soluzioni](log-analytics-add-solutions.md) per approfondire i dati.
- [Utilizzare query di ricerca](log-analytics-log-searches.md) per analizzare i dati.
