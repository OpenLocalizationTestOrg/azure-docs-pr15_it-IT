<properties 
    pageTitle="Scalabilità flessibile SQL Azure domande frequenti su | Microsoft Azure" 
    description="Domande frequenti su scalabilità flessibile Database SQL Azure." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Strumenti di database flessibile domande frequenti 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se dispone di un tenant singola per condiviso e nessun tasto sharding, come popolare la chiave sharding per informazioni sullo schema?

L'oggetto info schema viene utilizzato solo per dividere scenari di stampa unione. Se un'applicazione è progettato per single-tenant, non richiede lo strumento Dividi unire e pertanto non è necessario per popolare l'oggetto info schema.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>È stato effettuato il provisioning di un database e dispone già di un responsabile di mappa condiviso, come registrare il nuovo database come un condiviso?

Vedere **[aggiunta di un condiviso per un'applicazione tramite la libreria di client database flessibile](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Come Strumenti database flessibile costo?

Utilizzo della libreria di client flessibile database non è soggetta a sui costi previsti. Solo per i database di SQL Azure utilizzate per shards e gestione di mappa condiviso, nonché i ruoli web/lavoro che viene effettuato il provisioning per lo strumento Dividi unire attribuzione dei costi.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Perché le credenziali non funzionano quando si aggiunge un condiviso da un server diverso?
Non utilizzare le credenziali sotto forma di "utente ID=username@servername”, se, tuttavia è sufficiente usare" ID utente = username ".  Inoltre, assicurarsi che l'account di accesso "username" disponga delle autorizzazioni per il condiviso.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>È necessario creare un responsabile di mappa condiviso e popolare shards ogni volta che è possibile avviare le applicazioni?

No, ovvero la creazione di gestore di mappa condiviso (ad esempio **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) è un'operazione su una sola volta.  L'applicazione deve utilizzare la chiamata **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** in fase di avvio dell'applicazione.  È presente solo una chiamata di questo tipo per ogni dominio applicazione.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Sono domande sull'utilizzo di strumenti flessibile database, come è possibile ottenere le risposte? 

Per raggiungere Contattaci nel [forum di Database SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando riceve una connessione al database utilizzando una chiave sharding, comunque possibile eseguire query sui dati per altri tasti sharding nella stessa condiviso.  Si tratta di una caratteristica predefinita?

L'API scala flessibile per ottenere una connessione al database corretto per trovare il codice sharding, ma non forniscono filtri chiave sharding.  Se necessario, aggiungere le clausole **WHERE** alla query per limitare l'ambito alla chiave sharding fornito.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>È possibile usare un'edizione di Database Azure diversa per ogni condiviso il set di condiviso?

Sì, un condiviso è un singolo database e pertanto un condiviso può essere un'edizione Premium mentre un altro un'edizione Standard. Inoltre, l'edizione di un condiviso ridimensionare verso l'alto o verso il basso più volte durante la durata della condiviso.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Esegue il provisioning di strumento unione divisa (o Elimina) un database durante un'operazione di divisione o unione? 

No. Per **dividere** le operazioni di database di destinazione deve esistere con lo schema appropriato e che sia registrato presso il responsabile di mappa condiviso.  Per le operazioni di **unione** , è necessario eliminare il condiviso al responsabile della mappa condiviso e quindi eliminare il database.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 