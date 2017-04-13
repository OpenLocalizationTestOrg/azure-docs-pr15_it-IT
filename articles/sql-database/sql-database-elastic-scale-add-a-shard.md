<properties 
    pageTitle="Aggiunta di un condiviso con Strumenti database flessibile | Microsoft Azure" 
    description="Imposta come usare flessibile scala API per aggiungere nuovi shards a un condiviso." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Aggiunta di un condiviso con strumenti Database flessibile

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Per aggiungere un condiviso per un nuovo intervallo o una chiave  

Applicazioni, spesso è necessario aggiungere semplicemente shards nuovo per gestire i dati che si prevede di nuove chiavi o intervalli di chiavi, per una mappa condiviso già esistente. Ad esempio, un'applicazione sharded per Tenant ID potrebbe essere necessario eseguire il provisioning di un nuovo condiviso per un nuovo tenant o mensile sharded dati potrebbe essere necessario un nuovo condiviso viene completato il provisioning prima dell'inizio di ogni nuovo mese. 

Se il nuovo intervallo di valori di chiave non è già parte di un mapping esistente, è molto semplice aggiungere il nuovo condiviso e associare la nuova chiave o l'intervallo per tale condiviso. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Esempio: aggiunta di un condiviso e l'intervallo di una mappa condiviso esistente
In questo esempio viene utilizzata la [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) metodi e crea un'istanza della classe [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . Nell'esempio riportato di seguito sono stati creati un database denominato **sample_shard_2** e tutti gli oggetti schema necessarie all'interno di essa per mettere in attesa intervallo [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


In alternativa, è possibile utilizzare Powershell per creare un nuovo Manager mappa condiviso. È disponibile un esempio [di seguito](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Per aggiungere un condiviso per un'area vuota di un intervallo esistente  

In alcuni casi potrebbe già mappato un intervallo a un condiviso e parzialmente compilato con i dati, ma ora è imminenti dati vengano indirizzati a un condiviso diverso. Ad esempio, si condiviso dall'intervallo di giorni e già allocati 50 giorni a una condiviso, ma il giorno 24, si desidera dati in futuro a terra in un diverso condiviso. Lo database flessibile [strumento di unione di divisione](sql-database-elastic-scale-overview-split-and-merge.md) è possibile eseguire questa operazione, ma se lo spostamento dei dati non è necessario (ad esempio dati per l'intervallo di giorni che vanno [25, 50), ad esempio, non esiste ancora giorno 25 inclusi a vicenda, 50) è possibile eseguire questa operazione interamente mediante API di gestione di mappa condiviso direttamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Esempio: divisione di un intervallo e assegnazione a un condiviso appena aggiunto un parte vuota

Sono stati creati un database denominato "sample_shard_2" e tutti gli oggetti schema necessarie all'interno di essa.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: utilizzare questa tecnica solo se si è certi che l'intervallo per il mapping aggiornato non contiene alcun dato.  Procedure descritte sopra non selezionare dati per l'intervallo viene spostato, pertanto è consigliabile includere controlli nel codice.  Se sono presenti righe nell'intervallo da spostare, la distribuzione di dati effettivi non corrisponderanno mappa condiviso aggiornati. Per eseguire l'operazione invece in questi casi, utilizzare lo [strumento di unione di divisione](sql-database-elastic-scale-overview-split-and-merge.md) .  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
