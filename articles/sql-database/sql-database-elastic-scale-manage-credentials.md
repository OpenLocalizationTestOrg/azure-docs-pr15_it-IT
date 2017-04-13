<properties 
    pageTitle="Gestione delle credenziali nella libreria client database flessibile | Microsoft Azure" 
    description="Come impostare il livello di destro delle credenziali, amministrazione in sola lettura, per le applicazioni di database flessibile" 
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

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenziali usate per accedere alla raccolta di client Database flessibile

La [libreria client Database flessibile](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) utilizza tre diversi tipi di credenziali per accedere al [gestore mappa condiviso](sql-database-elastic-scale-shard-map-management.md). A seconda delle necessità, usare le credenziali con il livello di accesso possibili inferiore.

* **Credenziali di gestione**: per la creazione o modifica di un responsabile di mappa condiviso. (Vedere il [glossario](sql-database-elastic-scale-glossary.md)). 
* **Le credenziali di accesso**: per accedere a un manager di mappa condiviso esistente per ottenere informazioni su shards.
* **Credenziali di connessione**: connettersi a shards. 

Vedere anche [Gestione database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Sulle credenziali di gestione

Gestione credenziali vengono utilizzate per creare un oggetto [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) per le applicazioni che modificano mappe condiviso. (Ad esempio, vedere [aggiunta di un condiviso con strumenti Database flessibile](sql-database-elastic-scale-add-a-shard.md) e [il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md)) L'utente della libreria client scalabilità flessibile creati utenti SQL e accessi SQL e consente di verificare che ogni vengono concesse le autorizzazioni di lettura/scrittura nel database di mappa condiviso globale e tutti i database condiviso anche. Queste credenziali vengono utilizzate per mantenere la mappa globale condiviso e le mappe condiviso locale durante l'esecuzione di modifiche alla mappa condiviso. Ad esempio, utilizzare le credenziali di gestione per creare l'oggetto di gestione della mappa condiviso (usando [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

Variabile **smmAdminConnectionString** è una stringa di connessione che contiene le credenziali di gestione. L'ID utente e la password offre accesso in lettura/scrittura per il database condiviso mapping e shards singoli. La stringa di connessione di gestione include anche il nome del server e il nome di database per identificare il database di mappa globale condiviso. Ecco una stringa di connessione a tale scopo:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Non utilizzare valori sotto forma di "username@server"—instead usare il valore di "username".  Ciò avviene perché le credenziali devono usare il database condiviso mapping manager e singoli shards, che può essere su server diversi.

## <a name="access-credentials"></a>Credenziali di accesso
  
Quando si crea un condiviso manager mappa in un'applicazione che non amministrare mappe condiviso, utilizzare le credenziali che dispongono delle autorizzazioni di sola lettura sulla mappa globale condiviso. Le informazioni recuperate dalla mappa globale condiviso in queste credenziali vengono usati per il [routing dipendenti dai dati](sql-database-elastic-scale-data-dependent-routing.md) e per popolare la cache di mappa condiviso sul client. Le credenziali sono disponibili tramite lo stesso modello di chiamata a **GetSqlShardMapManager** come illustrato in precedenza: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Si noti l'utilizzo di **smmReadOnlyConnectionString** in modo da rispecchiare l'uso di credenziali diverse per l'accesso per conto di utenti **non amministratori** : le credenziali non devono fornire le autorizzazioni di scrittura sulla mappa globale condiviso. 

## <a name="connection-credentials"></a>Credenziali di connessione 

Quando si utilizza il metodo [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) per accedere a un condiviso associato alla chiave sharding, sono necessarie le credenziali aggiuntive. Queste credenziali necessario fornire le autorizzazioni di accesso in sola lettura alle tabelle di mappa condiviso locale che risiedono sul condiviso. È necessario per eseguire la convalida di connessione per il routing dipendenti dai dati sul condiviso. Il frammento di codice consente l'accesso ai dati nel contesto di routing dipendenti dati: 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

In questo esempio, **smmUserConnectionString** contiene la stringa di connessione per le credenziali utente. Per DB di SQL Azure, ecco una stringa di connessione per le credenziali dell'utente: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Come con le credenziali di amministratore, non i valori in forma di "username@server". Utilizzare invece solo "nome".  Si noti inoltre che la stringa di connessione non contenga un nome di server e del database. Ciò avviene perché la chiamata **OpenConnectionForKey** darà automaticamente la connessione al condiviso corretto in base alla chiave. Di conseguenza, il nome del database e il nome di server non disponibili. 

## <a name="see-also"></a>Vedere anche
[Gestione di database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md)

[La protezione del Database SQL](sql-database-security.md)

[Guida introduttiva di processi di Database flessibile](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 