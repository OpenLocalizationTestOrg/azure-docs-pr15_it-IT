## <a name="receive-messages-with-apache-storm"></a>Ricevere messaggi con eccesso Apache

[**Apache eccesso**](https://storm.incubator.apache.org) è un sistema di calcolo in tempo reale distribuito che semplifica l'elaborazione affidabile di illimitata flussi di dati. In questa sezione viene illustrato come utilizzare un beccuccio eccesso hub evento per ricevere gli eventi da hub di evento. Usa il Apache, è possibile dividere eventi tra più processi ospitati in nodi diversi. L'integrazione di evento hub con eccesso semplifica consumo evento trasparente arresto dello stato di avanzamento tramite installazione Zookeeper dell'eccesso, la gestione di punti di controllo permanenti e parallelo riceve da hub di evento.

Per ulteriori informazioni sull'evento hub ricevere motivi, vedere la [Panoramica hub evento][].

In questa esercitazione utilizza un'installazione [HDInsight eccesso][] , viene fornito con beccuccio hub evento già disponibile.

1. Seguire la procedura [Eccesso HDInsight - Guida introduttiva](../articles/hdinsight/hdinsight-storm-overview.md) per creare un nuovo cluster HDInsight e connettersi tramite Desktop remoto.

2. Copia il `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` file all'ambiente di sviluppo locale. Questa pagina contiene beccuccio di eccesso eventi.

3. Usare il comando seguente per installare il pacchetto nell'archivio Maven locale. In questo modo è possibile aggiungere come riferimento nel progetto eccesso in un passaggio successivo.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. In Eclisse, creare un nuovo progetto Maven (fare clic su **File**, quindi **Nuovo**, quindi **Project**).

    ![][12]

5. Selezionare **Usa un'area di lavoro predefinita**e quindi fare clic su **Avanti**

6. Selezionare il sistema di **Guida introduttiva di sistema per maven** per, quindi fare clic su **Avanti**

7. Inserire un **ID gruppo** e **un ArtifactId**, quindi fare clic su **Fine**

8. In **pom.xml**, aggiungere le dipendenze seguenti nel `<dependency>` nodo.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. Nella cartella **src** , creare un file denominato **Config.properties** e copiare il contenuto seguente sostituendo i valori seguenti:

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    Il valore **eventhub.receiver.credits** determina il numero di eventi si esegue il batch prima di rilasciarli pipeline eccesso. Per semplicità, in questo esempio questo valore su 10. Produzione, in genere impostarlo per i valori più alti; ad esempio 1024.

10. Creare una nuova classe denominata **LoggerBolt** con il codice seguente:

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    La può essere eccesso registra il contenuto degli eventi ricevuti. Può essere esteso facilmente per archiviare tuple in un servizio di archiviazione. [Esercitazione di analisi sensore HDInsight] Usa lo stesso approccio per archiviare i dati nella HBase.

11. Creare una classe denominata **LogTopology** con il codice seguente:

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Questa classe crea un nuovo hub evento beccuccio, utilizzando le proprietà nella configurazione archiviarlo per instatiate. È importante tenere presente che questo esempio crea quante più attività spouts come il numero delle partizioni nell'Hub evento per utilizzare il parallelismo massimo consentito dall'Hub tale evento.

<!-- Links -->
[Cenni preliminari sui hub di eventi]: ../articles/event-hubs/event-hubs-overview.md
[HDInsight eccesso]: ../articles/hdinsight/hdinsight-storm-overview.md
[Esercitazione di analisi sensore HDInsight]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png