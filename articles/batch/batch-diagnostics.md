<properties
   pageTitle="La registrazione diagnostica Batch Azure | Microsoft Azure"
   description="Registrare e analizzare eventi del log diagnostici per le risorse account Azure Batch come pool e attività."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Registrazione diagnostica di Azure Batch

Come con molti servizi Azure, il servizio Batch genera eventi del log per determinate risorse per la durata della risorsa. È possibile abilitare Azure Batch registri diagnostici per tenere traccia di eventi per le risorse come pool e attività e quindi usare i registri diagnostica valutazione e di controllo. Creano eventi quali pool, Elimina pool, inizio, attività completata e gli altri utenti sono inclusi nei log diagnostici Batch.

>[AZURE.NOTE] In questo articolo vengono illustrati gli eventi di registrazione per le risorse account Batch se stessi, non di lavoro e attività dati di output. Per ulteriori informazioni sull'archiviazione dati di output dei processi e attività, vedere [mantenere Batch Azure mansione e delle attività output](batch-task-output.md).

## <a name="prerequisites"></a>Prerequisiti

* [Account Azure Batch](batch-account-create-portal.md)

* [Account di archiviazione Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Per mantenere registri diagnostici Batch, è necessario creare un account di archiviazione Azure in Azure verrà archiviati i log. Specificare l'account di archiviazione quando è [Attiva la registrazione diagnostica](#enable-diagnostic-logging) per il proprio account Batch. Account di archiviazione che è specificare quando si attiva l'insieme di log non è diverso da quello di un account di archiviazione collegate a cui gli articoli di [pacchetti di applicazioni](batch-application-packages.md) e [persistenza di output delle attività](batch-task-output.md) .

  >[AZURE.WARNING] Si sta **addebitate** per i dati memorizzati nell'account di archiviazione Azure. Sono inclusi i registri diagnostici descritti in questo articolo. Tenere presente in considerazione durante la progettazione di [criteri di conservazione dei log](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Attivare la registrazione diagnostica

Registrazione diagnostica non è abilitata per impostazione predefinita per il proprio account Batch. In modo esplicito, è necessario attivare la registrazione diagnostica per ogni account Batch che si desidera eseguire il monitoraggio:

[Come attivare la raccolta dei registri diagnostici](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

È consigliabile leggere l'articolo completo [Panoramica dei registri diagnostici Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per acquisire familiarità con non solo come attivare la registrazione, ma le categorie log supportate dai diversi servizi di Azure. Ad esempio, Batch Azure supporta attualmente una categoria di log: **I registri del servizio**.

## <a name="service-logs"></a>Registri del servizio

Log di servizio Azure Batch contengono eventi emessi dal servizio di Azure Batch nel corso della durata di una risorsa Batch come un pool o attività. Ogni evento creato da Batch verrà archiviato in account di archiviazione specificato nel formato JSON. Ad esempio, si tratta corpo di un esempio **pool creare evento**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Corpo ogni evento si trova in un file .json nell'account di archiviazione di Azure specificato. Se si vuole accedere direttamente i registri, è consigliabile esaminare lo [schema dei registri diagnostici nell'account di archiviazione](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventi del Log di servizio

Il servizio Batch genera attualmente servizio eventi seguenti. L'elenco potrebbe non essere completo, poiché gli eventi aggiuntivi che sono stati aggiunti dall'ultimo aggiornamento in questo articolo.

| **Eventi del Log di servizio** |
| ------------------ |
| [Creare pool][pool_create] |
| [Inizio Elimina pool][pool_delete_start] |
| [Elimina pool completata][pool_delete_complete] |
| [Avvio di ridimensionamento del pool][pool_resize_start] |
| [Ridimensionamento di pool completata][pool_resize_complete] |
| [Inizio delle attività][task_start] |
| [Attività completata][task_complete] |
| [Errori di attività][task_fail] |

## <a name="next-steps"></a>Passaggi successivi

Oltre a memorizzare la registrazione diagnostica degli eventi in un account di archiviazione Azure, è anche possibile flusso Batch servizio eventi a un [Hub evento Azure](../event-hubs/event-hubs-what-is-event-hubs.md)e inviarle a [Azure Log Analitica](../log-analytics/log-analytics-overview.md).

* [Flusso Azure registri diagnostici a un hub di evento](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Il flusso di eventi di diagnostica Batch al servizio di ingresso dati scalabilità hub evento. Hub di evento in grado di acquisire milioni di eventi al secondo, che può essere quindi trasformare e memorizzare tramite qualsiasi provider analitica in tempo reale.

* [Analizzare i registri diagnostici Azure utilizzando Analitica Log](../log-analytics/log-analytics-azure-storage-json.md)

  Inviare i registri diagnostici Analitica Log in cui è possibile analizzarli nel portale di operazioni gestione famiglia di prodotti (OMS) o esportarli per l'analisi in Power BI o Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
