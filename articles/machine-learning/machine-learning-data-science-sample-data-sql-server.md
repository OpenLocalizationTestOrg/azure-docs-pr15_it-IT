<properties 
    pageTitle="Esempio di dati di SQL Server Azure | Microsoft Azure" 
    description="Dati di esempio in SQL Server Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Dati di esempio in SQL Server Azure


In questo documento viene illustrato come i dati archiviati in SQL Server in Azure di esempio utilizzando SQL o il linguaggio di programmazione Python. Viene illustrato come spostare dati campionati in apprendimento Azure, salvarlo in un file, caricamento in un archivio blob Azure e quindi lettura in Azure Machine Learning Studio.

Il campione Python utilizza la libreria ODBC [pyodbc](https://code.google.com/p/pyodbc/) per connettersi al Server di SQL Azure e raccolta [Pandas](http://pandas.pydata.org/) per il campione.

>[AZURE.NOTE] Il codice SQL di esempio in questo documento si presuppone che i dati siano in un Server SQL Azure. In caso contrario, fare riferimento all'argomento [spostare i dati di SQL Server Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) per istruzioni su come spostare i dati in SQL Server in Azure.

**Perché i dati di esempio?**
Se il set di dati che si intende analizzare grandi dimensioni, in genere è consigliabile campioni verso il basso i dati per ridurre le dimensioni inferiori ma representative e più gestibili. In questo modo si facilita informazioni sui dati, ricerca e progettazione di funzionalità. Il ruolo di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) è consentire la creazione di prototipi veloce delle funzioni di elaborazione dei dati e modelli di apprendimento automatico.

**Menu** collegamenti ad argomenti che descrivono come i dati da vari ambienti di spazio di archiviazione di esempio seguenti. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questa attività di esempio è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Utilizzo di SQL

In questa sezione vengono descritti diversi metodi utilizzando SQL per eseguire semplice sondaggio con i dati nel database. Scegliere un metodo in base alla dimensione dei dati e la sua distribuzione.

Due elementi riportata di seguito viene illustrato come utilizzare newid in SQL Server per eseguire il campione. Il metodo scelto dipende casuale come desiderato nell'esempio essere (pk_id nel codice di esempio seguente viene utilizzata per una chiave primaria generato automaticamente).

1. Meno restrittive campione

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Più campione 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE può essere utilizzata per campionare effettuare nonché illustrato di seguito. Se la dimensione dei dati è grande (presupponendo che i dati in pagine diverse non è correlati) e per la query completare un'accettabile ora è possibile un approccio più efficace.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] È possibile esplorare e generare caratteristiche da questi dati campionati per archiviare i dati in una nuova tabella


###<a name="sql-aml"></a>Connessione per l'apprendimento Azure

È possibile utilizzare le query di esempio sopra direttamente nella finestra [Importa dati] di Azure apprendimento[ import-data] modulo per i dati in tempo reale verso il basso campioni e renderlo una prova di apprendimento Azure. Schermata di uso del modulo di lettura per leggere i dati campione è illustrata di seguito:
   
![lettore sql][1]

##<a name="python"></a>Utilizzo di Python linguaggio di programmazione 

In questa sezione viene illustrato come utilizzare la [libreria pyodbc](https://code.google.com/p/pyodbc/) per stabilire un database ODBC connettersi a un database SQL server in Python. La stringa di connessione di database è il seguente: (sostituire nomeserver, dbname, nome utente e password con la configurazione):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Raccolta [Pandas](http://pandas.pydata.org/) in Python offre una vasta gamma di strutture di dati e strumenti di analisi dei dati per la modifica dei dati per la programmazione Python. Il codice riportato di seguito legge i dati di esempio 0,1% da una tabella di database SQL Azure in dei dati Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

È ora possibile utilizzare con i dati campione nel riquadro dati Pandas. 

###<a name="python-aml"></a>Connessione per l'apprendimento Azure

È possibile utilizzare il codice di esempio seguente per salvare i dati campionati verso il basso in un file e caricare in un archivio blob Azure. I dati nel blob possono essere direttamente letto in una prova di apprendimento Azure computer utilizzando i [Dati di importazione] [ import-data] modulo. I passaggi sono i seguenti: 

1. Scrivere il frame di dati pandas in un file locale

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare file locale blob Azure

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Leggere i dati da blob Azure tramite apprendimento Azure [Importa dati] [ import-data] modulo come illustrato nella schermata seguente:
 
![blob reader][2]

## <a name="the-team-data-science-process-in-action-example"></a>Il processo di scienze Team dei dati nell'esempio di azione

Per un esempio di questa procedura dettagliata-to-end del processo di scienze dati Team un utilizzando un set di dati pubblici, vedere [Team dati scienza processo in azione: con SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
