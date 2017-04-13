<properties 
    pageTitle="Analizzare i dati di volo ritardo con Hive nel basati su Linux HDInsight | Microsoft Azure" 
    description="Informazioni su come utilizzare Hive per analizzare i dati di volo basati su Linux HDInsight, quindi esportare i dati in Database SQL utilizzando Sqoop." 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizzare i dati dei ritardi volo usando Hive in HDInsight

Informazioni su come analizzare i dati di ritardo volo utilizzando Hive HDInsight basati su Linux quindi esportare i dati in Database di SQL Azure utilizzando Sqoop.

> [AZURE.NOTE] Mentre singoli elementi di questo documento possono essere utilizzate con i cluster basato su Windows HDInsight (Python e Hive ad esempio), molti passaggi sono specifici di cluster basati su Linux. Per istruzioni che funzionano con un cluster basato su Windows, vedere [dati dei ritardi volo analizza utilizzando Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Prerequisiti di

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __HDInsight un cluster__. Per istruzioni sulla creazione di un nuovo cluster basati su Linux HDInsight, vedere [Introduzione all'utilizzo di Hadoop con Hive in HDInsight su Linux](hdinsight-hadoop-linux-tutorial-get-started.md) .

- __Database SQL azure__. Utilizzare un database SQL Azure come un archivio di dati di destinazione. Se si dispone già di un Database SQL, vedere [esercitazione di Database SQL: creare un database SQL in minuti](../sql-database/sql-database-get-started.md).

- __Azure CLI__. Se non è installato CLI Azure, vedere [installare e configurare CLI Azure](../xplat-cli-install.md) per altre procedure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Scaricare i dati di volo

1. Passare alla [ricerca e tecnologia innovativa amministrazione Bureau delle statistiche relative a trasporti][rita-website].
2. Nella pagina, selezionare i valori seguenti:

  	| Nome | Valore |
  	| ---- | ---- |
  	| Filtro anno | 2013 |
  	| Filtro periodo | Gennaio |
  	| Campi | Anno FlightDate, UniqueCarrier, vettore, FlightNum, OriginAirportID, origine, OriginCityName, OriginState, DestAirportID, destinazione, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Deselezionare tutti gli altri campi |

3. Fare clic su **Scarica**. 

##<a name="upload-the-data"></a>Caricare i dati

1. Utilizzare il comando seguente per caricare il file zip nodo principale cluster HDInsight:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Sostituire __FILENAME__ con il nome del file zip. Sostituire __nomeutente__ con l'account di accesso SSH per il cluster HDInsight. Sostituire nome cluster con il nome del cluster HDInsight.
    
    > [AZURE.NOTE] Se si usa una password per l'autenticazione dell'accesso SSH, verrà richiesto per la password. Se è stata utilizzata una chiave pubblica, potrebbe essere necessario utilizzare la `-i` parametro e specificare il percorso per la chiave privata corrispondente. Ad esempio `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Una volta completato il caricamento, connettersi al cluster utilizzando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Per ulteriori informazioni sull'utilizzo di SSH con basati su Linux HDInsight, vedere gli articoli seguenti:
    
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Una volta connessa, utilizzare le operazioni seguenti per decomprimere il file ZIP:

        unzip FILENAME.zip
    
    Verrà estratto un file CSV che sia approssimativamente 60MB dimensioni.
    
4. Utilizzare le operazioni seguenti per creare una nuova directory su WASB (archivio di dati distribuiti utilizzato da HDInsight) e copiare il file:

    hdfs dfs - mkdir -p /tutorials/flightdelays/data hdfs dfs-inserire FILENAME.csv/esercitazioni flightdelays/dati /
    
##<a name="create-and-run-the-hiveql"></a>Creare ed eseguire il HiveQL

Utilizzare la procedura seguente per importare dati da file CSV in una tabella Hive denominata __ritardi__.

1. Per creare e modificare un nuovo file denominato __flightdelays.hql__, utilizzare il seguente:

        nano flightdelays.hql
        
    Utilizzare la seguente come il contenuto del file:
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. __Ctrl + X__e __Y__ consente di salvare il file.

3. Per avviare Hive ed eseguire il file __flightdelays.hql__ , utilizzare il seguente:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] In questo esempio, `localhost` viene utilizzato dal momento che si è connessi a nodo principale del cluster HDInsight nel punto in cui sia in esecuzione come HiveServer2.

4. Utilizzare il comando seguente per aprire una sessione di Beeline interattiva:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Quando si riceve il `jdbc:hive2://localhost:10001/>` Chiedi conferma, utilizzare le operazioni seguenti per recuperare dati dai dati di ritardo volo importati.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Verrà recuperare un elenco delle città che si è verificato ritardi meteo, oltre il ritardo medio e salvarla per `/tutorials/flightdelays/output`. In un secondo momento, Sqoop leggerà i dati da questa posizione ed Esporta in Database SQL Azure.

6. Per uscire dalla Beeline, immettere `!quit` quando viene richiesto.

## <a name="create-a-sql-database"></a>Creare un Database SQL

Se si dispone già di un Database SQL, è necessario ottenere il nome del server. È possibile trovare nel [Portale di Azure](https://portal.azure.com) selezionando __Database SQL__e il filtraggio sul nome del database che si desidera utilizzare. Il nome del server viene elencato nella colonna __SERVER__ .

Se non si dispone già di un Database SQL, usare le informazioni in [esercitazione di Database SQL: creare un database SQL in minuti](../sql-database/sql-database-get-started.md) crearne uno. È necessario salvare il nome del server per il database utilizzato.

##<a name="create-a-sql-database-table"></a>Creare una tabella di Database SQL

> [AZURE.NOTE] Esistono diversi modi per connettersi al Database di SQL per creare una tabella. La procedura seguente utilizza [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

1. Consente di connettersi al cluster basati su Linux HDInsight SSH ed eseguire la procedura seguente dalla sessione SSH.

3. Usare il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Dopo aver installato FreeTDS, usare il seguente comando per la connessione al Database SQL server. Sostituire __nomeserver__ con il nome del Database di SQL server. Sostituire __adminLogin__ e __adminPassword__ con l'account di accesso per Database SQL. Sostituire __databaseName__ con il nome del database.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Si riceverà output simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Nel `1>` richiesto, immettere le righe seguenti:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Quando il `GO` viene immessa, le istruzioni precedenti verranno valutate. Verrà creata una nuova tabella denominata __ritardi__, con un indice cluster (richiesto da Database SQL).

    Per verificare che la tabella è stata creata, utilizzare le operazioni seguenti:

        SELECT * FROM information_schema.tables
        GO

    È necessario vedere output simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Immettere `exit` nel `1>` prompt uscire dall'utilità tsql.
    
##<a name="export-data-with-sqoop"></a>Esportare i dati con Sqoop

2. Utilizzare il comando seguente per verificare che Sqoop possibilità di visualizzare il Database di SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    In questo modo un elenco di database, incluso il database creato la tabella ritardi in versioni precedenti.

3. Per esportare i dati da hivesampletable alla tabella mobiledata, utilizzare il comando seguente:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Questo indica Sqoop per connettersi al Database SQL, per il database contenente la tabella ritardi ed esportare dati dal wasbs: / / / esercitazioni/flightdelays/output (in cui è archiviato l'output della query hive in precedenza,) per la tabella ritardi.

4. Al termine dell'esecuzione del comando, utilizzare le operazioni seguenti per la connessione al database mediante TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessa, usare le istruzioni seguenti per verificare che i dati è stati esportati alla tabella mobiledata:
    
        SELECT * FROM delays
        GO

    Verrà visualizzato un elenco di dati della tabella. Tipo di `exit` per uscire dall'utilità tsql.

##<a id="nextsteps"></a>Passaggi successivi

A questo punto si informazioni su come caricare un file in archiviazione Blob Azure, come popolare una tabella Hive utilizzando i dati dallo spazio di archiviazione Blob Azure, come eseguire query Hive e come usare Sqoop per esportare dati da HDFS in un database SQL Azure. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Introduzione a HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Utilizzare Oozie con HDInsight][hdinsight-use-oozie]
* [Utilizzare Sqoop con HDInsight][hdinsight-use-sqoop]
* [Utilizzare maialino con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi Java MapReduce per HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare Hadoop Python streaming programmi per HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
