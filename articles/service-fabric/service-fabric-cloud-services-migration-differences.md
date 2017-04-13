<properties
   pageTitle="Differenze tra servizi Cloud e servizio tessuti | Microsoft Azure"
   description="Informazioni generali per la migrazione applicazioni dai servizi Cloud per tessuti servizio."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Informazioni sulle differenze tra servizi Cloud e servizio tessuti prima della migrazione di applicazioni.
Infrastruttura di Microsoft Azure servizio è la piattaforma avanguardia cloud per le applicazioni distribuite scalabile, affidabile. Introduce numerose nuove funzionalità di confezioni per i prodotti, distribuzione, l'aggiornamento e gestione applicazioni cloud distribuito. 

Si tratta di una Guida introduttiva alla migrazione applicazioni dai servizi Cloud per tessuti servizio. È incentrata principalmente su architettura e differenze strutturali tra servizi Cloud e servizio tessuti.
 
## <a name="applications-and-infrastructure"></a>Infrastruttura e applicazioni

Una differenza fondamentale tra servizi Cloud e tessuti servizio è la relazione tra macchine virtuali, carichi di lavoro e le applicazioni. Di seguito un carico di lavoro è definito come il codice che scritto per eseguire un'attività specifica o fornire un servizio.
 
 - **Servizi cloud è sulla distribuzione di applicazioni come macchine virtuali.** Il codice che scritto è strettamente collegato a un'istanza di macchine Virtuali, ad esempio un Web o un ruolo di lavoro. Per distribuire un carico di lavoro nei servizi Cloud consiste nel distribuire una o più istanze di macchine Virtuali che eseguono il carico di lavoro. Non prevede la separazione di applicazioni e macchine virtuali e non pertanto c'è alcun formale definizione di un'applicazione. Un'applicazione può essere considerata come un insieme di istanze Web o un ruolo di lavoro all'interno di una distribuzione di servizi Cloud o un'intera distribuzione servizi Cloud. In questo esempio, un'applicazione viene visualizzata come un insieme di istanze del ruolo.
 
![Applicazioni di servizi cloud e topologia][1]

 - **Servizio tessuti è sulla distribuzione delle applicazioni in macchine virtuali esistenti o computer che eseguono servizio tessuti su Windows o Linux.** I servizi che si scrive sono completamente separati da infrastruttura viene estratto della piattaforma di applicazione di servizio tessuti, in modo da distribuire un'applicazione a più ambienti. Un carico di lavoro nel servizio tessuti è denominata "servizio" e uno o più servizi sono raggruppati in un'applicazione formale definiti utilizzabile con la piattaforma di applicazione di servizio tessuti. Più applicazioni possono essere distribuite a un singolo cluster di servizio tessuti.
 
![Topologia e le applicazioni di servizio tessuti][2]
 
Infrastruttura di servizio stesso è un livello di piattaforma applicazione eseguibile in Windows o Linux, mentre servizi Cloud è un sistema per la distribuzione di macchine virtuali gestite Azure con carichi di lavoro collegati.
Il modello di applicazione di servizio tessuti è numerosi vantaggi:

 - Tempi di distribuzione rapida. Creazione di istanze di macchine Virtuali può essere molto tempo. Nel servizio tessuti, macchine virtuali vengono distribuite solo una volta per formare un cluster che ospita la piattaforma di applicazione di servizio tessuti. Da quel momento in poi pacchetti di applicazioni possono essere distribuiti al cluster molto rapidamente.
 - Ospitare ad alta densità. Servizi Cloud, una macchina virtuale ruolo di lavoro contiene un carico di lavoro. In servizio tessuti, applicazioni sono separate da macchine virtuali che eseguono, ovvero che è possibile distribuire un numero elevato di applicazioni in modo che un numero limitato di macchine virtuali, che può ridurre il costo totale per le distribuzioni più grande.
 - Tessuti servizio piattaforma supporti l'esecuzione in un punto qualsiasi che sono computer Windows Server o Linux, che si tratti Azure o locale. La piattaforma offre un livello di astrazione infrastruttura sottostante in modo che l'applicazione può essere eseguita in ambienti diversi. 
 - Distribuito Gestione applicazioni. Servizio tessuti è una piattaforma che ospita le applicazioni distribuite non solo, ma anche consente di gestire il ciclo di vita indipendente macchina virtuale hosting o ciclo di vita del computer.

## <a name="application-architecture"></a>Architettura delle applicazioni

In genere, l'architettura di un'applicazione di servizi Cloud include numerosi dipendenze tra servizi esterni, ad esempio Bus di servizio, tabella Azure e archiviazione Blob, SQL, Redis e gli altri utenti per gestire lo stato e i dati di un'applicazione e le comunicazioni tra Web ruoli e di lavoro in una distribuzione di servizi Cloud. Esempio di un'applicazione completa di servizi Cloud potrebbe simile al seguente:  

![Architettura dei servizi cloud][9]

Le applicazioni di servizio tessuti possono anche scegliere di utilizzare gli stessi servizi esterni in un'applicazione completa. Questo esempio l'architettura dei servizi Cloud il percorso di migrazione più semplice dai servizi Cloud per tessuti servizio consiste nel sostituire solo la distribuzione di servizi Cloud con un'applicazione di servizio tessuti, mantenere la stessa architettura complessiva. Ai servizi senza informazioni sullo stato servizio tessuti con le modifiche al codice minima, è possibile trasferire il Web ruoli e lavoro.

![Architettura di servizio tessuti dopo la migrazione semplice][10]

In questa fase, il sistema deve continuare a lavorare diverso da quello di prima. Possibile interna sfruttando funzionalità dell'infrastruttura di servizio informazioni sullo stato, stato esterno Archivia come informazioni sullo stato dei servizi eventualmente. Questo è più complesso di una migrazione semplice di Web ruoli e di lavoro a servizi senza informazioni sullo stato del servizio tessuti, perché richiede la scrittura di servizi personalizzati che offrono funzionalità equivalente a un'applicazione come precedenza servizi esterni. Vantaggi dell'operazione: 

 - Rimuovere le relazioni esterne 
 - Per unire provenienti l'installazione, gestione e aggiornamento dei modelli. 
 
Un'architettura risultante di esempio di internalizing questi servizi Impossibile simile al seguente:

![Architettura di servizio tessuti dopo la migrazione completa][11]

## <a name="communication-and-workflow"></a>Comunicazioni e flusso di lavoro

La maggior parte delle applicazioni di servizio Cloud sono costituiti da più di un livello. Analogamente, un'applicazione di servizio tessuti è costituito da più di un servizio (in genere molti servizi). Due modelli di comunicazione comuni sono comunicazione diretta e tramite un archivio permanente esterno.

### <a name="direct-communication"></a>Comunicazione diretta

Con la comunicazione diretta, livelli possono comunicare direttamente tramite endpoint esposto dall'ogni livello. Negli ambienti senza informazioni sullo stato, ad esempio servizi Cloud, questa indica che la selezione di un'istanza di un ruolo di macchine Virtuali, in modo casuale o circolari caricare saldo e connesso direttamente all'endpoint.

![Servizi cloud la comunicazione diretta][5]

 Comunicazione diretta è un modello di comunicazione comuni nel servizio tessuti. La differenza fondamentale tra servizi Cloud e servizio tessuti è tale servizi Cloud in connettersi a una macchina virtuale, mentre tessuti servizio è possibile connettersi a un servizio. Si tratta distinzione è importante per due motivi:

 - Servizi di infrastruttura di servizio non sono associati a macchine virtuali che ospitano. servizi possono spostarsi all'interno del cluster ed effetti, prevista per spostarsi all'interno per diversi motivi: risorse bilanciamento del carico, failover, applicazione e infrastruttura aggiornamenti e i vincoli di posizionamento o per caricarli. Di conseguenza, che indirizzo dell'istanza del servizio può cambiare in qualsiasi momento. 
 - Una macchina virtuale nel servizio tessuti può contenere più servizi, ognuna con i punti finali univoci.

Servizio tessuti fornisce un meccanismo di individuazione del servizio, denominato Naming Service, che possono essere utilizzati per risolvere gli indirizzi endpoint dei servizi. 

![Comunicazione diretta tessuti servizio][6]

### <a name="queues"></a>Code

Un meccanismo di comunicazione comune tra livelli in ambienti senza informazioni sullo stato, ad esempio servizi Cloud consiste nell'usare una coda di memorizzazione esterna in modo permanente archiviare mansioni da un livello a un altro. Scenari comuni sono un livello di web che invia i processi a una coda di Azure o Bus di servizio in cui istanze del ruolo di lavoro è possono annullare l'accodamento ed elaborare i processi.

![Comunicazione coda dei servizi cloud][7]

Lo stesso modello di comunicazione può essere usato in servizio tessuti. Può essere utile durante la migrazione di un'applicazione di servizi Cloud esistente per tessuti servizio. 

![Comunicazione diretta tessuti servizio][8]
 
## <a name="next-steps"></a>Passaggi successivi

Il percorso di migrazione più semplice dai servizi Cloud per tessuti servizio consiste nel sostituire solo la distribuzione di servizi Cloud con un'applicazione di servizio tessuti, abbreviando l'architettura complessiva dell'applicazione più o meno la stessa. L'articolo seguente offre una Guida per convertire un Web o un ruolo di lavoro in un servizio senza informazioni sullo stato del servizio tessuti.

 - [Migrazione semplice: convertire un Web o un ruolo di lavoro in un servizio senza informazioni sullo stato del servizio tessuti](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
