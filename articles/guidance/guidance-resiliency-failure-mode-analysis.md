<properties
   pageTitle="Analisi degli errori modalità | Microsoft Azure"
   description="Linee guida per l'esecuzione di analisi modalità degli errori per le soluzioni cloud basate su Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Linee guida sulla resilienza Azure: analisi modalità degli errori

Analisi di modalità degli errori (FMA) sono un processo per la creazione di flessibilità in un sistema, identificando i punti di errore possibili nel sistema. Il FMA deve essere parte delle fasi architettura e la progettazione, in modo che è possibile creare il ripristino degli errori nel sistema dall'inizio.

Ecco la procedura generale per condurre un FMA: 

1. Identificare tutti i componenti del sistema. Includere le relazioni esterne, ad esempio come provider di identità, servizi di terze parti e così via.   

2. Per ogni componente, identificare i potenziali errori che possono verificarsi. Un singolo componente potrebbe essere più la modalità di errore. Ad esempio, si deve valutare la possibilità di errori di lettura ed errori di scrittura separatamente, poiché l'impatto e possibili soluzioni di attenuazione saranno diversi.

3. Valutare la modalità di ogni errore secondo il rischio globale. Considerare i fattori seguenti:  

    - Che cos'è la probabilità dell'errore. È abbastanza comune? Extrememly rari? Non è necessario esatti numeri. lo scopo è aiutare classificare la priorità.

    - Che cos'è l'impatto sull'applicazione in termini di disponibilità, perdite di dati, costo e le interruzioni di business? 

4. Per ogni modalità di errore, determinare la modalità di risposta e recuperare l'applicazione. Valutare la possibilità di compromessi complessità costi e dell'applicazione.   

Come punto di partenza per il processo FMA, in questo articolo contiene un catalogo di gestione degli errori potenziali e le soluzioni di attenuazione. Il catalogo è organizzato per tecnologia o servizio Azure, oltre a una categoria generale per la struttura di livello di applicazione. Il catalogo non è esaustivo, ma illustra molte delle principali Azure servizi. 


## <a name="app-service"></a>Servizio di App

### <a name="app-service-app-shuts-down"></a>Applicazione di servizio App viene chiuso.

**Rilevamento**. Le possibili cause:

- Arresto previsto
    
    - Un operatore arresta l'applicazione; ad esempio, tramite il portale di Azure.

    - Perché non è stato inattivo è stato scaricato l'app. (Solo se la `Always On` impostazione è disattivata.)

- Arresto imprevisto

    - Determina l'arresto anomalo dell'app.

    - Un'istanza di macchine Virtuali di servizio App non è disponibile.


Registrazione Application_End rileva l'arresto dominio app (arresto anomalo del processo contorni) ed è l'unico modo per rilevare l'arresto dominio applicazione.

**Ripristino**

- Se l'arresto previsto, utilizzare evento di chiusura dell'applicazione di chiudere normalmente. Ad esempio, in ASP.NET, utilizzare la `Application_End` metodo.

- Se l'applicazione è stato scaricato mentre inattivo, viene automaticamente riavviato per la richiesta successiva. Tuttavia, è necessario pianificare "inizio fredda" costi. 

- Per impedire che l'applicazione viene scaricato mentre inattivo, abilitare il `Always On` impostazione in web app. Vedere [configurare web apps in Azure App servizio][app-service-configure].

- Per impedire un operatore di chiusura dell'app, impostare un blocco di risorse con `ReadOnly` livello. Vedere [risorse di blocco con Gestione risorse di Azure][rm-locks].

- Se determina l'arresto anomalo dell'app o un servizio App riavvia automaticamente l'app macchine Virtuali di servizio App non è più disponibile. 


**Diagnostica**. Registri applicazioni e dei registri del server web. Vedere [abilitare la registrazione diagnostica per App web di servizio App Azure][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Un utente particolare ripetutamente rende richieste non valide o overload del sistema. 

**Rilevamento**. Autenticazione degli utenti e includere ID utente nei log dell'applicazione.

**Ripristino**

- Utilizzare [Gestione API Azure] [ api-management] alle richieste di limitazione da parte dell'utente. Vedere [limitazione con Azure API di gestione delle richieste avanzate][api-management-throttling]

- Impedire che l'utente.

**Diagnostica**. Registrare tutte le richieste di autenticazione.


### <a name="a-bad-update-was-deployed"></a>Distribuzione di un aggiornamento non valido.

**Rilevamento**. Monitorare l'applicazione tramite il portale Azure (vedere [prestazioni app web di Azure Monitor][app-insights-web-apps]) o implementare [motivo monitoraggio endpoint integrità][health-endpoint-monitoring-pattern].

**Ripristino**. Utilizzare più [bande orarie distribuzione] [ app-service-slots] e ripristinare la distribuzione ultimo funzionante. Per ulteriori informazioni, vedere [architettura di riferimento dell'applicazione web di base][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>Autenticazione OpenID connettersi (OIDC) non riesce.

**Rilevamento**. Gestione degli errori possibili includono:

1. Azure Active Directory non è disponibile o non è possibile accedere a causa di un problema di rete. Si verifica un errore di reindirizzamento per l'endpoint di autenticazione e middleware OIDC genera un'eccezione.
2. Tenant di Azure Active Directory non esiste. Il reindirizzamento per l'endpoint di autenticazione restituisce un codice di errore HTTP e middleware OIDC genera un'eccezione.
3. Non è possibile eseguire l'autenticazione utente. Nessuna strategia di rilevamento è necessaria; Azure Active Directory gestisce gli errori di accesso.

**Ripristino**

1. Rilevare le eccezioni non gestite da middleware.
2. Gestire `AuthenticationFailed` eventi.
3. Reindirizzare l'utente a una pagina di errore.
4. Tentativi di utente.


## <a name="azure-search"></a>Ricerca di Azure

### <a name="writing-data-to-azure-search-fails"></a>Scrittura di dati per la ricerca di Azure viene interrotta.

**Rilevamento**. Rilevare `Microsoft.Rest.Azure.CloudException` errori.

**Ripristino**

[Ricerca .NET SDK] [ search-sdk] ritenta automaticamente dopo errori temporanei. Tutte le eccezioni generate dal client SDK da considerare come errori non temporanei.

Il criterio di Riprova predefinito utilizza esponenziale back-off. Per utilizzare un criterio di diversi tentativi, chiamare `SetRetryPolicy` sul `SearchIndexClient` o `SearchServiceClient` classe. Per ulteriori informazioni, vedere [Tentativi automatico][auto-rest-client-retry].

**Diagnostica**. Utilizzare [ricerca il traffico Analitica][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Si leggono dati dalla ricerca Azure viene interrotta.

**Rilevamento**. Rilevare `Microsoft.Rest.Azure.CloudException` errori.

**Ripristino**

[Ricerca .NET SDK] [ search-sdk] ritenta automaticamente dopo errori temporanei. Tutte le eccezioni generate dal client SDK da considerare come errori non temporanei.

Il criterio di Riprova predefinito utilizza esponenziale back-off. Per utilizzare un criterio di diversi tentativi, chiamare `SetRetryPolicy` sul `SearchIndexClient` o `SearchServiceClient` classe. Per ulteriori informazioni, vedere [Tentativi automatico][auto-rest-client-retry].

**Diagnostica**. Utilizzare [ricerca il traffico Analitica][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Leggere o scrivere un nodo ha esito negativo.

**Rilevamento**. Rilevare l'eccezione. Per i client .NET, sarà generalmente il `System.Web.HttpException`. Altri client siano disponibili altri tipi di eccezione.  Per ulteriori informazioni, vedere [gestione degli errori di Cassandra fatto a destra](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Ripristino**

- Ogni [client Cassandra](https://wiki.apache.org/cassandra/ClientOptions) ha propri criteri Riprova e funzionalità. Per ulteriori informazioni, vedere [gestione degli errori di Cassandra completato destra][cassandra-error-handling].
- Utilizzare una distribuzione rack importanti con nodi di dati distribuiti in domini guasto.
- Distribuire per più paesi con coerenza base locale. Se si verifica un errore non temporanei, non su un'altra area.

**Diagnostica**. Registri delle applicazioni


## <a name="cloud-service"></a>Servizio cloud

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Ruoli Web o lavoro in modo imprevisto sono verrà chiuso.

**Rilevamento**. [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] evento.

**Ripristino**. Ignorare [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] metodo per pulire normalmente. Per ulteriori informazioni, vedere [Il modo di destra per gestire gli eventi di Azure OnStop] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Lettura di dati da DocumentDB ha esito negativo.

**Rilevamento**. Rilevare `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`. 

**Ripristino**

- SDK ritenta automaticamente tentativi. Per impostare il numero di tentativi e il tempo di attesa massimo, configurare `ConnectionPolicy.RetryOptions`. Eccezioni che genera il client sono oltre il criterio Riprova o non sono errori temporanei. 

- Se DocumentDB limita il client, viene restituito un errore 429 HTTP. Archiviare il codice di stato di `DocumentClientException`. Se si desidera ottenere errore 429 in modo coerente, aumentare il valore di produttività dell'insieme DocumentDB.

- Replicare il database DocumentDB su due o più aree. Ogni replica è leggibile. Utilizzando il client SDK, specificare la `PreferredLocations` parametro. Si tratta di un elenco ordinato delle aree Azure. Verranno inviate tutte le operazioni lettura per la prima area disponibile nell'elenco. Se la richiesta non riesce, il client tenterà regioni nell'elenco in ordine. Per ulteriori informazioni, vedere [sviluppo di applicazioni con un account di più paesi DocumentDB][docdb-multi-region].

**Diagnostica**. Registra tutti gli errori sul lato client.


### <a name="writing-data-to-documentdb-fails"></a>Scrivere dati DocumentDB ha esito negativo.

**Rilevamento**. Rilevare `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`. 

**Ripristino**

- SDK ritenta automaticamente tentativi. Per impostare il numero di tentativi e il tempo di attesa massimo, configurare `ConnectionPolicy.RetryOptions`. Eccezioni che genera il client sono oltre il criterio Riprova o non sono errori temporanei. 

- Se DocumentDB limita il client, viene restituito un errore 429 HTTP. Archiviare il codice di stato di `DocumentClientException`. Se si desidera ottenere errore 429 in modo coerente, aumentare il valore di produttività dell'insieme DocumentDB.

- Replicare il database DocumentDB su due o più aree. Se l'area principale non riesce, un'altra area alzato di livello per scrivere. È anche possibile attivare manualmente caso di errore. SDK esegue il rilevamento automatico routing, in modo che il codice dell'applicazione continua a funzionare dopo caso di errore. Durante il periodo di failover (in genere minuti), le operazioni di scrittura avranno latenza superiore, SDK rileva la nuova area di scrittura. Per ulteriori informazioni, vedere [sviluppo di applicazioni con un account di più paesi DocumentDB][docdb-multi-region].

- Come fallback, il documento a una coda di backup in modo permanente ed elaborare la coda in un secondo momento.

**Diagnostica**. Registra tutti gli errori sul lato client.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Lettura di dati da Elasticsearch ha esito negativo.

**Rilevamento**. Rilevare l'eccezione appropriata per il particolare [client Elasticsearch] [ elasticsearch-client] in uso. 

**Ripristino**

- Utilizzare un meccanismo di ripetizione. Ogni client sono previsti i propri criteri Riprova. 

- Distribuire più nodi Elasticsearch e utilizzare la replica disponibilità elevata.

Per ulteriori informazioni, vedere [Elasticsearch in esecuzione in Azure][elasticsearch-azure].

**Diagnostica**. È possibile utilizzare gli strumenti di monitoraggio per Elasticsearch o registra tutti gli errori sul lato client con payload. Nella sezione 'Monitoraggio' [Elasticsearch in esecuzione in Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Scrivere dati Elasticsearch ha esito negativo.

**Rilevamento**. Rilevare l'eccezione appropriata per il particolare [client Elasticsearch] [ elasticsearch-client] in uso.  

**Ripristino**

- Utilizzare un meccanismo di ripetizione. Ogni client sono previsti i propri criteri Riprova. 
 
- Se l'applicazione è in grado di tollerare un livello di coerenza ridotte, prendere in considerazione per la scrittura con `write_consistency` l'impostazione di `quorum`.

Per ulteriori informazioni, vedere [Elasticsearch in esecuzione in Azure][elasticsearch-azure].

**Diagnostica**. È possibile utilizzare gli strumenti di monitoraggio per Elasticsearch o registra tutti gli errori sul lato client con payload. Nella sezione 'Monitoraggio' [Elasticsearch in esecuzione in Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Spazio di archiviazione coda

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Scrivere un messaggio non riesce lo spazio di archiviazione di Azure coda in modo coerente.

**Rilevamento**. Dopo *N* tentativi, non riesce comunque l'operazione di scrittura.

**Ripristino**

- Archiviare i dati in una cache locale e inoltrare le operazioni di scrittura per lo spazio di archiviazione in un secondo momento, quando il servizio diventa disponibile.
- Creare una coda secondaria e scrittura per tale coda se coda primaria non è disponibile.

**Diagnostica**. Utilizzare [metriche di archiviazione][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L'applicazione non è in grado di elaborare un determinato messaggio dalla coda. 

**Rilevamento**. Specifica l'applicazione. Ad esempio, il messaggio contiene dati non validi o la regola business ha esito negativo per qualche motivo. 

**Ripristino**

Spostare il messaggio a una coda separata. Eseguire un processo separato per esaminare i messaggi presenti nella coda.

Valutare la possibilità di utilizzo delle code Azure servizio Bus messaggistica che fornisce una [coda inattiva] [ sb-dead-letter-queue] funzionalità a questo scopo.

Nota: Se si utilizza code di spazio di archiviazione con WebJobs, SDK WebJobs fornisce la gestione dei messaggi predefiniti. Informazioni su [come utilizzare l'archiviazione di Azure coda con SDK WebJobs][sb-poison-message].

**Diagnostica**. Utilizzare la registrazione delle applicazioni.


## <a name="redis-cache"></a>Redis Cache

### <a name="reading-from-the-cache-fails"></a>Errore di lettura dalla cache.

**Rilevamento**. Rilevare `StackExchange.Redis.RedisConnectionException`.

**Ripristino**

1. Riprova in errori temporanei. Azure Redis cache supporta tentativi incorporato tramite vedere [linee guida Riprova Cache Redis][redis-retry].
2. Indicazione non temporanei errori di accesso alla cache e tornare all'origine dati.

**Diagnostica**. Utilizzare [la diagnostica Cache Redis][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Ha esito negativo per la scrittura nella cache.

**Rilevamento**. Rilevare `StackExchange.Redis.RedisConnectionException`.

**Ripristino**

1. Riprova in errori temporanei. Azure Redis cache supporta tentativi incorporato tramite vedere [linee guida Riprova Cache Redis][redis-retry].
2. Se l'errore si è non temporanei, ignorarla e consentire altre transazioni scrivere la cache in un secondo momento.

**Diagnostica**. Utilizzare [la diagnostica Cache Redis][redis-monitor].


## <a name="sql-database"></a>Database SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Non è possibile connettersi al database nell'area principale.

**Rilevamento**. Connessione non riesce.

**Ripristino**

Prerequisito: Il database deve essere configurato per la replica geografico attiva. Vedere [SQL Database attiva geografico replica][sql-db-replication].

- Per le query, leggere da una replica secondaria. 
- Per gli inserimenti e gli aggiornamenti manualmente esito negativo su a una replica secondaria. Vedere [avviare caso di errore pianificato o non pianificato per il Database di SQL Azure][sql-db-failover].

La replica utilizza una stringa di connessione diverso, pertanto è necessario aggiornare la stringa di connessione dell'applicazione.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Client esaurito connessioni nel pool di connessioni.

**Rilevamento**. Rilevare `System.InvalidOperationException` errori. 

**Ripristino**

- Ripetere l'operazione.
- Come un piano di attenuazione, isolare il pool di connessioni per ogni caso di utilizzo, in modo che un caso di utilizzo non è possibile dominare tutte le connessioni.
- Aumentare il pool di connessioni massimo.

**Diagnostica**. Log applicazioni.


### <a name="database-connection-limit-is-reached"></a>È raggiunto il limite di connessione di database. 

**Rilevamento**. Database SQL Azure limita il numero di collaboratori simultanei, gli account di accesso e sessioni. I limiti dipendono dal livello di servizio. Per ulteriori informazioni, vedere [limiti delle risorse di Database SQL Azure][sql-db-limits].

Per rilevare questi errori, intercettare `System.Data.SqlClient.SqlException` e controllare il valore di `SqlException.Number` per il codice di errore SQL. Per un elenco dei codici di errore pertinenti, vedere [codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database][sql-db-errors].

**Ripristino**. Questi errori sono considerati temporanei, in modo che la ripetizione potrebbe risolvere il problema. Se si raggiunge in modo coerente questi errori, è consigliabile proporzioni dei caratteri del database.

**Diagnostica**. - [Sys.event_log] [ sys.event_log] query restituisce le connessioni al database ha esito positivo, gli errori delle connessioni e blocchi critici.

- Creare una [regola di avviso] [ azure-alerts] per connessioni non riuscite.

- Abilitare [il controllo dei Database SQL] [ sql-db-audit] e controllare gli accessi non riusciti.


## <a name="service-bus-messaging"></a>Servizio di messaggistica Bus

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Leggere un messaggio da una coda di servizio Bus ha esito negativo.

**Rilevamento**. Eccezioni dal client SDK. La classe di base per le eccezioni Bus di servizio è [MessagingException][sb-messagingexception-class]. Se l'errore si è temporanea, il `IsTransient` è true. 

Per ulteriori informazioni, vedere [Bus di servizio di messaggistica eccezioni][sb-messaging-exceptions].

**Ripristino**

1. Riprova in errori temporanei. Vedere [Bus di servizio Riprova linee guida][sb-retry].

2. I messaggi che non viene recapitati a un destinatario vengono inseriti in una *coda inattiva*. Usare questa coda per visualizzare i messaggi non è stati ricevuti. Non esiste alcun pulizia automatica di coda inattiva. I messaggi restano presenti finché non si recuperano in modo esplicito. Vedere [Panoramica di servizio Bus inattiva code][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Si scrive un messaggio a un servizio Bus coda ha esito negativo.

**Rilevamento**. Eccezioni dal client SDK. La classe di base per le eccezioni Bus di servizio è [MessagingException][sb-messagingexception-class]. Se l'errore si è temporanea, il `IsTransient` è true. 

Per ulteriori informazioni, vedere [Bus di servizio di messaggistica eccezioni][sb-messaging-exceptions].

**Ripristino**

1. Il client di servizio Bus ritenta automaticamente dopo errori temporanei. Per impostazione predefinita, utilizzerà esponenziale back-off. Dopo il numero massimo di tentativi o il periodo di timeout massimo, il client genera un'eccezione. Per ulteriori informazioni, vedere [Bus di servizio Riprova linee guida][sb-retry].

2. Se viene superata la quota di coda, il client genera [QuotaExceededException][QuotaExceededException]. Il messaggio dell'eccezione fornisce ulteriori dettagli. Svuotare alcuni messaggi dalla coda prima di riprovare, si consiglia di utilizzare il modello interruttore per evitare tentativi "Continua" mentre la quota superata. Inoltre, verificare che la proprietà [BrokeredMessage.TimeToLive] non è impostata troppo alto. 

3. In un'area sulla resilienza può essere migliorata utilizzando [code partizionate o argomenti][sb-partition]. Non suddiviso coda o un argomento è assegnata a un archivio di SMS. Se questo archivio messaggistica non è disponibile, tutte le operazioni nella coda o argomento avrà esito negativo. Coda partizionata o un argomento è suddiviso in più archivi di messaggistica. 

4. Per un'ulteriore verifica, creare due spazi dei nomi Bus di servizio in diverse aree geografiche e replicare i messaggi. È possibile utilizzare sia la replica attiva o passivo.

    - La replica di Active: il client invia tutti i messaggi a due code. Il ricevitore è in attesa in entrambe le code. Contrassegnare i messaggi con un identificatore univoco, in modo che il cliente può eliminare i messaggi duplicati.

    - Replica passiva: il client invia il messaggio a una coda. Se si verifica un errore, il client a altra coda. Il ricevitore è in attesa in entrambe le code. Questo approccio riduce il numero di messaggi duplicati inviati. Tuttavia, il destinatario deve ancora gestire i messaggi duplicati.

    Per ulteriori informazioni, vedere [esempio GeoReplication] [ sb-georeplication-sample] e [le procedure consigliate per le applicazioni isolanti contro interruzioni Bus di servizio e guasti][sb-outages].


### <a name="duplicate-message"></a>Messaggio duplicato.

**Rilevamento**. Esaminare il `MessageId` e `DeliveryCount` proprietà del messaggio.

**Ripristino**

- Se possibile, progettare il messaggio di operazioni di trasformazione per essere idempotenti. In caso contrario, archiviare gli ID di messaggio dei messaggi già elaborati e controllare l'ID prima di elaborazione di un messaggio.

- Attivare il rilevamento duplicati, creando coda con `RequiresDuplicateDetection` impostato su true. Con questa impostazione, Bus di servizio elimina automaticamente tutti i messaggi inviati con lo stesso `MessageId` come messaggio precedente.  Tenere presente quanto segue:

    -  Questa impostazione impedisce che viene inserito nella coda di messaggi duplicati. Un ricevitore non impedisce di elaborazione più volte lo stesso messaggio.

    - Rilevamento duplicati ha un intervallo di tempo. Oltre a questa finestra viene inviato un duplicato, non rilevata.

**Diagnostica**. Registrare i messaggi duplicati.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L'applicazione non è in grado di elaborare un determinato messaggio dalla coda. 

**Rilevamento**. Specifica l'applicazione. Ad esempio, il messaggio contiene dati non validi o la regola business ha esito negativo per qualche motivo. 

**Ripristino**

Esistono due tipi di guasto da prendere in considerazione. 

- Il ricevitore rileva l'errore. In questo caso, spostare il messaggio in coda inattiva. In un secondo momento, eseguire un processo separato per esaminare i messaggi nella coda inattiva.

- Il ricevitore non riesce nel centro di elaborazione del messaggio &mdash; , ad esempio a causa di un'eccezione non gestita. Per gestire questo caso, utilizzare `PeekLock` modalità. In questa modalità, se il blocco scade, il messaggio sarà disponibile per gli altri destinatari. Se il messaggio supera il numero massimo di recapito o la durata, il messaggio viene automaticamente spostato coda inattiva.

Per ulteriori informazioni, vedere [Panoramica di servizio Bus inattiva code][sb-dead-letter-queue].

**Diagnostica**. Ogni volta che l'applicazione viene spostato un messaggio in coda inattiva, scrivere un evento nel registro applicazione.


## <a name="service-fabric"></a>Infrastruttura di servizio

### <a name="a-request-to-a-service-fails"></a>Una richiesta a un servizio non riesce.

**Rilevamento**. Il servizio restituisce un errore.

**Ripristino**

- Individuare un proxy di nuovo (`ServiceProxy` o `ActorProxy`) e chiamare nuovamente il metodo di servizio/attore. 

- **Servizio informazioni sullo stato**. Disporre le operazioni sulle raccolte affidabile in una transazione. Se si verifica un errore, verrà riportata nuovamente la transazione. La richiesta, se estratti da una coda verrà elaborata nuovamente. 
 
- **Servizio senza informazioni sullo stato**. Se il servizio mantiene i dati in un archivio esterno, tutte le operazioni devono essere idempotenti.


**Diagnostica**. Registro delle applicazioni

### <a name="service-fabric-node-is-shut-down"></a>Nodo tessuti servizio viene chiuso.

**Rilevamento**. Un token di annullamento è passato al servizio `RunAsync` metodo. Servizio tessuti Annulla l'attività prima di arrestare il nodo.

**Ripristino**. Utilizzare il token di annullamento per rilevare arresto. Quando servizio tessuti richiede l'annullamento, completare le attività e uscire da `RunAsync` presto.

**Diagnostica**. Registri delle applicazioni


## <a name="storage"></a>Spazio di archiviazione

### <a name="writing-data-to-azure-storage-fails"></a>Scrittura di dati per lo spazio di archiviazione di Azure viene interrotta

**Rilevamento**. Il client riceve errori durante la scrittura.

**Ripristino**

1. Ripetere l'operazione per recuperare gli errori temporanei. [Riprovare criteri] [ Storage.RetryPolicies] nel client SDK viene gestita automaticamente.
2. Implementare il modello interruttore per evitare enorme lo spazio di archiviazione.
3. Se N tentativi hanno esito negativo, eseguire un fallback irregolari. Per esempio:

    - Archiviare i dati in una cache locale e inoltrare le operazioni di scrittura per lo spazio di archiviazione in un secondo momento, quando il servizio diventa disponibile.
    - Se l'operazione di scrittura è stato in un ambito transazione, compensare la transazione.

**Diagnostica**. Utilizzare [metriche di archiviazione][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Si leggono dati dallo spazio di archiviazione di Azure viene interrotta.

**Rilevamento**. Il client riceve errori durante la lettura.

**Ripristino**

1. Ripetere l'operazione per recuperare gli errori temporanei. [Riprovare criteri] [ Storage.RetryPolicies] nel client SDK viene gestita automaticamente.
2. Per l'archiviazione RA GRS, se la lettura dal punto finale principale non riesce, provare a lettura da endpoint secondario. Il client SDK può gestire questo automaticamente. Vedere [lo spazio di archiviazione di Azure replica][storage-replication].
3. Se *N* tentativi hanno esito negativo, eseguire un'azione di fallback per adattarsi. Ad esempio, se non è possibile recuperare un'immagine di prodotto dallo spazio di archiviazione, mostrare un'immagine segnaposto generico.

**Diagnostica**. Utilizzare [metriche di archiviazione][storage-metrics].


## <a name="virtual-machine"></a>Macchina virtuale

### <a name="connection-to-a-backend-vm-fails"></a>Connessione a un back-end macchine Virtuali non riesce.

**Rilevamento**. Errori di connessione di rete.

**Ripristino**

- Distribuire macchine virtuali di almeno due back-end in un set di disponibilità, dietro un bilanciamento del carico.

- Se l'errore di connessione è temporaneo, a volte TCP correttamente tenterà di inviare il messaggio. 

- Implementare un criterio di Riprova nell'applicazione. 

- Per gli errori permanenti o non temporanei, implementare l' [interruttore] [ circuit-breaker] motivo.

- Se la macchina virtuale chiamante supera il limite di uscita di rete, pertanto riempiono coda in uscita. Se coda in uscita in modo coerente completa, valutare la possibilità di scalabilità. 

**Diagnostica**. Registro eventi in corrispondenza dei limiti di servizio.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Istanza macchine Virtuali diventa disponibile o non corretti.

**Rilevamento**. Configurare una [verifica dell'integrità] di bilanciamento del carico[ lb-probe] che segnala se l'istanza di macchine Virtuali è integro. La ricerca è necessario controllare se funzioni critiche siano rispondendo correttamente. 

**Ripristino**. Per ogni livello di applicazione, inserire lo stesso set di disponibilità di più istanze di macchine Virtuali e inserire un bilanciamento del carico davanti macchine virtuali. Se la verifica dell'integrità non riesce, bilanciamento del carico interrompe l'invio di nuove connessioni all'istanza non corretti.

**Diagnostica**. -Utilizzare bilanciamento del carico [analitica log][lb-monitor].
- Configurare il sistema di monitoraggio per monitorare tutti i punti finali di monitoraggio dello stato.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Operatore arrestato accidentalmente una macchina virtuale.

**Rilevamento**. N/D

**Ripristino**. Impostare un blocco di risorse con `ReadOnly` livello. Vedere [risorse di blocco con Gestione risorse di Azure][rm-locks].

**Diagnostica**. Utilizzare [attività Azure registra][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Processo continuo si arresta quando l'host dei servizi è inattivo.

**Rilevamento**. Passare alla funzione WebJob un token di annullamento. Per ulteriori informazioni, vedere [spegnimento][web-jobs-shutdown]. 

**Ripristino**. Abilitare la `Always On` impostazione in web app. Per ulteriori informazioni, vedere [attività di esecuzione in Background con WebJobs][web-jobs].


## <a name="application-design"></a>Progettazione di applicazioni

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Applicazione non è possibile gestire un picco nelle richieste in arrivo.

**Rilevamento**. Dipende dall'applicazione. Sintomi tipici:

- Il sito Web verrà avviata la restituzione di codici di errore 5xx HTTP.
- Servizi dipendenti, ad esempio database o lo spazio di archiviazione, iniziare limitare le richieste. Cercare gli errori HTTP, ad esempio 429 HTTP (troppo numerose richieste), a seconda del servizio.
- Lunghezza coda HTTP cresce.

**Ripristino**

- Scalabilità per gestire l'aumento del carico. 

- Ridurre gli errori per evitare errori a cascata interrompere l'intera applicazione. Strategie di attenuazione includono:

    - Implementare [La limitazione motivo] [ throttling-pattern] per evitare enorme sistemi di back-end.
    - Utilizzare [carico basato su coda livellamento] [ queue-based-load-leveling] buffer richieste di elaborazione a una velocità appropriato.
    - Definizione delle priorità alcuni client. Ad esempio, se l'applicazione è gratuiti e a pagamento livelli, limitare i clienti a livello di gratuita, ma non saldati clienti. [Priorità coda]modello[priority-queue-pattern].

**Diagnostica**. Utilizzare [la registrazione diagnostica servizio App][app-service-logging]. Utilizzare un servizio, ad esempio [Azure Log Analitica][azure-log-analytics], [Applicazione approfondimenti][app-insights], o [Nuovo Relic] [ new-relic] identifichi i registri diagnostici.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Una delle operazioni in un flusso di lavoro o di una transazione distribuita ha esito negativo.

**Rilevamento**. Dopo *N* tentativi, il problema persiste.

**Ripristino**

- Come un piano di attenuazione, implementare l' [Utilità di pianificazione agente Supervisore] [ scheduler-agent-supervisor] motivo per la gestione del flusso di lavoro intera. 
- Non Riprova in timeout. Esiste un tasso di successo bassa per questo errore. 
- Lavoro coda per riprovare in seguito.

**Diagnostica**. Registrare tutte le operazioni (e non riuscite), tra cui compensatori azioni. Utilizzare correlazione ID, è possibile tenere traccia di tutte le operazioni all'interno della stessa transazione.


### <a name="a-call-to-a-remote-service-fails"></a>Errore di una chiamata a un servizio remoto.

**Rilevamento**. Codice di errore HTTP.

**Ripristino**

1. Riprova in errori temporanei. 
2. In caso contrario dopo *N* tentativi, eseguire un'azione di fallback. (Applicazione specifica).
3. Implementare l' [interruttore motivo] [ circuit-breaker] per evitare errori a cascata. 

**Diagnostica**. Registrare tutti gli errori di chiamate remote.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo FMA, vedere [recupero di una caratteristica predefinita per i servizi cloud] [ resilience-by-design-pdf] (Scarica PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

