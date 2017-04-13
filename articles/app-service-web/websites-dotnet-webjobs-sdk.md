<properties 
    pageTitle="Che cos'è Azure WebJobs SDK" 
    description="Introduzione all'Azure WebJobs SDK. Spiega cosa SDK, scenari tipici è utile per ed esempi di codice." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>Che cos'è Azure WebJobs SDK

## <a id="overview"></a>Panoramica

In questo articolo spiega cosa SDK WebJobs, vengono esaminati alcuni scenari comuni è utile per e viene fornita una panoramica su come utilizzare il codice.

[WebJobs](websites-webjobs-resources.md) è una funzionalità di Azure App servizio che consente di eseguire un programma o script nello stesso contesto come web app, API app o app per dispositivi mobili. Lo scopo di [SDK WebJobs](websites-webjobs-resources.md) è per semplificare il codice che scritto per le attività comuni che un WebJob possono eseguire, ad esempio l'elaborazione di immagini, coda di elaborazione, RSS aggregazione, manutenzione del file e invio di messaggi di posta elettronica. SDK WebJobs sono caratteristiche incorporate per l'utilizzo di archiviazione di Azure e Bus di servizio per la pianificazione delle attività e la gestione degli errori e per molti altri scenari comuni. Inoltre, è progettato per essere esteso. [WebJobs SDK è Apri origine](https://github.com/Azure/azure-webjobs-sdk/), viene rilevato un [aprire repository di origine per le estensioni](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

SDK WebJobs include i componenti seguenti:

* **Pacchetti NuGet**. Pacchetti NuGet aggiunti a un progetto di applicazione Console di Visual Studio forniscono una struttura che il codice utilizza tramite i metodi con attributi WebJobs SDK.
  
* **Dashboard**. Parte di SDK WebJobs incluso nel servizio App Azure e fornisce RTF monitoraggio e diagnostica per i programmi che utilizzano i pacchetti NuGet. Non è necessario scrivere codice per utilizzare queste funzionalità di monitoraggio e diagnostica.

## <a id="scenarios"></a>Scenari

Ecco alcuni scenari tipici che è possibile gestire più facilmente con Azure WebJobs SDK:

* Immagine del lavoro CPU che richiede significative attività di elaborazione o un altro. Una funzionalità comune dei siti Web è la possibilità di caricare le immagini o video. Frequenza si desidera modificare il contenuto dopo che è stato caricato, ma non si vuole rendere l'utente è in corso di effettuare questa operazione.

* Elaborazione delle code. Un modo comune per un front-end web comunicare con un servizio di back-end consiste nell'utilizzare code. Quando il sito Web deve svolgere il lavoro, inserisce un messaggio in una coda. Un servizio di back-end estrae i messaggi dalla coda e vengono eseguite le operazioni. È possibile utilizzare code per l'elaborazione di immagini: ad esempio, dopo l'utente carica un numero di file, inserire i nomi dei file in un messaggio di coda di essere estratti da back-end per l'elaborazione. È inoltre possibile utilizzare code per migliorare la velocità di risposta del sito. Ad esempio, anziché direttamente a un database SQL di scrittura, scrivere a una coda di fornire all'utente che termine e consentire il database relazionali con latenza elevata quadratino back-end del servizio di lavoro. Per un esempio di coda di elaborazione con processo delle immagini, vedere l' [esercitazione WebJobs SDK per iniziare](websites-dotnet-webjobs-sdk-get-started.md).

* Aggregazione RSS. Se si dispone di un sito che contiene un elenco di feed RSS, può effettuare il pull in tutti gli articoli dal feed in un processo in background.

* Gestione di file, ad esempio l'aggregazione o eliminazione dei file di log.  Potrebbe essere file di log creati da più siti o per intervalli di tempo separato che si desidera combinare per eseguire analisi processi su di essi. Oppure è possibile pianificare un'attività da eseguire settimanale per eliminare i file di log precedente.

* Ingresso in tabelle Azure. Disporre i file archiviati e BLOB e si desidera analizzare loro e archiviare i dati nelle tabelle. La funzione di ingresso Impossibile scrivere grandi quantità di righe (milioni in alcuni casi) e SDK WebJobs modo è possibile implementare facilmente questa funzionalità. SDK vengono forniti anche monitoraggio in tempo reale di indicatori di stato di avanzamento, ad esempio il numero di righe scritte nella tabella.

* Altre attività lunga che si desidera eseguire in un thread in background, ad esempio [l'invio di messaggi di posta elettronica](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Le attività che si desidera eseguire una frequenza, ad esempio un'operazione di backup ogni notte.

In molte di queste situazioni può essere necessario ridimensionare un'app web per l'esecuzione in macchine virtuali più che siano in più WebJobs contemporaneamente. In alcuni casi potrebbe comportare gli stessi dati elaborati più volte, ma questa non è un problema quando si utilizza la coda incorporata, blob e trigger Bus di servizio di SDK WebJobs. SDK garantisce che le funzioni verranno elaborate una sola volta per ogni messaggio o blob.

SDK WebJobs facilmente anche gestire gli scenari di gestione degli errori comuni. È possibile impostare avvisi per inviare notifiche quando si verifica un errore di una funzione ed è possibile impostare timeout in modo che una funzione viene annullata automaticamente se non viene completata entro un limite di tempo specificato.

## <a id="code"></a>Esempi di codice

Il codice per la gestione delle attività più comuni che funzionano con lo spazio di archiviazione di Azure è semplice. Nell'applicazione Console `Main` metodo si crea un `JobHost` oggetto che le chiamate a metodi scrivere di coordinate. Framework WebJobs SDK sa quando i metodi di chiamata e in attributi WebJobs SDK che utilizzare che basano valori di parametro da utilizzare. SDK fornisce i *trigger* per specificare quali condizioni causano la funzione da chiamare e *gestori di associazione* che specifica la modalità ottenere informazioni e in uscita parametri.

Ad esempio, l'attributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) , una funzione da chiamare quando si riceve un messaggio in una coda e se il formato dei messaggi è JSON per una matrice di byte o un tipo personalizzato, il messaggio è stato deserializzato automaticamente. L'attributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) attiva un processo ogni volta che viene creato un nuovo blob in un account di archiviazione Azure.

Ecco un semplice programma che esegue il polling una coda e crea un blob per ogni messaggio coda ricevuto:

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

Il `JobHost` oggetto è un contenitore di un insieme di funzioni di sfondo. Il `JobHost` oggetto esegue il monitoraggio delle funzioni, le espressioni di controllo per gli eventi che li, attivano ed esegue le funzioni quando si verificano eventi trigger. Si chiama un `JobHost` metodo per indicare se si desidera che il processo di contenitore per l'esecuzione in thread corrente o un thread in background. Nell'esempio, il `RunAndBlock` metodo esegue il processo di continuamente sul thread corrente.

Poiché il `ProcessQueueMessage` metodo in questo esempio è un `QueueTrigger` dell'attributo, il trigger per la funzione è la ricezione di un nuovo messaggio. Il `JobHost` oggetto controlla per i nuovi messaggi coda nella coda specificata ("webjobsqueue" in questo esempio) e una volta trovato, chiama `ProcessQueueMessage`. 

Il `QueueTrigger` dell'attributo associa il `inputText` parametro per il valore del messaggio coda. E la `Blob` dell'attributo associa un `TextWriter` oggetto in un blob denominato "blobname" in un contenitore denominato "nomecontenitore".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

La funzione utilizza quindi questi parametri per scrivere il valore del messaggio coda il blob:

        writer.WriteLine(inputText);

Le caratteristiche di trigger e raccoglitore di SDK WebJobs semplificano notevolmente il codice che è necessario scrivere. Il codice di basso livello necessario per l'elaborazione di code, BLOB o file oppure per avviare attività programmate, viene eseguito da framework WebJobs SDK. Ad esempio, il framework crea code che non esistono ancora, viene visualizzata la coda, legge accoda i messaggi, eliminazioni accodare i messaggi quando elaborazione viene completata, crea contenitori blob che non esistono ancora, scrive BLOB e così via.

Nell'esempio seguente mostra un'ampia gamma di trigger in uno WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, e `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

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
    }
```

## <a id="schedule"></a>Pianificazione

Il `TimerTrigger` attributo consente alle funzioni di trigger per l'esecuzione in una pianificazione. È possibile pianificare un WebJob come un intero tramite le funzioni di singoli Azure o una pianificazione di una WebJob tramite SDK WebJobs `TimerTrigger`. Ecco un esempio di codice.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Per ulteriori esempi di codice, vedere [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) repository estensioni del sdk di webjobs azure su GitHub.com.

## <a name="extensibility"></a>Extensibility

Non si è limitata alla funzionalità incorporata - SDK WebJobs consente di scrivere gestori di associazione e trigger personalizzato.  Ad esempio, è possibile scrivere trigger per gli eventi della cache e pianificazioni periodiche. Un [aprire repository di origine](https://github.com/Azure/azure-webjobs-sdk-extensions) contiene un [Guida dettagliata su extensibility WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) e codice di esempio per iniziare a scrivere il proprio trigger e gestori di associazione.

## <a id="workerrole"></a>Tramite il SDK WebJobs all'esterno di WebJobs

Un programma che utilizza il SDK WebJobs è un'applicazione Console standard e può eseguire ovunque - non è necessario eseguire come un WebJob. È possibile testare il programma in locale nel computer di sviluppo e di produzione è possibile eseguirlo in un ruolo di servizio Cloud lavoro o un servizio Windows se si preferisce uno di questi ambienti. 

Tuttavia, il dashboard è disponibile solo come un'estensione per un'app web di Azure App servizio. Se si desidera eseguire all'esterno di un WebJob e continuare a utilizzare il Dashboard, è possibile configurare un'applicazione web per usare lo stesso account di archiviazione che si intende la stringa di connessione WebJobs SDK Dashboard e che WebJobs Dashboard dell'applicazione web verranno quindi visualizzati i dati sull'esecuzione di funzione da un'applicazione è in esecuzione in un'altra posizione. È possibile ottenere al dashboard utilizzando il.scm.azurewebsites.net/azurejobs/#/functions*{webappname}*https:// URL. Per ulteriori informazioni, vedere [Guida un dashboard per lo sviluppo locale con SDK WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), ma si noti che il post di blog Mostra un nome di stringa di connessione precedente. 

## <a id="nostorage"></a>Funzionalità del dashboard

Anche se non si usa trigger WebJobs SDK o gestori di associazione, SDK WebJobs offre diversi vantaggi:

* È possibile richiamare funzioni nel dashboard.
* È possibile riprodurre funzioni nel dashboard.
* È possibile visualizzare registri nel Dashboard collegati a WebJob specifico (registri applicazioni, scritti utilizzando console, Console.Error, individua e così via) o collegata la chiamata di funzione particolare che ha generato loro (registri scritti mediante un `TextWriter` oggetto SDK passa alla funzione come parametro). 

Per ulteriori informazioni, vedere [come manualmente richiamare una funzione](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [la scrittura dei log](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Passaggi successivi

Per ulteriori informazioni sul SDK WebJobs, vedere [Azure WebJobs consigliati risorse](http://go.microsoft.com/fwlink/?linkid=390226).

Per informazioni sui miglioramenti più recente di SDK WebJobs, vedere le [Note sulla versione](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
