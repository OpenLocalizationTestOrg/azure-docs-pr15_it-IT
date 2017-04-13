<properties
   pageTitle="Analizzare e JSON processo documenti con Hive in HDInsight | Microsoft Azure"
   description="Informazioni su come usare documenti JSON e analizzarli mediante Hive in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Elaborare e analizzare i documenti JSON utilizzando Hive in HDInsight

Informazioni su come elaborare e analizzare i file JSON usando Hive in HDInsight. Il documento JSON seguente verrà utilizzato nell'esercitazione

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

Il file si trova in wasbs://processjson@hditutorialdata.blob.core.windows.net/. Per ulteriori informazioni sull'utilizzo di archiviazione Blob Azure con HDInsight, vedere [lo spazio di archiviazione Blob Azure di usare HDFS compatibile con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). Se si desidera, è possibile copiare il file per il contenitore predefinito del cluster.

In questa esercitazione si utilizzerà la console Hive.  Per istruzioni di apertura console Hive, vedere [Usare Hive con Hadoop in HDInsight con Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Unisci documenti JSON

I metodi elencati nella sezione successiva richiedono la JSON del documento in una singola riga. Pertanto è necessario unire il documento JSON da una stringa. Se il documento JSON già unito, è possibile ignorare questo passaggio e passare direttamente alla sezione successiva sui dati di analisi JSON.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

File JSON non elaborato si trova in **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La tabella Hive *StudentsRaw* punta al documento JSON piatto non elaborato.

La tabella Hive *StudentsOneLine* archiviano i dati nel file system predefinito HDInsight in ** /json/studenti/percorso.

L'istruzione INSERT popolare la tabella StudentOneLine con i dati JSON piatta.

L'istruzione SELECT deve restituire solo la 1 riga.

Ecco l'output dell'istruzione SELECT:

![Conversione del documento JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive

Hive sono disponibili tre diversi meccanismi per eseguire query su documenti JSON:

- Utilizzare il metodo GET\_JSON\_oggetto utente (funzione definita dall'utente)
- usare l'utente JSON_TUPLE
- Utilizzare SerDe personalizzato
- scrivere che si è proprietari utente tramite Python o altre lingue. Vedere [l'articolo] [ hdinsight-python] in esecuzione di codice Python con Hive.

### <a name="use-the-getjsonobject-udf"></a>Utilizzare il metodo GET\_JSON_OBJECT utente
Hive fornisce che una predefiniti utente chiamato [ottenere oggetto json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) che possono essere eseguite JSON query in fase di esecuzione. Questo metodo accetta due argomenti: il nome della tabella e il nome di metodo che contiene il documento JSON piatto e il campo JSON che deve essere da analizzare. Esaminiamo un esempio per vedere come funziona questo utente.

Ottenere il nome e cognome per ogni studente

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Ecco l'output durante l'esecuzione di questa query nella finestra console.

![get_json_object utente][image-hdi-hivejson-getjsonobject]

Esistono alcune limitazioni dell'utente get-json_object.

- Dato che ogni campo nella query richiede un'analisi nuovamente la query, influenza le prestazioni.
- OTTENERE\_JSON_OBJECT() restituisce la rappresentazione di stringa di una matrice. Per convertire una matrice Hive, sarà necessario utilizzare espressioni regolari per sostituire le parentesi quadre ' ["e"] "e quindi chiamare anche diviso per ottenere la matrice.


Questo è il motivo per cui wiki Hive consiglia json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Usare l'utente JSON_TUPLE

Un altro utente fornito dall'Hive rappresenta la cosiddetta [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) prestazioni migliori [Object json get _](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un insieme di chiavi e una stringa JSON e restituisce una tupla di valori mediante una funzione. La query seguente restituisce l'id di studenti e la classificazione dal documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

L'output di questo script nella console di Hive:

![json_tuple utente][image-hdi-hivejson-jsontuple]

JSON\_TUPLA utilizza la sintassi di [visualizzazione laterale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) nell'Hive che consente di json\_tupla per creare una tabella virtuale applicando la funzione di tipo definito dall'utente per ogni riga della tabella originale.  Complesso JSONs diventare troppo difficile da gestire perché ripetuta uso della visualizzazione laterale. Inoltre, JSON_TUPLE non è possibile gestire JSONs annidate.


###<a name="use-custom-serde"></a>Utilizzare SerDe personalizzato

SerDe è la scelta migliore per l'analisi documenti JSON nidificati, consente di definire lo schema JSON e utilizzare lo schema per analizzare i documenti. In questa esercitazione si utilizzerà uno dei SerDe più comuni che è stato sviluppato da [rcongiu](https://github.com/rcongiu).

**Per usare SerDe personalizzato:**

1. Installare [sviluppo SE Java Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Scegliere la versione di Windows X64 di JDK se si prevede di utilizzare la distribuzione di Windows di HDInsight

    >[AZURE.WARNING] 1.8 JDK non funziona con questo SerDe.

    Al termine dell'installazione, aggiungere una nuova variabile di ambiente utente:

    1. Aprire **impostazioni di sistema avanzate di visualizzazione** nella schermata di Windows.
    2. Fare clic su **variabili**.  
    3. Aggiungere che una nuova variabile di ambiente **JAVA_HOME** fa riferimento a **C:\Programmi\Microsoft Files\Java\jdk1.7.0_55** o nel punto in cui è installato il JDK.

    ![Impostazione dei valori di configurazione corretta per JDK][image-hdi-hivejson-jdk]

2. Installare [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Aggiungere la cartella bin al percorso di facendo clic su controllo pannello--> Modifica le variabili di sistema per le variabili di Environment account. Nella schermata seguente viene illustrato come eseguire questa operazione.

    ![La configurazione Maven][image-hdi-hivejson-maven]

3. Duplicare il progetto dal sito github [SerDe JSON Hive](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . È possibile eseguire questa operazione, fare clic sul pulsante "Zip di Download" come illustrato nella figura riportata di seguito.

    ![La duplicazione del progetto][image-hdi-hivejson-serde]

4: passare alla cartella in cui è stato scaricato questo package e tipo "mvn". Verrà creato i file vaso necessarie che è possibile copiare quindi sopra al cluster.

5: passare alla cartella di destinazione presente nella cartella radice in cui è stato scaricato il pacchetto. Caricare il file json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar testa nodo del cluster. In genere inserite nella cartella binario hive: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin o qualcosa di simile.

6: l'hive al prompt dei comandi digitare "Aggiungi vaso /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Poiché in questo caso, vaso nella cartella C:\apps\dist\hive-0.13.x\bin, è possibile aggiungere direttamente vaso con il nome, come illustrato di seguito:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

A questo punto, si è pronti a utilizzare il SerDe per eseguire query su documento JSON.

L'istruzione seguente creare una tabella con uno schema definito

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Per visualizzare un elenco il nome e il cognome dello studente

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Ecco il risultato dalla console di Hive.

![Query SerDe 1][image-hdi-hivejson-serde_query1]

Per calcolare la somma dei punteggi del documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La query precedente utilizza [visualizzazione laterale esplodere](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) utente per espandere la matrice di punteggi in modo che possono essere sommati.

Ecco l'output dalla console di Hive.

![Query SerDe 2][image-hdi-hivejson-serde_query2]

Per trovare un determinato studente ha totalizza un punteggio 80 di più argomenti selezionare  
      JT. StudentClassCollection.ClassId da json_table jt laterale visualizzazione esplodere (JT. Insieme di StudentClassCollection.Score) come punteggio nel punto in cui punteggio > 80;

La query precedente restituisce una matrice Hive diversamente da quanto succede get\_json\_oggetto che restituisce una stringa.

![Query SerDe 3][image-hdi-hivejson-serde_query3]

Se si desidera skil JSON non valido, quindi come descritto nella [pagina wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) di questo SerDe è possibile ottenere che digitando il codice riportato di seguito:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Riepilogo
In conclusione, il tipo di operatore JSON nell'Hive che si è scelto dipende dallo scenario. Se si dispone di un documento JSON semplice e si dispone solo un campo da cercare nella – è possibile scegliere di utilizzare get Hive utente\_json\_oggetto. Se si usano più monitor tasti per cercare è possibile utilizzare json_tuple. Se si dispone di un documento nidificato, è necessario utilizzare SerDe JSON.

Per altri articoli correlati, vedere

- [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file di esempio Apache log4j](hdinsight-use-hive.md)
- [Analizzare i dati dei ritardi volo usando Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analizzare i dati di Twitter utilizzando Hive in HDInsight](hdinsight-analyze-twitter-data.md)
- [Eseguire un processo Hadoop utilizzando DocumentDB e HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
