<properties
    pageTitle="Analizzare i dati di Twitter con Apache Hive su HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare Python per archiviare TWEET che contengono parole chiave specifiche, quindi usare Hive e Hadoop HDInsight per trasformare i dati non elaborati TWitter in una tabella Hive che supportano le ricerche."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizzare i dati di Twitter utilizzando Hive in HDInsight

In questo documento si otterrà TWEET utilizzando un Twitter API di flusso e quindi utilizzare Apache Hive in un cluster basati su Linux HDInsight al processo di JSON dati formattati. Il risultato sarà un elenco di utenti Twitter che ha inviato la maggior parte dei TWEET contenente una determinata parola.

> [AZURE.NOTE] Mentre singoli elementi di questo documento possono essere utilizzate con i cluster basato su Windows HDInsight (Python ad esempio), più passaggi basati sull'utilizzo di un cluster basati su Linux HDInsight. Per istruzioni specifiche in un cluster di Windows, vedere [dati analizzare Twitter tramite Hive in HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- Un __cluster basati su Linux Azure HDInsight__. Per informazioni sulla creazione di un cluster, vedere [Introduzione a HDInsight basati su Linux](hdinsight-hadoop-linux-tutorial-get-started.md) per istruzioni sulla creazione di un cluster.

- Un __client SSH__. Per ulteriori informazioni sull'utilizzo di SSH con basati su Linux HDInsight, vedere gli articoli seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ e [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Ottenere un Twitter feed

Twitter consente di recuperare i [dati per ogni tweet](https://dev.twitter.com/docs/platform-objects/tweets) come documento JavaScript Object Notation (JSON) tramite un API REST. È necessario per l'autenticazione all'API [OAuth](http://oauth.net) . È anche necessario creare un' _Applicazione Twitter_ che contiene le impostazioni usate per accedere all'API.

###<a name="create-a-twitter-application"></a>Creare un'applicazione di Twitter

1. Da un web browser, accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **per iscriversi** .
2. Fare clic su **Crea nuova App**.
3. Immettere il **nome**, **Descrizione**, **sito Web**. Si può creare un campo URL per il **sito Web** . Nella tabella seguente mostra alcuni valori di esempio da usare:

  	| Campo | Valore |
  	|:----- |:----- |
  	| Nome  | MyHDInsightApp |
  	| Descrizione | MyHDInsightApp |
  	| Sito Web | http://www.myhdinsightapp.com |
    
4. Selezionare **Sì, accetto**e quindi fare clic su **Crea l'applicazione Twitter**.
5. Fare clic sulla scheda **autorizzazioni** . L'autorizzazione predefinita è di **sola lettura**. Questo è sufficiente per questa esercitazione.
6. Fare clic sulla scheda **tasti e token di accesso** .
7. Fare clic su **Crea i token di accesso**.
8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9. Prendere nota **chiave consumer**, **segreto Consumer**, **token di accesso**e **segreto token di accesso**. Sarà necessario i valori in un secondo momento.

>[AZURE.NOTE] Quando si utilizza il comando curvatura in Windows, utilizzare le virgolette doppie anziché tra virgolette singole per i valori dell'opzione.

###<a name="download-tweets"></a>Scaricare TWEET

Il codice Python seguente verrà scaricare 10.000 TWEET da Twitter e salvarli in un file denominato __tweets.txt__.

> [AZURE.NOTE] La procedura seguente vengono eseguita in cluster HDInsight poiché Python è già installato.

1. Connettersi a cluster HDInsight utilizzando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Se è stata usata una password per proteggere il proprio account utente SSH, verrà richiesto di immetterla. Se è stata utilizzata una chiave pubblica, è necessario utilizzare la `-i` parametro per specificare la chiave privata corrispondente. Ad esempio `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Per ulteriori informazioni sull'utilizzo di SSH con basati su Linux HDInsight, vedere gli articoli seguenti:
    
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Per impostazione predefinita, l'utilità di __pip__ non è installato sul nodo principale HDInsight. Utilizzare le operazioni seguenti per installare e quindi aggiornare questa utilità:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. Il codice per scaricare TWEET si basa su [Tweepy](http://www.tweepy.org/) e [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Per installare questi, usare il comando seguente:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] I bit sulla rimozione python openssl, l'installazione di python dev, libffi-dev, libssl-dev, pyOpenSSL e le richieste di [protezione] consiste nell'evitare un avviso di InsecurePlatform quando ci si connette a Twitter tramite SSL da Python.
    >
    > Tweepy v3.2.0 viene utilizzato per correggere [un errore](https://github.com/tweepy/tweepy/issues/576) che possono verificarsi durante l'elaborazione TWEET.

4. Utilizzare il comando seguente per creare un nuovo file denominato __gettweets.py__:

        nano gettweets.py

5. Di seguito vengono il contenuto del file __gettweets.py__ . Sostituire le informazioni di segnaposto per __consumer\_segreto__, __consumer\_chiave__, __access /\_token__, e __access\_token\_segreto__ con le informazioni dall'applicazione di Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. __Ctrl + X__e __Y__ consente di salvare il file.

7. Utilizzare il comando seguente per eseguire il file e scaricare TWEET:

        python gettweets.py

    Un indicatore di stato dovrà essere visualizzato e considerati fino al 100% di tweet vengono scaricati e salvati in file.

    > [AZURE.NOTE] Se si impiega molto tempo per l'indicatore di avanzamento, è consigliabile modificare il filtro per tenere traccia delle tendenze argomenti; Quando sono presenti molte TWEET sull'argomento che si sta filtrando, è possibile ottenere rapidamente 10000 TWEET necessari.

###<a name="upload-the-data"></a>Caricare i dati

Per caricare i dati su WASB (il file system distribuito utilizzato da HDInsight), utilizzare i comandi seguenti:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Qui vengono memorizzati i dati in un percorso accessibile a tutti i nodi del cluster.

##<a name="run-the-hiveql-job"></a>Eseguire il processo di HiveQL


1. Utilizzare il comando seguente per creare un file contenente le istruzioni HiveQL:

        nano twitter.hql
    
    Utilizzare la seguente come il contenuto del file:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        
3. Premere __Ctrl + X__, quindi premere __Y__ per salvare il file.

4. Utilizzare il comando seguente per eseguire HiveQL contenute nel file:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Verrà caricare shell Hive, eseguire la HiveQL nel file __twitter.hql__ e infine restituire un `jdbc:hive2//localhost:10001/>` prompt dei comandi.
    
5. Prompt beeline per verificare che è possibile selezionare i dati dalla tabella __TWEET__ autore HiveQL nel file __twitter.hql__ utilizzare le operazioni seguenti:
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Viene restituito un massimo di 10 TWEET contenenti la parola __Azure__ nel testo del messaggio.

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo visto come trasformare un set di dati JSON non strutturati in una tabella Hive strutturata di query, esplorare e analizzare i dati da Twitter tramite HDInsight su Azure. Per ulteriori informazioni, vedere:

- [Guida introduttiva a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analizzare i dati dei ritardi volo utilizzando HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
