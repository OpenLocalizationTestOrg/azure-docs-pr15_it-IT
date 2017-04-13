<properties 
    pageTitle="Richiamare programmi motori di Azure Data Factory" 
    description="Informazioni su come richiamare programmi motori da una factory di Azure dati utilizzando l'attività MapReduce." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Richiamare programmi motori da Factory dati
## <a name="introduction"></a>Introduzione
È possibile utilizzare l'attività MapReduce nella pipeline di Factory dati per l'esecuzione ad applicazioni il cluster HDInsight Spark. Vedere l'articolo [MapReduce attività](data-factory-map-reduce.md) per informazioni dettagliate sull'utilizzo dell'attività prima di leggere questo articolo. 

## <a name="spark-sample-on-github"></a>Esempio di motori in GitHub
[Motori - Factory dati di esempio in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) viene illustrato come utilizzare attività MapReduce per avviare un programma di motori. I motori appena copiato dati da un contenitore di Blob Azure a un'altra. 

## <a name="data-factory-entities"></a>Entità Factory dati
La cartella **Alimentatore automatico ad/ADFJsons src** contiene file entità Factory dati (servizi collegati, set di dati, pipeline).  

In questo esempio sono disponibili due **servizi collegati** : archiviazione Azure e Azure HDInsight. Specificare il nome di spazio di archiviazione Azure e i valori di chiave in **StorageLinkedService.json** e clusterUri, nome utente e password in **HDInsightLinkedService.json**.

Esistono due **set di dati** in questo esempio: **input.json** e **output.json**. Questi file si trovano nella cartella **set di dati** .  Questi file rappresentano set di dati di input e di output per l'attività MapReduce

Pipeline di esempio disponibili nella cartella **ADFJsons/Pipeline** . Esaminare una pipeline per informazioni su come avviare un programma ad tramite l'attività MapReduce. 

L'attività MapReduce è configurata per richiamare **com.adf.sparklauncher.jar** nel contenitore **adflibs** lo spazio di archiviazione Azure (specificato nel StorageLinkedService.json). Il codice sorgente per questo programma è in motori-alimentatore automatico/src/principali/java/com/alimentatore automatico/cartella e chiama inviare ad ed eseguire i motori processi. 

> [AZURE.IMPORTANT] 
> Leggere [file Leggimi. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) per informazioni più recenti e aggiuntive prima di utilizzarla. 
>  
> Utilizzare cluster HDInsight Spark con questo approccio per richiamare programmi motori utilizzando l'attività MapReduce. Utilizzo di un cluster di HDInsight su richiesta non è supportato.   


## <a name="see-also"></a>Vedere anche
- [Attività di hive](data-factory-hive-activity.md)
- [Maialino attività](data-factory-pig-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Attività di flusso Hadoop](data-factory-hadoop-streaming-activity.md)
- [Richiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
