<properties
   pageTitle="Panoramica di servizio tessuti | Microsoft Azure"
   description="Panoramica dell'infrastruttura di servizio, nel punto in cui le applicazioni sono costituite da molti microservices per fornire scala e resilienza. Servizio tessuti è una piattaforma di sistemi distribuiti utilizzato per creare scalable, affidabile e può essere gestito facilmente applicazioni per il cloud"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Panoramica dell'infrastruttura di servizio
Servizio tessuti è una piattaforma di sistemi distribuiti che consente di creare un pacchetto, distribuire e gestire microservices scalabilità e affidabilità facilmente. Servizio tessuti risolve anche sfide significative per lo sviluppo e la gestione di applicazioni cloud. Gli sviluppatori e per gli amministratori possono evitare la risoluzione di problemi di infrastruttura complessa e lo stato attivo invece sull'implementazione critiche, complesse carichi di lavoro di sapere che siano scalable, affidabile e gestibile. Servizio tessuti rappresenta la piattaforma middleware avanguardia per la creazione e gestione di questi aziendali, applicazioni cloud scala di livello 1.

Questo [breve video](https://aka.ms/servicefabricvideo) vengono fornite informazioni introduttive per tessuti servizio e microservices.


## <a name="applications-composed-of-microservices"></a>Applicazioni è composto da microservices
Servizio tessuti consente di creare e gestire applicazioni scalabile e affidabile composte da microservices esecuzione densità molto elevato in un pool condiviso di macchine (definiti un cluster). Fornisce un runtime sofisticato per la creazione di microservices e senza stato distribuito, scalable. Offre funzionalità di gestione dell'applicazione completo per il provisioning, distribuzione, monitoraggio, l'aggiornamento/patch e l'eliminazione delle applicazioni distribuite.

Perché è importante un approccio microservices? Sono due motivi principali:

1. Consentono di scalare diverse parti dell'applicazione a seconda delle proprie necessità.

2. Team di sviluppo sono in grado di essere più flessibile in implementazione di modifiche e pertanto forniscono funzionalità ai clienti più velocemente e con più di frequente.

Servizio tessuti potenza molti servizi Microsoft oggi, incluso il Database di SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure evento hub, IoT Azure, Skype for Business e molti core services Azure.

Servizio tessuti personalizzato per la creazione di cloud services nativi che possono iniziare piccola, in base alle esigenze e aumento di scala enorme con centinaia o migliaia di computer.

Servizi Internet scala odierna sono costituiti da microservices. Esempi di microservices gateway protocollo, i profili utente, gli acquisti carrelli acquisti, inventario di elaborazione, code e memorizza nella cache. Servizio tessuti è una piattaforma microservices che fornisce un nome univoco che può essere senza informazioni sullo stato o informazioni sullo stato di ogni microservice.

Servizio tessuti offre funzionalità complete di gestione di ciclo di vita e runtime alle applicazioni composte da queste microservices. Hosts microservices all'interno di contenitori distribuito e attivato nel cluster servizio tessuti. Spostamento da macchine virtuali a possibile rende contenitori un aumento di ordine di grandezza densità. Analogamente, un altro ordine di grandezza densità diventa possibile spostando dai contenitori microservices. Ad esempio, un singolo cluster di Database SQL Azure comprende centinaia di computer che eseguono decine di migliaia di contenitori hosting un totale di centinaia di migliaia di database. Ogni database è una microservice informazioni sullo stato del servizio tessuti. Lo stesso avviene degli altri servizi in precedenza, motivo per cui il termine "superscalari" viene utilizzato per descrivere le funzionalità dell'infrastruttura di servizio. Se i contenitori producono ad alta densità, quindi microservices offrono superscalari.

Per ulteriori informazioni sull'approccio microservices, leggere [perché un approccio microservices alla creazione di applicazioni?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>La gestione e distribuzione contenitore
Infrastruttura di servizio è un [orchestrator](service-fabric-cluster-resource-manager-introduction.md) di microservices in un cluster di computer. È possibile sviluppare Microservices in molti modi di utilizzo dei [modelli di programmazione tessuti servizio](service-fabric-choose-framework.md) alla distribuzione [eseguibili guest](service-fabric-deploy-existing-app.md). Servizio tessuti possibile distribuire i servizi di immagini contenitore ed importante è possibile combinare i servizi di processi e servizi inclusi nei contenitori insieme nella stessa applicazione. Se si desidera [distribuire e gestire le immagini contenitore](service-fabric-containers-overview.md) in un cluster di computer, tessuti servizio è ideale per l'oggetto.


## <a name="create-service-fabric-clusters-anywhere"></a>Creare un punto qualsiasi cluster tessuti servizio
È possibile creare tessuti servizio cluster in numerosi ambienti, tra cui Azure o in locale, Windows Server o su Linux. Inoltre, l'ambiente di sviluppo in SDK è identico all'ambiente di produzione con alcun emulatori correlati. In altre parole, se non si verifica il cluster sviluppo locale distribuisce allo stesso cluster in altri ambienti.

Per ulteriori informazioni sulla creazione di cluster locale, leggere [creazione di un cluster in Windows Server o Linux](service-fabric-deploy-anywhere.md) o per Azure creazione di un cluster [tramite il portale Azure](service-fabric-cluster-creation-via-portal.md).

![Piattaforma tessuti servizio][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Informazioni sullo stato e senza microservices tessuti servizio

Servizio tessuti consente di creare applicazioni composta da microservices. Senza informazioni sullo stato microservices (gateway protocollo, proxy web, ecc.) non mantengono uno stato modificabile all'esterno di una richiesta specificata e la relativa risposta dal servizio. Ruoli di lavoro servizi Cloud Azure sono un esempio di un servizio senza informazioni sullo stato. Informazioni sullo stato microservices (account utente, database, dispositivi, i carrelli acquisti, code e così via) mantenere uno stato modificabile, rilevanti oltre la richiesta e la relativa risposta. Applicazioni su Internet oggi è costituito da una combinazione di stato e senza microservices.

Perché aggiungere informazioni sullo stato microservices insieme a quelli senza informazioni sullo stato? Sono due motivi principali:

1. La possibilità di creare ad alta velocità, bassa latenza, tolleranza di errore in linea di elaborazione delle transazioni servizi (OLTP) mantenendo codice e i dati Chiudi nello stesso computer. Alcuni esempi sono interattivi negozi, ricerca, sistemi Internet di elementi (IoT), sistemi commerciali, sistemi di rilevamento di elaborazione e le frodi carta di credito e gestione dei record.

2. Semplificazione di progettazione dell'applicazione. Informazioni sullo stato microservices eliminare la necessità di code aggiuntive e memorizza, in genere è necessario per soddisfare i requisiti di disponibilità e la latenza di un'applicazione puramente senza stato. Informazioni sullo stato servizi sono naturale elevata disponibilità e latenza bassa, riducendo il numero di elementi mobili per la gestione dell'applicazione nel suo insieme.

Per ulteriori informazioni sui modelli di applicazione di servizio tessuti, leggere [gli scenari di applicazioni](service-fabric-application-scenarios.md) e [scegliendo un framework di modello di programmazione](service-fabric-choose-framework.md) per il servizio

## <a name="application-lifecycle-management"></a>Gestione del ciclo di vita delle applicazioni
Servizio tessuti fornisce supporto di livello superiore per l'applicazione completa gestione vita () di applicazioni cloud - dallo sviluppo distribuzione, gestione quotidiana e manutenzione per rimuovere eventuale.

Le funzionalità di servizio tessuti ALM consentono agli amministratori applicazione / operatori IT per usare i flussi di lavoro semplice, bassa tocco a disposizione, distribuzione, patch e monitorare le applicazioni. I flussi di lavoro predefiniti notevolmente ridurre il carico di lavoro per gli operatori di IT per rendere sempre disponibili le applicazioni.

La maggior parte delle applicazioni è costituito da una combinazione di stato e senza microservices e altri file eseguibili/runtime distribuiti. Se i tipi di sicuri applicazioni e microservices nel pacchetto, tessuti servizio consente l'installazione di più istanze dell'applicazione. Ogni istanza gestito e aggiornato in modo indipendente. È importante tessuti servizio è in grado di distribuire *qualsiasi* eseguibili o runtime e renderle affidabili. Ad esempio servizio tessuti distribuisce ASP.NET Core 1, Node, macchine virtuali di linguaggio, script o qualsiasi altro elemento che costituisce l'applicazione.

Per ulteriori informazioni sulla gestione del ciclo di vita delle applicazioni, leggere [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) e sulla distribuzione di codice vedere [distribuire guest eseguibile](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Funzionalità principali
Utilizzando tessuti servizio, è possibile:

- Sviluppo di applicazioni market scalable ortografica automatica.

- Sviluppo di applicazioni composte da microservices utilizzando il modello di programmazione servizio tessuti. In alternativa, è sufficiente host guest eseguibili e altri framework applicazione desiderata, ad esempio ASP.NET Core 1 o Node.

- Sviluppare affidabilità microservices e senza stato.

- Distribuire e gestire i contenitori includono contenitori di Windows e Docker in un cluster. Questi contenitori possono contenitore guest eseguibili o affidabile microservices e senza stato.  In entrambi i casi è possibile ottenere porta contenitore mapping porta host, il rilevamento di contenitore e failover automatico.

- Semplificare la struttura dell'applicazione utilizzando microservices informazioni sullo stato al posto di cache e code.

- Distribuire in Azure o in aree locale che eseguono Windows Server o Linux con zero modifiche al codice. Scrivere una sola volta e distribuirlo in un punto qualsiasi in un cluster di servizio tessuti.

- Sviluppare con un approccio "Data Center nel computer in uso". L'ambiente di sviluppo locale è lo stesso codice eseguibile i Data Center di Azure.

- Distribuire applicazioni in secondi.

- Distribuire le applicazioni densità superiore rispetto a macchine virtuali, la distribuzione di centinaia o migliaia di applicazioni per computer.

- Distribuire diverse versioni della stessa applicazione affiancata, ognuno in modo indipendente aggiornabile.

- Gestire il ciclo di vita delle applicazioni informazioni sullo stato senza alcun tempo di inattività, inclusi gli aggiornamenti ultime e unificatori.

- Gestire le applicazioni che utilizzano API .NET Java (Linux), PowerShell, Azure CLI (Linux) o resto interfacce.

- Eseguire l'aggiornamento e patch microservices all'interno delle applicazioni in modo indipendente.

- Monitorare e diagnosticare lo stato delle applicazioni e impostare i criteri per l'esecuzione di operazioni di ripristino automatici.

- Possibilità di scalabilità o il numero dei nodi in un cluster, nonché scalabilità in scala o verso il basso la dimensione di ogni nodo sapere che ridimensionare automaticamente le applicazioni e distribuite in base alle risorse disponibili scala.

- Guardare il bilanciamento risorsa automatica progettare la ridistribuzione di applicazioni all'interno del cluster. Servizio tessuti recupera da errori e consente di ottimizzare la distribuzione del carico basata sulle risorse disponibili.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni:
    * [Perché un microservices approccio alla creazione di applicazioni?](service-fabric-overview-microservices.md)
    * [Panoramica della terminologia](service-fabric-technical-overview.md)
* Configurare il servizio tessuti [ambiente di sviluppo](service-fabric-get-started.md)  
* [Scelta di un framework modello programmazione](service-fabric-choose-framework.md) per il servizio

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
