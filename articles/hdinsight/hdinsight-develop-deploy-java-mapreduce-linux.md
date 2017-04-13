<properties
    pageTitle="Sviluppare programmi Java MapReduce per basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come sviluppare programmi Java MapReduce e distribuirle a HDInsight basati su Linux."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Sviluppare programmi Java MapReduce per Hadoop su HDInsight Linux

Questo documenti illustra l'uso Maven Apache per creare un'applicazione di MapReduce, quindi distribuire e viene eseguito in un Hadoop basati su Linux cluster HDInsight.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o versioni successive (o un equivalente, ad esempio OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Una sottoscrizione di Azure**

- **CLI Azure**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate durante l'installazione di Java e JDK. Tuttavia, è necessario verificare che siano già presenti e che contengono i valori corretti per i singoli sistemi.

* **JAVA_HOME** - deve fare riferimento alla directory in cui è installato l'ambiente di runtime del linguaggio (JRE). Ad esempio, in un sistema di OS X, Unix o Linux deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, ha un valore simile a`c:\Program Files (x86)\Java\jre1.7`

* **Percorso** - deve contenere i seguenti percorsi:

    * **JAVA_HOME** (o il percorso equivalente)

    * **JAVA_HOME\bin** (o il percorso equivalente)

    * Directory in cui è installato Maven

##<a name="create-a-new-maven-project"></a>Creare un nuovo progetto Maven

1. Da una sessione terminal o riga di comando nel proprio ambiente di sviluppo, passare alla posizione desiderata per l'archiviazione del progetto.

3. Utilizzare il comando __mvn__ viene installato con Maven, per generare le pagine di supporto temporaneo per il progetto.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Questa operazione verrà creata una nuova directory nella directory corrente con il nome specificato dal parametro __un artifactID__ (in questo esempio,**wordcountjava** ). Questa directory conterrà gli elementi seguenti:

    * __pom.xml__ - [Progetto oggetto modello (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) che contiene i dettagli informazioni e configurazione usati per generare il progetto.

    * __src__ - directory che contiene la directory __principali/java/org/apache/hadoop/esempi__ in cui verrà creata l'applicazione.

3. Eliminare file __src/test/java/org/apache/hadoop/examples/apptest.java__ come non verranno utilizzata in questo esempio.

##<a name="add-dependencies"></a>Aggiungere le dipendenze

1. Modificare il file __pom.xml__ e aggiungere quanto segue all'interno di `<dependencies>` sezione:

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    In questo modo Maven che il progetto richiede le librerie (elencati all'interno di &lt;un artifactId\>) con una versione specifica (elencati all'interno di &lt;versione\>). Durante la compilazione, questo verrà scaricato dal repository Maven predefinito. È possibile utilizzare la [ricerca di archivio Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) per visualizzare ulteriori.

    Il `<scope>provided</scope>` indica Maven che le dipendenze non devono essere incluso con l'applicazione, come questi saranno forniti dal cluster HDInsight in fase di esecuzione.

2. Aggiungere quanto segue al file __pom.xml__ . Deve trattarsi all'interno di `<project>...</project>` tag nel file. ad esempio tra `</dependencies>` e `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    Plug-in di primo configura il [Plug-in ombreggiatura Maven](http://maven.apache.org/plugins/maven-shade-plugin/), che viene utilizzato per creare un uberjar (a volte chiamato un fatjar), che contiene le dipendenze necessarie all'applicazione. Impedisce anche la duplicazione di licenze all'interno del pacchetto vaso, che possono causare problemi in alcuni sistemi.

    Plug-in di secondo configura il compilatore Maven, che consente di impostare la versione di linguaggio necessario per questa applicazione alla versione utilizzata in cluster HDInsight.

3. Salvare il file __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Creare l'applicazione MapReduce

1. Passare alla directory __wordcountjava/src/principali/java/org/apache/hadoop/esempi__ e rinominare il file __App.java__ in __WordCount.java__.

2. Aprire il file __WordCount.java__ in un editor di testo e sostituire il contenuto con le operazioni seguenti:

        package org.apache.hadoop.examples;

        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class WordCount {

          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{

            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();

            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }

          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }

          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }

    Notare il nome del pacchetto è **org.apache.hadoop.examples** e il nome della classe è **WordCount**. Utilizzare i nomi seguenti quando si invia il processo di MapReduce.

3. Salvare il file.

##<a name="build-the-application"></a>Creare l'applicazione

1. Passare alla directory __wordcountjava__ se non si è già presente.

2. Usare il comando seguente per creare un file di VASO che contiene l'applicazione:

        mvn clean package

    Questo sarà pulire eventuali elementi di compilazione precedente, scaricare le dipendenze che non sono già state installate, creano e creare un pacchetto dell'applicazione.

3. Al termine, il comando nella directory __wordcountjava/destinazione__ conterrà un file denominato __SNAPSHOT.jar di 1.0 wordcountjava__.

    > [AZURE.NOTE] Il file __SNAPSHOT.jar di 1.0 wordcountjava__ è un uberjar che contiene non solo il processo WordCount, ma anche le relazioni tra cui il processo è necessaria in fase di esecuzione.


##<a id="upload"></a>Caricare vaso

Utilizzare il comando seguente per caricare il file vaso headnode HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

I file viene copiata dal sistema locale il nodo principale.

> [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto per la password. Se è stata utilizzata una chiave SSH, è necessario utilizzare la `-i` parametro e il percorso per la chiave privata. Ad esempio `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Eseguire il processo di MapReduce

1. Connettersi a HDInsight utilizzando SSH come descritto nei seguenti articoli:

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Da sessione SSH, usare il comando seguente per eseguire l'applicazione MapReduce:

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Verrà usare l'applicazione WordCount MapReduce per contare le parole presenti nel file davinci.txt e archiviare i risultati della __wasbs: / / / esempio/dati/wordcountout__. Del file di input e di output sono archiviate per lo spazio di archiviazione predefinito per il cluster.

3. Al termine del processo, utilizzare le operazioni seguenti per visualizzare i risultati:

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Verrà visualizzato un elenco di parole e i conteggi con valori simili al seguente:

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Passaggi successivi

In questo documento hanno appreso come sviluppare un processo di linguaggio MapReduce. Vedere i documenti seguenti per altre modalità per lavorare con HDInsight.

- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Utilizzare maialino con HDInsight][hdinsight-use-pig]
- [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di linguaggio](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

