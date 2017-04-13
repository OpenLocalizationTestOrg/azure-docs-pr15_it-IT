<properties 
    pageTitle="Elaborare dati di SQL Azure | Microsoft Azure" 
    description="Dati di SQL Azure processo" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Dati di processo in SQL Server virtuale Machine su Azure

In questo documento viene illustrato come esplorare i dati e generare caratteristiche per i dati archiviati in una macchina virtuale Server SQL in Azure. Possono essere eseguita mediante dati wrangling tramite SQL o un linguaggio di programmazione come Python.


> [AZURE.NOTE] Le istruzioni SQL di esempio in questo documento presuppongono che i dati siano in SQL Server. In caso contrario, fare riferimento al cloud scienza processo mapping dei dati per informazioni su come spostare i dati in SQL Server.

##<a name="SQL"></a>Utilizzo di SQL

Sono descritte le attività wrangling dati seguenti in questa sezione utilizzando SQL:

1. [Esplorazione dei dati](#sql-dataexploration)
2. [Generazione di funzionalità](#sql-featuregen)

###<a name="sql-dataexploration"></a>Esplorazione dei dati
Ecco alcuni script SQL di esempio che può essere utilizzato per esplorare gli archivi di dati di SQL Server.


> [AZURE.NOTE] Per un esempio pratico, è possibile utilizzare il [set di dati Roma Taxi](http://www.andresmh.com/nyctaxitrips/) e fanno riferimento a IPNB intitolato [Roma dati wrangling Usa blocco appunti IPython e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una descrizione dettagliata-to-end.

1. Ottenere il numero di osservazioni al giorno

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Ottenere i livelli di una colonna di categoria

    `select  distinct <column_name> from <databasename>`

3. Ottenere il numero di livelli in una combinazione di due colonne di categoria 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Ottenere la distribuzione per colonne numeriche

    `select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Generazione di funzionalità

In questa sezione sono illustrate alcune generazione caratteristiche utilizzando SQL:  

1. [Contare in base a caratteristica generazione](#sql-countfeature)
2. [Generazione di funzionalità binning](#sql-binningfeature)
3. [Attuare le caratteristiche da una singola colonna](#sql-featurerollout)


> [AZURE.NOTE] Dopo aver generato caratteristiche aggiuntive, è possibile aggiungere loro come colonne alla tabella esistente o creare una nuova tabella con le caratteristiche aggiuntive e di una chiave primaria, che può essere collegate con la tabella originale. 

###<a name="sql-countfeature"></a>Contare in base a caratteristica generazione

In questo documento viene due modi per generare le funzionalità di conteggio. Il primo metodo usano la funzione Somma condizionale e il secondo metodo viene utilizzata la clausola where'. Questi da unire quindi con la tabella originale (con colonne di chiave primaria) per le funzionalità di conteggio insieme ai dati originali.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Generazione di funzionalità binning

Nell'esempio seguente viene illustrato come generare caratteristiche binned da binning (con 5 bin) una colonna numerica che può essere utilizzata come una caratteristica invece:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Attuare le caratteristiche da una singola colonna

In questa sezione viene illustrato come distribuire una singola colonna in una tabella per generare caratteristiche aggiuntive. Si presuppone che sia una colonna latitudine o longitudine descritti nella tabella da cui si sta tentando di generare caratteristiche.

Ecco una breve panoramica sulle dati sulla posizione latitudine/longitudine (risorse assegnate da stackoverflow [come misurare la precisione di latitudine e longitudine?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Questo è utile per comprendere prima di featurizing il campo posizione:

- Il segno di sapere se si sono Nord o sud o est, ovest sul globo.
- Un centinaia diverso da zero cifre indicano viene utilizzata la longitudine, latitudine non!
- Le decine cifra offre una posizione per circa 1000 chilometri. Offre informazioni utili su quali continente o Oceano sono presenti.
- La cifra unità (un grado decimale) offre una posizione fino a 111 chilometri (60 miglia, circa 69 miglia). È possibile inviare commenti e suggerimenti circa lo stato di grandi dimensioni o il paese in.
- Decimale prima è opportuno fino a 11.1 km: distingue la posizione di una città di grandi dimensioni di una città di grandi dimensioni adiacente.
- La seconda posizione decimale è opportuno fino a 1.1 km: è possibile separare una località dall'elemento successivo.
- Il terzo decimale è opportuno fino a 110 m: è possibile identificare un campo agricolo grande o campus istituzionali.
- La quarta decimale è opportuno m fino a 11: è possibile identificare un pacco di terra. È analoghe con una precisione tipica di un'unità GPS non corrette alcuna interferenza.
- Decimale quinta è opportuno fino a 1.1 m per distinguere alberi tra loro. Precisione di questo livello con unità GPS commerciale è possibile solo grazie correzione differenziale.
- Decimale sesto è opportuno fino a 0.11 m: è possibile utilizzare questo per disporre le strutture in modo dettagliato, per la progettazione di paesaggi, creazione di strade. Dovrebbe essere più efficace per tenere traccia degli spostamenti di glaciers e fiumi. Questa operazione può essere ottenuta adozione di misure tensione con GPS, ad esempio GPS differenziali corretto.

Informazioni sulla posizione può essere featurized come indicato di seguito, separando fuori area geografica, luogo e città. Si noti che è possibile anche chiamare un punto finale resto, ad esempio API Bing Maps disponibili [trovare un percorso di punto](https://msdn.microsoft.com/library/ff701710.aspx) per ottenere informazioni regione/distretto.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Le caratteristiche descritte in precedenza in base a posizione ulteriormente utilizzabile per generare le funzionalità di conteggio aggiuntive come descritto in precedenza. 


> [AZURE.TIP] Livello di programmazione, è possibile inserire i record utilizzando il linguaggio scelto. Potrebbe essere necessario inserire i dati in blocchi per migliorare l'efficienza di scrittura (per informazioni su come eseguire questa operazione utilizzando pyodbc, vedere [esempio HelloWorld A per accedere a SQL Server con python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). In alternativa è possibile inserire i dati nel database mediante l' [utilità BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Connessione per l'apprendimento Azure

La caratteristica appena generata può essere aggiunti come colonna a una tabella esistente o archiviata in una nuova tabella e uniti tramite join con la tabella originale per l'apprendimento. Caratteristiche possono essere generate o accedervi se è già stato creato, utilizzando i [Dati di importazione] [ import-data] modulo di Azure apprendimento come illustrato di seguito:

![lettori azureml][1] 

##<a name="python"></a>Utilizzando un linguaggio di programmazione come Python

Utilizzo di Python per esplorare i dati e generare caratteristiche quando i dati di SQL Server è simile all'elaborazione dei dati in blob Azure tramite Python come descritto nei [dati Blob Azure processo si scienze ambiente dati](machine-learning-data-science-process-data-blob.md). I dati devono essere caricato dal database in un intervallo di dati pandas e quindi possono essere elaborati ulteriormente. Vengono illustrate il processo di connessione al database e caricare i dati in frame di dati in questa sezione.

Per connettersi a un database SQL Server da Python mediante pyodbc (NomeServer Sostituisci, dbname, nome utente e password con i valori specifici), è possibile utilizzare il seguente formato stringa di connessione:

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Raccolta Pandas](http://pandas.pydata.org/) in Python offre una vasta gamma di strutture di dati e strumenti di analisi dei dati per la modifica dei dati per la programmazione Python. Il codice riportato di seguito legge i risultati restituiti da un database di SQL Server in una cornice di dati Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

A questo punto è possibile collaborare con l'intervallo di dati Pandas stesse illustra l'articolo [dati Blob Azure processo in si scienze ambiente dati](machine-learning-data-science-process-data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Ricerca di scienze di dati di Azure nell'esempio di azione

Per un esempio di questa procedura dettagliata-to-end del processo di scienze dati di Azure utilizzando un set di dati pubblici, vedere [Processo di scienze dei dati di Azure in azione](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
