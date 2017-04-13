<properties
   pageTitle="Ottimizzare le query Hive esecuzione più veloce in HDInsight | Microsoft Azure"
   description="Informazioni su come ottimizzare le query Hive per Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Ottimizzare le query Hive per Hadoop in HDInsight

Per impostazione predefinita, i cluster Hadoop non sono ottimizzati per prestazioni ottimali. Questo articolo illustra alcune dei metodi di ottimizzazione Hive più comuni che è possibile applicare alla query.

##<a name="scale-out-worker-nodes"></a>Scalabilità nodi di lavoro

Aumentare il numero dei nodi di lavoro in un cluster possibile sfruttare le altre proprietà temporaneo e riduttori per l'esecuzione in parallelo. Esistono due modi per aumentare scalabilità HDInsight:

- Durante la disposizione, è possibile specificare il numero dei nodi di lavoro tramite il portale di Azure, PowerShell Azure o interfaccia riga di comando multipiattaforma.  Per ulteriori informazioni, vedere [cluster di provisioning HDInsight](hdinsight-provision-clusters.md). La schermata seguente mostra il lavoro configurazione nodo nel portale di Azure:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- In fase di esecuzione, è possibile anche potenziare un cluster senza ricreare uno. Come illustrato di seguito.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Per ulteriori informazioni sulle diverse macchine virtuali supportate da HDInsight, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Attivare Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) è un motore di esecuzione alternativo al motore di MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]


Tez è più veloce perché:

- Eseguire indirizzati aciclici grafico (DAG) come un singolo processo del motore di MapReduce, DAG espresso richiede ogni set di proprietà temporaneo deve essere seguito da un set di riduttori. In questo modo più processi MapReduce essere rimosso per ogni query Hive. Tez non dispone di tale vincolo e può elaborare DAG complesse con un'unica operazione per ridurre al minimo il sovraccarico di avvio di processo.
- **Scrive Avoids non necessarie** A causa di più processi vengano filati per la stessa query Hive nel motore di MapReduce, l'output di ogni processo è destinata a HDFS dati intermedi. Poiché Tez viene ridotto a icona numero di processi per ogni query Hive, è possibile evitare scrittura non necessarie.
- **Ritardi avvio riduce al minimo** Tez meglio in grado di ridurre al minimo ritardo avvio riducendo il numero di temporaneo che è necessario avviare e inoltre a migliorare l'ottimizzazione nell'intera.
- **Riutilizza i contenitori** Ogni volta che è in grado di riutilizzare i contenitori per assicurarsi che la latenza a causa di avvio di contenitori verrà ridotte Tez possibili.
- **Tecniche di ottimizzazione continua** In genere l'ottimizzazione venisse eseguito durante la fase di compilazione. Tuttavia, sono disponibili ulteriori informazioni sulle input che consentono di ottimizzazione migliore in fase di esecuzione. Tez utilizza tecniche di ottimizzazione continua che consente di ottimizzare il piano ulteriormente nella fase di runtime.

Per ulteriori informazioni su questi concetti, fare clic [qui](http://hortonworks.com/hadoop/tez/)

È possibile apportare tutte le query Hive Tez abilitato anteponendo la query con l'impostazione riportata di seguito:

    set hive.execution.engine=tez;

Per i cluster basato su Windows HDInsight, Tez deve essere abilitata al momento del provisioning. Di seguito è uno script di PowerShell Azure di esempio per il provisioning di un cluster di Hadoop con Tez abilitato:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Cluster HDInsight basati su Linux hanno Tez attivata per impostazione predefinita.
    

## <a name="hive-partitioning"></a>Hive partizione

Operazione i/o bottiglia prestazioni principali per l'esecuzione di query Hive è. Se è possibile ridurre la quantità di dati che devono essere letti, è possono migliorare le prestazioni. Per impostazione predefinita, le query Hive digitalizzare intere tabelle Hive. Questo è ideale per le query come tabella analizza, tuttavia per le query che solo necessario eseguire la scansione una piccola quantità di dati (ad esempio query con il filtro), verrà creato inutile. Hive partizioni consente di accedere a solo la quantità di dati in tabelle Hive necessaria query Hive.

Hive partizioni viene implementata da riorganizzare i dati non elaborati nelle nuove directory con ogni partizione con il proprio directory - nel punto in cui la partizione è definita dall'utente. Il diagramma seguente illustra partizione di una tabella Hive in base alla colonna *Year*. Viene creata una nuova directory per ciascun anno.

![partizione][image-hdi-optimize-hive-partitioning_1]

Alcune considerazioni partizioni:

- **Eseguire non insufficiente partizione** - partizione sulle colonne con solo alcuni valori possono causare pochi partizioni. Partizione in genere, ad esempio, verrà solo creare due partizioni da creare (maschile e femminile), verrà pertanto ridurre la latenza solo da un massimo di metà.

- **Non è eccessivo partizione** - in altri casi, creazione di una partizione su una colonna con un valore univoco (ad esempio ID utente) si verificherà più partizioni causando molte sottolineare sul namenode cluster come sarà necessario gestire grandi quantità di directory.

- **Evitare inclinare** - scegliere con attenzione il tasto partizione in modo che tutte le partizioni sono di dimensioni pari. Un esempio è partizioni in *stato* può causare il numero di record in California da quasi 30 x che Vermont a causa la differenza di popolazione.

Per creare una tabella di partizione, utilizzare la clausola *Suddivisi per* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Dopo aver creata la tabella partizionata, è possibile creare partizioni statica o dinamica partizioni.

- **Partizione statico** significa che già sharded i dati sono contenuti nelle directory appropriate e si possono porre partizioni Hive manualmente in base all'ubicazione directory. Come illustrato nel frammento di codice riportata di seguito.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Partizione dinamico** significa che si desidera Hive per creare automaticamente le partizioni dell'utente. Poiché è già stata creata la tabella partizione dalla tabella di gestione temporanea, è sufficiente è inserire dati nella tabella partizionata, come illustrato di seguito:

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Per ulteriori informazioni, vedere [Tabelle di partizioni](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Utilizzare il formato ORCFile

Hive supporta diversi formati di file. Per esempio:

- **Testo**: questo è il formato di file predefinito e interagisce con maggior parte dei casi
- **Avro**: ideale per scenari di interoperabilità
- **ORCO/Parquet**: più adatta per prestazioni ottimali

Formato ORCO (ottimizzato righe a colonne) è un modo efficiente per archiviare i dati Hive. Confronto con altri formati, ORCO è i seguenti vantaggi:

- supporto per i tipi complessi compresi tipi DateTime e complessi strutturati e semistrutturati
- fino a 70% di compressione
- gli indici ogni 10.000 righe che consentono l'omissione di righe
- un elenco a discesa significativa in esecuzione in fase di esecuzione

Per abilitare il formato ORCO, è prima di tutto creare una tabella con la clausola *archiviato come ORCO*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Inserire quindi i dati alla tabella ORCO dalla tabella di gestione temporanea. Per esempio:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

È possibile leggere informazioni sul formato ORCO [qui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vettorizzazione

Vettorizzazione consente Hive elaborare un batch di righe 1024 insieme anziché l'elaborazione di una riga alla volta. Questo errore indica che le semplici operazioni vengono eseguite più velocemente perché meno codice interno necessarie per l'esecuzione.

Per abilitare vettorizzazione prefisso query Hive con le impostazioni seguenti:

    set hive.vectorized.execution.enabled = true;

Per ulteriori informazioni, vedere [esecuzione di query Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Altri metodi di ottimizzazione

Sono disponibili altri metodi di ottimizzazione che è possibile prendere in considerazione, ad esempio:

- **Hive il bucket:** una tecnica che consente a cluster o segmento grandi set di dati per ottimizzare le prestazioni delle query.
- **L'ottimizzazione del join:** ottimizzazione dell'Hive esecuzione della query si prevede di migliorare l'efficienza delle linee di join e ridurre la necessità di suggerimenti di utente. Per ulteriori informazioni, vedere [ottimizzazione del Join](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **aumentare riduttori**

##<a id="nextsteps"></a>Passaggi successivi
In questo articolo sono state fornite diversi metodi Ottimizzazione query comuni di Hive. Per ulteriori informazioni, vedere gli articoli seguenti:

- [Utilizzare Hive Apache in HDInsight](hdinsight-use-hive.md)
- [Analizzare i dati dei ritardi volo usando Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analizzare i dati di Twitter utilizzando Hive in HDInsight](hdinsight-analyze-twitter-data.md)
- [Analizzare i dati sensore attraverso la Console di Query Hive Hadoop in HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Usare Hive con HDInsight per analizzare i registri da siti Web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
