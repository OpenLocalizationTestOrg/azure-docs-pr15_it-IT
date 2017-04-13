<properties
    pageTitle="Analizzare i dati di Twitter con Hadoop in HDInsight | Microsoft Azure"
    description="Informazioni su come usare Hive per analizzare i dati di Twitter in Hadoop in HDInsight per trovare la frequenza di utilizzo di una determinata parola."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizzare i dati di Twitter utilizzando Hive in HDInsight

Siti Web di social networking sono uno dei motori di Guida principali per l'adozione di grande dati. API pubbliche fornite da siti come Twitter sono un'utile origine dati per l'analisi e le tendenze popolari in base. In questa esercitazione si otterrà TWEET utilizzando un Twitter API di flusso e quindi utilizzare Apache Hive su Azure HDInsight per ottenere un elenco di utenti Twitter che ha inviato la maggior parte dei TWEET contenente una determinata parola.

> [AZURE.NOTE] La procedura descritta in questo documento richiede un cluster basato su Windows HDInsight. Per istruzioni specifiche in un cluster di Linux, vedere [dati analizzare Twitter tramite Hive in HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Una workstation** con PowerShell Azure installato e configurato. 

    Per eseguire script di Windows PowerShell, è necessario eseguire PowerShell Azure come amministratore e impostare il criterio di esecuzione su *RemoteSigned*. Vedere [gli script di Windows PowerShell eseguire][powershell-script].

    Prima di eseguire gli script di Windows PowerShell, assicurarsi che si è connessi al proprio abbonamento Azure utilizzando il seguente cmdlet:

        Login-AzureRmAccount

    Se si hanno più abbonamenti Azure, utilizzare il cmdlet seguente per impostare l'abbonamento corrente:

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Cluster di Azure HDInsight**. Per istruzioni su cluster di provisioning, vedere [Introduzione all'utilizzo di HDInsight] [ hdinsight-get-started] o [cluster di provisioning HDInsight] [hdinsight-provision]. È necessario il nome del cluster in un secondo momento nell'esercitazione.

Nella tabella seguente elenca i file utilizzati in questa esercitazione:

File|Descrizione
---|---
/tutorials/Twitter/data/tweets.txt|Dati di origine per il processo di Hive.
/tutorials/Twitter/output|La cartella di output per il processo di Hive. Il nome di file predefinito Hive processo output è **000000_0**.
tutorials/Twitter/Twitter.hql|File di script HiveQL.
/tutorials/Twitter/JobStatus|Lo stato del processo di Hadoop.


##<a name="get-twitter-feed"></a>Twitter Get feed

In questa esercitazione si utilizzerà [Twitter API di flusso][twitter-streaming-api]. Specifiche della Twitter API si userà lo streaming è [stati/filtro][twitter-statuses-filter].

>[AZURE.NOTE] Un file contenente 10.000 TWEET e il file di script Hive (vedere la sezione successiva) sono stati caricati in un contenitore di Blob pubblico. È possibile ignorare questa sezione se si desidera utilizzare i file caricati.

[TWEET dati](https://dev.twitter.com/docs/platform-objects/tweets) vengono archiviati in formato JavaScript Object Notation (JSON) che contiene una struttura complessa annidata. Anziché scrivere molte righe di codice utilizzando un linguaggio di programmazione convenzionale, è possibile trasformare la struttura annidata in una tabella Hive, in modo che è possibile eseguire una query da un linguaggio SQL (Structured Query)-come linguaggio denominato HiveQL.

Twitter utilizza OAuth per fornire accesso autorizzato alla relativa API. OAuth è un protocollo di autenticazione che consente agli utenti di approvare applicazioni per agire per conto suo senza condividere la propria password. Sono disponibili ulteriori informazioni su [oauth.net](http://oauth.net/) o in ottima [Guida introduttiva a OAuth](http://hueniverse.com/oauth/) da Hueniverse.

Il primo passaggio usare OAuth consiste nel creare una nuova applicazione nel sito di sviluppo di Twitter.

**Per creare un'applicazione di Twitter**

1. Accedere a [https://apps.twitter.com/](https://apps.twitter.com/). Se non si dispone di un account Twitter, fare clic sul collegamento **per iscriversi** .
2. Fare clic su **Crea nuova App**.
3. Immettere il **nome**, **Descrizione**, **sito Web**. Si può creare un campo URL per il **sito Web** . Nella tabella seguente mostra alcuni valori di esempio da usare:

    Campo|Valore
    ---|---
    Nome|MyHDInsightApp
    Descrizione|MyHDInsightApp
    Sito Web|http://www.myhdinsightapp.com

4. Selezionare **Sì, accetto**e quindi fare clic su **Crea l'applicazione Twitter**.
5. Fare clic sulla scheda **autorizzazioni** . L'autorizzazione predefinita è di **sola lettura**. Questo è sufficiente per questa esercitazione.
6. Fare clic sulla scheda **tasti e token di accesso** .
7. Fare clic su **Crea i token di accesso**.
8. Fare clic su **Test OAuth** nell'angolo superiore destro della pagina.
9. Prendere nota **chiave consumer**, **segreto Consumer**, **token di accesso**e **segreto token di accesso**. Sarà necessario i valori in un secondo momento nell'esercitazione.

In questa esercitazione, utilizzare Windows PowerShell per eseguire il servizio web di chiamata. Per un esempio .NET c#, vedere [Analizza valutazione in Twitter in tempo reale con HBase in HDInsight][hdinsight-hbase-twitter-sentiment]. Lo strumento altre impostazioni generali per effettuare chiamate al servizio web è [*voltare*][curl]. Curvatura può essere scaricato dal [qui][curl-download].

>[AZURE.NOTE] Quando si utilizza il comando curvatura in Windows, utilizzare le virgolette doppie anziché tra virgolette singole per i valori dell'opzione.

**Per ottenere TWEET**

1. Aprire Windows PowerShell integrato ambiente di Scripting (ISE). (Nella schermata Start di Windows 8, digitare **PowerShell_ISE** e quindi fare clic su **Windows PowerShell ISE**. Avviare [Windows PowerShell in Windows 8 e Windows][powershell-start].)

2. Copiare il seguente script nel riquadro dello script:

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Impostare il primo cinque a otto variabili dello script:


    Variabile|Descrizione
    ---|---
    $clusterName|Questo è il nome del cluster HDInsight in cui si vuole eseguire l'applicazione.
    $oauth_consumer_key|Si tratta di applicazione Twitter **chiave consumer** che è annotato in precedenza al momento della creazione dell'applicazione Twitter.
    $oauth_consumer_secret|Si tratta di applicazione Twitter **Segreto consumer** annotato in precedenza.
    $oauth_token|Si tratta di applicazione Twitter **token di accesso** annotato in precedenza.
    $oauth_token_secret|Si tratta di applicazione Twitter **segreto token di accesso** annotato in precedenza.
    $destBlobName|Questo è il nome del blob di output. Il valore predefinito è **tutorials/twitter/data/tweets.txt**. Se si cambia il valore predefinito, è necessario aggiornare gli script di Windows PowerShell di conseguenza.
    $trackString|Il servizio web restituirà TWEET correlati a tali parole chiave. Il valore predefinito è **Azure, Cloud, HDInsight**. Se si modifica il valore predefinito, sarà necessario aggiornare gli script di Windows PowerShell di conseguenza.
    $lineMax|Il valore determina quanti TWEET lo script leggerà. Bastano circa tre minuti per leggere 100 TWEET. È possibile impostare un numero più grande, ma richiederà più tempo per il download.

5. Premere **F5** per eseguire lo script. Se si verificano problemi, come una possibile soluzione, selezionare tutte le righe e quindi premere **F8**.
6. Si devono vedere "Complete!" alla fine dell'output. Verranno visualizzati messaggi di errore in rosso.

Come una procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/data/tweets.txt**sullo spazio di archiviazione Blob Azure usando un elenco di cartelle di archiviazione Azure o Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco di file, vedere [lo spazio di archiviazione Blob usare con HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Creare uno script HiveQL

Utilizzo di PowerShell di Azure, è possibile eseguire più istruzioni HiveQL uno alla volta o comprimere l'istruzione HiveQL in un file di script. In questa esercitazione, è necessario creare uno script HiveQL. Il file di script deve essere caricato in archiviazione Blob Azure. Nella sezione successiva, si eseguirà il file di script tramite PowerShell Azure.

>[AZURE.NOTE] Il file di script Hive e un file contenente 10.000 TWEET sono stati caricati in un contenitore di Blob pubblico. È possibile ignorare questa sezione se si desidera utilizzare i file caricati.

Lo script HiveQL verrà eseguite le operazioni seguenti:

1. **Eliminare la tabella tweets_raw** nel caso la tabella esiste già.
2. **Creare la tabella Hive tweets_raw**. Questa tabella strutturata Hive temporanea contenente i dati per estrarre ulteriormente, trasformare e caricare elaborazione (ETL). Per informazioni sulle partizioni, vedere [Hive esercitazione][apache-hive-tutorial].  
3. **Caricare i dati** dalla cartella di origine, /tutorials/twitter/data. Il set di dati di grandi dimensioni TWEET nel formato JSON annidato a questo punto è stato trasformato in una struttura di tabella Hive temporanea.
3. **Eliminare la tabella TWEET** nel caso la tabella esiste già.
4. **Creare la tabella TWEET**. Possono eseguire ricerche per il set di dati di tweet con Hive, è necessario eseguire un altro processo ETL. Questo processo ETL definito uno schema di tabella più dettagliato per i dati memorizzati nella tabella "twitter_raw".  
5. **Inserire una tabella di sovrascrittura**. Questo script Hive complesso verrà avviare un insieme di processi MapReduce lunghi dal cluster Hadoop. A seconda del set di dati e le dimensioni del cluster, operazione potrebbe richiedere circa 10 minuti.
6. **Inserisci sovrascrivere directory**. Eseguire una query e il set di dati in un file di output. La query restituirà un elenco di utenti Twitter che ha inviato la maggior parte delle TWEET contenenti la parola "Azure".

**Per creare uno script Hive e caricarlo in Azure**

1. Aprire Windows PowerShell ISE.
2. Copiare il seguente script nel riquadro dello script:

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
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
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Impostare le prime due variabili dello script:

    Variabile|Descrizione
    ---|---
    $clusterName|Immettere il nome di cluster HDInsight in cui si vuole eseguire l'applicazione.
    $subscriptionID|Immettere l'ID di abbonamento Azure.
    $sourceDataPath|Posizione di archiviazione Blob Azure in cui le query Hive leggerà i dati. Non è necessario modificare questa variabile.
    $outputPath|Posizione di archiviazione Blob Azure in cui le query Hive verranno restituiti i risultati. Non è necessario modificare questa variabile.
    $hqlScriptFile|Il percorso e il nome del file del file di script HiveQL. Non è necessario modificare questa variabile.

5. Premere **F5** per eseguire lo script. Se si verificano problemi, come una possibile soluzione, selezionare tutte le righe e quindi premere **F8**.
6. Si devono vedere "Complete!" alla fine dell'output. Verranno visualizzati messaggi di errore in rosso.

Come una procedura di convalida, è possibile controllare il file di output, **/tutorials/twitter/twitter.hql**sullo spazio di archiviazione Blob Azure usando un elenco di cartelle di archiviazione Azure o Azure PowerShell. Per uno script di Windows PowerShell di esempio per l'elenco di file, vedere [lo spazio di archiviazione Blob usare con HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Elaborare dati Twitter utilizzando Hive

Aver completato tutte le operazioni di preparazione. A questo punto, è possibile richiamare lo script Hive e controllare i risultati.

### <a name="submit-a-hive-job"></a>Inviare un processo Hive

Usare il seguente script di Windows PowerShell per eseguire lo script Hive. È necessario impostare la variabile di prima.

>[AZURE.NOTE] Per utilizzare il TWEET e lo script HiveQL caricata nelle ultime due sezioni, impostare $hqlScriptFile su "/ tutorials/twitter/twitter.hql". Per utilizzare quelli che sono stati caricati in un blob pubblico dell'utente, impostare $hqlScriptFile su "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Controllare i risultati

Usare il seguente script di Windows PowerShell per controllare l'output di processo Hive. È necessario impostare le prime due variabili.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] La tabella Hive utilizza \001 come delimitatore di campo. Il delimitatore non è visibile nell'output.

Dopo i risultati dell'analisi sono stati inseriti nel archiviazione Blob Azure, è possibile esportare i dati in un server di database di SQL Azure SQL, esportare i dati in Excel tramite Power Query o connettere l'applicazione ai dati utilizzando il Driver ODBC Hive. Per ulteriori informazioni, vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop], [dati dei ritardi volo analizza utilizzando HDInsight][hdinsight-analyze-flight-delay-data], [Connettere Excel a HDInsight con Power Query][hdinsight-power-query]e [Connettere Excel a HDInsight con il Driver ODBC Hive Microsoft][hdinsight-hive-odbc].

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo visto come trasformare un set di dati JSON non strutturati in una tabella Hive strutturata di query, esplorare e analizzare i dati da Twitter tramite HDInsight su Azure. Per ulteriori informazioni, vedere:

- [Guida introduttiva a HDInsight][hdinsight-get-started]
- [Analizzare in tempo reale valutazione in Twitter con HBase in HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analizzare i dati dei ritardi volo utilizzando HDInsight][hdinsight-analyze-flight-delay-data]
- [Connettere Excel a HDInsight con Power Query][hdinsight-power-query]
- [Connettere Excel a HDInsight con il Driver ODBC Microsoft Hive][hdinsight-hive-odbc]
- [Utilizzare Sqoop con HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
