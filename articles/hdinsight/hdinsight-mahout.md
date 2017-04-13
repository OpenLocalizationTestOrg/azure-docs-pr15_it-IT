<properties
    pageTitle="Genera indicazioni utilizzando Mahout e basato su WIndows HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare la libreria di apprendimento Apache Mahout per generare consigli filmato con basato su Windows HDInsight (Hadoop)."
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

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Genera indicazioni filmato utilizzando Apache Mahout con Hadoop in HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come utilizzare la libreria con Azure HDInsight apprendimento automatico [Apache Mahout](http://mahout.apache.org) per generare consigli filmato.

> [AZURE.NOTE] La procedura descritta in questo documento richiesta un client di Windows e un cluster basato su Windows HDInsight. Per informazioni sull'utilizzo di Mahout da Linux, OS X o client Unix, con un cluster basati su Linux HDInsight, vedere [suggerimenti filmato genera utilizzando Apache Mahout con basati su Linux Hadoop in HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Si apprenderà

Mahout è un [computer formazione] [ ml] raccolta per Apache Hadoop. Mahout contiene algoritmi per l'elaborazione dei dati, ad esempio, classificazione, cluster. In questo articolo si utilizzerà un motore recommendation per generare suggerimenti filmato basati sui film che hanno visto amici. Verrà anche informazioni su come eseguire classificazioni con un insieme di strutture di decisione. Seguente verrà descritte le operazioni seguenti:

* Modalità di esecuzione di processi Mahout mediante Windows PowerShell

* Modalità di esecuzione di processi Mahout dalla riga di comando Hadoop

* Come installare Mahout in cluster HDInsight 3.0 e HDInsight 2.0

    > [AZURE.NOTE] Mahout viene fornito con la versione 3.1 HDInsight dei cluster. Se si utilizza una versione precedente di HDInsight, vedere [Installare Mahout](#install) prima di continuare.

##<a name="prerequisites"></a>prerequisiti

- **Hadoop basato su Windows un cluster in HDInsight**. Per informazioni sulla creazione di una, vedere [Introduzione all'utilizzo di Hadoop in HDInsight][getstarted]
- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Genera indicazioni mediante Windows PowerShell

> [AZURE.NOTE] Sebbene il processo utilizzato questa sezione funziona utilizzando Windows PowerShell, molte delle classi fornite con Mahout attualmente non funziona con Windows PowerShell e deve essere eseguite da Hadoop dalla riga di comando. Per un elenco di classi non compatibili con Windows PowerShell, vedere la sezione [risoluzione dei problemi](#troubleshooting) .
>
> Per un esempio di Hadoop dalla riga di comando per eseguire Mahout processi, vedere [Classifica dati utilizzando la riga di comando Hadoop](#classify).

Una delle funzioni fornito da Mahout è un motore recommendation. Questo motore accetta i dati nel formato di `userID`, `itemId`, e `prefValue` (preferenza agli utenti per l'elemento). Mahout quindi possibile eseguire analisi di CO-occorrenza per determinare: _gli utenti che dispongono di una preferenza per un elemento dispongono anche di una preferenza per questi altri elementi_. Mahout determina con preferenze elemento mi piace, che possono essere utilizzate per suggerimenti degli utenti.

Di seguito è illustrato un esempio estremamente semplice che utilizza filmati:

* __CO-occorrenza__: Fausto, Alice e Roberto mi _attraverso stella_, _Il Empire scioperi indietro_e _tornare del Jedi_. Mahout determina che gli utenti che ad esempio uno di questi filmati anche come gli altri due.

* __CO-occorrenza__: Davide e Alice anche apprezzamento per _Il alieni fantasma_, _attacco i duplicati_e _ritorsioni del Sith_. Mahout determina che gli utenti che mi i tre filmati precedenti anche come questi tre.

* __Suggerimento analogie__: Fausto perché apprezzamento per i primi tre filmati, Mahout esamina filmati che altri utenti mediante Preferenze simili apprezzamento, ma Fausto non è controllate (mi/classificazione). In questo caso, Mahout consiglia _Il alieni fantasma_, _attacco i duplicati_e _ritorsioni del Sith_.

###<a name="understanding-the-data"></a>Informazioni sui dati

In modo appropriato, [Ricerche GroupLens] [ movielens] fornisce i dati di valutazione per i filmati in un formato compatibile con Mahout. Questi dati sono disponibili in spazio di archiviazione predefinito del cluster in `/HdiSamples/MahoutMovieData`.

Esistono due file, `moviedb.txt` (informazioni sui filmati,) e `user-ratings.txt`. Il file ratings.txt utente viene utilizzato durante l'analisi, mentre moviedb.txt viene utilizzata per fornire informazioni testo descrittivo quando si visualizzano i risultati dell'analisi.

I dati contenuti nel ratings.txt utente presenta una struttura di `userID`, `movieID`, `userRating`, e `timestamp`, che indica classificazione di un filmato come elevata ogni utente. Ecco un esempio dei dati:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Eseguire il processo

Usare il seguente script di Windows PowerShell per eseguire un processo che utilizza il motore di recommendation Mahout con i dati di filmato:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Processi mahout non rimuovere dati temporanei create durante l'elaborazione del processo. Il `--tempDir` viene specificato nel processo di esempio per isolare i file temporanei in una directory specifica.

Il processo di Mahout non viene restituito l'output in STDOUT. Se, tuttavia, viene memorizzato nella directory di output specificata come __parte-r-00000__. Lo script consente di scaricare questo file __txt__ nella directory corrente sulla workstation.

Di seguito è illustrato un esempio di contenuto del file:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La prima colonna è la `userID`. I valori contenuti in ' ["e"]' sono `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Visualizzare l'output

Anche se l'output generato potrebbe essere OK per l'utilizzo in un'applicazione, non è molto leggibile. Il `moviedb.txt` dal server possono essere usati per risolvere il `movieId` a un filmato nome, ma è necessario prima scaricare it e il file di valutazioni dal server utilizzando il seguente script:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Dopo avere scaricato i file, usare il seguente script di PowerShell per visualizzare i suggerimenti con i nomi di filmato:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Di seguito è illustrato un esempio dell'esecuzione dello script:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

L'output dovrebbe essere simile al seguente:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classificare i dati utilizzando la riga di comando Hadoop

Uno dei metodi di classificazione disponibili con Mahout consiste nel creare una [foresta casuale][forest]. Si tratta di una più passaggi che è necessario utilizzare dati di formazione per generare decisionali, quindi utilizzati per classificare i dati. Utilizza la classe __org.apache.mahout.classifier.df.tools.Describe__ fornita da Mahout. Deve essere eseguita attualmente da Hadoop dalla riga di comando.

###<a name="load-the-data"></a>Caricare i dati

1. Scaricare i file seguenti [nel](http://nsl.cs.unb.ca/NSL-KDD/)set di dati NSL KDD.

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): il file di formazione

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): i dati di test

2. Aprire ogni file e rimuovere le righe nella parte superiore che iniziano con '@', e quindi salvare i file. Se queste non vengono rimossi, si riceveranno messaggi di errore quando si usa questi dati con Mahout.

2. Caricare i file di __dati di esempio /__. È possibile eseguire questa operazione utilizzando il seguente script. Sostituire __nome cluster__ con il nome del cluster HDInsight. Sostituire FILENAME con il nome fo il file da caricare.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Eseguire il processo

1. Il processo richiede una riga di comando Hadoop. Attivare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [connessione a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Dopo la connessione, usare l'icona __della riga di comando Hadoop__ per aprire la riga di comando Hadoop:

    ![Hadoop cli][hadoopcli]

3. Utilizzare il comando seguente per generare il descrittore di file (__KDDTrain +. Info__), che utilizza Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    Il `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` vengono descritti gli attributi dei dati nel file. Ad esempio, L indica un'etichetta.

4. Creare un insieme di strutture di decisione tramite il comando seguente:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    L'output di questa operazione è archiviato nella directory __nsl foresta__ , che si trova nell'archivio per il cluster HDInsight _ wasbs://user/&lt;nomeutente > / nsl-foresta/nsl-forest.seq. Il &lt;nomeutente > è il nome utente utilizzato per la sessione Desktop remoto. Il file non è leggibile dagli utenti.

5. Verificare l'insieme di strutture classificare il set di dati __KDDTest + .arff__ . Usare il comando seguente:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Questo comando restituisce le informazioni di riepilogo informazioni sul processo di classificazione analogo al seguente:

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Questo processo produce anche un file memorizzato in __wasbs:///example/data/predictions/KDDTest+.arff.out__. Tuttavia, il file non è leggibile dagli utenti.

> [AZURE.NOTE] Processi mahout non sovrascriveranno i file. Se si desidera eseguire di nuovo questi processi, è necessario eliminare i file creati dai processi precedenti.

##<a name="troubleshooting"></a>Risoluzione dei problemi

###<a name="install"></a>Installare Mahout

Mahout è installato nel cluster 3.1 HDInsight, ma può essere installato manualmente su cluster 3.0 HDInsight o HDInsight 2.1 eseguendo la procedura seguente:

1. La versione di Mahout da utilizzare dipende dalla versione HDInsight del cluster. È possibile trovare la versione cluster, visualizzare le proprietà per il cluster nel portale di Azure.

  * __Per HDInsight 2.1__, è possibile scaricare un file di archivio Java (VASO) che contiene [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Per 3.0 HDInsight__, è necessario [creare Mahout dal sito di origine] [ build] e specificare la versione Hadoop fornita da HDInsight. Installare i prerequisiti elencati nella pagina compilazione, scaricare l'origine e quindi usare il comando seguente per creare i file di vaso Mahout:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Quando si rilascia Mahout 1.0, dovrebbe essere possibile utilizzare i pacchetti predefiniti con 3.0 HDInsight.

2. Caricare il file vaso __esempio/JAR__ in spazio di archiviazione predefinito per il cluster. Sostituire il nome del cluster HDInsight nome cluster nello script seguente e sostituire FILENAME con il percorso del file __mahout-coure-0,9-job.jar__ ..

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Non è possibile sovrascrivere i file

Eseguire processi mahout non eliminare file temporanei che sono stati creati durante l'elaborazione. Inoltre, i processi non sovrascriverà un file di output esistente.

Per evitare errori durante l'esecuzione di processi Mahout, eliminare i file temporanei e di output tra le esecuzioni oppure utilizzare i nomi di directory temporanea e di output univoco.

###<a name="cannot-find-the-jar-file"></a>Non è possibile trovare il file VASO

3.1 HDInsight cluster includere Mahout. Il percorso e il nome includere il numero di versione di Mahout installata nel cluster. Script di esempio di Windows PowerShell in questa esercitazione utilizza un percorso alla data di novembre 2015, ma il numero di versione cambierà in futuro gli aggiornamenti a HDInsight. Per determinare il percorso corrente per il file JAR Mahout per il cluster, utilizzare il comando di Windows PowerShell seguente e quindi modificare lo script per fare riferimento il percorso del file che viene restituito:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classi non compatibili con Windows PowerShell

Processi mahout che utilizzano le classi seguenti restituiscono una varietà di messaggi di errore se vengono utilizzati da Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Per eseguire processi che utilizzano queste classi, connettersi al cluster HDInsight ed eseguire i processi utilizzando la riga di comando Hadoop. Per un esempio, vedere [dati classifica Hadoop dalla riga di comando](#classify) .

##<a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato come utilizzare Mahout, scoprire altri modi di utilizzo dei dati in HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Maialino con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
