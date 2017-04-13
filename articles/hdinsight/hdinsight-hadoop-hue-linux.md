<properties
    pageTitle="Usare tonalità con Hadoop sul cluster HDInsight Linux | Microsoft Azure"
    description="Informazioni su come installare e usare tonalità con i cluster Hadoop su HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installare e usare tonalità nello HDInsight Hadoop cluster

Informazioni su come installare tonalità nei cluster HDInsight Linux e utilizzare tunneling per indirizzare le richieste in tonalità.

## <a name="what-is-hue"></a>Che cos'è tonalità?

Tonalità è un insieme di applicazioni Web utilizzato per interagire con un cluster di Hadoop. È possibile utilizzare tonalità per individuare lo spazio di archiviazione associato a un cluster di Hadoop (WASB, nel caso dei cluster HDInsight), eseguire i processi Hive e gli script maialino e così via. I componenti seguenti sono disponibili con le installazioni di tonalità in un cluster di HDInsight Hadoop.

* La cera Hive Editor
* Maialino
* Gestione Metastore
* Oozie
* FileBrowser (che parla a contenitore predefinito WASB)
* Processo Browser

> [AZURE.WARNING] Componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installare tonalità utilizzo di azioni di Script

La seguente azione script può essere utilizzata per installare tonalità in un cluster basati su Linux HDInsight.
https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.sh
    
In questa sezione vengono fornite istruzioni su come utilizzare lo script durante il provisioning di cluster tramite il portale di Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, HDInsight .NET SDK o Gestione risorse Azure modelli utilizzabili per applicare le azioni script. È inoltre possibile applicare le azioni script per già in esecuzione cluster. Per ulteriori informazioni, vedere [personalizzare HDInsight cluster con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster utilizzando la procedura descritta in [cluster di provisioning HDInsight su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), ma non completate il provisioning.

    > [AZURE.NOTE] Per installare tonalità in cluster HDInsight, la dimensione headnode consigliato è almeno A4 (8 core; 14 GB di memoria).

2. Nella e **Facoltativi** , selezionare **Le azioni Script**e fornire le informazioni come illustrato di seguito:

    ![Parametri di azione script specifica per tonalità] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Parametri di azione script specifica per tonalità")

    * __Nome__: immettere un nome descrittivo per l'azione script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Testa__: selezionare questa opzione
    * __Lavoro__: lasciare vuoto il campo.
    * __ZOOKEEPER__: lasciare vuoto il campo.
    * __Parametri__: lasciare vuoto il campo.

3. Nella parte inferiore delle **Azioni di Script**, utilizzare il pulsante **selezione** per salvare la configurazione. Infine, utilizzare il pulsante **Selezionare** nella parte inferiore della stessa e **Facoltativi** per salvare le informazioni di configurazione facoltativo.

4. Continuare il provisioning del cluster come descritto in [cluster di provisioning HDInsight su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Utilizzare tonalità con i cluster HDInsight

Tunneling SSH è l'unico modo per accedere a tonalità nel cluster dopo l'esecuzione. Tunneling tramite SSH consente il traffico passare direttamente alla headnode del cluster in tonalità è in esecuzione. Al termine di provisioning cluster, utilizzare la procedura seguente per utilizzare tonalità in un cluster HDInsight Linux.

1. Utilizzare le informazioni per creare un tunnel SSH dal sistema client al cluster HDInsight [Utilizzare SSH connettersi accedere Ambari web dell'interfaccia utente, ResourceManager, JobHistory, NameNode, Oozie e altre web dell'interfaccia utente](hdinsight-linux-ambari-ssh-tunnel.md) e quindi configurare il Web browser per utilizzare il tunnel SSH come proxy.

2. Dopo aver creato un tunnel SSH e configurato il browser per il traffico proxy, è necessario trovare il nome host del nodo principale principale. È possibile eseguire questa operazione mediante la connessione al cluster utilizzando SSH su porta 22. Ad esempio `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` dove __nomeutente__ è il proprio nome utente SSH e __nome cluster__ è il nome del cluster.

    Per ulteriori informazioni sull'uso di SSH, vedere i documenti seguenti:

    * [Usare SSH con basati su Linux HDInsight da un client Linux, Unix o Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con basati su Linux HDInsight da un client di Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Una volta connessa, utilizzare il comando seguente per ottenere il nome di dominio completo del headnode primaria:

        hostname -f

    Verrà restituito un nome simile al seguente:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Questo è il nome host del headnode principale in cui si trova il sito Web tonalità.

2. Utilizzare il browser per aprire il portale di tonalità all'http://HOSTNAME:8888. Sostituire HOSTNAME con il nome ottenuto nel passaggio precedente.

    > [AZURE.NOTE] Quando si accede per la prima volta, verrà richiesto di creare un account per accedere al portale di tonalità. Le credenziali specificate verranno limitate per il portale e non riguardano le credenziali di amministratore o SSH utente specificato durante il provisioning del cluster.

    ![Accesso al portale di tonalità] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Specificare le credenziali per il portale di tonalità")

### <a name="run-a-hive-query"></a>Eseguire una query di Hive

1. Dal portale di tonalità, fare clic su **Editor di Query**e quindi fare clic su **Hive** per aprire l'editor Hive.

    ![Utilizzare Hive] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Utilizzare Hive")

2. Nella scheda **assistere** in **Database**, verrà visualizzato **hivesampletable**. Si tratta di una tabella di esempio forniti con tutti i cluster Hadoop HDInsight. Immettere una query di esempio nel riquadro destro e controllare l'output nella scheda **risultati** nel riquadro sotto, come illustrato nella schermata.

    ![Eseguire Hive query] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Eseguire Hive query")

    È anche possibile usare la scheda **grafico** per visualizzare una rappresentazione visiva del risultato.

### <a name="browse-the-cluster-storage"></a>Individuare l'archivio

1. Dal portale di tonalità, fare clic su **Browser File** nell'angolo superiore destro della barra dei menu.

2. Per impostazione predefinita nella directory **/user/myuser** si apre il Visualizzatore di file. Fare clic su barra destra prima directory dell'utente nel percorso per passare alla radice del contenitore di archiviazione Azure associato al cluster.

    ![Browser di file di utilizzo] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Browser di file di utilizzo")

3. Pulsante destro del mouse su un file o cartelle per visualizzare le operazioni disponibili. Utilizzare il pulsante **Carica** nell'angolo destro per caricare i file nella directory corrente. Utilizzare il pulsante **Nuovo** per creare nuovi file o directory.

> [AZURE.NOTE] Il browser file tonalità possa solo visualizzare il contenuto del contenitore predefinito associato al cluster HDInsight. Qualsiasi account/contenitori di spazio di archiviazione aggiuntivo che potrebbe avere associato al cluster non saranno accessibili tramite il browser file. Tuttavia, altri contenitori associati al cluster sia sempre accessibili per i processi di Hive. Ad esempio, se si immette il comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` nell'editor di Hive, è possibile visualizzare il contenuto della contenitori aggiuntivi e. In questo comando **newcontainer** non è il contenitore predefinito associato a un cluster.

## <a name="important-considerations"></a>Considerazioni importanti

1. Lo script utilizzato per installare tonalità installarla solo in headnode principale del cluster.

2. Durante l'installazione, riavvio più dei servizi Hadoop (HDFS, filati, MR2, Oozie) per aggiornare la configurazione. Al termine lo script l'installazione di tonalità, può richiedere del tempo per altri servizi di Hadoop per l'avvio. Le prestazioni della tonalità può influire inizialmente. Una volta tutti i servizi avviati, tonalità funzioneranno completamente.

3.  Tonalità non riconosciute Tez processi, ovvero il valore predefinito per Hive. Se si desidera utilizzare MapReduce come il motore di esecuzione Hive, aggiornare lo script per usare il comando seguente in uno script:

        set hive.execution.engine=mr;

4.  Con i cluster Linux, è possibile impostare uno scenario in cui il provider di servizi in esecuzione nel headnode primaria durante la gestione risorse potrebbe essere in esecuzione nel database secondario. Questo scenario potrebbe causare errori (come illustrati di seguito) quando si usa tonalità per visualizzare i dettagli dei processi in esecuzione nel cluster. Tuttavia, è possibile visualizzare i dettagli di processo completato il processo.

    ![Errore portale tonalità] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Errore portale tonalità")

    Si tratta di un problema noto. In alternativa, modificare Ambari in modo che la gestione di risorse active anche essere eseguito in headnode principale.

5.  Tonalità riconosce WebHDFS mentre HDInsight cluster utilizzare lo spazio di archiviazione di Azure tramite `wasbs://`. Pertanto, lo script personalizzato utilizzato con script azione Installa WebWasb, è un servizio compatibile con WebHDFS per comunicare con WASB. Pertanto, anche se il portale di tonalità dice HDFS in cifre (ad esempio quando si sposta il puntatore del mouse sul **File Browser**) devono essere interpretato come WASB.


## <a name="next-steps"></a>Passaggi successivi

- [Installare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Personalizzazione di cluster utilizzata per installare Giraph nei cluster HDInsight Hadoop. Giraph consente di eseguire l'elaborazione di grafico utilizzando Hadoop e può essere utilizzato con Azure HDInsight.

- [Installare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Personalizzazione di cluster utilizzata per installare Solr nei cluster HDInsight Hadoop. Solr consente di eseguire le operazioni di ricerca potenti sui dati archiviati.

- [Installare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md). Personalizzazione di cluster utilizzata per installare R nei cluster HDInsight Hadoop. R è un ambiente per il calcolo statistiche e lingua Apri origine. Fornisce centinaia di funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
