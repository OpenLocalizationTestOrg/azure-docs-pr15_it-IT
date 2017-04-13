<properties 
    pageTitle="Disponibile con i blocchi appunti Jupyter in HDInsight Spark. x cluster Linux | Microsoft Azure" 
    description="Le informazioni di altre Jupyter blocco appunti. x disponibile con cluster ad su HDInsight Linux." 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Disponibile per i blocchi appunti Jupyter con i cluster ad Apache su HDInsight Linux. x

Cluster ad Apache su HDInsight (Linux) include i blocchi appunti Jupyter che è possibile utilizzare per testare le applicazioni. Un kernel è un programma che viene eseguito e interpreta il codice. HDInsight Spark cluster offrono due vengono utilizzate con il blocco appunti Jupyter. Di seguito sono:

1. **PySpark** (per le applicazioni scritto in Python)
2. **Motori** (per le applicazioni scritte in Scala)

In questo articolo verranno fornite informazioni su come utilizzare queste x e quali sono i vantaggi che si riceve dal loro utilizzo.

**Prerequisiti:**

È necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Utilizzo di x 

1. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Dashboard Cluster**e il cluster motori e quindi fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti vengono nuovo. Fare clic su **Nuovo**e quindi fare clic su **Pyspark** o **motori**. Utilizzare la kernel motori per le applicazioni di Scala e kernel PySpark Applications Python.

    ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Creare un nuovo blocco appunti Jupyter") 

3. Verrà aperto un nuovo blocco appunti con kernel selezionato.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Perché è opportuno usare vengono PySpark o ad?

Ecco alcuni vantaggi derivanti dall'uso x nuovo.

1. **Preimpostate contesti**. Con **PySpark** o **motori** vengono forniti con i blocchi appunti Jupyter, non è necessario impostare in modo esplicito contesti motori o Hive prima di iniziare a utilizzare l'applicazione che si sviluppa; Questi sono disponibili per impostazione predefinita. Questi contesti sono:

    * **sc** - per il contesto di motori
    * **sqlContext** - per Hive contesto


    Pertanto, non è necessario eseguire istruzioni simile al seguente per impostare i contesti:

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    Se, tuttavia, è possibile utilizzare direttamente contesti preimpostati dell'applicazione.
    
2. **Magics di cella**. Il kernel PySpark fornisce alcuni predefiniti "magics", che sono comandi speciali che è possibile chiamare con `%%` (ad esempio `%%MAGIC` <args>). Il comando strumento deve essere la prima parola in una cella di codice e consentire di più righe di contenuto. La parola strumento dovrebbe essere la prima parola nella cella. Aggiunta di qualsiasi altro speciale, anche i commenti, si verificherà un errore.   Per ulteriori informazioni sulla magics, vedere [di seguito](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    La tabella seguente elenca magics diversi disponibili tramite la x.

  	| Speciale     | Esempio                         | Descrizione  |
  	|-----------|---------------------------------|--------------|
  	| Guida      | `%%help`                            | Genera una tabella contenente tutti i magics disponibili con esempio e la descrizione |
  	| Info      | `%%info`                          | Informazioni sulla sessione di output per l'endpoint di inserire il corrente |
  	| configurare | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura i parametri per la creazione di una sessione. Il contrassegno forza (-f) è obbligatorio se è già stata creata una sessione e la sessione verrà eliminata e ricreata. Osservare [POST /sessions di inserire il corpo richiesta](https://github.com/cloudera/livy#request-body) per un elenco di parametri validi. Parametri devono essere passati come una stringa JSON e devono essere sulla riga successiva dopo la chiave, come illustrato nell'esempio la colonna. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Esegue una query Hive il sqlContext. Se il `-o` parametro passato, il risultato della query viene mantenuto nella % contesto Python locale come un dataframe [Pandas](http://pandas.pydata.org/) .   |
  	| locale     |     `%%local`<br>`a=1`              | Tutto il codice nelle righe successive verrà eseguito in locale. Codice deve essere codice Python valido. |
  	| log      | `%%logs`                        | Restituisce i registri per la sessione di inserire il corrente.  |
  	| Elimina    | `%%delete -f -s <session number>` | Elimina una specifica sessione l'endpoint di inserire il corrente. Si noti che non è possibile eliminare la sessione viene avviata per il kernel stesso. |
  	| pulizia   | `%%cleanup -f`                    | Elimina tutte le sessioni per l'endpoint di inserire il corrente, tra cui sessione del blocco appunti. Forza flag -f è obbligatorio.  |

    >[AZURE.NOTE] Oltre a magics aggiunto dal kernel PySpark, è inoltre possibile utilizzare [magics IPython predefiniti](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), tra cui `%%sh`. È possibile utilizzare il `%%sh` speciale per l'esecuzione di script e blocco di codice in headnode cluster. 

3. **Visualizzazione automatica**. Kernel **Pyspark** vengono visualizzati automaticamente l'output della query Hive e SQL. È possibile scegliere tra diversi tipi di visualizzazioni inclusi nella tabella, grafici a torta, a linee, Area, barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametri supportati con la % chiave sql

La % chiave sql supporta diversi parametri che è possibile utilizzare per controllare il tipo di output che viene visualizzato quando si eseguono query. La tabella seguente elenca l'output.

| Parametro     | Esempio                         | Descrizione  |
|-----------|---------------------------------|--------------|
| -o      | `-o <VARIABLE NAME>`                          | Utilizzare questo parametro per mantenere il risultato della query, nel % contesto Python locale, come un dataframe [Pandas](http://pandas.pydata.org/) . Il nome della variabile dataframe è il nome della variabile specificata. |
| -q      | `-q`                          | Consente di disattivare visualizzazioni per la cella. Se non si vuole visualizzare automaticamente il contenuto di una cella e si vuole acquisire come un dataframe, quindi usare `-q -o <VARIABLE>`. Se si vuole disattivare visualizzazioni senza acquisire i risultati (ad esempio per l'esecuzione di una query SQL con gli effetti secondari, ad esempio un `CREATE TABLE` istruzione), utilizzare solo `-q` senza specificare un `-o` argomento. |
| -m       |  `-m <METHOD>`    | **Nel punto in cui è **eseguire** o **campione** ** (impostazione predefinita è **eseguire**). Se il metodo è **eseguire**, kernel sceglie elementi dall'inizio del set di risultati specificato da MAXROWS (descritto più avanti in questa tabella). Se il metodo di **esempio**, kernel verrà di esempio in modo casuale gli elementi del set di dati in base a `-r` parametro, descritto di seguito in questa tabella.   |
| -r     |     `-r <FRACTION>`            | Ecco un numero a virgola mobile compreso tra 0,0 e 1.0 **frazione** . Se il metodo di esempio per la query SQL `sample`, quindi il kernel esempi in modo casuale la frazione specificata di elementi del set di risultati per. ad esempio, se si esegue una query SQL con gli argomenti `-m sample -r 0.01`, quindi 1% delle righe risultato verrà campionato in modo casuale. |
| -n      | `-n <MAXROWS>`                        | **MAXROWS** è un valore intero. Il kernel verrà limitare il numero di righe di output da **MAXROWS**. Se **MAXROWS** non è un numero negativo, ad esempio **-1**, il numero di righe nel set di risultati non verranno limitato. |

**Esempio:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

Istruzione sopra indicata esegue le operazioni seguenti:

* Seleziona tutti i record da **hivesampletable**.
* Poiché si utilizza - q, disattiva visualizzazione automatica.
* Poiché si utilizza `-m sample -r 0.1 -n 500` it in modo casuale esempi 10% delle righe della hivesampletable e limita la dimensione del risultato impostata su 500 righe.
* Infine, perché sono stati utilizzati `-o query2` salva anche l'output una dataframe denominata **query 2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Considerazioni durante l'uso di nuovo x

Qualunque kernel utilizzare PySpark o motori, lasciando i blocchi appunti in esecuzione utilizzerà le risorse del cluster.  Questi vengono perché i contesti sono preimpostati, è sufficiente chiudere i blocchi appunti non interrompere il contesto e quindi le risorse cluster continuerà a essere in uso. Buona norma vengono PySpark e motori, è possibile utilizzare l'opzione **Chiudi e interruzione** dal menu **File** del blocco appunti. Si interrompe il contesto e quindi si chiude il blocco appunti.    


## <a name="show-me-some-examples"></a>Mostra alcuni esempi

Quando si apre un blocco appunti Jupyter, si vedrà due cartelle a livello radice.

* La cartella **PySpark** è i blocchi appunti di esempio che utilizzano la nuova **Python** .
* La cartella di **Scala** è i blocchi appunti di esempio che utilizzano la nuova **motori** .

È possibile aprire il blocco appunti **00 - [leggere per primo] ad speciale Kernel caratteristiche** dalla cartella **PySpark** o **motori** per conoscere i diversi magics disponibili. È possibile usare anche altri notebook esempio disponibile in due cartelle per informazioni su come ottenere diversi scenari di utilizzo di blocchi appunti Jupyter con i cluster HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Dove vengono archiviati i blocchi appunti?

I blocchi appunti Jupyter vengono salvati nell'account di archiviazione associato al cluster nella cartella **/HdiNotebooks** .  I blocchi appunti, file di testo e cartelle create dall'interno Jupyter sarà accessibile dal WASB.  Ad esempio, se si utilizza Jupyter per creare una cartella **MiaCartella** e un blocco appunti **myfolder/mynotebook.ipynb**, è possibile accedere al blocco appunti `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  Anche l'operazione true, vale a dire se si carica un blocco appunti direttamente al proprio account di archiviazione su `/HdiNotebooks/mynotebook1.ipynb`, il blocco appunti sarà visibile da Jupyter anche.  I blocchi appunti rimarrà nell'account di archiviazione anche dopo il raggruppamento viene eliminato.

La modalità di blocchi appunti vengono salvati nell'account di archiviazione è compatibile con HDFS. Questa operazione, se si SSH nel cluster che è possibile usare file di comandi di gestione simile al seguente:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


Nel caso in cui sono presenti problemi di accesso all'account di archiviazione per il cluster, i blocchi appunti anche salvati nel headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser supportati
I blocchi appunti Jupyter esecuzione su cluster HDInsight Spark sono supportati solo in Google Chrome.

## <a name="feedback"></a>Commenti e suggerimenti

Nuovo vengono è in evoluzione secondario e verrà scadono nel tempo. Ciò vuol che API soggetta a questi vengono scadono. Grazie eventuali commenti e suggerimenti contenenti durante l'uso di questi vengono nuovo. Ciò potrebbe risultare molto utile nel modellare la versione finale di questi. x. È possibile lasciare i commenti/commenti nella sezione **commenti** nella parte inferiore di questo articolo.


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

* [Motori di apprendimento: usare i in HDInsight per l'analisi temperatura predefiniti utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log sito Web tramite motori in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Plug-in strumenti di HDInsight per IntelliJ IDEA consente di creare e inviare Scala ad applicazioni](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare plug-in strumenti di HDInsight per IntelliJ IDEA il debug delle applicazioni di motori in modalità remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i blocchi appunti Zeppelin con un cluster di motori su HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)
