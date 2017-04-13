<properties
    pageTitle="Monitoraggio delle prestazioni di database nel Database di SQL Azure | Microsoft Azure"
    description="Informazioni sulle opzioni disponibili per il database con strumenti Azure e viste a gestione dinamica di monitoraggio."
    keywords="database di monitoraggio delle prestazioni di database cloud"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitoraggio delle prestazioni di database nel Database di SQL Azure
Monitoraggio delle prestazioni di un database SQL di Azure inizia con l'utilizzo delle risorse rispetto il livello di prestazioni del database che si sceglie di monitoraggio. Monitoraggio consente di che determinare se il database ha una capacità in eccesso o sia verificando problemi perché le risorse sono il limite massimo e quindi decidere se è possibile regolare il livello di prestazioni e il [livello di servizio](sql-database-service-tiers.md) del database. È possibile monitorare il database utilizzando strumenti grafici nel [portale di Azure](https://portal.azure.com) o SQL [viste a gestione dinamica](https://msdn.microsoft.com/library/ms188754.aspx).

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorare i database tramite il portale di Azure

Nel [portale di Azure](https://portal.azure.com/), è possibile monitorare l'utilizzo del database un singolo selezionando il database e fare clic su grafico **monitoraggio** . Verrà visualizzata una finestra di **unità di misura metriche** che è possibile modificare facendo clic sul pulsante **Modifica grafico** . Aggiungere le metriche seguenti:

- Percentuale di CPU
- Percentuale DTU
- Percentuale di IO dati
- Percentuale delle dimensioni del database

Dopo aver aggiunto queste metriche, è possibile continuare a visualizzarle nel grafico **monitoraggio** con ulteriori dettagli nella finestra **unità di misura metriche** . Tutte le quattro metriche mostrano la percentuale di utilizzo medio rispetto **DTU** del database. Vedere l'articolo di [livelli di servizio](sql-database-service-tiers.md) per informazioni dettagliate su DTUs.

![Servizio di livello il monitoraggio delle prestazioni del database.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

È inoltre possibile configurare avvisi in misurazioni sulle prestazioni. Fare clic sul pulsante **Aggiungi avviso** nella finestra di **unità di misura metriche** . Seguire la procedura guidata per configurare l'avviso. Viene offerta la possibilità di avviso se le metriche superano una determinata soglia o se l'unità di misura metriche è inferiore a una determinata soglia.

Ad esempio, se si prevede che il carico di lavoro sul database di aumentare le dimensioni, è possibile configurare un avviso di posta elettronica ogni volta che il database raggiunge 80% in uno qualsiasi di misurazione delle prestazioni. È possibile utilizzare come un preavviso per stabilire quando potrebbe essere necessario passare a livello di prestazioni immediatamente superiore.

Misurazioni sulle prestazioni consentono di determinare se in grado di eseguire il downgrade a un livello inferiore di prestazioni. Si supponga che si usa un database S2 Standard e visualizzare tutti i dati sulle prestazioni del database in Media non viene utilizzata più di 10% in qualsiasi momento. È probabile che il database funzionerà correttamente in S1 Standard. Tuttavia, prestare carichi di lavoro che sovraccarico o variare prima di decidere per passare a un livello inferiore di prestazioni.

## <a name="monitor-databases-using-dmvs"></a>Database monitor con viste

La stessa esposti nel portale sono anche disponibili tramite le visualizzazioni di sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database di logica di tipo **master** del server e [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nel database utente. Utilizzare **sys.resource_stats** se è necessario controllare dati meno granulari attraverso un periodo di tempo. Utilizzare **sys.dm_db_resource_stats** se è necessario monitorare i dati più granulari all'interno di un intervallo di tempo più piccole. Per ulteriori informazioni, vedere [Indicazioni sulle prestazioni di Database SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **Sys.dm_db_resource_stats** restituisce un risultato vuoto impostato se usata in Web e Business database edition, vengano ritirati.

Per i pool di database flessibile, è possibile monitorare singoli database nel pool di con le tecniche descritte in questa sezione. Ma è anche possibile monitorare il pool nel suo insieme. Per informazioni, vedere [monitorare e gestire un pool di database flessibile](sql-database-elastic-pool-manage-portal.md).
