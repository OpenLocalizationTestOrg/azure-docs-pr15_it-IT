<properties
    pageTitle="Che cos'è il Database di SQL? Introduzione al Database SQL | Microsoft Azure"
    description="Per un'introduzione al Database SQL: dettagli tecnici e le funzionalità di Microsoft relazionali di database di sistema di gestione (RDBMS) nel cloud."
    keywords="Introduzione a sql, Introduzione a sql, che cos'è database sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>Che cos'è il Database di SQL? Introduzione al Database SQL

Database SQL è un servizio di database relazionale nel cloud basato sul modulo di Microsoft SQL Server avanguardia, con funzionalità critiche. Database SQL offre prestazioni prevedibili, scalabilità senza tempo di inattività, continuità aziendale e protezione dei dati, ovvero con amministrazione vicino a zero. Consente di tracciare lo sviluppo di app rapido e accelerare il tempo di mercato, invece di gestione dell'infrastruttura e macchine virtuali. Poiché è basato sul motore di [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , Database SQL supporta strumenti SQL Server esistente, raccolte e API, che semplifica spostare ed estendere i nel cloud.

In questo articolo è un'introduzione ai concetti di base di Database SQL e le funzionalità relative alle prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Se si è pronti per passare, è possibile [creare il primo database di SQL](sql-database-get-started.md) o [creare un pool di database flessibile](sql-database-elastic-pool-create-portal.md) in minuti. Se si desidera ulteriori approfondimenti sugli, guardare questo video di 30 minuti.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Regolare le prestazioni e la scala senza tempi di inattività

Database SQL è disponibile in Basic, Standard e Premium *livelli di servizio*. Ogni livello di servizio offre [diversi livelli di prestazioni e capacità](sql-database-service-tiers.md) per supportare lightweight a carichi di lavoro di database ad alta densità. È possibile creare la prima app in un database di piccole dimensioni per alcuni bucks mese, quindi [modificare il livello di servizio](sql-database-scale-up.md) manualmente o a livello di programmazione in qualsiasi momento durante l'app scontato sono in tutto il mondo, i tempi di inattività per l'app e i clienti.

Per molti imprese e App, la possibilità di creare database e comporre le prestazioni del database singolo verso l'alto o verso il basso su richiesta è sufficiente, soprattutto se criteri di utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevisti, è possibile renderla difficile da gestire i costi e il modello aziendale.

[Pool flessibile](sql-database-elastic-pool.md) in Database SQL di risolvere il problema. Il concetto è semplice. Allocare prestazioni a un pool e pagare le prestazioni collettive del pool di piuttosto che le prestazioni del database singola. Non è necessario comporre le prestazioni del database verso l'alto o verso il basso. I database in pool, denominato *database flessibile*, adattamento automatico alto e verso il basso esigenze. Database flessibile utilizzano ma non deve superare i limiti del pool, in modo che il costo rimane prevedibile anche se non è l'uso di database. Inoltre, è possibile [aggiungere e rimuovere i database al pool](sql-database-elastic-pool-manage-portal.md), proporzioni dei caratteri l'app da alcuni dei database a migliaia di tutto all'interno di un budget che è possibile controllare. Per ulteriori informazioni sui modelli di progettazione per applicazioni SaaS utilizzando pool flessibile, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant con Database di SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

In entrambi i casi di continuare, ovvero singolo o flessibile, ovvero non vincolare. È possibile fusione singoli database con i pool di database flessibile e modificare i livelli di servizio di singoli database e pool per creare progettazioni innovativi. Inoltre, con la potenza e portata di Azure, è possibile sconto e i servizi Azure con Database di SQL per soddisfare le esigenze di progettazione univoca per l'app moderna, unità efficienza costo e delle risorse e sbloccare nuove opportunità commerciali.

Ma come è possibile confrontare le prestazioni relative di database e pool di database? Come si capisce destra fare clic su Interrompi quando ci si connette alto e verso il basso La risposta è Database transazione unità (DTU) per i database singoli DTU flessibile (eDTU) per database flessibile e pool di database. Vedere [prestazioni e le opzioni del Database di SQL: informazioni sulle funzionalità disponibili in ogni livello di servizio](sql-database-service-tiers.md) per informazioni dettagliate.

## <a name="keep-your-app-and-business-running"></a>Mantenere l'app e business in esecuzione

Settore iniziale 99,99% disponibilità contratto di servizio del Azure [(contratto di servizio)](http://azure.microsoft.com/support/legal/sla/), con una rete globale del Data Center gestite Microsoft, consente di mantenere l'app in esecuzione 24/7. Con ogni database SQL, si sfruttare la protezione dei dati predefiniti, tolleranza e protezione dei dati che in caso contrario è necessario progettare, acquistare, creare e gestire. Anche in questo caso, a seconda delle esigenze dell'azienda, si possono chiedere ulteriori livelli di protezione per assicurarsi che l'app e dell'azienda consente di recuperare rapidamente in caso di emergenza, un messaggio di errore o un altro elemento. Con Database di SQL, ogni livello di servizio offre un menu diverso delle caratteristiche è possibile utilizzare per la configurazione e in esecuzione e restare in questo modo. È possibile usare in un momento Ripristina per ripristinare un database a uno stato precedente, fino alla versione di 35 giorni. Inoltre, se la Data Center che ospita i database si verifica un'interruzione del servizio, è possibile il controllo a repliche di database in un'area diversa. È possibile utilizzare repliche degli aggiornamenti o trasferimento per aree geografiche diverse.

![Geografico-replica di Database SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Per ulteriori informazioni sulle caratteristiche di continuità aziendale diversi disponibili per i livelli di servizio diverso, vedere [La continuità aziendale](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Protezione dei dati
SQL Server deve garantire a tinta unita della protezione dei dati che rispetta i Database SQL con caratteristiche che consentono di monitorare l'attività, proteggere i dati e limitare l'accesso. Vedere [la protezione del database SQL](sql-database-security.md) per una rapida panoramica delle opzioni di protezione che si dispone di Database SQL. Visitare il [Centro protezione per il motore di Database di SQL Server e Database di SQL](https://msdn.microsoft.com/library/bb510589) per una visualizzazione più completa di funzionalità di sicurezza. E visitare il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/security/) per informazioni sulla protezione di piattaforma di Azure.

## <a name="next-steps"></a>Passaggi successivi
Un'introduzione al Database SQL di leggere e rispondere alla domanda "Qual è il Database SQL?", si è pronti per:

- Vedere la [pagina prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per singolo database e database flessibile costo confronti e di calcolo.
- Informazioni sui [pool flessibile](sql-database-elastic-pool.md).
- Iniziare a [creare il primo database](sql-database-get-started.md).
- [Connettersi ed eseguire query con SQL Server Management Studio](sql-database-connect-query-ssms.md)
- Creazione della prima applicazione c#, Java, Node, PHP, Python o trascrizione: [raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md)
- Visualizzare un indice dei titoli e descrizioni di [tutti gli argomenti per il servizio di database sql Azure](sql-database-index-all-articles.md).
