<properties
    pageTitle="Contatori delle prestazioni per il gestore di mappa condiviso"
    description="ShardMapManager classe e dati dipendenti routing contatori delle prestazioni"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Contatori delle prestazioni per il gestore di mappa condiviso

È possibile acquisire le prestazioni di un [manager mappa condiviso](sql-database-elastic-scale-shard-map-management.md), soprattutto quando si utilizza [il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md). Contatori vengono creati con metodi della classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Contatori vengono utilizzati per tenere traccia delle prestazioni di esecuzione delle operazioni [il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) . Questi contatori sono accessibili in Monitoraggio delle prestazioni della categoria "Flessibile Database: condiviso Management".

**Per la versione più recente:** Passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Vedere anche [aggiornare un'app per utilizzare la libreria di client flessibile database più recente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Prerequisiti

* Per creare la categoria prestazioni e contatori, l'utente deve essere una parte del gruppo **Administrators** locale nel computer in cui risiede l'applicazione.  

* Per creare un'istanza di un indicatore di prestazioni e aggiornare i contatori, l'utente deve essere membro del gruppo **Administrators** o **Performance Monitor Users** . 

## <a name="create-performance-category-and-counters"></a>Creazione di contatori e categoria delle prestazioni 

Per creare i contatori, chiamare il metodo CreatePeformanceCategoryAndCounters della [classe ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Solo gli amministratori possono eseguire il metodo: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

È possibile anche utilizzare [questo](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script di PowerShell per eseguire il metodo. Il metodo crea contatori riportati di seguito:  

* **Mapping cache**: numero di mapping cache per la mappa condiviso.
*  **DDR operazioni/sec**: tasso di operazioni routing dipendenti dei dati per la mappa condiviso. Questo contatore viene aggiornato quando una chiamata a [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) risultati in una connessione a condiviso destinazione. 
*  **Mappatura di ricerca dalla cache/secondo**: tasso di operazioni di ricerca cache esito negativo per mapping condiviso. 
*  **Mapping ricerca cache al secondo**: tasso di operazioni di ricerca non riuscito cache per mapping condiviso.
*  **Mapping aggiunto o aggiornato nella cache/sec**: frequenza con cui mapping vengono aggiunti o aggiornati nella cache per la mappa condiviso. 
*  **Mapping rimossi dalla cache/sec**: tasso quale i mapping vengono rimossi dalla cache per la mappa condiviso. 

Contatori vengono creati per ogni mappa condiviso memorizzati nella cache per ogni processo.  


## <a name="notes"></a>Note
Gli eventi seguenti attivano la creazione dei contatori delle prestazioni:  

* Inizializzare [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) con [caricamento immediato](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), se la ShardMapManager contiene le mappe condiviso. Ad esempio [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) e i metodi di [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Ricerca corretta di una mappa condiviso (con [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) o [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Creazione di mappa condiviso mediante CreateShardMap().

Verranno aggiornati i contatori da tutte le operazioni di cache eseguite il mapping condiviso e i mapping. La rimozione della mappa condiviso con DeleteShardMap () reults l'eliminazione dell'istanza contatori prestazioni.  

## <a name="best-practices"></a>Procedure consigliate

* Creazione della categoria prestazioni e contatori deve essere eseguita solo una volta prima della creazione dell'oggetto ShardMapManager. Ogni esecuzione del comando CreatePerformanceCategoryAndCounters() Cancella contatori precedenti (la perdita di dati segnalati da tutte le istanze) e consente di creare nuovi file.  

* Istanze del contatore prestazioni vengono create per ogni processo. Qualsiasi arresto anomalo dell'applicazione o rimozione di una mappa condiviso dalla cache determinerà l'eliminazione delle istanze di contatori delle prestazioni.  

### <a name="see-also"></a>Vedere anche

[Panoramica delle funzionalità Database flessibile](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

