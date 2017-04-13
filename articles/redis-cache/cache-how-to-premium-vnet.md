<properties 
    pageTitle="Come configurare il supporto di rete virtuale per una Redis Cache Premium Azure | Microsoft Azure" 
    description="Informazioni su come creare e gestire il supporto di rete virtuale per le istanze di Azure Redis Cache livello Premium" 
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

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Come configurare il supporto di rete virtuale per una Redis Cache Premium Azure
Azure Cache Redis sono offerte cache diverso che forniscono flessibilità nella scelta delle dimensioni della cache e sulle funzionalità, tra cui il nuovo livello di Premium.

Le caratteristiche di livello premium Azure Redis Cache includono cluster persistenza e il supporto di rete virtuale (VNet). Un VNet è una rete privata nel cloud. Quando un'istanza di Azure Redis Cache è configurata con un VNet, non è pubblicamente utilizzabili e accessibili solo da macchine virtuali e applicazioni all'interno di VNet. In questo articolo viene descritto come configurare il supporto di rete virtuale per un'istanza di Azure Redis Cache premium.

>[AZURE.NOTE] Azure Cache Redis supporta entrambi classica e ARM VNets.

Per informazioni su altre funzionalità di cache premium, vedere [Introduzione a livello di Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Perché VNet?
Distribuzione di [Azure virtuali (VNet)](https://azure.microsoft.com/services/virtual-network/) consente di isolamento per la Cache Redis Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità limitare ulteriormente l'accesso alla Cache Redis Azure e protezione avanzata.

## <a name="virtual-network-support"></a>Supporto di rete virtuale
Supporto di rete (VNet) virtuale è configurato nel e **Nuovo Cache Redis** durante la creazione della cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un premio prezzi livello, è possibile configurare l'integrazione di Azure Redis Cache VNet selezionando un VNet è lo stesso abbonamento e posizione come la cache. Per utilizzare un nuovo VNet, creare innanzitutto eseguendo la procedura descritta in [creare una rete virtuale tramite il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [una rete virtuale (classico) tramite il portale Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) e tornare alla e **Nuovo Cache Redis** per creare e configurare la cache premium.

Per configurare VNet per la nuova cache, fare clic su **Virtuali** sulla e **Nuovo Cache Redis** e selezionare VNet desiderato dall'elenco a discesa.

![Rete virtuale][redis-cache-vnet]

Selezionare subnet desiderata dall'elenco a discesa **Subnet** e specificare l' **indirizzo IP statico**desiderato. Se si utilizza un VNet classica il campo **indirizzo IP statico** è facoltativo, e se non è specificato, viene effettuata una scelta da subnet selezionata.

>[AZURE.IMPORTANT] Quando si distribuisce una Cache Redis Azure a un VNet ARM, la cache deve essere in una subnet dedicata che non contiene altre risorse escluse le istanze di Azure Redis Cache. Se si è tentato di distribuire una Cache Redis Azure in un VNet ARM a una subnet contenente altre risorse, la distribuzione ha esito negativo.

![Rete virtuale][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Gli indirizzi in una subnet primi quattro sono riservati e non possono essere utilizzati. Per ulteriori informazioni, vedere [Limitazioni sull'utilizzo di indirizzi IP all'interno di queste subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Dopo aver creata la cache, è possibile visualizzare la configurazione per la VNet, fare clic su **Virtuali** e **l'Impostazioni** .

![Rete virtuale][redis-cache-vnet-info]


Per connettersi all'istanza di cache Azure Redis quando si utilizza un VNet, specificare il nome host della cache nella stringa di connessione, come illustrato nell'esempio seguente.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet domande frequenti

Di seguito sono riportate le risposte alle domande frequenti sulla scala di Azure Redis Cache.

-   [Quali sono alcuni comuni problemi di configurazione con Azure Redis Cache e VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [È possibile utilizzare VNets con una cache standard o di base?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Perché è la creazione di una cache Redis non in alcune subnet ma non altri?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Durante l'hosting di una cache in un VNET tutte le funzionalità di cache funzionano correttamente?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quali sono alcuni comuni problemi di configurazione con Azure Redis Cache e VNets?

Quando Azure Redis Cache è ospitata in un VNet, vengono utilizzate le porte descritti nella tabella seguente. Se queste porte sono bloccate, la cache potrebbe non funzionare correttamente. Con una o più delle seguenti porte bloccate è il problema più comune di configurazione errata quando si utilizza Azure Redis Cache in un VNet.

| Porte     | Direzione        | Protocollo di trasporto | Scopo                                                                           | IP remoto                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | In uscita         | TCP                | Redis dipendenze su Azure lo spazio di archiviazione/infrastruttura a chiave pubblica (Internet)                                | *                                   |
| 53          | In uscita         | TCP/UDP            | Redis dipendenza DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | In ingresso          | TCP                | Comunicazione di client per Redis, il bilanciamento del carico di Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | In ingresso/uscita | TCP                | Dettagli dell'implementazione per Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | In ingresso          | TCP/UDP            | Bilanciamento del carico Azure                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | In ingresso/uscita | TCP                | Dettagli dell'implementazione per Redis (possono limitare l'endpoint remoto alle VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000 13999 | In ingresso          | TCP                | Comunicazione client a cluster Redis, il bilanciamento del carico di Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000 15999 | In ingresso          | TCP                | Comunicazione client a cluster Redis, il bilanciamento del carico di Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | In ingresso          | TCP/UDP            | Bilanciamento del carico Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | In ingresso + in uscita | TCP                | Dettagli dell'implementazione per i cluster Redis                                          | VIRTUAL_NETWORK                     |


Esistono requisiti di connettività di rete per Azure Redis Cache che non possono essere soddisfatte inizialmente in una rete virtuale. Azure Cache Redis richiede tutti gli elementi seguenti per il corretto funzionamento se utilizzata all'interno di una rete virtuale.

-  Connettività di rete in uscita per i punti finali dello spazio di archiviazione di Azure in tutto il mondo. Sono inclusi i punti finali che si trova nella stessa regione se l'istanza di Azure Redis Cache, nonché i punti finali dello spazio di archiviazione disponibile in **altre** aree Azure. Risolvere i punti finali dello spazio di archiviazione Azure in seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*e *file.core.windows.net*. 
-  Connettività di rete in uscita *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Connettività è necessaria per supportare funzionalità SSL.
-  La configurazione del DNS per la rete virtuale deve essere in grado di risolvere tutti i punti finali e domini menzionati ai punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo un'infrastruttura DNS valida viene configurata e gestita per la rete virtuale.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>È possibile utilizzare VNets con una cache standard o di base?

VNets può essere utilizzato solo con premium cache.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Perché è la creazione di una cache Redis non in alcune subnet ma non altri?

Se si distribuisce una Cache Redis Azure in un VNet ARM, la cache deve essere in una subnet dedicata contenente nessun altro tipo di risorsa. Se si è tentato di distribuire una Cache Redis Azure subnet ARM VNet contenente altre risorse, la distribuzione ha esito negativo. È necessario eliminare le risorse esistenti all'interno della subnet prima di poter creare una nuova cache Redis.

È possibile distribuire più tipi di risorse in un VNet classica purché si dispongono di indirizzi IP sufficiente.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Durante l'hosting di una cache in un VNET tutte le funzionalità di cache funzionano correttamente?

Quando la cache fa parte di un VNET, solo client nel VNET possono accedere la cache. Le seguenti funzionalità di gestione della cache di conseguenza, non funzionano in questo momento.

-   Redis Console - poiché Redis Console utilizza il client redis cli.exe ospitato in macchine virtuali che non fanno parte del VNET, non è possibile connettersi alla cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Utilizzare ExpressRoute con Azure Redis Cache

I clienti possono connettersi un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) dell'infrastruttura di rete virtuale, pertanto si estende la rete locale in Azure. 

Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connessione Internet in uscita. Con questa configurazione applicazioni client in grado di connettersi ad altri endpoint Azure inclusi Azure Redis Cache.

Tuttavia una configurazione cliente comune consiste nel definire le proprie route predefinita (0.0.0.0/0) che impone Internet il traffico in uscita invece flusso locale. Questo flusso di traffico interruzioni sempre la connettività con Azure Redis Cache perché il traffico in uscita bloccati in locale oppure NAT da un set di indirizzi che non funzionano con vari endpoint Azure riconosciuto.

La soluzione consiste nel definire uno (più) definite dall'utente route (UDRs) su subnet che contiene la Cache di Redis Azure. Un UDR definisce route specifici della subnet che verranno utilizzato il valore anziché la route predefinita.

Se possibile, si consiglia di utilizzare le seguenti operazioni:

- La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita forza tunnel tutto il traffico in uscita locale.
- UDR applicati alla subnet contenente la Cache di Azure Redis definisce 0.0.0.0/0 con un tipo di hop successivo di Internet (un esempio è più in basso in questo articolo).

L'effetto combinato di questa procedura è che il livello di subnet UDR avrà la priorità sul ExpressRoute forzata tunneling, in modo da garantire accesso a Internet in uscita dalla Cache Redis Azure.

Anche se la connessione a un'istanza di Azure Redis Cache da un locale applicazione tramite ExpressRoute non uno scenario di utilizzo tipico a causa di prestazioni (per ottenere prestazioni ottimali Azure Redis Cache client dovrebbero essere nella stessa regione Azure Redis Cache), in questo scenario che il percorso di rete in uscita non passano attraverso proxy aziendale interno e non è possibile forzare tunnel locale. In questo modo modifica l'indirizzo NAT effettivo del traffico in uscita dalla Cache Redis Azure. Modifica dell'indirizzo NAT di una Cache Redis Azure il traffico di rete in uscita dell'istanza causa problemi di connettività a-molti dei punti finali elencati sopra. Il risultato tentativi di creazione di Azure Redis Cache.

**Importante:**  Route definite in una UDR **deve** essere abbastanza specifico per hanno la precedenza su qualsiasi route annunciate da ExpressRoute configurazione non corretta. Nell'esempio seguente viene utilizzata l'intervallo di indirizzi 0.0.0.0/0 generali e come tali potrebbero venire accidentalmente annullata da annunci route con più intervalli di indirizzi.

**Molto importante:**  Azure Redis Cache non è supportata con configurazioni ExpressRoute tale **in modo non corretto cross-annunciare route dal percorso di peering pubblico al percorso peering privato**. Configurazioni ExpressRoute contenenti pubblico peering configurato, verrà visualizzato annunci route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi in modo non corretto tra annunciata nel percorso peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza Azure Redis Cache siano in modo non corretto forza tunnel a infrastruttura di rete locale del cliente. Questo flusso di rete le interruzioni di Azure Redis Cache. Per risolvere questo problema è interrompere route relative alla pubblicità tra dal percorso di peering pubblico al percorso peering privato.

Informazioni generali su route definite dall'utente sono disponibile in questa [Panoramica](../virtual-network/virtual-networks-udr-overview.md). 

Per ulteriori informazioni su ExpressRoute, vedere [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre caratteristiche di cache premium.

-   [Introduzione a livello di Azure Redis Cache Premium](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

