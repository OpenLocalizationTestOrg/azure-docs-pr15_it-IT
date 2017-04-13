<properties
   pageTitle="Ottimizzazione del codice Azure in Visual Studio | Microsoft Azure"
   description="Informazioni sul codice come Azure strumenti di ottimizzazione in Visual Studio mettere il codice più prestazioni."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Ottimizzazione del codice Azure

Quando si programma applicazioni che utilizzano Microsoft Azure, esistono alcune procedure consigliate da seguire per evitare problemi di scalabilità app, comportamento e le prestazioni in un ambiente basato su cloud. Microsoft offre uno strumento di analisi del codice di Azure che riconosce e identifica molti di questi problemi comunemente riscontrati e consente di risolverli. È possibile scaricare lo strumento di Visual Studio tramite NuGet.

## <a name="azure-code-analysis-rules"></a>Regole di analisi codice Azure

Lo strumento di analisi del codice di Azure utilizza le regole seguenti segnali automaticamente il codice Azure quando rileva problemi noti impatto delle prestazioni. Rilevati problemi vengono visualizzati come un avvisi o errori. Correzioni di codice o i suggerimenti per risolvere l'errore o l'avviso vengono spesso forniti mediante un'icona di lampadina.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Evitare l'utilizzo di modalità stato sessione (in corso) predefinita

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Descrizione

Se si usa la modalità di stato della sessione (in process) predefinita per le applicazioni cloud, è possibile perdere lo stato della sessione.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Per impostazione predefinita, la modalità di stato sessione specificata nel file config è in corso. Inoltre, se nessuna voce specificata nel file di configurazione, la modalità di sessione predefinita nel processo. La modalità di processo memorizza lo stato della sessione nel server web. Quando si riavvia un'istanza o una nuova istanza viene utilizzata per il bilanciamento del carico o il supporto di failover, lo stato della sessione archiviato in memoria sul server web non viene salvato. Questa situazione impedisce che l'applicazione venga scalable nel cloud.

Lo stato sessione ASP.NET supporta diverse opzioni di archiviazione per i dati di stato sessione: InProc, StateServer, SQL Server, personalizzato e Off. Si consiglia di utilizzare la modalità di personalizzato per ospitare dati in un archivio di sessione esterno, ad esempio [lo stato della sessione di Azure provider per Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Soluzione

Una soluzione consigliata è archiviare lo stato della sessione in un servizio di cache gestito. Informazioni su come utilizzare [lo stato della sessione di Azure provider per Redis](http://go.microsoft.com/fwlink/?LinkId=401521) per memorizzare lo stato di una sessione. È anche possibile memorizzare lo stato della sessione in altre posizioni per assicurarsi che l'applicazione è scalable nel cloud. Per ulteriori informazioni sulle alternative soluzioni leggere [Modalità degli stati sessione](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Esegui metodo non deve essere asincrono

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Descrizione

Creare metodi asincroni (ad esempio [attesa](https://msdn.microsoft.com/library/hh156528.aspx)) all'esterno del metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e quindi chiamare i metodi asincrono da [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Dichiarazione di metodo [[Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) come asincrono causa il ruolo di lavoro immettere un ciclo di riavvio.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

La chiamata metodi asincrone all'interno del metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) causa runtime del servizio cloud per Cestino il ruolo di lavoro. Quando si avvia un ruolo di lavoro, l'esecuzione del programma tutti entrerà in vigore all'interno del metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Chiudere il metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) causa il ruolo di lavoro riavviare. Quando il runtime ruolo lavoro raggiunge il metodo asincrono, invia tutte le operazioni dopo il metodo asincrono e quindi restituisce. In questo modo il ruolo di lavoro uscire dal metodo [[[[Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e riavviare. Nell'iterazione successiva dell'esecuzione, il ruolo di lavoro raggiunge nuovamente il metodo asincrono e riavvia, causando il ruolo di lavoro al Cestino nuovamente anche.

### <a name="solution"></a>Soluzione

Posizionare tutte le operazioni asincrone all'esterno del metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Quindi, il metodo asincrono refactoring all'interno del metodo [[Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , ad esempio RunAsync () .wait. Lo strumento di analisi del codice di Azure consentono di risolvere il problema.

Frammento di codice riportato di seguito viene illustrato lo strumento fix di codice per risolvere il problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Usa l'autenticazione del servizio Bus condiviso accesso firma

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Descrizione

Utilizzare condiviso accesso firma (SA) per l'autenticazione. Servizio controllo di accesso (ACS) deprecato per l'autenticazione bus di servizio.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Per una maggiore protezione, Azure Active Directory sostituisce l'autenticazione di ACS con l'autenticazione SA. Per informazioni sul piano di transizione, vedere [Azure Active Directory è il futuro della ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) .

### <a name="solution"></a>Soluzione

Utilizzare l'autenticazione SA nelle applicazioni. Nell'esempio seguente viene illustrato come utilizzare un token sa esistente per accedere a un servizio bus dello spazio dei nomi o entità.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Vedere gli argomenti seguenti per altre informazioni.

- Per una panoramica, vedere [Autenticazione tramite firma accesso condiviso con Bus di servizio](https://msdn.microsoft.com/library/dn170477.aspx)

- [Come utilizzare autenticazione tramite firma accesso condiviso con Bus di servizio](https://msdn.microsoft.com/library/dn205161.aspx)

- Per un progetto di esempio, vedere [l'autenticazione tramite firma di Access condiviso (SA) con le sottoscrizioni Bus assistenza](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Valutare la possibilità di utilizzo del metodo OnMessage per evitare "ricevere ciclo"

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Descrizione

Per evitare inseriti in un "ciclo di ricevere", il metodo **OnMessage** è una soluzione migliore per la ricezione di messaggi di chiamare il metodo di **ricezione** . Tuttavia, se è necessario utilizzare il metodo di **ricezione** e si specificano un server non predefinito tempo di attesa, assicurarsi che il tempo di attesa server sia più di un minuto.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Quando si chiama **OnMessage**, viene avviato il client interno pumping che costantemente polling coda o l'abbonamento. Pompa questo messaggio contiene un ciclo infinito che invia una chiamata a ricevere messaggi. Se la chiamata timeout, genera una nuova chiamata. L'intervallo di timeout è il valore della proprietà [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) di [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)in uso.

Il vantaggio derivante dall'uso **OnMessage** confrontate con quelle di **ricezione** è che gli utenti non sono necessario manualmente sondaggio per i messaggi, gestire le eccezioni, elaborare più messaggi in parallelo e completare i messaggi.

Se si chiama **ricezione** senza usare il valore predefinito, verificare che il valore di *ServerWaitTime* più di un minuto. Impostazione *ServerWaitTime* da più di un minuto impedisce al server scaduta prima che venga completamente ricevuto il messaggio.

### <a name="solution"></a>Soluzione

Vedere esempi di codice seguenti per utilizzi consigliati. Per ulteriori informazioni, vedere [Metodo QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)e [Il metodo QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Per migliorare le prestazioni dell'infrastruttura di messaggistica Azure, vedere il modello di progettazione [Asincrono messaggistica nozioni di base](https://msdn.microsoft.com/library/dn589781.aspx).

Di seguito è illustrato un esempio dell'uso **OnMessage** per ricevere i messaggi.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Di seguito è illustrato un esempio dell'uso di **ricezione** con il tempo di attesa server predefinito.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Di seguito è illustrato un esempio dell'uso di **ricezione** con un tempo di attesa server non predefinito.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Si consiglia di utilizzare metodi Bus di servizio asincroni

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Descrizione

Utilizzare asincrono Bus di servizio per migliorare le prestazioni grazie alla messaggistica gestito.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Metodi asincrono attivato concorrenza programma applicazione poiché esecuzione ogni chiamata non bloccare il thread principale. Quando si usa Bus di servizio di messaggistica metodi, un'operazione (inviare, ricevere, eliminare, ecc) richiede tempo. Questa volta include l'elaborazione dell'operazione dal servizio di assistenza Bus oltre la latenza della richiesta e la risposta. Per aumentare il numero di operazioni per tempo, è necessario eseguire le operazioni contemporaneamente. Per ulteriori informazioni, vedere [Procedure consigliate per le prestazioni miglioramenti utilizzando servizio Bus negoziate messaggistica](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Soluzione

Per informazioni su come utilizzare il metodo asincrono consigliato, vedere [Classe QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) .

Per migliorare le prestazioni dell'infrastruttura di messaggistica Azure, vedere il modello di progettazione [Asincrono messaggistica nozioni di base](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Valutare la possibilità di partizioni code Bus di servizio e argomenti

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Descrizione

Partizione Bus di servizio code e argomenti per migliorare le prestazioni grazie alla messaggistica Bus di servizio.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Partizione code Bus di servizio e gli argomenti della aumenta la disponibilità di velocità e servizi prestazioni perché non è più limitata velocità effettiva totale coda partizionata o un argomento tramite le prestazioni di un gestore singolo messaggio o di messaggistica. Inoltre, temporaneamente fuori di un archivio di messaggistica non costituiscono una coda partizionata o argomento non è disponibile. Per ulteriori informazioni, vedere [Partizioni messaggistica entità](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Soluzione

Frammento di codice riportato di seguito viene illustrato come partizioni messaggistica entità.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Per ulteriori informazioni, vedere argomenti e [suddiviso servizio Bus code | Blog di Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) ed estrarre il campione di [Microsoft Azure servizio Bus partizioni coda](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>Non è impostata SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Descrizione

Evitare di utilizzare SharedAccessStartTimeset per l'ora corrente per iniziare immediatamente il criterio di accesso condiviso. È sufficiente impostare questa proprietà se si desidera iniziare il criterio di accesso condiviso in un secondo momento.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Sincronizzazione orologio, una differenza di orario lieve tra i Data Center. Ad esempio, si potrebbe pensare in modo logico impostando l'ora di inizio di spazio di archiviazione dei criteri sa come l'ora corrente utilizzando Now o un metodo simile verrà criteri SA abbiano effetto immediato. Tuttavia, le differenze di lieve ora tra i Data Center possono causare problemi quanto alcune volte Data Center potrebbero essere leggermente successive all'ora di inizio, mentre altri precedono. Di conseguenza, il criterio SA può scadere rapidamente (o persino immediatamente) se la durata di criteri è troppo breve.

Per altre indicazioni sull'utilizzo condiviso accesso firma su Azure dello spazio di archiviazione, vedere [Introduzione a tabella SA (condiviso accesso firma) SA coda e aggiornamento di Blob SA - blog MSDN Blog del Team di Microsoft Azure lo spazio di archiviazione - sito Home](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Soluzione

Rimuovere l'istruzione che imposta l'ora di inizio del criterio di accesso condiviso. Lo strumento di analisi del codice di Azure fornisce una soluzione per risolvere il problema. Per ulteriori informazioni sulla gestione della sicurezza, vedere il modello di progettazione [Valet chiave motivo](https://msdn.microsoft.com/library/dn568102.aspx).

Frammento di codice riportato di seguito viene illustrato lo strumento fix di codice per risolvere il problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Condividere criteri di accesso ora di scadenza deve essere più di cinque minuti

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Descrizione

Può essere molto simile a quella differenza di cinque minuti in clock tra i Data Center in posizioni diverse a causa di una condizione noto come "orologio asimmetria." Per evitare che le SA token di criteri di scadenza impostare precedente alla data di scadenza a più di cinque minuti.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Sincronizzare un segnale orologio Data Center in posizioni diverse in tutto il mondo. Poiché il tempo per segnale orologio recarsi presso diverse posizioni, possono essere presenti una variazione di tempo tra i Data Center in diverse aree geografiche anche se tutti gli elementi apparentemente è sincronizzato. Questa differenza di orario può influire l'accesso condiviso start tempi e scadenza intervallo di criteri. Per garantire criterio di accesso condiviso ha effetto immediato, pertanto non specificare l'ora di inizio. Inoltre, assicurarsi che la scadenza è più di 5 minuti per evitare che timeout anticipata.

Per ulteriori informazioni sull'uso di firma Access condiviso su Azure dello spazio di archiviazione, vedere [Introduzione a tabella SA (condiviso accesso firma) SA coda e aggiornamento di Blob SA - blog MSDN Blog del Team di Microsoft Azure lo spazio di archiviazione - sito Home](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Soluzione

Per ulteriori informazioni sulla gestione della sicurezza, vedere il modello di progettazione [Valet chiave motivo](https://msdn.microsoft.com/library/dn568102.aspx).

Di seguito è illustrato un esempio di senza specificare un'ora di inizio dei criteri di accesso condiviso.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Di seguito è illustrato un esempio di specificare un'ora di inizio dei criteri di accesso condiviso con una durata scadenza criteri maggiore di cinque minuti.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Per ulteriori informazioni, vedere [creare e utilizzare una firma di Access condiviso](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Utilizzare CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Descrizione

Utilizzo della classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) per i progetti, ad esempio sito Web di Azure e Azure servizi mobile non è possibile causare problemi di runtime. Come ottimale, tuttavia, è consigliabile utilizzare Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) allo scopo di gestione delle configurazioni per tutte le applicazioni di Azure Cloud unificato.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

CloudConfigurationManager legge il file di configurazione appropriato per l'ambiente di applicazione.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Soluzione

Il refactoring del codice per utilizzare la [Classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Dallo strumento di analisi codice Azure viene fornita una correzione di codice per risolvere il problema.

Frammento di codice riportato di seguito viene illustrato lo strumento fix di codice per risolvere il problema. Sostituisci

`var settings = ConfigurationManager.AppSettings["mySettings"];`

con

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Ecco un esempio di come salvare l'impostazione di configurazione in un file App o Web. config. Aggiungere le impostazioni per la sezione appSettings del file di configurazione. Di seguito è config per l'esempio precedente.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Evitare l'utilizzo di stringhe di connessione hardcoded

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Descrizione

Se si utilizzano stringhe di connessione hardcoded ed è necessario aggiornarli in un secondo momento, è necessario apportare modifiche al codice sorgente e ricompilare l'applicazione. Tuttavia, se si archiviano le stringhe di connessione in un file di configurazione, è possibile modificarle in un secondo momento semplicemente aggiornando il file di configurazione.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Livello di codice stringhe di connessione è un buon perché introduce problemi quando è necessario modificare rapidamente le stringhe di connessione. Inoltre, se il progetto deve essere archiviate in controllo del codice sorgente, le stringhe di connessione hardcoded introducono vulnerabilità poiché le stringhe possono essere visualizzate nel codice sorgente.

### <a name="solution"></a>Soluzione

Archiviare stringhe di connessione nel file di configurazione o ambienti Azure.

- Per le applicazioni autonome utilizzare App per archiviare le impostazioni della stringa di connessione.

- Per le applicazioni web ospitato IIS, utilizzare config per archiviare le stringhe di connessione.

- Per le applicazioni vNext ASP.NET, utilizzare configuration.json per archiviare le stringhe di connessione.

Per informazioni sull'utilizzo di file di configurazione, ad esempio config o App, vedere [Le istruzioni di configurazione Web ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Per informazioni su come Azure lavoro variabili di ambiente, vedere [siti Web di Azure: come le stringhe dell'applicazione e utilizzare le stringhe di connessione](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Per informazioni sull'archiviazione stringa di connessione nel controllo origine, vedere [non inserire informazioni riservate, ad esempio stringhe di connessione nel file che vengono salvate nell'archivio di codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Utilizzare file di configurazione di diagnostica

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Descrizione

Invece di configurazione delle impostazioni di diagnostica nel codice ad esempio tramite Microsoft.WindowsAzure.Diagnostics API di programmazione, è necessario configurare le impostazioni di diagnostica nel file diagnostics.wadcfg. (O diagnostics.wadcfgx se si usa Azure SDK 2.5). In questo modo è possibile modificare le impostazioni di diagnostica senza dover ricompilare il codice.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Prima di Azure SDK 2,5 (che utilizza diagnostica Windows Azure 1.3), Azure diagnostica (tampone) può essere configurato utilizzando diversi metodi: aggiunta a quello configurazione nel sistema di archiviazione, tramite il codice fondamentale, configurazione dichiarativa o la configurazione predefinita. Tuttavia, il modo migliore per configurare la diagnostica consiste nell'utilizzare un file di configurazione XML (diagnostics.wadcfg o diagnositcs.wadcfgx per SDK 2.5 e versioni successive) nel progetto di applicazione. In questo caso, il file diagnostics.wadcfg completamente definisce la configurazione e può essere aggiornato e ridistribuire piacimento. Combinazione l'utilizzo del file di configurazione diagnostics.wadcfg con i metodi di programmazione di configurazione delle impostazioni, utilizzando le classi [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)o [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)può generare confusione. Per ulteriori informazioni, vedere [inizializzazione o modifica Azure diagnostica configurazione](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

A partire da 1.3 tampone (incluso in Azure SDK 2,5), non è più possibile utilizzare codice per configurare la diagnostica. Di conseguenza, è possibile fornire solo la configurazione durante l'applicazione o l'aggiornamento l'estensione di diagnostica.

### <a name="solution"></a>Soluzione

Utilizzare la finestra di progettazione di configurazione di diagnostica per spostare le impostazioni di diagnostiche per il file di configurazione di diagnostica (diagnositcs.wadcfg o diagnositcs.wadcfgx per SDK 2.5 e versioni successive). È anche consigliabile installare [Azure SDK 2,5](http://go.microsoft.com/fwlink/?LinkId=513188) e utilizzare la funzionalità di diagnostica più recente.

1. Dal menu di scelta rapida per il ruolo che si desidera configurare, scegliere Proprietà e quindi scegliere la scheda configurazione.

1. Nella sezione **diagnostica** assicurarsi che sia selezionata la casella di controllo **Abilita diagnostica** .

1. Fare clic su **Configura** .

  ![L'opzione Abilita diagnostica di accesso](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Per ulteriori informazioni, vedere [Configurazione di diagnostica per servizi Cloud Windows Azure e macchine virtuali](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Evitare di dichiarare oggetti DbContext come statico

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Descrizione

Per salvare memoria, evitare di dichiarare DBContext oggetti statici.

Condividere idee e suggerimenti sulla [commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Gli oggetti DBContext contengono i risultati della query da ogni chiamata. In oggetti statici DBContext non vengono eliminati finché non viene scaricato il dominio dell'applicazione. Di conseguenza, un oggetto DBContext statico può utilizzare grandi quantità di memoria.

### <a name="solution"></a>Soluzione

Dichiarare DBContext come una variabile locale o di un campo di istanza statiche, utilizzare per un'attività e quindi potrà essere eliminata dopo l'utilizzo.

Nell'esempio seguente classe controller MVC viene illustrato come utilizzare l'oggetto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su optimzing e risoluzione dei problemi di Azure App, vedere [risoluzione dei problemi di un'app web nel servizio App Azure utilizzando Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
