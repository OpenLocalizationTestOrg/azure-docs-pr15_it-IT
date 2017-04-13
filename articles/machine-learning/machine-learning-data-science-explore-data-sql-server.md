<properties 
    pageTitle="Esplorare i dati di SQL Server Virtual Machine su Azure | Microsoft Azure" 
    description="Come esplorare i dati archiviati in una macchina virtuale Server SQL in Azure." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Esplorare i dati di SQL Server Virtual Machine su Azure


In questo documento viene illustrato come esplorare i dati archiviati in una macchina virtuale Server SQL in Azure. Possono essere eseguita mediante dati wrangling tramite SQL o un linguaggio di programmazione come Python.

I collegamenti seguenti **menu** ad argomenti che descrivono come utilizzare gli strumenti per esplorare i dati da vari ambienti di spazio di archiviazione. Questa attività è un'azione in Cortana Analitica processo (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] Le istruzioni SQL di esempio in questo documento presuppongono che i dati siano in SQL Server. In caso contrario, fare riferimento alla cloud scienza processo mapping dei dati per informazioni su come spostare i dati in SQL Server.



## <a name="sql-dataexploration"></a>Esplorare i dati SQL con script SQL

Ecco alcuni script SQL di esempio che può essere utilizzato per esplorare gli archivi di dati di SQL Server.

1. Ottenere il numero di osservazioni al giorno

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Ottenere i livelli di una colonna di categoria

    `select  distinct <column_name> from <databasename>`

3. Ottenere il numero di livelli in una combinazione di due colonne di categoria 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Ottenere la distribuzione per colonne numeriche

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Per un esempio pratico, è possibile utilizzare il [set di dati Roma Taxi](http://www.andresmh.com/nyctaxitrips/) e fanno riferimento a IPNB intitolato [Roma dati wrangling Usa blocco appunti IPython e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una descrizione dettagliata-to-end.

##<a name="python"></a>Esplorare i dati SQL con Python

Utilizzo di Python per esplorare i dati e generare caratteristiche quando i dati di SQL Server è simile all'elaborazione dei dati in blob Azure tramite Python, come descritto nei [dati Blob Azure processo nel proprio ambiente di scienze di dati](machine-learning-data-science-process-data-blob.md). I dati devono essere caricato dal database in un pandas DataFrame e quindi possono essere elaborati ulteriormente. Vengono illustrate il processo di connessione al database e caricare i dati in DataFrame in questa sezione.

Per connettersi a un database SQL Server da Python mediante pyodbc (NomeServer Sostituisci, dbname, nome utente e password con i valori specifici), è possibile utilizzare il formato di stringa di connessione seguenti:

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Raccolta Pandas](http://pandas.pydata.org/) in Python offre una vasta gamma di strutture di dati e strumenti di analisi dei dati per la modifica dei dati per la programmazione Python. Il codice seguente legge i risultati restituiti da un database di SQL Server in una cornice di dati Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

A questo punto è possibile collaborare con DataFrame Pandas descritte nell'argomento [Blob Azure processo dati nel proprio ambiente di scienze di dati](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Processo di Cortana Analitica nell'esempio di azione

Per un esempio di questa procedura dettagliata-to-end del processo di Analitica Cortana utilizzando un set di dati pubblici, vedere [il processo di scienze di dati di Team in azione: utilizzo di SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
