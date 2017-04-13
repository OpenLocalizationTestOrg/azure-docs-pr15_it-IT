<properties
    pageTitle="Eseguire il debug e analizzare i servizi Hadoop con immagini heap | Microsoft Azure"
    description="Raccolta immagini heap per i servizi di Hadoop e inserire all'interno di account di archiviazione Blob Azure per il debug e analisi automaticamente."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Raccogliere heap trasferisce nell'archiviazione Blob per eseguire il debug e analizzare i servizi Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Le immagini heap contenere uno snapshot della memoria dell'applicazione, inclusi i valori delle variabili durante la creazione di dettagli. In modo che siano molto utile per la diagnosi problemi che si verificano in fase di esecuzione. Immagini di heap possono essere automaticamente raccolte per i servizi di Hadoop e inseriti all'interno dell'account di archiviazione Blob Azure di un utente in HDInsightHeapDumps /. 

La raccolta di immagini di heap per diversi servizi deve essere abilitata per i servizi sui singoli cluster. Per impostazione predefinita per questa funzionalità viene essere disattivata per un cluster. Queste immagini heap che possono essere grandi dimensioni, è consigliabile per monitorare l'account di archiviazione Blob nel punto in cui vengono vengono salvati dopo la raccolta è stata abilitata.

> [AZURE.NOTE] Le informazioni in questo articolo si applicano solo a HDInsight basato su Windows. Per informazioni su basati su Linux HDInsight, vedere [abilitare heap immagini per i servizi Hadoop su HDInsight basati su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Servizi per heap immagini

È possibile abilitare heap immagini per i servizi seguenti:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **filati** , resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementi di configurazione che consentono di heap immagini

Per attivare heap immagini di un servizio, è necessario impostare gli elementi di configurazione appropriato nella sezione per tale servizio, specificato da **nome_servizio**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Il valore di **nome_servizio** può essere uno dei servizi elencati sopra: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, o namenode.

## <a name="enable-using-azure-powershell"></a>Abilitare l'utilizzo di PowerShell di Azure

Per attivare immagini heap tramite PowerShell Azure per jobhistoryserver, ad esempio, da eseguire le operazioni seguenti:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Abilitare utilizzando .NET SDK

Per attivare heap immagini con Azure HDInsight .NET SDK per jobhistoryserver, ad esempio, da eseguire le operazioni seguenti:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
