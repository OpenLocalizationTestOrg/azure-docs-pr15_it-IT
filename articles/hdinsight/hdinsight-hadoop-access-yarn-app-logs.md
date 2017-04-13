<properties
    pageTitle="Applicazione di Access filati Hadoop registri a livello di programmazione | Microsoft Azure"
    description="Applicazione di Access registra a livello di programmazione in un cluster di Hadoop in HDInsight."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Applicazione di Access filati accede HDInsight basato su Windows

In questo argomento viene illustrato l'accesso dei registri per le applicazioni di filati (ancora un'altra risorsa negoziatore) termine in un cluster di Hadoop in Azure HDInsight

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano solo a cluster HDInsight basato su Windows. Per informazioni su come accedere filati accede cluster basati su Linux HDInsight, vedere [applicazione di Access filati accede basati su Linux Hadoop in HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Prerequisiti

- Un cluster basato su Windows HDInsight.  Vedere [cluster basato su Windows creare Hadoop in HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>Server di filati sequenza temporale

Per informazioni generiche sulla completa di applicazioni, nonché informazioni specifiche del framework applicazione tramite due diverse interfacce <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Filati sequenza temporale Server</a> . In particolare:

* Archiviazione e il recupero delle informazioni di applicazione generica nei cluster HDInsight è stato abilitato con versione 3.1.1.374 o versione successiva.
* Il componente di informazioni specifiche del framework applicazione del Server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.


Informazioni generali sulla applicazioni includono i tipi di dati seguenti:

* ID applicazione, un identificatore univoco di un'applicazione
* L'utente che ha avviato l'applicazione
* Informazioni su tentativi per completare l'applicazione
* I contenitori utilizzati da qualsiasi tentativo di applicazione specificato

Nei cluster di HDInsight queste informazioni verranno archiviate da Gestione risorse di Azure a un archivio di cronologia nel contenitore predefinito del proprio account Azure lo spazio di archiviazione predefinito. Questi dati generici applicazioni completate possono essere recuperati tramite un API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>FILATI applicazioni e log

FILATI supporta più modelli di programmazione (MapReduce da uno di essi), la separazione di gestione delle risorse dalla programmazione/monitoraggio delle applicazioni. Ciò avviene tramite un globale *ResourceManager* (RM), per nodo lavoro *NodeManagers* (NMs) e all'applicazione *ApplicationMasters* (AMs). AM per applicazione negoziare risorse (CPU, memoria, disco, rete) la eseguire l'applicazione con suo Il funzionamento con NMs da concedere a queste risorse, concesso come *contenitori*. Il AM è responsabile per verificare l'avanzamento della contenitori assegnato dal suo Un'applicazione può richiedere molti contenitori a seconda della natura dell'applicazione.

Inoltre, ogni applicazione può essere costituito più *applicazione tenta* per terminare in presenza di anomalo o a causa la perdita di comunicazione tra un AM e un RM. Di conseguenza, contenitori vengono assegnati a un tentativo di specifico di un'applicazione. Concetto di, un contenitore fornisce il contesto per unità di base del lavoro eseguito da un'applicazione di filati e tutte le operazioni che sono stata completata nel contesto di un contenitore viene eseguita sul nodo singolo lavoro in cui è stato ricevuto il contenitore. Vedere [Concetti filati] [ YARN-concepts] per un'ulteriore riferimento.

Registri delle applicazioni e dei registri del contenitore associato sono critici debug applicazioni Hadoop problematiche. FILATI fornisce un utile per la raccolta, l'aggregazione e archiviazione di registri delle applicazioni con l' [Aggregazione Log] [ log-aggregation] caratteristica. La funzione di aggregazione Log rende più deterministico accesso ai registri applicazioni ed è aggrega registri in tutti i contenitori su un nodo lavoro Archivia come un aggregato file di log per nodo di lavoro nel file system predefinito al termine dell'applicazione. L'applicazione può utilizzare centinaia o migliaia di contenitori, ma registri per tutti i contenitori eseguirvi su un nodo singolo lavoro verranno sempre aggregati in un unico file risultante in un file di log per nodo lavoro utilizzata dall'applicazione. Registro aggregazione è attivata per impostazione predefinita nei cluster HDInsight (versione 3.0 e versioni successive), e i log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

    wasbs:///app-logs/<user>/logs/<applicationId>

In posizione, *l'utente* è il nome dell'utente che ha avviato l'applicazione e *ID applicazione* è l'identificatore univoco di un'applicazione come assegnato dal suo filati

I registri aggregati non sono direttamente leggibili, man mano che vengono scritte in una [TFile][T-file], [formato binario] [ binary-format] indicizzato dal contenitore. FILATI sono disponibili gli strumenti CLI per scaricare questi file di log come testo normale per applicazioni o i contenitori di interesse. È possibile visualizzare i registri come testo normale, eseguire una di filo seguente comandi direttamente nei nodi del cluster (dopo la connessione a tale tramite RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager filati

L'interfaccia utente ResourceManager filati viene eseguito su headnode cluster e sono accessibili tramite il dashboard portale Azure: 

1. Accedere al [portale di Azure](https://portal.azure.com/). 
2. Nel menu a sinistra, fare clic su **Sfoglia**, fare clic su **HDInsight cluster**, fare clic su un cluster basato su Windows che si desidera accedere ai log di applicazione di filati.
3. Nel menu superiore fare clic su **Dashboard**. Verrà visualizzata una pagina aperta in un nuovo browser chiamata **HDInsight Console di Query**della scheda.
4. Dalla **Console di Query HDInsight**, fare clic su **Filati dell'interfaccia utente**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
