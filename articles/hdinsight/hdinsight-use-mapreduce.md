<properties
   pageTitle="MapReduce con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come eseguire i processi di MapReduce su Hadoop cluster HDInsight. Si eseguirà un'operazione di conteggio di base in word implementata come processo linguaggio MapReduce."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usare MapReduce in Hadoop in HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si imparerà eseguire processi MapReduce nel Hadoop in cluster HDInsight. Abbiamo eseguire un'operazione di conteggio di base in word implementata come processo linguaggio MapReduce.

##<a id="whatis"></a>Che cos'è MapReduce?

Hadoop MapReduce è una struttura di software per la scrittura di processi che elaborare grandi quantità di dati. Dati di input viene suddiviso in blocchi indipendenti vengono elaborate in parallelo tra i nodi del cluster. Un processo MapReduce è costituito da due funzioni:

* **Mappatura**: fruisce dei dati di input, analizza (in genere con filtro e le operazioni di ordinamento) e genera Tuple (coppie di parole chiave valore)
* **Riduttore**: utilizza tuple emesse dal mapping degli ed esegue un'operazione di riepilogo che consente di creare un risultato inferiore, combinato dai dati di mapping

Nel diagramma seguente è illustrato un esempio di processo MapReduce conteggio di base in word:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

L'output di questo processo è un conteggio del numero di volte in cui si è verificato ogni parola nel testo analizzati.

* Il mapping degli accetta le righe di testo di input come input e suddivide in parole. Genera un valore di chiave/coppia ogni volta che si verifica una parola della parola è seguita da 1. L'output viene ordinato prima di inviarlo riduttore.

* Il riduttore la somma di questi conteggi singoli per ogni parola e genera una coppia di chiave/valore singolo che contiene la parola di seguita dalla somma dei casi.

MapReduce può essere implementata in diverse lingue. Linguaggio è l'implementazione più comune e viene utilizzata per scopi di questo documento.

### <a name="hadoop-streaming"></a>Hadoop di flusso

Lingue e strutture in cui sono basati sul linguaggio e Java Virtual Machine (ad esempio Scalding o sovrapposte,) possono essere è stato eseguito direttamente come MapReduce, simile a un'applicazione di linguaggio. Gli altri utenti, ad esempio c# o Python o eseguibili autonoma, necessario utilizzare Hadoop streaming.

Hadoop streaming comunica con il mapping degli e riduttore tramite STDIN e STDOUT - il mapping degli e riduttore leggere dati di una riga alla volta da STDIN e scrivere l'output in STDOUT. Ogni riga leggere o emesso dal mapping riduttore deve essere nel formato di una coppia chiave/valore delimitato da una scheda charaacter:

    [key]/t[value]

Per ulteriori informazioni, vedere [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Per esempi di utilizzo Hadoop streaming con HDInsight, vedere le operazioni seguenti:

* [Sviluppare processi Python MapReduce](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Sui dati di esempio

In questo esempio, per i dati di esempio, utilizzare i blocchi appunti di Leonardo Da Vinci fornite come un documento di testo all'interno del cluster HDInsight.

I dati di esempio vengono archiviati in archiviazione Blob Azure, che utilizza HDInsight come il sistema di file predefinito per i cluster Hadoop. HDInsight accedere ai file archiviati in archiviazione Blob utilizzando il prefisso **wasb** . Ad esempio, per accedere al file sample.log, utilizzare la sintassi seguente:

    wasbs:///example/data/gutenberg/davinci.txt

Poiché l'archiviazione Blob Azure spazio di archiviazione predefinito per HDInsight, è possibile accedere anche il file tramite **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Nella sintassi precedente **wasbs: / / /** viene usato per accedere ai file archiviati nel contenitore di spazio di archiviazione predefinito per il cluster HDInsight. Se è stato specificato account spazio di archiviazione aggiuntivo quando viene completato il provisioning del cluster e si vuole accedere ai file archiviati in questi account, è possibile accedere i dati specificando l'indirizzo account contenitore nome e lo spazio di archiviazione. Ad esempio **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Informazioni su MapReduce di esempio

Il processo di MapReduce utilizzati in questo esempio si trova in **wasbs://example/jars/hadoop-mapreduce-examples.jar**e viene fornito con il cluster HDInsight. Questa pagina contiene un esempio di conteggio di word che verrà eseguita su **davinci.txt**.

> [AZURE.NOTE] Nei cluster HDInsight 2.1, il percorso del file è **wasbs:///example/jars/hadoop-examples.jar**.

Per riferimento, di seguito è il codice di linguaggio per il processo di MapReduce conteggio word:

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

Per istruzioni su come scrivere un processo MapReduce, vedere [MapReduce linguaggio di sviluppo di applicazioni per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Eseguire il MapReduce

HDInsight eseguire HiveQL processi, con una vasta gamma di metodi. Utilizzare la tabella seguente per decidere quale metodo appropriato, quindi fare clic sul collegamento per una procedura dettagliata.

| **Utilizzare questa**...                                                    | **via .to eseguire questa operazione**                                       | via nuovo **sistema operativo del cluster** | via Soido questo **sistema operativo client** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Usare il comando Hadoop tramite **SSH**                  | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Curvatura](hdinsight-hadoop-use-mapreduce-curl.md)                     | Inviare il processo in remoto utilizzando **resto**               | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Inviare il processo in modalità remota mediante **Windows PowerShell** | Linux o Windows                          | Windows                                  |
| [Desktop remoto](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Usare il comando Hadoop tramite **Desktop remoto**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Passaggi successivi

Sebbene MapReduce fornisce potenti funzionalità di diagnostica, può essere un po' difficile allo schema. Esistono diversi framework basato su Java che rendono più semplice definire le applicazioni di MapReduce, nonché tecnologie, ad esempio maialino e Hive, che forniscono un modo più semplice per gestire i dati in HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Sviluppare programmi Java MapReduce per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Sviluppare Python streaming MapReduce programmi per HDInsight](hdinsight-hadoop-streaming-python.md)

* [Sviluppare processi scottatura MapReduce con Hadoop Apache in HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Usare Hive con HDInsight][hdinsight-use-hive]

* [Utilizzare maialino con HDInsight][hdinsight-use-pig]

* [Eseguire gli esempi di HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
