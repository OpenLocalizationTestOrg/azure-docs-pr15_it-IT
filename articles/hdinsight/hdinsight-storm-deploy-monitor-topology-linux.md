<properties
   pageTitle="Distribuire e gestire topologie Apache eccesso su basati su Linux HDInsight | Microsoft Azure"
   description="Informazioni su come distribuire, monitorare e gestire topologie Apache eccesso tramite il Dashboard eccesso nella HDInsight basati su Linux. Usare Hadoop tools per Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Distribuire e gestire topologie Apache eccesso su basati su Linux HDInsight

In questo documento per informazioni sulle nozioni fondamentali di gestione e monitoraggio topologie eccesso in esecuzione in eccesso basati su Linux nei cluster HDInsight.

> [AZURE.IMPORTANT] La procedura descritta in questo articolo richiesta un eccesso basati su Linux cluster HDInsight. Per informazioni sulla distribuzione e il monitoraggio topologie su HDInsight basato su Windows, vedere [Distribuisci e gestire topologie Apache eccesso su basato su Windows HDInsight](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Prerequisiti

* **In base A Linux eccesso cluster HDInsight**: vedere [Guida introduttiva a eccesso Apache su HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) per istruzioni sulla creazione di un cluster

* **Familiarità con SSH e SCP**: per altre informazioni sull'utilizzo di SSH e SCP con HDInsight, vedere il seguente:

    * **Client Linux, Unix o OS X**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Client Windows**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP un client**: viene fornito con tutti i sistemi Linux, Unix e OS X. Per i client di Windows, è consigliabile PSCP, disponibile alla [pagina di download PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Avviare una topologia eccesso

### <a name="using-ssh-and-the-storm-command"></a>Utilizzo di SSH e il comando eccesso

1. Utilizzare SSH per connettersi al cluster HDInsight. Sostituire **il nome utente** il nome di accesso dell'utente SSH. Sostituire **nome cluster** con il proprio nome cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per ulteriori informazioni sull'utilizzo di SSH per connettersi al cluster HDInsight, vedere i documenti seguenti:
    
    * **Client Linux, Unix o OS X**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Client Windows**: vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilizzare il comando seguente per avviare una topologia di esempio:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Verrà avviata la topologia di esempio WordCount sul cluster. Verrà in modo casuale generare frasi e contare le occorrenze di ogni parola le frasi.

    > [AZURE.NOTE] Quando si inviano topologia al cluster, è prima necessario copiare il file di vaso che include il cluster prima di utilizzare il `storm` comando. Questo risultato può essere ottenuto utilizzando il `scp` comando dal client in cui è presente il file. Per esempio`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Nell'esempio WordCount e altri esempi di starter eccesso sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Livello di programmazione

È possibile distribuire a livello di programmazione una topologia in eccesso su HDInsight per comunicare con il servizio Nimbus ospitato nel cluster. [https://github.com/Azure-Samples/hdinsight-Java-deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) viene fornito un esempio applicazione Java che illustra come distribuire e avviare una topologia tramite il servizio Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Monitorare e gestire con il comando eccesso

Il `storm` che consente di lavorare con esecuzione topologie dalla riga di comando. Di seguito è un elenco di comandi utilizzati di frequente. Usare `storm -h` per un elenco completo dei comandi.

### <a name="list-topologies"></a>Elenco topologie

Usare il comando seguente per elencare tutti esecuzione topologie:

    storm list
    
Restituirà informazioni simile al seguente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Disattivare e riattivare

La disattivazione di una topologia sospende il finché non viene eliminato o riattivato. Per disattivare e riattivare, utilizzare il seguente:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminare una topologia in esecuzione

Topologie eccesso, una volta avviate, continuerà in esecuzione fino a interruzione. Per interrompere una topologia, utilizzare il comando seguente:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Ribilanciamento

Riequilibrare una topologia consente al sistema di rivedere il parallelismo della topologia. Ad esempio, se si dispone di ridimensionato il cluster per aggiungere altre note, riequilibrare consentirà una topologia di esecuzione per rendere utilizzare dei nodi di nuovo.

> [AZURE.WARNING] Riequilibrare prima di tutto una topologia disattiva la topologia ridistribuisce dei collaboratori in modo uniforme all'interno del cluster, quindi infine restituisce la topologia lo stato prima riequilibrare si è verificato. Pertanto se la topologia era attiva, diventerà attiva nuovamente. Se è stata disattivata, rimarrà disattivato.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Monitorare e gestire tramite l'interfaccia utente eccesso

L'interfaccia utente eccesso offre un'interfaccia web per l'utilizzo di esecuzione topologie e viene fornito con il cluster HDInsight. Per visualizzare l'interfaccia utente eccesso, utilizzare un web browser per aprire __https://CLUSTERNAME.azurehdinsight.net/stormui__, dove __nome cluster__ è il nome del cluster.

> [AZURE.NOTE] Se viene chiesto di specificare un nome utente e password, immettere l'amministratore del cluster (amministratori) e la password utilizzati quando creare il cluster.


### <a name="main-page"></a>Pagina principale

Nella pagina principale dell'interfaccia utente eccesso sono le seguenti informazioni:

* **Cluster riepilogo**: informazioni di base su cluster eccesso.

* **Topologia riepilogo**: un elenco di esecuzione topologie. Utilizzare i collegamenti in questa sezione per visualizzare ulteriori informazioni sulle topologie specifiche.

* **Supervisore riepilogo**: informazioni su Supervisore eccesso.

* **Configurazione di nimbus**: configurazione Nimbus per il cluster.

### <a name="topology-summary"></a>Topologia di riepilogo

Selezione di un collegamento nella sezione **topologia di riepilogo** vengono visualizzate le seguenti informazioni sulla topologia:

* **Topologia riepilogo**: informazioni di base sulla topologia.

* **Azioni topologia**: azioni di gestione che è possibile eseguire per la topologia.

  * **Attiva**: elaborazione di curriculum di una topologia disattivata.

  * **Disattiva**: sospende una topologia in esecuzione.

  * **Ribilanciare**: consente di regolare il parallelismo della topologia. Dopo avere modificato il numero dei nodi del cluster, si devono ribilanciare topologie in esecuzione. In questo modo la topologia regolare parallelismo a compensa il numero maggiore o minore di nodi del cluster.

    Per ulteriori informazioni, vedere <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">informazioni sul parallelismo di una topologia eccesso</a>.

  * **Eliminare**: e termina con una topologia eccesso il timeout specificato.

* **Stat topologia**: statistiche sulla topologia. Utilizzare i collegamenti nella colonna della **finestra** per impostare il periodo di tempo per le voci rimanenti nella pagina.

* **Spouts**: spouts utilizzato per la topologia. Utilizzare i collegamenti in questa sezione per visualizzare ulteriori informazioni sugli spouts specifico.

* **Bulloni**: bulloni utilizzati per la topologia. Utilizzare i collegamenti in questa sezione per visualizzare ulteriori informazioni su bulloni specifici.

* **Configurazione della topologia**: configurazione della topologia selezionata.

### <a name="spout-and-bolt-summary"></a>Beccuccio e bulloni riepilogo

Selezione di un beccuccio dalla sezione **Spouts** o **bulloni** vengono visualizzate le informazioni seguenti sull'elemento selezionato:

* **Componente riepilogo**: informazioni di base sulla beccuccio o bulloni.

* **Stat beccuccio/bulloni**: statistiche sulla beccuccio o bulloni. Utilizzare i collegamenti nella colonna della **finestra** per impostare il periodo di tempo per le voci rimanenti nella pagina.

* **Statistiche di input** (solo bullone): informazioni sui flussi di input consumata dai bulloni.

* **Statistiche di output**: informazioni sui flussi emesso da questo spout o bullone.

* **Esecutori**: informazioni sulle istanze del beccuccio o bulloni. Selezionare la voce di **porta** per un esecutore specifico visualizzare un elenco delle informazioni di diagnostica per questa istanza.

* **Errori**: le informazioni sugli errori per questo spout o bullone.

## <a name="rest-api"></a>API REST

L'interfaccia utente eccesso è basato su API REST, per poter eseguire Gestione e il monitoraggio delle funzionalità tramite l'API REST simili. È possibile utilizzare l'API REST per creare strumenti personalizzati per la gestione e monitoraggio topologie eccesso.

Per ulteriori informazioni, vedere [API REST di eccesso dell'interfaccia utente](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Le informazioni seguenti sono specifiche per l'utilizzo di API REST con eccesso Apache su HDInsight.

> [AZURE.IMPORTANT] API REST eccesso non è disponibile al pubblico su internet e deve avvenire utilizzando un tunnel SSH nodo principale cluster HDInsight. Per informazioni sulla creazione e utilizzo di un tunnel SSH, vedere [Usare SSH connettersi accesso web Ambari dell'interfaccia utente, ResourceManager, JobHistory, NameNode, Oozie e altre web dell'interfaccia utente](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI base

URI di base per l'API REST nei cluster basati su Linux HDInsight è disponibile sul nodo principale in **https://HEADNODEFQDN:8744/api/v1/**; Tuttavia, il nome di dominio del nodo principale viene generato durante la creazione di cluster e non statico.

È possibile trovare il nome di dominio completo (FQDN) per il nodo principale cluster in molti modi diversi:

* __Da un SSH sessione__: usare il comando `headnode -f` da una sessione SSH al cluster.

* __Dal Ambari Web__: selezionare __i servizi__ nella parte superiore della pagina, quindi selezionare __eccesso__. Nella scheda __Riepilogo__ selezionare __Il Server di interfaccia utente__. Il nome di dominio completo del nodo che l'interfaccia utente di eccesso e API REST è in esecuzione su sarà nella parte superiore della pagina.

* __Da API REST Ambari__: usare il comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` per recuperare le informazioni relative al nodo dell'interfaccia utente di eccesso e API REST in esecuzione nel. Sostituire __PASSWORD__ con la password di amministratore per il cluster. Sostituire __nome cluster__ con il nome del cluster. In risposta, la voce "host_name" contiene il nome di dominio completo del nodo.

### <a name="authentication"></a>Autenticazione

Richieste di assistenza all'API REST necessario utilizzare **l'autenticazione di base**, in modo che si usa il nome dell'amministratore cluster HDInsight e la password.

> [AZURE.NOTE] Poiché l'autenticazione di base viene inviato tramite testo non crittografato, è necessario utilizza **sempre** HTTPS per proteggere le comunicazioni con i cluster.

### <a name="return-values"></a>Valori restituiti

I dati restituiti dall'API REST possono essere solo utilizzabili da all'interno del cluster o macchine virtuali di Azure della stessa rete cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per server Zookeeper non essere accessibile da Internet.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come distribuire e monitorare topologie utilizzando il Dashboard eccesso, informazioni su come [topologie basato su Java sviluppare utilizzando Maven](hdinsight-storm-develop-java-topology.md).

Per un elenco di altre topologie di esempio, vedere [topologie di esempio per eccesso in HDInsight](hdinsight-storm-example-topology.md).
