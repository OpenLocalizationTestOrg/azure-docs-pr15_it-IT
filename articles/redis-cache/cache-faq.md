<properties 
    pageTitle="Azure Redis Cache domande frequenti su | Microsoft Azure" 
    description="Risposte alle domande comuni, modelli e procedure consigliate per Azure Redis Cache" 
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
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Azure Redis Cache domande frequenti

Risposte alle domande comuni, modelli e procedure consigliate per Azure Redis Cache. 


## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se una domanda non ha ricevuto risposta qui?

Se la domanda non è elencata qui, indicare e Internet consente di trovare una risposta.

-   È possibile inserire una domanda nella [Disqus thread](#comments) alla fine di queste domande frequenti e con il team di Azure Cache e altri membri della community su questo articolo.
-   Per raggiungere un pubblico più ampio, è possibile pubblicare una domanda nel [Forum MSDN di Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e con il team di Azure Cache e gli altri membri della community.
-   Se si desidera effettuare una richiesta di funzionalità, è possibile inviare le convocazioni e idee [Azure Redis Cache utenti vocali](https://feedback.azure.com/forums/169382-cache).
-   È anche possibile inviare un messaggio di posta elettronica a Microsoft [Azure](mailto:azurecache@microsoft.com)il feedback di esterni Cache.

## <a name="azure-redis-cache-basics"></a>Nozioni di base di Azure Redis Cache

Domande frequenti in questa sezione illustrate alcune delle nozioni fondamentali di Azure Redis Cache.

-    [Che cos'è Azure Redis Cache?](#what-is-azure-redis-cache)
-    [Come è possibile iniziare con Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

Domande frequenti seguenti riguardano i concetti di base e domande su Azure Redis Cache e ha ricevuto risposta in una delle altre sezioni domande frequenti.

-   [Quali offerta Redis Cache e le dimensioni da utilizzare](#what-redis-cache-offering-and-size-should-i-use)
-   [I client di cache Redis è possibile usare?](#what-redis-cache-clients-can-i-use)
-   [Esiste un emulatore per Azure Redis Cache locale?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Come monitorare l'integrità e le prestazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Domande frequenti pianificazione

-   [Quali offerta Redis Cache e le dimensioni da utilizzare](#what-redis-cache-offering-and-size-should-i-use)
-   [Prestazioni Redis Cache Azure](#azure-redis-cache-performance)
-   [In quale area è utile individuare la cache?](#in-what-region-should-i-locate-my-cache)
-   [Come si fatturati Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
-   [È possibile usare Azure Redis Cache con Azure Government Cloud o Azure Cina Cloud?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Domande frequenti di sviluppo

-   [In che modo le opzioni di configurazione StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [I client di cache Redis è possibile usare?](#what-redis-cache-clients-can-i-use)
-   [Esiste un emulatore per Azure Redis Cache locale?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Come è possibile eseguire i comandi Redis?](#how-can-i-run-redis-commands)
-   [Perché non è presente un riferimento di libreria di classi MSDN come parte di altri servizi Azure in Azure Redis Cache?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [È possibile usare Azure Redis Cache come cache sessione PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>Domande frequenti sulla protezione

-   [Quando è necessario attivare la porta non SSL per la connessione a Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>Domande frequenti di produzione

-   [Quali sono le procedure consigliate produzione?](#what-are-some-production-best-practices)
-   [Quali sono alcune delle considerazioni quando si utilizzano i comandi comuni Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Come è possibile valutare e verificare le prestazioni del personale cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Dettagli importanti crescita del pool](#important-details-about-threadpool-growth)
-   [Abilitare server catalogo globale ottenere ulteriori velocità sul client quando si utilizza StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Monitoraggio e risoluzione dei problemi domande frequenti

Domande frequenti in questa sezione coprono il monitoraggio e risoluzione dei problemi relativi a domande comuni. Per ulteriori informazioni sul monitoraggio e risoluzione dei problemi le istanze di Azure Redis Cache, vedere [come eseguire il monitoraggio della Cache Redis Azure](cache-how-to-monitor.md) e su [come risolvere i problemi di Azure Redis Cache](cache-how-to-troubleshoot.md).

-   [Come monitorare l'integrità e le prestazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [La cache diagnostica archiviazione impostazioni dell'account modificate, cosa è successo?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Perché è abilitata la diagnostica per alcune nuove cache, ma non altri?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Perché si vedono timeout?](#why-am-i-seeing-timeouts)
-   [Perché i client disconnessa dalla cache?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Domande frequenti offerta Cache precedente

-   [Offerta migliore Cache Azure è appropriata?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>Che cos'è Azure Redis Cache?

Azure Cache Redis basata sull'origine di Apri popolari [Redis cache](http://redis.io). Consente l'accesso a una sicura e dedicata Redis cache, gestita da Microsoft e accessibile da qualsiasi applicazione all'interno di Azure. Per informazioni più dettagliate, vedere la pagina di prodotto [Azure Redis Cache](https://azure.microsoft.com/services/cache/) nella Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Come è possibile iniziare con Azure Redis Cache?

Esistono diversi modi per iniziare con Azure Redis Cache.

-    È possibile estrarre una le esercitazioni disponibili per [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node](cache-nodejs-get-started.md)e [Python](cache-python-get-started.md). 
-    È possibile guardare [come compilare le prestazioni App utilizzando Microsoft Azure Redis Cache alta](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    È possibile estrarre la documentazione di client per i client che corrisponde alla lingua di sviluppo del progetto per informazioni su come usare Redis. Sono disponibili molti client Redis che possono essere usate con Azure Redis Cache. Per un elenco di clienti Redis, vedere [http://redis.io/clients](http://redis.io/clients).


Se si dispone già di un account Azure, è possibile:

-    [Aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). È possibile ottenere crediti che possono essere utilizzati per provare a utilizzare i servizi di Azure a pagamento. Anche dopo il crediti vengono utilizzati, è possibile mantenere l'account e usare le caratteristiche e servizi Azure gratuiti.
-    [Vantaggi dell'abbonato attivare Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). L'abbonamento MSDN offre crediti ogni mese che è possibile utilizzare per i servizi di Azure a pagamento.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Quali offerta Redis Cache e le dimensioni da utilizzare
Ogni Azure Redis Cache offre diversi livelli di **dimensioni**, **la larghezza di banda**, **disponibilità**e opzioni **contratto di servizio** .

Di seguito sono considerazioni per la scelta di un'offerta di Cache.

-   **Memoria**: livelli Basic e Standard offrono 250 MB-53 GB. Il livello di Premium offre fino a 530 GB con non è più disponibile [su richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per ulteriori informazioni, vedere [Azure Redis Cache prezzi](https://azure.microsoft.com/pricing/details/cache/).
-   **Le prestazioni di rete**: se si dispone di un carico di lavoro che richiede alta velocità livello Premium offre maggiore rispetto a Standard o di base. All'interno di ogni livello cache di dimensioni più grandi sono disponibili anche altre della larghezza di banda a causa di macchina virtuale sottostante che ospita la cache. Vedere la [tabella seguente](#cache-performance) per ulteriori informazioni.
-   **Produttività**: livello di Premium offre la massima produttività disponibili. Se il server di cache o il client raggiunge i limiti della larghezza di banda, si riceverà timeout sul lato client. Per ulteriori informazioni, vedere la tabella seguente.
-   **Alta disponibilità/contratto di servizio**: Azure Redis Cache garantisce che una cache Standard/Premium saranno disponibile almeno 99,9% del tempo. Per ulteriori informazioni sul contratto di servizio, vedere [Azure Redis Cache prezzi](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Il contratto di servizio copre solo la connettività per i punti finali della Cache. Il contratto di servizio non viene illustrata la protezione dalla perdita di dati. È consigliabile utilizzare la funzione di persistenza dati Redis nel livello Premium per aumentare flessibilità perdite di dati.
-   **Redis persistenza dei dati**: livello di Premium consente di mantenere i dati della cache in un account di archiviazione Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di infrastruttura sottostante sono problemi possono essere possibile perdita di dati. È consigliabile utilizzare la funzione di persistenza dati Redis nel livello Premium per aumentare flessibilità perdite di dati. Azure Redis Cache offre RDB e AOF (disponibile a breve) opzioni nella persistenza Redis. Per ulteriori informazioni, vedere [come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md).
-   **Redis Cluster**: per creare cache maggiore di 53 GB o ai dati condiviso tra più nodi Redis, è possibile utilizzare il servizio cluster Redis, che è disponibile nel livello Premium. Ogni nodo è costituito da una coppia di cache principale/replica disponibilità elevata. Per ulteriori informazioni, vedere [come configurare il servizio cluster per una Redis Cache Premium Azure](cache-how-to-premium-clustering.md).
-   **Isolamento di rete e protezione avanzata**: la distribuzione di Azure virtuale rete (VNET) offre maggiore sicurezza e isolamento per Azure Redis Cache, nonché subnet, criteri di controllo di accesso, e altre funzionalità possono ulteriormente limitare l'accesso. Per ulteriori informazioni, vedere [come configurare il supporto di rete virtuale per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md).
-   **Configurare Redis**: In livelli Standard e Premium, è possibile configurare Redis per le notifiche di spazio delle chiavi.
-   **Numero massimo di connessioni client**: livello di Premium offre il numero massimo di client in grado di connettersi a Redis con un numero elevato di connessioni per cache di dimensioni più grandi. [Effettuare fare riferimento alla pagina prezzo per informazioni dettagliate](https://azure.microsoft.com/pricing/details/cache/).
-   **Principali dedicato per Server Redis**: nel livello Premium tutte le dimensioni della cache dispone di un core dedicato per Redis. In base/Standard livelli C1 e disporre sopra un core dedicato per Redis server.
-   **Redis è thread singolo** in modo che più di due core non fornisce vantaggio su con solo due core, ma di dimensioni più grandi macchine Virtuali sono in genere larghezza di banda maggiori di piccole dimensioni. Se il server di cache o il client raggiunge i limiti della larghezza di banda, si riceverà timeout sul lato client.
-   **Miglioramenti delle prestazioni**: cache nel livello Premium vengono distribuite su hardware che dispone di processori più veloci e offre migliori prestazioni rispetto ai livelli Basic o Standard. Cache di livello Premium hanno effettiva superiore e inferiore latenza.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Prestazioni Redis Cache Azure

La tabella seguente mostra i valori massimo della larghezza di banda osservati durante la verifica di varie dimensioni dello Standard e Premium memorizza utilizzando `redis-benchmark.exe` da un VM Iaas contro l'endpoint di Azure Redis Cache. Si noti che questi valori non vengono garantiti e non esiste alcun contratto di servizio per questi numeri, ma dovrebbe essere tipico. È necessario caricare testare l'applicazione per determinare le dimensioni della cache appropriata per l'applicazione.

Da questa tabella è possibile trarre conclusioni seguenti.

-   Velocità nelle cache con le stesse dimensioni è superiore al livello Premium rispetto al livello Standard. Ad esempio, con una Cache GB 6, velocità di P1 è 140K RPS rispetto a 49 K per C3.
-   Con Redis cluster, velocità aumenta in modo lineare si aumenta il numero di shards (nodi) del cluster. Ad esempio, se si crea un cluster di P4 di 10 shards, quindi la velocità disponibile è 250K * 10 = 2,5 milioni RPS.
-   Velocità di grande dimensioni chiave è superiore al livello Premium rispetto al livello Standard.

| Prezzi livello             | Dimensioni   | Core CPU | Larghezza di banda disponibile                                    | Dimensioni chiave 1 KB                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Dimensioni della cache standard** |        |           | **Megabit al secondo (Mb/s) / megabyte (MB/s) secondo** | **Richieste al secondo**            |
| C0                       | 250 MB | Condiviso    | 5 / 0.625                                              | 600                                      |
| C1                       | ALMENO 1 GB   | 1         | 100 / 12,5                                             | 12200                                    |
| CELLA C2                       | 2,5 GB | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62,5                                             | 61000                                    |
| C5                       | GB 26  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | GB 53  | 8         | 2000 / 250                                             | 150000                                   |
| **Dimensioni della cache Premium**  |        | **Core CPU per condiviso**  |                                         | **Richieste al secondo, per condiviso** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | GB 26  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | GB 53  | 8         | 4000 / 500                                             | 250000                                   |


Per istruzioni su come scaricare gli strumenti di Redis ad esempio `redis-benchmark.exe`, vedere la [come è possibile eseguire i comandi Redis?](#cache-commands) sezione.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>In quale area è utile individuare la cache?

Per ottimizzare le prestazioni e più bassa latenza, individuare la Cache di Azure Redis nella stessa regione applicazione client cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Come si fatturati Azure Redis Cache?

Prezzi Redis Cache Azure è [qui](https://azure.microsoft.com/pricing/details/cache/). La pagina prezzo Elenca i prezzi come una tariffa oraria. Cache sono effettuate in base al minuto dal momento che la cache viene creata fino alla volta che viene eliminata una cache. Opzione non è disponibile per interrompere o sospendere la fatturazione di una cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>È possibile usare Azure Redis Cache con Azure Government Cloud o Azure Cina Cloud?

Sì, Azure Redis Cache è disponibile nel Cloud per enti pubblici Azure e Azure Cina Cloud. Si noti che l'URL per l'accesso e gestione Cache Redis Azure sono diversi in Azure Government Cloud e Azure Cina Cloud confrontati con Cloud pubblica Azure. Per ulteriori informazioni sull'utilizzo delle Cache Redis Azure con Azure Government Cloud e Azure Cina Cloud, vedere [Azure per la pubblica amministrazione database - Cache Redis Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache) e [Azure Cina Cloud - Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/).

Per informazioni sull'utilizzo della Cache Redis Azure con PowerShell nel Cloud per enti pubblici Azure e Azure Cina Cloud, vedere [connessione al Cloud per enti pubblici Azure o Azure Cina Cloud](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>In che modo le opzioni di configurazione StackExchange.Redis?

StackExchange.Redis sono molte opzioni. In questa sezione parla alcune impostazioni comuni. Per informazioni più dettagliate sulle opzioni di StackExchange.Redis, vedere [configurazione di StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descrizione|Suggerimento
---|---|---
AbortOnConnectFail|Quando è impostato su true, la connessione non sarà riconnettersi dopo un errore di rete.|Impostare su false e consentire StackExchange.Redis riconnettersi automaticamente.
ConnectRetry|Connessione il numero di ripetizioni tentativi di connessione durante l'iniziale.| Vedere le note per indicazioni seguenti. |
ConnectTimeout|Timeout in millisecondi per operazioni di connessione.| Vedere le note per indicazioni seguenti. |

Nella maggior parte dei casi sono sufficienti i valori predefiniti del client. È possibile ottimizzare le opzioni in base a carico di lavoro.

-   **Tentativi**
    -   Per ConnectRetry e ConnectTimeout le indicazioni generali consiste nell'avere esito negativo rapidamente e riprova. Questo è basato il carico di lavoro e la quantità di tempo nella Media accetta per i client per eseguire un comando Redis e ricevere una risposta.
    -   Consentire StackExchange.Redis riconnettersi automaticamente invece di verifica dello stato di connessione e riconnessione se stessi. **Evitare l'utilizzo della proprietà ConnectionMultiplexer.IsConnected**.
    -   Snowballing - talvolta potrebbe verificarsi un problema nel punto in cui riprovare e ciò snowballs e mai recupera. In questo caso è necessario prendere in considerazione utilizzando un algoritmo di Riprova backoff esponenziale, come descritto in [tentativi indicazioni generali](best-practices-retry-general.md) pubblicato dal gruppo di Microsoft Patterns & Practices.
-   **Valori di timeout**
    -   Valutare la possibilità di carico di lavoro e impostare i valori di conseguenza. Se si archiviano i valori di grandi dimensioni, impostare il timeout su un valore superiore.
    -   Impostare `AbortOnConnectFail` a StackExchange.Redis false e consentire riconnettersi automaticamente.
    -   Usare una sola istanza di ConnectionMultiplexer per l'applicazione. È possibile utilizzare un LazyConnection per creare una singola istanza restituita da una proprietà di connessione, come illustrato in [connessione alla cache utilizzando la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Impostare il `ConnectionMultiplexer.ClientName` proprietà su un nome univoco istanza di app per diagnostica.
    -   Utilizzare più `ConnectionMultiplexer` istanze per carichi di lavoro personalizzati.
    -   Se si dispone di carico variabile nell'applicazione, è possibile seguire questo modello. Per esempio:
    -   È possibile inserire un multiplexer per gestire le chiavi di grandi dimensioni.
    -   È possibile inserire un multiplexer per la gestione di tasti piccoli.
    -   È possibile impostare valori diversi per timeout di connessione e riprovare logica per ogni ConnectionMultiplexer in uso.
    -   Impostare il `ClientName` proprietà ciascuno multiplexer per agevolare la diagnostica.
    -   Questo può provocare più semplice latenza per `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>I client di cache Redis è possibile usare?

Uno dei vantaggi delle Redis è sono presenti molti client che supportano più lingue diverse sviluppo. Per un elenco corrente dei clienti, vedere [Redis client](http://redis.io/clients). Per esercitazioni che illustrate diverse lingue diverse e clienti, informazioni su [come utilizzare Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e fare clic sulla lingua desiderata del selettore di lingua nella parte superiore dell'articolo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Esiste un emulatore per Azure Redis Cache locale?

Non esiste alcun emulatore per Azure Redis Cache locale, ma è possibile eseguire la versione MSOpenTech di redis server.exe da [Redis strumenti riga di comando](https://github.com/MSOpenTech/redis/releases/) nel computer locale e accedervi per ottenere un'esperienza simile a un emulatore cache locale, come illustrato nell'esempio seguente.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


È possibile configurare un file di [redis.conf](http://redis.io/topics/config) per adattarli le [impostazioni della cache predefinita](cache-configure.md#default-redis-server-configuration) per la Cache di Redis Azure online se lo si desidera.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Come è possibile eseguire i comandi Redis?

È possibile usare uno dei comandi elencati [Redis comandi](http://redis.io/commands#) ad eccezione dei comandi elencati [Redis comandi non supportati in Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Per eseguire comandi Redis sono disponibili diverse opzioni.

-   Se si dispone di una cache Standard o Premium, è possibile eseguire i comandi Redis tramite la [Console Redis](cache-configure.md#redis-console). In questo modo sicuro per eseguire i comandi Redis nel portale di Azure.
-   È anche possibile utilizzare gli strumenti di riga di comando Redis. A questo scopo, eseguire i passaggi seguenti.
-   Scaricare [Redis strumenti riga di comando](https://github.com/MSOpenTech/redis/releases/).
-   Connettersi alla cache utilizzando `redis-cli.exe`. Passare cache endpoint utilizzando che il -h passare e il tasto tramite-come illustrato nell'esempio seguente.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Si noti che gli strumenti di riga di comando Redis non funzionano con la porta SSL, ma è possibile utilizzare un'utilità, ad esempio `stunnel` per garantire una connessione sicura gli strumenti per la porta SSL seguendo le indicazioni sul post di blog [Annuncio Provider dello stato sessione ASP.NET Redis versione Preview di](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Perché non è presente un riferimento di libreria di classi MSDN come parte di altri servizi Azure in Azure Redis Cache?

Microsoft Azure Redis Cache si basa sulla fonte Apri popolari Redis Cache e sono accessibili da una vasta gamma di [Redis client](http://redis.io/clients) disponibili per molti linguaggi di programmazione. Ogni cliente ha la propria API che effettua chiamate all'istanza della cache Redis utilizzando [Redis comandi](http://redis.io/commands).

Poiché ogni cliente è diverso, non è non un riferimento di classe centralizzato su MSDN; ogni client mantiene invece della propria documentazione di riferimento. Oltre la documentazione di riferimento, sono disponibili varie esercitazioni che mostra come iniziare a utilizzare Azure Redis Cache con lingue diverse e client di cache. Per accedere a queste esercitazioni, vedere [come utilizzare Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e fare clic sulla lingua desiderata del selettore di lingua nella parte superiore dell'articolo.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>È possibile usare Azure Redis Cache come cache sessione PHP?

Sì, per utilizzare Azure Redis Cache come cache sessione PHP, specificare la stringa di connessione all'istanza di Azure Redis Cache in `session.save_path`.

>[AZURE.IMPORTANT] Quando si usa Azure Redis Cache come cache sessione PHP, è necessario URL codificare la chiave di protezione usata per connettersi alla cache, come illustrato nell'esempio seguente.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Se la chiave non è codificata URL, potrebbe essere visualizzato un'eccezione simile al seguente:`Failed to parse session.save_path`

Per ulteriori informazioni sull'utilizzo della Cache Redis come cache sessione PHP con il client PhpRedis, vedere [gestore PHP sessione](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando è necessario attivare la porta non SSL per la connessione a Redis?

Redis server non supporta SSL all'esterno della casella, a differenza Azure Redis Cache. Se si è connessi alla Cache Redis Azure e il client supporta SSL, ad esempio StackExchange.Redis, è necessario utilizzare SSL.

Si noti che la porta SSL non è disattivata per impostazione predefinita per le nuove istanze di Azure Redis Cache. Se il client non supporta SSL, è necessario abilitare la porta non SSL seguendo le indicazioni disponibili nella sezione [accesso a porte](cache-configure.md#access-ports) dell'articolo [Configura una cache nella Cache Redis Azure](cache-configure.md) .

Redis strumenti quali `redis-cli` non funzionano con la porta SSL, ma è possibile utilizzare un'utilità, ad esempio `stunnel` per garantire una connessione sicura gli strumenti per la porta SSL seguendo le indicazioni sul post di blog [Annuncio Provider dello stato sessione ASP.NET Redis versione Preview di](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Per istruzioni su come scaricare gli strumenti di Redis, vedere la [come è possibile eseguire i comandi Redis?](#cache-commands) sezione.



### <a name="what-are-some-production-best-practices"></a>Quali sono le procedure consigliate produzione?

-   [Procedure consigliate StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Concetti e configurazione](#configuration-and-concepts)
-   [Verifica delle prestazioni](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Procedure consigliate StackExchange.Redis

-   Impostare `AbortConnect` su false, quindi consentire la ConnectionMultiplexer riconnettersi automaticamente. [Per informazioni dettagliate, vedere di seguito](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Riutilizzare il ConnectionMultiplexer - non crearne uno nuovo per ogni richiesta. Il `Lazy<ConnectionMultiplexer>` si consiglia di motivo [illustrato di seguito](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) .
-   Redis funziona meglio con valori più piccoli, pertanto è consigliabile la tritatura i dati più grandi in più tasti. In [questo Redis discussione](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb è considerato "grande". Leggere [questo articolo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.
-   Configurare le [impostazioni del pool](#important-details-about-threadpool-growth) per evitare timeout.
-   Usare almeno connectTimeout predefinito di 5 secondi. Avrebbe così tempo StackExchange.Redis di ristabilire la connessione, in caso di un acustico di rete.
-   Prestare particolare attenzione dei costi delle prestazioni associati a diverse operazioni che è in esecuzione. Ad esempio, il `KEYS` comando è un'operazione o ed evitare. Il [sito redis.io](http://redis.io/commands/) con dettagli intorno complessità volta per ogni operazione che supporta. Fare clic su ogni comando per visualizzare la complessità per ogni operazione.

#### <a name="configuration-and-concepts"></a>Concetti e configurazione

-   Utilizzare Standard o livello Premium per sistemi di produzione. Il livello di base è un sistema singolo nodo con alcuna replica di dati e non contratto di servizio. Inoltre, usare almeno una cache C1. Cache C0 sono destinate per gli scenari di sviluppo/test semplice.
-   Tenere presente che Redis un archivio di dati **In memoria** . Leggere [questo articolo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , in modo che siano a conoscenza scenari in cui possono verificarsi perdite di dati.
-   Sviluppare il sistema in modo che possa gestire connessione blips [a causa di failover e patch](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Verifica delle prestazioni

-   Avviare utilizzando `redis-benchmark.exe` per avere un'idea di velocità possibile prima della scrittura personalizzate delle prestazioni test. Si noti che benchmark redis non supporta SSL, in modo che hanno per [abilitare la porta SSL Non tramite il portale di Azure](cache-configure.md#access-ports) prima di eseguire il test. Per esempi, vedere [come benchmark e verificare le prestazioni del personale cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   Il client macchine Virtuali utilizzate per la verifica dovrebbe essere nella stessa regione l'istanza della cache Redis.
-   È consigliabile usare Dv2 macchine Virtuali serie per il client come quelli che hanno componenti hardware migliori e per ottenere risultati ottimali.
-   Verificare che il client macchine Virtuali si sceglie abbia almeno molto elaborazione e la funzionalità di larghezza di banda come la cache si stanno verificando. 
-   Se si utilizza Windows, abilitare VRSS nel computer client. [Per informazioni dettagliate, vedere di seguito](https://technet.microsoft.com/library/dn383582.aspx).
-   Le istanze di Redis livello Premium verranno dispone di rete migliore latenza e la velocità effettiva perché sono in esecuzione su hardware migliori per CPU e di rete.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quali sono alcune delle considerazioni quando si utilizzano i comandi comuni Redis?

-   Non è necessario eseguire alcuni comandi Redis che richiederà molto tempo per completare senza valutare l'impatto di questi comandi.
    -   Ad esempio, non eseguire il comando di [tasti di](http://redis.io/commands/keys) produzione come possono essere molto tempo per tornare a seconda del numero di tasti. Redis è un server multithread ed elabora i comandi uno alla volta. Se si dispone di altri comandi rilasciati dopo tasti, che non essere elaborati finché non Redis elabora il comando di tasti. Il [sito redis.io](http://redis.io/commands/) con dettagli intorno complessità volta per ogni operazione che supporta. Fare clic su ogni comando per visualizzare la complessità per ogni operazione.
-   Le dimensioni delle chiavi - è opportuno usare tasto/valori più bassi o valori/della chiave di grandi dimensioni? In generale dipende lo scenario. Se lo scenario richiede chiavi quindi regolare ConnectionTimeout e riprovare a valori e regolare la logica di ripetizione. Dal punto di vista server Redis, i valori più bassi osservano per migliorare le prestazioni.
-   Questo significa che è possibile memorizzare valori più grandi in Redis; è necessario tenere presenti le considerazioni seguenti. Latenza sarà più in alto. Se si dispone di una serie di dati di dimensioni maggiori e quella inferiore, è possibile utilizzare più istanze di ConnectionMultiplexer, ognuno configurato con un diverso set di valori di timeout e riprovare, come descritto nella sezione precedente [cosa le opzioni di configurazione StackExchange.Redis fare](#cache-configuration) .



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Come è possibile valutare e verificare le prestazioni del personale cache?

-   [Attivare la diagnostica cache](cache-how-to-monitor.md#enable-cache-diagnostics) in modo da poter [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure ed è inoltre possibile [scaricare e rivedere](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante gli strumenti di propria scelta.
-   È possibile utilizzare redis benchmark.exe per caricare test del server Redis.
-   Assicurarsi che il carico test client e la cache di Redis nella stessa regione.
-   Usare redis cli.exe e controllare la cache utilizzando il comando INFO.
-   Se il carico provoca frammentazione della memoria alta quindi necessario le dimensioni di una dimensione maggiore per cache.
-   Per istruzioni su come scaricare gli strumenti di Redis, vedere la [come è possibile eseguire i comandi Redis?](#cache-commands) sezione.

Di seguito è illustrato un esempio dell'uso redis benchmark.exe. Per ottenere risultati precisi, eseguire il comando da una macchina virtuale nella stessa regione la cache.

-   Richieste in pipeline SET di test con un payload k 1

    -h redis benchmark.exe **yourcache**. redis.cache.windows.net- **yourAccesskey** -t imposta - n di Security 1000000 - Security d 1024 - P 50
    
-   Test in pipeline ottenere richieste che utilizzano un payload k 1. 
    Nota: Eseguire il SET di test illustrato in precedenza prima di tutto per popolare cache
    
    -h redis benchmark.exe **yourcache**. redis.cache.windows.net - **yourAccesskey** -t GET -n 1000000 - d 1024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Dettagli importanti crescita del pool

Thread CLR sono disponibili due tipi di thread - "Lavoro" e "I/o porta di completamento" (o IOCP) thread. 

-   Vengono utilizzati quando per attività come elaborazione `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)` metodi. Questi thread utilizzati anche da diversi componenti di CLR quando si verificano nel thread in background è lavoro.
-   IOCP vengono utilizzati in caso di IO asincrono (ad esempio leggere dalla rete). 

Pool di thread fornisce nuovi thread di lavoro o thread di completamento i/o su richiesta (senza qualsiasi limitazione) fino a raggiungere l'impostazione "Minimo" per ogni tipo di thread. Per impostazione predefinita, il numero minimo di thread è impostato per il numero di processori in un sistema. 

Dopo il numero di esistente (non disponibile) thread accessi che numero "minimo" di thread, il thread verrà rallentare la frequenza con cui si inserisce nuovi thread a un thread per ogni 500 millisecondi. Questo errore indica che se il sistema è un burst di lavoro che richiedono un thread IOCP, elaborerà compatibili molto rapidamente. Tuttavia, se il burst del lavoro è maggiore rispetto all'impostazione "Minimo" configurato, si verificherà un alcune Ritardo elaborazione parte del lavoro mentre il thread di attesa per uno dei due operazioni da eseguire.

1. Un thread esistente diventa disponibile per l'elaborazione di lavoro.
1. Nessun thread esistente diventa disponibile per 500 ms, in modo che viene creato un nuovo thread.

In pratica, significa che quando il numero di thread occupati è maggiore di thread le funzioni Min, sono pagamento probabilmente un ritardo di 500 ms prima che il traffico di rete è elaborato dall'applicazione. Inoltre, è importante tenere presente che quando un thread esistente rimane inattivo per più di 15 secondi (in base a quali ricordare), verrà pulito e ripetere il ciclo di crescita e ritiro.

Se si osserva il messaggio di errore di esempio da StackExchange.Redis (build 1.0.450 o versioni successive), si noterà che venga stampato ora le statistiche del pool (vedere informazioni dettagliate su IOCP e lavoro riportata di seguito).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Nell'esempio precedente, è possibile visualizzare che per thread IOCP esistono 6 thread occupati e il sistema è configurato per consentire a 4 thread minimo. In questo caso, il client sarebbe probabilmente verificato due 500 ms ritardi perché 6 > 4.

Si noti che StackExchange.Redis possibile raggiunto timeout se crescita di IOCP o lavoro thread ottiene limitata.

### <a name="recommendation"></a>Suggerimento

Con queste informazioni, è consigliabile che i clienti impostano il valore minimo di configurazione per IOCP e thread di lavoro su un valore maggiore del valore predefinito. È Impossibile inviare giusto indicazioni su cosa questo valore deve essere perché il valore corretto per un'applicazione sarà troppo alta/bassa per un'altra applicazione. Questa impostazione può influire sulle prestazioni di altre parti di applicazioni complicate, anche in modo che ogni cliente deve ottimizzare questa impostazione in base a specifiche esigenze. Un ottimo punto di partenza è 200 o 300, quindi verificare e modificare in base alle esigenze.

Procedura per configurare questa impostazione:

-   In ASP.NET, utilizzare l' [impostazione di configurazione "minIoThreads"][] nel `<processModel>` elemento di configurazione in config. Se si esegue all'interno di siti Web di Azure, questa impostazione non viene esposto tramite le opzioni di configurazione. Tuttavia, sarà comunque possibile impostare il livello di programmazione (vedere di seguito) dal metodo Application_Start in global.asax.cs.

> **Nota importante:** il valore specificato in questo elemento di configurazione è un'impostazione *- core* . Ad esempio, se si dispone di un computer 4 core e si desidera l'impostazione minIOThreads 200 in fase di esecuzione, utilizzare `<processModel minIoThreads="50"/>`.

-   All'esterno di ASP.NET, utilizzare [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Abilitare server catalogo globale ottenere ulteriori velocità sul client quando si utilizza StackExchange.Redis

Abilitazione del catalogo globale server può ottimizzare il client e fornire velocità e prestazioni migliori quando si utilizza StackExchange.Redis. Per ulteriori informazioni sul server catalogo globale e su come abilitarla, vedere gli articoli seguenti.

-   [Per abilitare server catalogo globale](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Nozioni fondamentali di simultanea](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Prestazioni e simultanea](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Come monitorare l'integrità e le prestazioni della cache?

Nel [portale di Azure](https://portal.azure.com), è possono monitorare le istanze di Microsoft Azure Redis Cache. È possibile visualizzare metriche, aggiungere grafici metriche per il Startboard, personalizzare l'intervallo di data e l'ora di monitoraggio grafici, aggiungere e rimuovere i tipi di grafici metriche e impostare gli avvisi quando vengono soddisfatte determinate condizioni. Per ulteriori informazioni, vedere [Monitor Azure Redis Cache](cache-how-to-monitor.md).

La sezione **supporto + risoluzione dei problemi** della stessa e Redis Cache **Impostazioni** contiene anche diversi strumenti per il monitoraggio e risoluzione dei problemi delle cache. 

-   **Risoluzione dei problemi** di vengono fornite informazioni comuni problemi e strategie per risolverli.
-   **Log di controllo** vengono fornite informazioni sulle azioni eseguite sulla cache. È anche possibile usare il filtro per espandere la visualizzazione in modo da includere altre risorse.
-   **Risorsa salute** espressioni di controllo la risorsa e indica se è in esecuzione come previsto. Per ulteriori informazioni sul servizio di integrità di Azure risorse, vedere [Panoramica dell'integrità della risorsa Azure](../resource-health/resource-health-overview.md).
-   **Nuova richiesta di assistenza** , saranno disponibili opzioni per aprire una richiesta di assistenza per la cache.

Questi strumenti consentono di controllare lo stato delle istanze di Azure Redis Cache e consentono di gestire le applicazioni di memorizzazione nella cache. Per ulteriori informazioni, vedere [supporto e risoluzione dei problemi di impostazioni](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>La cache diagnostica archiviazione impostazioni dell'account modificate, cosa è successo?

Cache nella stessa regione e sottoscrizione condividono le stesse impostazioni di archiviazione di diagnostica e se la configurazione è modificata (diagnostica attivazione/disattivazione o la modifica dell'account di archiviazione) it si applica a tutte le cache nella sottoscrizione in tale area. Se sono state modificate le impostazioni di diagnostica per la cache, verificare se le impostazioni di diagnostiche per un'altra cache nello stesso abbonamento e opzioni internazionali sono cambiati. Un modo per verificarlo consiste nel visualizzare il log di controllo per la cache per un `Write DiagnosticSettings` evento. Per ulteriori informazioni sull'utilizzo dei registri di controllo, vedere [visualizzare gli eventi e registri di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md) e [controllare le operazioni con Gestione risorse](../resource-group-audit.md). Per ulteriori informazioni sul monitoraggio eventi Azure Redis Cache, vedere [operazioni e gli avvisi](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Perché è abilitata la diagnostica per alcune nuove cache, ma non altri?

Cache nella stessa area geografica e sottoscrizione condividono le stesse impostazioni di archiviazione di diagnostica. Se si crea una nuova cache nello stesso abbonamento come un'altra cache contenente diagnostica abilitata e area geografica, diagnostica è attivata la nuova cache utilizzando le stesse impostazioni.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Perché si vedono timeout?

Timeout verificarsi nel client che consente di comunicare con Redis. La maggior parte dei casi Redis server non ha timeout. Quando un comando viene inviato al server Redis, il comando è messe e Redis server non hanno un limite riceve il comando e viene eseguito. Tuttavia il client può timeout durante l'operazione e in caso affermativo un'eccezione viene generato sul lato chiamante. Per ulteriori informazioni sulla risoluzione dei problemi di timeout, vedere [risoluzione dei problemi sul lato Client](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [StackExchange.Redis timeout eccezioni](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Perché i client disconnessa dalla cache?

Di seguito sono motivi comuni per una disconnessione cache.

-   Cause lato client
    -   È stato ridistribuito l'applicazione client.
    -   L'applicazione client eseguita un'operazione di ridimensionamento.
        -   Nel caso dei servizi Cloud o Web Apps, problema potrebbe dipendere il ridimensionamento automatico.
    -   Modifica il livello di rete sul lato client.
    -   Temporaneo errori nel client o in nodi di rete tra il client e server.
    -   Sono stati raggiunti i limiti di soglia della larghezza di banda.
    -   Operazioni associate alla CPU ha richiesto troppo tempo.
-   Cause sul lato server
    -   Sulla cache standard che offre il servizio Cache Redis Azure avviato un sopra fail dal nodo primario per il nodo secondario.
    -   Azure stato patch istanza nel punto in cui è stata distribuita la cache
        -   Può trattarsi di Redis server aggiornamenti o la manutenzione di macchine Virtuali generale.







### <a name="which-azure-cache-offering-is-right-for-me"></a>Offerta migliore Cache Azure è appropriata?

>[AZURE.IMPORTANT]Secondo anno scorso [annuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), ritiro servizio servizio Cache gestite Azure e Azure In ruolo Cache su 30 novembre 2016. È consigliabile utilizzare [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Per informazioni sulla migrazione, vedere [eseguire la migrazione dal servizio di Cache gestita da Azure Redis memorizzare nella Cache](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Cache Redis Azure
Azure Cache Redis è generalmente disponibile in dimensioni fino a 53 GB e una contratto di servizio del 99,9% di disponibilità sono previsti. Il nuovo [livello di premium](cache-premium-tier-intro.md) offre dimensioni fino a 530 GB e il supporto per il raggruppamento, VNET e persistenza, con un contratto di servizio 99,9%.

Azure Cache Redis offre la possibilità di utilizzare una cache Redis sicura e dedicata, gestita da Microsoft. Con questa offerta, è possibile sfruttare le ampia gamma di funzionalità ed ecosistema forniti da Redis, affidabile hosting e il monitoraggio da Microsoft.

Diversamente da quanto succede cache tradizionale che gestiscono solo coppie di parole chiave valore, Redis è diffuso per tipi di dati altamente efficienti. Redis anche supporta l'esecuzione di operazioni atomiche in questi tipi, ad esempio aggiungendo a una stringa. aumentare il valore in un hash; inserimento di un elenco. elaborazione set di intersezione, unione e differenza; o fornire il membro con valutazione più alto in un set ordinato. Altre caratteristiche includono il supporto per le transazioni, pub/sub, Lua esecuzione script, le chiavi con limitata alla durata e le impostazioni di configurazione per rendere Redis comportano più come una cache tradizionale.

Un altro aspetto chiave per il successo Redis è ecosistema integro e Apri origine integrato attorno a essa. Questo viene riflesso nei vari client Redis disponibile in più lingue. In questo modo possono essere utilizzate da quasi tutti carico di lavoro create all'interno di Azure. 

Per ulteriori informazioni sulla Guida introduttiva di Azure Redis Cache, vedere [come utilizzare Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) e [la documentazione di Azure Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Servizio Cache gestito
[Gestite Cache servizio è impostato per essere ritirata 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>In ruolo Cache
[Cache in ruolo è impostata per essere ritirata 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[impostazione di configurazione "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
