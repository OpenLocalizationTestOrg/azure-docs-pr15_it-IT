## <a name="specifying-formats"></a>Specifica formati

Azure Data Factory supporta i tipi di formato seguenti: 

- [Formato testo](#specifying-textformat)
- [Formato JSON](#specifying-jsonformat)
- [Formato Avro](#specifying-avroformat)
- [Formato ORCO](#specifying-orcformat)
- [Formato parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Specifica formato testo

Se il formato è impostato su **formato testo**, è possibile specificare le seguenti proprietà **facoltativo** nella sezione **formato** .

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | Carattere utilizzato per separare le colonne in un file. | È consentito solo un carattere. Il valore predefinito è punto e virgola (','). <br/><br/>Per usare un carattere Unicode, fare riferimento ai [Caratteri Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) per ottenere il codice corrispondente per renderla. Ad esempio, è possibile usare un carattere non stampabile raro che probabilmente non esiste nei dati: specificare "\u0001" che rappresenta l'inizio del titolo rapporto di integrità. | No |
| rowDelimiter | Carattere utilizzato per separare le righe in un file. | È consentito solo un carattere. Il valore predefinito è uno dei seguenti valori in lettura: ["\r\n", "\r", "\n"] e "\r\n" durante la scrittura. | No |
| escapeChar | Carattere speciale utilizzato per interrompere un delimitatore di colonna nel contenuto del file di input. <br/><br/>Non è possibile specificare escapeChar e quoteChar per una tabella. | È consentito solo un carattere. Nessun valore predefinito. <br/><br/>Esempio: se si dispone di punto e virgola (', ') come il delimitatore di colonna ma si desidera, il carattere punto e virgola nel testo (esempio: "Hello, world"), è possibile definire '$' come carattere di escape e utilizzare stringa "$Hello, world" nell'origine. | No | 
| quoteChar | Carattere utilizzato per inserire un valore stringa. I delimitatori di colonna e riga all'interno di virgolette verrebbe considerati come parte del valore di stringa. Questa proprietà è applicabile al set di dati di input e di output.<br/><br/>Non è possibile specificare escapeChar e quoteChar per una tabella. | È consentito solo un carattere. Nessun valore predefinito. <br/><br/>Ad esempio, se si dispone di punto e virgola (', ') come il delimitatore di colonna, ma si vuole avere carattere virgola nel testo (esempio: < Hello, world >), è possibile definire "(virgolette) come la virgoletta e l'utilizzo della stringa"Hello, world"nell'origine. | No |
| nullValue | Uno o più caratteri utilizzati per rappresentare un valore null. | Uno o più caratteri. I valori predefiniti sono "\N" e "NULL" in lettura e "\N" durante la scrittura. | No |
| encodingName | Specificare il nome della codifica. | Un nome di codifica valido. vedere [Proprietà Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Esempio: windows 1250 o punti. Il valore predefinito è UTF-8. | No | 
| firstRowAsHeader | Specifica se tenere in considerazione la prima riga come intestazione. Per un set di dati di input, Data Factory legge prima riga come intestazione. Per un set di dati di output, Data Factory scrive prima riga come intestazione. <br/><br/>Per scenari di esempio, vedere [scenari di utilizzo di **firstRowAsHeader** e **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) . | Vero<br/>FALSO (impostazione predefinita) | No |
| skipLineCount | Indica il numero di righe da omettere quando si leggono dati dai file di input. Se sono specificati skipLineCount e firstRowAsHeader, le righe vengono ignorate prima di tutto e quindi le informazioni di intestazione viene letto dal file di input. <br/><br/>Per scenari di esempio, vedere [scenari di utilizzo di firstRowAsHeader e skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) . | Numero intero | No | 
| treatEmptyAsNull | Specifica se considerare una stringa vuota o null come un valore null quando si leggono dati da un file di input. | VERO (impostazione predefinita)<br/>FALSO | No |  

#### <a name="textformat-example"></a>Esempio di formato testo
Nell'esempio seguente vengono illustrate alcune delle proprietà formato per formato testo.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Per utilizzare un escapeChar anziché quoteChar, sostituire la riga con quoteChar con escapeChar seguenti:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenari per l'utilizzo firstRowAsHeader e skipLineCount

- Si sta copiando da una fonte non file in un file di testo e si desidera aggiungere una riga di intestazione che contiene i metadati di schema (ad esempio: schema SQL). Specificare **firstRowAsHeader** come vero nel set di dati di output per questo scenario. 
- Si sta copiando da un file di testo che contiene una riga di intestazione a un sink non file e si desidera eliminare la riga. Specificare **firstRowAsHeader** come vero nel set di dati di input.
- Si sta copiando un file di testo e si vuole ignorare alcune righe all'inizio che non contengono dati o intestazione informazioni. Specificare **skipLineCount** per indicare il numero di righe vengono ignorate. Se il resto del file contiene una riga di intestazione, è possibile specificare anche **firstRowAsHeader**. Se vengono specificati **skipLineCount** e **firstRowAsHeader** , le righe vengono ignorate prima di tutto e quindi le informazioni di intestazione vengono lette dal file di input

### <a name="specifying-avroformat"></a>Se si specifica AvroFormat
Se il formato è impostato su AvroFormat, non è necessario specificare le proprietà nella sezione formato all'interno della sezione typeProperties. Esempio:

    "format":
    {
        "type": "AvroFormat",
    }

Per utilizzare il formato di Avro in una tabella Hive, è possibile fare riferimento [all'esercitazione dell'Hive di Apache](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Noti quanto segue:  

- [Tipi di dati complessi](http://avro.apache.org/docs/current/spec.html#schema_complex) non supportati (record enumerazioni, matrici, mappe, unioni e fisso)

### <a name="specifying-jsonformat"></a>Se si specifica JsonFormat

Se il formato è impostato su **JsonFormat**, è possibile specificare le seguenti proprietà **facoltativo** nella sezione **formato** .

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| filePattern | Indicare il modello di dati archiviati in ogni file JSON. Valori consentiti: **setOfObjects** e **arrayOfObjects**. Il valore **predefinito** è: **setOfObjects**. Vedere sezioni per informazioni dettagliate su questi modelli che seguono.| No |
| encodingName | Specificare il nome della codifica. Per l'elenco dei nomi di codifica validi, vedere: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) proprietà. Ad esempio: windows 1250 o punti. Il valore **predefinito** è: **UTF-8**. | No | 
| nestingSeparator | Carattere utilizzato per separare i livelli di annidamento. Il valore predefinito è '. " (punto). | No | 


#### <a name="setofobjects-file-pattern"></a>modello di file setOfObjects

Ogni file contiene solo oggetto o linea-delimitato/concatenate più oggetti. Quando si sceglie questa opzione in un set di dati di output, Copia attività restituisce un singolo file JSON con ogni oggetto per ogni riga (riga da virgole).

**singolo oggetto** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**riga delimitata JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenati**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>modello di file arrayOfObjects. 

Ogni file contiene una matrice di oggetti. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Esempio di JsonFormat

Se si dispone di un file JSON con il contenuto seguente:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

e si desidera copiare in una tabella di SQL Azure nel formato seguente: 

ID  | Name.First | Name.Middle | . Cognome | Tag
--- | ---------- | ----------- | --------- | ----
1 | John | null | Rossi | ["Dati Factory", "Azure"]

Il set di dati di input con tipo JsonFormat è definita nel seguente modo: (definizione parziale con solo le sezioni pertinenti)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Se non è definita la struttura, le attività di copia semplifica la struttura per impostazione predefinita e copiare ogni elemento. 

#### <a name="supported-json-structure"></a>Struttura JSON supportato
Noti quanto segue: 

- Tutti gli oggetti con un insieme di coppie nome/valore siano associati a una riga di dati in un formato tabulare. Gli oggetti possono essere nidificati ed è possibile definire come unire la struttura di un set di dati con il separatore di annidamento (.) per impostazione predefinita. Vedere l' [esempio JsonFormat](#jsonformat-example) precedente sezione per un esempio.  
- Se la struttura non è definita nel set di dati Data Factory, l'attività Copia rileva lo schema dal primo oggetto e unire l'intero oggetto. 
- Se l'input JSON dispone di una matrice, l'attività Copia converte il valore intera matrice in una stringa. È possibile scegliere di ignorare mediante [la mappatura di colonna o filtri](#column-mapping-with-translator-rules).
- Se sono presenti nomi duplicati allo stesso livello, l'attività Copia sceglie quello precedente.
- I nomi delle proprietà sono maiuscole e minuscole. Due proprietà con lo stesso nome ma maiuscole e minuscole diverse sono considerate due proprietà distinte. 

### <a name="specifying-orcformat"></a>Se si specifica OrcFormat
Se il formato è impostato su OrcFormat, non è necessario specificare le proprietà nella sezione formato all'interno della sezione typeProperties. Esempio:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Se non si copiano file ORCO **come-è** tra locali e cloud archivi dati, è necessario installare il 8 JRE (Java Runtime ambiente) nel computer gateway. Un gateway a 64 bit richiede JRE 64 bit e a 32 bit gateway JRE versione a 32 bit. È possibile trovare entrambe le versioni da [qui](http://go.microsoft.com/fwlink/?LinkId=808605). Scegliere quella appropriata.

Noti quanto segue:

-   Dati complessi tipi non sono supportati (struttura, mappa, elenco, unione)
-   File ORCO sono disponibili tre [Opzioni di compressione](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): nessuna, ZLIB, SNAPPY. Dati Factory supporta la lettura dei dati da file ORCO in uno di questi formati compressi. Viene utilizzata la compressione codec è nei metadati per leggere i dati. Tuttavia, quando si scrive in un file di ORCO, Data Factory sceglie ZLIB, che rappresenta il valore predefinito per ORCO. Attualmente, non è possibile ignorare questo comportamento. 

### <a name="specifying-parquetformat"></a>Se si specifica ParquetFormat
Se il formato è impostato su ParquetFormat, non è necessario specificare le proprietà nella sezione formato all'interno della sezione typeProperties. Esempio:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Se non si copiano file Parquet **come-è** tra locali e cloud archivi dati, è necessario installare il 8 JRE (Java Runtime ambiente) nel computer gateway. Un gateway a 64 bit richiede JRE 64 bit e a 32 bit gateway JRE versione a 32 bit. È possibile trovare entrambe le versioni da [qui](http://go.microsoft.com/fwlink/?LinkId=808605). Scegliere quella appropriata.

Noti quanto segue:

-   Dati complessi tipi non sono supportati (mappa, elenco)
-   File parquet è le seguenti opzioni compressione: nessuna, SNAPPY, GZIP e LZO. Dati Factory supporta la lettura dei dati da file ORCO in uno di questi formati compressi. Utilizza il codec di compressione nei metadati per leggere i dati. Tuttavia, quando si scrive in un file Parquet, Data Factory sceglie SNAPPY, il valore predefinito per il formato Parquet. Attualmente, non è possibile ignorare questo comportamento. 
