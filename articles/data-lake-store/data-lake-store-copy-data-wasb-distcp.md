<properties
   pageTitle="Copiare dati da e verso WASB archivio Lake Distcp | Microsoft Azure"
   description="Utilizzare lo strumento Distcp per copiare i dati da e verso lo spazio di archiviazione BLOB all'archivio dati Lake"
   services="data-lake-store"
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Consente di copiare i dati tra Azure lo spazio di archiviazione BLOB e archivio dati Lake Distcp

> [AZURE.SELECTOR]
- [Utilizzo DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilizzo AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Dopo aver creato un cluster di HDInsight che ha accesso a un account di archivio Lake dati, è possibile utilizzare strumenti ecosistema Hadoop come Distcp per copiare i dati **da e verso** un archivio di cluster HDInsight (WASB) in un account di archivio di dati Lake. In questo articolo fornisce istruzioni su come eseguire questa operazione.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Attivare l'abbonamento Azure** per anteprima pubblica archivio Lake dati. Vedere [le istruzioni](data-lake-store-get-started-portal.md#signup).
- **Cluster di azure HDInsight** con l'accesso a un account di archivio di dati Lake. Vedere [creare un cluster di HDInsight con dati Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Verificare che si attiva Desktop remoto per il cluster.

## <a name="do-you-learn-fast-with-videos"></a>Trovare rapidamente con video?

[Guardare questo video](https://mix.office.com/watch/1liuojvdx6sie) su come copiare i dati tra lo spazio di archiviazione BLOB e archivio Lake dati utilizzando DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Utilizzare Distcp dal Desktop remoto (cluster di Windows) o SSH (Linux cluster)

Un cluster di HDInsight viene fornito con l'utilità Distcp, che può essere utilizzato per copiare i dati provenienti da origini diverse in un cluster di HDInsight. Se è stato configurato il cluster HDInsight per utilizzare dati Lake archivio come un ulteriore spazio di archiviazione, l'utilità di Distcp può essere usati-the-pronte per copiare i dati da e verso un account di archivio di dati Lake anche. In questa sezione si osserva come usare l'utilità Distcp.

1. Se si dispone di un cluster di Windows, remote in un cluster di HDInsight che ha accesso a un account di archivio di dati Lake. Per ulteriori informazioni, vedere [connettersi a cluster mediante RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Cluster Desktop, aprire la riga di comando Hadoop.

    Se si dispone di un cluster Linux, usare SSH per connettersi al cluster. Vedere [connettersi a un cluster basati su Linux HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Eseguire i comandi dal prompt dei comandi SSH.

3. Verificare se è possibile accedere a di Azure lo spazio di archiviazione BLOB (WASB). Eseguire il comando seguente:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Questa operazione fornisce un elenco del contenuto nel blob lo spazio di archiviazione.

4. Analogamente, verificare se è possibile accedere all'account di archivio dati Lake dal cluster. Eseguire il comando seguente:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Questa operazione fornisce un elenco di file e cartelle nella finestra account archivio Lake dati.

5. Consente di copiare i dati da WASB a un account di archivio di dati Lake Distcp.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Contenuto della **cartella/esempio** dati/gutenberg/verrà copiato con la WASB a **/myfolder** nell'account archivio Lake dati.

6. Analogamente, utilizzare Distcp per copiare i dati da account archivio Lake dati WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Contenuto di **/myfolder** verrà copiato con l'account di archivio di dati Lake in **** esempio/dati/gutenberg/cartella WASB.

## <a name="see-also"></a>Vedere anche

- [Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
