<properties
    pageTitle="Creare cluster Hadoop, HBase, eccesso o motori su Linux in HDInsight tramite il portale | Microsoft Azure"
    description="Informazioni su come creare cluster Hadoop, HBase, eccesso o motori su Linux per tramite un web browser e il portale di anteprima Azure HDInsight."
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
    ms.date="10/05/2016"
    ms.author="nitinme"/>


#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster basati su Linux nel portale di Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Il portale di Azure è uno strumento di gestione basata sul web per i servizi e risorse ospitate nel cloud Microsoft Azure. In questo articolo si imparerà a creare cluster basati su Linux HDInsight tramite il portale.

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Un browser web moderno__. Portale di Azure utilizza HTML5 e Javascript e potrebbe non funzionare correttamente in browser web meno recenti.

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-clusters"></a>Creare cluster

Portale di Azure espone la maggior parte delle proprietà cluster. Usa il modello di gestione risorse di Azure, è possibile nascondere molte dettagli. Per ulteriori informazioni, vedere [Hadoop basati su Linux creare cluster in uso dei modelli di gestione risorse di Azure HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Nuovo**, fare clic su **Dati Analitica**e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")
3. Immettere **Il nome del Cluster**: questo nome deve essere univoco globale.
4. Fare clic su **Seleziona tipo di grafico**e quindi selezionare:

    - **Tipo di cluster**: se non si sa come scegliere, selezionare **Hadoop**. Si tratta del tipo di grafico più popolari.

        > [AZURE.IMPORTANT] HDInsight cluster siano disponibili numerosi tipi corrispondono al carico di lavoro o la tecnologia che il cluster è ottimizzato per. Non esiste alcun metodo supportato per creare un cluster che combina più tipi, ad esempio eccesso e HBase in un cluster. 

    - **Sistema operativo**: selezionare **Linux**.
    - **Versione**: usare la versione predefinita se non si sa quale scegliere. Per ulteriori informazioni, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).
    - **Livello di cluster**: Azure HDInsight fornisce le offerte cloud di dati in due categorie: livello Standard e Premium. Per ulteriori informazioni, vedere [livelli di raggruppamento](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
    
    ![Configurazione di livello premium HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

4. Fare clic su **sottoscrizione** per selezionare l'abbonamento Azure che verrà utilizzato per il cluster.

5. Fare clic su **Gruppo di risorse** per selezionare un gruppo di risorse esistente oppure fare clic su **Nuovo** per creare un nuovo gruppo di risorse

    > [AZURE.NOTE] Questa voce verrà automaticamente a uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **credenziali** e quindi immettere una password per l'utente amministratore. È inoltre necessario immettere un **Nome utente SSH** e una **PASSWORD** o **Chiave pubblica**, che verrà utilizzato per eseguire l'autenticazione utente SSH. Si consiglia di utilizzando una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

    ![Credenziali cluster specifica] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Credenziali cluster specifica")

    Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere uno degli articoli seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne uno nuovo.

    ![Per le origini dati blade] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Configurazione di origine dati specifica")

    Attualmente è possibile selezionare un Account di archiviazione di Azure come origine dati per un cluster di HDInsight. Utilizzare le operazioni seguenti per comprendere le voci nel e **Origine dati** .

    - **Metodo di selezione**: impostare **da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questo **Tasto di scelta rapida** se si desidera immettere il **Nome di spazio di archiviazione** e un **Tasto di scelta rapida** di un account di archiviazione esistente.

    - **Selezionare account di archiviazione / nuovo**: fare clic su **selezionare account di archiviazione** per esplorare e selezionare un account di archiviazione esistente che si desidera associare il cluster. In alternativa, fare clic su **Nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che compare, per immettere il nome dell'account di archiviazione. Se il nome è disponibile, viene visualizzato un segno di spunta verde.

    - **Scegliere contenitore predefinito**: consente di immettere il nome del contenitore predefinito da usare per il raggruppamento. È possibile immettere un nome di seguito, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente riconoscibile che viene utilizzato il contenitore per il cluster specifico.

    - **Posizione**: l'area geografica account di archiviazione o verrà creato.

        > [AZURE.IMPORTANT] Selezionare la posizione per l'origine dati predefinita verrà impostato anche il percorso del cluster HDInsight. L'origine di dati predefiniti e cluster deve trovarsi nella stessa regione.
        
    - **Cluster AAD identità**: configurandolo, apportate cluster accessibile per gli archivi di Azure dati Lake in base alla configurazione AAD.

    Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Livelli prezzi nodo** per visualizzare informazioni su nodi che verranno creati per questo cluster. Impostare il numero dei nodi di lavoro che è necessario per il cluster. Verrà visualizzato il costo previsto del cluster all'interno e il.

    ![Blade livelli prezzi nodo] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Specificare il numero dei nodi cluster")
    
    > [AZURE.IMPORTANT] Se si prevede di più di 32 nodi di lavoro, al momento della creazione cluster o modificando il cluster dopo la creazione, è necessario selezionare una dimensione di nodo principale con almeno 8 core e 14GB di ram.
    >
    > Per ulteriori informazioni su costi associati e le dimensioni massime nodo, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Fare clic su **Seleziona** per salvare la configurazione dei prezzi nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione cluster, come configurare altre impostazioni facoltativi, ad esempio partecipare a una **Rete virtuale**, impostazione di un **Metastore esterni** per contenere dati per Hive e Oozie, utilizzare le azioni Script per personalizzare un cluster per installare componenti personalizzati o usare gli account di spazio di archiviazione aggiuntivo con il cluster.

    * **Virtuali**: selezionare una rete virtuale Azure e alla subnet, se si desidera inserire il cluster in una rete virtuale.  

        ![Blade rete virtuale] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Specificare i dettagli di rete virtuale")

        Per informazioni sull'utilizzo di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifiche per la rete virtuale, vedere [funzionalità estendere HDInsight tramite una rete virtuale Azure](hdinsight-extend-hadoop-virtual-network.md).

    * Fare clic su **Metastores esterno** per specificare i database SQL che si desidera utilizzare per salvare i metadati Hive e Oozie associato al cluster.
    
        > [AZURE.NOTE] Configurazione di Metastore non è disponibile per i tipi di grafico HBase.

        ![Blade metastores personalizzata] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Specificare metastores esterni")

        Per **utilizzare un esistente di SQL DB Hive** metadati, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **utilizzare un DB SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** finché non si trovi indietro e il **Facoltativi** .

        >[AZURE.NOTE] Il database di SQL Azure utilizzato per la metastore deve consentono la connessione a altri servizi di Azure, tra cui Azure HDInsight. Nel dashboard di database SQL Azure, sul lato destro fare clic sul nome del server. Si tratta del server che esegue l'istanza di database SQL. Una volta sono nella visualizzazione server, fare clic su **Configura**e quindi fare clic su **Sì**per i **Servizi di Windows Azure**e quindi fare clic su **Salva**.

        &nbsp;

        > [AZURE.IMPORTANT] Quando si crea un metastore, non usare un nome di database che contiene trattini o trattini, poiché ciò potrebbe causare il processo di creazione cluster l'esito negativo.

    * **Azioni di script** se si desidera utilizzare uno script personalizzato per personalizzare un cluster, come il cluster è stata creata. Per ulteriori informazioni sulle operazioni di script, vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md). In e l'azioni Script offrono i dettagli, come illustrato nell'acquisizione della schermata.

        ![Blade azione script] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Azione di script specifica")

    * Fare clic su **Account di archiviazione collegati** per specificare gli account di spazio di archiviazione aggiuntivo da associare il cluster. In e il **Chiavi per l'archiviazione Azure** , fare clic su **Aggiungi una chiave di spazio di archiviazione**, quindi selezionare un account di archiviazione esistente o creare un nuovo account.

        ![Blade di spazio di archiviazione aggiuntivo] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Specificare gli account di spazio di archiviazione aggiuntivo")

        È anche possibile aggiungere account di spazio di archiviazione aggiuntivo dopo la creazione di un cluster.  Vedere [cluster basati su Linux personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md).

        Fare clic su **Seleziona** finché non si trovi indietro e il **Nuovo HDInsight cluster** .
        
        Oltre ad account di archiviazione Blob, è anche possibile collegare Azure dati Lake stores. La configurazione può essere configurando AAD dall'origine dati in cui è configurato l'account di archiviazione predefinito e contenitore predefinito.

10. In e il **Nuovo HDInsight Cluster** , assicurarsi che **Pin per Startboard** sia selezionata e quindi fare clic su **Crea**. Questa operazione verrà creare il cluster e aggiungere un riquadro per renderla alla Startboard del portale Azure. L'icona indica che il cluster è il provisioning e cambierà per visualizzare l'icona HDInsight una volta completato il provisioning.

  	| Durante il provisioning | Il provisioning di completamento |
  	| ------------------ | --------------------- |
  	| ![Indicatore di provisioning in startboard](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Riquadro cluster provisioning](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

    > [AZURE.NOTE] Richiederà molto tempo per il cluster da creare, in genere circa 15 minuti. Utilizzare il riquadro la Startboard o la voce di **notifiche** sul lato sinistro della pagina per verificare se il processo di provisioning.

11. Dopo avere completato il processo di creazione, fare clic sul riquadro per il cluster da Startboard per avviare e il raggruppamento. E il cluster fornisce informazioni essenziali sui cluster, ad esempio nome, il gruppo di risorse che a cui appartiene, il percorso e il sistema operativo, URL per il dashboard cluster e così via.

    ![Blade cluster] (./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Proprietà cluster")

    Utilizzare le operazioni seguenti per comprendere le icone nella parte superiore della stessa questo e e nella sezione **Nozioni di base** :

    * **Impostazioni** e **Tutte le impostazioni**: consente di visualizzare e **l'Impostazioni** per il cluster che consente di accedere a informazioni dettagliate sulla configurazione per il cluster.

    * **Dashboard**, **Dashboard Cluster**e **URL**: si tratta di tutti i metodi di accedere al dashboard di cluster, un portale Web per l'esecuzione di processi sul cluster.

    * **Secure Shell**: le informazioni necessarie per accedere al cluster utilizzando SSH.

    * **Elimina**: Elimina cluster HDInsight.

    * **Guida introduttiva** (![icona cloud e thunderbolt = Guida introduttiva](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): Visualizza le informazioni utili per iniziare a utilizzare HDInsight.

    * **Utenti** (![sull'icona utenti](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti per l'abbonamento Azure.

        > [AZURE.IMPORTANT] Questo _solo_ viene applicata l'accesso e autorizzazioni per il cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi a cluster HDInsight.

    * **Tag** (![sull'icona tag](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): tag consente di impostare coppie di parole chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, può creare una chiave denominata __progetto__e quindi utilizzare un valore comune per tutti i servizi associati a un progetto specifico.

##<a name="customize-clusters"></a>Personalizzare i cluster

- Vedere [personalizzare HDInsight cluster tramite avvio](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Vedere [cluster basati su Linux personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md).

##<a name="delete-the-cluster"></a>Eliminare il cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Passaggi successivi

Ora che è stato creato correttamente un cluster di HDInsight, utilizzare le operazioni seguenti per informazioni su come lavorare con i cluster:

###<a name="hadoop-clusters"></a>Hadoop cluster

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase cluster

* [Guida introduttiva a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppo di applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Cluster eccesso

* [Sviluppare topologie Java per eccesso in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizzare i componenti di Python in eccesso in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare topologie con eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###<a name="spark-clusters"></a>Motori cluster

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)
* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)
* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
