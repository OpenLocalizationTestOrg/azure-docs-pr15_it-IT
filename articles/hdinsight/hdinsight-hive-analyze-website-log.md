<properties 
    pageTitle="Usare Hive con Hadoop per analisi dei registri di sito Web | Microsoft Azure" 
    description="Informazioni su come usare Hive con HDInsight per analizzare i registri di sito Web. È necessario utilizzare un file di log come input in una tabella HDInsight e usare HiveQL per query sui dati." 
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
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Usare Hive con HDInsight per analizzare i registri da siti Web

Informazioni su come usare HiveQL con HDInsight per analizzare i registri da un sito Web. Analisi dei registri di sito Web possono essere utilizzati per segmento al pubblico non solo in base alle attività simili, categorizzare i visitatori del sito da fasce di utenti e per trovare il layout del contenuto Visualizza, provengono da siti Web e così via.

In questo esempio, utilizzare un cluster di HDInsight per analizzare i file di log di sito Web per ottenere informazioni dettagliate sulla frequenza delle visite al sito Web da siti esterni in un giorno. È inoltre necessario generare un riepilogo degli errori di sito Web che gli utenti. Si apprenderà come:

- Connettersi a un sistema di archiviazione Blob Azure, che contiene il file di log di sito Web.
- Creare tabelle HIVE per quelli della query.
- Creare query HIVE per analizzare i dati.
- Utilizzare Microsoft Excel per connettersi a HDInsight (usando (ODBC) open database connectivity per recuperare i dati analizzati.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Prerequisiti

- È necessario aver il provisioning un cluster di Hadoop su Azure HDInsight. Per ulteriori informazioni, vedere [Disposizione HDInsight cluster][hdinsight-provision]. 
- È necessario disporre di Microsoft Excel 2013 o Excel 2010 installato.
- È necessario che il [Driver ODBC Hive Microsoft](http://www.microsoft.com/download/details.aspx?id=40886) per importare dati da Hive in Excel.


##<a name="to-run-the-sample"></a>Per eseguire l'esempio

1. Dal [Portale di Azure](https://portal.azure.com/), da Startboard (se è bloccato cluster di), fare clic sul riquadro cluster in cui si desidera eseguire il codice di esempio.

2. Da e il cluster, in **Collegamenti rapidi**, fare clic su **Dashboard Cluster**e da e il **Cluster Dashboard** , fare clic su **Dashboard Cluster HDInsight**. In alternativa, è possibile aprire direttamente nel dashboard utilizzando l'URL seguente:

        https://<clustername>.azurehdinsight.net
    
    Quando richiesto, eseguire l'autenticazione utilizzando il nome utente e la password utilizzati durante il provisioning del cluster.
  
2. Dalla pagina web che si apre, fare clic sulla scheda **Guida introduttiva raccolta** e scegliere il campione di **Analisi dei registri di sito Web** nella categoria **soluzioni con dati di esempio** .

3. Seguire le istruzioni visualizzate nella pagina web per completare l'esempio.

##<a name="next-steps"></a>Passaggi successivi
Provare a nell'esempio seguente: [analisi dei dati sensore utilizzando Hive con HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
