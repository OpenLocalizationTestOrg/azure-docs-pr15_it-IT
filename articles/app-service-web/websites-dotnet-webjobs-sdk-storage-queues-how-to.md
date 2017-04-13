<properties 
    pageTitle="Come utilizzare lo spazio di archiviazione di Azure coda con SDK WebJobs" 
    description="Informazioni su come utilizzare lo spazio di archiviazione di Azure coda con SDK WebJobs. Creare ed eliminare code; inserire, leggere, ottenere ed eliminare i messaggi e altro ancora." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>Come utilizzare lo spazio di archiviazione di Azure coda con SDK WebJobs

## <a name="overview"></a>Panoramica

Questa guida vengono forniti esempi di codice c# che illustrano come usare la versione di Azure WebJobs SDK 1. x con il servizio di archiviazione coda Azure.

Presuppone che si sa [come creare un progetto WebJob in Visual Studio con stringhe di connessione che fanno riferimento al proprio account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) o a [più account di archiviazione](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

La maggior parte dei frammenti di codice mostra solo le funzioni, non il codice creato il `JobHost` oggetto come in questo esempio:

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
La Guida include i seguenti argomenti:

-   [Procedura di attivazione di una funzione quando si riceve un messaggio di coda](#trigger)
    - Messaggi stringa
    - Messaggi coda POCO
    - Funzioni asincrone
    - L'attributo QueueTrigger funziona con i tipi di
    - Algoritmo di polling
    - Più istanze
    - Esecuzione in parallelo
    - Ottenere coda o coda messaggio metadati
    - Spegnimento
-   [Come creare un messaggio di coda durante l'elaborazione di un messaggio di coda](#createqueue)
    - Messaggi stringa
    - Messaggi coda POCO
    - Creare più messaggi o nelle funzioni asincrone
    - L'attributo coda funziona con i tipi di
    - Utilizzare gli attributi WebJobs SDK nel corpo di una funzione
-   [Come leggere e scrivere BLOB durante l'elaborazione di un messaggio di coda](#blobs)
    - Messaggi stringa
    - Messaggi coda POCO
    - L'attributo Blob funziona con i tipi di
-   [Come gestire i messaggi danneggiati](#poison)
    - Gestione automatica dei messaggi
    - Gestione manuale dei messaggi
-   [Come impostare le opzioni di configurazione](#config)
    - Impostare le stringhe di connessione SDK nel codice
    - Configurare le impostazioni di QueueTrigger
    - Impostare i valori per WebJobs SDK parametri di costruzione nel codice
-   [Come attivare manualmente una funzione](#manual)
-   [Modalità di scrittura dei log](#logs) 
-   [Come gestire gli errori e configurare i timeout](#errors)
-   [Passaggi successivi](#nextsteps)

## <a id="trigger"></a>Procedura di attivazione di una funzione quando si riceve un messaggio di coda

Per scrivere una funzione che SDK WebJobs chiama quando si riceve un messaggio di coda, utilizzare la `QueueTrigger` attributo. Costruttore dell'attributo accetta un parametro stringa che specifica il nome della coda al sondaggio. È inoltre possibile [impostare il nome della coda in modo dinamico](#config).

### <a name="string-queue-messages"></a>Messaggi stringa

Nell'esempio seguente la coda contiene un messaggio di stringa, in modo `QueueTrigger` viene applicato a un parametro stringa denominato `logMessage` che contiene il contenuto del messaggio coda. La funzione [scrive un messaggio di log al dashboard](#logs).
 

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Oltre alle `string`, il parametro può essere una matrice di byte, un `CloudQueueMessage` oggetto o un POCO definite dall'utente.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(normale oggetto CLR precedente](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) Accoda i messaggi

Nell'esempio seguente, il messaggio coda contiene JSON per un `BlobInformation` oggetto che include una `BlobName` proprietà. SDK deserializza automaticamente l'oggetto.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

SDK utilizza il [pacchetto Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano i messaggi in un programma che non utilizza SDK WebJobs, è possibile scrivere codice simile a quello riportato di seguito per creare un messaggio di coda POCO che consente di analizzare la SDK. 

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Funzioni asincrone

Le seguenti asincrono funzione [scrive un file di log al dashboard](#logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Funzioni asincrone possono richiedere un [token di annullamento](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), come illustrato nell'esempio seguente, che consente di copiare un blob. (Per una spiegazione di `queueTrigger` segnaposto, vedere la sezione [BLOB](#blobs) .)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a id="qtattributetypes"></a>L'attributo QueueTrigger funziona con i tipi di

È possibile utilizzare `QueueTrigger` con i tipi seguenti:

* `string`
* Un tipo di POCO serializzato come JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a>Algoritmo di polling

SDK implementato un casuale esponenziale back-off algoritmo per ridurre l'effetto di inattività coda polling sui costi transazione lo spazio di archiviazione.  Quando un messaggio viene trovato, il SDK due secondi di attesa e quindi verifica la presenza di un altro messaggio; Quando viene trovato alcun messaggio è in attesa di quattro secondi prima di riprovare. Al termine successivi tentativi per ottenere un messaggio di coda, il tempo di attesa continua ingrandire le dimensioni fino a raggiungere il tempo di attesa massimo, che per impostazione predefinita a un minuto. [Il tempo di attesa massimo può essere configurato](#config).

### <a id="instances"></a>Più istanze

Se l'applicazione web viene eseguito su più istanze, un WebJob continuo viene eseguito in tutti i computer e ogni computer verranno attendere trigger e tenta di eseguire funzioni. Trigger di coda WebJobs SDK impedisce automaticamente una funzione di elaborazione di un messaggio di coda più volte; funzioni non sono stati scritti essere idempotenti. Tuttavia, se si vuole garantire una sola istanza di una funzione viene eseguita anche quando sono presenti più istanze di host web app, è possibile utilizzare il `Singleton` attributo. 

### <a id="parallel"></a>Esecuzione in parallelo

Se si dispone di più funzioni in ascolto code diverse, SDK eseguiranno una chiamata loro in parallelo ricezione dei messaggi contemporaneamente. 

Lo stesso avviene alla ricezione di più messaggi per una singola coda. Per impostazione predefinita, il SDK Ottiene un batch di 16 messaggi contemporaneamente ed esegue la funzione che li elabora in parallelo. [Sono possibile configurare le dimensioni del batch](#config). Quando il numero in fase di elaborazione Ottiene verso il basso a metà della dimensione del batch, SDK Ottiene un altro batch e avvia l'elaborazione dei messaggi. Di conseguenza il numero massimo di messaggi simultanei elaborati per ogni funzione è pari a una volta e mezza la dimensione del batch. Questo limite si applica separatamente per ogni funzione che contiene un `QueueTrigger` attributo. 

Se non si vuole esecuzione parallela per i messaggi ricevuti in una coda, è possibile impostare le dimensioni del batch su 1. Vedere anche **un maggiore controllo elaborazione della coda** in [Azure WebJobs SDK 1.1.0 RTM](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Ottenere coda o coda messaggio metadati

È possibile ottenere le seguenti proprietà del messaggio mediante l'aggiunta di parametri per la firma del metodo:

* `DateTimeOffset`expirationTime
* `DateTimeOffset`insertionTime
* `DateTimeOffset`nextVisibleTime
* `string`queueTrigger (contiene il testo del messaggio)
* `string`ID
* `string`popReceipt
* `int`dequeueCount

Se si desidera utilizzare direttamente l'API dello spazio di archiviazione di Azure, è possibile aggiungere anche un `CloudStorageAccount` parametro.

Nell'esempio seguente scrive tutti i metadati di un file di registro dell'applicazione. Nell'esempio logMessage e queueTrigger il contenuto del messaggio coda.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Ecco un file di log di esempio scritto dal codice di esempio:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

### <a id="graceful"></a>Spegnimento

Una funzione che viene eseguita in un WebJob continuo può accettare un `CancellationToken` parametro che consente al sistema operativo per la funzione di una notifica quando il WebJob sta per essere terminate. È possibile utilizzare questa notifica per assicurarsi che la funzione non termina in modo imprevisto in modo da lascia dati incoerente.

Nell'esempio seguente viene illustrato come verificare la presenza di imminente terminazione WebJob in una funzione.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Nota:** Dashboard di potrebbe non essere visualizzati correttamente lo stato e l'output di funzioni che è stato chiuso.
 
Per ulteriori informazioni, vedere [WebJobs spegnimento](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a>Come creare un messaggio di coda durante l'elaborazione di un messaggio di coda

Per scrivere una funzione che crea un nuovo messaggio, utilizzare la `Queue` attributo. Ad esempio `QueueTrigger`, si passa il nome di coda come stringa o è possibile [impostare il nome della coda in modo dinamico](#config).

### <a name="string-queue-messages"></a>Messaggi stringa

Non asincrono nell'esempio seguente viene creato un nuovo messaggio nella coda denominata "outputqueue" con lo stesso contenuto del messaggio di coda ricevuto nella coda denominata "inputqueue". (Per asincrono utilizzano funzioni `IAsyncCollector<T>` come illustrato più avanti in questa sezione.)


        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }
  
### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(normale oggetto CLR precedente](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) Accoda i messaggi

Per creare un messaggio di coda che contiene un POCO anziché una stringa, passare il tipo POCO come un parametro di output per il `Queue` costruttore attributo.
 
        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

SDK serializza automaticamente l'oggetto in JSON. Un messaggio di coda viene sempre creato, anche se l'oggetto è null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Creare più messaggi o nelle funzioni asincrone

Per creare più messaggi, verificare il tipo di parametro per coda di output `ICollector<T>` o `IAsyncCollector<T>`, come illustrato nell'esempio seguente.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Ogni messaggio coda viene creata immediatamente quando il `Add` metodo.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipi di utilizzabile con l'attributo coda

È possibile utilizzare il `Queue` attributo sui tipi di parametro seguenti:

* `out string`(Crea messaggio della coda se il valore del parametro non null quando la funzione termina)
* `out byte[]`(funzionamento `string`) 
* `out CloudQueueMessage`(funzionamento `string`) 
* `out POCO`(un tipo serializzabile crea un messaggio con un oggetto null, se il parametro è null quando la funzione termina)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue`(per la creazione manuale di messaggi con l'API di archiviazione di Azure direttamente)

### <a id="ibinder"></a>Utilizzare gli attributi WebJobs SDK nel corpo di una funzione

Se è necessario eseguire alcune operazioni nella funzione prima di utilizzare un attributo WebJobs SDK ad esempio `Queue`, `Blob`, o `Table`, è possibile utilizzare il `IBinder` interfaccia.

Nell'esempio seguente accetta un messaggio di input coda e crea un nuovo messaggio con lo stesso contenuto in una coda di output. Il nome della coda output viene impostato dal codice nel corpo della funzione.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

Il `IBinder` interfaccia può essere utilizzata anche con il `Table` e `Blob` attributi.

## <a id="blobs"></a>Come leggere e scrivere BLOB e tabelle durante l'elaborazione di un messaggio di coda

Il `Blob` e `Table` attributi consentono di leggere e scrivere BLOB e tabelle. Negli esempi di questa sezione si applicano a BLOB. Per esempi di codice che illustrano come impostare un trigger processi quando BLOB creati o aggiornati, vedere [come utilizzare archiviazione blob Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)e per esempi di codice che leggere e scrivere le tabelle, vedere [come utilizzare archiviazione tabelle Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### <a name="string-queue-messages-triggering-blob-operations"></a>Messaggi stringa attivazione blob operazioni

Per un messaggio di coda che contiene una stringa `queueTrigger` è un segnaposto in cui è possibile utilizzare il `Blob` dell'attributo `blobPath` parametro che contiene il contenuto del messaggio. 

Nell'esempio seguente viene utilizzato `Stream` oggetti per leggere e scrivere BLOB. Il messaggio di coda è il nome di un blob nel contenitore textblobs. Una copia del blob con "-nuovo" aggiungere il nome viene creato nello stesso contenitore. 

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Il `Blob` dell'attributo costruttore abbia un `blobPath` parametro che specifica il nome del contenitore e blob. Per ulteriori informazioni su questo segnaposto, vedere [come utilizzare archiviazione blob Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) 

Quando l'attributo aggiungono un `Stream` oggetto, un altro costruttore parametro specifica la `FileAccess` modalità di lettura, scrittura o lettura/scrittura. 

Nell'esempio seguente viene utilizzato un `CloudBlockBlob` oggetto per eliminare un blob. Il messaggio di coda è il nome del blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a id="pocoblobs"></a>POCO [(normale oggetto CLR precedente](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) Accoda i messaggi

Per POCO memorizzato come JSON nel messaggio della coda, è possibile utilizzare i segnaposto di assegnare un nome proprietà dell'oggetto all'interno di `Queue` dell'attributo `blobPath` parametro. È anche possibile utilizzare [i nomi delle proprietà dei metadati coda](#queuemetadata) come segnaposto. 

Nell'esempio seguente copia un blob in un nuovo blob con un'estensione diversa. Il messaggio di coda è un `BlobInformation` oggetto che include `BlobName` e `BlobNameWithoutExtension` proprietà. I nomi delle proprietà vengono usati come segnaposto nel percorso blob per la `Blob` attributi. 
 
        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

SDK utilizza il [pacchetto Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano i messaggi in un programma che non utilizza SDK WebJobs, è possibile scrivere codice simile a quello riportato di seguito per creare un messaggio di coda POCO che consente di analizzare la SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Se è necessario eseguire alcune operazioni nella funzione prima di associazione di un blob a un oggetto, è possibile utilizzare l'attributo nel corpo della funzione, [come illustrato in precedenza per l'attributo coda](#ibinder).

### <a id="blobattributetypes"></a>È possibile utilizzare l'attributo Blob con i tipi di
 
Il `Blob` attributo può essere utilizzato con i tipi seguenti:

* `Stream`(leggere o scrivere, specificato utilizzando il parametro di costruttore FileAccess)
* `TextReader`
* `TextWriter`
* `string`(sola lettura)
* `out string`(scrivere; crea un blob solo se il parametro stringa non null quando la funzione restituisce)
* POCO (lettura)
* fuori POCO (scrivere; sempre creato un blob, come oggetto null se POCO parametro è null quando la funzione restituisce)
* `CloudBlobStream`(scrittura)
* `ICloudBlob`(lettura o scrittura)
* `CloudBlockBlob`(lettura o scrittura) 
* `CloudPageBlob`(lettura o scrittura) 

## <a id="poison"></a>Come gestire i messaggi danneggiati

Messaggi il cui contenuto genera una funzione non viene eseguita sono denominati *messaggi danneggiati*. Quando la funzione non riesce, il messaggio di coda non viene eliminato e infine acquisito nuovamente causa il ciclo da ripetere. SDK possibile interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile eseguire manualmente.

### <a name="automatic-poison-message-handling"></a>Gestione automatica dei messaggi

SDK chiama una funzione fino a 5 ore per l'elaborazione di un messaggio di coda. Se il quinto tentativo non riesce, il messaggio viene spostato a una coda danneggiata. [Il numero massimo di tentativi è configurabile](#config). 

Coda danneggiata, denominata *{originalqueuename}*-danneggiato. È possibile scrivere è necessaria una funzione per elaborare i messaggi dalla coda danneggiata registrazione loro o inviando il file una notifica che attenzione manuale. 

Nell'esempio seguente la `CopyBlob` funzione avrà esito negativo quando un messaggio di coda contiene il nome di un blob che non esiste. In questo caso, il messaggio viene spostato dalla coda copyblobqueue coda veleno copyblobqueue. Il `ProcessPoisonMessage` quindi registrare il messaggio danneggiato.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }
        
        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

L'illustrazione seguente mostra l'output console queste funzioni durante l'elaborazione di un messaggio danneggiato.

![Console di output per la gestione dei messaggi](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>Gestione manuale dei messaggi

È possibile ottenere il numero di volte in cui un messaggio è stato selezionato per l'elaborazione mediante l'aggiunta di un `int` parametro denominato `dequeueCount` alla funzione. È quindi possibile controllare il numero di annullamento dell'accodamento nel codice della funzione ed eseguire il proprio messaggio danneggiato gestione quando il numero supera una determinata soglia, come illustrato nell'esempio seguente.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a id="config"></a>Come impostare le opzioni di configurazione

È possibile utilizzare il `JobHostConfiguration` tipo per impostare le opzioni di configurazione seguenti:

* Impostare le stringhe di connessione SDK nel codice.
* Configurare `QueueTrigger` impostazioni, ad esempio massimo dalla coda conteggio.
* Ottenere i nomi di coda dalla configurazione.

### <a id="setconnstr"></a>Impostare le stringhe di connessione SDK nel codice

Impostare le stringhe di connessione SDK nel codice consente di utilizzare i proprio nomi stringa di connessione nel file di configurazione o variabili di ambiente, come illustrato nell'esempio seguente.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;
        
            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;
        
            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
        
            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="configqueue"></a>Configurare le impostazioni di QueueTrigger

È possibile configurare le seguenti impostazioni che riguardano l'elaborazione del messaggio coda:

- Il numero massimo di messaggi che vengono selezionati contemporaneamente a da eseguire in parallelo (impostazione predefinita è 16).
- Il numero massimo di tentativi prima che venga inviato un messaggio di coda a una coda danneggiata (impostazione predefinita è 5).
- Il numero massimo di attesa prima di polling nuovamente quando una coda è vuota (valore predefinito è 1 minuto).

Nell'esempio seguente viene illustrato come configurare queste impostazioni:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="setnamesincode"></a>Impostare i valori per WebJobs SDK parametri di costruzione nel codice

Talvolta può essere utile specificare un nome di coda, un nome blob o un contenitore o una tabella denominarlo nel codice anziché hardcoded. È ad esempio specificare il nome della coda `QueueTrigger` in una variabile di ambiente o file di configurazione. 

È possibile farlo passando un `NameResolver` oggetto per la `JobHostConfiguration` tipo. Includere segnaposto speciali circondati da segni di percentuale (%)) in WebJobs SDK costruttore parametri e il `NameResolver` codice specifica i valori effettivi da utilizzare al posto di questi segnaposto.

Si supponga ad esempio, che si desidera utilizzare una coda denominata logqueuetest nell'ambiente di prova e un logqueueprod denominato nell'ambiente di produzione. Invece di un nome di coda hardcoded, si desidera specificare il nome di una voce il `appSettings` insieme contenenti il nome effettivo. Se il `appSettings` chiave logqueue, la funzione potrebbe essere simile al seguente.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Il `NameResolver` classe potrebbe quindi ottenere il nome della coda da `appSettings` come illustrato nell'esempio seguente:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Si passa il `NameResolver` classe per la `JobHost` corrente, come illustrato nell'esempio seguente.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }
 
**Nota:** Coda, tabella e i nomi di blob vengono risolti chiamata una funzione, ma i nomi dei contenitori blob vengono risolte solo all'avvio dell'applicazione. Non è possibile modificare il nome di contenitore blob durante il processo è in esecuzione. 

## <a id="manual"></a>Come attivare manualmente una funzione

Per attivare manualmente una funzione, utilizzare la `Call` o `CallAsync` metodo sul `JobHost` oggetto e il `NoAutomaticTrigger` dell'attributo nella funzione, come illustrato nell'esempio seguente. 

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }
        
            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger, 
                string value, 
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a id="logs"></a>Modalità di scrittura dei log

Dashboard di Mostra log in due posizioni: la pagina per la WebJob e della pagina per una particolare chiamata WebJob. 

![Registri nella pagina WebJob](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Registri nella pagina di chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

L'output metodi Console che si chiama in una funzione o nel `Main()` metodo viene visualizzata la pagina Dashboard per la WebJob e non la pagina per una chiamata metodo specifico. Output dall'oggetto TextWriter ottenuta da un parametro nella firma di metodo viene visualizzato nella pagina del Dashboard per una chiamata metodo.

Output della console non è collegato a una chiamata a un particolare metodo in quanto la Console è multithread, mentre molte funzioni di processo potrebbero essere in esecuzione nello stesso momento. Per tale motivo SDK fornisce ogni chiamata di funzione con il proprio oggetto writer log univoco.

Per scrivere [i registri di analisi dell'applicazione](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilizzare `Console.Out` (verranno creati i registri contrassegnati come INFO) e `Console.Error` (verranno creati registri contrassegnati come errore). In alternativa consiste nell'usare [traccia o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), che fornisce diversi livelli di dettaglio, avviso e critica oltre a informazioni e di errore. Registri di analisi dell'applicazione visualizzata nei file di registro web app, tabelle Azure o BLOB Azure a seconda di come configurare un'applicazione web Azure. Come di tutti gli output Console i registri 100 applicazioni più recenti vengono visualizzate anche nella pagina del Dashboard per WebJob, non la pagina per una chiamata di funzione. 

L'output della console viene visualizzato nel Dashboard solo se il programma è in esecuzione in un WebJob di Azure, non se il programma è in esecuzione in locale o in altri ambienti di rete.

Disattivare la registrazione di dashboard per gli scenari di alta velocità. Per impostazione predefinita, il SDK scrive registri allo spazio di archiviazione e questa attività può ridurre le prestazioni quando si elaborazione tanti messaggi. Per disattivare la registrazione, impostare la stringa di connessione dashboard su null come illustrato nell'esempio seguente.

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

Nell'esempio seguente mostra diversi modi per scrivere i registri:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

Il dashboard di SDK WebJobs l'output di `TextWriter` oggetto viene visualizzato quando si passa alla pagina per una particolare chiamata di funzione e fare clic su **Mostra/Nascondi Output**:

![Fare clic sul collegamento di chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Registri nella pagina di chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Dashboard di SDK WebJobs più recente 100 righe della Console output mostra la quando si passa alla pagina per WebJob (non per la chiamata di funzione) e fare clic su **Mostra/Nascondi Output**.
 
![Fare clic su Mostra/Nascondi Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In un WebJob continuo registri applicazioni disponibili nelle/dati/processi/continuo /*{webjobname}*/job_log.txt nel file system app web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In un Azure blob l'applicazione dei registri questo aspetto: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

In una tabella di Azure il `Console.Out` e `Console.Error` registri aspetto simile al seguente:

![Registro informazioni nella tabella](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Log degli errori di tabella](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Se si desidera collegare un logger personalizzato, vedere [questo esempio](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Come gestire gli errori e configurare i timeout

SDK WebJobs include anche un [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) attributo che è possibile utilizzare per una funzione deve essere annullato se non viene completata entro un periodo di tempo specificato. E se si desidera generare un avviso quando si verificano errori troppi all'interno di un determinato periodo di tempo, è possibile utilizzare il `ErrorTrigger` attributo. Ecco un [esempio ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

Anche in modo dinamico, è possibile disattivare e consentono di controllare le funzioni se possono essere generati, utilizzando un'opzione di configurazione che può essere un'impostazione di app o il nome della variabile di ambiente. Per esempi di codice, vedere la `Disable` attributo nel [repository esempi SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a>Passaggi successivi

Questa guida è forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di code Azure. Per ulteriori informazioni sull'utilizzo di Azure WebJobs e WebJobs SDK, vedere [Azure WebJobs consigliati risorse](http://go.microsoft.com/fwlink/?linkid=390226).
 
