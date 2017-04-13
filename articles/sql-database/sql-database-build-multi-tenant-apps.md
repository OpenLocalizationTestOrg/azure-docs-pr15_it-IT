<properties
   pageTitle="Database SQL Azure consente di creare applicazioni multi-Tenant con isolamento e l'efficienza"
   description="Altre modalità di compilazione di applicazioni multi-tenant Database SQL"
   keywords=""
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
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Consente di creare applicazioni multi-tenant con Database SQL Azure con isolamento e l'efficienza

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Sfruttare i pool flessibile e creazione di applicazioni multi-tenant più efficiente

Se si è un dev SaaS la scrittura di un'app multi-tenant e gestione di molti clienti, spesso si rende compromessi nel cliente prestazioni, gestione e sicurezza. Con Azure SQL Database flessibile Database pool, non è più necessario assicurarsi che. Questi pool consentono di gestire e monitorare le app multi-tenant e ottenere vantaggi di isolamento del cliente al database. Vedere [modelli di progettazione per applicazioni multi-tenant SaaS con Database SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![App tenant di più build](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>È possibile controllare il ridimensionamento automatico

Pool di adattamento automatico delle prestazioni e capacità di archiviazione per i database flessibile in tempo reale. È possibile controllare le prestazioni assegnate al pool, aggiungere o rimuovere database flessibile su richiesta e definire le prestazioni dei database flessibile senza influenzare il costo totale del pool di. Di conseguenza, che non è necessario preoccuparsi di gestire l'utilizzo di singoli database.

[Leggere la documentazione](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Gestione intelligente dell'ambiente

Consigli predefiniti ridimensionamento tempestiva identificano i database in grado di sfruttare dal pool. Queste raccomandazioni consentono "" analisi di simulazione per l'ottimizzazione veloce soddisfare i propri obiettivi di prestazioni. Prestazioni RTF monitoraggio e risoluzione dei problemi dashboard consentono di visualizzare utilizzo cronologici pool.

[Leggere la documentazione](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Prestazioni e prezzi per soddisfare le esigenze

Standard, di base e pool Premium forniscono un'ampia gamma di prestazioni, lo spazio di archiviazione e prezzi opzioni. Pool possono contenere fino a 400 database flessibile. Database flessibile possono ridimensionamento automatico tutte le unità di transazione database flessibile 1000 (eDTU).

[Leggere la documentazione](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Strumenti flessibile

Oltre al pool di flessibile, sono disponibili caratteristiche di Database SQL per gestire attività operative su più database:

**Eseguire query tra database e creazione di report.**  
[Query di database flessibile](sql-database-elastic-query-overview.md) consente di eseguire query o report su database in pool di flessibile e accesso remoto ai dati archiviato nella maggior parte dei database del pool di contemporaneamente.

**Eseguire le transazioni del database cross.**  
[Le transazioni del database flessibile](sql-database-elastic-transactions-overview.md) consentono di eseguire operazioni che includono diversi database nei database SQL ed eseguono operazioni (ad esempio durante l'elaborazione di operazioni finanziarie tra database, o l'aggiornamento inventario in un database e ordini).

**Eseguire le stesse operazioni in diversi database.**  
[Processi database flessibile](sql-database-elastic-jobs-overview.md) eseguire le operazioni amministrative, ad esempio la ricostruzione degli indici o aggiornamento di schemi per ogni database nel pool di flessibile.

Passare alla home page di per vedere altri Database di SQL sono riportate le novità per l'offerta.
[Estrarre il file](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere una [sottoscrizione gratuita Azure](https://azure.microsoft.com/get-started/) e [creare il primo Database di SQL Azure](sql-database-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive

Esplorare tutte le [funzionalità di Database SQL](https://azure.microsoft.com/services/sql-database/).
 
Esaminare la [Panoramica tecnica di Database SQL](sql-database-technical-overview.md).  
