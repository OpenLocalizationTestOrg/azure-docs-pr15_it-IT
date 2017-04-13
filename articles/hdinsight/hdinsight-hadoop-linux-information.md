<properties
   pageTitle="Suggerimenti per l'uso di Hadoop su basati su Linux HDInsight | Microsoft Azure"
   description="È possibile ottenere suggerimenti sull'implementazione per l'uso di cluster basati su Linux HDInsight (Hadoop) in un ambiente Linux familiare in esecuzione nel cloud Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Informazioni sull'utilizzo di HDInsight su Linux

Cluster di Azure HDInsight basati su Linux offrono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud Azure. Per la maggior parte delle operazioni, funziona esattamente come qualsiasi altra installazione Hadoop su Linux. In questo documento vengono indicati specifiche differenze da tenere presenti.

##<a name="prerequisites"></a>Prerequisiti

Molti i passaggi descritti in questo documento vengono utilizzati utilità seguenti, che potrebbe essere necessario installato nel computer.

* [voltare](https://curl.haxx.se/) - utilizzato per comunicare con i servizi basati sul web
* [jq](https://stedolan.github.io/jq/) - utilizzato per analizzare i documenti JSON
* [Azure CLI](../xplat-cli-install.md) - utilizzato per gestire i servizi di Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Nomi di dominio

Il nome di dominio completo (FQDN) da utilizzare quando ci si connette al cluster da internet è ** &lt;nome cluster >. azurehdinsight.net** o (per SSH) ** &lt;nome cluster-ssh >. azurehdinsight.net**.

Internamente, ogni nodo del cluster ha un nome che viene assegnato durante la configurazione cluster. Per trovare i nomi di cluster, visitare la pagina __host__ dell'interfaccia utente Web Ambari o per restituire un elenco di host dall'API REST Ambari utilizzare le operazioni seguenti:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Sostituire __PASSWORD__ con la password dell'account di amministratore e __nome cluster__ con il nome del cluster. Verrà restituito un documento JSON che contiene un elenco di host del cluster, quindi jq estrae il `host_name` valore dell'elemento per ogni host del cluster.

Se è necessario trovare il nome del nodo di un servizio specifico, è possibile richiedere Ambari per tale componente. Ad esempio, per trovare l'host del nodo di nome HDFS, utilizzare le operazioni seguenti.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Restituisce un documento JSON che descrive il servizio e quindi jq estrae solo il `host_name` valore per l'host.

## <a name="remote-access-to-services"></a>Accesso remoto ai servizi

* **Ambari (web)** - https://&lt;nome cluster >. azurehdinsight.net

    Eseguire l'autenticazione mediante l'utente amministratore cluster e la password e quindi accedere al Ambari. Utilizza inoltre l'utente amministratore cluster e la password.

    L'autenticazione in testo normale: Usa sempre HTTPS per garantire la sicurezza della connessione.

    > [AZURE.IMPORTANT] Sebbene Ambari per il cluster è accessibile direttamente tramite Internet, alcune funzionalità si basa sull'accesso ai nodi tramite il nome di dominio interno utilizzato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, viene visualizzato "server non trovato" quando si tenta di accedere a alcune caratteristiche via Internet.
    >
    > Per usare le funzionalità complete di web Ambari dell'interfaccia utente, utilizzare un tunnel SSH per inoltrare il traffico web per il nodo principale cluster. Vedere [Usare SSH connettersi accesso web Ambari dell'interfaccia utente, ResourceManager, JobHistory, NameNode, Oozie e altre web dell'interfaccia utente](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;nome cluster >.azurehdinsight.net/ambari

    > [AZURE.NOTE] Eseguire l'autenticazione mediante l'utente amministratore cluster e la password.
    >
    > L'autenticazione in testo normale: Usa sempre HTTPS per garantire la sicurezza della connessione.

* **WebHCat (Templeton)** - https://&lt;nome cluster >.azurehdinsight.net/templeton

    > [AZURE.NOTE] Eseguire l'autenticazione mediante l'utente amministratore cluster e la password.
    >
    > L'autenticazione in testo normale: Usa sempre HTTPS per garantire la sicurezza della connessione.

* **SSH** - &lt;nome cluster >-ssh.azurehdinsight.net sulla porta 22 o 23. Porta 22 viene utilizzata per connettersi a headnode principale, mentre 23 viene utilizzato per connettersi al server secondario. Per ulteriori informazioni sulla sede nodi, vedere [disponibilità e l'affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] È possibile accedere a solo i nodi di testa cluster tramite SSH da un computer client. Una volta connessa, è possibile accedere quindi i nodi di lavoro usando SSH da un headnode.

## <a name="file-locations"></a>Percorsi dei file

File Hadoop correlati sono disponibili nei nodi del cluster in `/usr/hdp`. Questa directory contiene le sottodirectory seguenti:

* __2.2.4.9-1__: questa directory è denominata per la versione della piattaforma dati Hortonworks utilizzato da HDInsight, in modo che il numero il cluster potrebbe essere diverso da quelli elencati di seguito.
* __corrente__: questa directory contiene collegamenti alle directory nella directory __2.2.4.9-1__ ed è presente in modo che non è necessario digitare un numero di versione (che potrebbero essere modificati,) ogni volta che si desidera accedere a un file.

Dati di esempio e file VASO sono disponibili in archiviazione Blob Azure o Hadoop Distributed File System (HDFS) ' / esempio ' o ' wasbs: / / / esempio '.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, archiviazione Blob Azure e procedure consigliate di archiviazione

Nella maggior parte delle distribuzioni Hadoop, HDFS è supportato da archivio locale nel computer del cluster. Mentre si tratta in modo efficiente, può essere costosa per una soluzione basata su cloud in cui viene effettuato ogni ora o minuti per le risorse di calcolo.

HDInsight utilizza archiviazione Blob Azure come archivio predefinito, che offre i seguenti vantaggi:

* Spazio di archiviazione a lungo termine economico

* Accessibilità da servizi esterni come siti Web, utilità di caricamento o download di file, vari SDK lingua e web browser

Poiché l'archivio predefinito per HDInsight, in genere non è necessario eseguire alcuna operazione per usarlo. File nella cartella **/example/data** archiviati in archiviazione Blob Azure verrà elencati, ad esempio, il comando seguente:

    hdfs dfs -ls /example/data

Alcuni comandi potrebbero essere necessario specificare che si sta utilizzando archiviazione Blob. Per questo motivo, è possibile anteporre il comando con **wasb: / /**, o **wasbs: / /**.

HDInsight consente anche di associare un cluster di più account di archiviazione Blob. Per accedere ai dati in un account di archiviazione Blob non predefinito, è possibile utilizzare il formato **wasbs: / /&lt;container-name>@&lt;nome account >.blob.core.windows.net/**. Ad esempio, le operazioni seguenti verranno elencati il contenuto della directory **/example/data** per il contenitore specificato e l'account di archiviazione Blob:

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Quali archiviazione Blob è utilizzando il cluster?

Durante la creazione di cluster è scelto di usare un account di archiviazione Azure esistente e contenitore o crearne uno nuovo. Quindi, è probabile che sia stata dimenticata affrontarlo. È possibile trovare l'account predefinito di spazio di archiviazione e contenitore tramite l'API REST Ambari.

1. Utilizzare il comando seguente per recuperare informazioni di configurazione HDFS tramite curvatura e filtrarli utilizzando [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Verrà restituito prima configurazione applicata al server (`service_config_version=1`,) che conterrà queste informazioni. Se si desidera recuperare un valore che è stato modificato dopo la creazione di cluster, potrebbe essere necessario elencare le versioni di configurazione e recuperare quello più recente.

    Verrà restituito un valore simile al seguente, dove __contenitore__ è il contenitore predefinito e __nome account__ è il nome dell'Account di archiviazione di Azure:

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Ottenere il gruppo di risorse per l'Account di archiviazione, usare [CLI Azure](../xplat-cli-install.md). Il comando seguente, sostituire __nome account__ con il nome dell'Account di archiviazione recuperato Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Verrà restituito il nome del gruppo di risorse per l'account.
    
    > [AZURE.NOTE] Se non viene restituito da questo comando, potrebbe essere necessario modificare CLI Azure alla modalità di gestione di risorse Azure ed eseguire nuovamente il comando. Per passare alla modalità di gestione risorse di Azure, usare il comando seguente.
    >
    > `azure config mode arm`
    
2. È possibile ottenere il tasto per l'account di archiviazione. Sostituire __NOMEGRUPPO__ con il gruppo di risorse del passaggio precedente. Sostituire __nome account__ con il nome dell'Account di archiviazione:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Restituisce la chiave primaria per l'account.

È anche possibile trovare le informazioni dello spazio di archiviazione tramite il portale di Azure:

1. Nel [Portale di Azure](https://portal.azure.com/), selezionare il cluster HDInsight.

2. Nella sezione __Nozioni di base__ , selezionare __tutte le impostazioni__.

3. In __Impostazioni__selezionare __Chiavi per l'archiviazione Azure__.

4. __Chiavi per l'archiviazione Azure__, selezionare uno degli account di archiviazione elencati. Informazioni sull'account di archiviazione verranno visualizzati.

5. Selezionare l'icona chiave. Verranno visualizzati i tasti per l'account di archiviazione.

### <a name="how-do-i-access-blob-storage"></a>Come si accede a archiviazione Blob?

Diverso da tramite il comando Hadoop dal cluster, esistono diversi modi per accedere a BLOB:

* [Azure CLI per Mac, Linux e Windows](../xplat-cli-install.md): comandi di interfaccia della riga di comando per l'utilizzo di Azure. Dopo l'installazione, utilizzare la `azure storage` comando per informazioni sull'utilizzo di archiviazione, o `azure blob` per i comandi specifici di blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un python script per l'utilizzo di BLOB di archiviazione Azure.

* Una varietà di SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Trascrizione](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST di spazio di archiviazione](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Ridimensionamento il cluster

Cluster proporzioni dei caratteri caratteristica consente di modificare il numero dei nodi di dati utilizzato da un cluster che è in esecuzione in Azure HDInsight senza eliminare e ricreare il cluster.

È possibile eseguire le operazioni di ridimensionamento mentre altri processi o processi sono in esecuzione in un cluster.

I tipi di grafico diverso dipendono dal ridimensionamento come indicato di seguito:

* __Hadoop__: in caso di ridimensionamento verso il basso il numero dei nodi in un cluster, alcuni dei servizi del cluster, è necessario riavviare. In questo modo processi in esecuzione o in sospeso in fase di completamento dell'operazione di ridimensionamento. È possibile inviare nuovamente i processi una volta completata l'operazione.

* __HBase__: server regionali vengono automaticamente bilanciato entro pochi minuti dopo il completamento dell'operazione di ridimensionamento. Per bilanciare manualmente server regionali, utilizzare la procedura seguente:

    1. Connettersi a cluster HDInsight utilizzando SSH. Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere uno dei seguenti documenti:

        * [Usare SSH con HDInsight da Linux, Unix e Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Usare SSH con HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Per avviare la shell HBase, utilizzare le operazioni seguenti:

            hbase shell

    2. Dopo aver caricato shell HBase, utilizzare la seguente riconciliare manualmente il server regionali:

            balancer

* __Eccesso__: È consigliabile ribilanciare qualsiasi topologie eccesso in esecuzione dopo aver eseguita un'operazione di ridimensionamento. In questo modo la topologia di regolare le impostazioni di parallelismo in base al nuovo numero dei nodi del cluster. Per effettuare topologie in esecuzione, utilizzare una delle opzioni seguenti:

    * __SSH__: la connessione al server e usare il comando seguente per ribilanciare una topologia:

            storm rebalance TOPOLOGYNAME

        È anche possibile specificare parametri per ignorare i suggerimenti di parallelismo originariamente forniti da della topologia. Ad esempio `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` verrà riconfigurare la topologia 5 processi di lavoro, 3 esecutori per il componente beccuccio blu e 10 esecutori per il componente giallo bulloni.

    * __Interfaccia utente eccesso__: utilizzare la procedura seguente per ribilanciare una topologia tramite l'interfaccia utente eccesso.

        1. Aprire __https://CLUSTERNAME.azurehdinsight.net/stormui__ nel web browser, dove nome cluster è il nome del cluster eccesso. Se richiesto, immettere il nome dell'amministratore (amministratore) cluster HDInsight e la password specificate durante la creazione del cluster.

        3. Selezionare la topologia che si desidera ribilanciare, quindi selezionare il pulsante __ribilanciare__ . Immettere il ritardo prima che venga eseguita l'operazione ribilanciamento.

Per informazioni specifiche sulle proporzioni dei caratteri il cluster HDInsight, vedere:

* [Gestire i cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gestire i cluster Hadoop in HDinsight tramite PowerShell Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Come installare tonalità (o altro componente Hadoop)?

HDInsight è un servizio gestito, ovvero nodi in un cluster potrebbero essere eliminati e di nuovo automaticamente da Azure se viene rilevato un problema. Per questi motivi, non è consigliabile installare manualmente elementi direttamente nei nodi del cluster. Utilizzare invece [Le azioni di Script HDInsight](hdinsight-hadoop-customize-cluster.md) quando è necessario installare le operazioni seguenti:

* Un servizio o un sito web, ad esempio motori o tonalità.
* Componente che richiede modifiche alla configurazione in più nodi del cluster. Ad esempio una variabile di ambiente necessarie, la creazione di una directory di registrazione o la creazione di un file di configurazione.

Azioni di script sono Bash gli script eseguiti durante il provisioning di cluster e possono essere utilizzate per installare e configurare i componenti aggiuntivi sul cluster. Script di esempio sono disponibili per l'installazione di componenti seguenti:

* [Tonalità](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Per informazioni sullo sviluppo delle Script azioni personalizzate, vedere [sviluppo di Script azione con HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>File vaso

Vengono fornite alcune tecnologie Hadoop nei file di vaso indipendente contenenti funzioni utilizzate come parte di un processo MapReduce o da all'interno di maialino o Hive. Mentre è possibile installarli utilizzo di azioni di Script, vengono spesso non è richiesta alcuna impostazione possibile appena caricati al cluster dopo il provisioning ed e utilizzate direttamente. Se si desidera che il componente viene conservata ricostruzione del cluster makle, è possibile archiviare il file vaso in WASB.

Ad esempio, se si desidera utilizzare l'ultima versione di [DataFu](http://datafu.incubator.apache.org/), è possibile scaricare un vaso contenente il progetto e caricarlo in cluster HDInsight. Seguire la documentazione DataFu su come utilizzare da maialino o Hive.

> [AZURE.IMPORTANT] Componenti di file vaso autonomi forniti con HDInsight, ma non si trovano sul percorso. Se si sta cercando un componente specifico, è possibile utilizzare il completamento per cercare il cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Verrà restituito il percorso del file vaso corrispondente.

Se il cluster offre già una versione di un componente come un file di vaso autonomo, ma si desidera utilizzare una versione diversa, è possibile caricare una nuova versione del componente al cluster e provare a utilizzare il tipo di attività.

> [AZURE.WARNING] Componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/), [motori](http://spark.apache.org/).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la migrazione da HDInsight basato su Windows a basati su Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
