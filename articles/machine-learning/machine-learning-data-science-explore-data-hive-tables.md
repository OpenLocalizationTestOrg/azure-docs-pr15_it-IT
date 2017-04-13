<properties
    pageTitle="Esplorare i dati in tabelle Hive con le query Hive | Microsoft Azure"
    description="Esplorare i dati in tabelle Hive l'utilizzo delle query Hive."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Esplorare i dati in tabelle Hive con le query Hive

Questo documento offre script di esempio Hive utilizzati per esplorare i dati in tabelle Hive in un cluster di HDInsight Hadoop.

I collegamenti seguenti **menu** ad argomenti che descrivono come utilizzare gli strumenti per esplorare i dati da vari ambienti di spazio di archiviazione.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia:

* Creare un account di archiviazione Azure. Se è necessario istruzioni, vedere [creare un account di archiviazione Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight. Se è necessario istruzioni, vedere [Personalizzare Azure HDInsight Hadoop cluster per Analitica avanzate](machine-learning-data-science-customize-hadoop-cluster.md).
* I dati sono stati caricati alle tabelle Hive cluster di Azure HDInsight Hadoop. In caso contrario, seguire le istruzioni fornite in [creare e caricare i dati alle tabelle Hive](machine-learning-data-science-move-hive-tables.md) per caricare dati in tabelle Hive prima di tutto.
* Accesso remoto al cluster attivato. Se è necessario istruzioni, vedere [accesso Hadoop Cluster testa nodi](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Se è necessario istruzioni su come inviare query Hive, informazioni su [come inviare query Hive](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Script di query Hive di esempio per l'esplorazione dei dati

1. Ottenere il numero di osservazioni per partizione `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Ottenere il numero di osservazioni al giorno `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Ottenere i livelli di una colonna di categoria  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Ottenere il numero di livelli in una combinazione di due colonne di categoria `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Ottenere la distribuzione per colonne numeriche  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Estrarre i record da unire due tabelle

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Script di query aggiuntive per gli scenari di dati di andata e ritorno taxi

Esempi di query specifici di [Dati di andata e ritorno Taxi Roma](http://chriswhong.com/open-data/foil_nyc_taxi/) scenari vengono forniti anche [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)repository. Queste query già schema dati specificato e sono pronte per essere inviato per l'esecuzione.
