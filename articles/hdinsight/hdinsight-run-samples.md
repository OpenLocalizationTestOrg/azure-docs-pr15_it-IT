<properties
    pageTitle="Eseguire gli esempi Hadoop in HDInsight | Microsoft Azure"
    description="Iniziare a usare il servizio di Azure HDInsight con gli esempi forniti. Usare gli script di PowerShell eseguiti programmi MapReduce nel cluster di dati."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>

#<a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Eseguire Hadoop MapReduce gli esempi in HDInsight basato su Windows

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Una serie di esempi vengono forniti per ottenere iniziare i processi di MapReduce in esecuzione su cluster Hadoop mediante Azure HDInsight. In questi esempi vengono resi disponibili su ognuno dei cluster HDInsight gestite creati. Esecuzione di questi esempi consentono di familiarizzare con l'uso dei cmdlet di PowerShell Azure per eseguire i processi sui cluster Hadoop.

- [**Conteggio delle parole**][hdinsight-sample-wordcount]: conta le occorrenze di word in un file di testo.
- [**Conteggio di flusso c#**][hdinsight-sample-csharp-streaming]: conta le occorrenze di word in un file di testo utilizzando l'interfaccia di trasmissione Hadoop.
- [**Stima di pi greco**][hdinsight-sample-pi-estimator]: utilizza un statistiche (periodo di quasi Monte Carlo) metodo per calcolare il valore di pi greco.
- [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: eseguire un interesse generale GraySort su un file di 10 GB utilizzando HDInsight. Esistono tre processi da eseguire: Teragen per generare i dati, Terasort per ordinare i dati e Teravalidate per verificare che i dati siano stati ordinati in modo corretto.

>[AZURE.NOTE] Il codice sorgente disponibili nell'appendice. 

Documentazione molto aggiuntiva è presente sul web per tecnologie correlate Hadoop, ad esempio documentazione sui cmdlet usati in Windows PowerShell e streaming e programmazione basato su Java MapReduce degli script. Per ulteriori informazioni sulle risorse, vedere:

- [Sviluppare programmi Java MapReduce per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [Inviare i processi di Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introduzione a Azure HDInsight][hdinsight-introduction]

Oggi molte persone scegliere Hive e maialino su MapReduce.  Per ulteriori informazioni, vedere:

- [Utilizzare Hive in HDInsight](hdinsight-use-hive.md)
- [Usare maialino in HDInsight](hdinsight-use-pig.md)
 
**Prerequisiti**:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un cluster di HDInsight**. Per istruzioni su come i vari modi in cui è possibile creare tali cluster, vedere [creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md).
- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>Word count - Java 

Per inviare un progetto MapReduce, è necessario creare una definizione di processo MapReduce. Nella definizione del processo, specificare il vaso programma MapReduce e il percorso del file vaso, ossia **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, il nome della classe e gli argomenti.  Il programma di MapReduce wordcount accetta due argomenti: il file di origine che verrà utilizzato per contare le parole e il percorso per l'output.

Il codice sorgente disponibili in [Appendice](#apendix-a---the-word-count-MapReduce-program-in-java).

Per la procedura di sviluppo di un'applicazione di linguaggio MapReduce, vedere - [sviluppare MapReduce Java programmi per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
 
**Per inviare un processo MapReduce conteggio di word**

1. Aprire **Windows PowerShell ISE**. Per istruzioni, vedere [installare e configurare Azure PowerShell][powershell-install-configure].
2. Incollare lo script di PowerShell seguente:

        $subscriptionName = "<Azure Subscription Name>"
        $resourceGroupName = "<Resource Group Name>"
        $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
        
        Select-AzureRmSubscription -SubscriptionName $subscriptionName
        
        # Define the MapReduce job
        $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "wordcount" `
                                    -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
        
        # Submit the job and wait for job completion
        $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
        $mrJob = Start-AzureRmHDInsightJob `
                            -ResourceGroupName $resourceGroupName `
                            -ClusterName $clusterName `
                            -HttpCredential $cred `
                            -JobDefinition $mrJobDefinition 
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -JobId $mrJob.JobId 
        
        # Get the job output
        $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
        $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
        $defaultStorageContainer = $cluster.DefaultStorageContainer
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -DefaultStorageAccountName $defaultStorageAccount `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultContainer $defaultStorageContainer  `
            -JobId $mrJob.JobId `
            -DisplayOutputType StandardError

        # Download the job output to the workstation
        $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
        Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        # Display the output file
        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Il processo di MapReduce produce un file denominato *part-r-00000*, contenente errori ortografici e i conteggi. Lo script utilizza il comando **findstr** per visualizzare un elenco di tutte le parole che contiene *"non esiste"*.

3. Impostare le variabili prima di tutto 3 ed eseguire lo script.

## <a name="hdinsight-sample-csharp-streaming"></a>Word count - streaming c#

Hadoop fornisce un'API di flusso a MapReduce, in modo da poter scrivere mappa e ridurre funzioni in lingue diverse da linguaggio.

> [AZURE.NOTE] Procedura in questa esercitazione si applica solo a cluster HDInsight basato su Windows. Per un esempio di flusso per i cluster basati su Linux HDInsight, vedere [sviluppare Python flussi programmi per HDInsight](hdinsight-hadoop-streaming-python.md).

Nell'esempio, il mapping degli e il riduttore sono eseguibili leggere l'input [stdin] [ stdin-stdout-stderr] (riga per riga) e generare l'output in [stdout][stdin-stdout-stderr]. Il programma conta tutte le parole del testo.

Quando un file eseguibile viene specificato per **temporaneo**, ogni attività mappatura avvia l'eseguibile come un processo separato quando viene inizializzato il mapping degli. Durante l'esecuzione dell'operazione di mapping, converte l'input in righe e feed le linee [stdin] [ stdin-stdout-stderr] del processo.

Nel frattempo, il mapping degli raccoglie l'output orientata per riga da stdout del processo. Converte ogni riga in una coppia chiave/valore, raccolti come l'output della mappatura. Per impostazione predefinita, il prefisso di una riga per il primo carattere di tabulazione è la chiave e il resto della riga (esclusi il carattere di tabulazione) è il valore. Se non c'è alcun carattere di tabulazione nella riga, riga intera viene considerata come chiave e il valore è null.

Quando un file eseguibile viene specificato per **riduttori**, ogni attività riduttore avvia l'eseguibile come un processo separato quando viene inizializzato il riduttore. Come viene eseguita l'operazione riduttore, relativa coppie di valori di chiave input viene convertito in righe e le righe feed [stdin] [ stdin-stdout-stderr] del processo.

Nel frattempo, il riduttore raccoglie l'output orientata per riga da [stdout] [ stdin-stdout-stderr] del processo. Converte ogni riga in una coppia chiave/valore, raccolti come output di riduttori. Per impostazione predefinita, il prefisso di una riga per il primo carattere di tabulazione è la chiave e il resto della riga (esclusi il carattere di tabulazione) è il valore.

Per ulteriori informazioni sull'interfaccia Hadoop Streaming, vedere [Hadoop Streaming] [streaming hadoop].

**Per inviare un flusso di lavoro conteggio word c#**

- Seguire la procedura descritta nel [conteggio - linguaggio](#word-count-java)e sostituire la definizione del processo con le operazioni seguenti:

        $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                                -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                                -Mapper "cat.exe" `
                                -Reducer "wc.exe" `
                                -InputPath "/example/data/gutenberg/davinci.txt" `
                                -OutputPath "/example/data/StreamingOutput/wc.txt"  


    Il file di output sono i seguenti:
    
        example/data/StreamingOutput/wc.txt/part-00000      
                                
## <a name="hdinsight-sample-pi-estimator"></a>Stima di pi greco

La stima di pi greco utilizza un statistiche (periodo di quasi Monte Carlo) metodo per calcolare il valore di pi greco. Punti inseriti in modo casuale all'interno di un'unità sono compresi quadrato anche in un cerchio figurare all'interno di tale quadrato con una probabilità uguale all'area del cerchio, pi greco/4. Il valore di pi greco può essere stimato rispetto al valore del 4R, dove R è il rapporto tra il numero di punti che si trovano all'interno del cerchio e il numero totale di punti che si trovano entro la radice quadrata. Più grande all'esempio di punti utilizzati più facilmente la stima è.

Lo script fornito per questo esempio invia un processo di vaso Hadoop e imposta fino a eseguire con un valore 16 mappe, ognuno dei quali è richiesta per calcolare 10 milioni di punti di esempio per i valori di parametro. Questi valori di parametro possono essere modificati per migliorare il valore stimato di pi greco. Per riferimento primi 10 cifre decimali di pi greco sono 3.1415926535.

**Per inviare un processo di stima di pi greco**

- Seguire la procedura descritta nel [conteggio - linguaggio](#word-count-java)e sostituire la definizione del processo con le operazioni seguenti:

        $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "pi" `
                                    -Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB Graysort

In questo esempio viene usata una limitata 10GB di dati in modo che possa essere eseguita relativamente rapidamente. Utilizza le applicazioni di MapReduce sviluppate da Owen O'Malley e Arun Murthy che hanno vinto annuale benchmark di ordinamento terabyte generale ("daytona") di 2009 con un tasso di 0.578 TB/min (100 TB in minuti 173). Per ulteriori informazioni su questo e altri benchmark ordinamento, vedere il sito [Sortbenchmark](http://sortbenchmark.org/) .

In questo esempio Usa tre serie di applicazioni MapReduce:

1. **TeraGen** è un programma MapReduce che è possibile utilizzare per generare le righe di dati per l'ordinamento.
2. **TeraSort** esempi i dati di input e utilizza MapReduce per ordinare i dati in un ordine di totale. TeraSort è un ordinamento standard delle funzioni MapReduce, ad eccezione di un partitioner personalizzato che utilizza un elenco ordinato delle chiavi N-1 campionati che definiscono l'intervallo di chiave per ogni ridurre. In particolare, tutte le chiavi tali che esempio [i-1] < = chiave < esempio [i] vengono inviati per ridurre i. Questo garanzie che gli output di ridurre i sono tutti minore di output di ridurre i + 1.
3. **TeraValidate** è un programma MapReduce che verifica che l'output è globalmente ordinata. Crea una mappa per ogni file nella directory di output e ogni mapping assicura che ogni chiave è minore o uguale a quella precedente. La funzione mappa genera anche i record delle chiavi nome e il cognome di ogni file e la funzione Riduci garantisce il prima chiave del file i oltre l'ultima chiave i-1 file. Gli eventuali problemi indicati come esito della riduzione con i tasti che non sono in ordine.

Il formato di input e di output, utilizzato da tutte le tre applicazioni, legge e scrittura i file di testo nel formato corretto. L'output della riduzione ha replica impostata su 1, anziché il valore predefinito 3, perché il contesto di benchmark non richiede che i dati di output essere replicate a più nodi.

Tre attività sono necessari per il campione, ognuno corrispondente a uno dei programmi di MapReduce descritti nell'introduzione:

1. Generare i dati per l'ordinamento, eseguire il processo di MapReduce **TeraGen** .
2. Ordinare i dati eseguendo il processo di MapReduce **TeraSort** .
3. Verificare che i dati siano stati ordinati correttamente, eseguire il processo di **TeraValidate** MapReduce.

**Per inviare i processi**

- Seguire la procedura descritta nel [conteggio - linguaggio](#word-count-java)e usare le definizioni di processo seguenti:

    $teragen = nuovo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomeClasse "teragen" '-argomenti "-Dmapred.map.tasks=50", "100000000", "/ esempio / / 10 GB-Ordina-input di dati"
    
    $terasort = nuovo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomeClasse "terasort" '-argomenti "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ esempio / / 10 GB-Ordina-input di dati", "/ esempio / / 10 GB-Ordina-output dati"
    
    $teravalidate = nuovo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomeClasse "teravalidate" '-argomenti "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ esempio/output dei dati/10 GB-Ordina", "/ esempio/dati/10 GB-Ordina-convalidare"


##<a name="next-steps"></a>Passaggi successivi 

In questo articolo e gli articoli di tutti gli esempi di acquisite come eseguire gli esempi inclusi con i cluster HDInsight tramite PowerShell Azure. Per esercitazioni sull'utilizzo di maialino, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Iniziare a usare Hadoop con Hive in HDInsight per l'analisi di utilizzo del ricevitore per dispositivi mobili][hdinsight-get-started]
* [Utilizzare maialino con Hadoop in HDInsight][hdinsight-use-pig]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Inviare i processi di Hadoop in HDInsight] [hdinsight-submit-jobs]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Eseguire il debug Hadoop in HDInsight: messaggi di errore] [hdinsight-errors]


## <a name="appendix-a---the-word-count-source-code"></a>Appendice a-il codice sorgente conteggio di Word

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


## <a name="appendix-b---the-word-count-streaming-source-code"></a>Appendice B - il conteggio delle parole streaming codice sorgente

Il programma MapReduce utilizzata l'applicazione cat.exe come interfaccia mapping per il flusso di testo in una console e sull'applicazione di wc.exe dell'interfaccia di ridurre per contare il numero di parole trasmessi da un documento. Mappatura sia riduttore leggere caratteri, riga per riga, dal flusso di input standard (stdin) e scrivere nel flusso di output standard (stdout).

    // The source code for the cat.exe (Mapper).

    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0]));
                }

                string line;
                while ((line = Console.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
    }



Il codice di mapping nel file cat.cs utilizza [StreamReader] [ streamreader] oggetto per leggere i caratteri del flusso di posta in arrivo nella console, che scrive il flusso per il flusso di output standard con statico [WriteLine] [ console-writeline] metodo.


    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }


Il codice riduttore nel file wc.cs utilizza [StreamReader] [ streamreader] oggetto da leggere caratteri dal flusso di input standard che sono stati output per il mapping degli cat.exe. Durante la lettura di caratteri con [WriteLine] [ console-writeline] metodo, Conta le parole contando spazi e caratteri di fine della riga alla fine di ogni parola. Viene quindi scritto il totale per il flusso di output standard con [WriteLine] [ console-writeline] metodo.





## <a name="appendix-c---the-pi-estimator-source-code"></a>Appendice C - il codice sorgente stima di pi greco

La stima di pi greco codice Java che contiene le funzioni di mapping e riduttore è disponibile per la verifica riportata di seguito. Il programma di mapping genera un numero specificato di punti in modo casuale inserito all'interno di un quadrato unità e quindi viene contato il numero di tali punti che si trovano all'interno del cerchio. Il programma riduttore si accumula punti per la proprietà temporaneo contati e quindi restituisce una stima il valore di pi greco da 4R formule, in cui R è il rapporto tra il numero di punti contati all'interno del cerchio e il numero totale di punti che si trovano entro la radice quadrata.

    /**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements. See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership. The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License. You may obtain a copy of the License at
    *
    * http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or   implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.math.BigDecimal;
    import java.util.Iterator;

    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.BooleanWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Writable;
    import org.apache.hadoop.io.WritableComparable;
    import org.apache.hadoop.io.SequenceFile.CompressionType;
    import org.apache.hadoop.mapred.FileInputFormat;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.MapReduceBase;
    import org.apache.hadoop.mapred.Mapper;
    import org.apache.hadoop.mapred.OutputCollector;
    import org.apache.hadoop.mapred.Reducer;
    import org.apache.hadoop.mapred.Reporter;
    import org.apache.hadoop.mapred.SequenceFileInputFormat;
    import org.apache.hadoop.mapred.SequenceFileOutputFormat;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi
    //using quasi-Monte Carlo method.
    //
    //Mapper:
    //Generate points in a unit square
    //and then count points inside/outside of the inscribed circle of the square.
    //
    //Reducer:
    //Accumulate points inside/outside results from the mappers.
    //Let numTotal = numInside + numOutside.
    //The fraction numInside/numTotal is a rational approximation of
    //the value (Area of the circle)/(Area of the square),
    //where the area of the inscribed circle is Pi/4
    //and the area of unit square is 1.
    //Then, Pi is estimated value to be 4(numInside/numTotal).
    //

    public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output
    static private final Path TMP_DIR = new Path(
    PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //2-dimensional Halton sequence {H(i)},
    //where H(i) is a 2-dimensional point and i >= 1 is the index.
    //Halton sequence is used to generate sample points for Pi estimation.
    private static class HaltonSequence {
    // Bases
    static final int[] P = {2, 3};
    //Maximum number of digits allowed
    static final int[] K = {63, 40};

    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialize to H(startindex),
    //so the sequence begins with H(startindex+1).
    HaltonSequence(long startindex) {
    index = startindex;
    x = new double[K.length];
    q = new double[K.length][];
    d = new int[K.length][];
    for(int i = 0; i < K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]];
    }

    for(int i = 0; i < K.length; i++) {
    long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) {
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] * q[i][j];
    }
    }
    }

    //Compute next point.
    //Assume the current point is H(index).
    //Compute H(index+1).
    //@return a 2-dimensional point with coordinates in [0,1)^2
    double[] nextPoint() {
    index++;
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    }
    d[i][j] = 0;
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    }
    return x;
    }
    }

    //Mapper class for Pi estimation.
    //Generate points in a unit square and then
    //count points inside/outside of the inscribed circle of the square.
    public static class PiMapper extends MapReduceBase
    implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

    //Map method.
    //@param offset samples starting from the (offset+1)th sample.
    //@param size the number of samples for this map
    //@param out output {ture->numInside, false->numOutside}
    //@param reporter
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector<BooleanWritable, LongWritable> out,
    Reporter reporter) throws IOException {

    final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
    long numInside = 0L;
    long numOutside = 0L;

    for(long i = 0; i < size.get(); ) {
    //generate points in a unit square
    final double[] point = haltonsequence.nextPoint();

    //count points inside/outside of the inscribed circle of the square
    final double x = point[0] - 0.5;
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++;
    } else {
    numInside++;
    }

    //report status
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples.");
    }
    }

    //output map results
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    }
    }


    //Reducer class for Pi estimation.
    //Accumulate points inside/outside results from the mappers.
    public static class PiReducer extends MapReduceBase
    implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0;
    private long numOutside = 0;
    private JobConf conf; //configuration for accessing the file system

    //Store job configuration.
    @Override
    public void configure(JobConf job) {
    conf = job;
    }


    // Accumulate number of points inside/outside results from the mappers.
    // @param isInside Is the points inside?
    // @param values An iterator to a list of point counts
    // @param output dummy, not used here.
    // @param reporter

    public void reduce(BooleanWritable isInside,
    Iterator<LongWritable> values,
    OutputCollector<WritableComparable<?>, Writable> output,
    Reporter reporter) throws IOException {
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else {
    for(; values.hasNext(); numOutside += values.next().get());
    }
    }

    //Reduce task done, write output to a file.
    @Override
    public void close() throws IOException {
    //write output to a file
    Path outDir = new Path(TMP_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf);
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class, LongWritable.class,
    CompressionType.NONE);
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    }
    }

    //Run a map/reduce job for estimating Pi.
    //@return the estimated value of Pi.
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
    )
    throws IOException {
    //setup job conf
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class);
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // turn off speculative execution, because DFS doesn't handle
    // multiple writers to the same file.
    jobConf.setSpeculativeExecution(false);

    //setup input/output directories
    final Path inDir = new Path(TMP_DIR, "in");
    final Path outDir = new Path(TMP_DIR, "out");
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir);

    final FileSystem fs = FileSystem.get(jobConf);
    if (fs.exists(TMP_DIR)) {
     throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");
     }
     if (!fs.mkdirs(inDir)) {
     throw new IOException("Cannot create input directory " + inDir);
     }

     //generate an input file for each map task
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i);
     final LongWritable offset = new LongWritable(i * numPoints);
     final LongWritable size = new LongWritable(numPoints);
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //start a map/reduce job
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //read outputs
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //compute estimated value
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Parse arguments and then runs a map/reduce job.
    //Print output in standard out.
    //@return a non-zero if there is an error. Otherwise, return 0.
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //main method for running it as a stand alone command.
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }
     
## <a name="appendix-d---the-10gb-graysort-source-code"></a>Appendice D - il codice sorgente graysort di 10gb

Per il controllo in questa sezione è illustrato il codice per il programma TeraSort MapReduce.


    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     *     http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /**
     * Generates the sampled split points, launches the job,
     * and waits for it to finish.
     * <p>
     * To run the program:
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
     */

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**
       * A partitioner that splits text keys into roughly equal
       * partitions in a global sorted order.
       */

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];

          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p,
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }

      }

      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }
    }














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
