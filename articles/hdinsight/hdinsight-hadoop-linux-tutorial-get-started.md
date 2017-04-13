<properties
    pageTitle="Linux esercitazione: Introduzione a Hadoop e Hive | Microsoft Azure"
    description="Seguire questa esercitazione Linux per iniziare a utilizzare Hadoop in HDInsight. Informazioni su come effettuare il provisioning di cluster Linux ed eseguire query di dati con Hive."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="hadoop-tutorial-get-started-using-linux-based-hadoop-in-hdinsight"></a>Esercitazione Hadoop: iniziare a utilizzare basati su Linux Hadoop in HDInsight

> [AZURE.SELECTOR]
- [Basati su Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Basato su Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

Informazioni su come creare cluster basati su Linux [Hadoop](http://hadoop.apache.org/) in HDInsight e su come eseguire Hive processi in HDInsight. [Apache Hive](https://hive.apache.org/) è il componente più popolari ecosistema Hadoop. Attualmente HDInsight viene fornito con 4 tipi di grafico diverso: [Hadoop](hdinsight-hadoop-introduction.md), [vivacità](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) ed [eccesso](hdinsight-storm-overview.md).  Ogni tipo di cluster supporta un set di componenti diverso. Tutti i tipi di 4 cluster supportano Hive. Per un elenco dei componenti supportati in HDInsight, vedere [quali sono le novità in versioni cluster Hadoop fornite da HDInsight?](hdinsight-component-versioning.md)  

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario avere:

- **Abbonamento Azure**: per creare un account di valutazione di un mese gratuito, passare alla [azure.microsoft.com/free](https://azure.microsoft.com/free).

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-cluster"></a>Creare cluster

La maggior parte dei processi Hadoop sono processi batch. Creare un cluster, eseguire alcune processi e quindi eliminare il cluster. In questa sezione, è necessario creare un cluster Hadoop basati su Linux nel modello di [Gestione risorse di Azure](../resource-group-template-deploy.md)HDInsight. Modello di gestione risorse è completamente personalizzabile; semplice creare risorse Azure come HDInsight. Esperienza di gestione risorse modello non è necessario per seguendo l'esercitazione. Per altri metodi di creazione cluster e informazioni sulle proprietà utilizzate in questa esercitazione, vedere [creare HDInsight cluster](hdinsight-hadoop-provision-linux-clusters.md). Il modello di Manager delle risorse utilizzato in questa esercitazione si trova in un contenitore di blob pubblica, [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Fare clic sull'immagine seguente per accedere a Azure e aprire il modello di gestione risorse nel portale di Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da e il **parametri** immettere quanto segue:

    ![HDInsight Linux get avviato Gestione risorse modello nel portale](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è **amministratore**.
    - **SSH nome utente e password**: il nome utente predefinito è **sshuser**.  È possibile rinominarla. 
    
    Gli altri parametri sono facoltativi per seguendo l'esercitazione. È possibile lasciare loro così come sono. 
    
    Ogni cluster dispone di una relazione di account di archiviazione Blob Azure. Genere è definito l'account di archiviazione predefinito. HDInsight cluster e il relativo account predefinito di spazio di archiviazione necessario situati nella stessa regione Azure. Eliminazione di cluster, l'account di archiviazione non verrà eliminato. Nel modello, il nome di account di archiviazione predefinito è definito come nome del cluster con "store" aggiunto. 
    
3. Fare clic su **OK** per salvare i parametri.
4. Immettere **nome del nuovo gruppo risorse** per creare un nuovo gruppo di risorse e il **distribuzione personalizzata** .  Gruppo di risorse è un contenitore che raggruppa cluster, l'account di archiviazione dipendenti e altri elementi. La posizione di gruppo di risorse può essere diversa da quella cluster.
5. Fare clic su **note legali**e quindi fare clic su **Crea**.
6. Verificare che la casella di controllo **Aggiungi a dashboard** sia selezionata e quindi fare clic su **Crea**. Verrà visualizzato un nuovo riquadro intitolato **la distribuzione dei modelli di distribuzione**. Richiede circa circa il 20 minuti per creare un cluster. 
7.  Dopo aver creato il grafico, la didascalia del riquadro viene modificata il nome del gruppo risorse specificato. E il portale due pale viene aperto automaticamente con i cluster e le impostazioni di raggruppamento. 

    ![HDInsight Linux get avviato impostazioni cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    Sono disponibili due risorse elencate, il cluster e l'account di archiviazione predefinito.

##<a name="run-hive-queries"></a>Eseguire query di Hive

[Apache Hive](hdinsight-use-hive.md) è il componente più popolari utilizzato in HDInsight. Esistono diversi modi per eseguire i processi di Hive in HDInsight. In questa esercitazione si utilizzerà la visualizzazione di Ambari Hive dal portale di eseguire alcuni processi Hive. Per altri metodi per l'invio di processi Hive, vedere [Usare Hive in HDInsight](hdinsight-use-hive.md).

1. Passare alla **https://&lt;nome cluster >. azurehdinsight.net**, dove &lt;nome cluster > è il cluster creata nella sezione precedente per aprire Ambari.
2. Immettere il nome utente Hadoop e la password specificata nella sezione precedente. Il nome utente predefinito è **admin**.
3. Visualizzare **Hive** come illustrato nella schermata seguente:

    ![Se si selezionano Ambari visualizzazioni](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Incollare le seguenti istruzioni HiveQL nella sezione __Dell'Editor di Query__ della pagina del foglio di lavoro:

        SHOW TABLES;

    >[AZURE.NOTE] Punto e virgola è necessario Hive.       
        
5. Fare clic su __Esegui__. Una sezione __Query processo risultati__ dovrà essere visualizzato sotto l'Editor di Query e visualizzare informazioni sul processo. 

    Al termine di query, la sezione __Query processo risultati__ visualizzerà i risultati dell'operazione. Risulta una tabella denominata **hivesampletable**. Questa tabella Hive di esempio viene fornito con tutti i cluster HDInsight.

    ![Visualizzazioni HDInsight Hive](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Ripetere i passaggi 4 e 5 per eseguire la query seguente:

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Nota a discesa __salvare i risultati__ nell'angolo superiore sinistro della sezione dei __Risultati della Query processo__ ; è possibile utilizzare per scaricare i risultati oppure salvarli in HDInsight lo spazio di archiviazione in un file CSV.

7. Fare clic su **cronologia** per ottenere un elenco dei processi.

Dopo avere completato un processo Hive, è possibile [esportare i risultati al database SQL Azure o database di SQL Server](hdinsight-use-sqoop-mac-linux.md), è anche possibile [visualizzare i risultati tramite Excel](hdinsight-connect-excel-power-query.md). Per ulteriori informazioni sull'utilizzo Hive in HDInsight, vedere [usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file di esempio Apache log4j](hdinsight-use-hive.md).

##<a name="clean-up-the-tutorial"></a>Pulire l'esercitazione

Dopo aver completato l'esercitazione, può essere necessario eliminare il cluster. Con HDInsight, i dati vengono archiviati in archiviazione di Azure, in modo che è possibile eliminare un cluster quando non è in uso. Anche addebitate per un cluster di HDInsight, anche quando non è in uso. Poiché le spese per il cluster sono tutte le volte più le spese per lo spazio di archiviazione, è opportuno economico eliminare cluster quando non sono in uso. 

>[AZURE.NOTE] Utilizzo di [Azure dati](hdinsight-hadoop-create-linux-clusters-adf.md), è possibile creare cluster HDInsight su richiesta e configurare un'impostazione TimeToLive per eliminare i cluster automaticamente. 

**Per eliminare il cluster e/o l'account di archiviazione predefinito**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal dashboard del portale, fare clic sul riquadro con il nome del gruppo di risorse utilizzato per creare il grafico.
3. Scegliere **Elimina** dal e delle risorse per eliminare il gruppo di risorse che contiene il cluster e l'account di archiviazione predefinito; oppure fare clic sul nome cluster sul riquadro **risorse** e quindi scegliere **Elimina** dal e cluster. Nota eliminando il gruppo di risorse verrà eliminare l'account di archiviazione. Se si desidera mantenere l'account di archiviazione, scegliere di eliminare solo il cluster.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state come creare un cluster basati su Linux HDInsight utilizzando un modello di Manager delle risorse e come eseguire query di Hive di base.

Per ulteriori informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

- Per ulteriori informazioni sull'utilizzo di Hive con HDInsight, ad esempio eseguire query Hive da Visual Studio, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

- Per informazioni su maialino, un linguaggio utilizzato per trasformare i dati, vedere [Usare maialino con HDInsight][hdinsight-use-pig].

- Per informazioni su MapReduce, un modo per scrivere programmi che elaborano dati Hadoop, vedere [Usare MapReduce con HDInsight][hdinsight-use-mapreduce].

- Per informazioni sull'utilizzo di strumenti HDInsight per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'utilizzo di strumenti di Visual Studio Hadoop per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se si è pronti per iniziare a lavorare con i propri dati e ulteriori informazioni su come HDInsight archivia i dati o su come recuperare i dati in HDInsight, vedere gli articoli seguenti:

- Per informazioni sull'utilizzo di archiviazione blob Azure HDInsight, vedere [lo spazio di archiviazione Blob Azure usare con HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Per informazioni su come caricare dati HDInsight, vedere [caricare dati da HDInsight][hdinsight-upload-data].

Se si desidera acquisire familiarità con la creazione o la gestione di un cluster di HDInsight, vedere gli articoli seguenti:

- Per informazioni sulla gestione del cluster basati su Linux HDInsight, vedere [gestire HDInsight cluster utilizzando Ambari](hdinsight-hadoop-manage-ambari.md).

- Per ulteriori informazioni sulle opzioni che è possibile selezionare quando si crea un cluster di HDInsight, vedere [Creazione di HDInsight su Linux usando le opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md).

- Se si ha familiarità con Linux e Hadoop ma fonti di informazioni specifiche sulle Hadoop sul HDInsight, vedere [uso di HDInsight su Linux](hdinsight-hadoop-linux-information.md). Informazioni quali:

    * URL per i servizi ospitati in cluster, ad esempio Ambari e WebHCat
    * Il percorso del file Hadoop ed esempi nel file system locale
    * Archiviare l'utilizzo di Azure lo spazio di archiviazione (WASB) anziché HDFS come i dati predefiniti


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
