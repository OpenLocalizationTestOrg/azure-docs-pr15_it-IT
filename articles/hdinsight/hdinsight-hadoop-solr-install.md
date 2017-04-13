<properties
    pageTitle="Azione di Script per installare Solr in cluster Hadoop | Microsoft Azure"
    description="Informazioni su come personalizzare cluster HDInsight con Solr tramite Script azione."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installare e usare Solr sui HDInsight Hadoop cluster

Informazioni su come personalizzare cluster HDInsight basato su Windows con Solr tramite Script azione e come usare Solr per cercare i dati. Per informazioni sull'utilizzo di Solr con un cluster basato su Linux, vedere [installazione e utilizzo Solr sui HDinsight Hadoop cluster (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
È possibile installare Solr qualsiasi tipo di cluster (Hadoop, eccesso, HBase, motori) in Azure HDInsight tramite *Script azione*. Script di esempio per installare Solr in un cluster di HDInsight è disponibile un blob di sola lettura Azure lo spazio di archiviazione in [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

Script di esempio funziona solo con la versione cluster HDInsight 3.1. Per ulteriori informazioni sulle versioni di cluster HDInsight, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).

Script di esempio utilizzati in questo argomento crea un cluster basato su Windows Solr con una configurazione specifica. Se si desidera configurare il cluster Solr con diversi insiemi, shards, schemi, repliche e così via, è necessario modificare lo script e i file binari Solr di conseguenza.

**Articoli correlati**

- [Installare e usare Solr sui HDinsight Hadoop cluster (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight.
- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: informazioni generali su come personalizzare cluster HDInsight tramite Script azione.
- [Script di sviluppare Script azione per HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>Che cos'è Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> è una piattaforma di ricerca dell'organizzazione che consente di potente ricerca full-text sui dati. Mentre Hadoop consente di archiviare e gestire grandi quantità di dati, Apache Solr fornisce le funzionalità di ricerca per recuperare più rapidamente i dati. 

## <a name="install-solr-using-portal"></a>Installare Solr nel portale

1. Iniziare a creare un cluster utilizzando l'opzione **Crea personalizzato** , come descritto nel [cluster di creare Hadoop in HDInsight](hdinsight-provision-clusters.md#portal).
2. Nella pagina di **Azioni di Script** della procedura guidata, fare clic su **Aggiungi azione script** per fornire dettagli sull'azione script come illustrato di seguito:

    ![Azione di usare Script per personalizzare un cluster] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Azione di usare Script per personalizzare un cluster")

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione script. Ad esempio, <b>Installare Solr</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Specificare la risorsa identificatore URI (Uniform) per lo script richiamato per personalizzare il grafico. Ad esempio <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo di nodo</td>
            <td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>tutti i nodi</b>, <b>solo i nodi di testa</b>o <b>solo i nodi di lavoro</b>.
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesto dallo script. Script per installare Solr non richiede parametri, in modo che può essere lasciato vuoto.</td></tr>
    </table>

    È possibile aggiungere più di un'azione di script per installare componenti multipli nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare la creazione del cluster.


## <a name="use-solr"></a>Utilizzare Solr

Deve iniziare con l'indicizzazione Solr con alcuni file di dati. È quindi possibile utilizzare Solr per eseguire query di ricerca sui dati indicizzati. Per usare Solr in un cluster di HDInsight, procedere come segue:

1. **Usare RDP Remote Desktop Protocol () in remoto nel cluster HDInsight con Solr installato**. Dal portale di Azure, attivare Desktop remoto per il cluster che è stato creato con Solr installato e quindi remota nel cluster. Per ulteriori informazioni, vedere [connettersi a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Indice Solr caricando file di dati**. Quando si indicizza Solr, inserire i documenti che potrebbe essere necessario eseguire una ricerca in. Per indicizzare Solr, consente di RDP remote nel cluster, passare al desktop, aprire la riga di comando Hadoop e passare a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Eseguire il comando seguente:

        java -jar post.jar solr.xml monitor.xml

    Viene visualizzato il seguente output nella console di:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L'utilità post.jar indicizza Solr con due campioni documenti, **solr.xml** e **monitor.xml**. Sono disponibili con l'installazione di Solr l'utilità di post.jar e documenti di esempio.

3. **Utilizzare il dashboard Solr la ricerca all'interno dei documenti indicizzati**. Sessione RDP cluster HDInsight, aprire Internet Explorer e avviare il dashboard Solr in **http://headnodehost:8983/solr / #/**. Nel riquadro di sinistra, dall'elenco a discesa **Core selettore** selezionare **collection1**e all'interno di esso, fare clic su **Query**. Ad esempio, per restituire tutti i documenti in Solr e fornire i valori seguenti:

    * Nella casella di testo **domande** immettere ** \*:**\*. Tutti i documenti che siano indicizzati restituirà in Solr. Se si desidera cercare una stringa specifica all'interno dei documenti, è possibile immettere la stringa di seguito.
    
    * Nella casella di testo **wt** selezionare il formato di output. Il valore predefinito è **json**. Fare clic su **eseguire Query**.

    ![Azione di usare Script per personalizzare un cluster] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Eseguire una query nel dashboard Solr")
    
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


4. **Consigliato: eseguire il backup dei dati indicizzati da Solr a archiviazione Blob Azure associato al cluster HDInsight**. È buona norma, è consigliabile eseguire il backup dati indicizzati dei nodi di cluster Solr nello spazio di archiviazione Blob Azure. A questo scopo, procedere come segue:

    1. Nella sessione RDP aprire Internet Explorer e scegliere l'URL seguente:

            http://localhost:8983/solr/replication?command=backup

        Verrà visualizzata una risposta al seguente:

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. In sessione remota, passare a {SOLR_HOME}\{insieme} \data. Per il cluster creato tramite lo script di esempio, deve essere **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. In questa posizione visualizzata una cartella di snapshot creata con un nome simile a * *snapshot.* timestamp** *.

    3. Comprimere la cartella snapshot e caricarlo in archiviazione Blob Azure. Dalla riga di comando Hadoop passare al percorso della cartella snapshot tramite il comando seguente:

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Questo comando Copia immagine per /example/data / all'interno del contenitore all'interno dello spazio di archiviazione account associato al cluster predefinito.

## <a name="install-solr-using-aure-powershell"></a>Installare Solr usando Aure PowerShell

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  Nell'esempio viene illustrato come installare motori tramite PowerShell Azure. È necessario personalizzare lo script per usare [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installare Solr mediante .NET SDK

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). Nell'esempio viene illustrato come installare motori utilizzando .NET SDK. È necessario personalizzare lo script per usare [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Vedere anche

- [Installare e usare Solr sui HDinsight Hadoop cluster (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight.
- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: informazioni generali su come personalizzare cluster HDInsight tramite Script azione.
- [Script di sviluppare Script azione per HDInsight](hdinsight-hadoop-script-actions.md).
- [Installare e usare motori nei cluster HDInsight][hdinsight-install-spark]: esempio azione Script sull'installazione di motori.
- [Installare R nei cluster HDInsight][hdinsight-install-r]: esempio di azione Script sull'installazione R.
- [Installare Giraph sui HDInsight cluster](hdinsight-hadoop-giraph-install.md): esempio di azione Script sull'installazione Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
