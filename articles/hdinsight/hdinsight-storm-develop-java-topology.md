<properties
   pageTitle="Sviluppare topologie basato su Java per eccesso Apache | Microsoft Azure"
   description="Informazioni su come creare topologie eccesso in linguaggio mediante la creazione di una topologia di conteggio word semplice."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

#<a name="develop-java-based-topologies-for-a-basic-word-count-application-with-apache-storm-and-maven-on-hdinsight"></a>Sviluppare basato su Java topologie per un'applicazione di base del conteggio di parole con eccesso Apache e Maven su HDInsight

Informazioni su come creare una topologia di linguaggio per eccesso Apache su HDInsight utilizzando Maven. Verificherà il processo di creazione di un'applicazione di base del conteggio di parole con Maven e Java, in cui la topologia è definita in linguaggio. Quindi, viene spiegato definire la topologia utilizzando il framework luminoso.

> [AZURE.NOTE] Il framework luminoso è disponibile in eccesso 0.10.0 o versione successiva. Eccesso 0.10.0 è disponibile con HDInsight 3.3 e 3.4.

Dopo aver completato la procedura descritta in questo documento, si avrà una topologia di base che è possibile distribuire in eccesso Apache su HDInsight.

> [AZURE.NOTE] Una versione completa di topologie creata in questo documento è disponibile in [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##<a name="prerequisites"></a>Prerequisiti

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Linguaggio sviluppo Kit (JDK) versione 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven è un sistema di compilazione di progetto per i progetti di linguaggio.

* Un editor di testo, ad esempio Blocco note, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Testo Sublime</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Oppure è possibile utilizzare un ambiente di sviluppo integrato (IDE), ad esempio <a href="https://eclipse.org/" target="_blank">Eclisse</a> (versione Luna o versioni successive).

    > [AZURE.NOTE] L'editor o IDE potrebbe essere funzionalità specifiche per l'utilizzo di Maven non trattate in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto in uso.

##<a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate durante l'installazione di Java e JDK. Tuttavia, è necessario verificare che siano già presenti e che contengono i valori corretti per i singoli sistemi.

* **JAVA_HOME** - deve fare riferimento alla directory in cui è installato l'ambiente di runtime del linguaggio (JRE). Ad esempio, in una distribuzione Unix o Linux deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, ha un valore simile a`c:\Program Files (x86)\Java\jre1.7`

* **Percorso** - deve contenere i seguenti percorsi:

    * **JAVA_HOME** (o il percorso equivalente)

    * **JAVA_HOME\bin** (o il percorso equivalente)

    * Directory in cui è installato Maven

##<a name="create-a-new-maven-project"></a>Creare un nuovo progetto Maven

Dalla riga di comando, utilizzare il codice riportato di seguito per creare un nuovo progetto Maven denominato **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Verrà creato una nuova directory denominata **WordCount** nella posizione corrente, che contiene un progetto di Maven base.

La directory **WordCount** conterrà gli elementi seguenti:

* **POM.XML**: contiene le impostazioni per il progetto Maven.

* **src\main\java\com\microsoft\example**: contiene il codice dell'applicazione.

* **src\test\java\com\microsoft\example**: contiene test per l'applicazione. In questo esempio abbiamo non creeranno test.

###<a name="remove-the-example-code"></a>Rimuovere il codice di esempio

Dato che verrà creata l'applicazione, eliminare il test generato e i file dell'applicazione:

*  **src\test\java\com\microsoft\example\AppTest.Java**

*  **src\main\java\com\microsoft\example\App.Java**

##<a name="add-properties"></a>Aggiungi proprietà

Maven consente di definire i valori di livello di progetto chiamati proprietà. Aggiungere quanto segue dopo il `<url>http://maven.apache.org</url>` riga:

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

È ora possibile utilizzare questi valori in altre sezioni. Ad esempio, quando si specifica la versione di componenti eccesso, è possibile utilizzare `${storm.version}` invece di livello di codice un valore.

##<a name="add-dependencies"></a>Aggiungere le dipendenze

Poiché si tratta di una topologia eccesso, è necessario aggiungere una dipendenza per i componenti eccesso. Aprire il file **pom.xml** e aggiungere il codice seguente nel ** &lt;dipendenze >** sezione:

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

In fase di compilazione Maven utilizza queste informazioni per cercare **il core** repository Maven. Eseguita la ricerca nell'archivio sul computer locale. Se i file non sono presente, sarà scaricarle dal repository Maven pubblico e archiviarli nell'archivio locale.

> [AZURE.NOTE] Avviso di `<scope>provided</scope>` riga nella sezione abbiamo aggiunto. In questo modo Maven escludere **eccesso core** da qualsiasi file VASO che creiamo, poiché saranno fornito dal sistema. In questo modo, i pacchetti di creare per diventare un po' più piccolo e garantisce che utilizzano i bit **eccesso core** inclusi in eccesso cluster HDInsight.

##<a name="build-configuration"></a>Configurazione della build

Plug-in di Maven consente di personalizzare le fasi di sviluppo del progetto, ad esempio la modalità di compilazione di progetto o come assemblare in un file di VASO. Aprire il file **pom.xml** e aggiungere il codice seguente direttamente sopra la `</project>` riga.

    <build>
      <plugins>
      </plugins>
      <resources>
      </resources>
    </build>

In questa sezione verrà utilizzata per aggiungere plug-in, risorse e altre opzioni di configurazione della build. Per un riferimento completo del file __pom.xml__ , vedere [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

###<a name="add-plug-ins"></a>Aggiungere plug-in

Per topologie eccesso, il <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Plug-in Maven Exec</a> è utile perché consente di eseguire facilmente la topologia in locale nel proprio ambiente di sviluppo. Aggiungere le operazioni seguenti per la `<plugins>` sezione del file **pom.xml** per includere il plug-in Maven Exec:

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [AZURE.NOTE] Si noti che la `<mainClass>` voce ottimali `${storm.topology}`. Abbiamo non definiscono l'oggetto precedente nella sezione proprietà (ma di). Se, tuttavia, si questo valore verrà impostato dalla riga di comando durante l'esecuzione della topologia in ambiente di sviluppo in un passaggio successivo.

Un altro utile plug-in non <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven compilatore plug-in</a>, che consente di modificare le opzioni di compilazione. Il motivo principale che è necessario consiste nel modificare la versione Java Maven utilizzata per l'origine e destinazione per l'applicazione. Vogliamo versione 1.7.

Aggiungere le seguenti operazioni nel `<plugins>` sezione del file **pom.xml** per includere il plug-in Apache Maven compilatore e impostare le versioni di origine e destinazione 1.7.

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###<a name="configure-resources"></a>Configurare le risorse

Sezione delle risorse consente di includere risorse non di codice, ad esempio file di configurazione necessari dai componenti della topologia. In questo esempio aggiungere quanto segue nel `<resources>` sezione del file **pom.xml** .

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Verrà aggiunto nella directory di risorse nella radice del progetto (`${basedir}`) come percorso che contiene le risorse e include il file denominato __log4j2.xml__. Il file viene utilizzato per configurare le informazioni che ha eseguito l'accesso tramite la topologia.

##<a name="create-the-topology"></a>Creare la topologia

Una topologia basato su Java eccesso è costituito da tre componenti che è necessario creare (o riferimento) come dipendenza.

* **Spouts**: legge origini e genera flussi di dati nella topologia di dati dall'esterno.

* **Bulloni**: elaborazione su flussi emessi da spouts o altri bulloni e genera uno o più flussi.

* **Topologia**: consente di definire come del spouts bulloni sono disposti e fornisce il punto di ingresso per la topologia.

###<a name="create-the-spout"></a>Creare beccuccio

Per ridurre i requisiti per l'impostazione di origini dati esterne, beccuccio seguente emette semplicemente frasi casuali. Si tratta di una versione modificata del beccuccio fornito con [alcuni esempi di eccesso Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [AZURE.NOTE] Per un esempio di un beccuccio che legge da un'origine dati esterna, vedere uno degli esempi seguenti:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): un beccuccio di esempio che legge da Twitter
>
> * [Eccesso Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): beccuccio che legge da Kafka

Per beccuccio, creare un nuovo file denominato **RandomSentenceSpout.java** nella directory **src\main\java\com\microsoft\example** e utilizza la seguente come il contenuto:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Richiedere alcuni minuti per leggere i commenti di codice per comprendere il funzionamento di questa beccuccio.

> [AZURE.NOTE] Anche se questa topologia viene utilizzato un solo beccuccio, gli altri utenti potrebbe essere diverse da feed di dati provenienti da origini diverse nella topologia.

###<a name="create-the-bolts"></a>Creare i bulloni

Bulloni gestiscono l'elaborazione dei dati. Per questa topologia, sono disponibili due bulloni:

* **SplitSentence**: divide le frasi emesse da **RandomSentenceSpout** in singole parole.

* **WordCount**: conta quante volte si è verificato ogni parola.

> [AZURE.NOTE] Bulloni possono effettuare in forma letterale alcun elemento, ad esempio calcolo, persistenza o parla componenti esterni.

Creare due nuovi file, **SplitSentence.java** e **WordCount.Java** nella directory **src\main\java\com\microsoft\example** . Utilizzare la seguente come contenuto per i file:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

Richiedere alcuni minuti per leggere i commenti di codice per comprendere il funzionamento di ogni bulloni.

###<a name="define-the-topology"></a>Definire la topologia

La topologia collega la spouts e bulloni insieme in un grafico, che definisce il flusso di dati tra i componenti. Fornisce inoltre suggerimenti parallelismo usata in eccesso quando si creano istanze dei componenti all'interno del cluster.

Di seguito è un diagramma base del grafico dei componenti per questa topologia.

![diagramma che mostra la disposizione spouts e bulloni](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Per implementare la topologia, creare un nuovo file denominato **WordCountTopology.java** nella directory **src\main\java\com\microsoft\example** . Utilizzare la seguente come contenuto per il file:

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Richiedere alcuni minuti per leggere i commenti di codice per comprendere come la topologia viene definita e quindi inviata al cluster.

###<a name="configure-logging"></a>Configurare la registrazione

Eccesso utilizza Apache Log4j per registrare le informazioni. Se non si configura la registrazione, la topologia genererà numerose informazioni di diagnostica che possono essere difficile da leggere. Per controllare quali è stato effettuato l'accesso, creare un file denominato __log4j2.xml__ nella directory __risorse__ . Di seguito vengono il contenuto del file.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Consente di configurare un nuovo logger per la classe __com.microsoft.example__ , che include i componenti in questo esempio di topologia. Il livello è impostato su traccia per questo logger, che consente di acquisire tutte le informazioni di registrazione create dai componenti di questa topologia. Se si osserva il codice per il progetto, si noterà che il file di WordCount.java consente di implementare la registrazione. verrà registrato il conteggio di ogni parola.

Il `<Root level="error">` sezione Configura il livello radice di registrazione (tutti gli elementi non __com.microsoft.example__) per registrare solo le informazioni sull'errore.

> [AZURE.IMPORTANT] Mentre si riducono le informazioni registrate quando si verifica una topologia nel proprio ambiente di sviluppo, ma non tutte le informazioni di debug prodotte durante l'esecuzione in un cluster di produzione. Per ridurre le informazioni, è necessario impostare il debug su false nella configurazione inviata al cluster. Visualizzare il codice WordCountTopology.java in questo documento per un esempio. 

Per ulteriori informazioni sulla configurazione della registrazione per Log4j, vedere [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [AZURE.NOTE] Versione eccesso 0.10.0 utilizza Log4j 2. x. Le versioni precedenti di eccesso utilizzato Log4j 1. x, che utilizzato un formato diverso per la configurazione di registro. Per informazioni sulla configurazione di versioni precedenti, vedere [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

##<a name="test-the-topology-locally"></a>Testare la topologia localmente

Dopo aver salvato i file, utilizzare il comando seguente per verificare la topologia in locale.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Durante l'esecuzione della topologia verrà visualizzate le informazioni di avvio. Quindi inizia visualizzare le righe simile al seguente come frasi vengono emesso da beccuccio ed elaborate da bulloni.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Esaminando registrazione emessa da bulloni WordCount, è possibile vedere che ' and' è stato emesso 113 ore. Il numero totale di continuerà a salire di come viene eseguita la topologia perché beccuccio genera continuamente le stesse frasi.

Si verificherà un un secondo intervallo di 5 tra emissione delle parole e i conteggi. Ciò avviene perché il componente __WordCount__ è configurato per generare solo informazioni quando arriva una tupla dei segni di graduazione e richiede che tali tuple vengono inviate solo 5 secondi per impostazione predefinita.

## <a name="convert-the-topology-to-flux"></a>Convertire la topologia di flusso

Flusso è un nuovo framework disponibile con eccesso 0.10.0, che consente di separare configurazione dall'implementazione. I componenti (bulloni e spouts,) sono ancora definiti in linguaggio, ma la topologia è definita tramite un file YAML.

Il file YAML definisce i componenti da utilizzare per la topologia flusso dei dati tra di esse e i valori da utilizzare durante l'inizializzazione dei componenti. È possibile includere un file YAML come parte del file vaso contenente il progetto quando si distribuisce o è possibile utilizzare un file YAML esterno quando si avvia la topologia.

1. Spostare il file __WordCountTopology.java__ fuori del progetto. In precedenza, questo definito la topologia, ma è non verrà utilizzata per luminoso.

2. Creare un nuovo file denominato __topology.yaml__nella directory __risorse__ . Di seguito vengono il contenuto del file.

        # topology definition

        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"

        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1

        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1

        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1

        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1

        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE

        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]

    Richiedere alcuni minuti per leggere e comprendere che cosa significa ogni sezione e la correlazione con la definizione basato su Java nel file __WordCountTopology.java__ .

3. Apportare le modifiche seguenti al file __pom.xml__ .

    * Aggiungere la nuova dipendenza seguente nel `<dependencies>` sezione:

            <!-- Add a dependency on the Flux framework -->
            <dependency>
                <groupId>org.apache.storm</groupId>
                <artifactId>flux-core</artifactId>
                <version>${storm.version}</version>
            </dependency>

    * Aggiungere il plug-in seguenti per la `<plugins>` sezione. Il plug-in gestisce la creazione di un pacchetto (file vaso) per il progetto e applica alcune trasformazioni specifiche luminoso durante la creazione del pacchetto.

            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                        <!-- Keep us from getting a "can't overwrite file error" -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                        <!-- We're using Flux, so refer to it as main -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>org.apache.storm.flux.Flux</mainClass>
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
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
                    </execution>
                </executions>
            </plugin>

    * Nel __plug-in exec maven__ `<configuration>` delle sezioni, modificare il valore di `<mainClass>` a `org.apache.storm.flux.Flux`. In questo modo luminoso gestire in esecuzione la topologia quando si esegue localmente in fase di sviluppo.

    * Nel `<resources>` delle sezioni, aggiungere le operazioni seguenti per la `<includes>`. Incluso il file YAML che definisce la topologia come parte del progetto.
    
            <include>topology.yaml</include>

## <a name="test-the-flux-topology-locally"></a>Testare la topologia luminoso localmente

1. Utilizzare le operazioni seguenti per compilare ed eseguire la topologia di luminoso utilizzando Maven.

        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    
    Se si utilizza PowerShell, utilizzare le operazioni seguenti:
    
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    Se si sono in un sistema Linux/Unix/OS X e che sia [installato eccesso nel proprio ambiente di sviluppo](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), è possibile utilizzare invece i comandi seguenti:

        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml

    Il `--local` parametro consente di eseguire la topologia in modalità locale nel proprio ambiente di sviluppo. Il `-R /topology.yaml` parametro utilizza il `topology.yaml` risorsa file dal file vaso per definire la topologia.

    Durante l'esecuzione della topologia verrà visualizzate le informazioni di avvio. Quindi inizia visualizzare le righe simile al seguente come frasi vengono emesso da beccuccio ed elaborate da bulloni.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    
    Si verificherà un ritardo di secondo 10 tra blocchi delle informazioni registrate, come il `topology.yaml` file passa un valore di `10` quando viene creato il componente WordCount. Si imposta l'intervallo di ritardo per tupla dei segni di graduazione a 10 secondi.

2.  Creare una copia del `topology.yaml` file dal progetto. Chiamare simile al `newtopology.yaml`. Nel file, individuare il seguente sezione e impostare il valore di `10` a `5`. Questa operazione modificherà l'intervallo tra la creazione batch con un numero di parole dal 10 secondi a 5.

          - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 5
            parallelism: 1

3. Per eseguire la topologia, usare il comando seguente:

        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"

    In alternativa, se si dispone di eccesso l'ambiente di sviluppo Linux/Unix/OS X:

        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml

    Modificare il `/path/to/newtopology.yaml` il percorso del file newtopology.yaml creato nel passaggio precedente. Questo comando verrà utilizzato il newtopology.yaml come la definizione della topologia. Dal momento che non sono incluse le `compile` parametro, Maven riutilizza la versione del progetto creato in precedenza.

    Una volta che si avvia la topologia, si noterà che è stato modificato il tempo tra batch generato in base al valore in newtopology.yaml. In modo che è possibile vedere che è possibile modificare la configurazione tramite un file di YAML senza dover ricompilare della topologia.

Sono disponibili altre funzionalità luminoso forniti che non sono descritti in questo caso, ad esempio la sostituzione delle variabili nel file YAML in base ai parametri passati in fase di esecuzione o dalle variabili di ambiente. Per ulteriori informazioni su queste e altre funzionalità di framework luminoso, vedere [luminoso (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

##<a name="trident"></a>Trident

Trident è un'astrazione di alto livello fornita da eccesso. Supporta elaborazione informazioni sullo stato. Il vantaggio di Trident è che può garantire che tutti i messaggi che immette la topologia siano elaborato una sola volta. Questo è difficile da eseguire in una topologia di linguaggio non elaborata, quali garanzia che i messaggi verrà elaborato almeno una volta. Sono disponibili anche altre differenze, ad esempio componenti incorporati che possono essere usati invece di creare bulloni. Infatti, bulloni completamente vengono sostituiti dai componenti meno generico, ad esempio filtri, previsioni e funzioni.

Applicazioni tridente possono essere create utilizzando Maven progetti. Utilizzare gli stessi passaggi di base, come illustrato in precedenza in questo articolo, ovvero solo il codice è diverso. Trident anche non (attualmente) essere utilizzata con framework luminoso.

Per ulteriori informazioni su Trident, vedere <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Introduzione all'API Trident</a>.

Per un esempio di un'applicazione di Trident, vedere [Twitter tendenza argomenti con eccesso Apache su HDInsight](hdinsight-storm-twitter-trending.md).

##<a name="next-steps"></a>Passaggi successivi

Acquisite come creare una topologia eccesso tramite linguaggio. A questo punto informazioni su come:

* [Distribuire e gestire topologie Apache eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Sviluppare c# topologie per eccesso Apache su HDInsight utilizzando Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

È possibile trovare altre esempio topologie eccesso visitando [topologie di esempio per eccesso in HDInsight](hdinsight-storm-example-topology.md).
