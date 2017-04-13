<properties
   pageTitle="Creare cluster Hadoop in HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster per Azure HDInsight tramite il portale di Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster basato su Windows Hadoop nel portale di Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare un cluster di Hadoop nel portale di Azure HDInsight. Microsoft [Azure portale](../azure-portal-overview.md) è una posizione centralizzata in cui è possibile eseguire il provisioning e gestire le risorse Azure. Portale di Azure corrisponde a uno degli strumenti che è possibile utilizzare per creare cluster Hadoop basati su Linux o basato su Windows in HDInsight. Per la creazione di altri cluster caratteristiche e strumenti fare clic su Seleziona scheda nella parte superiore di questa pagina oppure [metodi di creazione Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Prima di iniziare le istruzioni fornite in questo articolo, è necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare cluster


**Per creare un cluster di HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo**, fare clic su **Dati Analitica**e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure] (./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Digitare o selezionare i valori seguenti:

    * **Nome cluster**: immettere un nome per il cluster. Un segno di spunta verde viene visualizzata accanto al nome del cluster se il nome è disponibile.

    * **Tipo di cluster**: selezionare **Hadoop**. Altre opzioni inclue **HBase** **eccesso**e **motori**.

        > [AZURE.IMPORTANT] HDInsight cluster siano disponibili numerosi tipi corrispondono al carico di lavoro o la tecnologia che il cluster è ottimizzato per. Non esiste alcun metodo supportato per creare un cluster che combina più tipi, ad esempio eccesso e HBase in un cluster.

    * **Sistema operativo del cluster**: selezionare **finestre**. Per creare un cluster di base Linux, selezionare **Linux**.
    * **Versione**: vedere [versioni HDInsight](hdinsight-component-versioning.md).
    * **Abbonamento**: selezionare l'abbonamento Azure che verrà utilizzato per la creazione di questo cluster.
    * **Gruppo risorse**: selezionare un oggetto esistente o creare un nuovo gruppo di risorse. Questa voce verrà automaticamente a uno dei gruppi di risorse esistenti, se disponibili.
    * **Le credenziali**: configurare il nome utente e la password per l'utente Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, sarà necessario configurare il nome utente desktop remoto utente e password e la data di scadenza. Fare clic su **Seleziona** nella parte inferiore di salvare le modifiche.

        ![Credenziali cluster specifica] (./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Credenziali cluster specifica")

    * **Origine dati**: creare un nuovo o selezionare un account di archiviazione Azure esistente da utilizzare come il sistema di file predefinito per il cluster.

        ![Per le origini dati blade] (./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Configurazione di origine dati specifica")

        * **Metodo di selezione**: impostare **da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questo **Tasto di scelta rapida** se si desidera immettere il **Nome di spazio di archiviazione** e un **Tasto di scelta rapida** di un account di archiviazione esistente.
        * **Selezionare account di archiviazione / Crea nuova**: fare clic su **selezionare account di archiviazione** per esplorare e selezionare un account di archiviazione esistente che si desidera associare il cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che compare, per immettere il nome dell'account di archiviazione. Se il nome è disponibile, viene visualizzato un segno di spunta verde.
        * **Scegliere contenitore predefinito**: consente di immettere il nome del contenitore predefinito da usare per il raggruppamento. È possibile immettere un nome di seguito, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente riconoscibile che viene utilizzato il contenitore per il cluster specifico.
        * **Posizione**: l'area geografica account di archiviazione o verrà creato. Questo percorso consente di verificare la posizione di cluster.  Il cluster e il relativo account di archiviazione predefinito devono posizionare nell'interfaccia di dati di Azure stesso.
    
    * **Nodo prezzi livelli**: impostare il numero dei nodi di lavoro che è necessario per il cluster. Verrà visualizzato il costo previsto del cluster all'interno e il.
  

        ![Blade livelli prezzi nodo] (./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specificare il numero dei nodi cluster")


    * **Configurazione facoltativa** per selezionare la versione cluster, come configurare altre impostazioni facoltativi, ad esempio partecipare a una **Rete virtuale**, impostazione di un **Metastore esterni** per contenere dati per Hive e Oozie, utilizzare le azioni Script per personalizzare un cluster per installare componenti personalizzati o usare gli account di spazio di archiviazione aggiuntivo con il cluster.

    * **Versione HDInsight**: selezionare la versione che si desidera utilizzare per il cluster. Per ulteriori informazioni, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).
    * **Virtuali**: selezionare una rete virtuale Azure e alla subnet, se si desidera inserire il cluster in una rete virtuale.  

        ![Blade rete virtuale] (./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specificare i dettagli di rete virtuale")

        Per informazioni sull'utilizzo HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifiche per la rete virtuale, vedere [capbilities HDInsight estendere tramite una rete virtuale Azure](hdinsight-extend-hadoop-virtual-network.md).
  

        
    * **Metastores esterno**: specificare un database di SQL Azure per archiviare metadati Hive e Oozie associato al cluster.
 
        > [AZURE.NOTE] Configurazione di Metastore non è disponibile per i tipi di grafico HBase.

    ![Blade metastores personalizzata] (./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specificare metastores esterni")

    Per **utilizzare un esistente di SQL DB Hive** metadati, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **utilizzare un DB SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** finché non si trovi indietro e il **Facoltativi** .

    >[AZURE.NOTE] Il database di SQL Azure utilizzato per la metastore deve consentono la connessione a altri servizi di Azure, tra cui Azure HDInsight. Nel dashboard di database SQL Azure, sul lato destro fare clic sul nome del server. Si tratta del server che esegue l'istanza di database SQL. Una volta sono nella visualizzazione server, fare clic su **Configura**e quindi fare clic su **Sì**per i **Servizi di Windows Azure**e quindi fare clic su **Salva**.

            &nbsp;

            > [AZURE.IMPORTANT] Quando si crea un metastore, non usare un nome di database che contiene trattini o trattini, poiché ciò potrebbe causare il processo di creazione cluster l'esito negativo.
        
        * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
    

            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

            ![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Fare clic su **Crea**. Selezionare **Aggiungi a Startboard** verrà aggiunto un riquadro per cluster Startboard del portale. L'icona indica che il cluster viene creato e verrà modificato per visualizzare l'icona HDInsight dopo il completamento della creazione.
    
    Richiederà molto tempo per il cluster da creare, in genere circa 15 minuti. Utilizzare il riquadro la Startboard o la voce di **notifiche** sul lato sinistro della pagina per verificare se il processo di provisioning.
    

5. Una volta completata la creazione, fare clic sul riquadro per il cluster da Startboard per avviare e il raggruppamento. E il cluster fornisce informazioni essenziali sui cluster, ad esempio nome, il gruppo di risorse che a cui appartiene, il percorso e il sistema operativo, URL per il dashboard cluster e così via.


    ![Blade cluster] (./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Proprietà cluster")


    Utilizzare le operazioni seguenti per comprendere le icone nella parte superiore della stessa questo e e nella sezione **Nozioni di base** :


    * **Impostazioni** e **Tutte le impostazioni**: consente di visualizzare e **l'Impostazioni** per il cluster che consente di accedere a informazioni dettagliate sulla configurazione per il cluster.
    * **Dashboard**, **Dashboard Cluster**e **URL**: si tratta di tutti i metodi di accedere al dashboard di cluster, un portale Web per l'esecuzione di processi sul cluster.
    * **Desktop remoto**: consente di abilitare/disabilitare desktop remoto nei nodi del cluster.
    * **Scala Cluster**: consente di modificare il numero dei nodi di lavoro per il cluster.
    * **Elimina**: Elimina cluster HDInsight.
    * **Guida introduttiva** (![icona cloud e thunderbolt = Guida introduttiva](./media/hdinsight-provision-clusters/quickstart.png)): Visualizza le informazioni utili per iniziare a utilizzare HDInsight.
    * **Utenti** (![sull'icona utenti](./media/hdinsight-provision-clusters/users.png)): consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti per l'abbonamento Azure.
    

        > [AZURE.IMPORTANT] Questo _solo_ viene applicata l'accesso e autorizzazioni per il cluster nel portale e non ha alcun effetto su chi può connettersi o inviare processi a cluster HDInsight.
        
    * **Tag** (![sull'icona tag](./media/hdinsight-provision-clusters/tags.png)): tag consente di impostare coppie di parole chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, può creare una chiave denominata __progetto__e quindi utilizzare un valore comune per tutti i servizi associati a un progetto specifico.

##<a name="customize-clusters"></a>Personalizzare i cluster

- Vedere [personalizzare HDInsight cluster tramite avvio](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Vedere [cluster basato su Windows personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster.md).

##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state fornite diversi modi per creare un cluster di HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - informazioni su come iniziare a lavorare con i cluster HDInsight
* [Inviare Hadoop processi a livello di programmazione](hdinsight-submit-hadoop-jobs-programmatically.md) - informazioni su come inviare a livello di programmazione processi a HDInsight
* [Gestire i cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)


