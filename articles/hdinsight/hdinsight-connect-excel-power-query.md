<properties
    pageTitle="Connettere Excel a Hadoop con Power Query | Microsoft Azure"
    description="Informazioni su come sfruttare componenti di business intelligence e usare Power Query per Excel per accedere ai dati archiviati in Hadoop in HDInsight."
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


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Connettere Excel a Hadoop tramite Power Query

Una funzionalità chiave della soluzione di grande dati Microsoft è l'integrazione dei componenti di Microsoft business intelligence (BI) con i cluster Hadoop Azure HDInsight. Esempio di tale integrazione è la possibilità di connettersi Excel all'account di archiviazione Azure che contiene i dati associati il cluster Hadoop utilizzando Microsoft Power Query per Excel. In questo articolo illustra come configurare e usare Power Query per eseguire query sui dati associati a un cluster Hadoop con HDInsight.

> [AZURE.NOTE] Durante la procedura descritta in questo articolo è utilizzabile con cluster Linux o HDInsight basato su Windows, Windows è necessario per la workstation client.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **HDInsight un cluster**. Per configurare uno, vedere [Guida introduttiva di Azure HDInsight][hdinsight-get-started].
- **Risposte workstation** che esegue Windows 7, Windows Server 2008 R2 o un sistema operativo successivo.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autonomo o Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Installazione di Power Query

Power Query può essere utilizzato per importare dati da diverse origini in Microsoft Excel, in cui è possibile power strumenti di Business Intelligence come PowerPivot e Power View. In particolare, Power Query è possibile importare dati che sono state output o generati tramite un processo Hadoop eseguito in un cluster di HDInsight.

Download di Microsoft Power Query per Excel dall' [Area Download Microsoft] [ powerquery-download] e installarlo.

## <a name="import-hdinsight-data-into-excel"></a>Importare dati HDInsight in Excel

Il componente aggiuntivo Power Query per Excel consente di importare dati da un cluster di HDInsight in Excel, in strumenti di Business Intelligence come PowerPivot e Power Map può essere utilizzato per verificare la presenza, analizzare e presentare i dati facilmente.

**Per importare dati da un cluster di HDInsight**

1. Aprire Excel.

2. Creare una nuova cartella di lavoro vuoto.

3. Fare clic sul menu di **Power Query** , fare clic su **Da Azure**e quindi fare clic su **Da Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Nota:** Se il menu di **Power Query** non è visualizzato, passare al **File** > **Opzioni** > **Componenti aggiuntivi**e selezionare **componenti aggiuntivi COM** nella finestra **Gestisci** di riepilogo a discesa nella parte inferiore della pagina. Selezionare il pulsante **Vai …** e verificare che la casella di Power Query per Excel è stata selezionata.

    **Nota:** Power Query consente anche di importare dati da HDFS facendo clic su **Da altre origini**.

3. Per **Nome dell'Account**, immettere il nome dell'account di archiviazione Blob Azure associato il cluster e quindi fare clic su **OK**. Può trattarsi di [account di archiviazione predefinito](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) oppure un account di archiviazione collegato.  Il formato è *https://<StorageAccountName>.blob.core.windows.net/*.

4. Per **Chiave Account**, immettere la chiave per l'account di archiviazione Blob e quindi fare clic su **Salva**. (È necessario eseguire questa operazione solo la prima volta che accedere all'archivio.)

5. Nel riquadro di **selezione** sul lato sinistro dell'Editor di Query fare doppio clic sul nome del contenitore di spazio di archiviazione Blob. Per impostazione predefinita, il nome del contenitore è lo stesso nome il nome del cluster.

6. Individuare **Hivesampledata** nella colonna **nome** (il percorso della cartella è **... / hive/warehouse/hivesampletable/**) e quindi fare clic su **binario** sul lato sinistro della Hivesampledata. Hivesampledata viene fornito con tutti i cluster. Facoltativamente, è possibile usare un file.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se si desidera, è possibile rinominare i nomi delle colonne. Quando si è pronti, fare clic su **Chiudi e carica**.  I dati sono stati caricati alla cartella di lavoro:

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. Analogamente, è possibile recuperare dati da HDInsight nel Database di SQL Azure. È inoltre possibile caricare dati in HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Connettere Excel a HDInsight con il Driver ODBC Microsoft Hive][hdinsight-ODBC]
* [Caricare dati HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
