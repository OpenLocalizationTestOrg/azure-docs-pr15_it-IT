<properties
   pageTitle="Creare cluster HDInsight con Azure dati Lake archivio utilizzare i modelli di gestione risorse | Microsoft Azure"
   description="Usare i modelli di gestione risorse Azure per creare e utilizzare HDInsight cluster con archivio Lake dati di Azure"
   services="data-lake-store,hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Creare un cluster di HDInsight con archivio Lake dati utilizzando il modello di gestione risorse di Azure

> [AZURE.SELECTOR] - [Nel portale](data-lake-store-hdinsight-hadoop-use-portal.md) - [tramite PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md) - [Gestione risorse](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Informazioni su come utilizzare un modello di gestione risorse Azure per configurare un cluster di HDInsight con l'accesso all'archivio Lake dati di Azure. Alcune considerazioni importanti per questa versione:

-   **Per i cluster (Linux) e cluster Hadoop/eccesso (Windows e Linux)**, l'archivio Lake dati può essere usata solo come un account di spazio di archiviazione aggiuntivo. L'account di archiviazione predefinito per i quali cluster continuerà a essere Azure lo spazio di archiviazione BLOB (WASB).

-   **Per HBase cluster (Windows e Linux)**, l'archivio Lake dati può essere utilizzata come spazio di archiviazione predefinito o spazio di archiviazione aggiuntivo.

> [AZURE.NOTE] Ecco alcuni punti importanti da tenere presente.
>
> - Opzione per creare cluster HDInsight con l'accesso ai dati Lake Store è disponibile solo per le versioni HDInsight 3.2 e 3.4 (per eccesso, Hadoop e HBase cluster in Windows, nonché Linux). Per i cluster di motori su Linux, questa opzione è disponibile solo nei cluster 3.4 HDInsight.
>
> - Come detto in precedenza, archivio Lake dati è disponibile come spazio di archiviazione predefinito per alcuni tipi di grafico (HBase) e spazio di archiviazione aggiuntivo per gli altri tipi di grafico (Hadoop, motori, eccesso). Uso di archivio dati Lake come un account di spazio di archiviazione aggiuntivo non ha effetti prestazioni o la possibilità di lettura/scrittura per lo spazio di archiviazione dal cluster. In uno scenario in archivio Lake dati viene utilizzata come ulteriore spazio di archiviazione, file correlati a cluster (ad esempio, i registri e così via) vengono salvati spazio di archiviazione predefinito (BLOB Azure), mentre i dati che si desidera elaborare possono essere creati in un account di archivio di dati Lake.
>

In questo articolo è effettuare il provisioning di un cluster di Hadoop con dati Lake Store come spazio di archiviazione aggiuntivo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

-   **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

-   **Azure PowerShell 1.0 o versione successiva**. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- **Principale del servizio di azure Active Directory**. Procedura descritta in questa esercitazione le istruzioni su come creare un'entità servizio in Azure Active Directory. Tuttavia, è necessario essere un amministratore di Azure Active Directory per poter creare un'entità servizio. Se si è un amministratore di Azure Active Directory, è possibile ignorare questo prerequisito e procedere con l'esercitazione.
    
    **Se non è un amministratore di Azure Active Directory**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In questo caso, l'amministratore di Azure Active Directory innanzitutto necessario creare un'entità servizio prima di creare un cluster di HDInsight con dati Lake archivio. Inoltre, il capitale servizio deve essere creato utilizzando un certificato, come descritto in [creare un servizio dell'entità con certificato](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Creare un cluster di HDInsight con archivio Lake dati di Azure

Il modello di Manager delle risorse e i prerequisiti per l'uso del modello, sono disponibili in GitHub su [Distribuisci un cluster HDInsight Linux con nuovo punto vendita Lake di dati](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Seguire le istruzioni fornite in questo collegamento per creare un cluster di HDInsight con Azure dati Lake Store come spazio di archiviazione aggiuntivo.

Le istruzioni disponibili in collegamento indicati in precedenza richiedono PowerShell. Prima di iniziare con queste istruzioni, verificare che l'accesso al proprio account Azure. Dal desktop, aprire una nuova finestra di PowerShell Azure e immettere i frammenti seguenti. Quando viene richiesto di effettuare l'accesso, assicurarsi che l'accesso un tipo di abbonamento admininistrators/proprietario:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Caricare dati di esempio per l'archivio Lake dati di Azure

Il modello di gestione risorse crea un nuovo account di archivio di dati Lake e associa con il cluster HDInsight. È ora necessario caricare alcuni dati di esempio per l'archivio Lake dati. È necessario questi dati in un secondo momento nell'esercitazione per eseguire i processi da un cluster di HDInsight l'accesso ai dati nell'archivio Lake dati. Per istruzioni su come caricare dati, vedere [caricare un file di archivio dati Lake](data-lake-store-get-started-portal.md#uploaddata). Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Impostare ACL pertinenti i dati di esempio

Per assicurarsi che i dati di esempio che è caricare sono accessibili dal cluster HDInsight, è necessario assicurarsi che l'applicazione di Azure Active Directory che consente di stabilire identità tra il cluster HDInsight e archivio dati Lake ha accesso a file/cartella che si sta tentando di accedere. A tale scopo, eseguire i passaggi seguenti.

1.  Individuare il nome dell'applicazione Azure Active Directory associato HDInsight cluster e l'archivio di Lake dati. Un modo per cercare il nome del consiste nell'aprire e il cluster HDInsight creata utilizzando il modello di Manager delle risorse, fare clic sulla scheda **Cluster AAD identità** e cercare il valore del **Nome visualizzato principale del servizio**.

2.  A questo punto, consentire l'accesso all'applicazione di Azure Active Directory file/cartella che si desidera accedere dal cluster HDInsight. Per impostare gli ACL destro file/cartella nell'archivio dati Lake, vedere [protezione dei dati nell'archivio dati Lake](data-lake-store-secure-data.md#assign-users-or-security-group-as-acls-to-the-azure-data-lake-store-file-system).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Eseguire i processi di test su cluster HDInsight usi l'archivio Lake dati

Dopo aver configurato un cluster di HDInsight, è possibile eseguire test processi sul cluster per verificare che il cluster HDInsight possa accedere archivio Lake dati. A tale scopo, si eseguirà un processo di Hive di esempio che crea una tabella utilizzando i dati di esempio caricati in precedenza all'archivio dati Lake.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

In questa sezione verranno SSH in cluster e di esecuzione di una query di Hive di esempio. Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1.  Una volta connessa, avviare CLI Hive utilizzando il comando seguente:

    ```
    hive
    ```

2.  Usa CLI, immettere le istruzioni seguenti per creare una nuova tabella denominata **veicoli** utilizzando i dati di esempio nell'archivio Lake dati:

    ```
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Verrà visualizzato un output simile al seguente:

    ```
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

Utilizzare i cmdlet seguenti per eseguire la query Hive. In questa query verrà creato una tabella dai dati nell'archivio Lake dati e quindi eseguire una query di selezione della tabella creata.

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

Questo avrà l'output seguente. **ExitValue** pari a 0 nell'output suggerisce che il processo è stato completato correttamente.

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

Recuperare l'output dal processo utilizzando il seguente cmdlet:

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

L'output processo simile al seguente:

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>Accesso dati Lake archivio usando i comandi HDFS

Dopo aver configurato il cluster HDInsight per utilizzare dati Lake archivio, è possibile utilizzare i comandi di shell HDFS per accedere all'archivio.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

In questa sezione è verrà SSH nel cluster ed eseguire i comandi HDFS. Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una volta connessa, il comando seguente HDFS file System per visualizzare un elenco di file di archivio di Lake dati.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

1.  Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2.  Fare clic su **Sfoglia**e quindi fare clic sul cluster HDInsight creato **cluster HDInsight**.

3.  In e il cluster fare clic su **Desktop remoto**e quindi scegliere **Connetti**e il **Desktop remoto** .

    ![Remote in cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

    Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4.  Nella sessione remota avviare Windows PowerShell e utilizzare i comandi di file System HDFS per elencare i file nell'archivio Lake dati di Azure.

    ```
    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
    ```

    Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

    ```
    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
    ```

    È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

## <a name="next-steps"></a>Passaggi successivi

-   [Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake](data-lake-store-copy-data-wasb-distcp.md)
