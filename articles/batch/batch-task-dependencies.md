<properties
    pageTitle="Relazioni tra le nel Batch di Azure attività | Microsoft Azure"
    description="Creazione di attività che dipendono dal completamento di altre attività per l'elaborazione di stile MapReduce e dati grandi simili carichi di lavoro nel Batch di Azure."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Relazioni tra attività nel Batch di Azure

La caratteristica di relazioni tra attività del Batch di Azure è adatto se si vuole elaborare:

- Carichi di lavoro di stile MapReduce nel cloud.
- Processi cui attività di elaborazione dei dati può essere espressa come un grafico aciclico diretto (DAG).
- Qualsiasi altro processo in cui le attività downstream dipendono dall'output di attività padre.

Relazioni tra attività batch consentono di creare attività pianificate per l'esecuzione su nodi di calcolo solo dopo il completamento di altre attività. Ad esempio, è possibile creare un processo che esegue il rendering di ogni fotogramma di un filmato 3D con attività separata e in parallelo. Finale attività, ossia il "unione attività" - unisce cornici rendering nel filmato completato solo dopo che tutte le cornici sono stato eseguito il rendering.

È possibile creare attività che dipendono da altre attività in una relazione uno a uno o uno-a-molti. È anche possibile creare una dipendenza intervallo in cui un'attività dipende dal completamento di un gruppo di attività in un intervallo specifico di ID delle attività. È possibile combinare questi tre scenari di base per creare relazioni molti-a-molti.

## <a name="task-dependencies-with-batch-net"></a>Relazioni tra attività con .NET Batch

In questo articolo verranno trattati gli argomenti sulla configurazione di relazioni tra attività utilizzando [.NET Batch] [ net_msdn] raccolta. Questo webinar mostra prima di tutto come [abilitare relazioni tra attività](#enable-task-dependencies) per i processi e viene descritto come [configurare un'attività e le dipendenze](#create-dependent-tasks). Infine, verranno illustrati gli [scenari di dipendenza](#dependency-scenarios) che supporta Batch.

## <a name="enable-task-dependencies"></a>Abilitare le relazioni tra attività

Per utilizzare le relazioni tra attività Batch nell'applicazione in uso, è innanzitutto necessario indicare il servizio Batch che il processo utilizza le relazioni tra attività. In Batch .NET abilitarla nel [CloudJob] [ net_cloudjob] mediante l'impostazione relativa [UsesTaskDependencies] [ net_usestaskdependencies] la proprietà `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Frammento di codice precedente, "batchClient" è un'istanza di [BatchClient] [ net_batchclient] classe.

## <a name="create-dependent-tasks"></a>Creare attività dipendenti

Per creare un'attività che dipende dal completamento di una o più attività, si indica Batch che l'attività "dipende" altre attività. In .NET Batch, configurare [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] proprietà con un'istanza di [TaskDependencies] [ net_taskdependencies] classe:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Il frammento di codice crea un'attività con l'ID di "Fiori" programmata per l'esecuzione su un nodo di calcolo solo dopo che le attività con ID di "Pioggia" e "DOM" è sono completata correttamente.

 > [AZURE.NOTE] Un'attività viene considerata come completato quando è stato **completato** e il relativo **codice di uscita** `0`. In .NET Batch, significa che un [CloudTask][net_cloudtask]. [Stato] [net_taskstate] valore della proprietà `Completed` e del CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [Codice di uscita] [net_exitcode] valore della proprietà `0`.

## <a name="dependency-scenarios"></a>Scenari di dipendenza

Esistono tre scenari di relazioni tra attività di base che è possibile usare nel Batch di Azure:-a-uno, uno-a-molti e ID attività intervallo dipendenza. Questi possono essere combinati per fornire un quarto scenario molti-a-molti.

 Scenario:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Esempio | |
 :-------------------: | ------------------- | -------------------
 [-A-uno](#one-to-one) | *taskB* dipende *taskA* <p/> *taskB* non verrà pianificato per l'esecuzione fino a *taskA* è stata completata correttamente | ![Diagramma: relazioni tra attività-a-uno][1]
 [Uno-a-molti](#one-to-many) | *taskC* dipende *taskA* e *taskB* <p/> *taskC* non verrà pianificato per l'esecuzione fino a *taskA* e *taskB* è stata completata correttamente | ![Diagramma: relazione uno-a-molti][2]
 [Intervallo di ID delle attività](#task-id-range) | *taskD* dipende da un intervallo di attività <p/> *taskD* non verrà pianificato per l'esecuzione fino a quando non sono stati completati correttamente le attività con ID *1* e *10* | ![Diagramma: Dipendenza dell'intervallo di id delle attività][3]

>[AZURE.TIP] È possibile creare relazioni **molti-a-molti** , ad esempio in cui le attività C, D, E e F ogni dipendono dalle attività A e B. In questo modo, ad esempio, in parallelizzata pre-elaborazione scenari in cui le attività downstream dipendono l'output di più attività padre.

### <a name="one-to-one"></a>-A-uno

Per creare un'attività che dipende dal completamento di un'altra attività, è fornire un ID singola attività [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] metodo statico quando si inseriscono dati [DependsOn] [ net_dependson] proprietà di [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno-a-molti

Per creare un'attività che dipende dal completamento di più attività, è fornire un insieme di attività ID a [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] metodo statico quando si inseriscono dati [DependsOn] [ net_dependson] proprietà di [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervallo di ID delle attività

Per creare un'attività che dipende dal completamento di un gruppo di attività di cui si trovano gli ID in un intervallo, specificare la prima e ultima attività ID nell'intervallo da [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] metodo statico quando si inseriscono dati [DependsOn] [ net_dependson] proprietà di [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Quando si usa gli intervalli di ID delle attività per le dipendenze, gli ID delle attività nel intervallo *deve* essere rappresentazioni di stringa di valori integer. Inoltre, l'ogni attività nell'intervallo devono essere completate per l'attività dipendente pianificare l'esecuzione.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Esempio di codice

[TaskDependencies] [ github_taskdependencies] progetto di esempio è uno degli [esempi di codice Azure Batch] [ github_samples] su GitHub. Questa soluzione Visual Studio 2015 viene illustrato come abilitare relazioni tra attività in un processo, creare attività che dipendono da altre attività ed eseguire le attività in un pool di nodi di calcolo.

## <a name="next-steps"></a>Passaggi successivi

### <a name="application-deployment"></a>Distribuzione di applicazioni

La caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch offre un modo semplice per entrambe distribuire e versione le applicazioni di esecuzione delle attività in nodi di calcolo.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e dati di gestione temporanea

Estrarre i [nodi di calcolo di installazione applicazioni e dati di gestione temporanea nel Batch] [ forum_post] inserire post nel forum Batch Azure per una panoramica dei diversi metodi per preparare i nodi per eseguire attività. Scritto da uno dei membri del team Batch Azure, questo post è una panoramica utile in diversi modi per trasferire i file (inclusi applicazioni e dati di input attività) sui nodi di calcolo.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramma: dipendenza-a-uno"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagramma: relazione uno-a-molti"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramma: dipendenza dell'intervallo di id delle attività"
