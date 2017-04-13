<properties
   pageTitle="Hadoop esercitazione: Introduzione a Hadoop in Windows | Microsoft Azure"
   description="Guida introduttiva a Hadoop in HDInsight. Informazioni su come creare cluster Hadoop in Windows, eseguire una query di Hive sui dati e analizzare l'output in Excel."
   keywords="esercitazione Hadoop, hadoop in windows, cluster hadoop, informazioni su hadoop, query hive"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight-on-windows"></a>Esercitazione Hadoop: iniziare a utilizzare Hadoop in HDInsight in Windows

> [AZURE.SELECTOR]
- [Basati su Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)
- [Basato su Windows](../hdinsight-hadoop-tutorial-get-started-windows.md)

Per informazioni su Hadoop in Windows e iniziare a utilizzare HDInsight, questa esercitazione viene illustrato come eseguire una query Hive su dati non strutturati in un cluster di Hadoop e quindi analizzare i risultati in Microsoft Excel.

>[AZURE.NOTE] Le informazioni contenute in questo documento sono specifiche di cluster HDInsight basato su Windows. Per informazioni sui cluster basati su Linux, vedere [Hadoop esercitazione: iniziare a utilizzare basati su Linux Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

Si supponga che si dispone di un set di dati non strutturato grande e si desidera eseguire una query Hive su di esso per estrarre alcune informazioni rilevanti. È esattamente si prevede di eseguire in questa esercitazione. Ecco come ottenere questo:

   !["Hadoop esercitazione: creare un account; creare un cluster di Hadoop; invio di una query di Hive; analizzare i dati in Excel.][image-hdi-getstarted-flow]

Guardare un video dimostrativo di questa esercitazione per informazioni su Hadoop in HDInsight:

![Video di un'esercitazione Hadoop prima: invio di una query di Hive in un cluster di Hadoop e analizzare i risultati in Excel.][img-hdi-getstarted-video]

**[Controllare l'esercitazione Hadoop HDInsight su YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

In combinazione con la disponibilità generale di Azure HDInsight, Microsoft offre anche emulatore HDInsight di Azure, precedentemente noto come *Microsoft HDInsight sviluppo Preview*. L'emulatore è destinato a scenari di sviluppo e supporta solo distribuzioni nodo singolo. Per informazioni sull'utilizzo di emulatore HDInsight, vedere [Guida introduttiva a emulatore HDInsight][hdinsight-emulator].

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione per Hadoop in Windows, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Risposte workstation computer** con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 autonomo o Office 2010 Professional Plus.

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-hadoop-clusters"></a>Creare Hadoop cluster

Quando si crea un cluster, si creano risorse di elaborazione Azure contenenti Hadoop e applicazioni correlate. In questa sezione, si crea un cluster di versione 3.2 HDInsight. È anche possibile creare cluster Hadoop per le altre versioni. Per ulteriori informazioni, vedere [creare HDInsight cluster usando le opzioni personalizzate][hdinsight-provision]. Per informazioni sulle versioni HDInsight e i contratti di servizio, vedere [il controllo delle versioni componente HDInsight](hdinsight-component-versioning.md).


**Per creare un cluster di Hadoop**

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Nuovo**, fare clic su **Dati Analitica**e quindi fare clic su **HDInsight**. Il portale verrà visualizzata una pala **Nuovo HDInsight Cluster** .

    ![Crea un nuovo cluster nel portale di Azure] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "Crea un nuovo cluster nel portale di Azure")

3. Immettere o selezionare le operazioni seguenti:

    ![Tipo e immettere il nome cluster] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "Tipo e immettere il nome cluster")
    
  	|Nome campo| Valore|
  	|----------|------|
  	|Nome cluster| Un nome univoco per identificare il cluster|
  	|Tipo di grafico| Selezionare **Hadoop** per questa esercitazione. |
  	|Sistema operativo del cluster| Selezionare **Data Center di Windows Server 2012 R2** per questa esercitazione.|
  	|Versione HDInsight| Selezionare la versione più recente per questa esercitazione.|
  	|Abbonamento| Selezionare l'abbonamento Azure che verrà utilizzato per il cluster.|
  	|Gruppo di risorse | Selezionare un gruppo di risorse Azure esistente o creare un nuovo gruppo di risorse. Un cluster HDInsight base contiene un cluster e il relativo account di archiviazione predefinito.  È possibile raggruppare i due in un gruppo di risorse per la gestione del semplice.|
  	|Credenziali| Immettere il nome utente di accesso cluster e la password. Basato su Windows cluster può avere 2 agli account utente.  L'utente cluster (o utente HTTP) viene utilizzato per gestire il cluster e inviare i processi.  Facoltativamente, è possibile creare un desktop remoto (RDP) account utente in remoto connettersi al cluster. Se si sceglie di attivare desktop remoto, è necessario creare l'account utente RDP.|
  	|Origine dati| Fare clic su Crea nuovo per creare un nuovo account Azure dello spazio di archiviazione predefinito. Usare il nome del cluster come nome del contenitore predefinito. Ogni cluster HDinsight dispone di un contenitore di Blob predefinito in un account di archiviazione Azure.  La posizione dell'account Azure dello spazio di archiviazione predefinito determina la posizione del cluster HDInsight.|
  	|Nodo prezzi livelli| Utilizzare 1 o 2 nodi di lavoro con il lavoro nodo e testa Nota predefinita prezzi livello per questa esercitazione.|
  	|Configurazione facoltativa| Ignorare questa parte.|

9. In e il **Nuovo HDInsight Cluster** , assicurarsi che **Pin per Startboard** sia selezionata e quindi fare clic su **Crea**. Questa operazione verrà creare il cluster e aggiungere un riquadro per renderla alla Startboard del portale Azure. L'icona indica che il cluster consiste nel creare e verrà modificato per visualizzare l'icona HDInsight dopo il completamento della creazione.

  	| Durante la creazione | Creazione completata |
  	| ------------------ | --------------------- |
  	| ![Creazione di indicatore su startboard](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) | ![Creato riquadro cluster](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |

    > [AZURE.NOTE] Richiederà molto tempo per il cluster da creare, in genere circa 15 minuti. Utilizzare il riquadro la Startboard o la voce di **notifiche** sul lato sinistro della pagina per verificare se il processo di creazione.

10. Una volta completata la creazione, fare clic sul riquadro per il cluster da Startboard per avviare e il raggruppamento.


## <a name="run-a-hive-query-from-the-portal"></a>Eseguire una query di Hive dal portale
Una volta creato un cluster di HDInsight, il passaggio successivo è per eseguire un processo Hive per un esempio di tabella Hive della query. Verrà usata *hivesampletable*, che viene fornito con i cluster HDInsight. La tabella contiene i dati relativi produttori di dispositivi mobili, piattaforme e modelli. Una query di Hive in questa tabella recupera i dati per i dispositivi mobili per un produttore specifico.

> [AZURE.NOTE] HDInsight Tools per Visual Studio viene fornito con Azure SDK per .NET versione 2.5 o versione successiva. Utilizzando gli strumenti di Visual Studio, è possibile connettersi a cluster HDInsight, creare tabelle Hive ed eseguire query Hive. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di HDInsight Hadoop Tools per Visual Studio][1].

**Per eseguire un processo Hive nel dashboard di cluster**

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Esplora tutto** e quindi fare clic su **Cluster HDInsight** per visualizzare un elenco di cluster, inclusi il cluster che appena creato nella sezione precedente.
3. Fare clic sul nome del cluster che si desidera utilizzare per eseguire il processo di Hive e quindi fare clic su **Dashboard** nella parte superiore e il.
4. Verrà aperta una pagina Web in una scheda del browser diversi. Immettere l'account utente Hadoop e la password. Il nome utente predefinito è **amministratore**; la password è è stata immessa durante la creazione del cluster.
5. Dal dashboard, fare clic sulla scheda **Editor Hive** . Verrà visualizzata la pagina web.

    ![Scheda Editor hive nel dashboard cluster HDInsight.][img-hdi-dashboard]

    Sono disponibili più schede nella parte superiore della pagina. La scheda predefinita è **Hive Editor**e le altre schede sono **Cronologia dei processi** e **Browser File**. Il dashboard, è possibile inviare query Hive, controllare i registri di processo Hadoop e sfogliare i file di spazio di archiviazione.

    > [AZURE.NOTE] Si noti che l'URL della pagina Web * &lt;nome cluster&gt;. azurehdinsight.net*. Invece di apertura del dashboard dal portale, in modo è possibile aprire il dashboard da un web browser tramite l'URL.

6. Nella scheda **Editor Hive** per **Nome di Query**, immettere **HTC20**.  Il nome della query è la posizione. Nel riquadro query, immettere la query Hive come illustrato nell'immagine:

    ![Hive query immessa nel riquadro di query dell'Editor di Hive.][img-hdi-dashboard-query-select]

4. Fare clic su **Invia**. È necessario eseguire alcuni istanti per ottenere i risultati. La schermata viene aggiornato ogni 30 secondi. È anche possibile fare clic su **Aggiorna** per aggiornare la schermata.

    ![Risultati da una query di Hive in elencati nella parte inferiore del dashboard cluster.][img-hdi-dashboard-query-select-result]

5. Dopo che viene visualizzato lo stato di processo, fare clic sul nome di query sullo schermo per visualizzare l'output. Prendere nota del **Processo avviare orario UTC**. Sarà necessario in un secondo momento.

    ![Processo ora di inizio elencata nella scheda Cronologia dei processi del dashboard cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    Nella pagina sono indicati anche l' **Output processo** e il **Log di processi**. È inoltre la possibilità di scaricare il file di output (\_stdout) e il file di log \(_stderr).


**Per individuare il file di output**

1. Scegliere **File Visualizzatore**dashboard cluster.
2. Scegliere il nome dell'account di archiviazione, fare clic sul nome di contenitore (che corrisponde al nome del cluster) e quindi fare clic su **utente**.
3. Fare clic su **amministratore** e quindi fare clic su GUID contenente l'ora dell'ultima modifica (leggermente dopo il processo di avvio indicato in precedenza). Copiare tale GUID. Sarà necessario nella sezione successiva.


    ![File che GUID elencato nella scheda del Browser di File di output della query Hive.][img-hdi-dashboard-query-browse-output]


##<a name="connect-to-microsoft-business-intelligence-tools-for-excel"></a>Connettersi a strumenti di Microsoft business intelligence per Excel

È possibile utilizzare il componente aggiuntivo Power Query per Microsoft Excel per importare output processo da HDInsight in Excel, in strumenti di Microsoft business intelligence possono essere utilizzati per analizzare ulteriormente i risultati.

È necessario disporre di Excel 2013 o 2010 per completare questa parte dell'esercitazione.

**Scaricare Microsoft Power Query per Excel**

- Download di Microsoft Power Query per Microsoft Excel dall' [Area Download Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) e installarlo.

**Per importare i dati HDInsight**

1. Aprire Excel e creare una nuova cartella.
3. Fare clic sul menu di **Power Query** , fare clic su **Da altre origini**e quindi fare clic su **Da Azure HDInsight**.

    ![Menu di Excel PowerQuery importazione aperto per Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Immettere il **Nome dell'Account** dell'account di archiviazione Blob Azure associato il cluster e quindi fare clic su **OK**. (Si tratta di account di archiviazione che è stato creato in precedenza nell'esercitazione).
4. Immettere la **Chiave Account** per l'account di archiviazione Blob Azure e quindi fare clic su **Salva**.
5. Nel riquadro destro fare doppio clic sul nome blob. Per impostazione predefinita il nome blob è lo stesso nome cluster.

6. Individuare **stdout** nella colonna **nome** . Verificare che il GUID nella colonna **Percorso della cartella** corrispondente corrisponda GUID copiato in precedenza. Una corrispondenza suggerisce che i dati di output corrispondano al processo che è inviato. Fare clic su **binario** nella colonna sinistra della **stdout**.

    ![Trovare l'output dei dati da GUID nell'elenco del contenuto.][image-hdi-gettingstarted-powerquery-importdata2]

9. Fare clic su **Chiudi e carica** nell'angolo superiore sinistro per importare il processo di Hive output in Excel.

##<a name="run-samples"></a>Eseguire gli esempi

HDInsight cluster fornisce una console di query che include una raccolta Guida introduttiva per eseguire esempi direttamente dal portale. Per informazioni su come lavorare con HDInsight esaminando alcuni scenari di base, è possibile usare gli esempi. In questi esempi vengono forniti con tutti i componenti necessari, ad esempio i dati da analizzare e query da eseguire sui dati. Per ulteriori informazioni sugli esempi in Raccolta Guida introduttiva, vedere [Informazioni su Hadoop in HDInsight usando la raccolta di HDInsight Guida introduttiva](hdinsight-learn-hadoop-use-sample-gallery.md).

**Per eseguire l'esempio**

1. Da startboard portale Azure fare clic sul riquadro per il cluster che appena creato.
 
2. Nella nuova pala cluster, fare clic su **Dashboard**. Quando richiesto, immettere il nome utente di amministratore e la password per il cluster.

    ![Dashboard di cluster di avvio] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "Dashboard di cluster di avvio")
 
3. Dalla pagina Web che si apre, fare clic sulla scheda **Guida introduttiva raccolta** e quindi nella categoria **soluzioni con dati di esempio** fare clic su nell'esempio che si desidera eseguire. Seguire le istruzioni nella pagina Web per completare l'esempio. Nella tabella seguente sono elencati alcuni esempi e vengono fornite ulteriori informazioni sulla quale ogni campione.

Esempio | Cosa?
------ | ---------------
[Analisi dei dati sensore][hdinsight-sensor-data-sample] | Informazioni su come utilizzare HDInsight per elaborare i dati cronologici prodotto da riscaldamento, ventilazione e sistemi di aria condizionata (HVAC) per identificare i sistemi che non sono in grado di gestire in modo affidabile una temperatura del set.
[Analisi dei registri di sito Web][hdinsight-weblogs-sample] | Informazioni su come usare HDInsight per analizzare i file di log di sito Web per ottenere informazioni dettagliate sulla frequenza delle visite al sito Web in un giorno da siti esterni e un riepilogo degli errori di sito Web che gli utenti.
[Analisi delle tendenze Twitter](hdinsight-analyze-twitter-data.md) | Informazioni su come usare HDInsight per analizzare le tendenze in Twitter.

##<a name="delete-the-cluster"></a>Eliminare il cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Passaggi successivi
In questa esercitazione Hadoop è stato illustrato come creare un cluster di Hadoop in Windows in HDInsight, eseguire una query Hive sui dati e importare i risultati in Excel, in cui può essere ulteriormente elaborati e visualizzato graficamente con strumenti di business intelligence. Per ulteriori informazioni, vedere le esercitazioni seguenti:

- [Per iniziare a usare HDInsight Hadoop Tools per Visual Studio][1]
- [Guida introduttiva a emulatore HDInsight][hdinsight-emulator]
- [Utilizzo dell'archiviazione Blob Azure HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]
- [Caricare dati HDInsight][hdinsight-upload-data]
- [Utilizzare MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Utilizzare maialino con HDInsight][hdinsight-use-pig]
- [Utilizzare Oozie con HDInsight][hdinsight-use-oozie]
- [Sviluppare programmi Java MapReduce per HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#step-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 
