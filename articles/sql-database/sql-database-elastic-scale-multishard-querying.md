<properties 
    pageTitle="Query su più condiviso | Microsoft Azure" 
    description="Eseguire query su shards utilizzando la libreria di client database flessibile." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Query su più condiviso

## <a name="overview"></a>Panoramica

Con gli [Strumenti di Database flessibile](sql-database-elastic-scale-introduction.md), è possibile creare soluzioni sharded database. **Query su più condiviso** viene usato per attività, ad esempio raccolta/relazioni sui dati che richiedono l'esecuzione di una query che si estende su shards diversi. (Diversamente per [il routing dipendenti dai dati](sql-database-elastic-scale-data-dependent-routing.md), che esegue tutto il lavoro su un singolo condiviso.) 

## <a name="overview"></a>Panoramica

1. È possibile ottenere un [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) o [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) mediante [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)o il metodo [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Vedere [**la creazione di un ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [**ottenere un RangeShardMap o ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Creare un oggetto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Creare un **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Impostare la **[proprietà CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** su un comando T-SQL.
3. Eseguire il comando chiamando il **[metodo ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Visualizzare i risultati utilizzando la **[classe MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Esempio

Il codice seguente viene illustrato l'utilizzo di condiviso più query che utilizzano un determinato **ShardMap** denominato *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
La differenza principale è la creazione di connessioni più condiviso. Dove **SqlConnection** opera su un solo database, **MultiShardConnection** utilizza un ***insieme di shards*** come input. Compilare l'insieme di shards da una mappa condiviso. La query viene quindi eseguita sull'insieme di shards utilizzando la semantica **UNION ALL** per assemblare un singolo risultato complessivo. Facoltativamente, il nome del condiviso in cui la riga deriva da può essere aggiunti all'output utilizzando la proprietà **ExecutionOptions** sul comando. 

Nota la chiamata a **myShardMap.GetShards()**. Questo metodo recupera tutti shards dalla mappa condiviso e offre un modo semplice per eseguire una query in tutti i database pertinenti. L'insieme di shards per una query più condiviso può essere suddivise ulteriormente eseguendo una query LINQ sull'insieme restituiti dalla chiamata a **myShardMap.GetShards()**. In combinazione con i criteri di risultati parziali, la funzionalità corrente in condiviso con più query è stata progettata per adatte decine fino a centinaia di shards.

Un limite di query più condiviso è attualmente la mancanza di convalida per shards e shardlets che vengono eseguite query. Mentre del routing dipendente dai dati viene verificato che una determinata condiviso fa parte della mappa condiviso al momento dell'invio di query, le query più condiviso non eseguire questo controllo. Questo può comportare condiviso più query in esecuzione nel database che sono state rimosse dalla mappa condiviso.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Query più condiviso e le operazioni di unione di divisione

Query più condiviso non verificare se shardlets il database delle query fanno parte di operazioni di divisione unione in corso. (Vedere [proporzioni utilizzando lo strumento di unione Dividi Database flessibile](sql-database-elastic-scale-overview-split-and-merge.md)). Questo può comportare incoerenze nel punto in cui le righe di shardlet stesso mostrano relative a più database nella stessa query più condiviso. Tenere presenti queste limitazioni e valutare la possibilità di svuotamento operazioni divisa unione in corso e sulle modifiche apportate alla mappa condiviso durante l'esecuzione di query più condiviso.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Vedere anche
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** esistenti.


Gestire shards utilizzando la [libreria client Database flessibile](sql-database-elastic-database-client-library.md). Include uno spazio dei nomi chiamato [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) che offre la possibilità di shards più utilizzando una singola query e il risultato della query. Fornisce un'astrazione query su un insieme di shards. Fornisce inoltre criteri di esecuzione alternativo, in particolare parziali risultati ottimali, di occuparsi di errori durante la ricerca su shards molti.  

 