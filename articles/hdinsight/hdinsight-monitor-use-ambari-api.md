<properties
    pageTitle="Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari | Microsoft Azure"
    description="Utilizzo delle Apache Ambari APIs per creare, gestire e monitoraggio dei cluster Hadoop. API e strumenti di operatore intuitivo nascondere la complessità della Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari

Informazioni su come eseguire il monitoraggio cluster HDInsight utilizzando Ambari APIs.

> [AZURE.NOTE] Le informazioni in questo articolo riguarda principalmente cluster HDInsight basato su Windows, fornire una versione di sola lettura dell'API REST Ambari. Per cluster basati su Linux, vedere [gestire Hadoop cluster utilizzando Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>Che cos'è Ambari?

[Apache Ambari] [ ambari-home] viene utilizzato per il provisioning, la gestione e monitoraggio dei cluster Apache Hadoop. Include un insieme di strumenti di operatore intuitivo e un set di API che nascondere la complessità della Hadoop, semplificando l'operazione di cluster efficaci. Per ulteriori informazioni sulle API, vedere [Informazioni di riferimento API Ambari][ambari-api-reference]. 

HDInsight supporta al momento solo la funzionalità di monitoraggio Ambari. Ambari API 1.0 è supportata dagli cluster con versioni 3.0 e 2.1 HDInsight. In questo articolo è descritta accesso APIs Ambari sui cluster versione 3.1 e 2.1 HDInsight. La differenza tra i due fondamentale è alcuni dei componenti sono stati modificati con l'introduzione di nuove funzionalità (ad esempio il Server di cronologia dei processi). 

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Facoltativo) [voltare] [curl]. Per eseguire l'installazione, vedere [voltare versioni e download][curl-download].

    >[AZURE.NOTE] Quando usare il comando curvatura in Windows, utilizzare virgolette doppie anziché virgolette singole per i valori di opzione.

- **Cluster di Azure HDInsight**. Per istruzioni su cluster di provisioning, vedere [Introduzione all'utilizzo di HDInsight] [ hdinsight-get-started] o [cluster di provisioning HDInsight][hdinsight-provision]. È necessario disporre i dati seguenti per accedere all'interno dell'esercitazione:

    Proprietà cluster|Nome della variabile PowerShell Azure|Valore|Descrizione
    ---|---|---|---
    Nome cluster HDInsight|$clusterName||Il nome del cluster HDInsight.
    Nome utente cluster|$clusterUsername||Nome utente cluster specificato quando è stato creato il grafico.
    Password cluster|$clusterPassword||Password dell'utente cluster.

    >[AZURE.NOTE] Fill-nei valori della tabella. Questo sarà utile per l'interfaccia in questa esercitazione.

## <a name="jump-start"></a>Per iniziare

Esistono diversi modi per usare Ambari per monitorare i cluster HDInsight.

**Usare PowerShell Azure**

Di seguito è riportato un script di PowerShell Azure per ottenere le informazioni sul registro processo MapReduce *in un cluster 3.1 HDInsight.*  La differenza fondamentale è è estrarre i dettagli nel servizio filati (anziché MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Di seguito è riportato un script di PowerShell Azure per ottenere il MapReduce processo registro delle informazioni *in un cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

L'output è:

![Jobtracker Output][img-jobtracker-output]

**Utilizzare curvatura**

Di seguito è illustrato un esempio di recupero di informazioni cluster utilizzando curvatura:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

L'output è:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Per 10/8/2014 versione**:

Quando si utilizza l'endpoint Ambari "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host_name* restituisce il nome di dominio completo (FQDN) del nodo anziché il nome host. Prima del rilascio di 8/10/2014, in questo esempio viene restituito semplicemente "**headnode0**". Dopo la pubblicazione di 8/10/2014, viene visualizzato il nome di dominio completo "**headnode0. { ClusterDNS} .azurehdinsight .net**", come illustrato nell'esempio precedente. Questa modifica è stata necessaria per agevolare gli scenari in cui più tipi di grafico (ad esempio HBase e Hadoop) possono essere distribuiti in un'unica rete virtuale (VNET). Si verifica questo evento, ad esempio, quando si utilizza HBase come piattaforma back-end per Hadoop.

## <a name="ambari-monitoring-apis"></a>API di monitoraggio delle Ambari

Nella tabella seguente sono elencati alcuni dei Ambari più comuni monitoraggio chiamate API. Per ulteriori informazioni sull'API, vedere [Informazioni di riferimento API Ambari][ambari-api-reference].

Monitor API chiamata|URI|Descrizione
---|---|---
Ottenere cluster|`/api/v1/clusters`|
È possibile ottenere informazioni cluster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|cluster, servizi, host
Richiedere servizi|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Servizi includono: hdfs, mapreduce
È possibile ottenere informazioni di servizi.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Ottenere i componenti del servizio|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
È possibile ottenere informazioni sui componenti.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo componenti di host, metriche
Ottenere host|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
È possibile ottenere informazioni host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Ottenere componenti host|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Ottenere informazioni sui componenti host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles component, host, metriche
Ottenere le configurazioni|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Tipi di configurazione: dei siti principali, siti hdfs, mapred sito, sito hive
È possibile ottenere le informazioni di configurazione.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Tipi di configurazione: dei siti principali, siti hdfs, mapred sito, sito hive


##<a name="next-steps"></a>Passaggi successivi

A questo punto sono appreso come utilizzare Ambari monitoraggio chiamate API. Per ulteriori informazioni, vedere:

- [Gestire i cluster HDInsight tramite il portale di Azure][hdinsight-admin-portal]
- [Gestire i cluster HDInsight tramite PowerShell Azure][hdinsight-admin-powershell]
- [Gestire i cluster HDInsight tramite l'interfaccia della riga di comando][hdinsight-admin-cli]
- [Documentazione HDInsight][hdinsight-documentation]
- [Guida introduttiva a HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
