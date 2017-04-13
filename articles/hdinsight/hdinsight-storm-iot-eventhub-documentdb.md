<properties
 pageTitle="Elaborare dati sensore veicolo con eccesso Apache nella HDInsight | Microsoft Azure"
 description="Informazioni su come elaborare dati sensore veicolo da hub di evento in eccesso Apache HDInsight. Aggiungere dati del modello da DocumentDB e archiviare output allo spazio di archiviazione."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Elaborare dati sensore veicolo da Azure evento hub in eccesso Apache HDInsight

Informazioni su come elaborare dati sensore veicolo da Azure evento hub in eccesso Apache HDInsight. In questo esempio legge i dati sensore da Azure evento hub faciliti la i dati, fare riferimento ai dati archiviati in Azure DocumentDB e infine archivia i dati in archiviazione Azure con il sistema di File Hadoop (HDFS).

![HDInsight e il diagramma di architettura Internet di elementi (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Panoramica

Aggiunta di sensori a veicoli consente di prevedere le tendenze nei dati cronologici in base problemi di apparecchiature, nonché di apportare miglioramenti alle versioni future in base all'analisi di utilizzo motivo. Durante l'elaborazione di batch MapReduce tradizionale può essere usata per l'analisi, è necessario essere possibile veloce ed efficiente caricare i dati da tutti i veicoli in Hadoop prima che venga eseguita l'elaborazione di MapReduce. Inoltre, è consigliabile eseguire analisi per i percorsi di errore critico (temperatura del motore, freni, ecc.) in tempo reale.

Hub evento Azure sono progettate per gestire il volume enorme dei dati generati da sensori e Apache eccesso nella HDInsight può essere utilizzato per caricare ed elaborare i dati prima dell'archiviazione nel HDFS (supportate da archiviazione Azure) per un'ulteriore elaborazione MapReduce.

##<a name="solution"></a>Soluzione

Dati di telemetria per temperatura del motore, temperatura e la velocità di veicolo sono registrati dai sensori, quindi inviati a un evento hub insieme veicolo identificazione numero (VIN di Auto) e un indicatore di data e ora. A questo punto, una topologia eccesso in esecuzione su un eccesso Apache cluster HDInsight legge i dati, viene elaborato e archivia in HDFS.

Durante l'elaborazione, il VIN viene utilizzato per recuperare le informazioni sul modello da Azure DocumentDB. Viene aggiunto al flusso di dati prima di essere archiviato.

I componenti utilizzati nella topologia di eccesso sono:

* **EventHubSpout** - legge i dati da Azure evento hub

* **TypeConversionBolt** - viene convertita la stringa JSON da hub di evento in una tupla contenente i valori di dati individuali per temperatura del motore, temperatura, velocità, VIN e timestamp

* **DataReferencBolt** - Cerca il modello di veicolo da DocumentDB utilizzando il VIN

* **WasbStoreBolt** - sono archiviati i dati HDFS (spazio di archiviazione Azure)

Di seguito è un diagramma di questa soluzione:

![topologia eccesso](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Si tratta di un diagramma semplificato e ogni componente della soluzione potrebbe essere più istanze. Ad esempio, più istanze di ogni componente nella topologia di vengono distribuite tra i nodi in eccesso cluster HDInsight.

##<a name="implementation"></a>Implementazione

Un completamento automatico soluzione per questo scenario è disponibile come parte dell'archivio [Esempi di eccesso HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) su GitHub. Per utilizzare questo esempio, seguire i passaggi descritti in [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori esempi di topologie eccesso, vedere [esempi di topologie per eccesso in HDInsight](hdinsight-storm-example-topology.md).
