<properties
    pageTitle="Gestire i cluster basati su Linux Hadoop nel portale Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare e gestire cluster basati su Linux HDInsight tramite il portale di Azure."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

#<a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestire i cluster Hadoop in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Tramite il [portale di Azure][azure-portal], è possibile gestire i cluster basati su Linux in Azure HDInsight. Utilizzare il selettore di tabulazione per informazioni sulla creazione di cluster Hadoop in HDInsight usare altri strumenti. 

**Prerequisiti**

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="open-the-portal"></a>Aprire il portale

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Dopo avere aperto il portale, è possibile:

    - Fare clic su **Nuovo** dal menu a sinistra per creare un nuovo cluster:
    
        ![pulsante nuovo su cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
    - Fare clic su **Cluster HDInsight** dal menu a sinistra per visualizzare un elenco cluster esistenti
    
        ![Pulsante di cluster HDInsight portale Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        Se **HDInsight** non è disponibile nel menu a sinistra, fare clic su **Sfoglia**e quindi fare clic su **Cluster HDInsight**.

        ![Azure buttomn di cluster Sfoglia portale](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##<a name="create-clusters"></a>Creare cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight funziona con una vasta gamma di Hadoop componenti. Per l'elenco dei componenti che sono state verificate e supportati, vedere [come individuare quale versione di Hadoop in Azure HDInsight](hdinsight-component-versioning.md). Per informazioni sulla creazione di cluster generali, vedere [creare Hadoop cluster in HDInsight](hdinsight-hadoop-provision-linux-clusters.md). 

##<a name="list-and-show-clusters"></a>Elenco e visualizzare cluster

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** dal menu a sinistra per visualizzare un elenco cluster esistenti.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare filtro nella parte superiore della pagina.
4. Fare doppio clic su un cluster dall'elenco per visualizzare i dettagli.

    **Menu e nozioni di base**:

    ![Nozioni di base di Azure hdinsight portale cluster](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
    
    - **Impostazioni** e **Tutte le impostazioni**: consente di visualizzare e **l'Impostazioni** per il cluster che consente di accedere a informazioni dettagliate sulla configurazione per il cluster.
    - **Dashboard**, **Dashboard Cluster** e * * URL: si tratta di tutti i metodi di accedere al dashboard di cluster, riguarda Ambari Web cluster basati su Linux.
    - **Secure Shell**: vengono visualizzate le istruzioni per la connessione al cluster tramite connessione sicura Shell (SSH).
    - **Scala Cluster**: consente di modificare il numero dei nodi di lavoro per il cluster.
    - **Elimina**: Elimina il cluster.
    - **Guida introduttiva (![icona cloud e thunderbolt = Guida introduttiva](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: Visualizza le informazioni utili per iniziare a utilizzare HDInsight.
    - **Gli utenti (![sull'icona utenti](./media/hdinsight-administer-use-portal-linux/users.png))**: consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti per l'abbonamento Azure.
    
        > [AZURE.IMPORTANT] Questo _solo_ viene applicata l'accesso e autorizzazioni per il cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi a cluster HDInsight.
    - **Tag (![sull'icona tag](./media/hdinsight-administer-use-portal-linux/tags.png))**: tag consente di impostare coppie di parole chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, può creare una chiave denominata __progetto__e quindi utilizzare un valore comune per tutti i servizi associati a un progetto specifico.
    - **Visualizzazioni Ambari**: collegamenti a Ambari Web.
    
    > [AZURE.IMPORTANT] Per gestire i servizi forniti da cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST Ambari. Per ulteriori informazioni sull'uso di Ambari, vedere [gestire HDInsight cluster utilizzando Ambari](hdinsight-hadoop-manage-ambari.md).

    **L'uso**:
    
    ![Uso di cluster di Azure hdinsight portale](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
    
5. Fare clic su **Impostazioni**.

    ![Uso di cluster di Azure hdinsight portale](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

    - **Log di controllo**:
    - **Guida introduttiva**: Visualizza le informazioni utili per iniziare a utilizzare HDInsight.
    - **Scala Cluster**: aumentare e ridurre il numero dei nodi di lavoro cluster.
    - **Secure Shell**: vengono visualizzate le istruzioni per la connessione al cluster tramite connessione sicura Shell (SSH).
    - **HDInsight Partner**: Aggiungi/Rimuovi HDInsight Partner corrente.
    - **Metastores esterno**: visualizzare metastores Hive e Oozie. Il metastores possono essere configurati solo durante il processo di creazione cluster.
    - **Azioni di script**: eseguire Bash script nel cluster.
    - **Proprietà**: visualizzare le proprietà del grafico.
    - **Chiavi per l'archiviazione Azure**: consente di visualizzare l'account predefinito di spazio di archiviazione e la relativa chiave. L'account di archiviazione è configurazione durante il processo di creazione cluster.
    - **Identità AAD cluster**: 
    - **Gli utenti**: consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti per l'abbonamento Azure.
    - **Tag**: tag consente di impostare coppie di parole chiave/valore per definire una tassonomia personalizzata dei servizi cloud. Ad esempio, può creare una chiave denominata __progetto__e quindi utilizzare un valore comune per tutti i servizi associati a un progetto specifico.
    
    > [AZURE.NOTE] Si tratta di un elenco generico di alcune delle impostazioni disponibili; non tutti sarà disponibile per tutti i tipi di grafico.

6. Fare clic su **proprietà**:

    Le proprietà sono:
    
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
    - **Origine dati predefinita**: il file system cluster predefinito.
    - **Nodi lavoro prezzi livello**.
    - **Livello prezzo nodo testa**.

##<a name="delete-clusters"></a>Eliminare cluster

Eliminare un cluster non verrà eliminato l'account di archiviazione predefinito o gli account di archiviazione collegato. È possibile ricreare il cluster con gli stessi account di archiviazione e la stessa metastores. È consigliabile utilizzare un nuovo contenitore Blob predefinito quando si ricrea il cluster.

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

    ![hdinsight eccesso scala ribilanciamento](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

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

    ![scala di motori di hdinsight hadoop hbase eccesso](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##<a name="pauseshut-down-clusters"></a>Pausa/arresta cluster

La maggior parte dei processi Hadoop sono processi batch solo che venga eseguito. Per la maggior parte dei Hadoop cluster, esistono periodi di tempo che non viene utilizzato il cluster di elaborazione lunghi. Con HDInsight, i dati vengono archiviati in archiviazione di Azure, in modo che è possibile eliminare un cluster quando non è in uso.
Anche addebitate per un cluster di HDInsight, anche quando non è in uso. Poiché le spese per il cluster sono tutte le volte più le spese per lo spazio di archiviazione, è opportuno economico eliminare cluster quando non sono in uso.

Esistono diversi modi è possibile programmare il processo:

- Factory Azure dati utente. Per la creazione di servizi HDInsight collegato su richiesta, vedere [creare su richiesta basate su Linux Hadoop cluster nell'utilizzo dei dati di Azure HDInsight](hdinsight-hadoop-create-linux-clusters-adf.md) .
- Usare PowerShell Azure.  Vedere i [dati dei ritardi volo analizza](hdinsight-analyze-flight-delay-data.md).
- Utilizzare CLI Azure. Vedere [cluster di gestire HDInsight usa CLI Azure](hdinsight-administer-use-command-line.md).
- Utilizzare SDK .NET HDInsight. Vedere [inviare Hadoop processi](hdinsight-submit-hadoop-jobs-programmatically.md).

Per informazioni sui prezzi, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Per eliminare un cluster dal portale, vedere [eliminare cluster](#delete-clusters)

##<a name="change-passwords"></a>Cambiare le password

Un cluster di HDInsight può avere due account utente. Il HDInsight cluster (noto anche account utente Account utente HTTP) e l'account utente SSH vengono creati durante il processo di creazione. È possibile Ambari web dell'interfaccia utente per cambiare i cluster nome utente dell'account utente e password e le azioni di script per modificare l'account utente SSH

###<a name="change-the-cluster-user-password"></a>Modificare la password dell'utente cluster

È possibile utilizzare l'interfaccia utente Web Ambari per modificare la password dell'utente Cluster. Per accedere a Ambari, è necessario usare il nome utente cluster esistente e la password.

> [AZURE.NOTE] Se si cambia la password dell'utente (amministratori) cluster, potrebbe script azioni state eseguite su questo cluster l'esito negativo. Se si dispone di tutte le azioni di script persistente che nodi di lavoro di destinazione, si potrebbe non riuscire quando si aggiungono nodi al cluster tramite operazioni di ridimensionamento. Per ulteriori informazioni sulle azioni di script, vedere [personalizzare HDInsight cluster utilizzo di azioni di script](hdinsight-hadoop-customize-cluster-linux.md).

1. Accedere all'interfaccia utente Web Ambari usando le credenziali dell'utente cluster HDInsight. Il nome utente predefinito è **admin**. L'URL è **https://&lt;nome Cluster HDInsight > azurehdinsight.net**.
2. Fare clic su **amministratore** dal menu superiore e quindi fare clic su "Gestisci Ambari". 
3. Nel menu a sinistra, fare clic su **utenti**.
4. Fare clic su **amministratore**.
5. Fare clic su **Cambia Password**.

Ambari quindi cambia la password in tutti i nodi del cluster.

###<a name="change-the-ssh-user-password"></a>Modificare la password dell'utente SSH

1. Con un editor di testo, salvare quanto segue come un file denominato __changepassword.sh__.

    > [AZURE.IMPORTANT] È necessario utilizzare un editor che utilizza una nuova riga come la fine della riga. Se l'editor utilizza CRLF, quindi lo script non funzionerà.
    
        #! /bin/bash
        USER=$1
        PASS=$2

        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER

2. Caricare il file in una posizione di archiviazione che è possibile accedervi da HDInsight tramite un indirizzo HTTP o HTTPS. Ad esempio, un file pubblica archiviare, ad esempio OneDrive o Blob Azure lo spazio di archiviazione. Salvare il file URI (indirizzo HTTP o HTTPS), come questa operazione è necessaria nel passaggio successivo.

3. Dal portale di Azure, selezionare il cluster HDInsight e quindi selezionare __tutte le impostazioni__. Selezionare __Le azioni Script__e __l'Impostazioni__ .

4. Selezionare __Nuovo invio__e __l'Azioni Script__ . Quando viene visualizzata e __l'azione script invia__ , immettere le informazioni seguenti.

  	| Campo | Valore |
  	| ----- | ----- |
  	| Nome | Modifica ssh password |
  	| Script Bash URI | URI al file changepassword.sh |
  	| Nodi (testa, lavoro, Nimbus, supervisore, Zookeeper e così via) | ✓ per tutti i tipi di nodo elencati |
  	| Parametri | Immettere il nome dell'utente SSH e quindi la nuova password. Dovrebbe essere presente uno spazio tra il nome utente e la password.
  	| Mantenere questa azione di script... | Lasciare questo campo è deselezionata.

5. Selezionare __Crea__ per applicare lo script. Al termine dell'esecuzione script, sarà possibile connettersi a cluster utilizzando SSH con la nuova password.

##<a name="grantrevoke-access"></a>Concessione/revoca accesso

HDInsight cluster sono i seguenti servizi web HTTP (tutti questi servizi presentano endpoint REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Per impostazione predefinita, questi servizi vengono concessi per l'accesso. È possibile revoke/concedere l'accesso tramite [CLI Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

##<a name="find-the-subscription-id"></a>Trovare l'ID di abbonamento

**Per trovare l'ID di abbonamento Azure**

1. Accedere al [portale][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra e quindi fare clic su **abbonamenti**. Ogni abbonamento con un nome e un ID.

Ogni cluster è collegata a un abbonamento a Azure. ID abbonamento viene visualizzata nel riquadro **essenziali** di cluster. Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).

##<a name="find-the-resource-group"></a>Individuare il gruppo di risorse 

In modalità ARM ogni cluster HDInsight viene creato con un gruppo di risorse Azure. Gruppo di risorse Azure appartenente a un cluster viene visualizzata:

- L'elenco di cluster contiene una colonna di **Gruppo di risorse** .
- Riquadro **essenziali** cluster.  

Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).


##<a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito

Ogni cluster HDInsight dispone di un account di archiviazione predefinito. L'account di archiviazione predefinito e le relative chiavi per un cluster verrà visualizzato in **Impostazioni**/**proprietà**/**Chiavi per l'archiviazione Azure**. Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).


##<a name="run-hive-queries"></a>Eseguire query di Hive

Non è possibile eseguire il processo di Hive direttamente dal portale di Azure, ma è possibile utilizzare la visualizzazione Hive sull'interfaccia utente Web Ambari.

**Per eseguire query Hive usando la vista Hive Ambari**

1. Accedere all'interfaccia utente Web Ambari usando le credenziali dell'utente cluster HDInsight. Il nome utente predefinito è **admin**. L'URL è **https://&lt;nome Cluster HDInsight > azurehdinsight.net**.
2. Visualizzare Hive come illustrato nella schermata seguente:  

    ![visualizzazione di hive hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Fare clic su **Query** dal menu superiore.
4. Immettere una query Hive **nell'Editor di Query**e quindi fare clic su **Esegui**.

##<a name="monitor-jobs"></a>Monitorare i processi

Vedere [gestire HDInsight cluster tramite l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

##<a name="browse-files"></a>Sfogliare i file

Tramite il portale di Azure, è possibile esplorare il contenuto del contenitore predefinito.

1. Accedere a [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** dal menu a sinistra per visualizzare un elenco cluster esistenti.
3. Fare clic sul nome del cluster. Se l'elenco di cluster è lungo, è possibile utilizzare filtro nella parte superiore della pagina.
4. Fare clic su **Impostazioni**.
5. Da blade **Impostazioni** , fare clic su **Chiavi per l'archiviazione Azure**.
6. Scegliere il nome di account di archiviazione predefinito.
7. Fare clic sul riquadro **BLOB** .
8. Fare clic su nome del contenitore predefinito.


##<a name="monitor-cluster-usage"></a>Monitorare l'utilizzo di cluster

Nella sezione __l'uso__ della stessa e cluster HDInsight vengono visualizzate informazioni sul numero di core disponibili all'abbonamento per l'utilizzo con HDInsight, come il numero di core allocata per il cluster e la modalità di allocazione nodi all'interno di questo cluster. Vedere [cluster di elenco e la presentazione](#list-and-show-clusters).

> [AZURE.IMPORTANT] Per controllare i servizi forniti da cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST Ambari. Per ulteriori informazioni sull'uso di Ambari, vedere [cluster di gestire HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

##<a name="connect-to-a-cluster"></a>Connettersi a un cluster

Vedere [usare Hive con Hadoop in HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).
    
##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state come creare un cluster HDInsight tramite il portale e come aprire lo strumento della riga di comando Hadoop. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Amministrare tramite PowerShell Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Amministrare usa CLI Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-provision-clusters.md)
* [Utilizzare Hive in HDInsight](hdinsight-use-hive.md)
* [Usare maialino in HDInsight](hdinsight-use-pig.md)
* [Usare Sqoop in HDInsight](hdinsight-use-sqoop.md)
* [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Come individuare quale versione di Hadoop è in Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Riga di comando Hadoop"
