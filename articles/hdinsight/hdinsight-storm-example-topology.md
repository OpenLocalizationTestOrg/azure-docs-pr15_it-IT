<properties
 pageTitle="Esempi di topologie Apache eccesso nella HDInsight | Microsoft Azure"
 description="Un elenco di esempi di topologie eccesso creati e testati con eccesso Apache nella HDInsight inclusi topologie c# e Java base e l'uso con gli hub di evento."
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

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Esempio eccesso toplogies e dei componenti per eccesso Apache in HDInsight

Di seguito è un elenco di esempi creati e gestiti da Microsoft per l'utilizzo con eccesso Apache su HDInsight. In questi esempi una vasta gamma di argomenti, dalla creazione base c# e Java topologie per l'uso di servizi Azure, ad esempio hub di evento, DocumentDB, Power BI, Database SQL, HBase HDInsight e lo spazio di archiviazione di Azure. Alcuni esempi viene inoltre illustrano come usare le tecnologie non Azure o persino non Microsoft, ad esempio SignalR e Socket.IO

| Descrizione                                                                                             | Di seguito viene                                         | Lingua/Framework         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Scrivere Azure dati Lake archivio da Apache eccesso](hdinsight-storm-write-data-lake-store.md) | Per la scrittura all'archivio Lake dati di Azure | Java |
| [Origine evento Hub Spout e bulloni](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Origine per l'evento Hub beccuccio e bulloni | Java |
| [Sviluppare topologie basato su Java per eccesso Apache in HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Sviluppare c# topologie per eccesso Apache su HDInsight utilizzando Visual Studio][16fce2d1]                     | HDInsight Tools per Visual Studio                    | C#, Java                   |
| [Creare più flussi di dati in una topologia c# eccesso][ec5a4064]                                         | Flussi di più                                     | C#                         |
| [Determinare gli argomenti della tendenze Twitter con eccesso su HDInsight][3c86c7c8]                                   | Trident                                              | Linguaggio, Trident              |
| [Elaborare gli eventi da Azure evento hub con eccesso su HDInsight (c#)][844d1d81]                                | Hub di evento                                           | C# e Java                |
| [Elaborare gli eventi da Azure evento hub con eccesso su HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Hub di evento | Java |
| [Usare Power Bi per visualizzare i dati da una topologia eccesso][94d15238]                              | Power BI                                             | C#                         |
| [Analisi dei dati sensore con eccesso e HBase in HDInsight][ab894747]                                     | Evento hub, HBase, Socket.IO, del dashboard Web          | C#, Java, JavaScript, HTML |
| [Elaborare dati sensore veicolo da hub di evento in eccesso HDInsight][246ee964]                        | Evento hub, DocumentDb, Azure archiviazione Blob (WASB)    | C#, Java                   |
| [Estrazione, trasformazione e caricamento (ETL) da Azure evento hub per HBase in eccesso HDInsight][b4b68194] | Hub di evento, HBase                                    | C#                         |
| [Progetto di topologia c# il modello per l'uso di servizi Azure eccesso su HDInsight][ce0c02a2]  | Evento hub, DocumentDb, SQL Database, HBase, SignalR | C#, Java                   |
| [Benchmark scalabilità per la lettura da Azure evento hub in eccesso HDInsight][d6c540e3]           | Velocità effettiva dei messaggi, eventi hub, Database SQL         | C#, Java                   |
| [Correlare gli eventi in eccesso e HBase HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Usare Python con eccesso su HDInsight](hdinsight-storm-develop-python-topology.md) | Componenti di Python con Java e Clojure in base a topologie eccesso | Python |

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva a eccesso Apache in HDInsight][2b8c3488]

* [Informazioni su come distribuire e gestire topologie eccesso con eccesso su HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Informazioni su come creare un eccesso cluster HDInsight e utilizzare il Dashboard eccesso per distribuire topologie di esempio."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Informazioni su come distribuire e gestire topologie utilizzando il Dashboard di eccesso basata sul web ed eccesso dell'interfaccia utente o gli strumenti di HDInsight per Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Informazioni su come creare topologie c# eccesso tramite gli strumenti di HDInsight per Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Informazioni su come creare topologie eccesso in Java, utilizzando Maven, tramite la creazione di una topologia di base wordcount."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Viene illustrato come scrivere dati in Power BI da una topologia c# e quindi creare un grafico e dashboard dai dati."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Viene descritta una topologia eccesso base che esegue una wordcount implementata in c#. Inoltre, questo viene illustrato come creare più flussi di dati all'interno di una topologia c#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Informazioni su come leggere e scrivere dati da Azure evento hub con eccesso su HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Informazioni su come usare eccesso Apache HDInsight per elaborare dati sensore da Azure evento hub, visualizzare utilizzando D3.js e, facoltativamente, archiviarlo in HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Informazioni su come usare Trident per creare una topologia eccesso che determina la tendenza argomenti (in base hashtags,) su Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Informazioni su come utilizzare una topologia eccesso per leggere i messaggi da Azure evento hub, leggere i documenti da Azure DocumentDB di fare riferimento ai dati e salvare i dati allo spazio di archiviazione Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Diverse topologie per illustrare velocità durante la lettura da Azure evento hub e archiviazione al Database di SQL in eccesso Apache HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Informazioni su come leggere i dati da hub evento Azure, aggregare e trasformare i dati, quindi archiviarlo in HBase in HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Il progetto contiene modelli per spouts, bulloni e topologie per interagire con diversi servizi di Azure come hub di evento, DocumentDB e Database SQL."
 
