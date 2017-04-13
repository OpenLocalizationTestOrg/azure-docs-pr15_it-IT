<properties
    pageTitle="Esercitazione - iniziare a utilizzare la libreria di Azure Batch .NET | Microsoft Azure"
    description="Informazioni sui concetti di base di Azure Batch e su come sviluppare per il servizio di Batch con uno scenario di esempio."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Iniziare con la raccolta di Azure Batch per .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Informazioni sulle nozioni fondamentali di [Azure Batch] [ azure_batch] e [.NET Batch] [ net_api] raccolta in questo articolo come illustrato un'applicazione di esempio c# passo dopo passo. Vengono illustrate come questa applicazione di esempio utilizza il servizio di Batch per elaborare un carico di lavoro in parallelo nel cloud, nonché come interagisce con [Lo spazio di archiviazione di Azure](../storage/storage-introduction.md) per il recupero e gestione temporanea file. Verranno fornite informazioni tecniche Batch di flusso di lavoro di applicazione. È anche possibile comprendere base dei componenti principali del Batch, ad esempio processi, attività, pool e nodi di calcolo.

![Flusso di lavoro soluzione batch (basic)][11]<br/>

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone una conoscenza di c# e Visual Studio. Inoltre, presuppone che è in grado di soddisfare i requisiti di creazione di account specificate di seguito per Azure e i servizi Batch e lo spazio di archiviazione.

### <a name="accounts"></a>Account

- **Account Azure**: se si dispone già di un abbonamento Azure, [creare un account Azure gratuito][azure_free_account].
- **Account batch**: dopo avere inserito una sottoscrizione Azure, [creare un account Azure Batch](batch-account-create-portal.md).
- **Account di archiviazione**: vedere [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [account di archiviazione su Azure](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Batch attualmente supporta *solo* il tipo di account archiviazione **Generale** , come descritto nel passaggio 5 # [Crea un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [account di archiviazione su Azure](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

È necessario che **Visual Studio 2015** per generare il progetto di esempio. È possibile trovare versioni gratuite o di valutazione di Visual Studio [Panoramica dei prodotti di Visual Studio 2015][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Esempio di codice *DotNetTutorial*

[DotNetTutorial] [ github_dotnettutorial] campione è uno dei molti esempi di codice trovati negli [esempi di batch azure] [ github_samples] archivio in GitHub. È possibile scaricare il campione facendo clic sul pulsante **Scarica ZIP** nella home page di archivio oppure facendo clic su [azure-batch-esempi-master.zip] [ github_samples_zip] collegamento diretto download. Una volta estratto il contenuto del file ZIP, si possono trovare la soluzione nella cartella seguente:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Explorer Batch (facoltativo)

[Azure Batch Explorer] [ github_batchexplorer] è un'utilità gratuita inclusa in [esempi di batch azure] [ github_samples] archivio in GitHub. Sebbene non sia necessario per completare questa esercitazione, può essere utile durante lo sviluppo e debug delle soluzioni il Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Informazioni generali progetto di esempio DotNetTutorial

Nell'esempio *DotNetTutorial* è una soluzione di Visual Studio 2015 che comprende due progetti: **DotNetTutorial** e **TaskApplication**.

- **DotNetTutorial** è l'applicazione client che interagisce con i servizi Batch e lo spazio di archiviazione per eseguire un carico di lavoro in parallelo su nodi (macchine virtuali). DotNetTutorial viene eseguito su workstation locale.

- **TaskApplication** è il programma che viene eseguito su nodi di calcolo di Azure per eseguire il lavoro effettivo. In questo esempio viene `TaskApplication.exe` analizza il testo in un file scaricato dallo spazio di archiviazione di Azure (file di input). Quindi viene generato un file di testo (file di output) che contiene un elenco delle prime tre parole presenti nel file di input. Dopo la creazione di file di output, per lo spazio di archiviazione di Azure TaskApplication caricamento del file. Rende disponibile all'applicazione client per il download. TaskApplication viene eseguita in parallelo su più nodi di calcolo nel servizio Batch.

Il diagramma seguente illustra le operazioni principale che vengono eseguite dall'applicazione client, *DotNetTutorial*e dell'applicazione in cui viene eseguito da attività, *TaskApplication*. Questo flusso di lavoro di base è tipico delle molte soluzioni di calcolo creati con Batch. Mentre non vengono visualizzati tutte le funzionalità disponibili nel servizio di Batch, quasi ogni scenario Batch include i processi simili.

![Flusso di lavoro di esempio batch][8]<br/>

[**Passaggio 1.**](#step-1-create-storage-containers) Creare **i contenitori** in archiviazione Blob Azure.<br/>
[**Passaggio 2.**](#step-2-upload-task-application-and-data-files) Caricare file di applicazione delle attività e input contenitori.<br/>
[**Passaggio 3.**](#step-3-create-batch-pool) Creare un Batch **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Pool **StartTask** scaricare i file binari di attività (TaskApplication) in nodi che si connettono del pool.<br/>
[**Passaggio 4.**](#step-4-create-batch-job) Creare un Batch **processo**.<br/>
[**Passaggio 5.**](#step-5-add-tasks-to-job) Aggiungere **attività** al processo.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Le attività vengono programmate per l'esecuzione su nodi.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Ogni attività download relativi dati di input dallo spazio di archiviazione di Azure e quindi inizia l'esecuzione.<br/>
[**Passaggio 6.**](#step-6-monitor-tasks) Monitorare l'attività.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Come le attività vengano completate, caricano i dati di output allo spazio di archiviazione Azure.<br/>
[**Passaggio 7.**](#step-7-download-task-output) Scaricare output attività dallo spazio di archiviazione.

Come detto, non ogni soluzione Batch esegue questa procedura esatta e possono essere presenti molti altri, ma l'applicazione di esempio *DotNetTutorial* viene processi comuni trovati una soluzione Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Compilare il progetto di esempio *DotNetTutorial*

Per poter eseguire correttamente l'esempio, è necessario specificare le credenziali dell'account Batch e lo spazio di archiviazione del progetto *DotNetTutorial* `Program.cs` file. Se non è già stato fatto, aprire la soluzione in Visual Studio facendo doppio clic sul `DotNetTutorial.sln` file soluzione. Oppure aprirlo da Visual Studio utilizzando il **File > Apri > progetto/soluzione** dal menu.

Apri `Program.cs` all'interno del progetto *DotNetTutorial* . Quindi aggiungere le credenziali specificate nella parte superiore del file:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Come detto in precedenza, è necessario specificare le credenziali per un account di archiviazione **Generale** attualmente in archiviazione Azure. Le applicazioni di Batch utilizzare archiviazione blob all'interno di account di archiviazione **Generale** . Non specificare le credenziali per un account di archiviazione che è stato creato, fare clic sul tipo di account di *archiviazione Blob* .

È possibile trovare le credenziali dell'account Batch e lo spazio di archiviazione all'interno e il conto di ogni servizio nel [portale di Azure][azure_portal]:

![Batch credenziali nel portale di][9]
![le credenziali di spazio di archiviazione nel portale][10]<br/>

Dopo aver aggiornato il progetto con le credenziali, fare doppio clic soluzione in Esplora soluzioni e fare clic su **Compila soluzione**. Confermare il ripristino di eventuali pacchetti NuGet se viene richiesto.

> [AZURE.TIP] Se non vengono ripristinati automaticamente i pacchetti NuGet o se vengono visualizzati errori relativi a un errore per ripristinare i pacchetti, verificare di disporre di [Gestione di pacchetti NuGet] [ nuget_packagemgr] installato. Quindi attivare il download dei pacchetti mancanti. Vedere [Abilitazione pacchetto ripristinare durante la compilazione] [ nuget_restore] per attivare il download del pacchetto.

Nelle sezioni seguenti sono suddividere l'applicazione di esempio in passaggi eseguiti per elaborare un carico di lavoro nel servizio Batch e illustrati i passaggi in dettaglio. È consigliabile fare riferimento alla soluzione aperta in Visual Studio mentre si lavora scorrere il resto di questo articolo, poiché viene descritto non ogni riga di codice nel campione.

Spostarsi all'inizio di `MainAsync` metodo del progetto *DotNetTutorial* `Program.cs` file per iniziare con il passaggio 1. Istruzioni dettagliate sotto quindi circa segue la progressione di chiamate in `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Passaggio 1: Creare contenitori di archiviazione

![Creare i contenitori di archiviazione Azure][1]
<br/>

Batch include il supporto per interagire con lo spazio di archiviazione di Azure. I contenitori di account di archiviazione costituiranno file necessari per le attività eseguite nell'account Batch. I contenitori offrono una posizione per archiviare i dati di output che generano le attività. La prima cosa l'applicazione client *DotNetTutorial* è creare tre contenitori in [Archiviazione Blob Azure](../storage/storage-introduction.md):

- **applicazione**: questo contenitore verrà archiviata l'applicazione eseguita le attività, nonché le relative dipendenze, ad esempio DLL.
- **input**: attività scaricare i file di dati per l'elaborazione di dal contenitore di *input* .
- **output**: attività completato l'elaborazione di file di input, sarà caricano i risultati al contenitore di *output* .

Per interagire con un account di archiviazione e creare contenitori, è utilizzare la [Libreria di Client di spazio di archiviazione di Azure per .NET][net_api_storage]. Creare un riferimento all'account con [CloudStorageAccount][net_cloudstorageaccount]e da cui creare un [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Viene utilizzata la `blobClient` Guida di riferimento in tutta l'applicazione e passare come parametro a diversi metodi. Un esempio è nel blocco di codice che segue immediatamente quanto sopra menzionato, nel punto in cui si chiama `CreateContainerIfNotExistAsync` per creare effettivamente i contenitori.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Dopo avere creati i contenitori, l'applicazione ora può caricare i file che verranno utilizzati per le attività.

> [AZURE.TIP] [Come usare archiviazione Blob da .NET](../storage/storage-dotnet-how-to-use-blobs.md) offre un'ampia panoramica dell'uso BLOB e contenitori di archiviazione di Azure. Dovrebbe essere nella parte superiore dell'elenco di lettura quando si inizia a lavorare con Batch.

## <a name="step-2-upload-task-application-and-data-files"></a>Passaggio 2: Caricare i file di dati delle applicazioni e delle attività

![Caricare file di input (dati) e applicazione di attività in contenitori][2]
<br/>

In operazione di caricamento di file, *DotNetTutorial* definisce innanzitutto insiemi **applicazione** e **input** percorsi dei file in cui si trovano nel computer locale. Quindi carica i file per i contenitori creato nel passaggio precedente.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Esistono due metodi in `Program.cs` impegnate nel processo di caricamento:

- `UploadFilesToContainerAsync`: Questo metodo restituisce un insieme di [ResourceFile] [ net_resourcefile] oggetti (come illustrati di seguito) e internamente chiamate `UploadFileToContainerAsync` per caricare ogni file passato nel parametro *filePaths* .
- `UploadFileToContainerAsync`: Si tratta del metodo che esegue il caricamento di file e crea [ResourceFile] effettivamente[ net_resourcefile] oggetti. Dopo il caricamento del file, ottiene una firma di accesso condiviso (SA) per il file e restituisce un oggetto ResourceFile che lo rappresenta. Firme sono descritti sotto l'accesso condiviso.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ net_resourcefile] vengono illustrate le attività in Batch con l'URL per un file di archiviazione di Azure che viene scaricata in un nodo di calcolo prima che venga eseguita l'attività. [ResourceFile.BlobSource] [ net_resourcefile_blobsource] proprietà specifica l'URL completo del file presenti nell'archiviazione Azure. L'URL può includere una firma di accesso condiviso (SA) che fornisce l'accesso sicuro al file. La maggior parte dei tipi di attività in Batch .NET includono una proprietà *ResourceFiles* , tra cui:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

L'applicazione di esempio DotNetTutorial non utilizzare i tipi di attività JobPreparationTask o JobReleaseTask, ma è possibile leggere informazioni relative ad esse in [esecuzione attività di preparazione e completamento mansioni nel Batch di Azure nodi di calcolo](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma di accesso condiviso (SA)

Accesso condiviso firme sono stringhe che, quando incluso come parte di un URL, fornire l'accesso sicuro ai contenitori e BLOB di archiviazione Azure. DotNetTutorial applicazione utilizza blob e contenitore condiviso accedere agli URL di firma e viene illustrato come ottenere le stringhe di firma accesso condiviso dal servizio di archiviazione.

- **BLOB condiviso firme access**: StartTask del pool in DotNetTutorial vengono utilizzate le firme di accesso di blob condiviso durante il download di file binari dell'applicazione e i file di dati di input dallo spazio di archiviazione (vedere Passaggio 3 riportata di seguito). Il `UploadFileToContainerAsync` metodo del DotNetTutorial `Program.cs` contiene il codice che ottiene firma dell'ogni blob accesso condiviso. Ciò avviene tramite la chiamata [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Contenitore condiviso firme access**: come ogni attività termina la sua attività sul nodo di calcolo, consente di caricare il file di output il contenitore di *output* in archiviazione Azure. A tale scopo, TaskApplication viene utilizzata una firma di accesso condiviso contenitore che fornisce l'accesso di scrittura al contenitore come parte del percorso quando carica i file. Ottenere la firma di accesso condiviso contenitore viene utilizzata in modo simile come per ottenere il blob condiviso firma di accesso. In DotNetTutorial, si noterà che il `GetContainerSasUrl` metodo di supporto chiama [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] per farlo. È possibile leggere informazioni su come TaskApplication utilizza il contenitore condiviso firma di access in "passaggio 6: controllare le attività."

> [AZURE.TIP] Estrarre la serie di due parti sulla firme accesso condiviso, [parte 1: informazioni sul modello di firma (SA) di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: creare e utilizzare una firma di accesso condiviso (SA) con archiviazione Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), per altre informazioni sull'accesso sicuro ai dati nel proprio account di archiviazione.

## <a name="step-3-create-batch-pool"></a>Passaggio 3: Creare pool Batch

![Creare un pool di Batch][3]
<br/>

Un Batch **pool** è una raccolta dei nodi di calcolo (macchine virtuali) in cui Batch esegue le attività del processo.

Dopo che consente di caricare i file di dati delle applicazioni e account di archiviazione, *DotNetTutorial* avvia l'interazione con il servizio Batch utilizzando la libreria di .NET Batch. A tale scopo, un [BatchClient] [ net_batchclient] appena creato:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Successivamente, viene creato un pool dei nodi di calcolo nella finestra account Batch con una chiamata a `CreatePoolAsync`. `CreatePoolAsync`Usa [BatchClient.PoolOperations.CreatePool] [ net_pool_create] metodo per la creazione di un pool in servizio Batch.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Quando si crea un pool con [CreatePool][net_pool_create], si specificano più parametri, ad esempio il numero dei nodi di calcolo e le [dimensioni dei nodi](../cloud-services/cloud-services-sizes-specs.md)del sistema operativo i nodi. In *DotNetTutorial*, viene utilizzata [CloudServiceConfiguration] [ net_cloudserviceconfiguration] per specificare Windows Server 2012 R2 dai [Servizi Cloud](../cloud-services/cloud-services-guestos-update-matrix.md). Tuttavia, specificando un [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] se, tuttavia, è possibile creare pool di nodi creati da immagini Marketplace, che include le immagini di Windows e Linux, per ulteriori informazioni, vedere [disposizione Linux nodi in Batch Azure pool](batch-linux-nodes.md) .

> [AZURE.IMPORTANT] Addebitate per le risorse di elaborazione batch. Per ridurre al minimo i costi, è possibile ridurre `targetDedicated` su 1 prima di eseguire l'esempio.

Insieme a queste proprietà nodo fisico, è inoltre possibile specificare un [StartTask] [ net_pool_starttask] per il pool. Il StartTask venga eseguito su ciascun nodo come tale nodo viene aggiunto il pool e ogni volta che si riavvia un nodo. Il StartTask è particolarmente utile per l'installazione di applicazioni su nodi di calcolo prima dell'esecuzione dell'attività. Se, ad esempio, le attività elaborare dati usando gli script Python, è possibile utilizzare una StartTask per installare Python sui nodi di calcolo.

In questa applicazione di esempio, il StartTask vengono copiati i file scaricati dallo spazio di archiviazione (che vengono specificati utilizzando [StartTask][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] proprietà) dalla directory di lavoro StartTask alla directory condivisa che possono accedere a *tutte* le attività in esecuzione sul nodo. In pratica, viene copiata `TaskApplication.exe` e le dipendenze alla directory condivisa su ciascun nodo come il nodo viene aggiunto il pool, in modo che tutte le attività da eseguire sul nodo possano accedervi.

> [AZURE.TIP] La caratteristica di **pacchetti di applicazioni** del Batch di Azure fornisce un altro modo per ottenere l'applicazione su nodi di calcolo in un pool. Per informazioni dettagliate, vedere [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md) .

Anche significativo frammento di codice precedente è l'utilizzo di due variabili di ambiente nella *riga di comando* proprietà del StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Ogni nodo di calcolo di un pool di Batch viene configurato automaticamente con diverse variabili di ambiente specifici di Batch. Qualsiasi processo che viene eseguita da un'attività può accedere a tali variabili.

> [AZURE.TIP] Per ulteriori informazioni sull'ambiente di variabili disponibili su nodi di calcolo in un pool di Batch e informazioni nella directory di lavoro di attività, vedere le sezioni di [impostazioni dell'ambiente per le attività](batch-api-basics.md#environment-settings-for-tasks) e [file e directory](batch-api-basics.md#files-and-directories) nella [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Passaggio 4: Creare processo Batch

![Crea processo Batch][4]<br/>

Un Batch **processo** è un insieme di attività ed è associata a un pool di nodi di calcolo. Le attività in un processo eseguire su nodi di calcolo del pool associato.

È possibile utilizzare un processo non solo per l'organizzazione e la verifica delle attività in carichi di lavoro correlati, ma anche per l'imposizione di determinati vincoli, ad esempio il massimo di esecuzione del processo di (e di conseguenza, delle attività) e la priorità del processo in relazione ad altri processi nell'account Batch. In questo esempio, tuttavia, il processo è associato solo al pool creato nel passaggio 3 #. Nessuna proprietà aggiuntive vengono configurati.

Tutti i processi Batch sono associati a uno specifico gruppo. Questa associazione indica i nodi che verranno eseguite le attività del processo. Specificare tramite [CloudJob.PoolInformation] [ net_job_poolinfo] proprietà, come illustrato nel frammento di codice riportata di seguito.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Una volta creato un processo, le attività vengono aggiunti per eseguire il lavoro.

## <a name="step-5-add-tasks-to-job"></a>Passaggio 5: Aggiungere attività al processo

![Aggiungere attività al lavoro][5]<br/>
*(1) le attività vengono aggiunte al processo, (2) le attività vengono programmate per l'esecuzione su nodi e (3) le attività scaricare i file di dati per l'elaborazione*

Batch **attività** sono le singole unità di lavoro da eseguire sui nodi di calcolo. Un'attività è una riga di comando ed esegue gli script o eseguibili specificati in tale riga di comando.

Per eseguire effettivamente lavoro, è necessario aggiungere attività a un processo. Ogni [CloudTask] [ net_task] viene configurato tramite una proprietà della riga di comando e [ResourceFiles] [ net_task_resourcefiles] (come StartTask del pool) che l'attività Scarica il nodo prima che la riga di comando viene eseguita automaticamente. Nel progetto di esempio *DotNetTutorial* ogni attività consente di elaborare un solo file. In questo modo, insieme ResourceFiles contiene un singolo elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Quando accedono a variabili di ambiente come `%AZ_BATCH_NODE_SHARED_DIR%` o eseguire un'applicazione non trovata nel nodo `PATH`, le righe di comando attività deve essere precedute da `cmd /c`. Verrà in modo esplicito eseguire interprete e indicare l'interruzione dopo aver eseguito il comando. Questo requisito è necessario se un'applicazione di esecuzione delle attività nel nodo `PATH` (ad esempio *robocopy.exe* o *powershell.exe*) e non vengono usate alcuna variabile di ambiente.

All'interno di `foreach` ciclo frammento di codice precedente, è possibile vedere che la riga di comando per l'attività sia impostata in modo che vengono passati tre argomenti della riga di comando per *TaskApplication.exe*:

1. L' **primo argomento** è il percorso del file per l'elaborazione. Questo è il percorso del file presente nel nodo. Quando il ResourceFile oggetto `UploadFileToContainerAsync` prima di tutto è stato creato in precedenza, il nome del file è stato usato per questa proprietà (come parametro al costruttore ResourceFile). Indica che è possibile trovare il file nella stessa directory *TaskApplication.exe*.

2. L' **secondo argomento** specifica che le prime parole *N* scrivere il file di output. Nell'esempio seguente viene hardcoded in modo che le prime tre parole scritte al file di output.

3. L' **terzo argomento** è la firma di accesso condiviso (SA) che fornisce l'accesso di scrittura per il contenitore di **output** in archiviazione Azure. *TaskApplication.exe* Usa questo URL firma accesso condiviso quando carica file di output allo spazio di archiviazione Azure. È possibile trovare il codice per l'oggetto nella `UploadFileToContainer` metodo del progetto TaskApplication `Program.cs` file:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Passaggio 6: Attività Monitor

![Controllare le attività][6]<br/>
*L'applicazione client (1) esegue il monitoraggio delle attività per il completamento e stato di esito positivo e (2) le attività caricare dati del risultato allo spazio di archiviazione di Azure*

Quando le attività vengono aggiunti a un processo, vengono automaticamente in coda e pianificare l'esecuzione su nodi di calcolo del pool associate al processo. In base alle impostazioni specificate, Batch gestisce Accodamento tutte le attività, pianificazione, la ripetizione e altre funzioni di amministrazione di attività dell'utente. Sono disponibili molti approcci alla monitoraggio esecuzione dell'attività. DotNetTutorial Mostra un semplice esempio che segnala solo per successo o errore completamento e attività uniti.

All'interno di `MonitorTasks` metodo del DotNetTutorial `Program.cs`, sono disponibili tre concetti .NET Batch in cui è consigliato discussione. Nell'ordine di visualizzazione vengano sono elencate di seguito:

1. **ODATADetailLevel**: specificare [ODATADetailLevel] [ net_odatadetaillevel] nell'elenco operazioni (ad esempio per ottenere un elenco di attività del processo) è fondamentale per garantire prestazioni dell'applicazione Batch. Aggiungere [il servizio di Azure Batch della Query in modo efficiente](batch-efficient-list-queries.md) all'elenco di lettura, se si prevede di eseguire qualsiasi tipo di stato monitoraggio all'interno delle applicazioni di Batch.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] include le applicazioni di .NET Batch con utilità di supporto per il monitoraggio attività uniti. In `MonitorTasks`, *DotNetTutorial* attesa per tutte le attività raggiungere [TaskState.Completed] [ net_taskstate] entro un termine. Terminare quindi il processo.

3. **TerminateJobAsync**: terminare un processo con [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (o JobOperations.TerminateJob blocco) contrassegna il processo come completata. È fondamentale per farlo se la soluzione Batch utilizza un [JobReleaseTask][net_jobreltask]. Si tratta di un tipo speciale di attività, nelle [attività di preparazione e completamento processo](batch-job-prep-release.md).

Il `MonitorTasks` dei metodi descritti *DotNetTutorial*di `Program.cs` viene visualizzata sotto:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Passaggio 7: Scaricare output attività

![Scaricare output attività dallo spazio di archiviazione][7]<br/>

Ora che il processo, l'output da attività può essere scaricato dallo spazio di archiviazione Azure. Questa operazione viene eseguita con una chiamata a `DownloadBlobsFromContainerAsync` in *DotNetTutorial*del `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] La chiamata a `DownloadBlobsFromContainerAsync` in *DotNetTutorial* applicazione specifica che devono essere scaricati i file per il `%TEMP%` cartella. È possibile modificare la posizione per l'output.

## <a name="step-8-delete-containers"></a>Passaggio 8: I contenitori Elimina

Poiché vengono addebitate per i dati che si trovano in archiviazione Azure, è sempre consigliabile rimuovere qualsiasi blob che non sono più necessari per i processi Batch. Del DotNetTutorial `Program.cs`, questa operazione viene eseguita con tre chiamate al metodo di supporto `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Il metodo semplicemente ottenuto un riferimento al contenitore e quindi chiama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passaggio 9: Eliminare il processo e al pool

Nel passaggio finale, viene chiesto di eliminare il processo e al pool che sono stati creati dall'applicazione DotNetTutorial. Sebbene non addebitate per processi e delle attività, non *vengono* addebitate per nodi di calcolo. In questo modo, è consigliabile allocare nodi solo in base alle esigenze. Eliminazione pool inutilizzati possono far parte del processo di manutenzione.

Del BatchClient [JobOperations] [ net_joboperations] e [PoolOperations] [ net_pooloperations] dispongono di corrispondenti metodi di eliminazione, ossia se l'utente conferma l'eliminazione:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Tenere presente che addebitate per risorse di elaborazione, ovvero eliminazione pool inutilizzato ridurrà costo. Inoltre, tenere presente che un pool se si elimina tutti i nodi di calcolo all'interno di tale pool e che tutti i dati nei nodi sarà irreversibili dopo l'eliminazione di pool.

## <a name="run-the-dotnettutorial-sample"></a>Eseguire l'esempio *DotNetTutorial*

Quando si esegue l'applicazione di esempio, l'output sarà simile al seguente. Durante l'esecuzione, si verificherà una pausa in `Awaiting task completion, timeout in 00:30:00...` durante l'avvio di nodi di elaborazione del pool. Usare il [portale di Azure] [ azure_portal] per controllare il pool, calcolare nodi, processo e attività durante e dopo l'esecuzione. Usare il [portale di Azure] [ azure_portal] o [Esplora archivi Azure] [ storage_explorers] per visualizzare le risorse di archiviazione (contenitori e BLOB) che vengono create dall'applicazione.

Tempo di esecuzione tipico è **circa 5 minuti** quando si esegue l'applicazione nella configurazione predefinita.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Passaggi successivi

È possibile apportare modifiche a *DotNetTutorial* e *TaskApplication* in sperimentare calcolo diversi scenari. Ad esempio, provare ad aggiungere un ritardo di esecuzione all' *TaskApplication*, ad esempio con [Sleep][net_thread_sleep], per simulare attività lunga e monitorare le nel portale. Provare aggiunta di altre attività o regolare il numero dei nodi di calcolo. Aggiungere logica per controllare e consentire l'utilizzo di un pool esistente per il tempo di esecuzione di velocità (*Suggerimento*: estrarre `ArticleHelpers.cs` in [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] progetto negli [esempi di batch azure][github_samples]).

Ora che si ha familiarità con il flusso di lavoro di base di una soluzione Batch, è possibile eseguire un'analisi ad altre caratteristiche del servizio di Batch.

- Leggere la [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md), che è consigliabile per tutti i nuovi utenti di Batch.
- Avviare gli altri articoli di sviluppo Batch in **sviluppo in modo completo** per il [percorso formativo: Batch][batch_learning_path].
- Estrarre una diversa implementazione di elaborazione il carico di lavoro di "parole primi N" utilizzando Batch in [TopNWords] [ github_topnwords] campione.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Creare i contenitori di archiviazione Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Caricare file di input (dati) e applicazione di attività in contenitori"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Creare pool Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Crea processo Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Aggiungere attività al lavoro"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Controllare le attività"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Scaricare output attività dallo spazio di archiviazione"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flusso di lavoro soluzione batch (diagramma completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenziali batch nel portale"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenziali di spazio di archiviazione nel portale"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flusso di lavoro soluzione batch (diagramma minimo)"
