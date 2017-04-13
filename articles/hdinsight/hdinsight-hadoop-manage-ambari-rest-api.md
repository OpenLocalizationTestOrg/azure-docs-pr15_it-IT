<properties
   pageTitle="Monitorare e gestire i cluster HDInsight tramite l'API REST di Apache Ambari | Microsoft Azure"
   description="Informazioni su come utilizzare Ambari per monitorare e gestire i cluster basati su Linux HDInsight. In questo documento si imparerà a utilizzare l'API REST Ambari incluso con i cluster HDInsight."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gestire i cluster HDInsight tramite l'API REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari semplifica la gestione e il controllo di un cluster Hadoop fornendo un semplice utilizzare l'interfaccia utente web e API REST. Ambari è incluso nei cluster basati su Linux HDInsight e viene utilizzata per monitorare il cluster e apportare le modifiche alla configurazione. In questo documento informazioni di base dell'uso di API REST Ambari eseguendo attività comuni di utilizzo curvatura.

##<a name="prerequisites"></a>Prerequisiti

* [voltare](http://curl.haxx.se/): curvatura è un'utilità multipiattaforma che può essere usata per lavorare con le API REST dalla riga di comando. In questo documento, viene utilizzato per comunicare con l'API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq è un'utilità della riga di comando multipiattaforma per l'utilizzo di documenti JSON. In questo documento, viene utilizzato per analizzare i documenti JSON restituiti dall'API REST Ambari.
* [Azure CLI](../xplat-cli-install.md): un'utilità della riga di comando multipiattaforma per l'uso con i servizi di Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>Che cos'è Ambari?

[Apache Ambari](http://ambari.apache.org) semplice gestione Hadoop fornendo un web di semplice utilizzo dell'interfaccia utente che può essere utilizzato per eseguire il provisioning, gestire e monitorare i cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle applicazioni tramite le [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari viene fornito con i cluster basati su Linux HDInsight per impostazione predefinita.

##<a name="rest-api"></a>API REST

URI di base per l'API REST Ambari in HDInsight è https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, dove __nome cluster__ è il nome del cluster.

> [AZURE.IMPORTANT] Con distinzione tra maiuscole e il nome del cluster nella parte nome di dominio completo (FQDN) di nome di URI (CLUSTERNAME.azurehdinsight.net), ricorrenze nell'URI sono maiuscole e minuscole. Ad esempio, se il cluster è denominato miocluster, di seguito sono URI validi:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> I seguenti URI restituito un errore perché la seconda occorrenza del nome non è lettere maiuscole o minuscole.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

La connessione a Ambari in HDInsight richiede HTTPS. Durante l'autenticazione la connessione, è necessario utilizzare il nome di account di amministratore (il valore predefinito è __amministratore__) e la password forniti quando il cluster è stato creato.

Nell'esempio seguente usa curvatura per effettuare una richiesta GET rispetto all'API REST. Sostituire __PASSWORD__ con la password di amministratore per il cluster. Sostituire __nome cluster__ con il nome del cluster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
La risposta è un documento JSON che inizia con informazioni simili a quanto segue:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Poiché si tratta di JSON, è più semplice utilizzare un parser JSON per lavorare con i dati. Ad esempio, nell'esempio seguente viene utilizzato jq per visualizzare solo i `health_report` elemento.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Esempio: Ottenere il nome di dominio completo dei nodi cluster

Quando si utilizzano HDInsight, è necessario conoscere il nome di dominio completo (FQDN) di un nodo cluster. È possibile recuperare facilmente il FQDN per diversi nodi cluster utilizzando le operazioni seguenti:

* __I nodi di testa__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __I nodi di lavoro__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __I nodi zookeeper__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Nota ognuno di questi esempi seguono lo stesso modello:

1. Esecuzione di query un componente che è possibile sapere su tali nodi.
2. Recuperare la `host_name` elementi, che contengono il FQDN per questi nodi.

Il `host_components` elemento del documento restituito contiene più elementi. Utilizzo `.host_components[]`, e quindi specificare un percorso all'interno dell'elemento viene scorrere ogni elemento ed estrarre il valore il percorso specifico. Se si vuole solo un valore, ad esempio prima immissione del nome di dominio completo, è possibile tornare gli elementi come insieme e quindi selezionare una voce specifica:

    jq '[.host_components[].HostRoles.host_name][0]'

Restituisce il FQDN prima dalla raccolta.

##<a name="example-get-the-default-storage-account-and-container"></a>Esempio: Ottenere l'account di archiviazione predefinito e di un contenitore

Quando si crea un cluster di HDInsight, è necessario utilizzare un Account di archiviazione di Azure e un contenitore di blob come spazio di archiviazione predefinito per il cluster. È possibile utilizzare Ambari per recuperare questa informazione dopo aver creato il grafico. Ad esempio, se si desidera a livello di programmazione scrivere dati direttamente al contenitore.

Di seguito verrà recuperato URI WASB dello spazio di archiviazione predefinito cluster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Restituisce la prima configurazione applicata al server (`service_config_version=1`,) che contiene le informazioni. Se si recupera un valore che è stato modificato dopo la creazione di cluster, potrebbe essere necessario elencare le versioni di configurazione e recuperare quello più recente.

Restituisce un valore simili all'esempio seguente, dove __contenitore__ è il contenitore predefinito e __nome account__ è il nome dell'Account di archiviazione di Azure:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

È quindi possibile utilizzare queste informazioni con [Azure CLI](../xplat-cli-install.md) per il caricamento o il download dei dati dal contenitore.

1. È possibile ottenere il gruppo di risorse per l'Account di archiviazione. Sostituire __nome account__ con il nome dell'Account di archiviazione recuperato Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Restituisce il nome del gruppo di risorse per l'account.
    
    > [AZURE.NOTE] Se non viene restituito da questo comando, potrebbe essere necessario modificare CLI Azure alla modalità di gestione di risorse Azure ed eseguire nuovamente il comando. Per passare alla modalità di gestione risorse di Azure, usare il comando seguente:
    >
    > `azure config mode arm`
    
2. È possibile ottenere il tasto per l'account di archiviazione. Sostituire __NOMEGRUPPO__ con il gruppo di risorse del passaggio precedente. Sostituire __nome account__ con il nome dell'Account di archiviazione:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Questo esempio viene restituita la chiave primaria per l'account.
    
3. Usare il comando Carica per archiviare un file nel contenitore:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Sostituire __nome account__ con il nome dell'Account di archiviazione. Sostituire __ACCOUNTKEY__ insieme al tasto recuperato in precedenza. __Percorso file__ è il percorso del file da caricare, mentre __BLOBPATH__ è il percorso tra il contenitore.

    Ad esempio, se si desidera il file deve essere visualizzato in HDInsight nella wasbs://example/data/filename.txt, __BLOBPATH__ sarà `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Esempio: Aggiornamento Ambari configurazione

1. Ottenere la configurazione corrente, Ambari Archivia come "configurazione desiderata":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Questo esempio viene restituito un documento JSON contenente la configurazione corrente (identificata dal valore _tag_ ) per i componenti installati nel cluster. Nell'esempio seguente è un estratto i dati restituiti da un tipo di grafico ad.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Da questo elenco, è necessario copiare il nome del componente (ad esempio, __ad\_thrift\_sparkconf__ e il valore di __tag__ .
    
2. Recuperare la configurazione per il componente e tag utilizzando il comando seguente. Sostituire __i-thrift-sparkconf__ e __iniziale__ con il componente e un tag che si desidera recuperare la configurazione per.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curvatura recupera il documento JSON, quindi jq viene utilizzato per apportare modifiche ai dati per creare un modello. Il modello viene quindi utilizzato per aggiungere o modificare i valori di configurazione. In particolare esegue le operazioni seguenti:
    
    * Crea un valore univoco che contiene la stringa "versione" e la data, archiviata in __newtag__.
    * Crea un documento principale per la nuova configurazione desiderata.
    * Ottenere i contenuti della `.items[]` matrice e lo aggiunge sotto l'elemento __desired_config__ .
    * Elimina __con sottoindirizzo__, __versione__e gli elementi di __configurazione__ , come questi elementi non sono necessarie per l'invio di una nuova configurazione.
    * Aggiunge un nuovo elemento __tag__ e il relativo valore su __versione # # #__. La parte numerica è in base alla data corrente. Ogni configurazione dovrà avere un identificatore univoco.
    
    Infine, i dati vengono salvati nel documento __newconfig.json__ . La struttura del documento dovrebbe essere simile al seguente:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Aprire i valori di documento e modificare/aggiungere __newconfig.json__ nell'oggetto di __proprietà__ . Nell'esempio seguente viene modificato il valore di __"spark.yarn.am.memory"__ da __"g 1"__ a __"3G"__ e e aggiunge un nuovo elemento per __"spark.kryoserializer.buffer.max"__ con un valore di __"m 256"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Una volta apportate le modifiche, salvare il file.

4. Utilizzare il comando seguente per inviare la configurazione aggiornata da Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Questo comando Invia il contenuto del file __newconfig.json__ alla richiesta di curvatura, che invia il cluster come nuova configurazione desiderata. La richiesta di curvatura restituisce un documento JSON. L'elemento __versionTag__ in questo documento deve corrispondere alla versione che è stata inviata e l'oggetto __configurazioni__ conterrà le modifiche alla configurazione richiesto.

###<a name="example-restart-a-service-component"></a>Esempio: Riavviare un servizio

A questo punto, se osserva il web Ambari dell'interfaccia utente, il servizio di motori indica che è necessario riavviare la nuova configurazione per rendere effettive. Utilizzare la procedura seguente per riavviare il servizio.

1. Per attivare la modalità di manutenzione per il servizio di motori, utilizzare il seguente:

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Questo comando invia un documento JSON al server (contenute nel `echo` istruzione,) in modo da attivare la modalità di manutenzione.
    È possibile verificare che il servizio è in modalità di manutenzione utilizzando la richiesta seguente:
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Verrà restituito il valore `"ON"`.

3. Per disattivare il servizio successivamente, utilizzare le operazioni seguenti:

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Questo comando restituisce una risposta simile al seguente.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    Il `href` valore restituito da questo URI utilizza l'indirizzo IP interno del nodo cluster. Per utilizzare da all'esterno del cluster, sostituire la parte '10.0.0.18:8080' con il nome di dominio completo del cluster. Ad esempio, il comando seguente recupera lo stato della richiesta.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Se viene restituito un valore di `"COMPLETED"` quindi terminata la richiesta.

4. Una volta completata la richiesta precedente, utilizzare le operazioni seguenti per avviare il servizio.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Dopo il riavvio del servizio, si tratta di nuove impostazioni di configurazione.

5. Infine, utilizzare le operazioni seguenti per disattivare la modalità di manutenzione.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Passaggi successivi

Per un riferimento completo dell'API REST, vedere [Ambari API riferimento V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Alcune funzionalità di Ambari è disattivato, come viene gestita dal servizio cloud HDInsight; ad esempio, aggiungere o rimuovere host dal cluster o aggiunta di nuovi servizi.
