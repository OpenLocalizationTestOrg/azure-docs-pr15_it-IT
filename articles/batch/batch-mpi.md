<properties
    pageTitle="Eseguire applicazioni MPI nel Batch di Azure con attività di più istanze | Microsoft Azure"
    description="Informazioni sull'esecuzione di applicazioni Message passando Interface (MPI) usando il tipo di attività di più istanze nel Batch di Azure."
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Usare le attività di più istanze per eseguire applicazioni messaggio passando Interface (MPI) in Batch di Azure

Attività di più istanze consentono di eseguire un'attività di Azure Batch su più nodi di calcolo contemporaneamente. Queste attività prestazioni elevate computing scenari come applicazioni di messaggio passando Interface (MPI) in Batch. In questo articolo si imparerà a eseguire attività di più istanze utilizzando [.NET Batch] [ api_net] raccolta.

>[AZURE.NOTE] Mentre gli esempi riportati in questo articolo focalizzare l'attenzione su .NET Batch, MS-MPI e nodi di calcolo di Windows, i concetti di attività di più istanze descritti di seguito sono applicabili per altre piattaforme e tecnologie (Python e Intel MPI su nodi Linux, ad esempio).

## <a name="multi-instance-task-overview"></a>Panoramica delle attività di più istanze

Nel Batch, ogni attività è in genere eseguito su un nodo di calcolo singola, ossia l'invio di più attività a un processo e il servizio Batch Pianifica ogni attività per l'esecuzione su un nodo. Tuttavia, configurando, **le impostazioni di più istanze dell'attività**, si indica Batch invece creare un'attività principale e diverse sottoattività che vengono quindi eseguite più nodi.

![Panoramica delle attività di più istanze][1]

Quando si invia un'attività con le impostazioni di più istanze a un processo, Batch esegue diversi passaggi univoci alle attività di più istanze:

1. Il servizio Batch crea un di tipo **principale** e diverse **sottoattività** in base alle impostazioni di più istanze. Il numero totale di attività (principale più tutte le sottoattività) corrisponde al numero di **istanze** (nodi di calcolo) specificare le impostazioni di più istanze.
1. Batch designa uno dei nodi di calcolo come **master**e Pianifica attività principali da eseguire nello schema. Consente di pianificare le sottoattività per eseguire sul resto dei nodi di elaborazione assegnata al compito di più istanze, una sottoattività per nodo.
1. Il principale e tutte le sottoattività scaricano i **file di risorse comune** che specificare le impostazioni di più istanze.
1. Dopo la risorsa comune file sono stati scaricati, principale e le sottoattività eseguire il **comando coordinazione** specificare le impostazioni di più istanze. Il comando coordinazione in genere viene utilizzato per preparare nodi per l'esecuzione dell'attività. Può trattarsi di avvio dei servizi di sfondo (ad esempio [Microsoft MPI][msmpi_msdn]del `smpd.exe`) e verificare che i nodi siano pronti per l'elaborazione di messaggi tra i nodi.
1. L'attività principale esegue il **comando applicazione** sul nodo principale *dopo* che il comando coordinazione sia stato completato correttamente, principale e tutte le sottoattività. Il comando applicazione la riga di comando dell'attività multiple istanza stessa e viene eseguito solo dall'attività principale. In un [MPI MS][msmpi_msdn]-in base a soluzione, si tratta in cui si esegue abilitato MPI applicazione utilizzando `mpiexec.exe`.

> [AZURE.NOTE] Sebbene sia funzionale distinto, l'attività di più istanze"" non è un tipo di attività univoco come [StartTask] [ net_starttask] o [JobPreparationTask][net_jobprep]. L'attività di più istanze è semplicemente un'attività Batch standard ([CloudTask] [ net_task] in Batch .NET) sono state configurate le cui impostazioni di più istanze. In questo articolo si parla come **attività di più istanze**.

## <a name="requirements-for-multi-instance-tasks"></a>Requisiti per le attività più istanze

Attività di più istanze richiedono un pool con **attivata le comunicazioni tra i nodi**e con **l'esecuzione di attività simultanee disabilitato**. Se si tenta di eseguire un'operazione di più istanze di un pool con le comunicazioni disabilitata o con un valore *maxTasksPerNode* maggiore di 1, l'attività non viene programmata mai - rimane tempo indefinito lo stato "attivo". Il frammento di codice viene illustrata la creazione del gruppo utilizzando la libreria .NET Batch.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Inoltre, le attività più istanze possono eseguire *solo* in nodi **pool creati dopo 14 dicembre 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Utilizzare un StartTask per installare MPI

Per eseguire applicazioni MPI con un'attività di più istanze, è prima di tutto necessario installare un'implementazione di MPI (MS MPI o MPI Intel, ad esempio) sui nodi di calcolo nel pool. Questo è consigliabile utilizzare un [StartTask][net_starttask], che viene eseguita ogni volta che un nodo viene aggiunto a un pool o si riavvia. Il frammento di codice viene creato un StartTask che specifica il pacchetto di installazione di MS MPI come [file di risorse][net_resourcefile]. Riga di comando di inizio dell'attività viene eseguito dopo avere scaricato il file di risorse al livello di nodo. In questo caso, la riga di comando esegue un'installazione automatica di MS MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remoto accesso diretto alla memoria (RDMA)

Quando si sceglie una [dimensione in grado di RDMA](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) , ad esempio A9 i nodi di calcolo nel pool di Batch, l'applicazione MPI possa trarre vantaggio della rete di accesso (RDMA) di Azure prestazioni ad alta, bassa latenza memoria diretto remoto.

Cercare le dimensioni specificate come "RDMA dotato di" gli articoli seguenti:

* Pool di **CloudServiceConfiguration**

  * [Formati per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md) (Solo Windows)

* Pool di **VirtualMachineConfiguration**

  * [Dimensioni per macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Dimensioni per macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Per sfruttare RDMA sul [Linux nodi di calcolo](batch-linux-nodes.md), è necessario utilizzare **Intel MPI** nei nodi. Per ulteriori informazioni sui pool di CloudServiceConfiguration e VirtualMachineConfiguration, vedere la sezione [Pool](batch-api-basics.md#Pool) di panoramica delle funzionalità Batch.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Creare un'attività di più istanze con .NET Batch

Ora che abbiamo trattato i requisiti di pool e l'installazione del pacchetto MPI, creare l'attività di più istanze. In questo frammento di codice, viene creata una standard [CloudTask][net_task], quindi configurare il [MultiInstanceSettings] [ net_multiinstance_prop] proprietà. Come detto in precedenza, l'attività di più istanze non è un tipo di attività distinte, ma un'attività Batch standard configurati con le impostazioni di più istanze.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Sottoattività e attività principale

Quando si creano le impostazioni di più istanze di un'attività, specificare il numero dei nodi di calcolo da eseguire l'attività. Quando si invia l'attività a un processo, il servizio Batch crea un'attività **principale** e abbastanza **sottoattività** che insieme corrisponde al numero dei nodi specificato.

Queste attività verranno assegnate un id integer nell'intervallo compreso tra 0 *numberOfInstances* - 1. L'attività con id 0 è l'attività principale e tutti gli altri ID sono le sottoattività. Ad esempio, se si creano le seguenti impostazioni di più istanze di un'attività, l'attività principale avrebbe un id pari a 0 e le sottoattività avrebbe ID 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nodo principale

Quando si invia un'attività di più istanze, il servizio Batch designa uno dei nodi di calcolo come il nodo "principale" e Pianifica attività principali da eseguire sul nodo principale. Le sottoattività sono programmate per eseguire sul resto dei nodi assegnata al compito di più istanze.

## <a name="coordination-command"></a>Comando relazione

Viene eseguito il **comando coordinazione** da entrambe le principale e sottoattività.

Blocca la chiamata del comando coordinazione - Batch non viene eseguito il comando applicazione fino a quando il comando coordinazione ha restituito correttamente per tutte le sottoattività. Il comando coordinazione pertanto deve iniziare a uno o più servizi necessari in background, verificare che siano pronti all'uso e quindi uscire da. Ad esempio, per coordinazione per una soluzione tramite MS MPI versione 7 avvia il servizio SMPD nel nodo, quindi chiudere:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Si noti l'utilizzo di `start` in questo comando di relazione. Questa operazione è necessaria in quanto la `smpd.exe` applicazione non viene restituito immediatamente dopo l'esecuzione. Senza l'utilizzo di [avviare] [ cmd_start] comandi, questo comando coordinazione non restituirà e pertanto Blocca esecuzione del comando dell'applicazione.

## <a name="application-command"></a>Comando applicazione

Dopo l'attività principale e tutte le sottoattività completata l'esecuzione del comando coordinazione, riga di comando dell'attività più istanze viene eseguito tramite l'attività principale *solo*. Si chiama questo **comando applicazione** per distinguere rispetto al comando relazione.

Per le applicazioni di MS MPI, usare il comando di applicazione per eseguire l'applicazione abilitata MPI con `mpiexec.exe`. Ecco, ad esempio, il comando di un'applicazione di una soluzione utilizzando MS-MPI versione 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Poiché MS-MPI `mpiexec.exe` utilizza il `CCP_NODES` variabile per impostazione predefinita (vedere [variabili](#environment-variables)) nell'esempio riga di comando applicazione esclusa in.

## <a name="environment-variables"></a>Variabili di ambiente

Batch Crea diverse [variabili di ambiente] [ msdn_env_var] specifiche per le attività più istanze sui nodi di calcolo a un'attività di più istanze. Le righe di comando coordinazione e applicazione può fare riferimento a tali variabili, come gli script e programmi che vengono eseguite.

Le variabili di ambiente seguenti vengono create dal servizio Batch per usare le attività più istanze:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Per informazioni dettagliate su questi e altri Batch calcolo nodo variabili di ambiente, inclusi i relativi contenuti e visibilità, vedere [calcolare le variabili di ambiente nodo][msdn_env_var].

>[AZURE.TIP] L'esempio di codice Batch Linux MPI contiene un esempio di come possono essere usate più di tali variabili. [Relazione cmd] [ coord_cmd_example] script Bash Scarica applicazione comune e input file dallo spazio di archiviazione di Azure, consente una condivisione di rete NFS (File System) sul nodo principale e configurare gli altri nodi assegnati al compito di più istanze come client NFS.

## <a name="resource-files"></a>File di risorse

Esistono due serie di file di risorse da prendere in considerazione per le attività più istanze: **file di risorse comuni** che *tutte* le attività scaricare (entrambi principale e sottoattività) e i **file di risorse** specificato per l'istanza di più attività stessa, quali attività *solo primario* download.

È possibile specificare uno o più **file di risorse comuni** nelle impostazioni di più istanze di un'attività. Questi file di risorse comuni vengono scaricati dallo [Spazio di archiviazione di Azure](./../storage/storage-introduction.md) in ciascun nodo **directory condivisa attività** dal server primario e tutte le sottoattività. È possibile accedere alla directory condivisa attività dall'applicazione e coordinazione righe di comando utilizzando il `AZ_BATCH_TASK_SHARED_DIR` variabile di ambiente. Il `AZ_BATCH_TASK_SHARED_DIR` percorso è identico in ogni nodo assegnata al compito di più istanze, pertanto è possibile condividere un comando singolo coordinazione tra principale e tutte le sottoattività. Batch non "condividere" nella directory in senso accesso remoto, ma è possibile utilizzarla come un mount o punto come detto in precedenza nel suggerimento su variabili di condivisione.

File di risorse specificate per l'attività multiple istanza stessa vengono scaricati nella cartella di lavoro dell'attività, `AZ_BATCH_TASK_WORKING_DIR`, per impostazione predefinita. Come detto, a differenza di file di risorse comuni, solo l'attività principale download di file di risorse specificati per l'attività multiple istanza stessa.

> [AZURE.IMPORTANT] Usa sempre le variabili di ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` per fare riferimento a queste directory delle linee di comando. Non tentare di creare manualmente i percorsi.

## <a name="task-lifetime"></a>Durata delle attività

La durata dell'attività principale controlla la durata dell'attività intera istanza multiple. Al termine, il principale vengono terminate tutte le sottoattività. Il codice di uscita dell'istanza primaria è il codice di uscita dell'attività e quindi viene utilizzato per determinare l'esito positivo o negativo dell'attività ai fini della Riprova.

Se una delle sottoattività ha esito negativo, uscire con un codice restituito diverso da zero, ad esempio, l'intera istanza più operazione non riesce. L'attività di più istanze quindi terminata e ripetuta fino al limite di tentativi.

Quando si elimina un'attività di più istanze, primario e tutte le sottoattività vengono eliminate anche dal servizio Batch. Tutti sottoattività directory e i file vengono eliminati da nodi di calcolo, come per un'attività standard.

[TaskConstraints] [ net_taskconstraints] per un'attività di più istanze, ad esempio [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]e [RetentionTime] [ net_taskconstraint_retention] vengono rispettate le proprietà, non appena vengono per un'attività standard e applicare a principale e tutte le sottoattività. Tuttavia, se si cambia [RetentionTime] [ net_taskconstraint_retention] proprietà dopo avere aggiunto l'attività di più istanze per il processo, questa modifica viene applicata solo alle attività principale. Tutte le sottoattività continuare a usare originale [RetentionTime][net_taskconstraint_retention].

Elenco attività recenti del nodo di calcolo riflette l'id di una sottoattività se l'attività recente fa parte di un'attività di più istanze.

## <a name="obtain-information-about-subtasks"></a>Ottenere informazioni su sottoattività

Per ottenere informazioni su sottoattività utilizzando la libreria .NET Batch, chiamare [CloudTask.ListSubtasks] [ net_task_listsubtasks] metodo. Questo metodo restituisce informazioni su tutte le sottoattività e informazioni su nodo di calcolo che eseguite le attività. Queste informazioni, è possibile stabilire directory radice di ogni sottoattività, l'id di pool, lo stato corrente, codice di uscita e altro. È possibile utilizzare queste informazioni in combinazione con [PoolOperations.GetNodeFile] [ poolops_getnodefile] metodo per ottenere i file del sottoattività. Si noti che questo metodo non restituisce informazioni per l'attività principale (id 0).

> [AZURE.NOTE] Se non diversamente specificato, metodi .NET Batch operare sul più istanze [CloudTask] [ net_task] stesso si applicano *solo* all'attività principale. Ad esempio, quando si chiama [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metodo su un'attività di più istanze, vengono restituiti solo i file di un'attività principale.

Frammento di codice riportato di seguito viene illustrato come ottenere informazioni delle sottoattività, come richiedere i nodi in cui vengono eseguiti il contenuto del file.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Esempio di codice

[MultiInstanceTasks] [ github_mpi] codice in GitHub viene illustrato come utilizzare un'attività di più istanze per eseguire un [MPI MS] [ msmpi_msdn] applicazione nel Batch nodi di calcolo. Seguire i passaggi di [Preparazione](#preparation) e [l'esecuzione](#execution) per eseguire l'esempio.

### <a name="preparation"></a>Preparazione

1. Seguire i primi due passaggi [come compilare ed eseguire un semplice programma MS MPI][msmpi_howto]. Questo soddisfa prerequesites per il passo successivo.
1. Creare *una versione di [MPIHelloWorld] * [ helloworld_proj] programma MPI di esempio. Questo è il programma che verrà eseguito su nodi di calcolo per l'attività di più istanze.
1. Creare un file zip contenente `MPIHelloWorld.exe` (quale è compilato il passaggio 2) e `MSMpiSetup.exe` (scaricato al passaggio 1). In questo file zip viene caricata come un pacchetto dell'applicazione nel passaggio successivo.
1. Usare il [portale di Azure] [ portal] per creare un Batch [applicazione](batch-application-packages.md) denominata "MPIHelloWorld" e specificare il file zip creato nel passaggio precedente come versione "1.0" del pacchetto dell'applicazione. Per ulteriori informazioni, vedere [caricare e gestire l'applicazione](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Creare una versione di *rilascio* `MPIHelloWorld.exe` in modo che non è necessario includere le dipendenze aggiuntive (ad esempio `msvcp140d.dll` o `vcruntime140d.dll`) nel pacchetto dell'applicazione.

### <a name="execution"></a>Esecuzione

1. Scaricare gli [esempi di batch azure] [ github_samples_zip] da GitHub.
1. Aprire la MultiInstanceTasks **soluzione** in Visual Studio 2015. Il `MultiInstanceTasks.sln` Esplora file si trova:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Immettere le credenziali dell'account Batch e lo spazio di archiviazione in `AccountSettings.settings` nel progetto **Microsoft.Azure.Batch.Samples.Common** .
1. **Creare ed eseguire** la soluzione MultiInstanceTasks per eseguire il MPI applicazione di esempio in nodi in un pool di Batch di calcolo.
1. *Facoltativo*: usare il [portale di Azure] [ portal] o [Explorer Batch] [ batch_explorer] per esaminare il pool di esempio, processo e attività ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") prima di eliminare le risorse.

>[AZURE.TIP] È possibile scaricare [Visual Studio Community] [ visual_studio] gratuitamente se non si dispone di Visual Studio.

Output da `MultiInstanceTasks.exe` è simile al seguente:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Passaggi successivi

- Nel blog di Microsoft HPC & Team Batch Azure viene descritto [MPI supporto per Linux nel Batch di Azure][blog_mpi_linux]e include informazioni sull'utilizzo di [OpenFOAM] [ openfoam] con Batch. È possibile trovare esempi di codice Python, ad [esempio OpenFOAM GitHub][github_mpi].

- Informazioni su come [creare pool Linux dei nodi di calcolo](batch-linux-nodes.md) per poterlo usare nelle soluzioni di Azure Batch MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Panoramica di più istanze"
