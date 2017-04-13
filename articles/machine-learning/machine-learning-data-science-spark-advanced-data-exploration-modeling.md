<properties
    pageTitle="Esplorazione dei dati e modellazione con motori avanzate | Microsoft Azure"
    description="Utilizzare HDInsight Spark eseguire esplorazione dei dati e formare binari modelli di classificazione e regressione utilizzando convalida incrociata e hyperparameter l'ottimizzazione."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Esplorazione dei dati avanzata e modellazione con motori 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

In questa procedura dettagliata utilizza HDInsight Spark eseguire esplorazione dei dati e formare classificazione binaria e i modelli di regressione utilizzando la convalida cross e ottimizzazione hyperparameter un campione del Roma taxi viaggio e presentare 2013 set di dati. Illustra i passaggi del [Processo di scienze dei dati](http://aka.ms/datascienceprocess), -to-end, utilizzando una HDInsight Spark cluster per l'elaborazione e Azure BLOB per archiviare i dati e i modelli. Il processo di Esplora e visualizza i dati importati da un Blob di spazio di archiviazione di Azure e quindi prepara i dati per la creazione di modelli di previsione. Python è stato utilizzato per il codice della soluzione e mostrare posti pertinenti. Questi modelli sono compilazione utilizzando il toolkit di MLlib ad eseguire le attività di modellazione di regressione e classificazione binaria. 

- L'attività di **classificazione binaria** consiste nel prevedere o meno un suggerimento è pagato per il viaggio. 
- L'attività di **regressione** è per stimare la quantità di suggerimento in base alle altre caratteristiche di suggerimento. 

La procedura di modellazione inoltre contiene codice illustrano come organizzare la formazione, valutare e salvare ogni tipo di modello. L'argomento vengono illustrate alcune delle terra stesso come argomento di [esplorazione dei dati e modellazione con motori](machine-learning-data-science-spark-data-exploration-modeling.md) . Ma si è più "avanzato" in quanto convalida incrociata viene inoltre utilizzato in combinazione con hyperparameter completa per formare modelli di classificazione e regressione ottimale accurati. 

**Convalida incrociata (CV)** è una tecnica che valuta come anche un modello di formazione su un set di dati noto generalizza a prevedere le caratteristiche di set di dati in cui si dispone non le competenze. L'idea generale questa tecnica è che è stato eseguito su un set di dati di dati noti in un modello e quindi viene eseguita la precisione delle previsioni rispetto a un set di dati indipendente. Un'implementazione comune utilizzata in questo esempio è dividere un set di dati K biglietto e quindi formare il modello in circolare su tutti tranne uno all'interno. 

**L'ottimizzazione Hyperparameter** è il problema della scelta di un set di hyperparameters per un algoritmo di risorse, in genere con l'obiettivo di ottimizzazione una misura delle prestazioni dell'algoritmo su un set di dati indipendente. **Hyperparameters** sono valori che è necessario specificare di fuori della procedura di formazione su modello. Ipotesi con questi valori possono influire della flessibilità accuratezza dei modelli. Decisionali hyperparameters, ad esempio, ad esempio sono profondità desiderata e il numero di foglie nella struttura. Supporto vettore computer (SVMs) richiedono l'impostazione di un termine di riduzione di errore di classificazione. 

Più raramente per eseguire l'ottimizzazione hyperparameter utilizzata in questo esempio è una ricerca di griglia o un' **Organizza parametro**. Si tratta di eseguire una ricerca approfondita tramite i valori di un subset di spazio hyperparameter specificato per un algoritmo di apprendimento. Convalida cross può fornire una metrica prestazioni per ordinare i risultati ottimali prodotti dall'algoritmo di ricerca della griglia. CV usato con accessibilitàhttp radicali hyperparameter problemi limite overfitting un modello di dati di formazione, in modo che il modello mantiene la capacità di applicare l'insieme generale di dati da cui è stato estratto i dati di formazione.

I modelli che serve includono regressione logistica e lineare, casuale insiemi di strutture e strutture invece un incremento sfumature:

- [Regressione lineare con SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) è un modello di regressione lineare che utilizza un metodo stocastico discesa sfumatura (SGD) e proporzioni dei caratteri per prevedere gli importi di suggerimento pagato per l'ottimizzazione e funzionalità. 
- Regressione [regressione logistica con LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) o "logit", è un modello di regressione che può essere utilizzato quando la variabile dipendente è per categoria eseguire classificazione di dati. LBFGS è un algoritmo di ottimizzazione Pisani periodo di quasi che si avvicina algoritmo Broyden-Fletcher-Goldfarb – Shanno (BFGS) utilizzando un numero limitato di memoria del computer e che usato in apprendimento.
- [Casuale insiemi](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sono per ragazzo di decisionali.  Combinano molti decisionali per ridurre il rischio di sovradattamento. Insiemi casuali vengono utilizzati per regressione e classificazione e possono gestire caratteristiche per categoria e possono essere esteso per l'impostazione di classificazione multiclass. Utenti che non richiedono funzionalità di scalabilità e sono in grado di acquisire non linearità e portare in primo piano interazioni. Insiemi casuali sono uno dei più efficace apprendimento modelli per la classificazione e regressione.
- [Sfumatura aumentata alberi](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sono per ragazzo di decisionali. GBTs formare decisionali in modo iterativo per ridurre a icona una funzione di perdita. GBTs vengono utilizzati per regressione e classificazione e può gestire caratteristiche per categoria, non richiedono funzionalità di scalabilità e sono in grado di acquisire non linearità e interazioni portare in primo piano. Possono inoltre essere utilizzati in un'impostazione di classificazione multiclass.

Esempi di utilizzo CV e Hyperparameter di modellazione organizza vengono visualizzate per il problema di classificazione binario. Nell'argomento principale per le attività di regressione sono illustrati esempi più semplici (senza parametro introduce). Ma in appendice, vengono illustrata la convalida utilizzando net flessibile di regressione lineare e CV con parametro Organizza di regressione foresta casuali. **Rete flessibile** è un metodo di regressione regolarizzarne di montaggio dei modelli di regressione lineare che combina lineare metriche L1 e L2 come sanzioni dei metodi [lazo](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) e [in rilievo](https://en.wikipedia.org/wiki/Tikhonov_regularization) .   



>[AZURE.NOTE] Sebbene il toolkit di motori MLlib è progettato allo svolgimento di grandi dimensioni set di dati, un campione relativamente (circa 30 Mb con righe 170K, circa 0,1% del set di dati di Roma originale) viene utilizzato qui per comodità. Esercizio fornita di seguito viene eseguito in modo efficiente (in circa 10 minuti) in un cluster di HDInsight con 2 nodi di lavoro. Lo stesso codice, con le modifiche secondarie, può essere utilizzato per elaborare più grande-set di dati, con le modifiche appropriate per la memorizzazione nella cache dei dati in memoria e modificare la dimensione cluster.


## <a name="prerequisites"></a>Prerequisiti

È necessario un account Azure e un HDInsight motori è necessario un 1.6 di motori 3.4 HDInsight cluster per completare questa procedura dettagliata. Vedere la [Panoramica dei dati scienza utilizzando motori su Azure HDInsight](machine-learning-data-science-spark-overview.md) per istruzioni su come tali requisiti. Questo argomento contiene anche una descrizione dei dati Roma 2013 Taxi utilizzati qui e istruzioni su come eseguire il codice da un blocco appunti Jupyter cluster motori. Il blocco appunti **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** che contiene gli esempi di codice in questo argomento è disponibile in [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installazione: percorsi di archiviazione, raccolte e il contesto di motori predefinito

Motori sono in grado di leggere e scrivere Azure lo spazio di archiviazione Blob (noto anche come WASB). In modo che i dati esistenti archiviati sono possono essere elaborati utilizzando vivacità ed i risultati nuovamente archiviati in WASB.

Per salvare i modelli o file in WASB, è necessario specificare correttamente il percorso. Il contenitore predefinito associato al cluster motori è possibile fare riferimento utilizzando un percorso che inizia con: "wasb: / / /". Altri percorsi fanno riferimento "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Impostare i percorsi di directory per i percorsi di archiviazione in WASB

Nell'esempio seguente specifica la posizione dei dati per la lettura e il percorso della directory di archiviazione modello in cui è salvato l'output di modello:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)


### <a name="import-libraries"></a>Librerie di importazione

Consente di importare necessarie raccolte con il codice seguente:

    # LOAD PYSPARK LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime
    

### <a name="preset-spark-context-and-pyspark-magics"></a>Contesto di motori preimpostato e PySpark magics

La x PySpark forniti con i blocchi appunti Jupyter avere un contesto predefinito. Pertanto non è necessario impostare i motori o lo sviluppo di Hive contesti in modo esplicito prima di iniziare a utilizzare l'applicazione. Contesti seguenti sono disponibili per impostazione predefinita. Questi contesti sono:

- SC - per motori 
- sqlContext - per Hive

Il kernel PySpark fornisce alcuni predefiniti "magics", che sono comandi speciali che è possibile chiamare con % %. Sono disponibili due tali comandi usati in questi esempi di codice.

- **% % locale** Specifica che il codice nelle righe successive è possibile eseguire in locale. Codice deve essere codice Python valido.
- **%%sql -o <variable name>** Esegue una query Hive il sqlContext. Se il parametro -o viene passato, il risultato della query viene mantenuto nella % contesto Python locale come un DataFrame Pandas.
 

Per ulteriori informazioni sulla x per i blocchi appunti Jupyter e predefinito "magics" che forniscono, vedere [disponibili per i blocchi appunti Jupyter con i cluster Linux ad HDInsight su HDInsight. X](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="data-ingestion-from-public-blob"></a>Acquisizione di dati da blob pubblico: 

Il primo passaggio del processo di scienze dei dati è per acquisire i dati da analizzare origini in cui si trova nel esplorazione dei dati e un ambiente di modellazione. Questo ambiente è motori in questa procedura dettagliata. In questa sezione contiene il codice per completare una serie di attività:

- acquisire l'esempio di dati da modellare
- leggere il set di dati di input (archiviato come file tsv)
- formattare ed eliminare i dati
- creare e memorizzare nella cache oggetti (RDDs o frame di dati) in memoria
- la registrazione come una tabella temp nel contesto di SQL.

Ecco il codice di acquisizione di dati.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
        
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo per eseguire sopra la cella: secondi 276.62


## <a name="data-exploration--visualization"></a>Visualizzazione ed esplorazione dei dati 

Dopo aver creato i dati sono stati scaricati in motori, il passaggio successivo del processo di scienze dati consiste per ottenere più approfondita dei dati tramite esplorazione e di visualizzazione. In questa sezione è esaminare i dati di taxi mediante query SQL e tracciare le variabili di destinazione e potenziali caratteristiche per il controllo visivo. In particolare, è tracciare la frequenza di conteggi passeggeri taxi viaggi, specificare la frequenza degli importi di suggerimento e come suggerimenti variano in base tipo e importo del pagamento.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracciare un istogramma della frequenza di conteggio passeggeri nel campione di trip taxi

Questo codice e i frammenti di codice successive utilizzare chiave SQL per il campione e speciale locale per tracciare i dati della query.

- **Chiave SQL (`%%sql`)** Il kernel HDInsight PySpark supporta la sqlContext query HiveQL all'interno del testo semplice. Il (-o nome_variabile) argomento mantiene l'output della query SQL sotto forma di una DataFrame Pandas nel server Jupyter. Di conseguenza, che è disponibile in modalità locale.
- Il ** `%%local` chiave** viene utilizzato per eseguire il codice in locale nel server Jupyter, ossia headnode del cluster HDInsight. In genere, si utilizza `%%local` particolare in combinazione con la `%%sql` strumento con parametro -o. Il parametro -o da mantenere l'output della query SQL in locale e quindi % locale speciale attiverà i successivi di frammento di codice per eseguire in locale per l'output della query SQL in modo permanente in locale

Output di cui verrà visualizzato automaticamente dopo l'esecuzione di codice.

La query recupera trip dal conteggio passeggeri. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Questo codice crea un intervallo di dati locale dall'output della query e vengono tracciati i dati. Il `%%local` speciale crea una cornice di dati locale, `sqlResults`, che può essere utilizzato per tracciare con matplotlib. 

>[AZURE.NOTE] Questa attività PySpark viene utilizzata più volte in questa procedura dettagliata. Se la quantità di dati è grande, deve di esempio per creare un intervallo di dati che può essere visualizzati nella cache locale.


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Ecco il codice per tracciare viaggi da passeggeri conteggi

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT**

![Frequenza di trip dal conteggio passeggeri](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

È possibile scegliere tra diversi tipi di visualizzazioni (tabella, grafici a torta, riga, Area o barra) utilizzando i pulsanti di menu **tipo** nel blocco appunti. Tracciato barra è illustrato di seguito.


### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tracciare un istogramma che mostra gli importi di suggerimento e come quantità di suggerimento varia in base ai valori conta. numeri e tariffa passeggeri.

Utilizzare una query SQL per i dati di esempio..
    
    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25
    

Questo codice nella cella viene utilizzata la query SQL per creare tre posti i dati.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    
    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()
    

**OUTPUT:** 

![Suggerimento per la distribuzione di importo](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Suggerimento per quantità, conteggio passeggeri](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Suggerimento per quantità, tariffa importo](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparazione di progettazione, la trasformazione e dati per una modellazione portare in primo piano

In questa sezione descrive e fornisce il codice per le procedure di preparare i dati per l'utilizzo in modellazione ML. Viene illustrato come eseguire le operazioni seguenti:

- Creare una nuova caratteristica di ore binning in intervalli di tempo il traffico
- Indicizzare e su hot codificare funzioni per categoria
- Creazione di oggetti point etichetta quando i dati immessi nelle funzioni ML
- Creare un sondaggio secondari dei dati e dividere in formazione e verifica set
- Funzionalità di scalabilità
- Oggetti cache in memoria


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Creare una nuova caratteristica di ore binning in intervalli di tempo il traffico

Questo codice viene illustrato come creare una nuova caratteristica di ore binning in intervalli di tempo il traffico e quindi come memorizzare nella cache frame di dati risultante in memoria. Quando sono utilizzati più volte flessibili distribuito set di dati (RDDs) e frame di dati, la memorizzazione nella cache conduce ad migliorato i tempi di esecuzione. Di conseguenza, nella cache RDDs e frame di dati in varie fasi della procedura dettagliata.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**OUTPUT**

126050


### <a name="index-and-one-hot-encode-categorical-features"></a>Indicizzare e hot uno codificare funzioni per categoria

In questa sezione viene illustrato come indicizzare o codificare funzioni per categoria quando i dati immessi nelle funzioni di modellazione. La modellazione e prevedere le funzioni di MLlib sono necessarie caratteristiche con dati di input per categoria essere indicizzate o codificato prima di utilizzare. 

A seconda del modello, è necessario indicizzare o li codifica in diversi modi. Ad esempio modelli Logistic e di regressione lineare richiedono hot una codifica, nel punto in cui, ad esempio, una caratteristica con tre categorie può essere espansi in tre colonne di funzionalità, con ogni contenente 0 o 1 a seconda della categoria di un osservazioni. MLlib offre funzione [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) per eseguire hot una codifica. Questo codificatore esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con almeno un singolo uno-valore. Questa codifica consente algoritmi aspettarsi le caratteristiche di valori numeriche, come regressione logistica, da applicare alle caratteristiche per categoria.

Ecco il codice di indice e codificare funzioni per categoria:


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo per eseguire sopra la cella: 3,14 secondi


### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Creazione di oggetti point etichetta quando i dati immessi nelle funzioni ML

In questa sezione contiene codice che illustra come indicizzare i dati di testo per categoria come tipo di dati decimale etichetta e la codifica in modo che può essere utilizzato per organizzare la formazione e testare regressione logistica MLlib e altri modelli di classificazione. Gli oggetti punto etichetta sono flessibili distribuito set di dati (RDD) formattate in modo che è necessario come dati di input per la maggior parte degli algoritmi ML in MLlib. Un' [etichetta punto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) è un vettore locale, densità o caricato in memoria bassa, associata a un'etichetta/risposta.

Ecco il codice di indice e codificare funzioni di testo per la classificazione binaria.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Ecco il codice per la codifica e indicizzare le caratteristiche di testo per categoria per l'analisi della regressione lineare.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Creare un sondaggio secondari dei dati e dividere in formazione e verifica set

Questo codice crea sondaggio di dati (25% viene usato qui). Anche se non è richiesta in questo esempio a causa delle dimensioni del set di dati, viene illustrato come è possibile di esempio qui per sapere quali da utilizzare per il proprio problema quando necessario. Dopo aver configurato esempi di grandi dimensioni, si può risparmiare molto tempo durante i modelli di formazione. Successivamente è dividere il campione in una parte di formazione (75%) e una parte di test (25%) da usare in classificazione e modellazione di regressione.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand
    
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tempo per eseguire sopra la cella: 0,31 secondi


### <a name="feature-scaling"></a>Funzionalità di scalabilità

Funzionalità di scalabilità, noto anche come normalizzazione dei dati, assicura che funzioni con valori ampiamente esborsare sono non dato eccessiva valutare nella funzione obiettiva. Il codice per il ridimensionamento caratteristica utilizzato [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) per ridimensionare le caratteristiche per la varianza di unità. Viene fornito da MLlib per l'utilizzo di regressione lineare con stocastico sfumatura discesa (SGD), un algoritmo più diffuso per formazione un'ampia gamma di altri modelli, ad esempio regolarizzarne regressione o supporto vettore macchine (SVM) di apprendimento.   

>[AZURE.TIP] È stato trovato algoritmo LinearRegressionWithSGD dalle proporzioni dei caratteri di portare in primo piano.   

Ecco il codice a scala variabili per l'utilizzo con l'algoritmo SGD lineare regularized.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tempo per eseguire sopra la cella: secondi 11.67


### <a name="cache-objects-in-memory"></a>Oggetti cache in memoria

Per la memorizzazione nella cache la cornice di dati di input e gli oggetti utilizzato per la classificazione, regressione, caratteristiche di architetture, è possibile ridurre il tempo necessario per la formazione e verifica degli algoritmi ML.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT** 

Tempo per eseguire sopra la cella: 0,13 secondi


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prevedere o meno un suggerimento è pagato con i modelli di classificazione binario

Questa sezione illustra come usare tre modelli per l'attività di classificazione binario di previsione è pagato un suggerimento per un viaggio taxi o meno. I modelli presentati sono:

- Regressione logistica 
- Casuale foresta
- Alberi di aumento della sfumati

Ogni modello creazione sezione codice viene suddiviso in passaggi: 

1. **Formazione su modello** dati con un set di parametro
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio modello** BLOB consumo futuro

Ecco come eseguire l'operazione di convalida incrociata (CV) con parametro completa in due modi:

1. Mediante il codice personalizzato **generico** che può essere applicato a qualsiasi algoritmo MLlib e a tutti i parametri set in un algoritmo. 
1. Utilizzare la **funzione pipeline CrossValidator pySpark**. Si noti che, pratico in base a esperienza, CrossValidator presenta alcune limitazioni per motori 1.5.0: 

    - Modelli di pipeline non possono essere salvate/mantenuta consumo futuro.
    - Non può essere usata per ogni parametro in un modello.
    - Non può essere usata per ogni algoritmo MLlib.


### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Generica cross convalida e sweep hyperparameter usato con l'algoritmo di regressione logistica per classificazione binaria

Il codice in questa sezione viene illustrato come formare, valutare e salvare un modello di regressione logistica con [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) che prevede o meno un suggerimento è pagato un viaggio il set di dati di Roma taxi viaggio e tariffa. Il modello è stato eseguito utilizzando cross convalida (CV) e sweep hyperparameter implementata con codice personalizzato che può essere applicato a uno degli algoritmi di apprendimento in MLlib.   

>[AZURE.NOTE] L'esecuzione di codice CV personalizzato può richiedere diversi minuti.

**Formare il modello di regressione logistica mediante CV e hyperparameter sweep**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    
    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)
    
    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric
    
    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
        
    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)
    
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))
    
    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Coefficienti: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intercetta:-0.0111216486893

Tempo per eseguire sopra la cella: secondi 14.43


**Valutare il modello di classificazione binario con i parametri di valutazione**

Il codice in questa sezione viene illustrato come valutare un modello di regressione logistica rispetto a un test-set di dati, inclusi un tracciato della curva ROC.


    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area sotto prezzo = 0.985336538462

Area sotto ROC = 0.983383274312

Statistiche di riepilogo

Precisione = 0.984174341679

Richiamare = 0.984174341679

F1 Punteggio = 0.984174341679

Tempo per eseguire sopra la cella: secondi 2.67


**Tracciare la curva ROC.**

*PredictionAndLabelsDF* registrato come una tabella, *tmp_results*nella cella precedente. *tmp_results* è utilizzabile per eseguire query e di output risultati nella cornice di dati sqlResults per il tracciato. Ecco il codice.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ecco il codice per rendere le stime e tracciare la curva di ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc
    
    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    # PLOT ROC CURVES
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**OUTPUT**

![Curva di regressione logistica ROC per approccio generico](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**Mantenere modello in un blob consumo futuro**

Il codice in questa sezione viene illustrato come salvare il modello di regressione logistica consumo.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    
    logitBest.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**OUTPUT**

Tempo per eseguire sopra la cella: secondi 34.57


### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Utilizzare funzione pipeline di CrossValidator del MLlib modello di regressione logistica (regressione flessibile)

Il codice in questa sezione viene illustrato come formare, valutare e salvare un modello di regressione logistica con [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) che prevede o meno un suggerimento è pagato un viaggio il set di dati di Roma taxi viaggio e tariffa. Il modello è stato eseguito utilizzando cross convalida (CV) e sweep hyperparameter implementata con la funzione di pipeline MLlib CrossValidator CV con organizza parametro.   


>[AZURE.NOTE] L'esecuzione di codice MLlib CV può richiedere diversi minuti.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc
    
    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT**

Tempo per eseguire sopra la cella: secondi 107.98


**Tracciare la curva ROC.**

*PredictionAndLabelsDF* registrato come una tabella, *tmp_results*nella cella precedente. *tmp_results* è utilizzabile per eseguire query e di output risultati nella cornice di dati sqlResults per il tracciato. Ecco il codice.


    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Ecco il codice per tracciare la curva ROC.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc
    
    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    #PLOT
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**OUTPUT**

![Curva ROC regressione logistica utilizzando CrossValidator del MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### <a name="random-forest-classification"></a>Classificazione casuale foresta

Il codice in questa sezione viene illustrato come formare, valutare e salvare una regressione foresta casuale che prevede o meno un suggerimento è pagato un viaggio il set di dati di Roma taxi viaggio e tariffa.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area sotto ROC = 0.985336538462

Tempo per eseguire sopra la cella: secondi 26.72


### <a name="gradient-boosting-trees-classification"></a>Classificazione di strutture aumento della sfumatura

Il codice in questa sezione viene illustrato come organizzare la formazione, valutare e salvare un modello di strutture aumento della sfumatura che prevede o meno un suggerimento è pagato un viaggio in viaggio taxi Roma e presentare i set di dati.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Area sotto ROC = 0.985336538462

Tempo per eseguire sopra la cella: secondi 28.13


## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prevedere importo suggerimento con i modelli di regressione (senza utilizzare CV)

Questa sezione illustra come usare tre modelli per l'attività di regressione di la previsione della punta pagato per un viaggio taxi basato su altre funzionalità di suggerimento. I modelli presentati sono:

- Regolarizzarne regressione lineare
- Casuale foresta
- Alberi di aumento della sfumati

Questi modelli sono stati descritti nell'introduzione. Ogni modello creazione sezione codice viene suddiviso in passaggi: 

1. **Formazione su modello** dati con un set di parametro
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio modello** BLOB consumo futuro   


>AZURE Nota: Convalida incrociata non viene usata con i modelli di tre regressione in questa sezione, poiché questo è stato illustrato in dettaglio per i modelli di regressione logistica. Nell'appendice di questo argomento viene fornito un esempio che mostra come usare CV con flessibile netto di regressione lineare.


>AZURE Nota: In esperienza, possono essere presenti problemi con convergenza dei modelli di LinearRegressionWithSGD e parametri devono essere modificate/ottimizzato con attenzione per ottenere un modello valido. Ridimensionamento delle variabili in modo significativo consente di convergenza. Flessibile regressione netta, illustrata nell'appendice di questo argomento, può essere utilizzata anche anziché LinearRegressionWithSGD.


### <a name="linear-regression-with-sgd"></a>Regressione lineare con SGD

Il codice in questa sezione viene illustrato come utilizzare funzionalità in scala per formare una regressione lineare che utilizza stocastico discesa sfumatura (SGD) per l'ottimizzazione e come punteggio, valutare e salvare il modello di archiviazione Blob Azure (WASB).

>[AZURE.TIP] In esperienza, possono essere presenti problemi con la convergenza dei modelli di LinearRegressionWithSGD e parametri devono essere modificate/ottimizzato con attenzione per ottenere un modello valido. Ridimensionamento delle variabili in modo significativo consente di convergenza.


    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Coefficienti: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Intercetta: 0.854507624459

RMSE = 1.23485131376

Sqr R = 0.597963951127

Tempo per eseguire sopra la cella: secondi 38.62


### <a name="random-forest-regression"></a>Regressione casuale foresta

Il codice in questa sezione viene illustrato come formare, valutare e salvare un modello di foresta casuale che prevede importo suggerimento per i dati di andata e ritorno taxi Roma.   

>[AZURE.NOTE] Convalida incrociata con parametro completa con codice personalizzato, vedere Appendice.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

RMSE = 0.931981967875

Sqr R = 0.733445485802

Tempo per eseguire sopra la cella: secondi 25.98


### <a name="gradient-boosting-trees-regression"></a>Sfumatura regressione alberi miglioramento

Il codice in questa sezione viene illustrato come formare, valutare e salvare un modello di strutture aumento della sfumatura che prevede importo suggerimento per i dati di andata e ritorno taxi Roma.

**Organizzare la formazione e valuta**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.928172197114

Sqr R = 0.732680354389

Tempo per eseguire sopra la cella: 20,9 secondi


**Tracciato**
    
*tmp_results* registrato come una tabella Hive nella cella precedente. Risultati della tabella vengono visualizzati nella cornice dati di *sqlResults* per il tracciato. Ecco il codice

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ecco il codice per tracciare i dati con il server Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np
    
    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Vs-stimati-suggerimento-gli importi effettivi](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Appendice: Attività di regressione aggiuntive utilizzando la convalida cross con parametro apertura

Viene indicato codice che mostra come effettuare CV utilizzando net flessibile di regressione lineare e su come eseguire CV organizza parametro con codice personalizzato di regressione foresta casuali.


### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Cross la convalida utilizzando elastici netto di regressione lineare

Il codice in questa sezione viene illustrato come cross la convalida utilizzando net flessibile di regressione lineare e come valutare il modello di dati di test.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    
    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 
    
    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
    
    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)
    
    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo per eseguire sopra la cella: secondi 161.21

**Valutare con metrica R SQR**

*tmp_results* registrato come una tabella Hive nella cella precedente. Risultati della tabella vengono visualizzati nella cornice dati di *sqlResults* per il tracciato. Ecco il codice

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Ecco il codice per calcolare R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats
    
    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

Sqr R = 0.619184907088


### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Cross convalida con organizza parametro con codice personalizzato di regressione casuale foresta

Il codice in questa sezione viene illustrato come cross convalida con organizza parametro con codice personalizzato di regressione foresta casuale e come valutare il modello di dati di test.


    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid
    
    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))
    
    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric
    
    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
            
    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.906972198262

Sqr R = 0.740751197012

Tempo per eseguire sopra la cella: secondi 69.17


### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Pulire gli oggetti dalla memoria e posizioni di modello di stampa

Usare `unpersist()` per eliminare gli oggetti memorizzati nella cache.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**OUTPUT**

PythonRDD [122] in RDD in PythonRDD.scala: 43


**Percorso di stampa di file di modello da utilizzare nel blocco appunti consumo.** Per utilizzare e punteggio un set di dati indipendente, è necessario copiare e incollare i nomi di file in "blocco appunti consumo".


    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Che cos'è successiva?

Una volta creato i modelli di regressione e la classificazione con MlLib motori, si è pronti imparare a punteggio e valutare questi modelli.

**Consumo del modello:** Per informazioni su come punteggio e valutare i modelli di classificazione e regressione creati in questo argomento, vedere [punteggio e valutare i modelli di apprendimento computer integrato ad](machine-learning-data-science-spark-model-consumption.md).
