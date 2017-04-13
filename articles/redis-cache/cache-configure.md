<properties 
    pageTitle="Come configurare Azure Redis Cache | Microsoft Azure"
    description="Comprendere la configurazione di Redis predefinita per Azure Redis Cache e informazioni su come configurare le istanze di Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Come configurare Azure Redis Cache

In questo argomento viene descritto come visualizzare e aggiornare la configurazione per le istanze di Azure Redis Cache e illustra la configurazione del server Redis predefinita per le istanze di Azure Redis Cache.

>[AZURE.NOTE] Per ulteriori informazioni sulla configurazione e utilizzo delle funzionalità di cache premium, vedere [come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md), [come configurare il servizio cluster per una Cache di Redis Azure Premium](cache-how-to-premium-clustering.md)e [come configurare il supporto di rete virtuale per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Configurare le impostazioni della cache Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache Redis fornisce le seguenti impostazioni in e **l'Impostazioni** .

![Redis le impostazioni della Cache](./media/cache-configure/redis-cache-settings.png)



-   [Supporto tecnico e risoluzione dei problemi di impostazioni](#support-amp-troubleshooting-settings)
-   [Impostazioni generali](#general-settings)
    -   [Proprietà](#properties)
    -   [Tasti di scelta](#access-keys)
    -   [Impostazioni avanzate](#advanced-settings)
    -   [Redis Advisor Cache](#redis-cache-advisor)
-   [Impostazioni di scala](#scale-settings)
    -   [Prezzi livello](#pricing-tier)
    -   [Redis dimensioni cluster](#cluster-size)
-   [Impostazioni di gestione dei dati](#data-management-settings)
    -   [Redis persistenza dei dati](#redis-data-persistence)
    -   [Importazione/esportazione](#importexport)
-   [Impostazioni di amministrazione](#administration-settings)
    -   [Riavviare il computer](#reboot)
    -   [Pianificare gli aggiornamenti](#schedule-updates)
-   [Impostazioni di diagnostica](#diagnostics-settings)
-   [Impostazioni di rete](#network-settings)
-   [Impostazioni di gestione delle risorse](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Supporto tecnico e risoluzione dei problemi di impostazioni

Le impostazioni nella sezione **supporto + risoluzione dei problemi** forniscono le opzioni per la risoluzione di problemi con la cache.

![Supporto + risoluzione dei problemi](./media/cache-configure/redis-cache-support-troubleshooting.png)

Fare clic su **diagnosi e risolvere i problemi** da fornire comuni sui problemi e strategie per risolverli.

Fare clic su **registro attività** per visualizzare le azioni eseguite sulla cache. È anche possibile usare il filtro per espandere la visualizzazione in modo da includere altre risorse. Per ulteriori informazioni sull'utilizzo dei registri di controllo, vedere [visualizzare gli eventi e registri di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md) e [controllare le operazioni con Gestione risorse](../resource-group-audit.md). Per ulteriori informazioni sul monitoraggio eventi Azure Redis Cache, vedere [operazioni e gli avvisi](cache-how-to-monitor.md#operations-and-alerts).

**Risorsa salute** espressioni di controllo la risorsa e indica se è in esecuzione come previsto. Per ulteriori informazioni sul servizio di integrità di Azure risorse, vedere [Panoramica dell'integrità della risorsa Azure](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Dello stato delle risorse è attualmente in grado di report sullo stato di integrità Azure Redis istanze della Cache inserito in una rete virtuale. Per ulteriori informazioni, vedere [tutte le funzionalità di cache funzionano durante l'hosting di una cache in un VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Fare clic su **nuova richiesta di assistenza** per aprire una richiesta di assistenza per la cache.

## <a name="general-settings"></a>Impostazioni generali

Le impostazioni nella sezione **Generale** consentono di accedere e configurare le impostazioni seguenti per la cache.

![Impostazioni generali](./media/cache-configure/redis-cache-general-settings.png)

-   [Proprietà](#properties)
-   [Tasti di scelta](#access-keys)
-   [Impostazioni avanzate](#advanced-settings)
-   [Redis Advisor Cache](#redis-cache-advisor)

### <a name="properties"></a>Proprietà

Fare clic su **proprietà** per visualizzare le informazioni nella cache, incluse le porte ed endpoint cache.

![Redis le proprietà della Cache](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Tasti di scelta

Fare clic su **i tasti di scelta** per visualizzare o rigenerare le chiavi di accesso per la cache. Questi tasti vengono utilizzati insieme al nome host e porte da e il **proprietà** dai client di connessione per la cache.

![Redis Cache i tasti di scelta](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Impostazioni avanzate

Le seguenti impostazioni sono configurate e **l'Impostazioni avanzate** .

-   [Accesso a porte](#access-ports)
-   [Criteri di MaxMemory e maxmemory riservato](#maxmemory-policy-and-maxmemory-reserved)
-   [Notifiche spazio delle chiavi (impostazioni avanzate)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Accesso a porte

Per impostazione predefinita, access non SSL è disattivata per nuove cache. Per abilitare la porta non SSL, fare clic su **No** per **Consenti accesso solo tramite SSL** su **blade impostazioni avanzate** e quindi fare clic su **Salva**.

![Accesso a Cache redis porte](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Criteri di MaxMemory e maxmemory riservato

Le impostazioni **dei criteri Maxmemory** e **riservato maxmemory** e **l'Impostazioni avanzate per** configurano i criteri di memoria per la cache. L'impostazione del **criterio maxmemory** configura i criteri di eliminazione per la cache e **riservato maxmemory** configura la memoria riservata per i processi non cache.

![Redis Cache Maxmemory criteri](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Criteri di MaxMemory** consente di scegliere tra i seguenti criteri di eliminazione.

-   volatili lru - l'impostazione predefinita.
-   AllKeys lru
-   casuale volatili
-   casuale AllKeys
-   ttl volatili
-   noeviction

Per ulteriori informazioni sui criteri di maxmemory, vedere [criteri di eliminazione](http://redis.io/topics/lru-cache#eviction-policies).

L'impostazione **riservato maxmemory** consente di configurare la quantità di memoria in MB riservata al non cache operazioni, ad esempio replica durante il failover. Può essere utilizzato anche quando si dispone di un rapporto di frammentazione elevata. Questo valore consente di avere un'esperienza di server Redis più coerente quando il carico varia. Questo valore deve essere impostato superiore per carichi di lavoro che si scrivono Incolla. Quando una memoria è riservata per tali operazioni non è disponibile per l'archiviazione dei dati memorizzati nella cache.

>[AZURE.IMPORTANT] L'impostazione **riservato maxmemory** è disponibile solo per Standard e le cache Premium.

### <a name="keyspace-notifications-advanced-settings"></a>Notifiche spazio delle chiavi (impostazioni avanzate)

Redis spazio delle chiavi le notifiche configurate e **l'Impostazioni avanzate** . Le notifiche di spazio delle chiavi consentiranno ai client di ricevere notifiche quando si verificano determinati eventi.

![Redis Cache impostazioni avanzata](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notifiche di spazio delle chiavi e l'impostazione di **una notifica eventi di spazio delle chiavi** sono disponibili solo per Standard e le cache Premium.

Per ulteriori informazioni, vedere [Redis spazio delle chiavi notifiche](http://redis.io/topics/notifications). Per esempi di codice, vedere il file [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) nel campione [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Advisor Cache

E il **consigli** Visualizza i suggerimenti per la cache. Durante il normale funzionamento, non vengono visualizzati consigli. 

![Consigli](./media/cache-configure/redis-cache-no-recommendations.png)

Se tutte le condizioni si verificano durante le operazioni della cache, ad esempio l'utilizzo di memoria elevato, la larghezza di banda di rete o carico del server, viene visualizzato un avviso in e il **Redis Cache** .

![Consigli](./media/cache-configure/redis-cache-recommendations-alert.png)

Sono disponibili ulteriori informazioni su e il **consigli** .

![Consigli](./media/cache-configure/redis-cache-recommendations.png)

È possibile monitorare queste metriche sulle sezioni [grafici di monitoraggio](cache-how-to-monitor.md#monitoring-charts) e [l'uso](cache-how-to-monitor.md#usage-charts) della stessa e **Redis Cache** .

Ogni livello prezzo sono previsti limiti diversi per le connessioni client, memoria e della larghezza di banda. Se la cache si avvicina la capacità massima di queste metriche su un periodo di tempo prolungato, viene creato un suggerimento. Per ulteriori informazioni sui limiti esaminati dallo strumento **consigli** metriche, vedere la tabella seguente.

| Redis unità di misura metriche Cache      | Per ulteriori informazioni, vedere                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Utilizzo della larghezza di banda di rete | [Prestazioni della cache - larghezza di banda disponibile](cache-faq.md#cache-performance) |
| Client connessi       | [Configurazione del server di predefinita Redis - maxclients](#maxclients)            |
| Carico del server             | [Grafici di utilizzo - Redis carico del Server](cache-how-to-monitor.md#usage-charts)  |
| Utilizzo della memoria            | [Prestazioni della cache - dimensioni](cache-faq.md#cache-performance)                |

Per aggiornare la cache, fare clic su **Aggiorna** per modificare il [livello di prezzi](#pricing-tier) e ridimensionare la cache. Per ulteriori informazioni sulla scelta di un livello prezzo, vedere [quali offerta Redis Cache e le dimensioni è opportuno usare?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Impostazioni di scala

Le impostazioni nella sezione **scala** consentono di accedere e configurare le impostazioni seguenti per la cache.

![Rete](./media/cache-configure/redis-cache-scale.png)

-   [Prezzi livello](#pricing-tier)
-   [Redis dimensioni cluster](#cluster-size)

### <a name="pricing-tier"></a>Prezzi livello

Fare clic su **livello prezzi** per visualizzare o modificare il livello prezzo per la cache. Per ulteriori informazioni sulle proporzioni dei caratteri, vedere [come scala Azure Redis Cache](cache-how-to-scale.md).

![Redis Cache prezzi livello](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis dimensioni Cluster

Fare clic su **Dimensioni Cluster Redis (PREVIEW)** per modificare le dimensioni di cluster per parziale cache premium con cluster abilitato.

>[AZURE.NOTE] Si noti che mentre il livello di Azure Redis Cache Premium è stato rilasciato alla disponibilità generale, la caratteristica Redis dimensioni Cluster si trova in anteprima.

![Redis dimensioni cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Per modificare le dimensioni di cluster, usare il dispositivo di scorrimento o digitare un numero compreso tra 1 e 10 nella casella di testo **conteggio condiviso** e fare clic su **OK** per salvare.

>[AZURE.IMPORTANT] Redis cluster è disponibile solo per le cache Premium. Per ulteriori informazioni, vedere [come configurare il servizio cluster per una Redis Cache Premium Azure](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Impostazioni di gestione dei dati

Le impostazioni nella sezione **gestione dei dati** consentono di accedere e configurare le impostazioni seguenti per la cache.

![Gestione dei dati](./media/cache-configure/redis-cache-data-management.png)

-   [Redis persistenza dei dati](#redis-data-persistence)
-   [Importazione/esportazione](#importexport)

### <a name="redis-data-persistence"></a>Redis persistenza dei dati

Fare clic su **Redis persistenza dei dati** per abilitare, disabilitare o configurare persistenza dei dati per la cache premium.

![Redis persistenza dei dati](./media/cache-configure/redis-cache-persistence-settings.png)

Per consentire la memorizzazione Redis, fare clic su **attivato** per attivare il backup RDB (Redis database). Per disattivare Redis persistenza, fare clic su **disattivato**.

Per configurare l'intervallo di backup, selezionare una **Frequenza di Backup** dall'elenco a discesa. Le scelte includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore**e **24 ore**. Questo intervallo avvia rovescia dopo il completamento dell'operazione di backup precedente e quando trascorso un nuovo backup viene avviato.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da utilizzare e scegliere la **chiave primaria** o **chiave secondaria** da utilizzare dall'elenco a discesa **Chiave di archiviazione** . È necessario scegliere un account di archiviazione nella stessa regione la cache e un account di **Archiviazione Premium** è consigliabile perché lo spazio di archiviazione premium ha velocità effettiva superiore. Ogni volta che la chiave di archiviazione per l'account persistenza rigenerazione, è necessario scegliere nuovamente il tasto desiderato dall'elenco a discesa **Chiave di archiviazione** .

Fare clic su **OK** per salvare la configurazione di persistenza.

>[AZURE.IMPORTANT] Persistenza dei dati redis è disponibile solo per le cache Premium. Per ulteriori informazioni, vedere [come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importazione/esportazione

Importazione/esportazione è un'operazione di gestione dei dati di Azure Redis Cache che consente di importare i dati nella Cache Redis Azure o esportare dati dalla Cache Redis Azure mediante l'importazione e l'esportazione di uno snapshot Redis Cache Database (RDB) da una cache premium in un blob di pagine in un Account di archiviazione Azure. In questo modo è possibile eseguire la migrazione tra diverse istanze di Azure Redis Cache o popolare la cache con i dati prima di utilizzare.

Importazione può essere utilizzato per visualizzare i file specifici RDB compatibili Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud ambiente o, inclusi Redis eseguono Linux, Windows o un provider del cloud, ad esempio servizi Web di Amazon e gli altri utenti. Importazione di dati è un modo semplice per creare una cache con i dati prepopolati. Durante il processo di importazione Azure Redis Cache carica i file RDB dallo spazio di archiviazione Azure in memoria e quindi inserisce le chiavi nella cache.

Esportazione consente di esportare i dati memorizzati nella Cache Redis Azure per Redis i file specifici RDB compatibili. È possibile utilizzare questa caratteristica per spostare i dati da un'istanza di Azure Redis Cache a un altro o a un altro server di Redis. Durante il processo di esportazione che viene creato un file temporaneo nella macchina virtuale che ospita l'istanza di server Azure Redis Cache e il file viene caricato per l'account di archiviazione designato. Al termine dell'operazione di esportazione con uno stato esito positivo o negativo, viene eliminato il file temporaneo.

>[AZURE.IMPORTANT] Importazione/esportazione è disponibile solo per le cache di livello Premium. Per ulteriori informazioni e istruzioni, vedere [importare ed esportare i dati nella Cache Redis Azure](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Impostazioni di amministrazione

Le impostazioni nella sezione **amministrazione** consentono di eseguire le seguenti attività amministrative per la cache premium. 

![Amministrazione](./media/cache-configure/redis-cache-administration.png)

-   [Riavviare il computer](#reboot)
-   [Pianificare gli aggiornamenti](#schedule-updates)

>[AZURE.IMPORTANT] Le impostazioni in questa sezione sono disponibili solo per le cache di livello Premium.

### <a name="reboot"></a>Riavviare il computer

E il **riavvio** consente di riavviare uno o più nodi della cache. In questo modo è possibile verificare l'applicazione per la resilienza in caso di errore.

![Riavviare il computer](./media/cache-configure/redis-cache-reboot.png)

Se si dispone di una cache premium con il servizio cluster abilitato, è possibile selezionare quali shards della cache per riavviare il computer.

![Riavviare il computer](./media/cache-configure/redis-cache-reboot-cluster.png)

Riavviare uno o più nodi della cache, selezionare i nodi desiderati e fare clic su **Riavvia**. Se si dispone di una cache premium con il servizio cluster abilitato, selezionare shard(s) riavviare il computer e quindi fare clic su **Riavvia**. Dopo alcuni minuti, riavviare il computer nodi selezionata e sono riportate in linea pochi minuti dopo.

>[AZURE.IMPORTANT] Riavviare il computer è disponibile solo per le cache di livello Premium. Per ulteriori informazioni e istruzioni, vedere [amministrazione Azure Redis Cache - riavviare il computer](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Pianificare gli aggiornamenti

E il **pianificare gli aggiornamenti** consente di designare una finestra di manutenzione Redis degli aggiornamenti di server per la cache. 

>[AZURE.IMPORTANT] Si noti che la finestra di manutenzione si applica solo alle Redis aggiornamenti server e non in tutti Azure aggiorna o aggiorna al sistema operativo di macchine virtuali che ospitano la cache.

![Pianificare gli aggiornamenti](./media/cache-configure/redis-schedule-updates.png)

Per specificare una finestra di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio finestra manutenzione per ogni giorno e fare clic su **OK**. Si noti che l'ora della finestra della manutenzione in formato UTC. 

>[AZURE.IMPORTANT] Pianificare gli aggiornamenti è disponibile solo per le cache di livello Premium. Per ulteriori informazioni e istruzioni, vedere [amministrazione Azure Redis Cache - pianificare gli aggiornamenti](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Impostazioni di diagnostica

La sezione **diagnostica** consente di configurare la diagnostica per la Cache Redis.

![Diagnostica](./media/cache-configure/redis-cache-diagnostics.png)

Fare clic su **diagnostica** per [configurare l'account di archiviazione](cache-how-to-monitor.md#enable-cache-diagnostics) usata per archiviare la diagnostica cache.

![Redis Cache diagnostica](./media/cache-configure/redis-cache-diagnostics-settings.png)

Fare clic su **Redis metriche** per [visualizzare metriche](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) per cache e **le regole di avviso** per [configurare le regole di avviso](cache-how-to-monitor.md#operations-and-alerts).

Per ulteriori informazioni sulla diagnostica Azure Redis Cache, vedere [come eseguire il monitoraggio Azure Redis Cache](cache-how-to-monitor.md).


## <a name="network-settings"></a>Impostazioni di rete

Le impostazioni della sezione **rete** consentono di accedere e configurare le impostazioni seguenti per la cache.

![Rete](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Impostazioni di rete virtuale sono disponibili solo per cache premium che sono stati configurati con supporto VNET durante la creazione della cache. Per informazioni sulla creazione di una cache premium con VNET supporto e aggiornamento delle impostazioni, informazioni su [come configurare la rete virtuale supporto per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Impostazioni di gestione delle risorse

![Gestione delle risorse](./media/cache-configure/redis-cache-resource-management.png)

La sezione **contrassegni** contribuisce di organizzare le risorse. Per ulteriori informazioni, vedere [usare i contrassegni per organizzare le risorse Azure](../resource-group-using-tags.md).

La sezione **Blocca** consente di bloccare un abbonamento, gruppo di risorse o delle risorse per impedire ad altri utenti dell'organizzazione venga eliminato accidentalmente o la modifica delle risorse critiche. Per ulteriori informazioni, vedere [risorse di blocco con Gestione risorse di Azure](../resource-group-lock-resources.md).

La sezione **utenti** fornisce supporto per il controllo di accesso basato sui ruoli (RBAC) nel portale di Azure per consentire alle organizzazioni di soddisfare i requisiti di gestione di accesso semplice e preciso. Per ulteriori informazioni, vedere [controllo dell'accesso basato sui ruoli nel portale di Azure](../active-directory/role-based-access-control-configure.md).

Fare clic su **Esporta modello** per creare ed esportare un modello delle risorse a disposizione distribuite per le distribuzioni future. Per ulteriori informazioni sull'utilizzo dei modelli, vedere [risorse di distribuzione con i modelli di gestione risorse di Azure](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configurazione del server Redis predefinita

Nuove istanze di Azure Redis Cache sono configurate con i valori di configurazione Redis predefiniti seguenti.

>[AZURE.NOTE] Non è possibile modificare le impostazioni in questa sezione utilizzando il `StackExchange.Redis.IServer.ConfigSet` metodo. Se questo metodo è chiamato con uno dei comandi in questa sezione, viene generata un'eccezione simile al seguente:  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>I valori che possono essere configurati, ad esempio **criteri di memoria max**, sono configurabili tramite gli strumenti di gestione di Azure portale o riga di comando, ad esempio CLI Azure o PowerShell.

|Impostazione|Valore predefinito|Descrizione|
|---|---|---|
|database|16|Il numero predefinito di database è 16, ma è possibile configurare un numero diverso in base a livello di prezzi. <sup>1</sup> il database predefinito è 0 DB, è possibile selezionare un altro in una base di per ogni connessione utilizzando `connection.GetDatabase(dbid)` dbid nel punto in cui non è un numero compreso tra `0` e `databases - 1`.|
|MaxClients|Dipende da prezzi livello<sup>2</sup>|Questo è il numero massimo di client connessi consentito nello stesso momento. Una volta raggiunto il limite di Redis si chiude tutte le connessioni di nuove l'invio di un messaggio di errore 'numero massimo di client raggiunto'.|
|criteri di MaxMemory|lru volatili|Criteri MaxMemory riguarda l'impostazione di come Redis verrà selezionare gli elementi da rimuovere quando viene raggiunta maxmemory (la dimensione della cache che offre la possibilità che è stata selezionata al momento della creazione della cache). Con Azure Redis Cache l'impostazione predefinita è volatili lru, che consente di rimuovere i tasti con una scadenza set mediante un algoritmo LRU. Questa impostazione può essere configurata nel portale di Azure. Per ulteriori informazioni, vedere [Maxmemory criteri e riservato maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|esempi di MaxMemory|3|LRU e degli algoritmi TTL minimi non sono algoritmi precisi ma approssimate algoritmi (per risparmiare memoria), che consente di selezionare anche la dimensione del campione per verificare. Ad esempio per impostazione predefinita Redis verrà selezionare tre chiavi e selezionare quello che è stato utilizzato meno recente.|
|limite di tempo LUA|5.000|Max il tempo di esecuzione di uno script Lua in millisecondi. Se si raggiunge la durata massima di esecuzione Redis accederà che uno script è ancora in esecuzione dopo il numero massimo consentito ora e inizia a rispondere alle query con un errore.|
|limite di evento LUA|500|Questa è la dimensione massima della coda di eventi di script.|
|limite buffer di output client limite buffer di output normalclient pubsub|0 0 032mb 8mb 60|I limiti di buffer di output client possono essere utilizzati per forzare la disconnessione ai clienti che sono non legge i dati dal server sufficientemente veloce per qualche motivo (un motivo comune è che un client Pub/Sub non è possibile utilizzare messaggi veloci come l'autore può presentarli). Per ulteriori informazioni, vedere [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> Il limite per `databases` è diverso per ogni Cache Redis Azure prezzi livello e possono essere impostate al momento della creazione della cache. Se non `databases` impostazione viene specificato durante la creazione della cache, il valore predefinito è 16.

-   Base e Standard
    -   C0 cache (250 MB) - fino a 16 database
    -   C1 cache (1 GB) - fino a 16 database
    -   C2 cache (2,5 GB) - fino a 16 database
    -   C3 (6 GB) di cache - fino a 16 database
    -   C4 cache (13 GB) - fino a 32 database
    -   C5 cache (26 GB) - fino a 48 database
    -   C6 cache (53 GB) - fino a 64 database
-   Cache Premium
    -   P1 (6-60 GB in) - fino a 16 database
    -   P2 (13-130 GB in) - fino a 32 database
    -   P3 (26-260 GB in) - fino a 48 database
    -   P4 (53-530 GB in) - fino a 64 database
    -   Tutte le cache premium con cluster Redis abilitato - cluster Redis supporta solo l'utilizzo del database 0 in modo che il `databases` limitare per qualsiasi cache premium con cluster Redis abilitate in modo efficace è 1 e il comando [Select](http://redis.io/commands/select) non è consentito. Per ulteriori informazioni, vedere [necessario apportare le modifiche alla mia applicazione client per utilizzare il servizio cluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] Il `databases` impostazione può essere configurate solo durante la creazione della cache che esclusivamente tramite PowerShell, CLI o altri client di gestione. Per un esempio di configurazione `databases` durante la creazione della cache tramite PowerShell, vedere [AzureRmRedisCache di nuovo](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` è diverso per ogni Cache Redis Azure prezzi livello.

-   Base e Standard
    -   C0 cache (250 MB) - tutte le 256 connessioni
    -   C1 cache (1 GB) - fino a 1.000 connessioni
    -   C2 cache (2,5 GB) - fino a 2.000 connessioni
    -   C3 (6 GB) di cache - fino a 5.000 connessioni
    -   C4 cache (13 GB) - fino a 10.000 connessioni
    -   C5 cache (26 GB) - tutte le 15.000 connessioni
    -   C6 cache (53 GB) - fino a 20.000 connessioni
-   Cache Premium
    -   P1 (6-60 GB in) - tutte le 7500 connessioni
    -   P2 (13-130 GB in) - tutte le 15.000 connessioni
    -   P3 (26-260 GB in) - tutte le 30.000 connessioni
    -   P4 (53-530 GB in) - tutte le 40.000 connessioni

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis comandi non è supportati nella Cache Redis Azure

>[AZURE.IMPORTANT] Perché configurazione e la gestione delle istanze di Azure Redis Cache viene gestita da Microsoft sono disabilitati i comandi seguenti. Se si tenta di richiamare li si riceverà un messaggio di errore simile a `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIGURAZIONE
>-  ESEGUIRE IL DEBUG
>-  ESEGUIRE LA MIGRAZIONE
>-  SALVA
>-  CHIUSURA
>-  SLAVEOF
>-  CLUSTER - Cluster scrittura sono disabilitati, ma i comandi consentiti Cluster di sola lettura.

Per ulteriori informazioni sui comandi Redis, vedere [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis console

È possibile eseguire in modo sicuro comandi per le istanze di Azure Redis Cache tramite la **Console Redis**, che è disponibile per Standard e le cache Premium.

>[AZURE.IMPORTANT] Console di Redis non funziona con VNET cluster e database diverso da 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - quando la cache fa parte di un VNET, solo client nel VNET possono accedere la cache. Poiché la Console Redis utilizza il client redis cli.exe ospitato in macchine virtuali che non fanno parte del VNET, non è possibile connettersi alla cache.
>-  [Cluster](cache-how-to-premium-clustering.md) - Console di Redis utilizzato il client redis cli.exe che non supporta i cluster al momento. L'utilità di redis cli ramo [instabile](http://redis.io/download) dell'archivio Redis in GitHub implementa il supporto di base quando avviato con la `-c` passare. Per ulteriori informazioni vedere [riproduzione con il cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) su [http://redis.io](http://redis.io) in [Redis esercitazione cluster](http://redis.io/topics/cluster-tutorial).
>-  Console Redis crea una nuova connessione al database 0 ogni volta che si invia un comando. Non è possibile utilizzare il `SELECT` comando per selezionare un altro database, perché il database sia su 0 con ogni comando. Per informazioni sull'esecuzione di comandi Redis, inclusa la modifica a un altro database, vedere [come è possibile eseguire i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)

Per accedere alla Console Redis, fare clic su **Console** e il **Redis Cache** .

![Redis console](./media/cache-configure/redis-console-menu.png)

Per eseguire i comandi sull'istanza della cache, è sufficiente digitare il comando desiderato alla console.

![Redis console](./media/cache-configure/redis-console.png)

Per l'elenco dei comandi di Redis che sono disabilitate per Azure Redis Cache, vedere la sezione precedente [Redis comandi non supportati in Azure Redis Cache](#redis-commands-not-supported-in-azure-redis-cache) . Per ulteriori informazioni sui comandi Redis, vedere [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Spostare la cache in una nuova sottoscrizione

È possibile spostare la cache in una nuova sottoscrizione, fare clic su **Sposta**.

![Spostare Redis Cache](./media/cache-configure/redis-cache-move.png)

Per informazioni sullo spostamento di risorse da un gruppo di risorse a un'altra e da una sottoscrizione a un altro, vedere [spostare le risorse nuovo gruppo di risorse o l'abbonamento](../resource-group-move-resources.md).

## <a name="next-steps"></a>Passaggi successivi
-   Per ulteriori informazioni sull'utilizzo dei comandi Redis, vedere [come è possibile eseguire i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands).
