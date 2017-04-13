<properties
    pageTitle="Monitorare e gestire un pool di database flessibile con c# | Microsoft Azure"
    description="Usare tecniche di sviluppo database c# per gestire un pool di database flessibile Database SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Monitorare e gestire un pool di database flessibile con C & #x 23. 

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Informazioni su come gestire un [pool di database flessibile](sql-database-elastic-pool.md) utilizzando C & #x 23.. 

>[AZURE.NOTE] Molte nuove funzionalità di Database SQL sono supportate solo se si usa il [modello di distribuzione di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md), pertanto è necessario utilizzare sempre le ultime **raccolta di gestione Database SQL Azure per .NET ([documenti](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Le versioni precedenti [librerie basate su modello di distribuzione classica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sono supportati solo, per la compatibilità in modo che si consiglia di utilizzare le raccolte di Manager delle risorse in base a più recente.

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

- Un pool flessibile (pool di che si desidera gestire). Per creare un pool, vedere [creare un pool di database flessibile con c#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina di [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool flessibile

È possibile spostare un database autonomo avanti o indietro un pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Elenca i database in un pool di flessibile

Per recuperare tutti i database in un pool, chiamare il metodo [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Modificare le impostazioni delle prestazioni di un pool

Recuperare la proprietà pool esistenti. Modificare i valori ed eseguire il metodo CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latenza di operazioni relative al pool flessibile

- Modifica in genere eDTUs min per eDTUs database o max per ogni database completa in cinque minuti.
- Tempo per modificare la dimensione del pool (eDTUs) dipende dalla dimensione totale di tutti i database nel pool. Modifiche calcolare la media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale di tutti i database nel pool di 200 GB, quindi la latenza prevista per la modifica eDTU pool al pool di 3 ore o meno.




## <a name="additional-resources"></a>Risorse aggiuntive

- [Codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database](sql-database-develop-error-messages.md).
- [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API di gestione delle risorse Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Creare un nuovo pool di database flessibile con c#](sql-database-elastic-pool-create-csharp.md)
- [Quando utilizzare un pool di database flessibile](sql-database-elastic-pool-guidance.md)
- Vedere [proporzioni fuori con Database di SQL Azure](sql-database-elastic-scale-introduction.md): utilizzare strumenti database flessibile per scalabilità, spostare i dati della query o creare le transazioni.

