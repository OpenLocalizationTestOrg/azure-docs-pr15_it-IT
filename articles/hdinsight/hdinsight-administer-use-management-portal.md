<properties
    pageTitle="Gestire i cluster Hadoop nel portale di Azure HDInsight | Microsoft Azure"
    description="Informazioni su come amministrare HDInsight Service. Creare un cluster di HDInsight, aprire la console JavaScript interattiva e aprire la console di comando Hadoop."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire i cluster Hadoop in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Tramite il [portale di Azure][azure-portal], è possibile creare cluster Hadoop in Azure HDInsight, modificare la password utente Hadoop e abilitare remoto protocollo RDP (Desktop) in modo che è possibile accedere alla console di comando Hadoop nel cluster.

Le informazioni in questo articolo si applicano solo ai cluster basate su finestra HDInsight. Per informazioni sulla gestione dei cluster basati su Linux, fare clic sul selettore di tabulazione precedente.

Fare clic sul selettore di tabulazione per informazioni sulla creazione di cluster Hadoop in HDInsight usare altri strumenti. 

**Prerequisiti**

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Account di archiviazione di azure** - HDInsight un cluster utilizza un contenitore di spazio di archiviazione Blob Azure come file system predefinito. Per ulteriori informazioni su come archiviazione Blob Azure offre un'esperienza integrata con i cluster HDInsight, vedere [Usare l'archiviazione Blob Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per informazioni dettagliate sulla creazione di un account di archiviazione Azure, vedere [come creare un Account di archiviazione](../storage/storage-create-storage-account.md).

##<a name="open-the-portal"></a>Aprire il portale

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Dopo avere aperto il portale, è possibile:

    - Fare clic su **Nuovo** dal menu a sinistra per creare un nuovo cluster:
    
        ![pulsante nuovo su cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
    - Fare clic su **Cluster HDInsight** dal menu a sinistra.
    
        ![Pulsante di cluster HDInsight portale Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

    Se **HDInsight** non è disponibile nel menu a sinistra, fare clic su **Sfoglia**. 

    ![Azure portale cluster pulsante](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##<a name="create-clusters"></a>Creare cluster

Per istruzioni di creazione tramite il portale, vedere [creare HDInsight cluster](hdinsight-provision-clusters.md#create-using-the-preview-portal).

HDInsight funziona con una vasta gamma di Hadoop componenti. Per l'elenco dei componenti che sono state verificate e supportati, vedere [come individuare quale versione di Hadoop in Azure HDInsight](hdinsight-component-versioning.md). È possibile personalizzare HDInsight usando una delle opzioni seguenti:

- Azione Script per l'esecuzione di script personalizzati che è possibile personalizzare un cluster per cambiare configurazione cluster o installare componenti personalizzati, ad esempio Giraph o Solr. Per ulteriori informazioni, vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md).
- Utilizzare i parametri di personalizzazione cluster del PowerShell Azure HDInsight .NET SDK durante la creazione di cluster. Queste modifiche alla configurazione quindi mantenute per tutta la durata del cluster e non sono interessate da reimages nodo cluster che piattaforma Azure esegue periodicamente per la manutenzione. Per ulteriori informazioni sull'utilizzo di parametri della personalizzazione cluster, vedere [creare HDInsight cluster](hdinsight-provision-clusters.md).
- Alcuni componenti di linguaggio nativi, ad esempio Mahout e CSS, possono essere eseguiti sul cluster come file VASO. Questi file VASO possono essere distribuiti a archiviazione Blob Azure e inviare HDInsight cluster tramite meccanismi di invio processo Hadoop. Per ulteriori informazioni, vedere [inviare Hadoop processi a livello di programmazione](hdinsight-submit-hadoop-jobs-programmatically.md).

    >[AZURE.NOTE] Se si sono verificati problemi per cluster HDInsight distribuzione VASO file o la chiamata file VASO su cluster HDInsight, contattare il [Supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

    > CSS non è supportato da HDInsight e non è idoneo per il supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [quali sono le novità in versioni cluster fornite da HDInsight?](hdinsight-component-versioning.md).

Installazione del software personalizzato nel cluster utilizzando connessione Desktop remoto non è supportata. Evitare di archiviare un file in unità di nodo principale come pertanto andranno perse se è necessario ricreare i cluster. È consigliabile archiviare i file in archiviazione Blob Azure. Archiviazione BLOB viene mantenuta.

##<a name="list-and-show-clusters"></a>Elenco e visualizzare cluster

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** dal menu a sinistra.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare filtro nella parte superiore della pagina.
4. Fare doppio clic su un cluster dall'elenco per visualizzare i dettagli.

    **Menu e nozioni di base**:

    ![Azure portale HDInsight cluster nozioni di base](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
    
    - Per personalizzare il menu, pulsante destro del mouse in un punto qualsiasi del menu e quindi fare clic su **Personalizza**.
    - **Impostazioni** e **Tutte le impostazioni**: consente di visualizzare e **l'Impostazioni** per il cluster che consente di accedere a informazioni dettagliate sulla configurazione per il cluster.
    - URL di **Dashboard**, **Dashboard Cluster** e **: si tratta di tutti i metodi di accedere al dashboard di cluster, riguarda Ambari Web cluster basati su Linux. - **Sicura Shell * *: vengono visualizzate le istruzioni per la connessione al cluster tramite connessione sicura Shell (SSH).
    - **Scala Cluster**: consente di modificare il numero dei nodi di lavoro per il cluster.
    - **Elimina**: Elimina il cluster.
    - **Guida introduttiva (![icona cloud e thunderbolt = Guida introduttiva](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: Visualizza le informazioni utili per iniziare a utilizzare HDInsight.
    - **Gli utenti (![sull'icona utenti](./media/hdinsight-administer-use-portal-linux/users.png))**: consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti per l'abbonamento Azure.
    
        > [AZURE.IMPORTANT] Questo _solo_ viene applicata l'accesso e autorizzazioni per il cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi a cluster HDInsight.
    - **Tag (![sull'icona tag](./media/hdinsight-administer-use-portal-linux/tags.png))**: tag consente di impostare coppie di parole chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, può creare una chiave denominata __progetto__e quindi utilizzare un valore comune per tutti i servizi associati a un progetto specifico.
    - **Visualizzazioni Ambari**: collegamenti a Ambari Web.
    
    > [AZURE.IMPORTANT] Per gestire i servizi forniti da cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST Ambari. Per ulteriori informazioni sull'uso di Ambari, vedere [gestire HDInsight cluster utilizzando Ambari](hdinsight-hadoop-manage-ambari.md).

    **L'uso**:
    
    ![Uso di cluster di Azure hdinsight portale](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
    
5. Fare clic su **Impostazioni**.

    ![Uso di cluster di Azure hdinsight portale](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

    - **Proprietà**: visualizzare le proprietà del grafico.
    - **Identità AAD cluster**: 
    - **Chiavi per l'archiviazione Azure**: consente di visualizzare l'account predefinito di spazio di archiviazione e la relativa chiave. L'account di archiviazione è configurazione durante il processo di creazione cluster.
    - **Accesso cluster**: modificare il nome utente cluster HTTP e la password.
    - **Metastores esterno**: visualizzare metastores Hive e Oozie. Il metastores possono essere configurati solo durante il processo di creazione cluster.
    - **Scala Cluster**: aumentare e ridurre il numero dei nodi di lavoro cluster.
    - **Desktop remoto**: abilitare e disabilitare l'accesso desktop remoto (RDP) e configurare il nome utente RDP.  Il nome dell'utente RDP deve essere diverso dal nome utente HTTP.
    - **Partner di Record**:
    
    > [AZURE.NOTE] Si tratta di un elenco generico di alcune delle impostazioni disponibili; non tutti sarà disponibile per tutti i tipi di grafico.

6. Fare clic su **proprietà**:

    Gli elenchi di proprietà le operazioni seguenti:
    
    - **Hostname**: nome Cluster.
    - **URL del cluster**.
    - **Stato**: includere interrotta, accettata, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operativi, in esecuzione, l'errore, l'eliminazione, eliminata, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
    - **L'area geografica**: percorso Azure. Per un elenco dei percorsi di Azure supportati, vedere l'elenco a discesa **area** sui [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
    - **Dati creati**.
    - **Sistema operativo**: uno dei due **Windows** o **Linux**.
    - **Tipo**: Hadoop, HBase, eccesso, riguardo. 
    - **Versione**. Visualizzare [le versioni HDInsight](hdinsight-component-versioning.md)
    - **Abbonamento**: nome dell'abbonamento.
    - **ID abbonamento**.
    - **Origine dati primaria**. Account di archiviazione Blob Azure utilizzato come file system Hadoop predefinito.
    - **Nodi lavoro prezzi livello**.
    - **Livello prezzo nodo testa**.

##<a name="delete-clusters"></a>Eliminare cluster

Eliminare un cluster non verrà eliminato l'account di archiviazione predefinito o gli account di archiviazione collegato. È possibile ricreare il cluster con gli stessi account di archiviazione e la stessa metastores.

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Elimina** dal menu superiore e quindi seguire le istruzioni.

Vedere anche [Pausa/arresta cluster](#pauseshut-down-clusters).

##<a name="scale-clusters"></a>Cluster di scala
Cluster proporzioni dei caratteri caratteristica consente di modificare il numero dei nodi di lavoro utilizzato da un cluster che è in esecuzione in Azure HDInsight senza dover ricreare il cluster.

>[AZURE.NOTE] Solo cluster con HDInsight versione 3.1.3 o superiore sono supportati. Se non si conosce la versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).

Effetti della modifica del numero dei nodi di dati per ogni tipo di grafico supportato da HDInsight:

- Hadoop

    Diretta, è possibile aumentare il numero dei nodi di lavoro in un cluster di Hadoop che è in esecuzione senza impatto processi in sospeso o è in esecuzione. Nuovi processi possono essere inviati anche durante l'esecuzione dell'operazione. Gli errori in un'operazione di ridimensionamento normalmente vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster di Hadoop viene ridimensionato verso il basso, riducendo il numero dei nodi di dati, alcuni dei servizi del cluster, è necessario riavviare. In questo modo tutti in esecuzione e processi in sospeso in fase di completamento dell'operazione di ridimensionamento. Al termine dell'operazione, è possibile, tuttavia, inviare nuovamente i processi.

- HBase

    Diretta, è possibile aggiungere o rimuovere nodi al cluster HBase mentre è in esecuzione. Server regionali automaticamente Bilanciamento all'interno di pochi minuti per completare l'operazione di ridimensionamento. Tuttavia, è possibile saldare manualmente server regionali accedendo headnode di cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Per ulteriori informazioni sull'utilizzo di shell HBase, vedere]
- Eccesso

    Diretta, è possibile aggiungere o rimuovere dati nodi al cluster eccesso mentre è in esecuzione. Ma dopo il completamento dell'operazione di ridimensionamento, sarà necessario ribilanciare la topologia.

    Riequilibrare può essere eseguita in due modi:

    * Interfaccia utente web eccesso
    * Strumento dell'interfaccia della riga di comando (CLI)

    Fare riferimento alla [documentazione per eccesso Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) per altri dettagli.

    L'interfaccia web eccesso è disponibile in cluster HDInsight:

    ![Ribilanciamento scala di eccesso HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Di seguito viene illustrato come utilizzare il comando CLI per ribilanciare la topologia eccesso:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Per ridimensionare cluster**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu superiore e quindi fare clic su **Scala Cluster**.
4. Immettere i **nodi di un numero di lavoro**. Il limite per il numero del nodo cluster varia a seconda dei Azure abbonamenti. È possibile contattare il supporto di fatturazione per aumentare il limite.  Le informazioni sul costo rifletteranno le modifiche apportate al numero dei nodi.

    ![Scala HDInsight Hadoop HBase eccesso motori](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##<a name="pauseshut-down-clusters"></a>Pausa/arresta cluster

La maggior parte dei processi Hadoop sono processi batch solo che venga eseguito. Per la maggior parte dei Hadoop cluster, esistono periodi di tempo che non viene utilizzato il cluster di elaborazione lunghi. Con HDInsight, i dati vengono archiviati in archiviazione di Azure, in modo che è possibile eliminare un cluster quando non è in uso.
Anche addebitate per un cluster di HDInsight, anche quando non è in uso. Poiché le spese per il cluster sono tutte le volte più le spese per lo spazio di archiviazione, è opportuno economico eliminare cluster quando non sono in uso.

Esistono diversi modi è possibile programmare il processo:

- Factory Azure dati utente. Vedere [Azure HDInsight collegate servizio](../data-factory/data-factory-compute-linked-services.md) e [trasformazione e utilizzando Azure Data Factory analizzare](../data-factory/data-factory-data-transformation-activities.md) per i servizi di HDInsight collegato su richiesta e self-definiti.
- Usare PowerShell Azure.  Vedere i [dati dei ritardi volo analizza](hdinsight-analyze-flight-delay-data.md).
- Utilizzare CLI Azure. Vedere [cluster di gestire HDInsight usa CLI Azure](hdinsight-administer-use-command-line.md).
- Utilizzare SDK .NET HDInsight. Vedere [inviare Hadoop processi](hdinsight-submit-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [eliminare cluster](#delete-clusters)

##<a name="change-cluster-username"></a>Modifica cluster username

Un cluster di HDInsight può avere due account utente. L'account utente di HDInsight cluster viene creato durante il processo di creazione. È anche possibile creare un account utente RDP per l'accesso a cluster tramite RDP. Vedere [abilitare desktop remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Per modificare il nome utente cluster HDInsight e la password**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu superiore e quindi fare clic su **Login Cluster**.
4. Se è stata attivata **login Cluster** , è necessario fare clic su **Disattiva**e quindi fare clic su **Attiva** per poter cambiare il nome utente e la password..
4. Modificare il **Nome di accesso Cluster** e/o la **Password di accesso Cluster**e quindi fare clic su **Salva**.

    ![HDInsight modificare cluster nomeutente password http utente](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##<a name="grantrevoke-access"></a>Concessione/revoca accesso

HDInsight cluster sono i seguenti servizi web HTTP (tutti questi servizi presentano endpoint REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Per impostazione predefinita, questi servizi vengono concessi per l'accesso. È possibile revocare/concedere l'accesso dal portale di Azure.

>[AZURE.NOTE] Per concedere o revocare l'accesso, si verrà reimpostare il nome utente cluster e la password.

**Per concedere l'accesso al servizio web HTTP/istruzione revoke**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu superiore e quindi fare clic su **Login Cluster**.
4. Se è stata attivata **login Cluster** , è necessario fare clic su **Disattiva**e quindi fare clic su **Attiva** per poter cambiare il nome utente e la password..
6. **Cluster accesso utente** e **La Password di accesso di Cluster**, immettere il nuovo nome utente e la password, rispettivamente, per il cluster.
7. Fare clic su **Salva**.

    ![HDInsight complessivi rimuovere accesso al servizio web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##<a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito

Ogni cluster HDInsight dispone di un account di archiviazione predefinito. L'account di archiviazione predefinito e le relative chiavi per un cluster verrà visualizzato in **Impostazioni**/**proprietà**/**Chiavi per l'archiviazione Azure**. Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).

    
##<a name="find-the-resource-group"></a>Individuare il gruppo di risorse 

Nella modalità di gestione risorse di Azure, ogni cluster HDInsight viene creato con un gruppo di risorse Azure. Gruppo di risorse Azure appartenente a un cluster viene visualizzata:

- L'elenco di cluster contiene una colonna di **Gruppo di risorse** .
- Riquadro **essenziali** cluster.  

Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).
   
##<a name="open-hdinsight-query-console"></a>Aprire la console HDInsight Query

Console di HDInsight Query include le caratteristiche seguenti:

- **Guida introduttiva raccolta**: per usare la raccolta, vedere [Informazioni su Hadoop mediante la raccolta di Azure HDInsight Guida introduttiva](hdinsight-learn-hadoop-use-sample-gallery.md).
- **Editor Hive**: A interfaccia grafica web per l'invio di processi Hive.  Vedere [query eseguire Hive tramite la Console di Query](hdinsight-hadoop-use-hive-query-console.md).

    ![Editor hive portale HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **Cronologia dei processi**: Hadoop Monitor processi.  

    ![Cronologia dei processi del portale di HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Fare clic sul **Nome di Query** per visualizzare i dettagli della proprietà del processo, **Query processo**, inclusi e * * Output processo. Workstation in uso, è possibile scaricare la query e l'output.

- **File Browser**: esplorare l'account di archiviazione predefinito e gli account di archiviazione collegato.

    ![Visualizzazione del browser di file portale HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Nella schermata, la **<Account>** tipo indica l'elemento è un account di archiviazione Azure.  Fare clic sul nome dell'account per accedere ai file.
    
- **Interfaccia utente Hadoop**.

    ![HDInsight portal Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
    
    Da **Hadoop dell'interfaccia utente*, è possibile esplorare i file e controllare i registri. 

- **Filati dell'interfaccia utente**.

    ![HDInsight portal filati dell'interfaccia utente](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##<a name="run-hive-queries"></a>Eseguire query di Hive

Per Hive processi è stato eseguito dal portale, fare clic su **Editor Hive** nella console di HDInsight Query. Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).

##<a name="monitor-jobs"></a>Monitorare i processi

Per controllare processi dal portale, fare clic su **Cronologia dei processi** nella console di HDInsight Query. Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).

##<a name="browse-files"></a>Sfogliare i file

Per esplorare i file archiviati in account di archiviazione predefinito e gli account di archiviazione collegato, fare clic su **File Browser** nella console di HDInsight Query. Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).

È anche possibile utilizzare l'utilità **individuare il file system** dall' **Interfaccia utente Hadoop** nella console di HDInsight.  Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).



##<a name="monitor-cluster-usage"></a>Monitorare l'utilizzo di cluster

Nella sezione __l'uso__ della stessa e cluster HDInsight vengono visualizzate informazioni sul numero di core disponibili all'abbonamento per l'utilizzo con HDInsight, come il numero di core allocata per il cluster e la modalità di allocazione nodi all'interno di questo cluster. Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).

> [AZURE.IMPORTANT] Per controllare i servizi forniti da cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST Ambari. Per ulteriori informazioni sull'uso di Ambari, vedere [cluster di gestire HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)


##<a name="open-hadoop-ui"></a>Aprire l'interfaccia utente Hadoop

Per monitorare il cluster, individuare il file system e controllare i registri, fare clic su **Hadoop dell'interfaccia utente** della console di HDInsight Query. Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).

##<a name="open-yarn-ui"></a>Aprire filati dell'interfaccia utente

Per utilizzare l'interfaccia utente di filati, fare clic su **Interfaccia utente di filati** nella console di HDInsight Query. Vedere [console Apri HDInsight Query](#open-hdinsight-query-console).

##<a name="connect-to-clusters-using-rdp"></a>Connettersi a cluster utilizzando RDP

Le credenziali per il cluster fornite al momento della creazione concedere l'accesso ai servizi nel cluster, ma non a cluster tramite Desktop remoto. È possibile attivare l'accesso Desktop remoto durante il provisioning di un cluster o dopo un cluster viene eseguito il provisioning. Per informazioni sull'abilitazione Desktop remoto al momento della creazione, vedere [creare HDInsight cluster](hdinsight-provision-clusters.md).

**Per attivare Desktop remoto**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu superiore e quindi fare clic su **Desktop remoto**.
4. Immettere ** **Scadrà**Remote Desktop nome utente** e **Password Desktop remoto**e quindi fare clic su **Attiva**.

    ![hdinsight attivare disattivare configurare desktop remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    I valori predefiniti per scadrà è una settimana.
> [AZURE.NOTE] È anche possibile usare HDInsight .NET SDK per attivare Desktop remoto in un cluster. Utilizzare il metodo **EnableRdp** sull'oggetto client HDInsight nel modo seguente: **client. EnableRdp (nome cluster, posizione, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Per disattivare Desktop remoto sul cluster, allo stesso modo, è possibile utilizzare il client **. DisableRdp (nome cluster, posizione)**. Per ulteriori informazioni su questi metodi, vedere [Informazioni di riferimento SDK .NET HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Questa opzione è disponibile solo per i cluster HDInsight in esecuzione su Windows.

**Connettersi a un cluster tramite RDP**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **HDInsight cluster**, fare clic sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu superiore e quindi fare clic su **Desktop remoto**.
4. Fare clic su **Connetti** e seguire le istruzioni. Se Connetti sono disattivato, è necessario abilitare prima di tutto. Assicurarsi che l'utente Desktop remoto e la password.  Non è possibile utilizzare le credenziali dell'utente Cluster.


##<a name="open-hadoop-command-line"></a>Aprire la riga di comando Hadoop

Per connettersi al cluster mediante Desktop remoto e utilizzare la riga di comando Hadoop, è necessario innanzitutto aver attivato accesso Desktop remoto al cluster come descritto nella sezione precedente.

**Per aprire una riga di comando Hadoop**

1. Connettersi a cluster con Desktop remoto.
8. Dal desktop, fare doppio clic sulla **riga di comando Hadoop**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    Per ulteriori informazioni sui comandi Hadoop, vedere [informazioni di riferimento Hadoop comandi](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

In alla schermata precedente, il nome della cartella contiene il numero di versione Hadoop incorporato. Il numero di versione modificabili in base alla versione dei componenti Hadoop installati nel cluster. È possibile utilizzare le variabili di ambiente Hadoop per fare riferimento a tali cartelle. Per esempio:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%
    
##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state come creare un cluster HDInsight tramite il portale e come aprire lo strumento della riga di comando Hadoop. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Amministrare tramite PowerShell Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Amministrare usa CLI Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-provision-clusters.md)
* [Inviare i processi di Hadoop a livello di programmazione](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Come individuare quale versione di Hadoop è in Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Riga di comando Hadoop"
