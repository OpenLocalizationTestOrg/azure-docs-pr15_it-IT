<properties
   pageTitle="Azure Case Study - Daxko/CSI di Database di SQL Azure | Microsoft Azure"
   description="Informazioni sulle modalità di utilizzo Daxko/CSI di Database SQL per accelerare il ciclo di sviluppo e migliorare il servizio clienti e prestazioni"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utilizzato Azure per accelerare il ciclo di sviluppo e migliorare il servizio clienti e prestazioni

![Logo di Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software Daxko/CSI seguendo una richiesta di verifica: la clientela di fitness e il tempo libero centri stava aumentando rapidamente, ringraziamento il successo della soluzione software aziendali esaustivo, ma mantenersi aggiornati con le esigenze di infrastruttura IT per il cliente crescente base è stata test della società il personale IT. La società è stata sempre più vincolata da aumento il sovraccarico di operazioni, in particolare per gestire i database crescente. Peggiore, il sovraccarico di operazioni che è stata taglio in risorse di sviluppo di nuove iniziative, ad esempio nuove caratteristiche di mobilità per il software dell'azienda.

In base a David Molina, Direttore sviluppo del prodotto in Daxko/CSI Azure fornito CSI Software con il modello (PaaS) della piattaforma come servizio necessari per semplificare la gestione di database, scalabilità e liberare risorse per concentrarsi su software invece di operazioni. "Database di SQL azure è un'ottima soluzione per noi. Non doversi preoccupare di gestione di SQL Server, un cluster di failover e tutte esigenze di un'altra infrastruttura è stato lo strumento ideale per noi."

Dopo la migrazione a Azure, CSI Software necessario personale di solo due per gestire oltre 600 database dei clienti. La società utilizza pool flessibile di Database SQL Azure per spostare i database dei clienti in base alle dimensioni ed è necessario.

Molina persiste, "clienti sentire la modifica immediatamente. Prima di flessibile pool, in alcuni casi avevano timeout e altri problemi durante i periodi burst. Con Azure pool flessibile, possono burst in base alle esigenze e utilizzare il software senza problemi."

Oltre a migliorare le prestazioni per i clienti, elastici Azure database pool liberare le risorse CSI Software per concentrarsi su lo sviluppo di nuovi servizi e funzionalità, anziché la gestione di operazioni e gestione. Le risorse IT consentito CSI Software di migliorare il software dell'organizzazione che offre la possibilità, SpectrumNG, per aiutare esercitare membri palestra, migliorare l'efficienza del personale e assegnare personale e i membri accesso mobile per le attività interattive e notifiche in tempo reale.

Azure ha contribuito anche Software CSI accelerare e migliorare lo sviluppo e ciclo di qualità (domande e risposte) tramite le opzioni di automazione. Con l'implementazione di Azure della società, è possono comprimere gestori di compilazione dei componenti con un semplice clic del mouse. Come descritto Molina, "come parte del ciclo di rilascio, domande e risposte è ora possibile distribuire in un ambiente di testing in Azure che strettamente riproduce il nostro stack di produzione. È possibile distribuire build immediatamente l'ambiente di sviluppo per esaminare attentamente le modifiche. È un grande successo, perché non si è sufficiente uniformità di testing prima che."

## <a name="offloading-to-the-cloud"></a>Possibilità di scaricare nel cloud

Prima di spostare nel cloud, CSI Software sono state realizzate correttamente la propria infrastruttura multi-tenant in un data center locale Houston. Come espanso, con la società seguendo crescente difficoltà crescita da acquistare, il provisioning e mantenere tutti i componenti hardware e software necessari per supportare i clienti. IT personale per gestire le operazioni diventato un'altra bottiglia che ha causato un rallentamento delle nuove risorse di provisioning quindi attuare nuovi servizi per i clienti.

Software CSI analizzata opzioni cloud per eliminare il sovraccarico di tale tipo, in modo che sia possibile concentrarsi sul relativo codice, anziché le operazioni. La società ha rilevato molti provider di servizi di cloud superiore offrono solo le soluzioni di infrastruttura come servizio (IaaS) che richiedono un grande personale IT per gestire stack IaaS. In conclusione, Software CSI determinato che la soluzione di Azure PaaS era ottimale per le esigenze. Viene spiegato Molina "Azure Ottiene dall'area di lavoro, il software hardware e software in modo che è possibile focalizzare l'attenzione sulle offerte software riducendo il sovraccarico IT"

## <a name="making-the-transition-to-azure"></a>La transizione a Azure

Dopo aver selezionato Azure per la soluzione PaaS, Software CSI ha iniziato a eseguire la migrazione dell'infrastruttura di back-end e database nel cloud. Nelle versioni precedenti Azure, è necessario installare un'applicazione client che comunicati con un servizio Windows Communication Foundation (WCF) back-end SpectrumNG clienti. In base alle Molina, "anche se alcuni clienti ospitati tutti gli elementi in un Data Center è realizzare il prodotto sia multi-tenant. Sono ospitati tutti gli elementi in un Data Center Houston con SQL Server come archivio dati.

"Il nostro prodotto che offre anche incluso un membro esposto portale web (scritto in ASP.net), che è stato progettato per etichetta vuoti in base alla presenza sul web del cliente e una API SOAP per supportare le pagine in linea e qualsiasi integrazione di terze parti."

La migrazione nel cloud non hanno lunga per l'architettura. In base alle Molina, "La maggior parte dell'impegno gestito modificando il modo in cui è leggere informazioni sui file di configurazione, una modifica centralizzata della stringa di connessione e il salvataggio di un pacchetto di automazione, durante il caricamento e distribuzione dei rilasci."

Per sviluppare la compilazione di automazione, CSI ingegneri utilizzato PowerShell Azure e le API REST per creare pacchetti e caricarli in un ambiente di gestione temporanea release ogni notte.
Transizione complessiva alla distribuzione di Windows Azure basate su cloud causa rapidamente per il team IT Software CSI. Viene spiegato Molina "In tutti, è stato un ambiente beta nel cloud all'interno di tre a quattro settimane prendere nel progetto. Che è stato un positivi sorprendenti per noi."

Dopo la configurazione e l'ambiente di testing, Software CSI iniziata la migrazione di clienti. Se si utilizza già hosting CSI Software, la transizione è stato quasi continua. Per i clienti la migrazione da una distribuzione locale, alcune altre durata la migrazione nel cloud, ma è stata ancora principalmente senza problemi per i clienti e CSI Software.

Per i del nuovi clienti, Software CSI personale IT usarli la procedura seguente per bordo per Azure:

1.  Script di PowerShell Azure vengono usate per ruotare di un nuovo database per il cliente. tutti i clienti inizialmente su un livello di premium per garantire sufficiente effettiva iniziale per la transizione.
2.  Se possibile, Software CSI utilizza la migrazione guidata SQL Azure per spostare i dati esistenti a un'istanza di Database SQL Azure.
3.  Infine, Microsoft SQL Server Integration Services (SSIS) vengono utilizzate per risolvere eventuali differenze nei dati o eseguire eventuali pulizia dei dati in base alle esigenze.

Oggi, il 99% dei clienti CSI Software sono ospitati in Azure attraverso quattro Data Center locale (centrale del Nord, Sud centrale, est e ovest). Con i Data Center nell'area geografica di ogni cliente, latenza è ridurre al minimo.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Pool di database flessibile Azure liberare risorse IT

Alcune funzionalità di Azure hanno contribuito CSI Software MAIUSC vengano infrastruttura e operazioni incentrate la caratteristica e mirati. Ad esempio il vantaggio più importante è stata dal pool di database flessibile.

Software CSI fornisce attualmente circa 550 database per i clienti. Prima di flessibile pool, era difficile da gestire i database di molti all'interno di una struttura di livello. I responsabili di operazioni dovevano assegnare livelli di prestazioni in base alle esigenze burst dei clienti che obbligatorio significativa IT alla risorsa sovraccarico. Con i pool di database flessibile responsabili possono assegnare tenant premium o un pool standard, in base alle esigenze, quindi spostare i clienti in base alle dimensioni e necessario. I clienti sentire gli effetti del pool di database flessibile quasi immediatamente. prima di flessibile pool, clienti hanno timeout e altri problemi durante l'uso di burst periodi, ma con i pool flessibile, gli utenti possono riscontrare picchi di attività in base alle esigenze e possono continuare a usare SpectrumNG senza problemi.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure Active geografico replica accelera dei report

Diversi clienti CSI Software anche vantaggi di Azure Active geografico replica. Con Active geografico replica, fino a quattro leggibili database secondari possono essere configurati in aree del Data Center stesse o in altre. Software CSI utilizza di Active geografico replica in due modi: prima di tutto, i database secondari sono disponibili in caso di un'interruzione del Data Center o impossibilità di connettersi al database principale. e in secondo luogo, possono essere letti i database secondari e possono essere usati per trasferire i carichi di lavoro di sola lettura, ad esempio processi di report. Alcuni clienti Software CSI sfruttare questa per accelerare reporting flussi di lavoro.

## <a name="csi-software-application-logic-and-architecture"></a>Architettura e la logica dell'applicazione Software CSI

SpectrumNG vengono utilizzati i ruoli di web. Poiché l'applicazione è multi-tenant, un servizio WCF viene utilizzato per gestire la richiesta di connessione iniziale dei clienti. Molina, come indicato "la richiesta identifica ogni cliente, quindi consente di creare una stringa di connessione indietro per i database per qualsiasi elemento è necessario."

Per il livello web del servizio, Software CSI consente di sfruttare il ridimensionamento automatico Azure, in base al giorno e l'ora. Risorse disponibili sono automaticamente aumentate adattato maggiore utilizzo durante le ore lavorative, secondo fuso orario di ogni data center locale. Risorse anche impostare scalare verso il basso nei fine settimana, quando sono inferiori a esigenze del cliente.

     
![Architettura Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Un ruolo di lavoro di servizi cloud disegna dati strutturati dal Database SQL Azure e dati semistrutturati dallo spazio di archiviazione tabella. Gli utenti SpectrumNG interagiscono con ruolo web di servizi dati tramite una cloud.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Usa il web App e un livello di web piano per App per dispositivi mobili

Utilizzo di Database di SQL Azure liberare le risorse per CSI Software per consentire nuove iniziative, tra cui una piattaforma mobile completa in base a un'API personalizzata ospitata in Azure web app. La piattaforma consente i membri palestra e personale per dispositivi mobili consente di controllare le pianificazioni, prenotare le classi e ricevere messaggi.

La piattaforma utilizza architettura orientata ai servizi (SOA) per eseguire un singolo componente, ad esempio un sistema di punti vendito (POS) o un sistema di vendite, spostarlo in tempo reale in un altro piano web e quindi ruotare un servizio per supportare tale componente, lasciando tutti gli altri elementi nel piano web originale. Questa funzionalità offre flessibilità CSI Software e può essere utile mantenere bassi i costi.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Lo stato attivo gli sviluppatori di Software CSI sulle applicazioni e servizi consente di Azure

Database SQL Azure non appena un vantaggio per i clienti SpectrumNG, guardare il servizio veloce e affidabile, è anche un grande successo per CSI Software il personale IT e gli sviluppatori. Possibilità di scaricare operazioni in Azure nel cloud, il Software CSI ridurre il sovraccarico per infrastruttura e alle risorse, notevolmente accelerata relative cicli di sviluppo e non è più esigenze ai database micromanage per ottimizzare le prestazioni per il tenant.

## <a name="more-information"></a>Ulteriori informazioni

- Per ulteriori informazioni sui pool di database flessibile Azure, vedere [pool di database flessibile](sql-database-elastic-pool.md).

- Per altre informazioni su Strumenti database e flessibile proporzioni dei caratteri, vedere [Strumenti database flessibile e proporzioni dei caratteri flessibile](sql-database-elastic-scale-get-started.md).

- Per ulteriori informazioni sulla migrazione di un database SQL Server, vedere [Migrazione guidata SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Per ulteriori informazioni sulle Active geografico replica, vedere [La replica di geografico Active](sql-database-geo-replication-overview.md).

- Per ulteriori informazioni sui ruoli Web ruoli e di lavoro, vedere [ruoli di lavoro](../fundamentals-introduction-to-azure.md#compute). 

- Per ulteriori informazioni su Bus di servizio Azure, vedere [Bus di servizio Azure](https://azure.microsoft.com/services/service-bus/).

- Per ulteriori informazioni su ridimensionamento automatico, vedere [ridimensionare servizi cloud](../cloud-services/cloud-services-how-to-scale.md).
