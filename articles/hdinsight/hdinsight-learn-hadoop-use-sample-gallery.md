<properties
   pageTitle="Informazioni su Hadoop in HDInsight con la raccolta di esempio | Microsoft Azure"
   description="Apprendere Hadoop eseguendo le applicazioni di esempio dalla raccolta HDInsight Guida introduttiva. Usare dati di esempio o fornire la propria."
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
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Informazioni su Hadoop mediante la raccolta di Azure HDInsight Guida introduttiva

Guida introduttiva raccolta è disponibile solo per i cluster HDInsight basato su Windows. La raccolta contiene una semplice e un modo rapido informazioni Hadoop eseguendo le applicazioni di esempio in HDInsight. Alcuni degli esempi forniti con dati di esempio. È possibile fornire i propri dati per gli esempi rimanenti. Attualmente sono disponibili negli esempi seguenti sei viene (con più proveniente):

- Soluzioni con i dati di Azure
    - Analisi dei registri di sito Web Microsoft Azure
    - Analitica lo spazio di archiviazione di Microsoft Azure
- Soluzioni con dati di esempio
    - Analisi dei dati sensore
    - Analisi delle tendenze Twitter
    - Analisi dei registri di sito Web
    - Suggerimento filmato mahout

![HDInsight Hadoop, eccesso e HBase Guida introduttiva raccolta soluzioni inclusi i dati di esempio.][hdinsight.sample.gallery]

Il video seguente viene illustrato come eseguire l'esempio di analisi di tendenza Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/EMBED/7ePbHot1SN4></a></center>

Dashboard di accessibili accedendo alla http://<YourHDInsightClusterName>.azurehdinsight.net/ o dal portale di Azure.

**Per eseguire un esempio dalla Guida introduttiva raccolta**

1. Accedere al [portale di Azure][azure.portal].
2. Fare clic su **Sfoglia** nel menu a sinistra e quindi scegliere il nome del cluster **Cluster HDInsight**.
3. Fare clic su **Dashboard** dal menu superiore.
4. Immettere il nome utente e la password per l'utente HTTP (denominato anche l'utente cluster).
6. Fare clic su **Guida introduttiva raccolta** nella parte superiore della pagina.
7. Fare clic su uno degli esempi. Ogni esempio fornisce istruzioni dettagliate per l'esecuzione. Nella figura seguente mostra il campione di analisi di tendenza Twitter:

    ![Esempio di analisi di tendenza HDInsight Twitter][hdinsight.twitter.sample]

## <a name="next-steps"></a>Passaggi successivi
Altri modi per informazioni sulle HDInsight includono:

- [Mappa di risorse HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
