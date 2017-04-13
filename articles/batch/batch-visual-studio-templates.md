<properties
    pageTitle="Modelli di Visual Studio per Batch Azure | Microsoft Azure"
    description="Informazioni su come questi modelli di progetto di Visual Studio consentono di implementare ed eseguire il carico di lavoro complesse Azure Batch"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Modelli di progetto di Visual Studio per Azure Batch

Il **Processo di gestione** e **modelli di attività processore Visual Studio** per Batch fornire codice che consentono di implementare ed eseguire i carichi di lavoro complesse in Batch con il minor numero di tentativi. In questo documento vengono illustrati questi modelli e fornisce istruzioni su come usarli.

>[AZURE.IMPORTANT] In questo articolo vengono illustrati solo le informazioni applicabili a questi due modelli e si presuppone che si ha familiarità con i concetti relativi a tale servizio Batch: pool, calcolare nodi, processi e attività, Gestione attività, le variabili di ambiente e altre informazioni. Ulteriori informazioni, [Nozioni di base di Azure Batch](batch-technical-overview.md) [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md)e [iniziare a utilizzare la libreria di Azure Batch per .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Panoramica

I modelli di gestione di processo e processore Task possono essere usati per creare due componenti utili:

* Gestione mansione che implementa una barra di divisione di processo che è possibile suddividere un processo in più attività che è possibile eseguire in modo indipendente, in parallelo.

* Processore attività che può essere utilizzato per eseguire pre-elaborazione e la successiva elaborazione all'interno di una riga di comando dell'applicazione.

Ad esempio, in uno scenario di rendering filmato, la barra di divisione di processo da trasformare un processo singolo filmato in centinaia o migliaia di attività separate da elaborare singoli frame separatamente. Analogamente, processore attività da richiamare l'applicazione di rendering e tutti i processi dipendenti sono necessari per il rendering di ogni frame, nonché eseguono azioni aggiuntive (ad esempio, la copia di rendering cornice in una posizione di archiviazione).

>[AZURE.NOTE] I modelli di processo responsabile e processore Task sono indipendenti tra loro, in modo che è possibile scegliere di utilizzare entrambi o solo uno di questi, in base ai requisiti del processo di calcolo e su Preferenze.

Come mostrato nella figura seguente, un processo di calcolo che utilizza questi modelli andrà tre fasi:

1. Il codice client (ad esempio, applicazione di servizio web, e così via) invia un processo al servizio Batch su Azure, che specifica come il proprio gestore processo attività il programma di gestione del processo.

2. Il servizio Batch viene eseguito la mansione manager su un nodo di calcolo e la barra di divisione di processo avvia il numero specificato di attività processore attività, in come molti nodi di calcolo in base alle esigenze in base ai parametri e specifiche nel codice di divisione di processo.

3. Le attività di processore attività eseguite in modo indipendente, in parallelo, per elaborare i dati di input e generare i dati di output.

![Diagramma che mostra come codice client interagisce con il servizio di Batch][diagram01]

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare i modelli di Batch, sarà necessario le operazioni seguenti:

* Un computer con Visual Studio 2015, o versione successiva, in cui è già installato.

* I modelli di Batch, sono disponibili nella [Raccolta di Visual Studio] [ vs_gallery] come estensioni di Visual Studio. Esistono due modi per ottenere i modelli:

  * Installare i modelli con la finestra di dialogo **estensioni e aggiornamenti** in Visual Studio (per ulteriori informazioni, vedere [ricerca e utilizzo delle estensioni di Visual Studio][vs_find_use_ext]). Nella finestra di dialogo **estensioni e aggiornamenti** , cercare e scaricare le seguenti estensioni:

    * Gestione di processo Batch Azure con barra di divisione di processo
    * Processore Task Batch Azure

  * Scaricare i modelli nella raccolta online per Visual Studio: [Modelli di progetto di Microsoft Azure Batch][vs_gallery_templates]

* Se si prevede di usare la caratteristica di [Pacchetti di applicazioni](batch-application-packages.md) per distribuire il responsabile di processo e processore task al Batch nodi di calcolo, è necessario collegare un account di archiviazione al proprio account Batch.

## <a name="preparation"></a>Preparazione

È consigliabile creare una soluzione che può contenere il gestore di processi, nonché il processore task, perché questo può rendere più semplice condividere codice tra il gestore dei processi e i programmi di processore attività. Per creare la soluzione, procedere come segue:

1. Aprire Visual Studio 2015 e selezionare **File** > **Nuovo** > **progetto**.

2. In **modelli**, espandere **Altri tipi di progetto**, fare clic su **Soluzioni di Visual Studio**e quindi selezionare **Soluzione vuota**.

3. Digitare un nome che descriva l'applicazione e lo scopo di questa soluzione (ad esempio, "LitwareBatchTaskPrograms").

4. Per creare la nuova soluzione, fare clic su **OK**.

## <a name="job-manager-template"></a>Modello di processo Manager

Il modello di processo Manager consente di implementare un'attività di gestione di processo che può eseguire le operazioni seguenti:

* Dividere un processo in più attività.
* Inviare le attività da eseguire in Batch.

>[AZURE.NOTE] Per ulteriori informazioni sulle attività di gestione di processo, vedere [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Creare un gestore di processo utilizzando il modello

Per aggiungere un responsabile di processo per la soluzione creata in precedenza, procedere come segue:

1. Aprire la soluzione esistente in Visual Studio 2015.

2. In Esplora soluzioni fare doppio clic la soluzione, fare clic su **Aggiungi** > **Nuovo progetto**.

3. In **Visual c#**, fare clic su **Cloud**e quindi fare clic su **Gestione di processi Batch Azure con barra di divisione di processo**.

4. Digitare un nome che descrive l'applicazione e identifica il progetto come il responsabile di processo (ad esempio "LitwareJobManager").

5. Per creare il progetto, fare clic su **OK**.

6. Infine, compilare il progetto per forzare Visual Studio per caricare tutti i pacchetti NuGet cui viene fatto riferimenti e verificare che il progetto è valido prima di iniziare la modifica.

### <a name="job-manager-template-files-and-their-purpose"></a>File di modello di processo gestione e la relativa funzione

Quando si crea un progetto utilizzando il modello di processo Manager, viene generato tre gruppi di file di codice:

* Il file di programma principale (Program.cs). Questa pagina contiene eccezioni principale e il punto di ingresso programma. Non devono in genere necessario modificare questa impostazione.

* Directory di Framework. Questa pagina contiene i file responsabili per il lavoro 'standard' effettuato tramite il programma di gestione del processo: decompressione parametri, aggiunta di attività al processo Batch e così via. Non devono in genere necessario modificare questi file.

* File di divisione di processo (JobSplitter.cs). Si tratta in cui si inseriranno la logica specifiche di un'applicazione per la divisione di un processo in attività.

Naturalmente è possibile aggiungere file aggiuntivi necessari per supportare il codice di divisione di processo, in base alla complessità del processo di divisione logica.

Il modello genera anche file di progetto .NET standard, ad esempio un file con estensione csproj, App, packages.config e così via.

Il resto di questa sezione vengono descritti i diversi file e la struttura di codice e che cosa significa ogni classe.

![Esplora Visual Studio con il gestore processi soluzione di modello][solution_explorer01]

**File Framework**

* `Configuration.cs`: Incapsula il caricamento di dati di configurazione del processo, ad esempio dettagli account Batch, le credenziali dell'account di archiviazione collegato, processo e informazioni sulle attività e parametri del processo. Anche possibile accedere alle variabili di ambiente definiti Batch (vedere impostazioni dell'ambiente per le attività, nella documentazione Batch) mediante la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Estrae l'implementazione della classe di configurazione, in modo da poter eseguire unit test la barra di divisione processo utilizzando un oggetto di configurazione fittizi o.

* `JobManager.cs`: Coordina i componenti della finestra del programma di gestione del processo. È responsabile per l'inizializzazione della barra di divisione di processo, le attività restituite dalla barra di divisione processo al mittente di attività di invio e richiamare la barra di divisione di processo.

* `JobManagerException.cs`: Rappresenta un messaggio di errore che richiede la gestione di processo terminare. JobManagerException viene utilizzato per mandare a capo 'previsti' errori nel punto in cui possono essere fornite informazioni specifiche di diagnostica come parte di chiusura.

* `TaskSubmitter.cs`: Questa classe è responsabile per l'aggiunta di attività restituita dalla barra di divisione processo al processo Batch. Le aggregazioni di classe JobManager sequenza delle attività in batch per l'addizione efficiente ma tempestivo al processo chiama TaskSubmitter.SubmitTasks su un thread in background per ogni batch.

**Barra di divisione di processo**

`JobSplitter.cs`: Questa classe contiene la logica specifiche di un'applicazione per la suddivisione del processo in attività. Il framework richiama il metodo JobSplitter.Split per ottenere una sequenza di attività, che viene aggiunto al processo come il metodo restituisce loro. Questa è la classe in cui si inserirà la logica del processo di. Implementare il metodo di divisione per restituire una sequenza di istanze di CloudTask che rappresentano le attività in cui si desidera suddividere il processo.

**File di progetto della riga di comando .NET standard**

* `App.config`: File di configurazione dell'applicazione .NET standard.

* `Packages.config`: File di dipendenze pacchetto NuGet standard.

* `Program.cs`: Contiene eccezioni principale e il punto di ingresso programma.

### <a name="implementing-the-job-splitter"></a>Implementazione della barra di divisione di processo

Quando si apre il progetto di modello di processo Manager, il progetto avrà il file JobSplitter.cs aprire per impostazione predefinita. È possibile implementare la logica di divisione per le attività del carico di lavoro tramite la presentazione di metodo Split () seguente:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] La sezione annotazioni nel `Split()` metodo è l'unica sezione del codice del modello di processo Manager che è destinato è possibile modificare mediante l'aggiunta di logica di dividere i processi in diverse attività. Se si desidera modificare una sezione diversa del modello, verificare che sono a conoscenza con come funziona il Batch e provare alcuni [esempi di codice Batch][github_samples].

L'implementazione Split () ha accesso a:

* Parametri del processo tramite il `_parameters` campo.
* L'oggetto CloudJob che rappresenta il processo tramite il `_job` campo.
* L'oggetto CloudTask che rappresenta l'attività di gestione del processo, tramite la `_jobManagerTask` campo.

Il `Split()` implementazione non è necessario aggiungere direttamente al processo attività. Se, tuttavia, il codice deve restituire una sequenza di oggetti CloudTask e verranno aggiunte al processo automaticamente dalle classi framework che richiamano la barra di divisione di processo. Viene in genere a iteratore utilizzare # (`yield return`) funzionalità per implementare separatori processo come in questo modo le attività da avviare esecuzione presto anziché in attesa per tutte le attività che si desidera calcolare.

**Errore di divisione di processo**

Se la barra di divisione processo si verifica un errore, è necessario:

* Terminare la sequenza in c# `yield break` istruzione, in cui maiuscole/minuscole Gestore processo verrà trattato come ha esito positivo. o

* Un'eccezione, nel qual caso che il responsabile di processo verrà trattato come non è riuscita e può essere ripetuta a seconda di come il client ha configurato).

In entrambi i casi, le attività già restituiti dalla barra di divisione processo e aggiunti al processo Batch sarà idonee per l'esecuzione. Se non si vuole a tale scopo, quindi è possibile:

* Terminare il processo prima di restituire con la barra di divisione di processo

* Formulare la raccolta intera attività prima della restituzione (ovvero viene restituito un `ICollection<CloudTask>` o `IList<CloudTask>` anziché implementare la divisione di processo mediante un iteratore c#)

* Utilizzare le relazioni tra attività per rendere tutte le attività dipendono dal completamento di gestione di processo

**Processo manager tentativi**

Se il responsabile di processo non riesce, potrebbe ripetuta dal servizio Batch a seconda delle impostazioni di tentativi di client. In generale, questa operazione è sicura, perché quando il framework aggiunge le attività del processo, ignora le attività già esistenti. Tuttavia, se il calcolo delle attività è costosa, non si desidera sostenere il costo di ricalcolo delle attività sono già stati aggiunti al processo. viceversa, se eseguire nuovamente non riesce a generare la stessa attività ID quindi il comportamento 'ignorare i duplicati' verrà non attivato. In questi casi è necessario progettare il separatore di processo per rilevare il lavoro già svolto e non ripeterla, ad esempio eseguendo una CloudJob.ListTasks prima di iniziare a rendimento attività.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Codici di uscita ed eccezioni nel modello di processo Manager

Codici di uscita ed eccezioni consentono di determinare il risultato dell'esecuzione di un programma e consentono di individuare eventuali problemi con l'esecuzione del programma. Il modello di processo Manager implementazione dei codici di uscita e le eccezioni descritte in questa sezione.

Un'attività di gestione di processo implementato con il modello di processo Manager può restituire tre codici di uscita possibili:

| Codice | Descrizione |
|------|-------------|
| 0    | Gestione del processo è stato completato correttamente. Il codice di divisione processo eseguita fino al completamento e tutte le attività sono stati aggiunti al processo. |
| 1    | L'attività di gestione del processo non riuscita con un'eccezione '' parte del programma. L'eccezione è stata convertita in un JobManagerException con informazioni di diagnostica e, se possibile, suggerimenti per la risoluzione dell'errore. |
| 2    | L'attività di gestione del processo non riuscita con un'eccezione 'imprevista'. L'eccezione è stata registrata nell'output standard, ma il responsabile del processo: Impossibile aggiungere le informazioni di diagnostica o rimedi. |

In questo caso processo Gestione attività alcune attività possono comunque essere stati aggiunti al servizio prima dell'errore. Queste operazioni vengono eseguite come previsto. Per discussione di questo percorso di codice, vedere "Errore di divisione del processo" sopra.

Tutte le informazioni restituite da eccezioni scritto nei file stdout.txt e stderr.txt. Per ulteriori informazioni, vedere [Gestione degli errori](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerazioni sui client

In questa sezione descrive alcuni requisiti di implementazione client quando si richiama un responsabile di processo basato su questo modello. Per informazioni sul passaggio di parametri e impostazioni dell'ambiente, vedere [come passare parametri e variabili di ambiente dal codice client](#pass-environment-settings) .

**Credenziali obbligatorie**

Per aggiungere attività al processo Batch di Azure, l'attività di gestione del processo richiede l'URL di account Azure Batch e chiave. È necessario passare questi elementi in variabili denominate YOUR_BATCH_URL e YOUR_BATCH_KEY. È possibile impostare questi il gestore di processi impostazioni dell'ambiente attività. Ad esempio in un client c#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenziali di spazio di archiviazione**

In genere, il client non è necessario fornire le credenziali dell'account di archiviazione collegato all'attività di gestione del processo, perché non è necessario accedere in modo esplicito all'account di archiviazione collegato (a) più responsabili di processo e (b) l'account di archiviazione collegato viene spesso fornito tutte le attività come impostazione ambiente comuni per il processo. Se non si desidera fornire l'account di archiviazione collegato tramite le impostazioni dell'ambiente comuni e Gestore processo richiede l'accesso allo spazio di archiviazione collegata, è necessario fornire le credenziali di archiviazione collegato come indicato di seguito:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Impostazioni relative all'attività Gestione processo**

Il client necessario impostare il contrassegno di *killJobOnCompletion* manager processo su **false**.

È in genere sicura per il client impostare *runExclusive* su **false**.

Il client deve utilizzare la raccolta *resourceFiles* o *applicationPackageReferences* per eseguire il processo di gestione eseguibile (e la DLL richieste) distribuita per il nodo di calcolo.

Per impostazione predefinita, il responsabile di processo verrà ripetuto non in caso di errore. A seconda di logica di gestione del processo, il client può essere necessario abilitare tentativi tramite *vincoli*/*maxTaskRetryCount*.

**Impostazioni del processo**

Se la barra di divisione processo crea attività con relazioni, il client necessario impostare usesTaskDependencies del processo su true.

Nel modello di divisione processo è insolito per i client a per aggiungere attività alla processi oltre alle operazioni consente di creare la barra di divisione di processo. Il client, pertanto, in genere imposti del processo *onAllTasksComplete* su **terminatejob**.

## <a name="task-processor-template"></a>Modello di processore attività

Un modello di processore Task consente di implementare un processore di attività che possa eseguire le operazioni seguenti:

* Configurare le informazioni necessarie per ogni attività Batch per l'esecuzione.
* Eseguire tutte le operazioni necessarie per ogni attività Batch.
* Salvare l'output di un'attività in un archivio permanente.

Anche se non è necessario che un processore task eseguire attività in Batch, il chiave vantaggio derivante dall'uso un processore task è che fornisce un spaziale per implementare tutte le azioni di esecuzione delle attività in un'unica posizione. Ad esempio, se è necessario eseguire diverse applicazioni nel contesto di ogni attività o se è necessario copiare dati in un archivio permanente dopo il completamento di ogni attività.

Le azioni eseguite da processore attività possono essere come semplice o complessa e il numero o solo pochi, in base alle esigenze per il carico di lavoro. Inoltre, l'implementazione di tutte le azioni di attività in un sistema di elaborazione delle attività, è possibile facilmente aggiornare o aggiungere azioni in base alle modifiche applicazioni o ai requisiti di carico di lavoro. Tuttavia, in alcuni casi un processore task potrebbe non essere la soluzione ottimale per l'implementazione come è possibile aggiungere complessità non necessarie, ad esempio durante l'esecuzione di processi che possono essere avviati rapidamente dalla riga di comando semplice.

### <a name="create-a-task-processor-using-the-template"></a>Creare un processore Task utilizzando il modello

Per aggiungere un processore task alla soluzione creata in precedenza, procedere come segue:

1. Aprire la soluzione esistente in Visual Studio 2015.

2. In Esplora soluzioni fare doppio clic la soluzione, fare clic su **Aggiungi**e quindi fare clic su **Nuovo progetto**.

3. In **Visual c#**, fare clic su **Cloud**e quindi fare clic su **Azure Batch attività processore**.

4. Digitare un nome che descrive l'applicazione e identifica il progetto come processore di attività (ad esempio "LitwareTaskProcessor").

5. Per creare il progetto, fare clic su **OK**.

6. Infine, compilare il progetto per forzare Visual Studio per caricare tutti i pacchetti NuGet cui viene fatto riferimenti e verificare che il progetto è valido prima di iniziare la modifica.

### <a name="task-processor-template-files-and-their-purpose"></a>File di modello processore attività e lo scopo

Quando si crea un progetto utilizzando il modello di processore attività, viene generato tre gruppi di file di codice:

* Il file di programma principale (Program.cs). Questa pagina contiene eccezioni principale e il punto di ingresso programma. Non devono in genere necessario modificare questa impostazione.

* Directory di Framework. Questa pagina contiene i file responsabili per il lavoro 'standard' effettuato tramite il programma di gestione del processo: decompressione parametri, aggiunta di attività al processo Batch e così via. Non devono in genere necessario modificare questi file.

* Il file processore attività (TaskProcessor.cs). Si tratta in cui si inseriranno la logica specifiche di un'applicazione per l'esecuzione di un'attività (in genere in base alla chiamate a un eseguibile esistente). Codice di elaborazione preliminare e successiva, ad esempio il download dei dati aggiuntivi o il caricamento di file dei risultati, inserire anche qui.

Naturalmente è possibile aggiungere file aggiuntivi necessari per supportare il codice di processore delle attività, a seconda della complessità del processo di divisione logica.

Il modello genera anche file di progetto .NET standard, ad esempio un file con estensione csproj, App, packages.config e così via.

Il resto di questa sezione vengono descritti i diversi file e la struttura di codice e che cosa significa ogni classe.

![Esplora Visual Studio con la soluzione di modello processore Task][solution_explorer02]

**File Framework**

* `Configuration.cs`: Incapsula il caricamento di dati di configurazione del processo, ad esempio dettagli account Batch, le credenziali dell'account di archiviazione collegato, processo e informazioni sulle attività e parametri del processo. Anche possibile accedere alle variabili di ambiente definiti Batch (vedere impostazioni dell'ambiente per le attività, nella documentazione Batch) mediante la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Estrae l'implementazione della classe di configurazione, in modo da poter eseguire unit test la barra di divisione processo utilizzando un oggetto di configurazione fittizi o.

* `TaskProcessorException.cs`: Rappresenta un messaggio di errore che richiede la gestione di processo terminare. TaskProcessorException viene utilizzato per mandare a capo 'previsti' errori nel punto in cui possono essere fornite informazioni specifiche di diagnostica come parte di chiusura.

**Processore task**

* `TaskProcessor.cs`: Esegue l'attività. Il framework richiama il metodo TaskProcessor.Run. Questa è la classe in cui si inserirà la logica specifiche di un'applicazione dell'attività. Implementare il metodo di esecuzione per:
  * Analizzare e convalidare i parametri di attività
  * Scrivere la riga di comando per qualsiasi programma esterno che si desidera richiamare
  * Registrare le informazioni di diagnostica che potrebbe essere necessario per il debug
  * Avviare un processo utilizzando la riga di comando
  * Attendere che il processo uscire
  * Acquisire il codice di uscita del processo per determinare se positivo o negativo
  * Salvare i file di output si desidera mantenere in un archivio permanente

**File di progetto della riga di comando .NET standard**

* `App.config`: File di configurazione dell'applicazione .NET standard.
* `Packages.config`: File di dipendenze pacchetto NuGet standard.
* `Program.cs`: Contiene eccezioni principale e il punto di ingresso programma.

## <a name="implementing-the-task-processor"></a>Implementazione processore attività

Quando si apre il progetto di modello processore Task, il progetto avrà il file TaskProcessor.cs aprire per impostazione predefinita. È possibile implementare la logica di esecuzione per le attività del carico di lavoro utilizzando il metodo Run () illustrato di seguito:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] La sezione annotazioni in metodo Run () è l'unica sezione del codice del modello processore attività che è destinato è possibile modificare mediante l'aggiunta di logica Esegui per le attività del carico di lavoro. Se si desidera modificare una sezione diversa del modello, vedere prima di tutto acquisire familiarità con come funziona il Batch esaminando la documentazione Batch e provare alcuni degli esempi di codice Batch.

Il metodo Run () è responsabile per l'avvio della riga di comando, partendo uno o più processi, in attesa per tutti i processi al completamento, salvare i risultati e infine restituendo con un codice di uscita. Il metodo Run () è nel punto in cui è implementare la logica di elaborazione delle proprie attività. Il framework degli processore attività richiama il metodo Run (). non è necessario chiamare manualmente.

L'implementazione di run () ha accesso a:

* I parametri di attività, tramite la `_parameters` campo.
* ID mansione e delle attività, tramite la `_jobId` e `_taskId` campi.
* Configurazione dell'attività, tramite la `_configuration` campo.

**Errore di attività**

In caso di errore, è possibile uscire il metodo Run () generata un'eccezione, ma in questo modo il gestore di eccezioni di livello superiore nel controllo del codice di uscita attività. Se è necessario controllare il codice di uscita, in modo da poter distinguere diversi tipi di errore, ad esempio per diagnostica o perché alcune guasti devono terminare il processo e gli altri utenti non dovrebbe essere, è necessario uscire il metodo Run () restituendo un codice di uscita diverso da zero. Questo nome diventa il codice di uscita attività.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Codici di uscita ed eccezioni nel modello di processore Task

Codici di uscita ed eccezioni consentono di determinare il risultato dell'esecuzione di un programma e consentono di individuare eventuali problemi con l'esecuzione del programma. Il modello di processore Task implementazione dei codici di uscita e le eccezioni descritte in questa sezione.

Attività processore che un'attività è implementata con il modello di processore Task può restituire tre codici di uscita possibili:

| Codice | Descrizione |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Processore attività eseguita fino al completamento. Si noti che non implica che il programma è stata completata-solo che il processore task richiamato correttamente ed eseguire elaborazioni senza eccezioni. Il significato del codice di uscita dipende il programma richiamato: in genere il codice di uscita 0 indica che il programma ha avuto esito positivo e qualsiasi altro codice di uscita indica che il programma non è riuscito. |
| 1    | Processore attività non riuscita con un'eccezione '' parte del programma. L'eccezione è stata convertita in un `TaskProcessorException` con informazioni di diagnostica e, se possibile, suggerimenti per la risoluzione dell'errore. |
| 2    | Processore attività non riuscita con un'eccezione 'imprevista'. L'eccezione è stata registrata nell'output standard, ma il processore task: Impossibile aggiungere le informazioni di diagnostica o rimedi. |

>[AZURE.NOTE] Se il programma che si richiama utilizza codici di uscita 1 e 2 per indicare le modalità di errore specifico, l'utilizzo di codici di uscita 1 e 2 per gli errori di attività processore è ambiguo. È possibile modificare i codici di errore processore attività per codici di uscita distintivo modificando eccezioni nel file Program.cs.

Tutte le informazioni restituite da eccezioni scritto nei file stdout.txt e stderr.txt. Per ulteriori informazioni, vedere Gestione degli errori, nella documentazione di Batch.

### <a name="client-considerations"></a>Considerazioni sui client

**Credenziali di spazio di archiviazione**

Se il processore task utilizza archiviazione blob Azure in modo permanente output, ad esempio mediante la libreria di supporto convenzioni di file, quindi è necessario l'accesso a *uno dei due* cloud nell'archivio account credenziali *o* un URL di contenitore blob che include una firma di accesso condiviso (SA). Il modello include il supporto per fornire le credenziali tramite variabili comuni. Il client è possibile passare le credenziali di spazio di archiviazione come indicato di seguito:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Account di archiviazione è disponibile in classe TaskProcessor tramite il `_configuration.StorageAccount` proprietà.

Se si preferisce utilizzare un URL contenitore con SA, è anche possibile passare questo tramite un'impostazione di ambiente comuni di processo, ma il modello di processore attività attualmente non include il supporto incorporato per questa.

**Installazione di archiviazione**

È consigliabile che l'attività di gestione client o processo creare eventuali contenitori necessari affinché le attività prima di aggiungere le attività per il processo. Questo è obbligatorio che se si utilizza un URL contenitore con SA, pertanto un URL non include autorizzazione per creare il contenitore. Anche se si passa le credenziali dell'account di archiviazione, è consigliabile mentre Salva tutte le attività che sia necessario chiamare CloudBlobContainer.CreateIfNotExistsAsync sul contenitore.

## <a name="pass-parameters-and-environment-variables"></a>Passare parametri e variabili

### <a name="pass-environment-settings"></a>Passare a impostazioni dell'ambiente

Un cliente può passare informazioni per l'attività di gestione di processo in forma di impostazioni dell'ambiente. Queste informazioni possono essere usate dal manager mansione quindi durante la generazione le attività di processore attività che verranno eseguito come parte del processo di calcolo. Esempi di informazioni che è possibile passare come impostazioni dell'ambiente sono:

* Chiavi per l'account e nome account archiviazione
* URL di account batch
* Chiave account batch

Il servizio Batch ha un meccanismo semplice per passare impostazioni dell'ambiente a un'attività di gestione del processo utilizzando il `EnvironmentSettings` proprietà in [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Ad esempio, per ottenere il `BatchClient` istanza di un account di Batch, è possibile passare come variabili di ambiente dal client di codice l'URL e condivise chiave credenziali per l'account di Batch. Analogamente, per accedere all'account di archiviazione che è collegato all'account Batch, è possibile passare il nome dell'account di archiviazione e la chiave account di archiviazione come variabili.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passare parametri al modello di processo Manager

In molti casi è utile passare al processo parametri per l'attività di gestione di processo, per controllare il processo di divisione di processo o per configurare le attività per il processo. Tale scopo, è possibile caricare un file di JSON denominato parameters.json come file di risorse per l'attività di gestione del processo. I parametri possono quindi diventano disponibili nel `JobSplitter._parameters` campo nel modello di processo Manager.

>[AZURE.NOTE] Il gestore di parametro predefiniti supporta solo i dizionari di stringa utilizzata per. Se si desidera passare valori JSON complessi come valori di parametro, sarà necessario passare questi come stringhe e li analizza nella barra di divisione processo o modificare il framework `Configuration.GetJobParameters` metodo.

### <a name="pass-parameters-to-the-task-processor-template"></a>Passare parametri al modello di processore Task

È anche possibile passare parametri a singole attività implementata utilizzando il modello di processore Task. Proprio come con il modello di gestione del processo, il modello di processore delle attività consente di cercare un file di risorse denominato

Parameters.JSON e se ha rilevato che carica come il dizionario di parametri. Esistono un paio di opzioni relative alla modalità passare i parametri per le attività di processore attività:

* Riutilizzare i parametri del processo JSON. Questo metodo funziona anche se gli unici parametri sono quelli a livello di processo (ad esempio, rendering altezza e larghezza). Per eseguire questa operazione, quando si crea un CloudTask nella barra di divisione processo, aggiungere un riferimento all'oggetto file di risorse parameters.json dalla ResourceFiles dell'attività di gestione processo (`JobSplitter._jobManagerTask.ResourceFiles`) alla raccolta ResourceFiles del CloudTask.

* Generare e caricare un documento parameters.json attività specifiche come parte divisione dell'esecuzione del processo e fanno riferimento a tale blob nella raccolta di file di risorse dell'attività. Questa operazione è necessaria se diverse attività presentano parametri diversi. Un esempio potrebbe essere uno scenario di rendering 3D nel punto in cui l'indice di frame viene passato all'attività come parametro.

>[AZURE.NOTE] Il gestore di parametro predefiniti supporta solo i dizionari di stringa utilizzata per. Se si desidera passare valori JSON complessi come valori di parametro, sarà necessario passare questi come stringhe e li analizza in processore task o modificare il framework `Configuration.GetTaskParameters` metodo.

## <a name="next-steps"></a>Passaggi successivi

### <a name="persist-job-and-task-output-to-azure-storage"></a>Mantenere output mansione e delle attività per lo spazio di archiviazione di Azure

Un altro strumento utile in sviluppo di soluzioni Batch è [Convenzioni File Batch Azure][nuget_package]. Utilizzare questa libreria di classi .NET (attualmente in versione di anteprima) nelle applicazioni .NET Batch per archiviare e recuperare l'output di un'attività da e verso lo spazio di archiviazione di Azure facilmente. [Processo Batch Azure viene conservata e attività di output](batch-task-output.md) contiene una descrizione completa della libreria di e il relativo uso.

### <a name="batch-forum"></a>Forum di batch

[Forum di Azure Batch] [ forum] su MSDN è ideale per discutere in Batch e porre domande relative al servizio. Testa sulla sopra per post "a" utili e rivolgere le domande mentre si verificano durante la creazione di soluzioni Batch.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
