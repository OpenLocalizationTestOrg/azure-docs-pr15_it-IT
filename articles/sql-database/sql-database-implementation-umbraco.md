<properties
   pageTitle="Azure Case Study - Umbraco di Database di SQL Azure | Microsoft Azure"
   description="Informazioni sulle modalità di utilizzo Database SQL di eseguire rapidamente il provisioning Umbraco e servizi scala migliaia di tenant nel cloud"
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
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utilizza il Database di SQL Azure ai servizi rapidamente proporzioni e le disposizioni per migliaia di tenant nel cloud

![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco è un sistema di gestione del contenuto di Apri origine popolari (CMS) che possa eseguire tutte da siti di brochure o campagna di piccole dimensioni per applicazioni complesse per le aziende Fortune 500 e siti Web multimediale globale. 

> "Sono molto una grande comunità di sviluppatori che utilizzano il sistema con più di 100.000 sviluppatori ai forum e più di 350.000 siti che sono dinamici, in esecuzione Umbraco."

> Ovvero Umbraco Christensen Morten, responsabile tecnico,

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Per semplificare distribuzioni del cliente, Umbraco aggiunto Umbraco-come-a-Service (UaaS): un'offerta di software-come-a-servizio che elimina la necessità di distribuzioni locali, fornisce proporzioni dei caratteri predefiniti e rimuove gestione sovraccarico consentendo agli sviluppatori per concentrarsi su gestione innovazione anziché soluzione del prodotto. Umbraco in grado di fornire tutti i vantaggi si basa su modello di (PaaS) della piattaforma come servizio flessibile offerto da Microsoft Azure.

UaaS consente agli utenti di SaaS di usare le funzionalità Umbraco CMS che in precedenza erano fuori loro portata. Questi clienti vengono effettuato il provisioning con un ambiente di CMS che include un database di produzione. I clienti possono aggiungere fino a due database aggiuntivi per lo sviluppo e ambienti di gestione temporanea, a seconda delle esigenze. Quando viene richiesto un nuovo ambiente, un processo automatizzato assegna al cliente un database automaticamente. Il nuovo database è pronto in secondi, perché il database sia già stato pre-preparato da Umbraco da un pool flessibile Azure dei database disponibili (vedere Figura 1).


![Ciclo di vita del provisioning Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Il provisioning di ciclo di vita per Umbraco come servizio (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Installazioni più semplici pool flessibile Azure e automazione

Con il Database di SQL Azure e altri servizi di Azure, clienti Umbraco possano il provisioning automatico ambienti e Umbraco può facilmente monitorare e gestire i database come parte di un flusso di lavoro intuitivo:

1.  Provisioning

    Umbraco mantiene una capacità di 200 database pre-provisioning disponibili dal pool flessibile. Quando un nuovo cliente si iscrive a UaaS, Umbraco fornisce al cliente con un nuovo ambiente CMS quasi in tempo reale tramite l'assegnazione di un database dal pool di disponibilità.

    Quando un pool di disponibilità raggiunge la soglia, viene creato un nuovo pool flessibile e nuovi database vengono pre-provisioning da assegnare ai clienti in base alle esigenze.

    Implementazione completamente automatizzata mediante le raccolte di gestione di c# e Bus di servizio Azure code.

2.  Utilizzare

    Utenti di utilizzare uno a tre ambienti (per produzione, gestione temporanea e/o sviluppo), ognuno con il proprio database. Database dei clienti sono in pool di database flessibile, che consente di Umbraco fornire efficiente proporzioni dei caratteri senza dover eccessiva effettuare il provisioning.

    ![Informazioni generali progetto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Dettagli progetto Umbraco](./media/sql-database-implementation-umbraco/figure3.png)

    Figura 2. Sito Web dei clienti Umbraco-come-a-Service (UaaS), che mostra i dettagli e informazioni generali progetto

    Database SQL Azure utilizza Database transazione unità (DTUs) per rappresentare il relativo potere necessario per le transazioni del database reale. Per i clienti UaaS, database funzionano in genere circa 10 DTUs, ma ogni ha la flessibilità scalare su richiesta. Questo significa che UaaS possibile assicurarsi che i clienti siano sempre le risorse necessarie, anche durante i periodi. Durante un evento di sport di domenica recente, ad esempio un database di esperti cliente UaaS picchi fino a 100 DTUs per la durata del gioco. Azure pool flessibile stata Umbraco supportare tale richiesta elevata senza riduzione delle prestazioni.

3.  Monitor

    Monitor Umbraco database attività utilizzando dashboard all'interno del portale di Azure, insieme avvisi tramite posta elettronica personalizzato.

4.  Ripristino di emergenza

    Azure offre due opzioni di ripristino di emergenza (DR): la replica di geografico Active e il ripristino geografico. L'opzione DR che è necessario selezionare una società dipende dei propri [obiettivi di continuità aziendale](sql-database-business-continuity.md).

    La replica di geografico Active offre il livello più veloce di risposta in caso di inattività. Utilizzo della replica geografico attiva, è possibile creare fino a quattro dei secondari leggibili su server in diverse aree geografiche e, è possibile avviare il controllo a uno dei database secondario in caso di errore.

    Umbraco non richiede la replica geografico, ma usufruire di Azure geografico ripristino per garantire il tempo di inattività minimo in caso di interruzione. Ripristino geografico si basa su Backup database in geografico ridondanti dello spazio di archiviazione Azure. Che consente agli utenti per il ripristino di una copia di backup quando è presente un'interruzione nell'area principale.

5.  Disposizione disattivazione

    Quando si elimina un ambiente del progetto, qualsiasi database associati (sviluppo, gestione temporaneo o live) vengono rimossi durante la pulizia coda Bus di servizio Azure. Questo processo automatizzato Ripristina database inutilizzati al pool di disponibilità del database flessibile del Umbraco, rendendoli disponibili per il provisioning futuri mantenendo utilizzo massimo.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Pool flessibile consentire UaaS scalare con facilità

Grazie del pool di database flessibile Azure, Umbraco possibile ottimizzare le prestazioni per i clienti senza dover eccesso o in difetto disposizione. Umbraco è attualmente fino a 3.000 database da una serie di database flessibile 19, con la possibilità di scalabilità per adattarli uno dei 325,000 clienti esistenti o nuovi clienti che hanno pronti per distribuire un CMS nel cloud.

Infatti, in base alle Morten Christensen, Technical Lead presso Umbraco, "UaaS è ora riscontrando aumento di circa 30 nuovi clienti al giorno. I clienti sono entusiasti grazie alla possibilità di essere in grado di effettuare il provisioning di nuovi progetti in secondi, pubblicare immediatamente gli aggiornamenti ai propri siti live da un ambiente di sviluppo utilizzando "fare clic su una distribuzione" e apportare modifiche altrettanto rapidamente se si trova errori."

Se un cliente non richiede un ambiente secondo o terzo più, è sufficiente rimuovere tali ambienti. Che consente di liberare risorse che possono essere usate per gli altri utenti come parte del pool di disponibilità del database flessibile Umbraco.

![Architettura di distribuzione Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Architettura di distribuzione UaaS in Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>Il percorso dalla Data Center nel cloud

Quando gli sviluppatori Umbraco inizialmente deciso di passare a un modello SaaS, erano la necessità un modo conveniente e scalabilità per creare il servizio.

> "Pool di database flessibile sono perfetti per offrire il nostro SaaS perché è possibile comporre capacità alto e verso il basso in base alle esigenze. Il provisioning è facile e con la configurazione, è possibile mantenere utilizzo al massimo."

> Ovvero Umbraco Christensen Morten, responsabile tecnico,

"Abbiamo voluto il tempo per la risoluzione dei problemi dei clienti, non la gestione dell'infrastruttura. Si desidera consentire facilmente ai clienti di ottenere il massimo,"indica Niels Hartvig, fondatore di Umbraco. "È considerato inizialmente hosting server noi, ma la pianificazione della capacità avrebbe un incubo." Casualmente, Umbraco non utilizzano agli amministratori di database, che caratteri di sottolineatura una proposta di valore chiave per l'uso di UaaS.

Uno degli obiettivi importante per gli sviluppatori Umbraco è stato in modo che per i clienti di UaaS effettuare il provisioning ambienti rapidamente e senza limiti di capacità. Ma fornisce un servizio di hosting dedicato nei data center Umbraco avrebbe richiesto numerose capacità in eccesso per gestire picchi di elaborazione. Che avrebbe voluto dire aggiunta un'infrastruttura di elaborazione notevole che si sono state regolarmente scarso utilizzo.

Inoltre, il team di sviluppo Umbraco desiderava una soluzione che consentirne di riutilizzare al meglio il proprio codice esistente come possibili. Lo sviluppatore Umbraco stati Mikkel Madsen, "siamo soddisfatti gli strumenti di sviluppo Microsoft siamo già abituati, ad esempio Microsoft SQL Server, Database SQL di Microsoft Azure, ASP.net e Internet Information Services (IIS). Prima di investire in una IaaS o un PaaS cloud soluzione, si desidera verificare che supporto per gli strumenti di Microsoft e piattaforme, in modo che non è necessario apportare modifiche rilevanti al nostro codice base. "

Per soddisfare tutti i criteri, Umbraco la ricerca per cercare un partner cloud con i requisiti seguenti:

-   Sufficienti capacità e affidabilità
-   Supporto per gli strumenti di sviluppo di Microsoft, in modo che gli ingegneri Umbraco non devono necessariamente completamente reinventare ambiente di sviluppo
-   Presenza in tutti i mercati in cui UaaS in competizione (aziende hanno bisogno per garantire che possano accedere rapidamente i dati e che i dati vengono memorizzati in un percorso che soddisfi i requisiti normativi regionali)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Perché Umbraco scelto Azure per UaaS

In base alle Morten Christensen "dopo aver considerato tutte le opzioni, l'opzione selezionata Azure perché soddisfatti tutti i criteri, da gestibilità e scalabilità per le opzioni famigliari ed efficacia. È necessario impostare gli ambienti in macchine virtuali di Azure ogni ambiente è la propria istanza di Database SQL Azure con tutte le istanze in pool database flessibile. Separando database tra ambienti di sviluppo, gestione temporanea e live, possiamo offrire ai clienti di isolamento prestazioni solida trovata una corrispondenza scala, ovvero un enorme successo. "

Morten persiste, "prima, era necessario manualmente il provisioning dei server di database web. A questo punto, non è necessario considerare il. Tutti gli elementi automatizzato, ovvero dal provisioning di pulizia. "

Morten è soddisfatti anche con le proporzioni funzionalità offerte da Azure. "Pool di database flessibile sono perfetti per offrire il nostro SaaS perché è possibile comporre capacità alto e verso il basso in base alle esigenze. Il provisioning è facile e con la configurazione, è possibile mantenere utilizzo al massimo." Stati morten, "la semplicità di pool flessibile, insieme a garanzia di servizio basato su livello DTUs offre la possibilità di effettuare il provisioning di nuovi pool di risorse su richiesta. Recente, uno dei propri clienti maggiore un massimo di 100 DTUs nel proprio ambiente live. Utilizzo di Azure, il nostro pool flessibile forniti database del cliente con le risorse necessarie in tempo reale senza dover prevedere requisiti DTU. In poche parole, clienti ottengono il tempo che si aspettano e poter soddisfare i contratti di servizio delle prestazioni."

Mikkel Madsen rappresenta la somma: "Sono state adottato l'algoritmo di Azure potente che si connette uno scenario di SaaS comune (onboarding nuovi clienti in tempo reale in scala) per il modello di applicazione (pre-il provisioning di database, entrambi sviluppo e live) nella parte superiore della tecnologia sottostante (con code Azure Bus di servizio in combinazione con Database SQL Azure)."

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Con Azure, UaaS supera le aspettative del cliente

Dopo la scelta di Azure come partner cloud Umbraco ha potuto offrire UaaS ottimizzata prestazioni di gestione del contenuto, senza l'investimento di risorse IT necessario da una soluzione indipendente. Come Morten dice "è già abituati la semplicità di utilizzo di sviluppo e scalabilità Azure offre e clienti sono thrilled con le caratteristiche e l'affidabilità. In generale, che siano trascorse un vero successo per noi!"
 
## <a name="more-information"></a>Ulteriori informazioni

- Per ulteriori informazioni sui pool di database flessibile Azure, vedere [pool di database flessibile](sql-database-elastic-pool.md).

- Per ulteriori informazioni su Bus di servizio Azure, vedere [Bus di servizio Azure](https://azure.microsoft.com/services/service-bus/).

- Per ulteriori informazioni sui ruoli Web ruoli e di lavoro, vedere [ruoli di lavoro](../fundamentals-introduction-to-azure.md#compute). 

- Per ulteriori informazioni sulle reti virtuali, vedere [la rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Per ulteriori informazioni sulla copia di backup e ripristino, vedere [la continuità aziendale](sql-database-business-continuity.md).  

- Per ulteriori informazioni su monitoring ppols, vedere [monitoraggio pool](sql-database-elastic-pool-manage-portal.md). 

- Per ulteriori informazioni sulle Umbraco come servizio, vedere [Umbraco](https://umbraco.com/cloud).

