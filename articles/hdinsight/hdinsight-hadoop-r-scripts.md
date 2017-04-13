<properties
    pageTitle="Usare R in HDInsight per personalizzare i cluster | Microsoft Azure"
    description="Informazioni su come installare R tramite Script azione e utilizzare R nei cluster HDInsight."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installare e usare R nei cluster HDInsight Hadoop

Informazioni su come personalizzare Windows in base a cluster HDInsight con R tramite Script azione e come usare R HDInsight cluster. Il [livello di premium](https://azure.microsoft.com/pricing/details/hdinsight/) che offre la possibilità di HDInsight include Server R come parte del cluster HDInsight. In questo modo script R usare MapReduce e motori per eseguire calcoli distribuiti. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di Server R HDInsight](hdinsight-hadoop-r-server-get-started.md). Per informazioni sull'utilizzo di R con un cluster basato su Linux, vedere [installazione e uso R su HDinsight Hadoop cluster (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
È possibile installare R qualsiasi tipo di grafico (Hadoop, eccesso, HBase, motori) in Azure HDInsight tramite *Script azione*. Script di esempio per installare R in un cluster di HDInsight è disponibile un blob di sola lettura Azure lo spazio di archiviazione in [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Articoli correlati**

- [Installare e usare R nei cluster HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight
- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: informazioni generali su come personalizzare cluster HDInsight tramite Script azione
- [Sviluppare script azione Script per HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Che cos'è R?

<a href="http://www.r-project.org/" target="_blank">R progetto per il calcolo statistiche</a> è un ambiente per il calcolo statistiche e aprire origine lingua. R offre centinaia di compilazione funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese. R è l'ambiente di programmazione preferito per più professionale statistici e scienziati in un'ampia varietà di campi.

R è compatibile con lo spazio di archiviazione Blob Azure (WASB) in modo che i dati archiviati sono possono essere elaborati utilizzando R su HDInsight.  

## <a name="install-r"></a>Installare R

Uno [script di esempio](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) per installare R in un cluster di HDInsight è disponibile un blob di sola lettura in archiviazione Azure. In questa sezione vengono fornite istruzioni su come utilizzare lo script di esempio durante la creazione di cluster tramite il portale di Azure.

> [AZURE.NOTE] Script di esempio è stata introdotta con la versione cluster HDInsight 3.1. Per ulteriori informazioni sulle versioni di cluster HDInsight, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).

1. Quando si crea un cluster HDInsight dal portale, fare clic su **Configurazione facoltativa**e quindi fare clic su **Azioni Script**.
2. Nella pagina **Azioni Script** immettere i valori seguenti:

    ![Azione di usare Script per personalizzare un cluster] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Azione di usare Script per personalizzare un cluster")

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione di script, ad esempio, <b>R installare</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Specificare l'URI per lo script richiamato per personalizzare il cluster, ad esempio <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo di nodo</td>
            <td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere solo <b>Tutti i nodi</b>, <b>solo i nodi di testa</b>o <b>nodi di lavoro</b> .
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesto dallo script. Tuttavia, lo script per installare R non richiede parametri, in modo che può essere lasciato vuoto.</td></tr>
    </table>

    È possibile aggiungere più di un'azione di script per installare componenti multipli nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare crating cluster.

È anche possibile utilizzare lo script per installare R in HDInsight tramite PowerShell Azure o HDInsight .NET SDK. Istruzioni per queste procedure sono vedere più avanti in questo articolo.

## <a name="run-r-scripts"></a>Esecuzione di script R
In questa sezione viene descritto come eseguire uno script R cluster Hadoop con HDInsight.

1. **Stabilire una connessione Desktop remoto a cluster**: dal portale di abilitare Desktop remoto per il cluster è stato creato con R installato e quindi connettersi al cluster. Per ulteriori informazioni, vedere [connettersi a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Aprire la console R**: installazione R la inserisce un collegamento alla console R sul desktop del nodo principale. Fare clic su di essa per aprire la console R.

3. **Eseguire lo script R**: R di script può essere eseguito direttamente dalla console R incollandolo, selezionarlo e premere INVIO. Ecco un script di esempio che genera i numeri da 1 a 100 e moltiplicando per 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Le prime due righe chiamino alle librerie di RHadoop che vengono installate con R. Nell'ultima riga consente di stampare i risultati nella console. L'output dovrebbe risultare analoga alla seguente:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installare R utilizzando Aure PowerShell

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  Nell'esempio viene illustrato come installare motori tramite PowerShell Azure. È necessario personalizzare lo script per utilizzare [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installare R utilizzando .NET SDK

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). Nell'esempio viene illustrato come installare motori utilizzando .NET SDK. È necessario personalizzare lo script per utilizzare [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Vedere anche

- [Installare e usare R nei cluster HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight
- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: informazioni generali su come personalizzare cluster HDInsight tramite Script azione
- [Sviluppare script azione Script per HDInsight](hdinsight-hadoop-script-actions.md)
- [Installare e usare motori nei cluster HDInsight][hdinsight-install-spark]: esempio azione Script sull'installazione di motori
- [Installare Giraph sui HDInsight cluster](hdinsight-hadoop-giraph-install.md): esempio azione Script sull'installazione Giraph
- [Installare Solr sui HDInsight cluster](hdinsight-hadoop-solr-install-linux.md): esempio azione Script sull'installazione Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
