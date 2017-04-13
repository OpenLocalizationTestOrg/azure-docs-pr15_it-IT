<properties 
    pageTitle="Come risolvere i problemi di Azure Redis Cache | Microsoft Azure" 
    description="Informazioni su come risolvere i problemi comuni con Azure Redis Cache." 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Come risolvere i problemi di Azure Redis Cache

In questo articolo fornisce indicazioni per la risoluzione dei problemi seguenti categorie dei problemi di Azure Redis Cache.

-   [Risoluzione dei problemi sul lato client](#client-side-troubleshooting) - in questa sezione vengono fornite istruzioni sull'identificazione e risoluzione dei problemi causati dall'applicazione di connessione a Azure Redis Cache.
-   [Risoluzione dei problemi sul lato server](#server-side-troubleshooting) - in questa sezione vengono fornite istruzioni sull'identificazione e risoluzione dei problemi causati sul lato server Azure Redis Cache.
-   [Eccezioni di timeout StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - in questa sezione vengono fornite informazioni sulla risoluzione dei problemi quando si usa il client StackExchange.Redis.


>[AZURE.NOTE] Molte delle procedure di risoluzione dei problemi in questa guida include istruzioni per eseguire comandi Redis e monitorare vari criteri di misurazione delle prestazioni. Per ulteriori informazioni e istruzioni, vedere gli articoli della sezione [informazioni aggiuntive](#additional-information) .

## <a name="client-side-troubleshooting"></a>Risoluzione dei problemi sul lato client


In questa sezione vengono illustrate la risoluzione dei problemi che si verificano a causa di una condizione nell'applicazione client.

-   [Pressione di memoria sul client](#memory-pressure-on-the-client)
-   [Burst del traffico](#burst-of-traffic)
-   [Client elevato utilizzo della CPU](#high-client-cpu-usage)
-   [Larghezza di banda di lato client superata](#client-side-bandwidth-exceeded)
-   [Richiesta di grandi dimensioni](#large-requestresponse-size)
-   [Modifiche relative ai dati personali in Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Pressione di memoria sul client

#### <a name="problem"></a>Problema

Pressione della memoria del computer client conduce a tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione dei dati inviati dall'istanza Redis senza ritardi. Quando viene premuto pressione della memoria, il sistema deve in genere dati di una pagina dalla memoria fisica a memoria virtuale su disco. Questa *pagina che* causa il rallentamento significativo del sistema.

#### <a name="measurement"></a>Misura 

1.  Monitorare l'utilizzo della memoria nel computer per assicurarsi che non superi memoria disponibile. 
2.  Monitorare il `Page Faults/Sec` contatore delle prestazioni. La maggior parte dei sistemi verranno sono alcuni degli errori di pagina anche durante il normale funzionamento, pertanto prestare attenzione ai picchi di questo contatore delle prestazioni errori pagina che corrispondono alle timeout.

#### <a name="resolution"></a>Risoluzione

Aggiornamento del client a un client di grande dimensioni macchine Virtuali con più memoria o approfondire i modelli di utilizzo della memoria per ridurre consuption memoria.


### <a name="burst-of-traffic"></a>Burst del traffico

#### <a name="problem"></a>Problema

Burst di traffico combinato con scarso `ThreadPool` impostazioni possono causare ritardi nell'elaborazione dei dati già inviato dal Server Redis ma non ancora consumate sul lato client.

#### <a name="measurement"></a>Misura 

Monitor come il `ThreadPool` statistiche cambiano nel tempo utilizzando codice [simile](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). È anche possibile esaminare il `TimeoutException` messaggio StackExchange.Redis. Ecco un esempio:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Nel messaggio precedente, esistono diversi problemi che sono interessanti:

 1. Si noti che la `IOCP` sezione e la `WORKER` sezione si dispone di un `Busy` valore maggiore del `Min` valore. Ciò significa che il `ThreadPool` impostazioni necessario regolare.
 2. È anche possibile visualizzare `in: 64221`. Indica che byte 64211 sono state ricevute al livello socket kernel ma non ancora letti dall'applicazione (ad esempio StackExchange.Redis). Questo errore indica in genere che l'applicazione non si legge dati dalla rete non appena il server invia all'utente.

#### <a name="resolution"></a>Risoluzione

Configurare le [Impostazioni del pool](https://gist.github.com/JonCole/e65411214030f0d823cb) per assicurarsi che il pool di thread verrà scalare rapidamente in scenari burst.


### <a name="high-client-cpu-usage"></a>Client elevato utilizzo della CPU

#### <a name="problem"></a>Problema

Utilizzo della CPU elevato sul client è l'indicazione che il sistema non è possibile gestire il lavoro che è stato chiesto di eseguire. Questo errore indica che il client potrebbe non riuscire per l'elaborazione di una risposta da Redis in modo tempestivo anche se Redis ha inviato la risposta molto rapidamente.

#### <a name="measurement"></a>Misura

Monitorare l'utilizzo della CPU di larghezza di sistema tramite il portale di Azure o tramite il contatore delle prestazioni associato. Prestare attenzione a non eseguire il monitoraggio delle CPU *processo* poiché un singolo processo può avere un basso utilizzo della CPU allo stesso tempo che il sistema globali CPU può essere elevato. Prestare attenzione ai picchi di utilizzo della CPU che corrispondono ai timeout. In seguito CPU elevata, potrebbe essere anche visualizzata alta `in: XXX` i valori nella `TimeoutException` i messaggi di errore come descritto nella sezione [Burst del traffico](#burst-of-traffic) .

>[AZURE.NOTE] StackExchange.Redis 1.1.603 e versioni successive include il `local-cpu` metriche in `TimeoutException` messaggi di errore. Assicurarsi che si utilizza la versione più recente del [pacchetto StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Esistono bug costantemente vengano il codice per renderla più efficace di timeout in modo che la versione più recente è importante.

#### <a name="resolution"></a>Risoluzione

Eseguire l'aggiornamento a una dimensione maggiore per macchine Virtuali con maggiore capacità di CPU o analizzare la causa CPU viene utilizzata. 



### <a name="client-side-bandwidth-exceeded"></a>Larghezza di banda di lato client superata

#### <a name="problem"></a>Problema

Computer client dimensioni diversi presentano limitazioni in quanta larghezza di banda di rete che hanno disponibili. Se il client supera la larghezza di banda disponibile, quindi dati non essere elaborati sul lato client non appena il server lo invia. Questo può comportare timeout.

#### <a name="measurement"></a>Misura

Monitorare come l'utilizzo della larghezza di banda cambiare nel tempo utilizzando codice [simile](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Si noti che questo codice non possono essere eseguite correttamente in alcuni ambienti con autorizzazioni limitate (ad esempio siti web Azure).

#### <a name="resolution"></a>Risoluzione 

Dimensione memoria virtuale Client di aumentare o ridurre l'utilizzo della larghezza di banda di rete.


### <a name="large-requestresponse-size"></a>Richiesta di grandi dimensioni

#### <a name="problem"></a>Problema

Una richiesta di grandi dimensioni/risposta possono causare timeout. Ad esempio, se il valore di timeout configurato nel client è 1 secondo. L'applicazione richiede due chiavi (ad esempio 'A' 'B') allo stesso tempo (usando la stessa connessione di rete). La maggior parte dei client supportano "Pipelining" delle richieste, in modo che entrambe le richieste 'A' e 'B' vengono inviate su una connessione al server uno dopo l'altra senza attendere che le risposte. Il server invierà le risposte nuovamente nello stesso ordine. Se risposta 'A' è grande abbastanza essa può influire notevolmente la maggior parte dei timeout per le successive richieste. 

Nell'esempio seguente viene illustrato questo scenario. In questo scenario, richiesta 'A' e 'B' vengono inviate rapidamente, il server inizia a inviare rapidamente le risposte 'A' e 'B', ma a causa di tempi di trasferimento di dati, 'B' caso di problemi dietro altri richiesta e timeout anche se il server ha risposto rapidamente.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Misura

Questo è difficile misurare. In pratica, è necessario dotare codice client per tenere traccia delle risposte e le richieste di grandi dimensioni. 

#### <a name="resolution"></a>Risoluzione

1.  Redis ottimizzata per un numero elevato di valori più bassi, invece di alcuni valori di grandi dimensioni. La soluzione migliore consiste nel suddividere i dati in valori più piccoli correlati. Vedere la [cos'è l'intervallo di dimensioni valore ideale per redis? È troppo grande 100KB?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) registrare per informazioni dettagliate su perché si consiglia di utilizzare i valori più piccoli.
2.  Aumentare le dimensioni della macchina virtuale (per client e Server Cache Redis), per ottenere le funzionalità della larghezza di banda superiore, ridurre i tempi di trasferimento di dati per le risposte più grande. Si noti che Guida larghezza di banda solo il server o solo via il client non sia sufficiente. Misurare l'utilizzo della larghezza di banda e confrontarla con le funzionalità delle dimensioni della macchine Virtuali attualmente installata.
3.  Aumentare il numero di `ConnectionMultiplexer` oggetti utilizzo e le richieste di circolari connessioni diverse.


### <a name="what-happened-to-my-data-in-redis"></a>Modifiche relative ai dati personali in Redis?

#### <a name="problem"></a>Problema

Previsto per alcuni dati in personale istanza di Azure Redis Cache ma non sembra sia presente.

##### <a name="resolution"></a>Risoluzione

Vedere [modifiche apportate ai dati personali in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) per possibili cause e soluzioni.


## <a name="server-side-troubleshooting"></a>Risoluzione dei problemi sul lato server

In questa sezione vengono illustrate la risoluzione dei problemi che si verificano a causa di una condizione nel server cache.

-   [Pressione di memoria nel server](#memory-pressure-on-the-server)
-   [Utilizzo della CPU elevato / Server caricare](#high-cpu-usage-server-load)
-   [Larghezza di banda di lato server superata](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pressione di memoria nel server

#### <a name="problem"></a>Problema

Pressione di memoria sul lato server conduce a tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle richieste. Quando viene premuto pressione della memoria, il sistema deve in genere dati di una pagina dalla memoria fisica a memoria virtuale su disco. Questa *pagina che* causa il rallentamento significativo del sistema. Esistono diverse cause di questa pressione di memoria: 

1.  Si ha la cache la piena i dati immessi. 
2.  Redis visualizzi frammentazione della memoria alta - spesso causati da archiviare oggetti di grandi dimensioni (Redis ottimizzata per oggetti di piccole dimensioni, vedere la [che cos'è l'intervallo di dimensioni valore ideale per redis? È troppo grande 100KB?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) post per informazioni dettagliate). 

#### <a name="measurement"></a>Misura

Redis espone due metriche che è possono identificare il problema. Il primo è `used_memory` e l'altro `used_memory_rss`. [Questi](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) sono disponibili nel portale di Azure o tramite il comando [Redis INFO](http://redis.io/commands/info) .

#### <a name="resolution"></a>Risoluzione

Esistono diverse eventuali modifiche apportate allo scopo di mantenere integro l'utilizzo di memoria:

1. [Configurare un criterio di memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e impostare le chiavi scadenze. Si noti che questo potrebbe non essere sufficiente se si dispone di frammentazione.
2. [Configurare un valore riservato maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) che sia sufficiente per compensa frammentazione della memoria.
3. Suddividere gli oggetti memorizzati nella cache grandi in più piccoli oggetti correlati.
4. [Scala](cache-how-to-scale.md) di una dimensione maggiore per cache.
5. Se si utilizza una [cache premium con cluster Redis abilitato](cache-how-to-premium-clustering.md) è possibile [aumentare il numero di shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilizzo della CPU elevato / Server caricare

#### <a name="problem"></a>Problema

Utilizzo della CPU elevato può indicare che sul lato client può avere esito negativo per l'elaborazione di una risposta da Redis in modo tempestivo anche se Redis ha inviato la risposta molto rapidamente.

#### <a name="measurement"></a>Misura

Monitorare l'utilizzo della CPU di larghezza di sistema tramite il portale di Azure o tramite il contatore delle prestazioni associato. Prestare attenzione a non eseguire il monitoraggio delle CPU *processo* poiché un singolo processo può avere un basso utilizzo della CPU allo stesso tempo che il sistema globali CPU può essere elevato. Prestare attenzione ai picchi di utilizzo della CPU che corrispondono ai timeout.

#### <a name="resolution"></a>Risoluzione

[Scala](cache-how-to-scale.md) a una cache maggiore livello con maggiore capacità di CPU o analizzare la causa CPU viene utilizzata. 

### <a name="server-side-bandwidth-exceeded"></a>Larghezza di banda di lato server superata

#### <a name="problem"></a>Problema

Le istanze della cache di dimensioni diverse presentano limitazioni in quanta larghezza di banda di rete che hanno disponibili. Se il server è maggiore della larghezza di banda, dati non verrà inviati al client come rapidamente. Questo può comportare timeout.

#### <a name="measurement"></a>Misura

È possibile monitorare il `Cache Read` unità di misura metriche, ossia la quantità di dati letti dalla cache in megabyte al secondo (MB/s) durante l'intervallo di creazione di report specificato. Questo valore corrisponde alla larghezza di banda di rete utilizzato da questa cache. Se si desidera impostare avvisi per limiti della larghezza di banda di rete sul lato server, è possibile crearli utilizzando questo `Cache Read` contatore. Confrontare i valori con i valori in [questa tabella](cache-faq.md#cache-performance) per i limiti della larghezza di banda osservati per cache vari prezzi livelli e le dimensioni.

#### <a name="resolution"></a>Risoluzione

Se si è in modo coerente vicino osservato massimo della larghezza di banda per le dimensioni di livello e della cache dei prezzi, valutare la possibilità di [ridimensionamento](cache-how-to-scale.md) a un livello o le dimensioni con maggiore larghezza di banda, con i valori indicati in [questa tabella](cache-faq.md#cache-performance) come guida prezzi.


## <a name="stackexchangeredis-timeout-exceptions"></a>Eccezioni di timeout StackExchange.Redis

StackExchange.Redis utilizza una configurazione impostazione denominata `synctimeout` per icona del operazioni che è un valore predefinito di 1000 ms. Se una chiamata icona del non viene completata nel tempo stabilito, il client StackExchange.Redis genera un errore di timeout simile all'esempio seguente.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Questo messaggio di errore contiene la metrica che consente di scegliere le cause e possibili nella risoluzione del problema. Nella tabella seguente contiene informazioni dettagliate sulle metriche di messaggio di errore.

| Unità di misura metriche messaggio errore | Dettagli                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inst       | Nella sezione ora ultima: 0 comandi sono stati rilasciati                                                                                                                                                                                              |
| gestione        | Esecuzione di gestione di socket `socket.select` il che significa che richiede il sistema operativo per indicare un socket che in qualche modo; in pratica: il lettore non viene lettura attivamente dalla rete perché non pensare c'è ancora qualcosa da eseguire |
| coda      | Esistono 73 totale operazioni in corso                                                                                                                                                                                                        |
| qu         | 6 di operazioni in corso nella coda non inviata e non sono state scritte alla rete in uscita                                                                                                                                                           |
| qs         | 67 operazioni in corso he sono stati inviati al server ma una risposta non è ancora disponibile. La risposta potrebbe essere `Not yet sent by the server` o`sent by the server but not yet processed by the client.`                                                   |
| QC         | 0 operazioni in corso sono visualizzate le risposte, ma non ancora contrassegnato come completato a causa di attesa nel ciclo di completamento                                                                                                                                      |
| WR         | Esiste un byte/activewriters writer attivo (ovvero che non vengono ignorate e le richieste non inviate 6)                                                                                                                                                   |
| in         | Nessun lettore attivo e zero byte sono disponibili a essere letti nel NIC byte/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Procedura per verificare se sono

1. Come una buona norma assicurarsi che si usa il modello per la connessione quando si usa il client StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Per ulteriori informazioni, vedere [connettersi alla cache utilizzando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Assicurarsi che la Cache di Redis Azure e l'applicazione client siano nella stessa regione Azure. Ad esempio, si potrebbe essere visualizzato timeout durante la cache negli Stati Uniti orientali ma il client è negli Stati Uniti ovest e la richiesta non viene completata entro il `synctimeout` intervallo o si potrebbe essere visualizzato timeout quando si esegue il debug dal computer locale sviluppo. 

    Si consiglia di cache e nel client nella stessa regione Azure. Se si dispone di uno scenario che include le chiamate tra l'area geografica, è necessario impostare il `synctimeout` intervallo su un valore superiore rispetto all'intervallo di 1000 ms predefinito facilmente individuabili perché includono un `synctimeout` proprietà nella stringa di connessione. Nell'esempio seguente mostra un frammento di stringa StackExchange.Redis cache connessione con un `synctimeout` di 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Assicurarsi che si utilizza la versione più recente del [pacchetto StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Esistono bug costantemente vengano il codice per renderla più efficace di timeout in modo che la versione più recente è importante.

4. Se sono presenti le richieste di recupero associate tramite limitazioni della larghezza di banda del server o client, richiederà più tempo per poter completare, causando timeout. Per verificare se il timeout a causa di larghezza di banda di rete sul server, vedere [larghezza di banda di lato Server superata](#server-side-bandwidth-exceeded). Per verificare se il timeout a causa di larghezza di banda client, vedere [larghezza di banda di lato Client superato](#client-side-bandwidth-exceeded).

6. Si guida CPU associati nel server o sul client?
    -   Verificare se è visualizzato associate tramite CPU nel client che potrebbero causare la richiesta di non essere elaborati all'interno di `synctimeout` intervallo, causando un timeout. Spostamento di una dimensione maggiore per client o la distribuzione del carico consentono di controllare questo valore. 
    -   Controllo se si desidera ottenere CPU legato nel server di monitoraggio di `CPU` [unità di misura metriche prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Le richieste in arrivo mentre Redis è associata alla CPU possono causare tali richieste al timeout. Per risolvere questo problema è possibile distribuire il carico tra più shards in una cache premium o l'aggiornamento a una dimensione maggiore o livello prezzo. Per ulteriori informazioni, vedere [Superata larghezza di banda sul lato Server](#server-side-bandwidth-exceeded).

7. Sono disponibili comandi impiegando molto tempo per l'elaborazione nel server? Esecuzione prolungata comandi che siano impiegando troppo tempo per l'elaborazione nel server redis possono causare timeout. Alcuni esempi di comandi lunga sono `mget` con un numero elevato di tasti, `keys *` o di livello inferiore lua script. È possibile connettersi all'istanza di Azure Redis Cache utilizzando il client redis cli o utilizzare la [Console Redis](cache-configure.md#redis-console) ed eseguire il comando [SlowLog](http://redis.io/commands/slowlog) per verificare se sono presenti richieste richiede più tempo del previsto. Server redis e StackExchange.Redis sono ottimizzati per molte richieste di piccole dimensioni anziché meno le richieste di grandi dimensioni. Dividere i dati in blocchi più piccoli, è possibile migliorare diverse cose. 

    Per informazioni sulla connessione all'endpoint Azure Redis Cache SSL utilizzando cli redis e stunnel, vedere il blog di [Annuncio Provider dello stato sessione ASP.NET Redis versione Preview di](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) post. Per ulteriori informazioni, vedere [SlowLog](http://redis.io/commands/slowlog).

8. Condizioni di carico elevato Redis server possono causare timeout. È possibile monitorare il carico del server controllando il `Redis Server Load` [unità di misura metriche prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Un carico di server di 100 (valore massimo) indica che il server redis è stato occupato con senza tempo di inattività, elaborazione delle richieste. Per vedere se determinate richieste occupano tutta la funzionalità server, eseguire il comando SlowLog, come descritto nel paragrafo precedente. Per ulteriori informazioni, vedere [utilizzo della CPU alta / Server caricare](#high-cpu-usage-server-load).

9. È presente qualsiasi altro evento sul lato client che può essere causato da un acustico rete? Verificare se il client (web, ruolo di lavoro o una VM Iaas) se si è verificato un evento come scala il numero di istanze di client verso l'alto o verso il basso o distribuire una nuova versione del client o ridimensionamento automatico è attivato? Nel testing che è stato trovato che può causare autoscale o il ridimensionamento crescenti/decrescenti può essere persa la connettività di rete in uscita per alcuni secondi. Codice StackExchange.Redis è adattabile a tali eventi e nuovamente la connessione. Durante questo periodo di riconnessione richieste nella coda può causare il timeout.

10. È presente una richiesta di grande precedente più piccole richieste alla Cache Redis timeout? Il parametro `qs` nel messaggio di errore messaggio indica quante richieste sono state inviate dal client al server, ma non è ancora stato elaborato una risposta. Questo valore può mantenere aumenta perché StackExchange.Redis utilizza una sola connessione TCP e sono disponibili solo una risposta alla volta. Anche se la prima operazione timeout, non termina i dati inviati da/verso il server e altre richieste vengono bloccate fino al termine di questa operazione, causando timeout. Una soluzione consiste nel ridurre il rischio di timeout per garantire che la cache sia sufficiente per il carico di lavoro e la divisione di valori di grandi dimensioni in piccoli. Un'altra possibile soluzione consiste nell'usare un pool di `ConnectionMultiplexer` gli oggetti nel client di e scegliere minimo caricato `ConnectionMultiplexer` per l'invio di una nuova richiesta. Dovrebbero impedire un solo timeout causa altre richieste a anche timeout.

11. Se si usa `RedisSessionStateprovider`, assicurarsi che si siano impostati correttamente il timeout tentativi. `retrytimeoutInMilliseconds`dovrebbe essere superiore `operationTimeoutinMilliseonds`, in caso contrario si verificherà alcun tentativi. Nell'esempio seguente `retrytimeoutInMilliseconds` è impostato su 3000. Per ulteriori informazioni, vedere [ASP.NET Session State Provider per Azure Redis Cache](cache-aspnet-session-state-provider.md) e [su come utilizzare i parametri di configurazione del Provider dello stato sessione e Provider della Cache di Output](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Controllare l'utilizzo di memoria nel server Azure Redis Cache [controllando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se un criterio di eliminazione, Redis verrà avviata la rimozione di tasti quando `Used_Memory` raggiunge le dimensioni della cache. Se possibile, `Used Memory RSS` dovrebbe essere solo leggermente superiore `Used memory`. Una differenza di grandi dimensioni significa frammentazione della memoria (interno o esterno. Quando `Used Memory RSS` è minore di `Used Memory`, significa che è stata invertita parte della memoria cache dal sistema operativo. In questo caso è possibile che alcuni latenza significativa. Perché Redis non possono controllare il mapping tra le allocazioni e pagine di memoria, alta `Used Memory RSS` è spesso il risultato di un picco dell'utilizzo della memoria. Quando Redis libera la memoria, la memoria verrà fornita allocatore e allocatore potrebbe o potrebbe non restituire la memoria al sistema. Può essere una differenza tra il `Used Memory` consumo valore e della memoria, come indicato dal sistema operativo. Può essere dovuta al fatto memoria è stata usata e rilasciata per Redis, ma non dato indietro per il sistema. Per ridurre i problemi relativi alla memoria è possibile eseguire la procedura seguente.
    -   Aggiornare la cache di una dimensione maggiore in modo che non si sta eseguendo e limitazioni della memoria del computer.
    -   Impostare le chiavi scadenze in modo che i valori nei formati precedenti sono eliminare tempestiva.
    -   Monitorare l'il `used_memory_rss` nella cache di unità di misura metriche. Quando questo valore si avvicina le dimensioni della cache, in genere che iniziare a ricevere i problemi di prestazioni. Distribuire i dati in più shards se si sta utilizzando una cache premium o eseguire l'aggiornamento a una dimensione maggiore per cache.

    Per ulteriori informazioni, vedere [Memoria nel server](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Ulteriori informazioni

-   [Quali offerta Redis Cache e le dimensioni da utilizzare](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Come è possibile valutare e verificare le prestazioni del personale cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Come è possibile eseguire i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
-   [Come eseguire il monitoraggio della Cache Redis Azure](cache-how-to-monitor.md)