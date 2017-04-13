<properties
    pageTitle="Opzioni e le prestazioni del Database di SQL: livelli di servizio | Microsoft Azure"
    description="Confronto tra SQL Database prestazioni e business continuità caratteristiche dei livelli di servizio per bilanciare costo e le funzionalità quando si adatta."
    keywords="opzioni di database, le prestazioni del database"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opzioni di Database SQL e prestazioni: informazioni sulle funzionalità disponibili in ogni livello di servizio

[Database SQL Azure](sql-database-technical-overview.md) offre tre livelli di servizio con più livelli di prestazioni di gestire diversi carichi di lavoro. Ogni livello di prestazioni fornisce un set di aumento delle risorse progettato per offrire velocità sempre più alti. È possibile gestire ogni database nel proprio [livello di servizio](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) con il proprio livello di prestazioni. È anche possibile gestire più database in un [pool di flessibile](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) con un set di risorse condiviso. Le risorse disponibili per i database autonomo sono espressi in termini di unità di transazione Database (DTUs) e per i pool flessibile in termini di flessibile DTUs o eDTUs. Per ulteriori informazioni su DTUs ed eDTUs, vedere [che cos'è un DTU](sql-database-what-is-a-dtu.md). 

In entrambi i casi, i livelli di servizio includono **base**, **Standard**e **Premium**. Opzioni di database nei livelli seguenti sono simili per database autonomo e flessibile pool, ma sono disponibili ulteriori considerazioni per i pool flessibile. In questo articolo fornisce dettagli dei livelli di servizio per i database autonomo e flessibile pool.

## <a name="service-tiers-and-database-options"></a>Livelli di servizio e le opzioni del database
Standard, di base e livelli di servizio Premium tutti dispongono di un contratto di servizio del 99,99% dei tempi di attività e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibile, funzionalità di sicurezza e la fatturazione oraria. Nella tabella seguente vengono forniti esempi dei livelli migliori adatti per carichi di lavoro di applicazione diverso.

| Livello di servizio | Carichi di lavoro di destinazione |
|---|---|
| **Base** | Adatte per un database di piccole dimensioni, in genere una sola active operazione in un determinato momento di supporto. Esempi di database utilizzati per lo sviluppo o test o piccole dimensioni raramente utilizzato nelle applicazioni. |
| **Standard** | L'opzione Vai a per la maggior parte delle applicazioni di cloud, che supportano più query simultanee. Esempi di applicazioni web o del gruppo di lavoro. |
| **Premium** | Progettato per regolare il volume elevato transazione, il supporto di molti utenti concorrenti e che richiedono il livello più alto continuità delle funzionalità di business. Esempi sono database che supportano applicazioni critiche. |

>[AZURE.NOTE] Edizioni Web e Business vengano ritirate. Leggere le [Domande frequenti su tramonto](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se si prevede di continuare a usare le edizioni Web e Business.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Livelli di servizio autonomo database e livelli di prestazioni
Per i database autonomo, sono disponibili più livelli di prestazioni all'interno di ogni livello di servizio. Si dispone della flessibilità necessaria per scegliere il livello più adatta alle esigenze del carico di lavoro. Se è necessario ridimensionare verso l'alto o verso il basso, è possibile modificare facilmente i livelli del database. Per informazioni dettagliate, vedere [Modifica livelli del servizio di Database e livelli di prestazioni](sql-database-scale-up.md) .

Caratteristiche delle prestazioni elencate di seguito è applicabile ai database creati con [SQL Database V12](sql-database-v12-whats-new.md). Indipendentemente dal numero di database ospitato, il database Ottiene un insieme garantito di risorse e le caratteristiche di prestazioni del database non sono interessate.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Per informazioni su tutte le altre righe in questa tabella livelli di servizio, vedere [limiti e funzionalità di servizi di livello](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Livelli di servizio pool flessibile e prestazioni ottimali in eDTUs
Oltre alle creazione e il ridimensionamento di un database autonomo, è inoltre possibile gestire più database all'interno di un [pool di flessibile](sql-database-elastic-pool.md). Tutti i database in un pool di flessibile condividono un insieme comune di risorse. Caratteristiche delle prestazioni vengono misurate dal *Flessibile Database transazione unità* (eDTUs). Come con i database autonoma, pool sono disponibili in tre livelli di servizio: **base** **Standard**e **Premium**. Per i pool, questi tre livelli ancora definiscono i limiti di prestazioni e alcune funzionalità.

Pool di consentono ai database di condividere e utilizzare le risorse DTU senza la necessità di assegnare un livello di prestazioni specifici per ogni database nel pool. Ad esempio, un database autonomo in un pool Standard è possibile passare da con 0 eDTUs a eDTU massime database impostate quando si configura il pool. Pool di consentono a più database con diversi carichi di lavoro per un utilizzo efficiente eDTU risorse disponibili per tutte le risorse. Per informazioni dettagliate, vedere [considerazioni di prezzo e prestazioni di un pool flessibile](sql-database-elastic-pool-guidance.md) .

Nella tabella seguente descrive le caratteristiche dei livelli di servizio pool.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ogni database di un pool anche aderisce alle caratteristiche di database autonomo per quel livello. Ad esempio, il pool di base ha un limite per le sessioni di max al pool di 4800 28800, ma un singolo database all'interno di un pool di base ha un limite di database di 300 sessioni.

## <a name="choosing-a-service-tier"></a>Scelta di un livello di servizio

Per scegliere un livello di servizio, stabilire innanzitutto se il database da un database autonomo o far parte di un pool flessibile. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Scelta di un livello di servizio per un database autonomo

Per scegliere un livello di servizio per un database autonomo, stabilire innanzitutto le caratteristiche di database che è necessario scegliere l'edizione di Database SQL:

- Dimensioni database (2 GB massimo di base, massimo di 250 GB per Standard e 500 GB a 1 TB massimo per Premium, a seconda del livello di prestazioni)
- Periodo di conservazione backup database (7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium)

Dopo aver determinato l'edizione di Database SQL, si è pronti determinare il livello di prestazioni per il database (il numero di DTUs). Si può immaginare e quindi [scalare verso l'alto o verso il basso in modo dinamico](sql-database-scale-up.md) in base a valori effettivi. Utilizzare la [Calcolatrice DTU](http://dtucalculator.azurewebsites.net/) per l'arrotondamento il numero di DTUs necessari. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Scelta di un livello di servizio per un pool di database flessibile.

Per determinare il livello di servizio per un pool di database flessibile, stabilire innanzitutto le caratteristiche di database che è necessario scegliere il livello di servizio per il pool.

- Dimensioni database (2 GB per Basic 250 GB per Standard e 500 GB per Premium)
- Periodo di conservazione backup database (7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium)
- Numero di database per pool (400 per Basic 400 per Standard e 50 per Premium)
- Massimo dello spazio di archiviazione al pool (117 GB per Basic, 1200 per Standard e 750 per Premium)

Dopo aver determinato il livello di servizio per il pool, si è pronti determinare il livello di prestazioni per il pool (eDTUs). Si può immaginare e quindi [scalare o modificare la scala verso il basso in modo dinamico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) in base a valori effettivi. È possibile utilizzare la [Calcolatrice DTU](http://dtucalculator.azurewebsites.net/) per l'arrotondamento il numero di DTUs necessarie per ogni database di un pool per determinare il valore massimo per il pool.

## <a name="next-steps"></a>Passaggi successivi
- Maggiori informazioni sui prezzi per questi livelli sui [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Per informazioni dettagliate del [pool flessibile](sql-database-elastic-pool-guidance.md) e [prezzo e prestazioni considerazioni pool flessibile](sql-database-elastic-pool-guidance.md).
- Informazioni su come [Monitor, gestire e ridimensionare i pool flessibile](sql-database-elastic-pool-manage-portal.md) e [le prestazioni dei database autonomo](sql-database-single-database-monitor.md).
- Ora che si conoscono i livelli di Database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) e imparare [a creare il primo database di SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Modelli di progettazione per le applicazioni SaaS multi-tenant utilizzando il Database di SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Corso video Microsoft Virtual Academy sulle funzionalità di database flessibile nel Database di SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
