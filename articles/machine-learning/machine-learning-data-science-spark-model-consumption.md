<properties
    pageTitle="Punteggio dei modelli di apprendimento integrato ad machine | Microsoft Azure"
    description="Come modelli di apprendimento punteggio archiviati in archiviazione Blob Azure (WASB)."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="score-spark-built-machine-learning-models"></a>Punteggio dei modelli di apprendimento integrato ad computer 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

In questo argomento viene descritto come caricare i modelli di formazione (ML) computer creata utilizzando i MLlib e archiviati in archiviazione Blob Azure (WASB) e sulla loro punteggio con set di dati inoltre memorizzati in WASB. Viene illustrato come pre-elaborazione dei dati di input, trasformare caratteristiche utilizzando le funzioni di indicizzazione e codifica Toolkit MLlib e come creare un oggetto di dati decimale con etichette che può essere utilizzato come input per il punteggio con i modelli di ML. I modelli utilizzati per il punteggio includono regressione lineare, regressione logistica, casuale foresta modelli e sfumatura incrementare albero modelli.


## <a name="prerequisites"></a>Prerequisiti

1. È necessario un account Azure e un HDInsight motori è necessario un 1.6 di motori 3.4 HDInsight cluster per completare questa procedura dettagliata. Vedere la [Panoramica dei dati scienza utilizzando motori su Azure HDInsight](machine-learning-data-science-spark-overview.md) per istruzioni su come tali requisiti. Questo argomento contiene anche una descrizione dei dati Roma 2013 Taxi utilizzati qui e istruzioni su come eseguire il codice da un blocco appunti Jupyter cluster motori. Il blocco appunti **pySpark-machine-learning-data-science-spark-model-consumption.ipynb** che contiene gli esempi di codice in questo argomento è disponibile in [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. È necessario creare anche i modelli da valutare qui l'esecuzione di sezione [esplorazione dei dati e modellazione con motori](machine-learning-data-science-spark-data-exploration-modeling.md) di apprendimento.   


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installazione: percorsi di archiviazione, raccolte e il contesto di motori predefinito

Motori è in grado di leggere e scrivere a un provider di servizi Internet (WASB, Azure lo spazio di archiviazione Blob). In modo che i dati esistenti archiviati sono possono essere elaborati utilizzando vivacità ed i risultati nuovamente archiviati in WASB.

Per salvare i modelli o file in WASB, è necessario specificare correttamente il percorso. Il contenitore predefinito associato al cluster motori è possibile fare riferimento utilizzando un percorso che inizia con: *"wasb / /"*. Nell'esempio seguente specifica la posizione dei dati per la lettura e il percorso della directory di archiviazione modello al quale l'output di modello viene salvato. 


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Impostare i percorsi di directory per i percorsi di archiviazione in WASB

I modelli vengono salvati in: "wasb: / / / / remoteuser/NYCTaxi/modelli utente". Se il percorso seguente non è impostato correttamente, i modelli non vengono caricati per il punteggio.

I risultati punteggio sono stati salvati: "wasb: / / / utente/remoteuser/NYCTaxi/ScoredResults". Se il percorso cartella non è corretto, i risultati non vengono salvati nella cartella.   


>[AZURE.NOTE] Possano copiare e incollare nei segnaposto questo codice dall'output dell'ultima cella del blocco appunti **machine-learning-data-science-spark-data-exploration-modeling.ipynb** percorsi file.   


Ecco il codice per impostare i percorsi di directory: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
    
    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
    
    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)


### <a name="import-libraries"></a>Librerie di importazione

Impostare il contesto di motori e importare necessarie raccolte con il seguente codice

    #IMPORT LIBRARIES
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

La x PySpark forniti con i blocchi appunti Jupyter avere un contesto predefinito. Pertanto non è necessario impostare i motori o lo sviluppo di Hive contesti in modo esplicito prima di iniziare a utilizzare l'applicazione. Questi sono disponibili per impostazione predefinita. Questi contesti sono:

- SC - per motori 
- sqlContext - per Hive

Il kernel PySpark fornisce alcuni predefiniti "magics", che sono comandi speciali che è possibile chiamare con % %. Sono disponibili due tali comandi usati in questi esempi di codice.

- **% % locale** Specificare che il codice nelle righe successive viene eseguito in locale. Codice deve essere codice Python valido.
- **% % sql -o<variable name>** 
- Esegue una query Hive il sqlContext. Se il parametro -o viene passato, il risultato della query viene mantenuto nella % contesto Python locale come un dataframe Pandas.
 

Per ulteriori informazioni sulla x per i blocchi appunti Jupyter e predefinito "magics" che forniscono, vedere [disponibili per i blocchi appunti Jupyter con i cluster Linux ad HDInsight su HDInsight. X](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Acquisire dati e creazione di un intervallo di dati pulito

In questa sezione contiene il codice per una serie di attività necessarie per acquisire i dati da valutare. Leggere un campione di 0,1% Uniti del taxi viaggio e tariffa file (archiviato come file tsv), formattare i dati e quindi viene creato un intervallo di dati ordinata.

I file di viaggio e tariffa taxi sono stati uniti in base nella procedura illustrata nella: [il processo di scienze di dati di Team in azione: utilizzo dei cluster HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) argomento.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
        
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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
    
    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo per eseguire sopra la cella: secondi 46.37


## <a name="prepare-data-for-scoring-in-spark"></a>Preparare i dati per il punteggio in motori 

In questa sezione viene illustrato come indicizzare, codificare e ridimensionare le funzionalità per categoria per prepararli per l'utilizzo in algoritmi MLlib controllati risorse per la classificazione e regressione.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Portare in primo piano trasformazione: indicizzare e codificare funzioni per categoria quando i dati immessi nei modelli per il punteggio 

In questa sezione viene illustrato come l'indicizzazione per categoria dati utilizzando un `StringIndexer` e codificare caratteristiche esclusive per database `OneHotEncoder` nei modelli di input.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica una colonna di stringhe di etichette a una colonna di indici etichetta. Gli indici vengono ordinati in frequenza etichetta. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con almeno un singolo uno-valore. Questa codifica consente algoritmi aspettarsi caratteristiche valutati continue, come regressione logistica, da applicare alle caratteristiche per categoria.
    
    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()
    
    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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
    
    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo per eseguire sopra la cella: 5,37 secondi


### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Creare RDD oggetti con le matrici caratteristica di input nei modelli

In questa sezione contiene codice che illustra come indicizzare i dati di testo per categoria come oggetto RDD e hot uno codificare in modo che può essere utilizzato per organizzare la formazione e testare regressione logistica MLlib e modelli di struttura. Dati indicizzati vengono archiviati in oggetti [Flessibili distribuito set di dati (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . Ecco l'estrazione di base in motori. Oggetto RDD rappresenta un insieme non modificabile, partizionato di elementi che può essere gestito in parallelo con motori.

Contiene anche il codice che illustra come ridurre i dati con la `StandardScalar` forniti da MLlib per l'utilizzo di regressione lineare con stocastico sfumatura discesa (SGD), un algoritmo comuni per un'ampia gamma di modelli di computer risorse di formazione. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) viene utilizzato per le caratteristiche per la varianza di unità. Funzionalità di scalabilità, noto anche come normalizzazione dei dati, assicura che funzioni con valori ampiamente esborsare sono non dato eccessiva valutare nella funzione obiettiva. 


    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
    
    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
    
    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
    
    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo per eseguire sopra la cella: 11.72 secondi


## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Punteggio con il modello di regressione logistica e salvare l'output per blob

Il codice in questa sezione viene illustrato come caricare un modello di regressione logistica è stato salvato in archiviazione blob Azure e usarlo per prevedere o meno un suggerimento è pagato per estinguere un viaggio taxi, punteggio con metriche di classificazione standard, quindi salvare e tracciare i risultati a archiviazione blob. I risultati punteggio vengono memorizzati in oggetti RDD. 


    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
    
    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tempo per eseguire sopra la cella: secondi 19.22


## <a name="score-a-linear-regression-model"></a>Punteggio un modello di regressione lineare

È stato usato [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) per formare un modello di regressione lineare utilizzando stocastico sfumatura discesa (SGD) per l'ottimizzazione per stimare la quantità di suggerimento pagato. 

Il codice in questa sezione viene illustrato come caricare un modello di regressione lineare dallo spazio di archiviazione blob Azure, punteggio utilizzando variabili in scala e quindi salvare i risultati al blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
    
    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT:**

Tempo per eseguire sopra la cella: secondi 16.63


## <a name="score-classification-and-regression-random-forest-models"></a>Punteggio modelli foresta casuale classificazione e regressione

Il codice in questa sezione viene illustrato come caricare la classificazione salvata e regressione casuale foresta modelli salvati in archiviazione blob Azure, punteggio le prestazioni con classificatore standard e misure di regressione e salvare i risultati nuovamente archiviazione blob.

[Casuale insiemi](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sono per ragazzo di decisionali.  Combinano molti decisionali per ridurre il rischio di sovradattamento. Insiemi casuali possono gestire le funzionalità per categoria, estendere l'impostazione di classificazione multiclass, non richiedono funzionalità di scalabilità e sono in grado di acquisire non linearità e interazioni portare in primo piano. Insiemi casuali sono uno dei più efficace apprendimento modelli per la classificazione e regressione.

[Spark.mllib](http://spark.apache.org/mllib/) supporta insiemi casuale per la classificazione binaria e multiclass e di regressione, con le funzionalità continua e per categoria. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES 
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tempo per eseguire sopra la cella: secondi 31.07


## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Punteggio classificazione e regressione sfumatura incremento della struttura modelli

Il codice in questa sezione viene illustrato come caricare classificazione regressione sfumatura incrementare albero modelli e dallo spazio di archiviazione blob Azure, punteggio le prestazioni con classificatore standard e misure di regressione e quindi salvare i risultati in archiviazione blob. 

**Spark.mllib** supporta GBTs per la classificazione binaria e di regressione, con le funzionalità continua e per categoria. 

[Alberi di aumento della sfumati](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sono per ragazzo di decisionali. GBTs formare decisionali in modo iterativo per ridurre a icona una funzione di perdita. GBTs può gestire caratteristiche per categoria, non richiedono funzionalità di scalabilità e sono in grado di acquisire non linearità e interazioni portare in primo piano. Possono inoltre essere utilizzati in un'impostazione di classificazione multiclass.


    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
    
**OUTPUT:**

Tempo per eseguire sopra la cella: 14.6 secondi


## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Pulire gli oggetti dalla memoria e stampare posizioni dei file punteggio

    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**OUTPUT:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt



## <a name="consume-spark-models-through-a-web-interface"></a>Utilizzare i modelli tramite un'interfaccia web

Motori fornisce un meccanismo per l'invio in remoto processi batch o query interattive tramite un'interfaccia resto con un componente denominato inserire il. Inserire il è attivato per impostazione predefinita il cluster HDInsight Spark. Per ulteriori informazioni su inserire il vedere: [inviare i processi in modalità remota utilizzando inserire il](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md). 

È possibile utilizzare inserire il per in remoto invia un processo che batch sugli indici di un file archiviato in un archivio blob Azure e quindi scrive i risultati in un altro blob. A questo scopo è caricare lo script Python da  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) a quello del cluster ad. È possibile utilizzare uno strumento come **Microsoft Azure archiviazione Explorer** o **AzCopy** per copiare lo script blob cluster. In questo caso è caricato lo script ***wasb:///example/python/ConsumeGBNYCReg.py***.   


>[AZURE.NOTE] I tasti che è necessario sono disponibili nel portale per l'account di archiviazione associato al cluster motori. 


Dopo aver caricato in questa posizione, questo script viene eseguita all'interno del cluster di motori in un contesto distribuito. Carica il modello ed eseguire le stime del file di input in base al modello.  

È possibile richiamare questo script in remoto effettuando una richiesta HTTPS/resto semplice in inserire il.  Ecco un comando curvatura per creare la richiesta HTTP per richiamare lo script Python in remoto. Sostituire i valori appropriati per il cluster ad CLUSTERLOGIN, CLUSTERPASSWORD, nome cluster.


    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

È possibile utilizzare qualsiasi lingua del sistema remoto per richiamare il lavoro ad tramite inserire il effettuando una chiamata HTTPS semplice con l'autenticazione di base.   


>[AZURE.NOTE] Sarebbe utile usare la raccolta di Python richieste quando si effettua la chiamata HTTP, ma non è attualmente installato per impostazione predefinita in Azure funzioni. In modo che le librerie HTTP precedente vengono utilizzate invece.   


Ecco il codice Python per la chiamata HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64
    
    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'
    
    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
    
    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


È anche possibile aggiungere questo codice Python alle [Funzioni di Azure](https://azure.microsoft.com/documentation/services/functions/) per attivare l'invio di un processi motori che punteggi blob in base a vari eventi timer, creazione o aggiornamento di un blob. 

Se si preferisce un'esperienza gratuito del client di codice, usare le [App di logica di Azure](https://azure.microsoft.com/documentation/services/app-service/logic/) per richiamare il batch di motori punteggio la definizione di un'azione HTTP su **Logica App Designer** e impostando parametri. 

- Dal portale di Azure, creare una nuova App logica selezionando **+ Nuovo** -> **Web + Mobile** -> **Logica App**. 
- Per visualizzare la **Logica App Designer**, immettere il nome della logica di App e App piano di servizio.
- Selezionare un'azione HTTP e immettere i parametri mostrati nella figura riportata di seguito:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## <a name="whats-next"></a>Che cos'è successiva? 

**Convalida incrociata e hyperparameter completa**: vedere [Avanzate esplorazione dei dati e modellazione con motori](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) su come modelli possono essere qualificato con sweep convalida incrociata e hyper parametro.
