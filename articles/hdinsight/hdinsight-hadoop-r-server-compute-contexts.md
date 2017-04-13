<properties
   pageTitle="Calcolare le opzioni di contesto di Server R in HDInsight (preview) | Microsoft Azure"
   description="Informazioni sulle opzioni di contesto diversi calcolo disponibili per gli utenti con Server R su HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calcolare le opzioni di contesto di Server R in HDInsight (preview)

Server R Microsoft Azure HDInsight (preview) fornisce le funzionalità più recenti per basate su R analitica. Utilizza i dati archiviati in HDFS in un contenitore all'account di archiviazione [Blob Azure](../storage/storage-introduction.md "archiviazione Blob Azure") o Linux file system locale. Poiché R Server si basa su Apri origine R, le applicazioni basate su R che si creano possono sfruttare uno dei pacchetti Apri origine R 8000 +. È anche possibile sfruttare le routine di [ridimensionamento](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Rivoluzione Analitica ridimensionamento"), pacchetto analitica di dati Microsoft che è incluso con Server R.  

Il nodo del bordo di un cluster Premium offre una posizione centralizzata per connettersi al cluster ed eseguire gli script R. Con un nodo del bordo, è possibile eseguire funzioni distribuite parallelizzate del ridimensionamento tra core del server di nodo bordo. È anche l'opzione per eseguirli tra i nodi del cluster utilizzando Hadoop mappa ridurre del ridimensionamento o ad calcolare contesti.

## <a name="compute-contexts-for-an-edge-node"></a>Calcolare contesti per un nodo del bordo

In generale, uno script R che viene eseguito nel Server R sul nodo del bordo viene eseguita all'interno di interprete R su tale nodo. L'eccezione è questi passaggi che chiamano una funzione di ridimensionamento. Le chiamate di ridimensionamento eseguire in un ambiente di elaborazione che dipende da come si configura il contesto di calcolo di ridimensionamento.  Quando si esegue lo script R da un nodo del bordo, i valori possibili del contesto di elaborazione sono locale sequenziale ("locale"), parallela locale ('localpar'), ridurre mappa e motori.

Le opzioni "locale" e 'localpar' si differenziano solo in modalità di esecuzione delle chiamate rxExec. Entrambi eseguire altre chiamate di funzioni di ricezione in modo parallelo tra core tutti disponibili se non diversamente specificato mediante l'utilizzo dell'opzione numCoresToUse ridimensionamento, ad esempio rxOptions(numCoresToUse=6). Di seguito sono riepilogate le varie opzioni di contesto di calcolo

| Calcolare contesto  | Come impostare                      | Contesto di esecuzione                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Locale sequenziali | rxSetComputeContext('local')    | Esecuzione parallelizzata tra i core del server nodo bordo, ad eccezione di rxExec chiama che vengono eseguite in serie |
| Parallelo locale   | rxSetComputeContext('localpar') | Esecuzione parallelizzata tra i core del server di nodo bordo                                 |
| Motori            | RxSpark()                       | Parallelizzata distribuito esecuzione tramite motori tra i nodi del cluster HDI      |
| Riduzione della mappa       | RxHadoopMR()                    | Parallelizzata distribuito esecuzione tramite ridurre i mapping tra i nodi del cluster HDI |


Supponendo che si desidera esecuzione parallelizzata per quanto riguarda le prestazioni, sono disponibili tre opzioni. Opzione scelta dipende dalla natura del proprio lavoro analitica, insieme alle dimensioni e posizione dei dati.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Linee guida per decidere in un contesto di calcolo

Attualmente non è alcuna formula che indica che il calcolo di contesto da utilizzare. Esistono tuttavia alcune principi che consentono di verificare la scelta giusta o almeno consentono di limitare le scelte prima di eseguire una prova comparativa. Questi orientamenti includono:

1.  Il file system di Linux locale è più HDFS veloce.
2.  Analisi ripetute sono più veloci se i dati sono locali e se è in XDF.
3.  È preferibile flusso piccole quantità di dati da un'origine dati di testo. Se la quantità di dati è grande, convertirlo in XDF prima dell'analisi.
4.  Il sovraccarico di copia o flusso di dati per il nodo del bordo per l'analisi diventa difficile da gestire per grandi quantità di dati.
5.  Motori sono più velocemente di quanto mappa ridurre per l'analisi in Hadoop.

Vengono specificati questi principi, alcune regole pratiche generali per la selezione di un contesto di calcolo:

### <a name="local"></a>Locale

- Se la quantità di dati da analizzare è piccola e non richiede analisi ripetute, il flusso direttamente in routine di analisi e utilizzare 'local' o 'localpar'.
- Se la quantità di dati da analizzare è piccole e medie e richiede analisi ripetuta, quindi copiare il file System locale, importarlo XDF e analizzare tramite 'local' o 'localpar'.

### <a name="hadoop-spark"></a>Motori Hadoop

- Se la quantità di dati da analizzare è grande, quindi importarli XDF in HDFS (a meno che non lo spazio di archiviazione è un problema) e analizzare tramite "Ad".

### <a name="hadoop-map-reduce"></a>Ridurre Hadoop mappa

- Utilizzare solo se si verifica un problema insormontabili con utilizzo del contesto di elaborazione ad dal momento che in genere che sia più lenta.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Guida in linea su rxSetComputeContext

Per ulteriori informazioni ed esempi di ridimensionamento calcolo contesti, vedere all'interno del testo Guida in R metodo rxSetComputeContext, ad esempio:

    > ?rxSetComputeContext

È inoltre possibile fare riferimento a "Ridimensionamento Distributed Computing Guide" che è disponibile dalla raccolta di(https://msdn.microsoft.com/library/mt674634.aspx "Server R su MSDN") [R Server MSDN].


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare un nuovo cluster HDInsight che include R Server. Inoltre appreso le nozioni di base dell'uso console R da una sessione SSH. A questo punto è possibile leggere gli articoli seguenti consentono di scoprire le altre modalità di utilizzo di Server R HDInsight:

- [Panoramica del Server R per Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Guida introduttiva a server R per Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Aggiungere RStudio Server HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opzioni relative all'archiviazione Azure per Server R nella HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
