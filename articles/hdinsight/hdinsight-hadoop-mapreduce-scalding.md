<properties
 pageTitle="Sviluppare processi scottatura MapReduce con Maven | Microsoft Azure"
 description="Informazioni su come usare Maven per creare un processo MapReduce scottatura, quindi distribuire ed eseguire il processo Hadoop cluster HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Sviluppare processi scottatura MapReduce con Hadoop Apache in HDInsight

Scottatura è una raccolta di Scala che facilita la creazione di processi Hadoop MapReduce. Offre una sintassi concisa, nonché stretta integrazione con Scala.

In questo documento per informazioni su come utilizzare Maven per creare un processo di MapReduce conteggio di base in word scritto in Scalding. È quindi come distribuire ed eseguire il processo in un cluster di HDInsight.

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A Windows o Linux in base a Hadoop cluster HDInsight**. Per ulteriori informazioni, vedere [Hadoop basati su Linux provisioning su HDInsight](hdinsight-hadoop-provision-linux-clusters.md) o [basato su Windows disposizione Hadoop su HDInsight](hdinsight-provision-clusters.md) .

* **[Maven](http://maven.apache.org/)**

* **[Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive**

## <a name="create-and-build-the-project"></a>Creare e compilare il progetto

1. Utilizzare il comando seguente per creare un nuovo progetto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Questo comando crea una nuova directory denominata **scaldingwordcount**e creare pagine di supporto temporaneo per un'applicazione di Scala.

2. Nella directory **scaldingwordcount** , aprire il file **pom.xml** e sostituire il contenuto con le operazioni seguenti:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Questo file descrive il progetto, le dipendenze e plug-in. Ecco le voci importanti:

    * **Maven.Compiler.Source** e **maven.compiler.target**: imposta la versione di linguaggio per il progetto

    * **archivi**: archivi che contengono i file delle dipendenze utilizzati per il progetto

    * **core_2.11 scottature** e **hadoop principali**: il progetto dipende Scalding e Hadoop pacchetti di base

    * **plug-in Maven scala**: plug-in per compilare le applicazioni di scala

    * **plug-in Maven ombreggiatura**: plug-in per creare ombreggiato JAR (fat). Si applica questo plug-in di filtri e le trasformazioni; specificially:

        * **filtri**: I filtri applicati modificare le informazioni di metadati incluse nel file vaso. Per impedire l'accesso eccezioni in fase di esecuzione, sono esclusi i vari file di firma che possono essere inclusi con dipendenze.

        * **le esecuzioni**: la configurazione di esecuzione del pacchetto fase specifica la classe **com.twitter.scalding.Tool** della classe principale per il pacchetto. In caso contrario, è necessario specificare com.twitter.scalding.Tool, nonché della classe che contiene la logica dell'applicazione durante l'esecuzione del processo con il comando hadoop.

3. Eliminare la directory **src/test** , come non si creano test con questo esempio.

4. Aprire il file **src/main/scala/com/microsoft/example/App.scala** e sostituire il contenuto con le operazioni seguenti:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Questo implementato un processo di conteggio di base in word.

5. Salvare e chiudere i file.

6. Utilizzare il comando seguente dalla directory **scaldingwordcount** per creare e creare un pacchetto dell'applicazione:

        mvn package

    Al termine di questo processo, il pacchetto contenente l'applicazione WordCount sono disponibili in **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Eseguire il processo in un cluster basato su Linux

> [AZURE.NOTE] La procedura seguente utilizza SSH e il comando Hadoop. Per altri metodi di MapReduce processi in esecuzione, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

1. Usare il comando seguente per caricare il pacchetto al cluster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    I file viene copiata dal sistema locale il nodo principale.

    > [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto per la password. Se è stata utilizzata una chiave SSH, è necessario utilizzare la `-i` parametro e il percorso per la chiave privata. Per esempio`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Usare il comando seguente per connettersi a nodo principale cluster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto per la password. Se è stata utilizzata una chiave SSH, è necessario utilizzare la `-i` parametro e il percorso per la chiave privata. Per esempio`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Una volta connessa a nodo principale, utilizzare il seguente comando per eseguire il processo di conteggio di word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Questa operazione viene eseguita la classe WordCount implementata in precedenza. `--hdfs`indica il processo di utilizzare HDFS. `--input`Specifica il file di testo di input, mentre `--output` specifica la posizione di output.

4. Al termine del processo, utilizzare le operazioni seguenti per visualizzare l'output.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Verranno visualizzati informazioni simile al seguente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Eseguire il processo in un cluster basato su Windows

La procedura seguente utilizza Windows PowerShell. Per altri metodi di MapReduce processi in esecuzione, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Avviare PowerShell Azure e accesso al proprio account Azure. Dopo avere fornito le credenziali, il comando restituisce le informazioni sull'account.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Se si hanno più abbonamenti, fornire l'id di abbonamento che si desidera utilizzare per la distribuzione.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] È possibile utilizzare `Get-AzureRMSubscription` per ottenere un elenco di tutte le sottoscrizioni associata al proprio account, che include l'Id di abbonamento per ognuna di esse.

4. usare il seguente script per caricare ed eseguire il processo di WordCount. Sostituire `CLUSTERNAME` con il nome del HDInsight cluster e verificare che l'opzione `$fileToUpload` è il percorso al file __SNAPSHOT.jar di 1.0 scaldingwordcount__ corretto.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Quando si esegue lo script, verrà richiesto di immettere il nome utente di amministratore e la password per il cluster HDInsight. Eventuali errori che si verificano durante il processo è in esecuzione verranno registrati nella console.
     
6. Al termine del processo, l'output verrà scaricato i file __txt__ nella directory corrente. Utilizzare il comando seguente per visualizzare i risultati.

        cat output.txt

    Il file deve contenere valori simili al seguente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato come utilizzare Scalding per creare processi MapReduce per HDInsight, utilizzare i collegamenti seguenti per esplorare altre modalità per lavorare con Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)

* [Utilizzare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
