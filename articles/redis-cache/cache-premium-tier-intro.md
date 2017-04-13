<properties 
    pageTitle="Introduzione a livello di Azure Redis Cache Premium | Microsoft Azure" 
    description="Informazioni su come creare e gestire persistenza Redis, Redis cluster e supporto VNET per le istanze di Azure Redis Cache livello Premium" 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduzione a livello di Azure Redis Cache Premium
Azure Cache Redis è una cache distribuita e gestita che consente di creare applicazioni altamente scalable e risponde fornendo velocità accesso ai dati. 

Il nuovo livello di Premium è un livello pronto Enterprise, che include tutte le caratteristiche di livello Standard e altri oggetti, ad esempio migliorare le prestazioni, più grande carichi di lavoro, il ripristino di emergenza, Importa/Esporta e protezione avanzata. Continuare la lettura per ulteriori informazioni sulle caratteristiche aggiuntive di livello della cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Migliorare le prestazioni rispetto al livello Standard o di base
**Prestazioni migliori su Standard o di base livello.** Cache nel livello Premium vengono distribuiti sull'hardware che ha processori più veloci e offre prestazioni migliori rispetto alla base o livello Standard. Cache di livello Premium hanno effettiva superiore e inferiore latenza. 

**Velocità per la stessa Cache dimensione è superiore rispetto a livello Standard Premium.** Ad esempio, la velocità di un 53 P4 GB (Premium) cache è 250K richieste al secondo rispetto a 150 K per C6 (Standard).

Per ulteriori informazioni sulle dimensioni, velocità e la larghezza di banda con cache premium, vedere [Domande frequenti di Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis persistenza dei dati
Il livello di Premium consente di mantenere i dati della cache in un account di archiviazione Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di infrastruttura sottostante sono problemi possono essere possibile perdita di dati. È consigliabile utilizzare la funzione di persistenza dati Redis nel livello Premium per aumentare flessibilità perdite di dati. Azure Cache Redis offre RDB e AOF (disponibile a breve) opzioni [Redis persistenza](http://redis.io/topics/persistence). 

Per istruzioni sulla configurazione persistenza, vedere [come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis cluster
Se si desidera creare cache maggiore di 53 GB o da dati condiviso tra più nodi Redis, è possibile utilizzare Redis cluster che è disponibile nel livello Premium. Ogni nodo è costituito da una coppia di cache principale/replica gestita da Azure disponibilità elevata. 

**Redis cluster fornisce effettiva e una scala massima.** Velocità aumenta in modo lineare si aumenta il numero di shards (nodi) del cluster. Ad esempio. Se si crea un cluster di P4 di 10 shards, quindi la velocità disponibile è 250K * 10 = 2,5 milioni richieste al secondo. Vedere le [Domande frequenti Azure di Cache Redis](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per ulteriori informazioni sulle dimensioni, velocità e la larghezza di banda con premium cache.

Per iniziare con il servizio cluster, vedere [come configurare il servizio cluster per una Redis Cache Premium Azure](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Isolamento e protezione avanzata

Cache creati in livelli Basic o Standard sono accessibili via internet pubblica. Accesso alla Cache è limitato in base a tasto di scelta rapida. Con il livello di Premium ulteriormente per garantire che solo i client all'interno di una rete specificata possono accedere alla Cache. È possibile distribuire Redis Cache in una [Rete virtuale Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). È possibile usare tutte le caratteristiche dei VNet, ad esempio subnet, criteri di controllo di accesso e altre funzionalità possono ulteriormente limitare l'accesso a Redis.

Per ulteriori informazioni, vedere [come configurare il supporto di rete virtuale per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importazione/esportazione

Importazione/esportazione è un'operazione di gestione dei dati di Azure Redis Cache che consente di importare i dati nella Cache Redis Azure o esportare dati dalla Cache Redis Azure mediante l'importazione e l'esportazione di uno snapshot Redis Cache Database (RDB) da una cache premium in un blob di pagine in un Account di archiviazione Azure. In questo modo è possibile eseguire la migrazione tra diverse istanze di Azure Redis Cache o popolare la cache con i dati prima di utilizzare.

Importazione può essere utilizzato per visualizzare i file specifici RDB compatibili Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud ambiente o, inclusi Redis eseguono Linux, Windows o un provider del cloud, ad esempio servizi Web di Amazon e gli altri utenti. Importazione di dati è un modo semplice per creare una cache con i dati prepopolati. Durante il processo di importazione, Azure Redis Cache carica i file RDB dallo spazio di archiviazione Azure in memoria e quindi inserisce le chiavi nella cache.

Esportazione consente di esportare i dati memorizzati nella Cache Redis Azure per Redis i file specifici RDB compatibili. È possibile utilizzare questa caratteristica per spostare i dati da un'istanza di Azure Redis Cache a un altro o a un altro server di Redis. Durante il processo di esportazione, viene creato un file temporaneo nella macchina virtuale che ospita l'istanza di server Azure Redis Cache e il file viene caricato per l'account di archiviazione designato. Al termine dell'operazione di esportazione con uno stato esito positivo o negativo, viene eliminato il file temporaneo.

Per ulteriori informazioni, vedere [come importare i dati ed esportare i dati dalla Cache Redis Azure](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Riavviare il computer

Il livello di premium consente di riavviare uno o più nodi della cache richiesta. In questo modo è possibile verificare l'applicazione per la resilienza in caso di errore. È possibile riavviare i nodi seguenti.

-   Nodo principale della cache
-   Nodo secondario della cache
-   Nodi principale e secondaria della cache
-   Quando si utilizza una cache premium con il servizio cluster, è possibile riavviare lo schema, secondario o entrambi i nodi per singoli shards nella cache

Per ulteriori informazioni, vedere [riavviare](cache-administration.md#reboot) e [Riavviare domande frequenti](cache-administration.md#reboot-faq).

## <a name="schedule-updates"></a>Pianificare gli aggiornamenti

La funzionalità di aggiornamento pianificato consente di designare una finestra di manutenzione per la cache. Se viene specificata la finestra di manutenzione, qualsiasi Redis server gli aggiornamenti durante questo periodo. Per designare una finestra di manutenzione, selezionare i giorni desiderati e specificare la manutenzione finestra iniziare ora per ogni giorno. Si noti che l'ora della finestra della manutenzione in formato UTC. 

Per ulteriori informazioni, vedere [pianificare gli aggiornamenti](cache-administration.md#schedule-updates) e [pianificare gli aggiornamenti domande frequenti](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Solo Redis server gli aggiornamenti effettuati durante la finestra di manutenzione pianificata. La finestra di manutenzione non è valida per gli aggiornamenti di Azure o gli aggiornamenti del sistema operativo macchine Virtuali.

## <a name="to-scale-to-the-premium-tier"></a>Per ridimensionare il livello di premium

Per ridimensionare al livello premium, è sufficiente scegliere uno dei livelli premium e il **Cambia prezzi livello** . È anche possibile ridimensionare la cache per il livello di premium tramite PowerShell e CLI. Per istruzioni dettagliate, vedere [come scala Azure Redis Cache](cache-how-to-scale.md) e [come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passaggi successivi

Creare una cache ed esplorare le nuove caratteristiche di livello premium.

-   [Come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md)
-   [Come configurare il supporto di rete virtuale per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md)
-   [Come configurare il servizio cluster per una Redis Cache Premium Azure](cache-how-to-premium-clustering.md)
-   [Come importare dati in ed esportare dati dalla Cache Redis Azure](cache-how-to-import-export-data.md)
-   [Come amministrare Azure Redis Cache](cache-administration.md)
  

