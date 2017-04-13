<properties 
    pageTitle="Spostare i dati da Cassandra utilizzo di dati | Microsoft Azure" 
    description="Informazioni su come spostare i dati da un database di Cassandra locali con Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Spostare i dati da un database di Cassandra locali con Azure Data Factory
Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per copiare i dati da un database di Cassandra locale nell'archivio di dati elencato nella colonna Sink nella sezione [origini supportate e sink](data-factory-data-movement-activities.md#supported-data-stores) . In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Dati factory supporta attualmente solo spostamento di dati da un database Cassandra devono [sink archivi di dati supportati](data-factory-data-movement-activities.md#supported-data-stores), ma non spostare dati da altri archivi dati a un database Cassandra.

## <a name="prerequisites"></a>Prerequisiti
Per il servizio di Azure Data Factory in grado di connettersi al database di Cassandra locale, è necessario installare le operazioni seguenti: 

- Gateway di gestione dati 2.0 o versione successiva nello stesso computer che ospita i database o su un computer separato per evitare concorrenti per le risorse con il database. Gateway di gestione dati è un programma che si connette di origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l'articolo [spostare dati tra locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate su Gateway di gestione dati.
  
    Quando si installa il gateway, viene installata automaticamente un driver ODBC Cassandra Microsoft usato per connettersi al database Cassandra. 

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare dati da un database Cassandra uno degli archivi di dati supportati sink consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da database Cassandra all'archivio Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Esempio: Copiare i dati da Cassandra a Blob
Nell'esempio dati copiati da un database Cassandra blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. Dati possono essere copiati direttamente uno dei sink indicato nell'articolo [Le attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory. 

- Servizio collegato di tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un input [set di dati](data-factory-create-datasets.md) di tipo [CassandraTable](#cassandratable-properties).
- Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [CassandraSource](#cassandrasource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servizio Cassandra collegato**

In questo esempio viene utilizzato il servizio **Cassandra** collegato. Vedere la sezione [Cassandra collegato servizio](#onpremisescassandra-linked-service-properties) per le proprietà supportate da questo servizio collegato.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }


**Servizio di archiviazione collegato di Azure**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
        "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

**Set di dati input Cassandra**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Impostando **esterni** su **true** si informa il servizio Factory dati che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.

**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Pipeline con attività di copia**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **CassandraSource** e tipo **sink** è impostato su **BlobSink**. 

Visualizzare [le proprietà di tipo RelationalSource](#cassandrasource-type-properties) per l'elenco delle proprietà supportate dalla RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
            ]   
        }
    }
## <a name="onpremisescassandra-linked-service-properties"></a>Proprietà del servizio OnPremisesCassandra collegato

La tabella seguente contiene una descrizione per gli elementi JSON specifici di servizio Cassandra collegato.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **OnPremisesCassandra** | Sì |
| host | Uno o più indirizzi IP o nomi host dei server Cassandra.<br/><br/>Specificare un elenco di indirizzi IP o nomi di host per connettersi a tutti i server contemporaneamente separati da virgola. | Sì | 
| porta | Porte TCP utilizzato dal server Cassandra ascoltare le connessioni client. | No, il valore predefinito: 9042 |
| authenticationType | Base o anonimo | Sì |
| nome utente |Specificare il nome utente per l'account utente. | Sì, se authenticationType è impostato su Basic. |
| password | Specificare la password per l'account utente.  | Sì, se authenticationType è impostato su Basic. |
| gatewayName | Il nome del gateway utilizzato per connettersi al database Cassandra locale. | Sì |
| encryptedCredential | Credenziali crittografata tramite il gateway. | No | 

## <a name="cassandratable-properties"></a>Proprietà CassandraTable

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties di set di dati di tipo **CassandraTable** include le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| spazio delle chiavi | Nome del spazio delle chiavi o schema Cassandra database. | Sì (se non è definita **query** per **CassandraSource** ). |
| tableName | Nome della tabella nel database Cassandra. | Sì (se non è definita **query** per **CassandraSource** ). |


## <a name="cassandrasource-type-properties"></a>Proprietà di tipo CassandraSource
Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine è di tipo **CassandraSource**, le proprietà seguenti sono disponibili nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilizzare la query personalizzata per leggere i dati. | Query SQL-92 o CQL. Vedere [informazioni di riferimento CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa query SQL, specificare **il nome di name.table spazio delle chiavi** per rappresentare la tabella che si desidera eseguire una query. | No (se sono definiti tableName e spazio delle chiavi nel set di dati).  |
| consistencyLevel | Il livello di coerenza indica quante repliche devono rispondere a una richiesta di lettura prima di restituire dati all'applicazione client. Cassandra controlla il numero specificato di repliche per i dati soddisfare la richiesta di lettura. | UNO, DUE, TRE, BASE, IN TUTTI, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Per informazioni dettagliate, vedere [la coerenza dei dati di configurazione](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) . | No. Valore predefinito è 1. |  


### <a name="type-mapping-for-cassandra"></a>Mapping dei tipi di Cassandra
Tipo di Cassandra | Tipo basato su .net
--------------- | ---------------
ASCII | Stringa 
BIGINT | Int64
BLOB | Byte
VALORE BOOLEANO | Valore booleano
DECIMALE | Decimale
EFFETTUARE UN DOPPIO | Effettuare un doppio
MARGINE DI FLESSIBILITÀ | Singolo
IMPOSTA | Stringa
INT | Int32
TESTO | Stringa
TIMESTAMP | DateTime
TIMEUUID | GUID
UUID | GUID
VARCHAR | Stringa
VARINT | Decimale

> [AZURE.NOTE]  
> Per raccolta tipi (mappa, insieme, elenco, e così via), fare riferimento alla sezione di [lavoro con i tipi di raccolta Cassandra tramite tabella virtuale](#work-with-collections-using-virtual-table) . 
> 
> Tipi definiti dall'utente non sono supportati.
> 
> La lunghezza della colonna di dati binari e colonna stringa di lunghezza non può essere maggiore di 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Lavorare con le raccolte tramite tabelle virtuale
Azure Data Factory utilizza un driver ODBC predefinito per connettersi e copiare i dati dal database Cassandra. Per i tipi di raccolta inclusi mappa, configurare e un elenco, il driver Normalizza nuovamente i dati in tabelle virtuali corrispondente. In particolare, se una tabella contiene tutte le colonne di raccolta, il driver genera virtuale tabelle riportate di seguito:

-   Una **tabella di base**, che contiene gli stessi dati come tabella reale fatta eccezione per le colonne della raccolta. Tabella di base utilizza lo stesso nome della tabella reale che rappresenta.
-   **Tabella virtuale** per ogni colonna insieme, che espande i dati annidati. Le tabelle virtuale che rappresentano raccolte sono denominate usando il nome della tabella reale, un separatore "_vt_" e il nome della colonna.

Tabelle virtuali fare riferimento ai dati descritti nella tabella reale, per consentire il driver accedere ai dati denormalizzati. Per ulteriori informazioni vedere la sezione esempio. È possibile accedere al contenuto delle raccolte Cassandra eseguendo una query e unire le tabelle virtuale.

È possibile sfruttare la [Copia guidata](data-factory-data-movement-activities.md#data-factory-copy-wizard) per intuitivo visualizzare l'elenco delle tabelle di database Cassandra inclusi tabelle virtuali e visualizzare in anteprima i dati all'interno. È anche possibile creare una query nella creazione guidata copia e convalidare per visualizzare il risultato.

### <a name="example"></a>Esempio
Ad esempio seguente "ExampleTable" è una tabella di database Cassandra contenente un'intera colonna di chiave primaria denominato "pk_int", una colonna di testo denominato value, una colonna di elenco, una colonna di mappa e una colonna del set (denominata "StringSet").

pk_int | Valore | Elenco | Mappa |   StringSet
------ | ----- | ---- | --- | --------
1 | "valore di esempio 1" | ["1", "2", "3"]  | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valore di esempio 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

Il driver genera più tabelle virtuali per rappresentare questa tabella singola. Le colonne di chiave esterna nelle tabelle virtuali fanno riferimento a colonne di chiave primaria nella tabella parte reale e indicano quale riga di tabella reale la riga di tabella virtuale corrisponde a. 

La prima tabella virtuale è la base tabella denominata "ExampleTable" viene visualizzata nella tabella seguente. Tabella di base contiene gli stessi dati della tabella di database originale fatta eccezione per gli insiemi di file che sono state omesse da questa tabella ed espanso in altre tabelle virtuale.

pk_int | Valore
------ | -----
1 | "valore di esempio 1"
3 | "valore di esempio 3"

Nella tabella seguente mostra le tabelle virtuale che renormalize i dati dalle colonne dell'elenco, mappa e StringSet. Le colonne con nomi che terminano con index"o Key" indicano la posizione dei dati nell'elenco o nella mappa originale. Le colonne con nomi che terminano con value"contengono i dati espansi dalla raccolta.

#### <a name="table-exampletablevtlist"></a>Tabella "ExampleTable_vt_List":
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Tabella "ExampleTable_vt_Map":
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | RISPOSTE
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Tabella "ExampleTable_vt_StringSet":
pk_int | StringSet_value
------ | ---------------
1 | RISPOSTE
1 | B
1 | C
3 | RISPOSTE
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.
