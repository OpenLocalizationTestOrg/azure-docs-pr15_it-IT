<properties
pageTitle="Utilizzare DataFu con maialino in HDInsight"
description="DataFu è un insieme di raccolte per l'utilizzo con Hadoop. Informazioni su come utilizzare con maialino DataFu il cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Utilizzare DataFu con maialino in HDInsight

DataFu è un insieme di raccolte in Apri origine per l'utilizzo con Hadoop. Nel documento, si apprenderà come usare DataFu il cluster HDInsight e come usare le funzioni definite dall'utente DataFu (utente) con maialino.

##<a name="prerequisites"></a>Prerequisiti

* Un abbonamento Azure.

* Un cluster di Azure HDInsight (Linux o basato su Windows)

* Una conoscenza di base con [maialino in HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Installare DataFu in HDInsight basati su Linux

> [AZURE.NOTE] DataFu viene installato nella versione cluster basati su Linux 3.3 e versioni successive e nei cluster basati su Windows. Non è installato nei cluster basati su Linux precedenti a 3.3.
>
> Se si utilizza una cluster basato su Linux 3.3 o versione successiva oppure un cluster basato su Windows, è possibile ignorare questa sezione.

DataFu può essere scaricato e installato dal repository Maven. Per aggiungere il cluster HDInsight DataFu, procedere come segue:

1. Connettere il cluster basati su Linux HDInsight tramite SSH. Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere uno dei seguenti documenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilizzare il comando seguente per scaricare il file di vaso DataFu utilità wget o copiare e incollare il collegamento nel browser per avviare il download.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Successivamente, caricare il file allo spazio di archiviazione predefinito per il cluster HDInsight. In questo modo disponibile per tutti i nodi del cluster e il file rimarrà nello spazio di archiviazione anche se eliminare e ricreare il cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] Nell'esempio precedente memorizza vaso in `wasbs:///example/jars` dal momento che la directory esiste già nell'archivio cluster. È possibile utilizzare qualsiasi posizione in cui che si desidera archivio cluster HDInsight.

##<a name="use-datafu-with-pig"></a>Utilizzare DataFu con maialino

La procedura descritta in questa sezione presuppone che si ha familiarità con le maialino su HDInsight e solo forniscono istruzioni sull'alfabeto latino maialino, non le informazioni sulle procedure per usarli con il cluster. Per ulteriori informazioni sull'utilizzo di maialino con HDInsight, vedere [Usare maialino con HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Quando si utilizza DataFu da maialino in un cluster basati su Linux HDInsight, è prima necessario registrare il file di vaso con l'istruzione maialino latino seguente:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu registrato per impostazione predefinita nei cluster HDInsight basato su Windows.

Definire in genere un alias per le funzioni DataFu. Per esempio:

    DEFINE SHA datafu.pig.hash.SHA();
    
Definisce un alias denominato `SHA` per di agente hashing funzione. È quindi possibile utilizzare questo script latino maialino per generare un hash per i dati di input. Ad esempio riportato di seguito sostituisce i nomi nei dati di input con un:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Se utilizzata con i dati di input seguenti:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Genera l'output seguente:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul DataFu o maialino, vedere i documenti seguenti:

* [Guida di DataFu maialino Apache](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
