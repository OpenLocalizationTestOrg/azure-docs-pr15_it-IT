<properties
 pageTitle="Comprendere e risolvere gli errori WebHCat HDInsight"
 description="Informazioni su come per informazioni su errori comuni restituiti da WebHCat in HDInsight e viene spiegato come risolverli."
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
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Comprendere e risolvere gli errori ricevuti da WebHCat (Templeton), in HDInsight

Quando si utilizza WebHCat (in precedenza noto come Templeton,) per l'uso con HDInsight, possono verificarsi errori. In questo documento fornisce indicazioni su errori più comuni: perché si verificano e cosa è possibile eseguire per risolverli.

##<a name="what-is-webhcat"></a>Che cos'è WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) è una API REST per livello di gestione [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), una tabella e lo spazio di archiviazione per Hadoop. WebHCat è attivata per impostazione predefinita nei cluster HDInsight e viene utilizzato da diversi strumenti per inviare i processi, lo stato del processo e così via senza effettuare l'accesso al cluster.

##<a name="modifying-configuration"></a>Modifica di configurazione

> [AZURE.IMPORTANT] Alcuni degli errori elencati in questo documento che sia stato superato massimo configurato. Quando il passaggio di risoluzione indica che è possibile modificare un valore, è necessario utilizzare una delle operazioni seguenti per eseguire la modifica:

* Per i cluster di **Windows** : utilizzare un'azione script per configurare il valore durante la creazione di cluster. Per ulteriori informazioni, vedere [le azioni di script di elaborazione](hdinsight-hadoop-script-actions.md).

* Per i cluster **Linux** : usare Ambari (web o all'API REST) per modificare il valore. Per ulteriori informazioni, vedere [gestire HDInsight utilizzo Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configurazione predefinita

Di seguito sono i valori di configurazione predefiniti che possono influire sulle prestazioni WebHCat o causano errori se si superano questi valori:

| Impostazione | Risultato | Valore predefinito |
| ------- | ------------ | ------------- |
| [applicazioni yarn.Scheduler.Capacity.Maximum][maximum-applications] | Il numero massimo di processi che possono essere attive contemporaneamente (in sospeso o è in esecuzione) | 10.000 |
| [procedure templeton.Exec.max][max-procs] | Il numero massimo di richieste che possono essere contemporaneamente | 20 |
| [MapReduce.jobhistory.max età ms][max-age-ms] | Il numero di giorni che verrà mantenuta cronologia processi | 7 giorni |

##<a name="too-many-requests"></a>Troppe richieste

**Codice di stato HTTP**: 429

| Causa | Risoluzione |
| ----- | ---------- |
| È stato superato il numero massimo di richieste simultanee rappresentate in WebHCat al minuto (impostazione predefinita 20) | Ridurre il carico di lavoro per assicurarsi che non presentano più valori il numero massimo di richieste simultanee o aumentare il limite di richieste simultanee modificando `templeton.exec.max-procs`. Per ulteriori informazioni, vedere [configurazione di modifica](#modifying-configuration) |

##<a name="server-unavailable"></a>Server non disponibile

**Codice di stato HTTP**: 503

| Causa | Risoluzione |
| ---------------- | ------------------- |
| Il problema si verifica durante il failover tra HeadNode primario e secondario per il cluster | Attendere due minuti, quindi ripetere l'operazione |

##<a name="bad-request-content-could-not-find-job"></a>Richiesta non valida contenuto: Impossibile trovare processo

**Codice di stato HTTP**: 400

| Causa | Risoluzione |
| ---------------- | ------------------- |
| I dettagli dei processi sono stati eliminati dalla cronologia processo pulizia | Il periodo di conservazione predefinito per cronologia dei processi è 7 giorni. Questa operazione può essere modificata modificando `mapreduce.jobhistory.max-age-ms`. Per ulteriori informazioni, vedere [configurazione di modifica](#modifying-configuration) |
| Processo è stato terminato a causa di caso di errore | Ripetere l'invio di processi per un massimo di due minuti |
| È stato usato un id processo non valido | Verificare se l'id di processo è corretto |

##<a name="bad-gateway"></a>Gateway non valido

**Codice di stato HTTP**: 502

| Causa | Risoluzione |
| ---------------- | ------------------- |
| Si sta verificando simultanea interni all'interno del processo di WebHCat | Attendere simultanea di fine o riavviare il servizio WebHCat |
| Timeout durante l'attesa di una risposta dal servizio di ResourceManager. È possibile che il numero di applicazioni attivi passa al massimo configurato (impostazione predefinita 10.000) | Attendere attualmente in esecuzione processi in modo da completare o aumentare il limite di processo simultanee modificando `yarn.scheduler.capacity.maximum-applications`. Per ulteriori informazioni, vedere [configurazione di modifica](#modifying-configuration)  |
| Durante il tentativo di recuperare tutti i processi tramite chiamata [ottenere /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) mentre si `Fields` è impostato su`*` | Non recuperare *tutti* i dettagli dei processi, utilizzare invece `jobid` per recuperare informazioni per i processi solo maggiori di determinati id del processo. Non usare`Fields` |
| Il servizio WebHCat è inattivo durante il failover HeadNode | Attendere due minuti e riprovare |
| Sono disponibili più di 500 processi in sospeso inviati tramite WebHCat | Attendere fino al termine processi attualmente in sospeso prima di inviare ulteriori processi |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
