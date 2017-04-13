<properties 
    pageTitle="Creare set di dati in dati Azure Factory | Microsoft Azure" 
    description="Informazioni su come creare set di dati di Azure Data Factory con esempi che utilizzano le proprietà, ad esempio offset e anchorDateTime."
    keywords="creare set di dati, esempio di set di dati, esempio offset"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Set di dati nella Factory dati Azure
In questo articolo descrive i set di dati di Azure Data Factory e include esempi, ad esempio offset, anchorDateTime e database offset/stile.

Quando si crea un set di dati, si sta creando un puntatore ai dati che si desidera elaborare. Elaborazione dei dati (ingresso/uscita) in un'attività e un'attività è contenuta in una pipeline. Un set di dati di input rappresenta l'input per un'attività nella pipeline e un set di dati di output rappresenta l'output per l'attività.

Set di dati identificare i dati all'interno di archivi dati diversi, ad esempio tabelle, file, cartelle e documenti. Dopo aver creato un set di dati, è possibile utilizzare gli impegni in una pipeline. Un set di dati, ad esempio, può essere un set di dati di input/output di un'attività di copia o HDInsightHive. Portale di Azure fornisce un layout visivo di tutte le tubazioni e dati di input e output. A colpo d'occhio è visualizzare tutte le relazioni e le dipendenze della pipeline tra tutte le origini sapere sempre la provenienza dei dati e utilizzo.

In Azure Data Factory, è possibile ottenere dati da un set di dati tramite Copia attività in una pipeline.

> [AZURE.NOTE] Se ha familiarità con Azure Data Factory, vedere [Introduzione a Azure Data Factory](data-factory-introduction.md) per una panoramica del servizio di Azure Data Factory. Vedere [creare la prima factory di dati](data-factory-build-your-first-pipeline.md) per un'esercitazione per creare il prima factory di dati. In questi due articoli sono che si sfondo informazioni necessarie per comprendere meglio questo articolo. 

## <a name="define-datasets"></a>Definire set di dati
Un set di dati di Azure Data Factory viene definita nel seguente modo: 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

Nella tabella seguente vengono descritte le proprietà in JSON precedente:   

| Proprietà | Descrizione | Obbligatorio | Impostazione predefinita |
| -------- | ----------- | -------- | ------- |
| nome | Nome del set di dati. Vedere [Azure Data Factory - regole di denominazione](data-factory-naming-rules.md) per le regole di denominazione. | Sì | ND |
| tipo | Tipo del set di dati. Specificare uno dei tipi supportati da Azure Data Factory (ad esempio: AzureBlob, AzureSqlTable). <br/><br/>Per informazioni dettagliate, vedere [Il tipo di set di dati](#Type) . | Sì | ND |
| struttura | Schema del<br/><br/>Per informazioni dettagliate, vedere [Set di dati struttura](#Structure) sezione. | No. | ND |
| typeProperties | Proprietà corrispondente al tipo di selezionato. Vedere la sezione [Tipo di set di dati](#Type) per informazioni dettagliate sui tipi di supportati e le relative proprietà. | Sì | ND |
| esterno | Flag booleano per specificare se un set di dati viene generato in modo esplicito da una pipeline di factory dati o meno.  | No | FALSO | 
| disponibilità | Definisce la finestra di elaborazione o il modello sezionamento di produzione set di dati. <br/><br/>Per informazioni dettagliate, vedere [La disponibilità di set di dati](#Availability) sezione. <br/><br/>Per informazioni dettagliate sul set di dati sezionare modello, vedere l'articolo di [pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md) . | Sì | ND
| criteri | Definisce i criteri o la condizione che devono soddisfare le sezioni del set di dati. <br/><br/>Per informazioni dettagliate, vedere [Set di dati criteri](#Policy) sezione. | No | ND |

## <a name="dataset-example"></a>Esempio di set di dati
Nell'esempio seguente, il set di dati rappresenta una tabella denominata **tabella** in un **database SQL Azure**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Noti quanto segue: 

- tipo è impostato su AzureSqlTable.
- proprietà di tipo tableName (specifica di tipo AzureSqlTable) è impostata su tabella.
- linkedServiceName fa riferimento a un servizio di tipo AzureSqlDatabase collegato. Vedere la definizione del servizio collegata seguente. 
- frequenza di disponibilità è impostata su giorno e intervallo è impostato su 1, in modo che la sezione prodotti ogni giorno.  

AzureSqlLinkedService viene definita nel seguente modo:

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

In JSON precedente: 

- tipo sia impostata su AzureSqlDatabase
- proprietà di tipo connectionString specifica le informazioni per connettersi a un database SQL Azure.  


Come si può notare, il servizio collegato definisce come connettersi a un database SQL Azure. Il set di dati consente di definire quali tabella viene utilizzato come un'ingresso/uscita per l'attività in una pipeline. La sezione attività nella [pipeline di](data-factory-create-pipelines.md) JSON specifica se il set di dati viene utilizzato come un set di dati di input o output.


> [AZURE.IMPORTANT] A meno che non è realizzate un set di dati tramite Azure Data Factory, devono essere contrassegnato come **esterni**. Questa impostazione in genere si applica a input della prima attività in una pipeline.   

## <a name="Type"></a>Tipo di set di dati
Le origini dati supportate e i tipi di set di dati sono allineati. Fare clic su argomenti a cui fa riferimento nell'articolo per informazioni sulla configurazione del set di dati e tipi di [Attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores) . Ad esempio, se si utilizza dati da un database SQL Azure, fare clic su Database SQL Azure nell'elenco degli archivi di dati supportati per informazioni dettagliate, vedere.  

## <a name="Structure"></a>Struttura del set di dati
La sezione **struttura** definisce lo schema del set di dati. La presentazione contiene un insieme di nomi e i tipi di dati delle colonne.  Nell'esempio seguente, il set di dati con tre colonne slicetimestamp, NomeProgetto e pageviews e sono del tipo: stringa, la stringa e decimali rispettivamente.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilità di set di dati
La **disponibilità** di un set di dati definisce la finestra di elaborazione (oraria, giornaliera, settimanale e così via) o il modello sezionamento per il set di dati. Vedere l'articolo [pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) per ulteriori informazioni sul modello di suddivisione e relazioni di set di dati. 

Nella sezione disponibilità seguente specifica che il set di dati di output prodotti oraria (o) input set di dati è disponibile ogni ora:

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

Nella tabella seguente vengono descritte le proprietà che è possibile utilizzare nella sezione disponibilità: 

| Proprietà | Descrizione | Obbligatorio | Impostazione predefinita |
| -------- | ----------- | -------- | ------- |
| frequenza | Specifica l'unità di tempo per produzione sezione set di dati.<br/><br/>**Frequenza supportati**: minuto, ora, giorno, settimana, mese | Sì | ND |
| intervallo | Specifica moltiplica per frequenza<br/><br/>"L'intervallo di frequenza x" determina con quale frequenza viene prodotta la sezione.<br/><br/>Se è necessario per tagliati ora per il set di dati, impostare **frequenza** **ore**e **dell'intervallo** da **1**.<br/><br/>**Nota:** Se si specifica frequenza come minuto, è consigliabile impostare l'intervallo di a non meno di 15 | Sì | ND |
| stile | Specifica se la sezione deve essere presentata all'inizio/fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se num_rate è impostato su mese e stile è impostato su EndOfInterval, la sezione viene visualizzato l'ultimo giorno del mese. Se lo stile è impostato su StartOfInterval, la sezione viene visualizzato il primo giorno del mese.<br/><br/>Se num_rate è impostato su giorno e stile è impostato su EndOfInterval, la sezione viene visualizzato durante l'ultima ora del giorno.<br/><br/>Se num_rate è impostato su ora e stile è impostato su EndOfInterval, la sezione viene visualizzato alla fine dell'ora. Ad esempio, per una sezione per PM 1-2 PM periodo, la sezione viene prodotto 2 PM. | No | EndOfInterval |
| anchorDateTime | Definisce la posizione assoluta in utilizzata dall'utilità di pianificazione per calcolare i limiti della sezione set di dati. <br/><br/>**Nota:** Se il AnchorDateTime è composta dalle parti di data che sono più granulare di specificare la frequenza vengono ignorate le parti più granulare. <br/><br/>Ad esempio, se l' **intervallo** è **ogni ora** (frequenza: ora e l'intervallo: 1) e **AnchorDateTime** contiene **minuti e secondi**, quindi le parti **minuti e secondi** del AnchorDateTime vengono ignorate. | No | 01/01/0001 |
| scarto | TimeSpan in base al quale vengono spostati all'inizio o alla fine di tutte le sezioni di set di dati. <br/><br/>**Nota:** Se sono specificati anchorDateTime e scarto, il risultato è MAIUSC combinato. | No | ND |

### <a name="offset-example"></a>esempio scarto

Giornaliera sezioni che iniziano 6 ore anziché mezzanotte predefinito. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

**Frequenza** è impostata su **giorno** e **intervallo** è impostato su **1** (una volta al giorno): se si desidera visualizzare la sezione a 6 AM anziché al momento predefinito: 24 ore. Tenere presente che questa volta è un'ora UTC. 

## <a name="anchordatetime-example"></a>esempio di anchorDateTime

**Esempio:** le sezioni set di dati 23 ore che inizino nel 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>offset/stile di esempio

Se è necessario set di dati su base mensile sulla data e ora specifiche (supponga 3o di ogni mese alle 8:00), utilizzare il tag **offset** per impostare la data e l'ora deve essere eseguita. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Criteri di set di dati

La sezione relativa ai **criteri** nella definizione di set di dati consente di definire i criteri o la condizione che devono soddisfare le sezioni del set di dati.

### <a name="validation-policies"></a>Criteri di convalida

| Nome del criterio | Descrizione | Applicato a | Obbligatorio | Impostazione predefinita |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Verifica che i dati in un **blob Azure** soddisfino i requisiti di dimensione minima (in megabyte). | Archivio Blob Azure | No | ND |
|minimumRows | Consente di verificare che i dati in un **database SQL Azure** o una **tabella Azure** contengano il numero minimo di righe. | <ul><li>Database SQL Azure</li><li>Tabelle di Azure</li></ul> | No | ND

#### <a name="examples"></a>Esempi

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Set di dati esterni

Set di dati esterni sono quelli che non vengono prodotti da una pipeline in esecuzione nell'ambiente di produzione di dati. Se il set di dati è contrassegnato come **esterno**, il criterio di **ExternalData** può essere definito per influenzare il comportamento della disponibilità di sezione set di dati. 

A meno che non è realizzate un set di dati tramite Azure Data Factory, devono essere contrassegnato come **esterni**. Questa impostazione si applica agli ingressi del prima attività in una pipeline in genere a meno che non viene utilizzato attività o pipeline concatenazione. 

| Nome | Descrizione | Obbligatorio | Valore predefinito  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tempo di ritardo la verifica della disponibilità dei dati esterni per la sezione specificato. Ad esempio, se i dati deve per essere disponibile ogni ora, è possibile ritardare controllo per visualizzare i dati esterni sono disponibili e la sezione corrispondente sia pronto usando dataDelay.<br/><br/>Si applica solo a quella corrente.  Ad esempio, se è 1:00 PM immediatamente e il valore è 10 minuti, la convalida parte 1:10 PM.<br/><br/>Questa impostazione non ha alcun effetto sezioni in passato (sezioni con data/ora fine sezione + dataDelay < ora) vengono elaborate senza ritardi.<br/><br/>Ora maggiore di 23:59 ore necessario specificato utilizzando il formato day.hours:minutes:seconds. Ad esempio, per specificare 24 ore, non utilizzare 24:00:00; Utilizzare invece 1.00:00:00. Se si usa 24:00:00, verrà considerato come 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. | No | 0 |
| retryInterval | Il tempo di attesa tra un errore e la successiva riprovare tentativo. Si applica al tempo presenta; Se il precedente prova non riuscito, attesa questo lungo dopo l'ultimo provare. <br/><br/>Se è 1:00 pm immediatamente, iniziamo al primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, Riprova successivo è 1.00 + 1 min (durata) + 1min (intervallo) = 1:02 pm. <br/><br/>Per le sezioni in passato non esiste alcun ritardo. Il tentativo accade immediatamente. | No | 00:01:00 (1 minuto) | 
| retryTimeout | Timeout per i tentativi.<br/><br/>Se è impostata su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo il timeout.<br/><br/>Se tutti i tentativi per la convalida timeout, la sezione viene contrassegnata come TimedOut. | No | 00:10:00 (10 minuti) |
| maximumRetry | Numero di volte in cui controllare la disponibilità dei dati esterni. Il valore massimo consentito è 10. | No | 3 | 

## <a name="scoped-datasets"></a>Set di dati dell'ambito
È possibile creare set di dati che sono limitate a una pipeline utilizzando la proprietà **set di dati** . Questi set di dati può essere usata solo per le attività in questa pipeline ma non per le attività di altre tubazioni. Nell'esempio seguente definisce una pipeline con due set di dati - connessione desktop remoto InputDataset e connessione desktop remoto OutputDataset - all'interno della pipeline:  

> [AZURE.IMPORTANT] Set di dati dell'ambito sono supportati solo con pipeline monouso (**pipelineMode** impostato su **OneTime**). Per informazioni dettagliate, vedere [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) .

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }