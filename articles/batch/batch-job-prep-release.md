<properties
    pageTitle="Processo di preparazione e pulizia nel Batch | Microsoft Azure"
    description="Usare le attività di preparazione a livello di processo per ridurre al minimo i trasferimenti di dati per i nodi di elaborazione Batch Azure e rilasciare attività per la pulizia del nodo al completamento del processo."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Eseguire il processo attività di preparazione e completamento nel Batch di Azure nodi di calcolo

 Un processo Batch Azure spesso occorre disporre del programma di installazione prima le attività vengono eseguite e post-processo manutenzione al termine delle relative attività. Potrebbe essere necessario scaricare i dati di input attività comuni i nodi di calcolo oppure caricare dati di output delle attività per lo spazio di archiviazione di Azure termine del processo. È possibile utilizzare le attività di **Preparazione del processo** e **rilasciare processo** per eseguire queste operazioni.

## <a name="what-are-job-preparation-and-release-tasks"></a>Quali sono preparazione del processo e rilasciare attività?

Prima di eseguire le attività del processo, l'attività di preparazione processo viene eseguito su tutti i nodi di calcolo esecuzione almeno un'attività pianificati. Una volta completato il processo, l'attività di rilascio processo viene eseguito su ogni nodo nel pool di esecuzione almeno un'attività. Come le normali attività Batch, è possibile specificare una riga di comando da richiamare durante l'esecuzione di un'attività di preparazione o versione finale del processo.

Attività di preparazione e rilascio processo offrono le caratteristiche comuni attività Batch, ad esempio download dei file ([file di risorse][net_job_prep_resourcefiles]), esecuzione, le variabili di ambiente personalizzato, durata massima esecuzione, numero di tentativi e ora di conservazione dei file con privilegi elevati.

Nelle sezioni seguenti si apprenderà come usare [JobPreparationTask] [ net_job_prep] e [JobReleaseTask] [ net_job_release] classi disponibili in [Batch.NET] [ api_net] raccolta.

> [AZURE.TIP] Attività di preparazione e rilascio mansioni sono particolarmente utili in ambienti "condiviso pool", in cui un pool di nodi di calcolo persiste tra esecuzione del processo e viene utilizzato da molti processi.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usare preparazione del processo e rilascia attività

Preparazione del processo e rilascio mansioni sono più adatta per le situazioni seguenti:

**Scaricare i dati di attività comuni**

Processi batch richiedono un insieme comune di dati come input per le attività del processo. Calcoli di analisi dei rischi giornaliera, ad esempio, dati di mercato sono specifici, ancora comuni a tutte le attività nel processo. Questi dati di mercato, dimensioni, spesso diversi gigabyte devono essere scaricati solo una volta per ogni nodo di calcolo in modo che tutte le attività che viene eseguita sul usarla. Utilizzare un' **attività di preparazione processo** per scaricare questi dati per ogni nodo prima dell'esecuzione del processo di 's altre attività.

**Eliminazione di output mansione e delle attività**

In un ambiente "condiviso pool" nodi di elaborazione del pool nel punto in cui non sono eliminati tra processi, potrebbe essere necessario eliminare i dati di processo tra le esecuzioni. Potrebbe essere necessario risparmiare spazio su disco nei nodi o soddisfano i criteri di sicurezza dell'organizzazione. Utilizzare una **versione mansione** per eliminare i dati che è stati scaricati da un'attività di preparazione processo o generati durante l'esecuzione di attività.

**Conservazione dei log**

È consigliabile mantenere una copia del file di log che generano le attività o potrebbe arrestarsi in modo anomalo file di dettagli che possono essere generati da errori nelle applicazioni. Utilizzare un' **attività di rilascio di processo** in tal caso per comprimere e caricare i dati in un' [Archiviazione Azure] [ azure_storage] account.

>[AZURE.TIP] Un altro modo per mantenere registri e altre attività di processo in output dati consiste nell'usare la raccolta di [Azure Batch File convenzioni](batch-task-output.md) .

## <a name="job-preparation-task"></a>Attività di preparazione di processo

Prima dell'esecuzione delle attività del processo, Batch esegue l'attività di preparazione processo su ogni nodo di calcolo è programmata per l'esecuzione di un'attività. Per impostazione predefinita, il servizio Batch attesa per l'attività di preparazione processo da completare prima di eseguire le attività pianificate da eseguire sul nodo. Tuttavia, è possibile configurare il servizio non è in attesa. Se si riavvia il nodo, viene eseguito di nuovo l'attività di preparazione di processo, ma è anche possibile disabilitare questo comportamento.

L'attività di preparazione processo viene eseguita solo su nodi pianificate per l'esecuzione di un'attività. In questo modo inutile esecuzione di un'attività di preparazione nel caso in cui un nodo non è assegnato un'attività. Questo può verificarsi quando il numero di attività per un processo è inferiore al numero dei nodi in un pool. Viene applicato anche quando si abilita [esecuzione dell'attività simultanee](batch-parallel-node-tasks.md) , in modo da lasciare alcuni nodi inattivo se il conteggio delle attività è inferiore alle attività simultanee totale. Eseguendo non mansione preparazione nei nodi inattivi, è possibile spesa nelle spese di trasferimento di dati.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] è diverso dal [CloudPool.StartTask] [ pool_starttask] in JobPreparationTask viene eseguita all'inizio di ogni processo, mentre StartTask viene eseguita solo quando un nodo di calcolo prima di tutto viene aggiunto un pool o si riavvia.

## <a name="job-release-task"></a>Attività di rilascio di processo

Quando un processo viene contrassegnato come completato, l'attività di rilascio processo viene eseguito su ogni nodo nel pool di esecuzione almeno un'attività. Un processo si contrassegna come completata da una richiesta di terminazione. Il servizio Batch quindi imposta lo stato del processo per *terminare*, termina le attività attive o esecuzione associate al processo e viene eseguita la versione mansione. Il processo quindi consente di spostare lo stato di *completamento* .

> [AZURE.NOTE] Eliminazione di processo esegue anche l'attività di rilascio di processo. Tuttavia, se è già stato terminato un processo, rilascia attività non viene eseguito una seconda volta se il processo viene eliminato in un secondo momento.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Processo prepara e rilasciare le attività con .NET Batch

Per utilizzare un'attività di preparazione del processo, assegnare un [JobPreparationTask] [ net_job_prep] oggetto [CloudJob.JobPreparationTask del processo] [ net_job_prep_cloudjob] proprietà. Analogamente, inizializzare un [JobReleaseTask] [ net_job_release] e assegnarla a [CloudJob.JobReleaseTask del processo] [ net_job_prep_cloudjob] proprietà per impostare l'attività di rilascio del processo.

In questo frammento di codice, `myBatchClient` è un'istanza di [BatchClient][net_batch_client], e `myPool` è un pool esistente all'interno di account Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Come detto in precedenza, rilascia attività viene eseguita quando un processo è terminato o eliminato. Terminare un processo con [JobOperations.TerminateJobAsync][net_job_terminate]. Eliminare un processo con [JobOperations.DeleteJobAsync][net_job_delete]. In genere terminare o eliminare un processo quando le attività vengano completate o è stato raggiunto un timeout che è stata definita.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Esempio di codice GitHub

Per vedere Preparazione al processo e rilasciare attività in azione, estrarre [JobPrepRelease] [ job_prep_release_sample] progetto di esempio in GitHub. Questa applicazione console vengono eseguite le operazioni seguenti:

1. Crea un pool con due nodi "piccoli".
2. Crea un processo con Preparazione del processo, rilasciare e attività standard.
3. Esegue l'attività di preparazione di processo, che scrive innanzitutto l'ID di nodo in un file di testo nella directory "condivisa" del nodo.
4. Viene eseguita un'attività su ciascun nodo che scrive il relativo ID attività allo stesso file di testo.
5. Una volta tutte le attività vengano completate (o viene raggiunto il timeout), stampa il contenuto del file di testo di ciascun nodo alla console.
6. Una volta completato il processo, viene eseguita la versione mansione per eliminare il file dal nodo.
6. Consente di stampare i codici di uscita le attività di preparazione e rilascio di processo per ogni nodo in cui vengono eseguiti.
7. Esecuzione di pause per consentire la conferma dell'eliminazione di processo e/o pool.

Output dall'applicazione di esempio è simile al seguente:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] A causa l'ora di creazione e iniziare a variabile dei nodi in un nuovo pool (alcuni nodi sono pronti per l'attività prima di altri utenti), è possibile riscontrare output diverso. In particolare, perché le attività completate rapidamente, uno dei nodi del pool può eseguire tutte le attività del processo. In questo caso, si noterà che il processo di preparazione e attività di rilascio non sono disponibili per il nodo che non eseguita alcuna attività.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Verificare la presenza preparazione del processo e attività di rilascio nel portale di Azure

Quando si esegue l'applicazione di esempio, è possibile utilizzare il [portale di Azure] [ portal] per visualizzare le proprietà del processo e le attività o anche scaricare il file di testo condivisa modificata dall'attività del processo.

Nella schermata seguente mostra **blade attività di preparazione** nel portale di Azure dopo l'esecuzione dell'applicazione di esempio. Passare alle proprietà *JobPrepReleaseSampleJob* dopo avere completato le attività (ma prima di eliminare il processo e pool) e fare clic su **attività di preparazione** o **attività di rilascio** per visualizzare le relative proprietà.

![Proprietà di preparazione del processo nel portale di Azure][1]

## <a name="next-steps"></a>Passaggi successivi

### <a name="application-packages"></a>Pacchetti di applicazioni

Oltre l'attività di preparazione di processo, è anche possibile usare la caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch per preparare l'ambiente nodi di calcolo per l'esecuzione di attività. Questa caratteristica è particolarmente utile per la distribuzione delle applicazioni che non richiedono l'esecuzione di un programma di installazione, applicazioni contenenti molti file (100 +) o applicazioni che richiedono il controllo delle versioni strict.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e dati di gestione temporanea

Questo post di forum MSDN viene fornita una panoramica dei diversi metodi di preparazione dei nodi per l'esecuzione di attività:

[Installazione delle applicazioni e dati nel Batch di gestione temporanea nodi di calcolo][forum_post]

Scritto da uno dei membri del team Azure Batch, vengono illustrate diverse tecniche per distribuire applicazioni e dati per nodi di calcolo.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
