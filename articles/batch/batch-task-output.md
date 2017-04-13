<properties
    pageTitle="Processo e attività output permanenza Batch Azure | Microsoft Azure"
    description="Informazioni su come utilizzare lo spazio di archiviazione di Azure come un archivio persistente per le attività Batch e il processo di output e attivare la visualizzazione di questo output persistente nel portale di Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Mantenere l'output del processo e attività Batch Azure

Le attività che eseguire in Batch in genere producono output che deve essere archiviati e recuperati in un secondo momento da altre attività del processo, l'applicazione client che esegue il processo o entrambe. L'output potrebbe essere file creati mediante l'elaborazione di dati di input o accedere file associati all'esecuzione dell'attività. In questo articolo introduce una libreria di classi .NET che utilizza una tecnica di convenzioni di mantenere tali output di attività a archiviazione Blob Azure, renderlo disponibile anche se si elimina il pool, processi e nodi di calcolo.

Utilizzando la tecnica in questo articolo, è possibile visualizzare l'output di attività in **file di output salvato** e **registri salvati** nel [portale di Azure][portal].

![File di output salvato e salvataggio dei registri selettori nel portale][1]

>[AZURE.NOTE] [Convenzioni di File Batch Azure] [ nuget_package] libreria di classi .NET descritta in questo articolo è attualmente in anteprima. Alcune delle funzionalità descritte qui possono cambiare prima di disponibilità generale.

## <a name="task-output-considerations"></a>Considerazioni sulla output di attività

Quando si progetta soluzione Batch, è necessario prendere in considerazione alcuni fattori relativi alle uscite mansione e delle attività.

* **Calcolare la durata nodo**: calcolare nodi spesso sono temporanei, in particolare in pool autoscale abilitato. Gli output delle attività da eseguire su un nodo sono disponibili solo il nodo è presente, ma solo entro il tempo di conservazione dei file impostate per l'attività. Per mantenere l'output di attività, le attività devono pertanto caricare i propri file di output in un archivio permanente, ad esempio lo spazio di archiviazione Azure.

* **Lo spazio di archiviazione di output**: per mantenere i dati di output delle attività in un archivio permanente, è possibile utilizzare [Lo spazio di archiviazione Azure SDK](../storage/storage-dotnet-how-to-use-blobs.md) nel codice attività per caricare l'output di attività in un contenitore di spazio di archiviazione Blob. Se è implementare un contenitore e una convenzione di denominazione dei file, l'applicazione client o altre attività nel processo quindi individuare e scaricare l'output in base alla convenzione.

* **Recupero di output**: È possibile recuperare output attività direttamente da nodi di calcolo nel pool o dallo spazio di archiviazione di Azure se l'attività viene conservata l'output. Per recuperare l'output di un'attività direttamente da un nodo di calcolo, è necessario il nome del file e la relativa posizione di output sul nodo. Se si continuano a output allo spazio di archiviazione di Azure, attività downstream o applicazione client deve contenere il percorso completo del file in archiviazione Azure per scaricarlo utilizzando Azure archiviazione SDK.

* **Visualizzazione di output**: quando si passare a un'attività Batch nel portale di Azure e selezionare **i file sul nodo**, viene visualizzata con tutti i file associati all'attività, non solo i file di output che interessa. Nuovo file su nodi di calcolo sono disponibili solo quando il nodo esiste ed solo durante il periodo di conservazione dei file è stato impostato per l'attività. Per visualizzare l'output di attività che è stata mantenuta allo spazio di archiviazione di Azure nel portale di oppure un'applicazione come [Esplora archivi Azure][storage_explorer], è necessario conoscere la posizione e passare direttamente al file.

## <a name="help-for-persisted-output"></a>Guida per l'output persistente

Per informazioni su come più facilmente persistenti processo e attività di output, il team Batch è definito e implementato un insieme di convenzioni di denominazione, nonché una libreria di classi .NET, le [Convenzioni di File Batch Azure] [ nuget_package] raccolta, che è possibile utilizzare nelle applicazioni Batch. Inoltre, il portale di Azure viene rilevato tali convenzioni di denominazione in modo da poter trovare facilmente i file che hanno archiviato usando la raccolta.

## <a name="using-the-file-conventions-library"></a>Utilizzo della libreria di convenzioni di file

[Convenzioni di File Batch Azure] [ nuget_package] è una libreria di classi .NET applicazioni .NET Batch consente di archiviare e recuperare l'output di un'attività da e verso lo spazio di archiviazione di Azure facilmente. È previsto per l'utilizzo in client e attività codice - attività per i file persistenti e codice client per elencare e recuperarli. Le attività inoltre possono utilizzare la libreria per il recupero di output delle attività padre, ad esempio in uno scenario di [relazioni tra attività](batch-task-dependencies.md) .

La raccolta convenzioni si occupa per garantire che attività e lo spazio di archiviazione contenitori output file sono denominati in base alla convenzione e caricare la soluzione quando mantenute allo spazio di archiviazione Azure. Quando si recuperano output, è possibile individuare facilmente gli output per un determinato processo o un'attività dall'elenco o il recupero di output, ID e scopo, anziché dover conoscere i nomi di file o la posizione all'interno dello spazio di archiviazione.

Ad esempio, è possibile utilizzare la libreria per "elencare tutti i file intermedi per attività 7" o "ottenere me l'anteprima per processo *mymovie*," senza dover conoscere i nomi di file o posizione all'interno di account di archiviazione.

### <a name="get-the-library"></a>Ottenere la libreria

È possibile ottenere la raccolta che contiene le classi nuove ed estende [CloudJob] [ net_cloudjob] e [CloudTask] [ net_cloudtask] classi con nuovi metodi da [NuGet][nuget_package]. È possibile aggiungerlo al progetto di Visual Studio con [Gestione pacchetti raccolta NuGet][nuget_manager].

>[AZURE.TIP] È possibile trovare il [codice sorgente] [ github_file_conventions] per la raccolta di Azure convenzioni di File Batch nel GitHub in Microsoft Azure SDK per repository .NET.

## <a name="requirement-linked-storage-account"></a>Requisito: account di archiviazione collegato

Per archiviare output allo spazio di archiviazione permanente usando la raccolta di file convenzioni e visualizzarle nel portale di Azure, è necessario [un account di archiviazione Azure collegamento](batch-application-packages.md#link-a-storage-account) al proprio account Batch. Se non è ancora disponibile, collegare un account di archiviazione per l'account Batch tramite il portale di Azure:

**Account batch** blade > **Impostazioni** > **Account di archiviazione** > **Account di archiviazione** (nessuno) > selezionare un account di archiviazione in abbonamento

Per una panoramica più dettagliata sul collegamento di un account di archiviazione, vedere [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md).

## <a name="persist-output"></a>Mantenere output

Esistono due azioni principali da eseguire durante il salvataggio di output mansione e delle attività con la raccolta di file convenzioni: creare il contenitore di spazio di archiviazione e salvare l'output al contenitore.

>[AZURE.WARNING] Poiché tutti gli output mansione e delle attività vengono memorizzati nello stesso contenitore, [lo spazio di archiviazione limitazioni](../storage/storage-performance-checklist.md#blobs) potrebbe applicate se un numero elevato di attività tenta di mantenere i file contemporaneamente.

### <a name="create-storage-container"></a>Creare lo spazio di archiviazione contenitore

Prima che le attività iniziano mantenimento output allo spazio di archiviazione, è necessario creare un contenitore di spazio di archiviazione blob a cui si viene caricata l'output. Eseguire questa operazione chiamando [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Questo metodo di estensione richiede un [CloudStorageAccount] [ net_cloudstorageaccount] oggetto come parametro e creare un contenitore denominato in modo che il contenuto individuabile dal portale di Azure e i metodi di recupero descritti più avanti in questo articolo.

In genere questo codice viene inserito nell'applicazione client, ossia l'applicazione che consente di creare il pool di processi e attività.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Archiviare output di un'attività

Ora che è stato creato un contenitore di archiviazione blob, attività possono salvare l'output al contenitore utilizzando [TaskOutputStorage] [ net_taskoutputstorage] classe trovata nella raccolta di convenzioni di file.

Nel codice attività prima di tutto creare una [TaskOutputStorage] [ net_taskoutputstorage] oggetto, quindi quando l'attività del completamento dell'operazione, chiamare [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] per salvare l'output allo spazio di archiviazione Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Il parametro "generare l'output di tipo" Categorizza file persistenti. Sono disponibili quattro predefiniti [TaskOutputKind] [ net_taskoutputkind] tipi: "TaskOutput", "TaskPreview", "TaskLog" e "TaskIntermediate". È inoltre possibile definire tipi personalizzati, se sono utili per il flusso di lavoro.

Questi tipi di output consentono di specificare il tipo di output per visualizzare un elenco eseguendo la query in un secondo momento Batch output persistente di una determinata attività. In altre parole, quando si elencano gli output per un'attività, è possibile filtrare l'elenco in uno dei tipi di output. Ad esempio, "forniscono l'output di *Anteprima* per attività *109*." Più sull'elenco e il recupero di output viene visualizzata nel [recuperare output](#retrieve-output) avanti in questo articolo.

>[AZURE.TIP] Il tipo di output designa anche nel punto in cui il portale di Azure in cui viene visualizzato un file specifico: *TaskOutput*-categorizzati file presenti in "File di output attività" e *TaskLog* file visualizzati in "I registri delle attività".

### <a name="store-job-outputs"></a>Archivio processi output

Oltre a memorizzare output di un'attività, è possibile archiviare gli output associati a un intero processo. Ad esempio, in attività Stampa unione di un processo di rendering filmato è Impossibile mantenere il filmato completamente rendering come output un processo. Al termine del processo, l'applicazione client può semplicemente elenco e recuperare l'output per il processo e non è necessario eseguire una query singole attività.

Archiviare output processo chiamando [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] metodo, e specificare [JobOutputKind] [ net_joboutputkind] e il nome file:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Quando con TaskOutputKind per output di un'attività, si usa [JobOutputKind] [ net_joboutputkind] parametro per suddividere un processo di mantenere i file. Questo parametro consente di eseguire query successiva per un tipo specifico di output (elenco). Il JobOutputKind include i tipi di output e anteprima e supporta la creazione di tipi di personalizzati.

### <a name="store-task-logs"></a>Registri delle attività

Oltre al mantenimento di un file di archivio permanente al completamento di un'attività o un processo, potrebbe essere necessario mantenere i file sono aggiornati durante l'esecuzione di un'attività, ossia file di log o `stdout.txt` e `stderr.txt`, ad esempio. A tale scopo, la libreria di Azure Batch File convenzioni fornisce [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] metodo. Con [SaveTrackedAsync][net_savetrackedasync], è possibile tenere traccia degli aggiornamenti a un file sul nodo (in un intervallo che specifica) e mantenere gli aggiornamenti allo spazio di archiviazione Azure.

Frammento di codice seguenti, serve [SaveTrackedAsync] [ net_savetrackedasync] aggiornare `stdout.txt` in archiviazione Azure ogni 15 secondi durante l'esecuzione dell'attività:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`è sufficiente un segnaposto per il codice per l'esecuzione in genere l'attività. Ad esempio, potrebbe essere presente Scarica dati dallo spazio di archiviazione di Azure, che esegue trasformazione o calcolo su di esso. La parte importante di questo frammento di codice è che illustra come è possibile disporre tale codice in un `using` blocco aggiornare periodicamente un file con [SaveTrackedAsync][net_savetrackedasync].

Il `Task.Delay` al fine di questo è necessario `using` blocco per garantire che l'agente di nodo è stata eseguita per cancellare il contenuto di output standard per il file stdout.txt nel nodo (agente di nodo è un programma che viene eseguito su ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch). Immediatamente, è possibile perdere gli ultimi secondi di output. Il ritardo potrebbe non essere necessario per tutti i file.

>[AZURE.NOTE] Quando si abilita file verifica con SaveTrackedAsync, solo *aggiunge* al file rilevato vengono mantenute allo spazio di archiviazione Azure. Utilizzare questo metodo solo per tenere traccia di file di log non rotazione o altri file che vengono accodati alla, ovvero, i dati viene aggiunta alla fine del file solo quando viene aggiornato.

## <a name="retrieve-output"></a>Recuperare l'output

Quando si recuperano l'output persistente utilizzando la raccolta di Azure Batch File convenzioni, questa operazione in modo basato su attività e a processo. È possibile richiedere l'output per determinate attività o un processo senza dover conoscere il percorso in archiviazione blob o anche il nome del file. È possibile semplicemente ad esempio, "Scarica i file di output per attività *109*."

Frammento di codice riportato scorre tutte le attività del processo, stampa alcune informazioni sui file di output per l'attività e quindi scarica propri file dallo spazio di archiviazione.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Output di un'attività e il portale di Azure

Portale di Azure consente di visualizzare registri che vengono mantenuti e output di un'attività a un account di archiviazione Azure collegato utilizzando le convenzioni di denominazione trovate nel [File Leggimi convenzioni di Azure Batch File][github_file_conventions_readme]. È possibile implementare tali convenzioni in una lingua scelta oppure è possibile utilizzare la libreria di convenzioni di file nelle applicazioni .NET.

### <a name="enable-portal-display"></a>Attivare la visualizzazione del portale

Per abilitare la visualizzazione dell'output nel portale, è necessario soddisfare i requisiti seguenti:

 1. [Collegamento a un account di archiviazione Azure](#requirement-linked-storage-account) al proprio account Batch.
 2. Rispettare le convenzioni di denominazione predefinite per i contenitori di spazio di archiviazione e i file durante il salvataggio di output. È possibile trovare la definizione di tali convenzioni nella raccolta convenzioni file [Leggimi][github_file_conventions_readme]. Se si utilizza le [Convenzioni di File Batch Azure] [ nuget_package] viene soddisfatta una raccolta per mantenere l'output, questo requisito.

### <a name="view-outputs-in-the-portal"></a>Output di visualizzazione nel portale

Per visualizzare l'output di un'attività e dei registri nel portale di Azure, passare all'attività il cui output desiderato, quindi fare clic su **file di output salvata** o **registri salvati**. Questa immagine mostra i **file di output salvato** per l'attività con ID "007":

![Attività vengono restituiti blade nel portale di Azure][2]

## <a name="code-sample"></a>Esempio di codice

[PersistOutputs] [ github_persistoutputs] progetto di esempio è uno degli [esempi di codice Azure Batch] [ github_samples] su GitHub. Questa soluzione Visual Studio 2015 viene illustrato come utilizzare la libreria di Azure Batch File convenzioni mantenere output di attività in un archivio permanente. Per eseguire l'esempio, procedere come segue:

1. Aprire il progetto in **Visual Studio 2015**.
2. Aggiungere il Batch di spazio di archiviazione e **le credenziali dell'account** **AccountSettings.settings** nel progetto Microsoft.Azure.Batch.Samples.Common.
3. **Creare** (ma non vengono eseguite) la soluzione. Ripristinare le eventuali pacchetti NuGet se richiesto.
4. Usare il portale di Azure per caricare un [pacchetto dell'applicazione](batch-application-packages.md) **PersistOutputsTask**. Includere la `PersistOutputsTask.exe` e agli assembly dipendenti nel pacchetto ZIP, impostare l'ID dell'applicazione da "PersistOutputsTask" e la versione del pacchetto dell'applicazione "1.0".
5. **Avviare** progetto (Esegui) **PersistOutputs** .

## <a name="next-steps"></a>Passaggi successivi

### <a name="application-deployment"></a>Distribuzione di applicazioni

La caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch offre un modo semplice per entrambe distribuire e versione le applicazioni di esecuzione delle attività in nodi di calcolo.

### <a name="installing-applications-and-staging-data"></a>Installazione delle applicazioni e dati di gestione temporanea

Estrarre i [nodi di calcolo di installazione applicazioni e dati di gestione temporanea nel Batch] [ forum_post] inserire post nel forum Batch Azure per una panoramica dei diversi metodi di preparazione dei nodi per l'esecuzione di attività. Scritto da uno dei membri del team Batch Azure, questo post è una panoramica utile in diversi modi per trasferire i file (inclusi applicazioni e dati di input attività) i nodi di calcolo e alcune considerazioni speciali per ogni metodo.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "File di output salvato e salvataggio dei registri selettori nel portale"
[2]: ./media/batch-task-output/task-output-02.png "Attività vengono restituiti blade nel portale di Azure"