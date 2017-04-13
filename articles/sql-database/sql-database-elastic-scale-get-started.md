<properties 
    pageTitle="Guida introduttiva a Strumenti database flessibile" 
    description="Descrizione generale della caratteristica strumenti di database flessibile del Database di SQL Azure, tra cui facile eseguire l'applicazione di esempio." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Guida introduttiva a strumenti Database flessibile

In questo documento introduce all'ambiente di sviluppo eseguendo l'app di esempio. Nell'esempio viene creato una semplice applicazione sharded e illustra le funzionalità principali di strumenti database flessibile. Nell'esempio vengono illustrate le funzioni della [libreria client database flessibile](sql-database-elastic-database-client-library.md)

Per installare la raccolta, passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Si noti che la raccolta viene installata con l'app di esempio descritto di seguito.

## <a name="prerequisites"></a>Prerequisiti

1. Visual Studio 2012 o versioni successiva con c# è necessario. Scaricare una versione gratuita al [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 o versione successiva. Per ottenere la versione più recente, vedere [Installazione NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'applicazione di esempio

Il **flessibile Database con SQL Azure, Guida introduttiva** applicazione esempio illustra gli aspetti più importanti per lo sviluppo di per sharded applicazioni che utilizzano strumenti di flessibile database SQL Azure. È incentrata su casi di utilizzo chiave per [condiviso mappare management](sql-database-elastic-scale-shard-map-management.md), [il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) ed [eseguire query più condiviso](sql-database-elastic-scale-multishard-querying.md). Per scaricare ed eseguire l'esempio, procedere come segue: 

1. Aprire Visual Studio e selezionare **File -> Nuovo -> progetto**.
2. Nella finestra di dialogo, fare clic su **Online**.

    ![Nuovo progetto > Online][2]
3. Fare clic su **Visual c#** in **esempi**.

    ![Fare clic su Visual C#][3]
4. Nella casella Cerca digitare **db flessibile** per cercare il codice di esempio. Il titolo visualizzato **Flessibile strumenti DB per SQL Azure - Guida introduttiva** .

    ![Casella di ricerca][1]
 
5. Selezionare il campione, scegliere un nome e un percorso per il nuovo progetto e fare clic su **OK** per creare il progetto.
6. Aprire il file **App** della soluzione per il progetto di esempio e seguire le istruzioni nel file per aggiungere il nome del server database SQL Azure e le informazioni di accesso (nome utente e password).
7. Creare ed eseguire l'applicazione. Quando viene richiesto, consentire Visual Studio ripristinare i pacchetti NuGet della soluzione. Questa operazione verrà scaricate l'ultima versione della libreria client database flessibile da NuGet.
8. Riprodurre con diverse opzioni per altre informazioni sulle funzionalità di raccolta client. Nota i passaggi di che dell'applicazione accetta nella console output e esplorare il codice dietro le quinte.

    ![stato di avanzamento][4]

Congratulazioni-correttamente creato ed eseguire l'applicazione sharded prima utilizzo degli strumenti database flessibile nel Database di SQL Azure. Esaminiamo rapido shards creata nell'esempio connettendosi con Visual Studio o SQL Server Management Studio per il Server di database Azure. Si noterà nuovi database condiviso di esempio e un database di gestione di mappa condiviso che ha creato il campione.

> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Parti chiave dell'esempio di codice

1. **Gestione Shards e mappe condiviso**: il codice viene illustrato come lavorare con shards, intervalli, e i mapping nel file **ShardMapManagerSample.cs**. È possibile trovare ulteriori informazioni su questo argomento qui: [Gestione di mappa condiviso](http://go.microsoft.com/?linkid=9862595).  
2. **Il Routing dipendente dai dati**: Routing delle transazioni a destra condiviso vengono visualizzate nel **DataDependentRoutingSample.cs**. Per ulteriori informazioni, vedere [Routing dipendenti di dati](http://go.microsoft.com/?linkid=9862596). 
3. **Esecuzione di query su più Shards**: esecuzione di query in shards è illustrato nel file **MultiShardQuerySample.cs**. Per ulteriori informazioni, vedere [Condiviso più query](http://go.microsoft.com/?linkid=9862597).
4. **Aggiunta vuota shards**: l'iterativo l'aggiunta di nuovi shards vuota viene eseguita mediante il codice nel file **AddNewShardsSample.cs**. I dettagli di questo argomento illustra sono qui: [Gestione di mappa condiviso](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Altre operazioni scalabilità flessibile

1. **Divisione di un condiviso esistente**: la possibilità di dividere shards viene fornita tramite lo **strumento di unione di divisione**. È possibile trovare ulteriori informazioni su questo strumento qui: [Panoramica dello strumento di unione divisa](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Unione shards esistente**: unisce condiviso vengono eseguite anche tramite lo **strumento di unione di divisione**. Per ulteriori informazioni, vedere: [Panoramica dello strumento di unione divisa](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Costo

Gli strumenti di database flessibile sono gratuiti. Strumenti database flessibile non impone aggiuntive in base alle tariffe sopra il costo per uso Azure. 

Ad esempio, l'applicazione di esempio crea nuovi database. Il costo dipende l'edizione di database SQL Azure DB che scelto e l'utilizzo di Azure dell'applicazione.

Per informazioni sui prezzi vedere [Dettagli prezzi Database di SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli strumenti di database flessibile, vedere:

* [Consultazione della documentazione Strumenti database flessibile](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Esempi di codice: 
    -    [DB flessibile con SQL Azure - Guida introduttiva](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [DB flessibile con SQL Azure - integrazione con Framework entità](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Flessibilità condiviso nell'interfaccia di Script](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [annuncio scalabilità flessibile](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canale 9: [Video panoramica scalabilità flessibile](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Forum di discussione: [forum di Database SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Migliorare le prestazioni: [contatori delle prestazioni per il gestore di mappa condiviso](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
