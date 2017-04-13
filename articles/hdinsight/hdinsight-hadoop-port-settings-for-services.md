<properties
pageTitle="Porte utilizzate da HDInsight | Azure"
description="Elenco di porte utilizzate dai servizi Hadoop in esecuzione su HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Porte e protocolli URI utilizzato da HDInsight

Questo documento offre un elenco delle porte utilizzate dai servizi Hadoop in esecuzione su cluster basati su Linux HDInsight. Vengono inoltre fornite informazioni sulle porte utilizzate per connettersi al cluster tramite SSH.

## <a name="public-ports-vs-non-public-ports"></a>Porte pubbliche e porte non pubblici

Cluster HDInsight basati su Linux espone solo tre porte al pubblico su internet. 22, 23 e 443. Questi vengono utilizzati per accedere in modo sicuro il cluster utilizzando SSH e servizi esposti tramite il protocollo HTTPS protetto.

Internamente, HDInsight è implementata da diverse macchine virtuali di Azure (i nodi all'interno del cluster) in esecuzione in una rete virtuale Azure. All'interno della rete virtuale, è possibile accedere porte non viene esposte su internet. Ad esempio, se ci si connette a uno dei nodi di testa utilizzando SSH, dal nodo principale è possibile quindi accedere direttamente services in esecuzione nei nodi del cluster.

> [AZURE.IMPORTANT] Quando si crea un cluster di HDInsight, se non si specifica una rete virtuale Azure come opzione di configurazione, verrà creato uno; Tuttavia, è possibile collegare altri computer (ad esempio altre macchine virtuali di Azure o computer di sviluppo client) a questa rete virtuale è stata creata. 

Per aggiungere ulteriori computer alla rete virtuale, creare la rete virtuale prima di tutto, quindi specificarlo quando si creano i cluster HDInsight. Per ulteriori informazioni, vedere [funzionalità estendere HDInsight tramite una rete virtuale Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Porte pubbliche

Tutti i nodi in un cluster di HDInsight si trovano in una rete virtuale Azure e non possono accedere direttamente da internet. Un gateway pubblico offre accesso a internet per le seguenti porte, sono comuni a tutti i tipi di grafico HDInsight.

| Servizio | Porta | Protocollo | Descrizione |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Connette client sshd nella headnode principale. Vedere [usare SSH con basati su Linux HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Connette client sshd del nodo di bordo (solo HDInsight Premium). Vedere [Introduzione all'utilizzo di Server R HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Connette client sshd su headnode secondario. Vedere [usare SSH con basati su Linux HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interfaccia utente web Ambari. Vedere [gestire HDInsight tramite l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API REST Ambari. Vedere [gestire HDInsight tramite l'API REST di Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | API REST HCatalog. Vedere [usare Hive latino](hdinsight-hadoop-use-pig-curl.md), [utilizzare maialino latino](hdinsight-hadoop-use-pig-curl.md), [MapReduce latino](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Si connette all'Hive utilizzando ODBC. Vedere [Connettere Excel a HDInsight con il driver ODBC Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Si connette all'Hive utilizzando JDBC. Vedere [connettersi a Hive nel HDInsight utilizzando il driver JDBC Hive](hdinsight-connect-hive-jdbc-driver.md) |

Di seguito sono disponibili per i tipi di grafico specifico:

| Servizio | Porta | Protocollo |Tipo di grafico | Descrizione |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API REST HBase. Vedere [Introduzione all'utilizzo di HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Inserire il | 443 | HTTPS |  Motori | API REST motori. Vedere [inviare i processi in modalità remota utilizzando inserire il](hdinsight-apache-spark-livy-rest-interface.md) |
| Eccesso | 443 | HTTPS | Eccesso | Interfaccia utente web eccesso. Vedere [Distribuisci e gestire topologie eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Autenticazione

Tutti i servizi pubblicamente esposti su internet devono essere autenticati:

| Porta | Credenziali |
| ---- | ----------- |
| 22 o 23 | Le credenziali utente SSH specificate durante la creazione di cluster |
| 443 | Il nome di accesso (impostazione predefinita: l'amministratore) e la password che sono stati impostati durante la creazione di cluster |

## <a name="non-public-ports"></a>Porte non pubblici

> [AZURE.NOTE] Sono disponibili i tipi di grafico specifico solo alcuni servizi. Ad esempio HBase è disponibile solo nei tipi di grafico HBase.

### <a name="hdfs-ports"></a>Porte HDFS

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- | 
| Interfaccia utente web NameNode | Nodi di testa | 30070 | HTTPS | Interfaccia utente per visualizzare lo stato corrente Web |
| Servizio metadati NameNode | testa nodi | 8020 | IPC | Metadati del file system 
| DataNode | Tutti i nodi di lavoro | 30075 | HTTPS | Interfaccia utente Web per visualizzare lo stato, i registri e così via. |
| DataNode | Tutti i nodi di lavoro | 30010 | &nbsp; | Trasferimento di dati |
| DataNode | Tutti i nodi di lavoro | 30020 | IPC | Operazioni dei metadati |
| NameNode secondario | Nodi di testa | 50090 | HTTP | Verifica per i metadati NameNode |

### <a name="yarn-ports"></a>Porte filati

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Interfaccia utente web Manager delle risorse | Nodi di testa | 8088 | HTTP | Interfaccia utente per Gestione risorse Web |
| Interfaccia utente web Manager delle risorse | Nodi di testa | 8090 | HTTPS | Interfaccia utente per Gestione risorse Web |
| Interfaccia di amministrazione di Manager delle risorse | testa nodi | 8141 | IPC | Per gli inoltri applicazione (Hive, server Hive, maialino, ecc.) |
| Utilità di pianificazione Manager delle risorse | testa nodi | 8030 | HTTP | Interfaccia di amministrazione |
| Interfaccia di applicazione Gestione risorse | testa nodi | 8050 | HTTP |Indirizzo dell'interfaccia di gestione applicazioni |
| NodeManager | Tutti i nodi di lavoro | 30050 | &nbsp; | L'indirizzo del gestore contenitore |
| Interfaccia utente web NodeManager | Tutti i nodi di lavoro | 30060 | HTTP | Interfaccia di gestione delle risorse |
| Indirizzo di sequenza temporale | Nodi di testa | 10200 | RPC | Il sequenza temporale RPC servizio. |
| Interfaccia utente web sequenza temporale | Nodi di testa | 8181 | HTTP | L'interfaccia web di servizio di sequenza temporale |

### <a name="hive-ports"></a>Porte hive

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nodi di testa | 10001 | Thrift | Servizio per la connessione a livello di programmazione per Hive Thrift/JDBC) |
| HiveServer | Nodi di testa | 10000 | Thrift | Servizio per la connessione a livello di programmazione per Hive Thrift/JDBC) |
| Hive Metastore | Nodi di testa | 9083 | Thrift | Servizio per la connessione a livello di programmazione ai metadati Hive Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porte WebHCat

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| WebHCat server | Nodi di testa | 30111 | HTTP | Dell'API Web nella parte superiore di HCatalog e altri servizi Hadoop |

### <a name="mapreduce-ports"></a>Porte MapReduce

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nodi di testa | 19888 | HTTP | Interfaccia utente web MapReduce JobHistory |
| JobHistory | Nodi di testa | 10020 | &nbsp; | MapReduce JobHistory server |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Mappa intermedio trasferimenti restituisce alla richiesta di riduttori |

### <a name="oozie"></a>Oozie

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Oozie server | Nodi di testa | 11000 | HTTP | URL del servizio Oozie |
| Oozie server | Nodi di testa | 11001 | HTTP | Porta per Oozie amministrazione |

### <a name="ambari-metrics"></a>Metrica Ambari

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Sequenza temporale (cronologia applicazione) | Nodi di testa | 6188 | HTTP | L'interfaccia web di servizio di sequenza temporale |
| Sequenza temporale (cronologia applicazione) | Nodi di testa | 30200 | RPC | L'interfaccia web di servizio di sequenza temporale |

### <a name="hbase-ports"></a>Porte HBase

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nodi di testa | 16000 | &nbsp; | &nbsp; |
| Informazioni HMaster dell'interfaccia utente Web | Nodi di testa | 16010 | HTTP | Porta per il web Master HBase dell'interfaccia utente |
| Server di area | Tutti i nodi di lavoro | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | La porta utilizzata dai client per connettersi a ZooKeeper |

### <a name="kafka-ports"></a>Porte Kafka

| Servizio | Nodi | Porta | Protocollo | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Gestore  | Nodi di lavoro | 9092 | [Protocollo Kafka](http://kafka.apache.org/protocol.html) | Usato per la comunicazione client |
| &nbsp; | Nodi zookeeper | 2181 | &nbsp; | La porta utilizzata dai client per connettersi a Zookeeper |
