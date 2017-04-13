<properties
    pageTitle="Azure Monitor il ridimensionamento automatico comuni metriche. | Microsoft Azure"
    description="Informazioni su quali metriche di uso comune per il ridimensionamento automatico i servizi Cloud, macchine virtuali e Web Apps."
    authors="kamathashwin"
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
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Metriche di comuni di Azure Monitor il ridimensionamento automatico

Il ridimensionamento automatico Monitor Azure consente di ridurre il numero di istanze in esecuzione verso l'alto o verso il basso, in base ai dati di telemetria (metriche). Questo documento descrive le metriche comuni che potrebbero da usare. Nel portale di Azure per servizi Cloud e la Server farm, è possibile scegliere la metrica della risorsa da scalare di. Tuttavia, è possibile scegliere anche le unità di misura metriche da una risorsa diversa per le dimensioni.

Ecco i dettagli su come trovare e metriche da scalare dell'elenco. Si applica le operazioni seguenti per il ridimensionamento anche macchina virtuale scala set.

## <a name="compute-metrics"></a>Calcolare metriche
Per impostazione predefinita, macchine Virtuali di Azure v2 viene fornito con estensione di diagnostica configurato e hanno le seguenti metriche attivate.

- [Metrica Guest per v2 macchine Virtuali di Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Metrica Guest per Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

È possibile utilizzare il `Get MetricDefinitions` API/PoSH/CLI per visualizzare le metriche disponibili per la risorsa VMSS. 

Se si usa set scala macchine Virtuali e non è presente una particolare metrica elencata, è probabile *disabilitata* nell'estensione di diagnostica.

Se non è una particolare metrica campionati o trasferire la frequenza desiderata, è possibile aggiornare la configurazione di diagnostica.

Se entrambi i casi indicati sono VERO, quindi esaminare [Usare PowerShell per attivare la diagnostica in un computer virtuale che esegue Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) su PowerShell per configurare e aggiornare l'estensione di Azure macchine Virtuali diagnostica per attivare l'unità di misura metriche. Questo articolo include anche un file di configurazione di diagnostica di esempio.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calcolare la metrica per macchine Virtuali di Windows v2 come guest del sistema operativo

Quando si crea una nuova macchina virtuale (v2) in Azure, diagnostica viene abilitata utilizzando l'estensione di diagnostica.

È possibile generare un elenco dei criteri di misurazione utilizzando il comando seguente in PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

È possibile creare un avviso per le seguenti metriche.

|Nome metriche|   Unità|
|---|---|
|\Processor(_Total)\% tempo processore    |Percentuale|
|\Processor(_Total)\% privilegi ora   |Percentuale|
|\Processor(_Total)\% tempo utente |Percentuale|
|Frequenza di \Processore \Processore informazioni (totale) |Conta. numeri|
|\System\Processes| Conta. numeri|
|Conteggio di \Thread \Processo (totale)| Conta. numeri|
|Conteggio di \Handle \Processo (totale)  |Conta. numeri|
|\Memory.\% approvata byte In uso   |Percentuale|
|\Memory\Available byte|   Byte|
|\Memory\Committed byte    |Byte|
|Limite \Memory\Commit|  Byte|
|Paging \Memory\Pool|  Byte|
|\Memory\Pool non|   Byte|
|\PhysicalDisk(_Total)\% tempo disco| Percentuale|
|\PhysicalDisk(_Total)\% disco in fase di lettura|    Percentuale|
|\PhysicalDisk(_Total)\% tempo scrittura disco|   Percentuale|
|\PhysicalDisk (totale) \Disk trasferimenti/sec   |CountPerSecond|
|Lettura \Disk \PhysicalDisk (totale) / sec   |CountPerSecond|
|\PhysicalDisk (totale) \Disk scrittura/sec  |CountPerSecond|
|\PhysicalDisk (totale) \Disk byte/sec   |BytesPerSecond|
|\Disk \PhysicalDisk (totale) byte letti/sec| BytesPerSecond|
|\PhysicalDisk (totale) \Disk byte scritti/sec |BytesPerSecond|
|\Avg \PhysicalDisk (totale). Coda del disco|  Conta. numeri|
|\Avg \PhysicalDisk (totale). Lettura coda del disco| Conta. numeri|
|\Avg \PhysicalDisk (totale). Scrittura coda del disco |Conta. numeri|
|\LogicalDisk(_Total)\% spazio| Percentuale|
|Megabyte \Free \LogicalDisk (totale)|   Conta. numeri|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calcolare la metrica per Linux VM v2 come guest del sistema operativo

Quando si crea una nuova macchina virtuale (v2) in Azure, diagnostica è attivata per impostazione predefinita con l'estensione di diagnostica.

È possibile generare un elenco dei criteri di misurazione utilizzando il comando seguente in PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 È possibile creare un avviso per le seguenti metriche.

|Nome metriche                            |Unità|
|---|---|
|\Memory\AvailableMemory                |Byte|
|\Memory\PercentAvailableMemory         |Percentuale|
|\Memory\UsedMemory                     |Byte|
|\Memory\PercentUsedMemory              |Percentuale|
|\Memory\PercentUsedByCache             |Percentuale|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Byte|
|\Memory\PercentAvailableSwap           |Percentuale|
|\Memory\UsedSwap                       |Byte|
|\Memory\PercentUsedSwap                |Percentuale|
|\Processor\PercentIdleTime             |Percentuale|
|\Processor\PercentUserTime             |Percentuale|
|\Processor\PercentNiceTime             |Percentuale|
|\Processor\PercentPrivilegedTime       |Percentuale|
|\Processor\PercentInterruptTime        |Percentuale|
|\Processor\PercentDPCTime              |Percentuale|
|\Processor\PercentProcessorTime        |Percentuale|
|\Processor\PercentIOWaitTime           |Percentuale|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Secondi|
|\PhysicalDisk\AverageWriteTime         |Secondi|
|\PhysicalDisk\AverageTransferTime      |Secondi|
|\PhysicalDisk\AverageDiskQueueLength   |Conta. numeri|
|\NetworkInterface\BytesTransmitted     |Byte|
|\NetworkInterface\BytesReceived        |Byte|
|\NetworkInterface\PacketsTransmitted   |Conta. numeri|
|\NetworkInterface\PacketsReceived      |Conta. numeri|
|\NetworkInterface\BytesTotal           |Byte|
|\NetworkInterface\TotalRxErrors        |Conta. numeri|
|\NetworkInterface\TotalTxErrors        |Conta. numeri|
|\NetworkInterface\TotalCollisions      |Conta. numeri|




## <a name="commonly-used-web-server-farm-metrics"></a>Metriche Web (Server Farm) usate di frequente

È anche possibile eseguire autoscale in base a metriche di server web comuni, ad esempio la lunghezza di coda Http. Metrica nome è **HttpQueueLength**.  La sezione seguente sono elencati metriche farm (Web Apps) server disponibile.

### <a name="web-apps-metrics"></a>Metriche Web App

È possibile generare un elenco di metriche Web Apps utilizzando il comando seguente in PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

È possibile su un avviso o ridimensionare da queste metriche.

|Nome metriche        |Unità|
|---                |---|
|CpuPercentage      |Percentuale|
|MemoryPercentage   |Percentuale|
|DiskQueueLength    |Conta. numeri|
|HttpQueueLength    |Conta. numeri|
|BytesReceived      |Byte|
|BytesSent          |Byte|


## <a name="commonly-used-storage-metrics"></a>Simboli di utilizzo metriche di archiviazione
È possibile ridimensionare in base alla lunghezza di coda lo spazio di archiviazione, ovvero il numero di messaggi nella coda di spazio di archiviazione. Lunghezza coda di spazio di archiviazione è una metrica speciale e soglia applicata sarà il numero di messaggi per ogni istanza. Questo significa che se sono presenti due istanze e se la soglia è impostata su 100, verrà ridimensionata quando il numero totale di messaggi nella coda è 200. Ad esempio 100 messaggi per ogni istanza.

È possibile configurare si tratta nel portale di Azure in e **l'Impostazioni** . Per i set di scala macchine Virtuali, è possibile aggiornare l'impostazione Autoscale nel modello di ARM per utilizzare *metricName* come *ApproximateMessageCount* e passare l'ID di coda lo spazio di archiviazione come *metricResourceUri*.

Ad esempio, con un Account di archiviazione classica metricTrigger di impostazione autoscale includerà:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Per un account di archiviazione (non classico), è necessario includere il metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Metrica Bus di servizio usata di frequente

È possibile ridimensionare in base alla lunghezza coda Bus di servizio, ovvero il numero di messaggi nella coda Bus di servizio. Lunghezza coda Bus di servizio è una metrica speciale e la soglia specificata applicato sarà il numero di messaggi per ogni istanza. Questo significa che se sono presenti due istanze e se la soglia è impostata su 100, verrà ridimensionata quando il numero totale di messaggi nella coda è 200. Ad esempio 100 messaggi per ogni istanza.

Per i set di scala macchine Virtuali, è possibile aggiornare l'impostazione Autoscale nel modello di ARM per utilizzare *metricName* come *ApproximateMessageCount* e passare l'ID di coda lo spazio di archiviazione come *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Per Bus di servizio, il concetto di gruppo di risorse non è presente ma Manager delle risorse Azure crea un gruppo di risorse predefiniti per ogni area geografica. Gruppo di risorse è in genere nel formato 'predefinito - ServiceBus-[region]. Ad esempio, 'EastUS di ServiceBus predefinito', 'WestUS di ServiceBus predefinito', 'Predefinito-ServiceBus-AustraliaEast' e così via.
