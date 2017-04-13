<properties
    pageTitle="In output un Azure Redis cache, utilizzando le funzioni di Azure, da Azure flusso Analitica | Microsoft Azure"
    description="Informazioni su come usare una funzione di Azure connessi a una coda di Bus del servizio, per popolare una Cache Redis Azure dall'output di un processo di flusso Analitica."
    keywords="flusso di dati, redis cache, coda bus del servizio"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Come memorizzare i dati di Azure flusso Analitica in una Cache Redis Azure mediante le funzioni di Azure

Azure Analitica flusso consente di sviluppare e distribuire soluzioni conveniente per ottenere in tempo reale informazioni approfondite da dispositivi, sensori, infrastruttura e le applicazioni o qualsiasi flusso di dati rapidamente. In questo modo diversi casi di utilizzo, ad esempio gestione in tempo reale e il monitoraggio, comando e controllo, frode il rilevamento Auto connessa e molti altri. In molti tali scenari, si desidera memorizzare i dati di output da Azure flusso Analitica in un archivio di dati distribuiti, ad esempio una cache Azure Redis.

Si supponga che parte di una società di telecomunicazione. Si sta tentando di rilevare frodi Image in più chiamate provenienti dalla stessa identità, allo stesso tempo, ma in diverse aree geografiche posizioni. Sono il compito di archiviazione di tutti i potenziali fraudolente telefonate in una cache Azure Redis. In questo blog fornite indicazioni su come è possibile completare l'attività. 

## <a name="prerequisites"></a>Prerequisiti
Completare il [Rilevamento di frodi in tempo reale] [ fraud-detection] dimostrativo per ASA

## <a name="architecture-overview"></a>Cenni preliminari sull'architettura
![Schermata dell'architettura](./media/stream-analytics-functions-redis/architecture-overview.png)

Come mostrato nella figura precedente, flusso Analitica consente di ascoltare i dati di input per eseguire la query e inviati a un output. In output di base, funzioni di Azure possono quindi attivare un tipo di evento. 

In questo blog attenzione nella parte Azure funzioni di questa pipeline o in particolare all'attivazione di un evento che archivia dati fraudolenti nella cache.
Dopo aver completato il [Rilevamento di frodi in tempo reale] [ fraud-detection] esercitazione, si dispone di un input (un hub evento), una query e un output (archiviazione blob) già configurato e in esecuzione. In questo blog viene modificata l'output per utilizzare invece una coda di Bus di servizio. Successivamente, una funzione di Azure abbiamo connettersi a questa coda. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Creazione e la connessione di una servizio Bus coda di output
Per creare una coda di Bus del servizio, seguire i passaggi 1 e 2 della sezione .NET in [Guida introduttiva a servizio Bus code][servicebus-getstarted].
A questo punto si connettersi coda il processo di flusso Analitica creata in precedenti dimostrativo di rilevamento di frode.



1. Nel portale di Azure, passare a e il **output** del lavoro e selezionare **Aggiungi** nella parte superiore della pagina.

    ![Aggiunta di output](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Scegliere **Servizio Bus coda** come **elaborare** e seguire le istruzioni sullo schermo. Assicurarsi di scegliere lo spazio dei nomi di coda Bus del servizio è stato creato in [Guida introduttiva a servizio Bus code][servicebus-getstarted]. Al termine, fare clic sul pulsante "buca".
3. Specificare i valori seguenti:
    - **Formato serializzatore evento**: JSON
    - **Codifica**: UTF8
    - **Formato**: riga

4. Fare clic sul pulsante **Crea** per aggiungere questa origine e per verificare che Analitica flusso è possibile connettersi all'account di archiviazione.

5. Nella scheda della **Query** , sostituire la query corrente con le operazioni seguenti. Sostituire *[Nome del servizio BUS]* con il nome di output creato nel passaggio 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Creare una Cache Redis Azure
Creare una cache Redis Azure, consultare la sezione .NET in [modalità di utilizzo Azure Redis Cache] [ use-rediscache] fino alla sezione chiamata ***configurare i client di cache***.
Al termine, si dispone di una nuova Cache Redis. In **tutte le impostazioni**, selezionare **i tasti di scelta** e Nota verso il basso la ***stringa di connessione principale***.

![Schermata dell'architettura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Creare una funzione di Azure
[Creare la funzione di Azure prima] di eseguire[ functions-getstarted] esercitazione per iniziare a utilizzare le funzioni di Azure. Se si dispone già di una funzione di Azure che si desidera utilizzare, quindi passare a [scrivere nella Cache Redis](#Writing-to-Redis-Cache)

1. Nel portale, selezionare App servizi dal riquadro di spostamento sinistro, quindi fare clic sul nome di funzione Azure app per accedere al sito Web app della funzione.
    ![Schermata dell'elenco di funzioni servizi app](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Fare clic su **nuova funzione > ServiceBusQueueTrigger – c#**. Seguire queste istruzioni per i campi seguenti:
    - **Nome coda**: lo stesso nome il nome immesso al momento della creazione della coda in [Guida introduttiva a servizio Bus code] [ servicebus-getstarted] (non il nome del bus di servizio). Verificare che si usa la coda che sia connesso a output flusso Analitica.
    - **Connessione Bus di servizio**: selezionare **Aggiungi una stringa di connessione**. Per trovare la stringa di connessione, accedere al portale di classica, selezionare **Servizio Bus**, bus del servizio che è stato creato e **Informazioni di connessione** nella parte inferiore dello schermo. Assicurarsi che siano nella schermata principale in questa pagina. Copiare e incollare la stringa di connessione. È possibile immettere un nome di connessione.
    
        ![Schermata della connessione al servizio bus](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: scegliere **Gestisci**


3. Fare clic su **Crea**

## <a name="writing-to-redis-cache"></a>Scrivere Redis Cache
A questo punto è stata creata una funzione di Azure che legge da una coda di Bus di servizio. Tutti gli elementi ancora da svolgere è utilizzare la funzione per scrivere i dati in Cache Redis. 

1. Selezionare il nuovo **ServiceBusQueueTrigger**e scegliere **le impostazioni dell'app funzione** angolo superiore destro. Selezionare **passare a impostazioni servizio App > Impostazioni > Impostazioni applicazione**

2. Nella sezione stringhe di connessione, creare un nome nella sezione **nome** . Incollare la stringa di connessione principale che è trovato nel passaggio **Crea una Cache Redis** nella sezione **valore** . Selezionare **personalizzato** nel punto in cui compare l'istruzione **SQL Database**.

3. Fare clic su **Salva** nella parte superiore.

    ![Schermata della connessione al servizio bus](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Ora tornare alle impostazioni del servizio App e selezionare **Strumenti > App servizio Editor (Preview) > su > Vai**.

    ![Schermata della connessione al servizio bus](./media/stream-analytics-functions-redis/app-service-editor.png)

5. In un editor di propria scelta, creare un file JSON denominato **project.json** con le operazioni seguenti e salvarlo sul disco locale.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Caricare il file nella directory radice della funzione (non WWWROOT). Verrà visualizzato un file denominato **project.lock.json** automaticamente visualizzati, verificare che la Nuget pacchetti "StackExchange.Redis" e "Newtonsoft.Json" sono state importate.

7. Nel file **run.csx** sostituire il codice pre-generato con il codice seguente. Funzione lazyConnection, sostituire "Nome CONN" con il nome che è stato creato nel passaggio 2 di **archiviare i dati nella cache Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Avviare il processo di flusso Analitica

1. Avviare l'applicazione telcodatagen.exe. L'utilizzo consiste nel modo seguente:````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. Scegliere **Start** nella parte superiore della pagina e il processo di Analitica flusso nel portale.

    ![Schermata del processo di avvio](./media/stream-analytics-functions-redis/starting-job.png)

3. In **Avvia processo** e il che viene visualizzata, selezionare **ora** e quindi fare clic sul pulsante **Start** nella parte inferiore dello schermo. Modifiche dello stato processo a partire da e dopo alcune modifiche al periodo di esecuzione.
 
    ![Schermata della selezione ora di inizio processo](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Eseguire soluzione e controllare i risultati
Tornando alla pagina account **ServiceBusQueueTrigger** , verrà visualizzata istruzioni di registrazione. Questi registri mostrano qualcosa ottenuto dal servizio Bus coda, inserirlo il database e recuperati utilizzando l'ora come chiave!

Per verificare che i dati sono presenti nella cache Redis, passare alla pagina account cache Redis nel portale di nuovo (come illustrato nel passaggio precedente [Crea una Cache Redis Azure](#Create-an-Azure-Redis-Cache) ) e selezionare Console.

A questo punto è possibile scrivere comandi Redis per verificare che i dati siano in realtà nella cache.

![Schermata della Console Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Passaggi successivi
Siamo entusiasti i cambiamenti nuove funzioni di Azure e analitica flusso possibili insieme e ci auguriamo che questa operazione sblocca nuove possibilità dell'utente. Se si dispone di eventuali commenti e suggerimenti su quella desiderata successivo, è possibile utilizzare il [sito Uservoicehttp Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Se si è nuovo Microsoft Azure, la invitiamo a provare per iscriversi a un [account di valutazione di Azure gratuito](https://azure.microsoft.com/pricing/free-trial/). Se ha familiarità con flusso Analitica, quindi invita tutti i per [creare il primo processo flusso Analitica](stream-analytics-create-a-job.md).

Se occorre una Guida o domande, post nei forum [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) o [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

È anche possibile vedere le risorse seguenti:

- [Riferimenti per sviluppatori di funzioni Azure](../azure-functions/functions-reference.md)
- [Riferimenti per sviluppatori di Azure funzioni c#](../azure-functions/functions-reference-csharp.md)
- [Riferimenti per sviluppatori di Azure funzioni F #](../azure-functions/functions-reference-fsharp.md)
- [Azure NodeJS funzioni di riferimento per sviluppatori](../azure-functions/functions-reference.md)
- [Associazioni e azure trigger di funzioni](../azure-functions/functions-triggers-bindings.md)
- [Come eseguire il monitoraggio della Cache Redis Azure](../redis-cache/cache-how-to-monitor.md)

Per mantenersi aggiornati sulle tutte le ultime notizie e funzionalità, seguire [@AzureStreaming](https://twitter.com/AzureStreaming) su Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
