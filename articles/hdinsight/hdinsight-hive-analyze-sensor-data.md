<properties
    pageTitle="Analizzare i dati sensore utilizzando Hive e Hadoop | Microsoft Azure"
    description="Informazioni su come analizzare i dati dei sensori tramite la Console di Query Hive con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel con PowerView."
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
    ms.date="09/20/2016" 
    ms.author="larryfr"/>

#<a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analizzare i dati sensore attraverso la Console di Query Hive Hadoop in HDInsight

Informazioni su come analizzare i dati dei sensori tramite la Console di Query Hive con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel tramite Power View.

> [AZURE.NOTE] La procedura descritta in questo documento funziona solo con i cluster HDInsight basato su Windows.

In questo esempio, si userà Hive per elaborare i dati cronologici prodotti da riscaldamento, ventilazione e sistemi di aria condizionata (HVAC) per identificare i sistemi che non sono in grado di gestire in modo affidabile una temperatura del set. Si apprenderà come:

- Creare tabelle HIVE eseguire query sui dati archiviati in file separati da virgola (CSV).
- Creare query HIVE per analizzare i dati.
- Utilizzare Microsoft Excel per connettersi a HDInsight del computer (con (ODBC) open database connectivity per recuperare i dati analizzati.
- Usare Power View per visualizzare i dati.

![Un diagramma dell'architettura di soluzione](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##<a name="prerequisites"></a>Prerequisiti

* Un cluster di HDInsight (Hadoop): per informazioni sulla creazione di un cluster, vedere [cluster di provisioning Hadoop in HDInsight](hdinsight-provision-clusters.md) .

* Microsoft Excel 2013

    > [AZURE.NOTE] Microsoft Excel viene usato per la visualizzazione dei dati con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Driver ODBC Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

##<a name="to-run-the-sample"></a>Per eseguire l'esempio

1. Dal web browser, selezionare l'URL seguente. Sostituire `<clustername>` con il nome del cluster HDInsight.

        https://<clustername>.azurehdinsight.net

    Quando richiesto, eseguire l'autenticazione utilizzando il nome utente e la password utilizzati durante il provisioning di questo cluster.

2. Dalla pagina web che si apre, fare clic sulla scheda **Guida introduttiva raccolta** e scegliere il campione di **Analisi dei dati sensore** nella categoria **soluzioni con dati di esempio** .

    ![Guida introduttiva nella galleria](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Seguire le istruzioni visualizzate nella pagina web per completare l'esempio.
