## <a name="fileshare-dataset-type-properties"></a>Proprietà del tipo di FileShare set di dati

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](../articles/data-factory/data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati. 

La sezione **typeProperties** è diversa per ogni tipo di set di dati. Vengono fornite informazioni specifiche per il tipo di set di dati. La sezione typeProperties per un set di dati di tipo **FileShare** set di dati è le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
folderPath | Percorso Sub della cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per esempi, vedere [esempio collegato definizioni servizio e set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** di percorsi delle cartelle in base a sezione Data-ora inizio/fine. | Sì
fileName | Specificare il nome del file in **folderPath** se si desidera che la tabella per fare riferimento a un file specifico nella cartella. Se non si specifica un valore per questa proprietà, la tabella rimanda a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato dovrebbero essere seguito in questo formato: <br/><br/>Dati. <Guid>txt (esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No
partitionedBy | Per specificare una folderPath dinamico filename per i dati di serie ora è possibile utilizzare partitionedBy. Ad esempio folderPath parametrizzata per ogni ora di dati. | No
Formato | Sono supportati i tipi di formato seguenti: **formato testo**, **AvroFormat**, **JsonFormat**e **OrcFormat**. Impostare la proprietà di **tipo** in formato a uno dei seguenti valori. Vedere [Formato testo che specifica](#specifying-textformat), [Specificando AvroFormat](#specifying-avroformat), [Specificando JsonFormat](#specifying-jsonformat)e [OrcFormat specificando](#specifying-orcformat) sezioni per informazioni dettagliate. Se si desidera copiare i file come-è tra archivi basati su file (copia binario), è possibile ignorare la sezione formato in entrambe le definizioni di set di dati di input e di output. | No
fileFilter | Specificare un filtro da utilizzare per selezionare un sottoinsieme di file nel folderPath piuttosto che tutti i file.<br/><br/>Valori consentiti: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1:`"fileFilter": "*.log"`<br/>Esempio 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter è applicabile per un set di dati di input FileShare. Questa proprietà non è supportata con HDFS.  | No
| compressione | Specificare il tipo e il livello di compressione per i dati. Sono supportati: **GZip**, **Deflate**e **BZip2** e livelli supportati sono: **ottimale** e **più veloce**. Impostazioni di compressione attualmente non sono supportate per i dati in **AvroFormat** o **OrcFormat**. Per ulteriori informazioni, vedere la sezione [supporto per la compressione](#compression-support) .  | No |
| useBinaryTransfer | Specificare se usare la modalità di trasferimento binario. True per la modalità binaria e ASCII false. Il valore predefinito: True. Questa proprietà può essere utilizzata solo quando il tipo di servizio collegato associato è di tipo: serverftp. | No | 
 

> [AZURE.NOTE] nome file e fileFilter non possono essere utilizzate contemporaneamente.

### <a name="using-partionedby-property"></a>Utilizzo di proprietà partionedBy

Come indicato nella sezione precedente, è possibile specificare un folderPath dinamico nome file per i dati di serie ora con partitionedBy. È possibile eseguire con le macro di dati Factory e la variabile di sistema SliceStart, SliceEnd che indicano il periodo di tempo logico per una sezione di dati specificato. 

Per informazioni sui set di dati serie ora, pianificazione e le sezioni, vedere [Creazione di set di dati](../articles/data-factory/data-factory-create-datasets.md), [pianificazione ed esecuzione](../articles/data-factory/data-factory-scheduling-and-execution.md)e [Pipeline di creazione di](../articles/data-factory/data-factory-create-pipelines.md) articoli. 

#### <a name="sample-1"></a>Esempio 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

In questo esempio {sezione} viene sostituito con il valore della variabile di sistema di Data Factory SliceStart nel formato (YYYYMMDDHH) specificato. Il SliceStart fa riferimento per l'ora di inizio della sezione. Il folderPath è diverso per ogni sezione. Esempio: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Esempio 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

In questo esempio, anno, mese, giorno e ora di SliceStart vengono estratti in variabili separate utilizzati dalle proprietà folderPath e il nome file.
