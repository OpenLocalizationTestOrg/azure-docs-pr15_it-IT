<properties
    pageTitle="Per ottimizzare l'utilizzo di nodo Batch con attività in parallelo | Microsoft Azure"
    description="Aumentare l'efficienza e ridurre i costi utilizzando meno nodi di calcolo e le attività in esecuzione simultanea su ciascun nodo in un pool di Azure Batch"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Ottimizzare l'uso delle risorse di elaborazione Batch Azure con attività simultanee nodo

Eseguendo più attività contemporaneamente su ogni nodo di calcolo nel pool di Azure Batch, è possibile ottimizzare l'utilizzo delle risorse in un numero minore di nodi nel pool. Per alcuni carichi di lavoro, questo può comportare più breve processo tempi e costi.

Mentre alcuni scenari trarre vantaggio da tutte le risorse del nodo dedicare a una singola attività, diverse situazioni vantaggi offerti dai consentendo più attività condividere le risorse:

 - **Ridurre i trasferimenti di dati** quando le attività sono in grado di condividere dati. In questo scenario, è possibile ridurre notevolmente le spese di trasferimento dati copiando dati condivisi in un numero minore di nodi e l'esecuzione di attività in parallelo su ciascun nodo. Si applica in particolare se i dati da copiare a ciascun nodo devono essere trasferiti tra le aree geografiche.

 - **Utilizzo di memoria Maximizing** quando attività richiedono una grande quantità di memoria, ma solo durante brevi periodi di tempo e momenti variabile durante l'esecuzione. È possibile utilizzare nodi di calcolo di un numero minore ma di dimensioni maggiori con più memoria gestire in modo efficiente tali picchi. Questi nodi avrebbe più attività in esecuzione in parallelo su ogni nodo, ma ogni attività da sfruttare memoria molta i nodi momenti diversi.

 - **Fattori limiti numerici nodo** quando è necessaria all'interno di un pool di comunicazione tra i nodi. Pool configurato per le comunicazioni tra i nodi sono attualmente limitato a 50 nodi di calcolo. Se ogni livello di nodo di un pool di tali in grado di eseguire attività in parallelo, un numero maggiore di attività può essere eseguito contemporaneamente.

 - **La replica di un locale calcolare cluster**, ad esempio quando si sposta prima di tutto un ambiente di calcolo in Azure. Se la soluzione locale corrente viene eseguito più attività per nodo di calcolo, è possibile aumentare il numero massimo di attività di nodo per riprodurre più fedelmente la configurazione.

## <a name="example-scenario"></a>Scenario di esempio

Ad esempio per illustrare i vantaggi dell'esecuzione delle attività in parallelo, si supponga che l'applicazione di attività sono previsti i requisiti di CPU e memoria in modo che [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nodi sono sufficienti. Tuttavia, per completare il processo dell'ora necessari, sono necessari 1.000 dei nodi.

Invece di usare Standard\_nodi D1 contenenti 1 nucleo CPU, è possibile utilizzare [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nodi che dispone di 16 core e consentire l'esecuzione in parallelo delle attività. Pertanto, potrebbero essere utilizzate *nodi di un numero minore di 16 ore* , invece di 1.000 nodi, 63 solo sono necessarie. Inoltre, se sono necessari per ogni nodo file applicazione di grandi dimensioni o dati di riferimento, l'efficienza e la durata di processo vengono nuovamente migliorati poiché i dati vengono copiati solo 16 nodi.

## <a name="enable-parallel-task-execution"></a>Consentire l'esecuzione in parallelo delle attività

Configurare i nodi di calcolo per l'esecuzione di attività in parallelo livello di pool. Con la raccolta .NET Batch impostare [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] proprietà quando si crea un pool. Se si utilizza l'API REST Batch, impostare [maxTasksPerNode] [ rest_addpool] elemento nel corpo della richiesta durante la creazione del pool.

Batch Azure consente di impostare numero massimo di attività per nodo fino a quattro volte (x 4) il numero di nodo Core. Ad esempio, se il pool è configurato con nodi di dimensioni "Grande" (quattro core), quindi `maxTasksPerNode` può essere impostato su 16. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni dei nodi, vedere [formati per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md). Per ulteriori informazioni sui limiti di servizio, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md).

> [AZURE.TIP] Assicurarsi di prendere in considerazione la `maxTasksPerNode` valore quando si crea una [formula autoscale] [ enable_autoscaling] per il pool. Ad esempio, una formula che calcola `$RunningTasks` potrebbero essere interessati in modo significativo da un aumento delle attività per nodo. Per ulteriori informazioni, vedere [nodi in un pool di Azure Batch di calcolo scala automaticamente](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Distribuzione di attività

Quando i nodi di calcolo di un pool possono eseguire attività contemporaneamente, è importante specificare come visualizzare le attività deve essere distribuito tra i nodi nel pool di.

Utilizzando [CloudPool.TaskSchedulingPolicy] [ task_schedule] proprietà, è possibile specificare che le attività devono essere assegnate in modo uniforme in tutti i nodi del pool ("diffusione"). Oppure è possibile specificare che come numero di possibili attività deve essere assegnato a ogni nodo prima attività vengono assegnate a un altro nodo del pool ("imballaggio").

Esempio di come questa funzionalità è utile, è consigliabile pool di [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nodi (esempio riportato sopra) che è configurato con un [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valore pari a 16. Se [CloudPool.TaskSchedulingPolicy] [ task_schedule] è configurato con un [ComputeNodeFillType] [ fill_type] di *Pack*da ottimizzare l'utilizzo di tutti i 16 core di ciascun nodo e consentire a un [pool di adattamento automatico](batch-automatic-scaling.md) all'eliminazione inutilizzati nodi del pool (nodi senza le attività assegnate). Si riduce l'uso delle risorse e risparmiare.

## <a name="batch-net-example"></a>Esempio di .NET batch

In questo [Batch.NET] [ api_net] frammento di codice API Mostra una richiesta per creare un pool che contiene quattro nodi di grandi dimensioni con un massimo di quattro attività per nodo. Specifica un criterio di compilazione ogni nodo con le attività prima di assegnazione di attività a un altro nodo del pool di pianificazione. Per ulteriori informazioni sull'aggiunta di pool tramite l'API di .NET Batch, vedere [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Esempio di resto batch

Il [Resto Batch] [ api_rest] frammento API Mostra richiede la creazione di un pool che contiene due nodi di grandi dimensioni con un massimo di quattro attività per nodo. Per ulteriori informazioni sull'aggiunta di pool tramite l'API REST, vedere [aggiungere un pool a un account][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] È possibile impostare il `maxTasksPerNode` elemento e [MaxTasksPerComputeNode] [ maxtasks_net] proprietà solo al momento della creazione del pool. Non possono essere modificate dopo un pool è già stato creato.

## <a name="code-sample"></a>Esempio di codice

[ParallelNodeTasks] [ parallel_tasks_sample] progetto su GitHub illustrato l'utilizzo della [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] proprietà.

Questa applicazione console c# utilizza [.NET Batch] [ api_net] raccolta per creare un pool con uno o più nodi di calcolo. Viene eseguito un numero configurabile delle attività dei nodi per simulare carico variabile. Output dell'applicazione specifica i nodi eseguita ogni attività. L'applicazione offre un riepilogo dei parametri del processo e durata. Di seguito è riportata la parte di riepilogo di output di due diverse esecuzioni dell'applicazione di esempio.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La prima esecuzione dell'applicazione di esempio mostra che con un singolo nodo del pool e l'impostazione di un'attività per ogni nodo, la durata del processo è più di 30 minuti.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La seconda eseguita dell'esempio sono visualizzati una significativa riduzione della durata di processo. Ciò avviene perché il pool è stato configurato con quattro attività per ogni nodo, che consente l'esecuzione in parallelo delle attività completare il processo quasi un trimestre dell'ora.

> [AZURE.NOTE] La durata di processo in riepiloghi sopra non include ora di creazione di pool. Ognuno dei processi in precedenza è stato inviato al pool creato in precedenza sono stati cui nodi di calcolo nello stato di *inattività* in fase di invio.

## <a name="next-steps"></a>Passaggi successivi

### <a name="batch-explorer-heat-map"></a>Mappa termica Explorer batch

[Azure Batch Explorer][batch_explorer], una delle Azure Batch [applicazioni di esempio][github_samples], contiene una funzione di *Mappa termica* che consente di visualizzazione dell'esecuzione delle attività. Quando si sta eseguendo l' [ParallelTasks] [ parallel_tasks_sample] applicazione di esempio, è possibile utilizzare la funzionalità di mappa termica per visualizzare facilmente l'esecuzione di attività in parallelo su ciascun nodo.

![Mappa termica Explorer batch][1]

*Mappa termica Explorer batch con un pool di quattro nodi, con ogni nodo attualmente in esecuzione quattro attività*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
