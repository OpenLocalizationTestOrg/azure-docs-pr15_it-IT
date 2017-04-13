<properties
   pageTitle="Introduzione a Gestione risorse Cluster tessuti servizio | Microsoft Azure"
   description="Introduzione alla gestione risorse servizio tessuti Cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduzione a Gestione risorse cluster tessuti servizio
In genere la gestione dei sistemi informatici o un insieme di servizi consente di gestire il recupero alcuni fisici o macchine virtuali dedicate ai servizi o sistemi. Molti servizi principali sono state suddivise in un livello di "web" e un livello "dati" o "archiviazione" cercando con alcuni altri componenti speciali come una cache. Altri tipi di applicazioni avrebbe un livello di messaggistica nel punto in cui le richieste in cui il passato avanti o indietro, connesso a un livello di lavoro per qualsiasi analisi o trasformazione necessario come parte del messaggio. Ogni tipo di carico di lavoro si ha un computer specifici a cui si dedicano: il database si ha un paio macchine dedicate su di esso, il server web alcune. Se un determinato tipo di carico di lavoro causato il computer in cui si trovava eseguire troppo caldo e quindi aggiunto più macchine con tale tipo di carico di lavoro configurato per l'esecuzione su di esso o sostituiti alcuni dei computer con computer più grande. Semplice. Se un computer non è riuscita, la parte dell'applicazione nel complesso eseguito capacità inferiore fino a quando non può essere ripristinato nel computer. Ancora semplice (se non necessariamente divertente).

A questo punto, tuttavia, si supponga aver trovato necessario scalabilità e sono stati contenitori e/o microservice tuffo. Improvvisamente disponibili se stessi con decine, centinaia o migliaia di computer, decine di diversi tipi di servizi, ad esempio centinaia di diverse istanze di tali servizi, ognuna con una o più istanze o repliche per alta disponibilità (HA).

Improvvisamente gestire l'ambiente non è molto semplice come si gestiscono alcuni computer dedicato a singoli tipi di carichi di lavoro. I server sono virtuali e i nomi non sono (si *hanno* cambiato approcci mentali da [animali a bovini](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) al termine di tutte). Configurazione è minore di computer e ulteriori informazioni sui servizi stessi. Hardware dedicato è un elemento del passato e servizi sono più piccoli sistemi distribuiti, che interessano più parti più piccole dell'hardware di prodotto.

Causa accedendo servizi distinti in esecuzione su hardware merci l'app in precedenza su, a più livelli, è ora possibile molte altre combinazioni di occuparsi di. Chi decide quali tipi di carichi di lavoro possono essere eseguiti sui quali hardware o quanti? Carichi di lavoro di funzionare correttamente con lo stesso hardware, e che sono in conflitto? Quando un computer non è raggiungibile... che cos'è stata anche in esecuzione? Chi è responsabile assicurandosi che il carico di lavoro viene eseguito di nuovo? Attendere per il computer (virtuale?) torni o i carichi di lavoro automaticamente esito negativo su altri computer e continuare l'esecuzione? È intervento umano? Informazioni sugli aggiornamenti di questo tipo di ambiente?

Come gli sviluppatori e dagli operatori che vivono in questo tipo del mondo, verranno per assistenza nella gestione di questa complessità e viene visualizzato un binge assunzioni e si tenta di carta sopra la complessità con altri utenti è non risposta corretta.

cosa fare?

## <a name="introducing-orchestrators"></a>Introduzione a orchestrators
Un "Orchestrator" è il termine generale per una parte di software che consente agli amministratori di gestire questi tipi di ambienti. Orchestrators sono i componenti che accettano nelle convocazioni come "Desidero 5 copie di questo servizio in esecuzione dell'ambiente operativo", renderli true e quindi (provare) per lasciarla invariata per fare in modo.

Orchestrators (non uomo) sono cosa creazione e utilizzo in caso di un computer o un carico di lavoro termina per qualche motivo imprevisto. Orchestrators la maggior parte delle limitarsi affrontare errore, ad esempio consentendo con nuove distribuzioni, gestire gli aggiornamenti e gestiscono il consumo di risorse, ma tutti sono sostanzialmente sulla gestione che alcune bene accolta lo stato della configurazione dell'ambiente. Si desidera stabilire un Orchestrator ciò che desideri è di eseguire la maggior parte. Chronos o Marathon nella parte superiore di Mesos parco, sciame, Kubernetes e tessuti servizio sono tutti esempi di Orchestrators oppure devono essere incorporate. Altre vengono create quanto la complessità della gestione delle distribuzioni di scenari reali in diversi tipi di ambienti e condizioni crescita o di modifica.

## <a name="orchestration-as-a-service"></a>Orchestrazione come servizio
Il processo di Orchestrator in un cluster di servizio tessuti viene gestito principalmente da Gestione risorse Cluster. Gestione risorse Cluster tessuti servizio corrisponde a uno dei servizi all'interno dell'infrastruttura di servizio e viene avviato automaticamente ogni cluster.  Processo di gestione delle risorse Cluster è in genere, suddivise in tre parti:

1. Applicazione di regole
2. Ottimizzare l'ambiente
3. Assistenza in altri processi

### <a name="what-it-isnt"></a>Non è
Nelle tradizionali N livello-App web durante sempre alcune concetto di un "bilanciamento del carico", in genere definito come una rete carico bilanciamento bilanciamento o un servizio di bilanciamento del carico applicazione (ALB) a seconda di dove suo dello stack di rete. Alcuni servizi di bilanciamento del carico Hardware in base a come offerta BigIP del F5, gli altri utenti sono software in base ad esempio Microsoft bilanciamento carico di rete. In altri ambienti che appare simile al HAProxy questo ruolo. In questi architetture il processo di bilanciamento del carico è per essere certi che tutte le macchine diversi senza informazioni sullo stato front-end o computer diversi del cluster ricevere (approssimativamente) la stessa quantità di lavoro. Strategie per questo variare da inviare ogni diversi chiamata a un altro server, sessione blocco/aderenza, a effettiva allocazione stima e chiamata in base al costo previsto e il carico di lavoro corrente.

Si noti che si tratta di migliore meccanismo per garantire che il livello di web rimasto approssimativamente bilanciato. Strategie per bilanciamento del carico il livello di dati sono stati completamente diverso e dipendenti l'archiviazione dei dati, in genere ruotare intorno sharding dati, la memorizzazione nella cache, database gestita visualizzazioni e stored procedure e così via.

Mentre alcune di queste strategie sono interessanti, Gestione risorse Cluster tessuti servizio non è nulla ad esempio un bilanciamento del carico di rete o una cache. Mentre un bilanciamento del carico di rete garantisce che front-end bilanciate spostando il traffico in cui i servizi sono in esecuzione, la gestione di risorse Cluster tessuti servizio accetta una strategia completamente diversa: sostanzialmente, tessuti servizio si sposta *servizi* nel punto in cui rendono più significativo (e prevede che il traffico o per caricarli da seguire). Possono essere, ad esempio nodi che sono attualmente freddo perché i servizi che sono presenti non eseguono molte lavoro immediatamente o che sono stati eliminati o spostati in un' posizione. Un altro esempio Gestione risorse Cluster potrebbe anche spostare un servizio lontano da un computer che è necessario aggiornare o che overload a causa di un picco consumo dai servizi in esecuzione su di esso. Poiché la gestione di risorse Cluster è responsabile dello spostamento servizi intorno (non fornire il traffico di rete a dove si trovano già servizi), contiene un set di funzionalità significativamente diverso rispetto a cosa trovare in un servizio di bilanciamento del carico di rete e utilizza sostanzialmente diverse strategie per garantire che le risorse hardware del cluster vengono utilizzate anche.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sul flusso di informazioni e architettura all'interno del gestore di risorsa, consultare anche [in questo articolo](service-fabric-cluster-resource-manager-architecture.md)
- Gestione risorse Cluster sono presenti molte opzioni per la descrizione del cluster. Per informazioni sui estrarre in questo articolo sulla [Descrizione un cluster di tessuti di servizio](service-fabric-cluster-resource-manager-cluster-description.md)
- Per ulteriori informazioni sulle altre opzioni disponibili per la configurazione dei servizi estrazione obbligatoria l'argomento in altre configurazioni Manager delle risorse Cluster disponibili [informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Sono come il gestore di risorse del servizio Cluster tessuti gestione è affidata a consumo e capacità del cluster. Per ulteriori informazioni sulle loro e come configurarli estrarre [in questo articolo](service-fabric-cluster-resource-manager-metrics.md)
- Gestione risorse Cluster funziona con le funzionalità di gestione dell'infrastruttura di servizio. Per ulteriori informazioni su tale integrazione, leggere [questo articolo](service-fabric-cluster-resource-manager-management-integration.md)
- Per scoprire le modalità di gestione risorse Cluster gestisce e bilancia carico del cluster, vedere l'articolo su [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
