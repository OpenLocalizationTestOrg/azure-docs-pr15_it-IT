<properties
    pageTitle="Che cos'è R nella HDInsight? Introduzione al Server R HDInsight (preview) | Microsoft Azure"
    description="Che cos'è Server R HDInsight (preview) e su come utilizzare R Server per la creazione di applicazioni per l'analisi di dati."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Panoramica dei Server R HDInsight \(anteprima\)

Con Microsoft Azure HDInsight Premium Microsoft R Server è ora disponibile come opzione quando si crea cluster HDInsight in Azure. La nuova funzionalità fornisce scienziati dati statistici e programmatori R con su richiesta di accesso a scalable, distribuito metodi analitica su HDInsight.

Cluster possono ridimensionare i progetti e attività ed eliminati quando non è più necessari. Dal momento che fanno parte di Azure HDInsight, questi cluster accompagnato da supporto 24/7 a livello di organizzazione, un contratto di servizio di inattività 99,9 e della flessibilità necessaria per l'integrazione con altri componenti ecosistema Azure.

>[AZURE.NOTE] R Server è disponibile solo con HDInsight Premium. Al momento HDInsight Premium è disponibile solo per i cluster Hadoop e motori. Pertanto, attualmente è possibile utilizzare Server R solo con i cluster Hadoop e motori in HDInsight. Per ulteriori informazioni, vedere [quali sono i livelli di servizio diverso e componenti Hadoop disponibili con HDInsight?](hdinsight-component-versioning.md).

Server R HDInsight fornisce le funzionalità più recenti per basate su R analitica nel set di dati di grandi dimensioni che vengono caricati a archiviazione Blob Azure. Poiché R Server si basa su Apri origine R, possono sfruttare le applicazioni basate su R che si creano i pacchetti di Apri origine R 8000 +, nonché le routine di ridimensionamento, pacchetto analitica di dati Microsoft che è incluso con R Server.

Il nodo del bordo di cluster Premium offre una posizione centralizzata per connettersi al cluster ed eseguire gli script R. Con un nodo del bordo, è possibile eseguire funzioni distribuite parallelizzate del ridimensionamento tra core del server di nodo bordo. È anche l'opzione per eseguirli tra i nodi del cluster utilizzando Hadoop mappa ridurre del ridimensionamento o ad calcolare contesti.

Modelli di o le stime che è possono scaricare i risultati di analisi per usare locale. Essi possono anche essere operationalized in un' posizione in Azure, ad esempio tramite un [servizio web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)di [Azure Machine Learning Studio](http://studio.azureml.net) .

## <a name="get-started-with-r-on-hdinsight"></a>Guida introduttiva a R in HDInsight

Per includere R Server in un cluster di HDInsight, è necessario creare cluster Hadoop o motori con l'opzione Premium quando si crea un cluster tramite il portale di Azure. Entrambi i tipi di grafico utilizzano la stessa configurazione, che include Server R i nodi di dati di un cluster e un nodo del bordo come area di destinazione per basate sul Server R analitica. Per una panoramica approfondita sulla creazione di un cluster, vedere [Introduzione a Server R HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>Informazioni sulle opzioni di archiviazione dei dati

Spazio di archiviazione predefinito per i cluster HDInsight è archiviazione Blob con il sistema di file HDFS mappata in un contenitore di blob. In questo modo che tutti i dati vengono caricati all'archiviazione cluster, o scritti allo spazio di archiviazione cluster nel corso dell'analisi, è una chiamata effettuata persistente. È possibile utilizzare l'utilità [AzCopy](../storage/storage-use-azcopy.md) per copiare i dati da e verso il blob.

Oltre che usando archiviazione Blob, avere la possibilità di usare [Lo spazio di archiviazione di Azure dati Lake](https://azure.microsoft.com/services/data-lake-store/) con i cluster. Se si usa Lake dati, è possibile utilizzare lo spazio di archiviazione Blob e Lake dati per l'archiviazione HDFS.

È anche possibile usare [File Azure](../storage/storage-how-to-use-files-linux.md) come opzione di spazio di archiviazione per l'utilizzo del nodo di bordo. File di Azure consente di installare una condivisione file che è stata creata in archiviazione Azure nel file System Linux. Per ulteriori informazioni sulle opzioni di archiviazione di dati per Server R cluster HDInsight, vedere [Opzioni di archiviazione per Server R cluster HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Accesso R Server cluster

Dopo aver creato un cluster con Server R, è possibile connettersi alla console R sui nodo del cluster tramite SSH/PuTTY bordo. È anche possibile connettersi tramite un browser se si sceglie di installare l'IDE Server RStudio facoltativo nel nodo del bordo. Per ulteriori informazioni sull'installazione di RStudio Server, vedere [Installazione RStudio Server cluster HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Sviluppare ed eseguire gli script R

Creare ed eseguire gli script di R possono usare uno dei pacchetti Apri origine R 8000 + oltre routine parallelizzate e distribuite nella raccolta di ridimensionamento. In generale, script che viene eseguito nel Server R sul nodo del bordo viene eseguita all'interno di interprete R su tale nodo. L'eccezione è questi passaggi che richiamano un ridimensionamento funzionano con un contesto di calcolo da impostare per Hadoop mappa ridurre (RxHadoopMR) o ad (RxSpark).

In questi casi, la funzione viene eseguito in modalità distribuita attraverso tali dati nodi (attività) del cluster che sono associati ai dati di riferimento. Per ulteriori informazioni sulle opzioni di contesto calcolo diverso, vedere [calcolare le opzioni di scelta rapida per Server R HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Rendono operativi un modello

Una volta completata la modellazione dei dati, è possibile rendono operativi il modello per creare le stime per i nuovi dati sia in Azure e locale. Questo processo è noto come punteggio. Di seguito sono riportati alcuni esempi.

### <a name="score-in-hdinsight"></a>Punteggio di HDInsight

Per punteggio in HDInsight, scrivere una funzione R che chiama il modello per creare le stime per un nuovo file di dati caricati al proprio account di archiviazione. Salvare le stime nuovo account di archiviazione. È possibile eseguire la routine su richiesta del nodo di bordo del cluster o tramite un processo pianificato.  

### <a name="score-in-azure-machine-learning"></a>Punteggio di apprendimento Azure

Per punteggio utilizzando un servizio web apprendimento Azure, utilizzare il pacchetto di Azure Machine Learning R Apri origine noto come [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) per pubblicare il modello come un servizio web Azure. Per maggiore comodità, questo pacchetto è preinstallato nel nodo del bordo. Successivamente, utilizzare gli strumenti di apprendimento per creare un'interfaccia utente per il servizio web e quindi chiama il servizio web in base alle esigenze per il punteggio.

Se si sceglie questa opzione, è necessario convertire gli oggetti del modello di ridimensionamento a oggetti di un modello Apri origine equivalente per l'utilizzo con il servizio web. A tale scopo tramite l'utilizzo di funzioni di imposizione di ridimensionamento, ad esempio `as.randomForest()` per i modelli di insieme.


### <a name="score-on-premises"></a>Punteggio locale

Per punteggio locale dopo aver creato il modello, è possibile serializzare il modello in R, scaricarla, deserializzarle e quindi utilizzare per il punteggio nuovi dati. È possibile punteggio nuovi dati usando l'approccio descritto in precedenza in [punteggio in HDInsight](#scoring-in-hdinsight) o [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Gestire il cluster

### <a name="install-and-maintain-r-packages"></a>Installare e gestire pacchetti R

La maggior parte dei pacchetti R in uso è necessario sul nodo bordo poiché la maggior parte degli script R eseguiranno sono. Per installare pacchetti R aggiuntivi sul nodo del bordo, è possibile utilizzare la consueta `install.packages()` metodo di R.

Nella maggior parte dei casi, non è necessario installare pacchetti R aggiuntivi nei nodi dati se si utilizza solo routine dalla raccolta di ridimensionamento all'interno del cluster. Tuttavia, potrebbe essere necessario pacchetti aggiuntivi per supportare l'utilizzo di **rxExec** o **RxDataStep** esecuzione dei nodi di dati.

In questi casi, pacchetti aggiuntivi devono essere specificati mediante l'utilizzo di un'azione script dopo aver creato il grafico. Per ulteriori informazioni, vedere [creazione di un cluster di HDInsight con R Server](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modificare le impostazioni di memoria Hadoop mappa ridurre

Un cluster può essere modificato per modificare la quantità di memoria disponibile al Server R quando viene eseguito un processo di riduzione della mappa. Per modificare un cluster, utilizzare l'interfaccia utente Ambari Apache disponibile tramite e il portale Azure per il cluster. Per istruzioni su come accedere UI Ambari per il cluster, vedere [gestire HDInsight cluster tramite l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md).

È anche possibile modificare la quantità di memoria disponibile al Server R utilizzando parametri Hadoop nella chiamata a **RxHadoopMR** come segue:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Ridimensionare il cluster

Un cluster esistente, è possibile architetture verso l'alto o verso il basso tramite il portale. Per il ridimensionamento, è possibile ottenere la capacità aggiuntiva che potrebbero essere necessari per le attività di elaborazione più grandi o è possibile ridimensionare nuovamente un cluster quando è inattivo. Per istruzioni su come ridurre un cluster, vedere [gestire HDInsight cluster](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Gestire il sistema

Manutenzione viene eseguita in macchine virtuali Linux sottostante in un cluster HDInsight orario per applicare patch del sistema operativo e altri aggiornamenti. In genere manutenzione viene eseguita in 3:30 AM (in base all'ora locale per la macchina virtuale) ogni lunedì e giovedì. Gli aggiornamenti vengono eseguiti in modo che in cui non influenzare più di un quarto del cluster alla volta.  

Poiché i nodi testa sono ridondanti e non tutti i nodi di dati sono interessati, potrebbero rallentare tutti i processi in esecuzione. Sono ancora perfettamente fino al completamento, tuttavia. Qualsiasi software personalizzato o dati locali che si sono vengono mantenuti in questi eventi di manutenzione, a meno che non si verifica un errore grave che richiede una ricreazione cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Informazioni sulle opzioni di IDE per R Server in un cluster di HDInsight

Il nodo del bordo Linux di un cluster di HDInsight Premium è l'area di destinazione per l'analisi basate su R. Dopo la connessione al cluster, è possibile avviare l'interfaccia della console al Server R, digitare **R** al prompt dei comandi di Linux. Utilizzo dell'interfaccia console è stato migliorato se si esegue un editor di testo per lo sviluppo di script R in un'altra finestra e tagliare e incollare sezioni di script nella console di R in base alle esigenze.

Uno strumento più complesse per lo sviluppo di uno script R è IDE basato su R per l'uso sul desktop, ad esempio Microsoft annunciato recente [R Tools per Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS). Si tratta di una famiglia di strumenti di desktop e server da [RStudio](https://www.rstudio.com/products/rstudio-server/). È inoltre possibile utilizzare basato su Eclipse del Walware [StatET](http://www.walware.de/goto/statet).

In alternativa è possibile installare un IDE nel nodo bordo Linux.  Una scelta popolare è [RStudio Server](https://www.rstudio.com/products/rstudio-server/), che fornisce un IDE basate su browser per l'utilizzo da client remoti. Installazione RStudio Server nel nodo del bordo di un cluster di HDInsight Premium offre un'esperienza completa con IDE per lo sviluppo e l'esecuzione di script R con R Server nel cluster. Può essere molto più produttivo rispetto a quella console R.  Se si desidera utilizzare RStudio Server, vedere [Installazione RStudio Server cluster HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>Informazioni sui prezzi

Le commissioni di sono associate a un cluster HDInsight Premium con Server R sono strutturate in modo analogo a tariffe standard cluster di HDInsight. Ridimensionamento delle macchine virtuali sottostante si basano su nome, dati e i nodi di un bordo, con l'aggiunta di un sollevamento core ora per Premium. Per ulteriori informazioni sulle HDInsight Premium prezzi, tra cui prezzi durante l'anteprima pubblica e la disponibilità di una prova gratuita di 30 giorni, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi

Seguire i collegamenti seguenti per ulteriori informazioni sull'utilizzo di Server R con i cluster HDInsight.

- [Guida introduttiva a Server R HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Aggiungere RStudio Server HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calcolare le opzioni di contesto di Server R in HDInsight (preview)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni relative all'archiviazione Azure per Server R nella HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
