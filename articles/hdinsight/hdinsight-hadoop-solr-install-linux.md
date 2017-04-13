<properties
    pageTitle="Usa azione Script per installare Solr basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come installare Solr nei cluster basati su Linux HDInsight Hadoop utilizzo di azioni di Script."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installare e usare Solr sui HDInsight Hadoop cluster

In questo argomento si imparerà a installare Solr in Azure HDInsight tramite Script azione. Solr è una piattaforma ricerca potenti e offre funzionalità di ricerca a livello dell'organizzazione per dati gestiti da Hadoop. Dopo aver installato Solr cluster HDInsight, si verrà anche informazioni su come cercare dati utilizzando Solr.

> [AZURE.NOTE] La procedura descritta in questo documento richiede un cluster basati su Linux HDInsight. Per informazioni sull'utilizzo di Solr con un cluster basato su Windows, vedere [installare e usare Solr sui HDinsight Hadoop cluster (Windows)](hdinsight-hadoop-solr-install.md)

Script di esempio utilizzati in questo argomento viene creato un cluster di Solr con una configurazione specifica. Se si desidera configurare il cluster Solr con diversi insiemi, shards, schemi, repliche e così via, è necessario modificare lo script e i file binari Solr di conseguenza.

## <a name="whatis"></a>Che cos'è Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) è una piattaforma di ricerca dell'organizzazione che consente di potente ricerca full-text sui dati. Mentre Hadoop consente di archiviare e gestire grandi quantità di dati, Apache Solr fornisce le funzionalità di ricerca per recuperare più rapidamente i dati. In questo argomento vengono fornite istruzioni su come personalizzare un cluster di HDInsight per installare Solr.

> [AZURE.WARNING] Componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati, ad esempio Solr, riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Che cosa significa lo script

Questo script apporta le modifiche seguenti al cluster HDInsight:

* Installa Solr in`/usr/hdp/current/solr`
* Creare un nuovo utente, __solrusr__, che viene utilizzato per eseguire il servizio Solr
* Imposta __solruser__ come il proprietario del`/usr/hdp/current/solr`
* Aggiunge una configurazione [Upstart](http://upstart.ubuntu.com/) che verrà avviato Solr se un nodo cluster riavvia. Solr viene avviato automaticamente anche nei nodi del cluster dopo l'installazione

## <a name="install"></a>Installare Solr utilizzo di azioni di Script

Script di esempio per installare Solr in un cluster di HDInsight è disponibile nel percorso seguente.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

In questa sezione fornisce istruzioni su come utilizzare lo script di esempio quando si crea un nuovo cluster tramite il portale di Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, HDInsight .NET SDK o Gestione risorse Azure modelli utilizzabili per applicare le azioni script. È inoltre possibile applicare le azioni script per già in esecuzione cluster. Per ulteriori informazioni, vedere [personalizzare HDInsight cluster con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster utilizzando la procedura descritta in [cluster basati su Linux disposizione HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), ma non completate il provisioning.

2. Nella e **Facoltativi** , selezionare **Le azioni Script**e fornire le informazioni seguenti:

    * __Nome__: immettere un nome descrittivo per l'azione script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
    * __Testa__: selezionare questa opzione
    * __Lavoro__: selezionare questa opzione
    * __ZOOKEEPER__: selezionare questa opzione per installare nel nodo Zookeeper
    * __Parametri__: lasciare questo campo vuoto

3. Nella parte inferiore delle **Azioni di Script**, utilizzare il pulsante **selezione** per salvare la configurazione. Infine, utilizzare il pulsante **Selezionare** nella parte inferiore della stessa e **Facoltativi** per salvare le informazioni di configurazione facoltativo.

4. Continuare il provisioning del cluster come descritto nei [cluster basati su Linux disposizione HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Utilizzo di Solr in HDInsight

### <a name="indexing-data"></a>L'indicizzazione di dati

Deve iniziare con l'indicizzazione Solr con alcuni file di dati. È quindi possibile utilizzare Solr per eseguire query di ricerca sui dati indicizzati. Utilizzare la procedura seguente per aggiungere alcuni dati di esempio Solr e quindi eseguire una query:

1. Connettersi a cluster HDInsight utilizzando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere le operazioni seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    > [AZURE.IMPORTANT] Procedura in un secondo momento in questa creazione documento utilizza un tunnel SSL per connettersi al web Solr dell'interfaccia utente. Per utilizzare questa procedura, è necessario stabilire un tunnel SSL e quindi configurare il browser per usarlo.
    >
    > Per ulteriori informazioni, vedere [Usare SSH connettersi accesso web Ambari dell'interfaccia utente, ResourceManager, JobHistory, NameNode, Oozie e altre web dell'interfaccia utente](hdinsight-linux-ambari-ssh-tunnel.md)

2. Utilizzare i comandi seguenti Solr indicizzare i dati di esempio:

        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml

    Viene visualizzato il seguente output nella console di:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L'utilità post.jar indicizza Solr con due campioni documenti, **solr.xml** e **monitor.xml**. Questi verranno archiviati in __collection1__ all'interno di Solr.

3. Eseguire una query API REST esposto dal Solr le operazioni seguenti:

        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

    Seguente emette query __collection1__ per tutti i documenti corrispondenti __ \*:\* __ (codificata come \*% 3A\* nella stringa di query) e che la risposta deve essere restituita come JSON. La risposta dovrebbe essere simile al seguente:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Tramite il dashboard Solr

Il dashboard Solr è un'interfaccia utente che consente di lavorare con Solr tramite un web browser web. Dashboard di Solr non viene esposto direttamente su Internet dal cluster HDInsight, ma è necessario accedere tramite un tunnel SSH. Per ulteriori informazioni sull'utilizzo di un tunnel SSH, vedere [Usare SSH connettersi accesso web Ambari dell'interfaccia utente, ResourceManager, JobHistory, NameNode, Oozie e altre web dell'interfaccia utente](hdinsight-linux-ambari-ssh-tunnel.md)

Dopo aver stabilito un tunnel SSH, utilizzare la procedura seguente per utilizzare il dashboard Solr:

1. Determinare il nome host per headnode primaria:

    1. Consente di connettersi al cluster sulla porta 22 SSH. Ad esempio `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` dove __nomeutente__ è il proprio nome utente SSH e __nome cluster__ è il nome del cluster.

        Per ulteriori informazioni sull'uso di SSH, vedere i documenti seguenti:

        * [Usare SSH con basati su Linux HDInsight da un client Linux, Unix o Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Usare SSH con basati su Linux HDInsight da un client di Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. Utilizzare il comando seguente per ottenere il nome host completo:

            hostname -f

        Verrà restituito un nome simile al seguente:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        Questo è il nome host che deve essere utilizzato nei passaggi seguenti.
    
1. Nel browser, connettersi a __http://HOSTNAME:8983/solr / #/__, dove __nome host__ è il nome definito in precedenza. 

    La richiesta deve essere distribuita attraverso il tunnel SSH per il nodo principale per il cluster HDInsight. Verrà visualizzata una pagina simile al seguente:

    ![Immagine del dashboard Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. Dal riquadro di sinistra, utilizzare il **Selettore di base** in giù per selezionare **collection1**. Diversi movimenti dovrebbero essere visualizzate in seguito __collection1__.

3. Le voci sotto __collection1__, selezionare __Query__. Usare i valori seguenti per popolare la pagina di ricerca:

    * Nella casella di testo **domande** immettere ** \*:**\*. Tutti i documenti che siano indicizzati restituirà in Solr. Se si desidera cercare una stringa specifica all'interno dei documenti, è possibile immettere la stringa di seguito.

    * Nella casella di testo **wt** selezionare il formato di output. Il valore predefinito è **json**.

    Infine, selezionare il pulsante **Esegui Query** nella parte inferiore della pate ricerca.

    ![Azione di Script per personalizzare un cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

    L'output viene restituita la due documenti che è stato usato per l'indicizzazione Solr. L'output simile al seguente:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="starting-and-stopping-solr"></a>Avviare e arrestare Solr

Se è necessario interrompere o avviare solare manualmente, utilizzare i comandi seguenti:

    sudo stop solr

    sudo start solr

## <a name="backup-indexed-data"></a>Backup dati indicizzati

È buona norma, è consigliabile eseguire il backup dati indicizzati dei nodi di cluster Solr nello spazio di archiviazione Blob Azure. A questo scopo, procedere come segue:

1. Connettersi a cluster utilizzando SSH e quindi utilizzare il comando seguente per ottenere il nome host per il nodo principale:

        hostname -f
        
2. Utilizzare le operazioni seguenti per creare uno snapshot dei dati indicizzati. Sostituire __HOSTNAME__ con il nome restituito rispetto al comando precedente:

        curl http://HOSTNAME:8983/solr/replication?command=backup

    Verrà visualizzata una risposta al seguente:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

2. Successivamente, passare a __/usr/hdp/current/solr/example/solr__. Si verificherà una sottodirectory per ogni raccolta. Ogni directory insieme contiene una directory di __dati__ , in cui lo snapshot per la raccolta è disponibile.

    Ad esempio, se è stato utilizzato la procedura descritta in precedenza per indicizzare i documenti di esempio, la directory __/usr/hdp/current/solr/example/solr/collection1/data__ dovrebbe ora essere contenuto una directory denominata __snapshot. # # #__ nel punto in cui il # presenti sono la data e l'ora dello snapshot.

3. Creare un archivio compresso della cartella snapshot utilizzando un comando simile al seguente:

        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

    Per creare un nuovo archivio denominato __snapshot.20150806185338855.tgz__, che contiene il contenuto della directory __snapshot.20150806185338855__ .

3. È quindi possibile memorizzare nell'archivio di archiviazione principale del cluster utilizzando il comando seguente:

    Hadoop fs - copyFromLocal snapshot.20150806185338855.tgz/esempio/dei dati

    > [AZURE.NOTE] È consigliabile creare una directory dedicata per archiviare Solr snapshot. Ad esempio `hadoop fs -mkdir /solrbackup`.

Per ulteriori informazioni sull'uso delle Solr eseguire il backup e ripristino, vedere [esecuzione e il ripristino di backup di SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## <a name="see-also"></a>Vedere anche

- [Installare e utilizzare cluster di tonalità in HDInsight](hdinsight-hadoop-hue-linux.md). Tonalità è un'interfaccia utente che semplifica la creazione, esecuzione e Salva processi maialino e Hive, oltre al tipo Sfoglia spazio di archiviazione predefinito per il HDInsight cluster web.

- [Installare R nei cluster HDInsight][hdinsight-install-r]. Personalizzazione di cluster utilizzata per installare R nei cluster HDInsight Hadoop. R è un ambiente per il calcolo statistiche e lingua Apri origine. Fornisce centinaia di funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese.

- [Installare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Personalizzazione di cluster utilizzata per installare Giraph nei cluster HDInsight Hadoop. Giraph consente di eseguire grafico elaborazione tramite Hadoop e può essere utilizzata con Azure HDInsight.

- [Installare tonalità sui cluster HDInsight](hdinsight-hadoop-hue-linux.md). Personalizzazione di cluster utilizzata per installare tonalità nei cluster HDInsight Hadoop. Tonalità è un insieme di applicazioni Web utilizzato per interagire con un cluster di Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
