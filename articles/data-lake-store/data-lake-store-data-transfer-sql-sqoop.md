<properties 
   pageTitle="Copiare i dati tra Lake archivio dati e database di SQL Azure con Sqoop | Microsoft Azure"
   description="Consente di copiare i dati tra Database SQL Azure e archivio Lake dati Sqoop" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiare i dati tra Lake archivio dati e database di SQL Azure con Sqoop

Informazioni su come usare Apache Sqoop per importare ed esportare i dati tra Database SQL Azure e archivio Lake dati.
 

## <a name="what-is-sqoop"></a>Che cos'è Sqoop?

Le applicazioni di dati sono una scelta naturale per l'elaborazione dei dati non strutturati e semistrutturati, ad esempio registri e file. Tuttavia, è anche possibile necessario elaborare dati strutturati archiviata nei database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) è uno strumento progettato per trasferire dati tra database relazionali e un archivio di dati, ad esempio archivio dati Lake. È possibile utilizzare per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio Database SQL Azure in archivio Lake dati. È quindi possibile trasformare e analizzare i dati utilizzando carichi di lavoro di dati e quindi esportare i dati in un RDBMS. In questa esercitazione, utilizzare un Database di SQL Azure del database relazionale per importare/esportare dalle.
 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Attivare l'abbonamento Azure** per anteprima pubblica archivio Lake dati. Vedere [le istruzioni](data-lake-store-get-started-portal.md#signup). 
- **Cluster di azure HDInsight** con l'accesso a un account di archivio di dati Lake. Vedere [creare un cluster di HDInsight con dati Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). In questo articolo si presuppone che si dispone di un cluster HDInsight Linux con accesso a dati Lake Store.
- **Database SQL azure**. Per istruzioni su come creare uno, vedere [creare un database SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Trovare rapidamente con video?

[Guardare questo video](https://mix.office.com/watch/1butcdjxmu114) su come copiare i dati tra lo spazio di archiviazione BLOB e archivio Lake dati utilizzando DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Creare tabelle di esempio nel Database di SQL Azure

1. Per iniziare, creare due tabelle di esempio nel Database di SQL Azure. Usare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al Database SQL Azure e quindi eseguire la query seguente.

    **Creare Tabella1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Creare Tabella2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. Nella **Tabella1**, aggiungere alcuni dati di esempio. Lasciare vuota la **Tabella2** . Si verranno importare dati da **Table1** in archivio Lake dati. Quindi, si verranno Esporta dati dall'archivio dati Lake in **Tabella2**. Eseguire il frammento di codice seguente.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Utilizzare Sqoop da un cluster di HDInsight con l'accesso all'archivio dati Lake

Un cluster HDInsight dispone già di pacchetti Sqoop disponibili. Se è stato configurato il cluster HDInsight per utilizzare dati Lake archivio come un ulteriore spazio di archiviazione, è possibile utilizzare Sqoop (senza apportare modifiche alla configurazione) per l'importazione/esportazione dati tra un database relazionale (in questo esempio, il Database di SQL Azure) e un account di archivio di dati Lake. 

1. Per questa esercitazione si presuppone che è stato creato un cluster Linux in modo che è necessario utilizzare SSH per connettersi al cluster. Vedere [connettersi a un cluster basati su Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Verificare se è possibile accedere all'account di archivio dati Lake dal cluster. Eseguire il comando seguente dal prompt dei comandi SSH:

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Questa operazione fornisce un elenco di file e cartelle nella finestra account archivio Lake dati.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importare dati da Database SQL Azure in archivio Lake dati.

3. Passare alla cartella in cui sono disponibili i pacchetti Sqoop. In genere, si sarà in `/usr/hdp/<version>/sqoop/bin`. 

4. Importare i dati dal **parametro Table1** in considerazione archivio Lake dati. Utilizzare la sintassi seguente:

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Si noti che segnaposto **nome server di database sql** rappresenta il nome del server in cui viene eseguito il database di SQL Azure. **nome del database SQL** segnaposto rappresenta il nome effettivo del database.

    Per esempio

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Verificare che i dati siano stati trasferiti all'account archivio Lake dati. Eseguire il comando seguente:

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Verrà visualizzato il seguente output.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Ogni **parte-m -** * file corrisponde a una riga nella tabella di origine, * *Tabella1**. È possibile visualizzare il contenuto della parte-m -* file da verificare.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Esportare i dati dall'archivio Lake dati nel Database di SQL Azure

6. Esportare i dati da account archivio Lake dati alla tabella vuota **Table2**nel Database di SQL Azure. Utilizzare la sintassi seguente.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Per esempio

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Verificare che i dati è stati caricati per la tabella di Database SQL. Usare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al Database SQL Azure e quindi eseguire la query seguente.

        
        SELECT * FROM TABLE2

    Dovrebbe avere l'output seguente.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Vedere anche

- [Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
