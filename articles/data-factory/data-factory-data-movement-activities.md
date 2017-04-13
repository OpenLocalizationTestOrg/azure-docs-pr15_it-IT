<properties
    pageTitle="Spostare i dati usando Copia attività | Microsoft Azure"
    description="Informazioni sulle spostamento dei dati in dati Factory pipeline: la migrazione dei dati tra gli archivi cloud e tra un archivio locale e un punto vendita cloud. Utilizzare attività di copia."
    keywords="copiare i dati, spostamento dei dati, la migrazione dei dati, trasferimento dati"
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
    ms.date="09/22/2016"
    ms.author="jingwang"/>

# <a name="move-data-by-using-copy-activity"></a>Spostare i dati usando Copia attività

## <a name="overview"></a>Panoramica
In Azure Data Factory, è possibile utilizzare attività Copia per copiare i dati delle forme diverse da vari locali e cloud origini dati di Azure. Dopo aver copiati dati, si possono essere ulteriormente trasformato e analizzato. Utilizzare attività Copia per pubblicare la trasformazione e risultati dell'analisi per business intelligence (BI) e il consumo di applicazione.

![Ruolo di attività di copia](media/data-factory-data-movement-activities/copy-activity.png)

Attività di copia è una tecnologia sicura, affidabile, scalable e [servizi disponibili a livello globale](#global). In questo articolo fornisce dettagli sul movimento di dati in dati Factory e attività di copia.

Prima di tutto, di seguito viene illustrato come la migrazione dei dati avviene tra due archivi dati cloud e tra un archivio di dati in locale e un archivio di dati cloud.

> [AZURE.NOTE] Per ulteriori informazioni sulle attività in generale, vedere [attività e delle informazioni sulle pipeline](data-factory-create-pipelines.md).

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiare i dati tra due archivi dati cloud
Dopo aver configurato archivi di dati di origine e sink nel cloud, Copia attività attraversa le fasi seguenti per copiare i dati dal sito di origine per il sink. Il servizio che si copia attività:

1. Legge i dati dall'archivio di dati di origine.
2. Esegue serializzazione/deserializzazione, compressione/decompressione, column mapping e digitare la conversione. Funzione queste operazioni sulla base delle configurazioni dei set di dati input, set di dati di output e attività di copia.
3.  Scrive dati all'archivio di dati di destinazione.

Il servizio sceglie automaticamente l'area ottimale per eseguire lo spostamento dei dati. Quest'area è in genere quello più vicina all'archivio dati sink.

![Copia cloud nel cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiare i dati tra un archivio di dati in locale e un archivio di dati cloud
Per spostare i dati in modo sicuro tra un archivio di dati in locale e un archivio di dati cloud, installare il Gateway di gestione di dati nel computer locale. Gateway di gestione dati è un agente che consente di elaborazione e spostamento dati ibrida. È possibile installare Office nello stesso computer durante l'archivio dati stesso o su un computer separato che ha accesso all'archivio dati.

In questo scenario, Gateway di gestione dati esegue la serializzazione/deserializzazione, compressione/decompressione, column mapping e digitare la conversione. Non del flusso di dati tramite il servizio di Azure Data Factory. Se, tuttavia, Gateway di gestione dati scrive direttamente i dati nell'archivio di destinazione.

![Copia in locale-nel cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Vedere [spostare i dati tra locali e cloud archivi dati](data-factory-move-data-between-onprem-and-cloud.md) per un'introduzione e una procedura dettagliata. Per informazioni dettagliate su questo agente, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md) .

È anche possibile spostare dati da/verso supportati archivi di dati che sono ospitati in Azure IaaS macchine () tramite Gateway di gestione dati. In questo caso, è possibile installare il Gateway di gestione di dati nella stessa macchina virtuale come l'archivio dati stesso o in una macchina virtuale separata che ha accesso all'archivio dati.

## <a name="supported-data-stores-and-formats"></a>Archivi di dati supportati e formati
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Se è necessario spostare dati da/verso un dati memorizzata che attività copia non supportano, usare un' **attività personalizzata** in Factory dati con la propria logica per copiare o spostare i dati. Per informazioni dettagliate sulla creazione e utilizzo di un'attività personalizzata, vedere [Usa attività personalizzata in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="supported-file-formats"></a>Formati di file supportati
È possibile utilizzare attività Copia per copiare il file come-tra archivi di due dati basate su file, ad esempio Blob Azure, File System e HDFS. A tale scopo, è possibile ignorare la [sezione formato](data-factory-create-datasets.md) in entrambe le definizioni di set di dati di input e di output. I dati vengono copiati in modo efficiente senza qualsiasi serializzazione/deserializzazione.

Attività Copia inoltre letta e scrivere file nei formati specificati: testo, Avro, ORCO, Parquet e JSON. È possibile eseguire le seguenti attività Copia, ad esempio:

-   Copiare i dati in formato testo (CSV) da Blob Azure e scrittura per il Database di SQL Azure.
-   Copiare i file in formato testo (CSV) dal File System locale e scrivere Blob Azure nel formato Avro.
-   Copiare i dati nel Database di SQL Azure e scrivere HDFS locale nel formato ORCO.



## <a name="global"></a>Spostamento dei dati disponibili a livello globale
Azure Data Factory è disponibile solo in aree ovest Stati Uniti, Stati Uniti orientali e Nord Europa. Tuttavia, il servizio che si copia attività è disponibile a livello globale nei seguenti aree geografiche e aree geografiche. La topologia disponibile a livello globale garantisce spostamento dei dati efficace che in genere evita hop tra regione. Per la disponibilità di dati Factory e spostamento dei dati in un'area, vedere [servizi per area geografica](https://azure.microsoft.com/regions/#services) .

### <a name="copy-data-between-cloud-data-stores"></a>Copiare i dati tra gli archivi dati cloud
Dopo aver configurato archivi di dati di origine e sink nel cloud, Data Factory utilizza una distribuzione di servizio nell'area che più si avvicina al sink sotto l'area geografica stessa per spostare i dati. Fare riferimento alla tabella seguente per la corrispondenza:

Area geografica dell'archivio di dati di destinazione | Area utilizzata per lo spostamento dei dati
:----------------------------------- | :----------------------------
Stati Uniti orientali | Stati Uniti orientali
Stati Uniti orientali 2 | Stati Uniti orientali 2
Usa ovest | Usa ovest
Usa ovest 2 | Usa ovest
Centrale USA | Centrale USA
Stati Uniti centro ovest | Centrale USA
America del Nord centrale USA | America del Nord centrale USA
Sud centrale USA | Sud centrale USA
Europa Nord America | Europa Nord America
Europa occidentale | Europa occidentale
Area Asia sudorientale | Area Asia sudorientale
Asia orientale | Area Asia sudorientale
Giappone est | Giappone est
Giappone ovest | Giappone est
Brasile sud | Brasile sud
Australia orientale | Australia orientale
Australia sudorientale | Australia sudorientale
India centrale | India centrale
India sud | India centrale
India ovest | India centrale


> [AZURE.NOTE] Se l'area dell'archivio di dati di destinazione non è presente nell'elenco precedente, invece di accedervi tramite un'area alternativa attività copia.

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiare i dati tra un archivio di dati in locale e un archivio di dati cloud
Quando vengono copiati i dati tra locale (o macchine virtuali di Azure/IaaS) e archivi cloud, [Gateway di gestione dati](data-factory-data-management-gateway.md) esegue lo spostamento dei dati in un computer locale o la macchina virtuale. I dati non passa attraverso il servizio nel cloud, a meno che non si utilizza la funzionalità [scalare copia](data-factory-copy-activity-performance.md#staged-copy) . In questo caso, flusso dei dati attraverso l'archiviazione Blob Azure gestione temporanea prima che venga scritto nell'archivio dati sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Creare una pipeline con attività di copia
È possibile creare una pipeline con attività di copia in due modi:

### <a name="by-using-the-copy-wizard"></a>Tramite la creazione guidata copia
Dati Factory Copia guidata consente di creare una pipeline con attività di copia. Questa pipeline consente di copiare dati da origini supportate per le destinazioni *senza scrivere JSON* definizioni di servizi collegati, set di dati e pipeline. Per informazioni dettagliate sulla creazione guidata, vedere [Dati Factory Copia guidata](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>L'utilizzo di script JSON
È possibile utilizzare dati Factory Editor nel portale di Azure, Visual Studio o Azure PowerShell per creare una definizione di JSON per una pipeline (mediante copia attività). Quindi, è possibile distribuirlo per creare la pipeline in Data Factory. Vedere [Esercitazione: usare Copia attività in una pipeline di Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per l'esercitazione con istruzioni dettagliate.    

Proprietà JSON (ad esempio nome, descrizione, input e output tabelle e i criteri) sono disponibili per tutti i tipi di attività. Proprietà che sono disponibili nel `typeProperties` sezione dell'attività possono variare a ogni tipo di attività.

Per copiare attività, il `typeProperties` sezione varia a seconda dei tipi di origini ed elabora. Fare clic su un'origine/sink nella sezione [origini e sink supportati](#supported-data-stores) per informazioni sulle proprietà del tipo che supporta l'attività Copia per l'archivio di dati.   

Ecco una definizione di JSON di esempio:

    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from Azure blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputBlobTable"
              }
            ],
            "outputs": [
              {
                "name": "OutputSQLTable"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    }

La pianificazione definita nel set di dati di output determina quando viene eseguita l'attività (ad esempio: **giornaliera**, di frequenza come **giorno**e intervallo come **1**). L'attività Copia dati da un set di dati input (**origine**) a un set di dati di output (**sink**).

È possibile specificare più di un set di dati input all'attività di copia. Vengono utilizzati per verificare le dipendenze prima dell'attività. Tuttavia, solo i dati dal primo set di dati viene copiati per il set di dati di destinazione. Per ulteriori informazioni, vedere [pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni
Vedere la [Guida regolazione e le prestazioni di copia attività](data-factory-copy-activity-performance.md), che descrive i principali fattori che influenzano le prestazioni di spostamento dei dati (copia attività) nella Factory di dati di Azure. Inoltre, elenca le prestazioni osservate durante i test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

## <a name="scheduling-and-sequential-copy"></a>Copia di pianificazione e sequenziale
Per informazioni dettagliate sulle modalità di pianificazione e l'esecuzione in Data Factory, vedere [pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md) . È possibile eseguire più operazioni di copia uno dopo l'altro in modo sequenziale/ordinata. Vedere la sezione [ordinati copia](data-factory-scheduling-and-execution.md#ordered-copy) .

## <a name="type-conversions"></a>Conversioni
Archivi dati diversi dispongono di sistemi di diverso tipo nativo. Attività di copia esegue le conversioni automatica del tipi di origine per elaborare tipi con l'approccio in due passaggi seguenti:

1. Conversione tra tipi di origine nativa a un tipo di .NET.
2. Convertire l'abbonamento da un tipo di .NET a un tipo nativo sink.

Il mapping tra un sistema di tipo nativo a un tipo di .NET per un archivio di dati è l'articolo di archivio di dati. (Fare clic sul collegamento specifico nella tabella [supportati archivi dati](#supported-data-stores) ). È possibile usare questi mapping per determinare tipi appropriati durante la creazione delle tabelle, in modo che attività Copia esegue le conversioni di destra.


## <a name="next-steps"></a>Passaggi successivi
- Per maggiori informazioni sull'attività Copia, vedere [copiare i dati dallo spazio di archiviazione Blob Azure al Database SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Per informazioni sullo spostamento dei dati da un archivio di dati in locale in un archivio di dati cloud, vedere [spostare i dati da locale per il cloud dati sono archiviati](data-factory-move-data-between-onprem-and-cloud.md).
