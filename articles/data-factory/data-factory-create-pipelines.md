<properties 
    pageTitle="Crea/pianificazione pipeline, per bicicletta attività nei dati Factory | Microsoft Azure" 
    description="Informazioni su come creare una pipeline di dati di Azure Data Factory spostare e trasformare i dati. Creare un flusso di lavoro basati sui dati per produrre pronti a usare informazioni disponibili." 
    keywords="pipeline di dati, dati basate su flusso di lavoro"
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="pipelines-and-activities-in-azure-data-factory"></a>Tubazioni e attività nei dati di Azure Factory
In questo articolo consente di comprendere tubazioni e le attività di Azure Data Factory e consente di creare-to-end basati sui dati flussi di lavoro per lo spostamento dei dati e scenari di elaborazione dei dati.  

> [AZURE.NOTE] In questo articolo si presuppone che hanno effettuato tramite [Introduzione alle Azure Data Factory](data-factory-introduction.md). Se non si dispone hands-on-esperienza con la creazione di dati consente di factory, passano attraverso il [creare la prima factory dati](data-factory-build-your-first-pipeline.md) esercitazione è comprendere meglio questo articolo.  

## <a name="what-is-a-data-pipeline"></a>Che cos'è una pipeline di dati?
**Pipeline** è un raggruppamento delle correlate logicamente **attività**. Viene utilizzato per le attività di gruppo in un'unità che effettua un'attività. Per comprendere meglio pipeline, è necessario sapere prima di tutto un'attività. 

## <a name="what-is-an-activity"></a>Che cos'è un'attività?
Attività di definiscono le azioni da eseguire sui dati. Ogni attività utilizza zero o più [set di dati](data-factory-create-datasets.md) come input e produce uno o più set di dati come output. 

Ad esempio, utilizzare un'attività di copia per progettare copia di dati dall'archivio di dati in un altro archivio di dati. Analogamente, utilizzare un'attività HDInsight Hive per eseguire una query Hive in un cluster di Azure HDInsight per trasformare i dati. Azure Data Factory offre una vasta gamma di [trasformazione dei dati](data-factory-data-transformation-activities.md)e le attività di [spostamento dei dati](data-factory-data-movement-activities.md) . È anche possibile creare un'attività .NET personalizzata per eseguire il codice. 

## <a name="sample-copy-pipeline"></a>Pipeline di copia di esempio
Nella pipeline di esempio seguente, esiste un'attività di tipo **Copia** nella sezione **attività** . In questo esempio, [copiare attività](data-factory-data-movement-activities.md) consente di copiare dati da un archivio Blob Azure a un database SQL Azure. 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
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

Noti quanto segue:

- Nella sezione attività esiste una sola attività il cui **tipo** sia impostata su **Copia**.
- Input per l'attività è impostata su **InputDataset** e output per l'attività è impostato su **OutputDataset**.
- Nella sezione **typeProperties** **BlobSource** viene specificato come tipo di origine e **SqlSink** viene specificato come tipo di sink.

Per una procedura dettagliata completamento della creazione di questa pipeline, vedere [Esercitazione: copiare i dati dallo spazio di archiviazione Blob al Database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Pipeline di trasformazione di esempio
Nella pipeline di esempio seguente, esiste un'attività di tipo **HDInsightHive** nella sezione **attività** . In questo esempio, l' [attività HDInsight Hive](data-factory-hive-activity.md) Trasforma i dati da un'archiviazione Blob Azure eseguendo un file di script Hive in un cluster di Azure HDInsight Hadoop. 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

Noti quanto segue: 

- Nella sezione attività esiste una sola attività il cui **tipo** è impostato su **HDInsightHive**.
- Nella finestra account di archiviazione Azure (specificata da scriptLinkedService, denominata **AzureStorageLinkedService**) e nella cartella **script** il contenitore **adfgetstarted**, è archiviato il file di script Hive, **partitionweblogs.hql**.
- La sezione **definisce** viene utilizzata per specificare le impostazioni di runtime passati allo script hive come valori di configurazione Hive (ad esempio ${hiveconf: inputtable}, {hiveconf:partitionedtable} $).

Per una procedura dettagliata completamento della creazione di questa pipeline, vedere [Esercitazione: creare il prima pipeline per elaborare i dati utilizzando cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Concatenazione di attività
Se si dispone di più attività in una pipeline e output di un'attività non è un input di un'altra attività, le attività possono essere eseguite in parallelo se le sezioni di dati di input per le attività siano pronte. 

È possibile concatenare due attività facendo in modo che il set di dati di output di un'attività come il set di dati di input di altre attività. Le attività possono essere nella stessa pipeline o condutture diverso. La seconda attività viene eseguita solo quando il primo viene completata correttamente. 

Si consideri ad esempio il caso seguente:
 
1.  Pipeline P1 ha A1 attività che richiede di set di dati esterni input D1 e produrre set di dati di **output** **D2**.
2.  Pipeline P2 sono A2 attività che richiede l' **input** dal set di dati **D2**e genera set di dati di output D3.
 
In questo scenario, l'attività A1 viene eseguita quando i dati esterni sono disponibili e viene raggiunta la frequenza di disponibilità pianificata.  L'attività A2 viene eseguita quando le sezioni pianificate da D2 diventano disponibile e quando viene raggiunta la frequenza di disponibilità pianificata. Se si verifica un errore in una delle sezioni nel set di dati D2, A2 non vengono eseguite per tale sezione fino a quando diventa disponibile.

Vista diagramma:

![Attività di concatenazione in due condutture](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vista diagramma con entrambe le attività nella stessa pipeline: 

![Concatenazione attività nella stessa pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Per ulteriori informazioni, vedere [pianificazione e all'esecuzione](#chaining-activities). 

## <a name="scheduling-and-execution"></a>Pianificazione e l'esecuzione
Fino a questo punto si è compreso quali sono le tubazioni e attività. Siano anche presenti come sono definiti e un livello superiore visualizzazione delle attività in Azure Data Factory. A questo punto possiamo osservare come sono state eseguite. 

Una pipeline è attiva solo tra l'ora di inizio e data/ora fine. Non viene eseguita prima l'ora di inizio o dopo l'ora di fine. Se è in pausa la pipeline, essa non vengono eseguita indipendentemente dalla relativa ora di inizio e fine. Per pipeline per l'esecuzione, dovrebbe non essere interrotto. Infatti, non è la pipeline che viene eseguita. Si tratta di attività nella pipeline che vengono eseguite. Tuttavia a tale scopo nel contesto complessivo della pipeline. 

Vedere [pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md) per comprendere il funzionamento di pianificazione e l'esecuzione in Azure Data Factory.

## <a name="create-pipelines"></a>Creare pipeline
Azure Data Factory fornisce vari meccanismi per creare e distribuire condutture (in cui sono presenti una o più attività in essa). 

### <a name="using-azure-portal"></a>Nel portale di Azure
È possibile utilizzare editor Factory dati nel portale di Azure per creare una pipeline. Per una procedura dettagliata-to-end, vedere [Guida introduttiva di Azure Data Factory (dati Factory Editor)](data-factory-build-your-first-pipeline-using-editor.md) . 

### <a name="using-visual-studio"></a>Utilizzo di Visual Studio 
È possibile utilizzare Visual Studio per creare e distribuire pipeline Azure Data Factory. Per una procedura dettagliata-to-end, vedere [Guida introduttiva di Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) . 

### <a name="using-azure-powershell"></a>Utilizzo di PowerShell Azure
È possibile utilizzare Azure PowerShell per creare pipeline in Azure Data Factory. Ad esempio, è stata definita la pipeline JSON in un file c:\DPWikisample.json. È possibile caricarlo all'istanza di Azure Data Factory, come illustrato nell'esempio seguente:

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Per una procedura dettagliata-to-end per la creazione di una factory di dati con una pipeline, vedere [Guida introduttiva di Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) . 

### <a name="using-net-sdk"></a>Utilizzando .NET SDK
È possibile creare e distribuire troppo pipeline tramite .NET SDK. Questo meccanismo può essere utilizzato per creare pipeline a livello di programmazione. Per ulteriori informazioni, vedere [creare, gestire e monitorare factory dati a livello di programmazione](data-factory-create-data-factories-programmatically.md). 


### <a name="using-azure-resource-manager-template"></a>Modello di gestione risorse di Azure
È possibile creare e distribuire pipeline utilizzando un modello di gestione di risorse Azure. Per ulteriori informazioni, vedere [Guida introduttiva di Azure Data Factory (Manager delle risorse di Azure)](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Uso di API REST
È possibile creare e distribuire pipeline utilizzando le API REST troppo. Questo meccanismo può essere utilizzato per creare pipeline a livello di programmazione. Per ulteriori informazioni, vedere [creare o aggiornare una Pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx). 


## <a name="monitor-and-manage-pipelines"></a>Monitorare e gestire pipeline  
Una volta distribuita una pipeline, è possibile gestire e monitorare le tubazioni, sezioni e viene eseguito. Altre informazioni sui crea qui: [monitorare e gestire pipeline](data-factory-monitor-manage-pipelines.md).


## <a name="pipeline-json"></a>Pipeline JSON   
Possiamo esaminare attentamente nel modo in cui una pipeline nel formato JSON. La struttura generica per una pipeline simile al seguente:

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [
    
            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

La sezione **attività** può avere una o più attività definite al suo interno. Ciascuna attività presenta la struttura di primo livello seguente:

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {
    
        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

In seguito tabella vengono descritte le proprietà all'interno delle definizioni di JSON attività e pipeline:

Tag | Descrizione | Obbligatorio
--- | ----------- | --------
nome | Nome dell'attività o la pipeline. Specificare un nome che rappresenta l'azione che l'attività pipeline sia configurata per eseguire<br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con un numero lettera di presentazione o un carattere di sottolineatura (_)</li><li>Le seguenti caratteri non consentiti: ".", "+", "?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> | Sì
Descrizione | Testo che descrive l'attività pipeline utilizzata per | Sì
tipo | Specifica il tipo di attività. Vedere gli articoli di [Attività di spostamento dei dati](data-factory-data-movement-activities.md) e [Attività di trasformazione dati](data-factory-data-transformation-activities.md) per diversi tipi di attività. | Sì
ingressi | Tabelle di input utilizzate dall'attività<br/><br/>una tabella di input<br/>"input": [{"nome": "inputtable1"}],<br/><br/>due tabelle di input <br/>"input": [{"nome": "inputtable1"}, {"nome": "inputtable2"}], | Sì
output | Tabelle di output utilizzati per la activity.// una tabella di output<br/>"Visualizza": [{"nome": "outputtable1"}],<br/><br/>due tabelle di output<br/>"Visualizza": [{"nome": "outputtable1"}, {"nome": "outputtable2"}], | Sì
linkedServiceName | Nome del servizio collegato utilizzato dall'attività. <br/><br/>Un'attività potrebbe essere necessario specificare il servizio collegato collegamenti per l'ambiente di elaborazione necessari. | Sì, per attività HDInsight e Azure apprendimento Batch punteggio attività <br/><br/>No per tutti gli altri utenti
typeProperties | Le proprietà della sezione typeProperties dipendono dal tipo di attività. | No
criteri | Criteri relative al comportamento in fase di esecuzione dell'attività. Se non è specificato, vengono utilizzati i criteri predefiniti. | No
inizio | Data-ora di inizio per la pipeline. Deve essere in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. <br/><br/>È possibile specificare un'ora locale, ad esempio un'ora EST. Ecco un esempio: "2016-02-27T06:00:00**-05:00**", ovvero 6 AM EST.<br/><br/>Insieme di proprietà inizio e fine specificare periodo attivo per la pipeline. Le sezioni di output solo create con questo periodo attivo. | No<br/><br/>Se si specifica un valore per la proprietà di fine, è necessario specificare valore per la proprietà di avvio.<br/><br/>Ora di inizio e fine può essere vuoto per creare una pipeline di entrambe. È necessario specificare entrambi i valori per impostare un periodo attivo per la pipeline di esecuzione. Se non si specifica ora di inizio e fine quando si crea una pipeline, è possibile impostare mediante il cmdlet Set-AzureRmDataFactoryPipelineActivePeriod in un secondo momento.
fine | Data-ora di fine per la pipeline. Se specificato deve essere in formato ISO. Ad esempio: 2014-10-14T17:32:41Z <br/><br/>È possibile specificare un'ora locale, ad esempio un'ora EST. Ecco un esempio: "2016-02-27T06:00:00**-05:00**", ovvero 6 AM EST.<br/><br/>Per eseguire la pipeline di tempo indefinito, specificare 9999-09-09 come valore per la proprietà di fine. | No <br/><br/>Se si specifica un valore per la proprietà di avvio, è necessario specificare valore per la proprietà di fine.<br/><br/>Vedere le note per la proprietà **start** .
isPaused | Se è impostato su true la pipeline non viene eseguito. Il valore predefinito = false. È possibile utilizzare questa proprietà per abilitare o disabilitare. | No 
utilità di pianificazione | proprietà di "utilità di pianificazione" viene utilizzata per definire la pianificazione desiderata per l'attività. Le sottoproprietà sono uguali a quelli nella [proprietà disponibilità in un set di dati](data-factory-create-datasets.md#Availability). | No |   
| pipelineMode | Il metodo per la programmazione viene eseguita per la pipeline. Valori consentiti: pianificato (impostazione predefinita), unica.<br/><br/>'Pianificato' indica che la pipeline viene eseguito in un intervallo di tempo specificato in base al periodo attivo (ora di inizio e fine). 'Unica' indica che la pipeline viene eseguita solo una volta. Pipeline unica dopo averlo create non possono essere modificato/aggiornato attualmente. Per informazioni sull'impostazione unica, vedere [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) . | No | 
| expirationTime | Intervallo di tempo dopo la creazione per il quale la pipeline valido e deve rimanere provisioning. Se non è attiva qualsiasi, non è riuscita, o in sospeso viene eseguito, la pipeline viene eliminata automaticamente una volta raggiunta la scadenza. | No | 
| set di dati | Elenco di set di dati da utilizzare per le attività definite nella pipeline. Questa proprietà può essere utilizzata per definire set di dati che sono specifici per questa pipeline e non è definita nella factory dati. Set di dati definito all'interno di questa pipeline può essere utilizzato solo per questa pipeline e non può essere condivisa. Per informazioni dettagliate, vedere [set di dati nell'ambito](data-factory-create-datasets.md#scoped-datasets) .| No |  
 

### <a name="policies"></a>Criteri
Criteri interferisce in fase di esecuzione di un'attività, in particolare durante l'elaborazione la sezione di una tabella. La tabella seguente contiene i dettagli.

Proprietà | Valori consentiti | Valore predefinito | Descrizione
-------- | ----------- | -------------- | ---------------
concorrenza | Numero intero <br/><br/>Valore massimo: 10 | 1 | Numero di esecuzioni simultanee dell'attività.<br/><br/>Determina il numero di esecuzioni attività parallela che possono verificarsi in sezioni diverse. Se è necessario passare un'attività, ad esempio, un set di grandi dimensioni dei dati disponibili, con un valore maggiore di concorrenza velocizza l'elaborazione dei dati. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina l'ordine delle sezioni di dati in fase di elaborazione.<br/><br/>Ad esempio, se si dispone 2 sezioni (una situazione 4 ore e un altro PM 5) e si verificano entrambe in attesa di esecuzione. Se si imposta executionPriorityOrder essere NewestFirst, la sezione 5 PM viene elaborata per primo. Allo stesso modo se si imposta executionPriorityORder essere OldestFIrst, viene elaborata la sezione 4 ore. 
Riprova | Numero intero<br/><br/>Valore massimo può essere 10 | 3 | Numero di tentativi prima l'elaborazione dei dati per la sezione è contrassegnato come errore. Esecuzione di attività per una sezione di dati viene ripetuta fino al numero di tentativi specificato. Il tentativo viene eseguito presto dopo l'errore.
timeout | TimeSpan | 00:00:00 | Timeout per l'attività. Esempio: 00:10:00 (implica timeout 10 minuti)<br/><br/>Se un valore non è specificato o è uguale a 0, il timeout è infinito.<br/><br/>Se il tempo di elaborazione dei dati in una sezione supera il valore di timeout, viene annullato e il sistema di tentativi di esecuzione dell'elaborazione. Il numero di tentativi dipende dalla proprietà Riprova. Quando si verifica timeout, lo stato è impostato su TimedOut.
ritardo | TimeSpan | 00:00:00 | Specificare il ritardo prima dell'elaborazione di dati della sezione inizia.<br/><br/>L'esecuzione di attività per una sezione di dati viene avviato dopo il ritardo ha superato il tempo di esecuzione previsto.<br/><br/>Esempio: 00:10:00 (implica ritardo di 10 minuti)
longRetry | Numero intero<br/><br/>Valore massimo: 10 | 1 | Il numero di tentativi tempo prima dell'esecuzione di sezione è non è riuscito.<br/><br/>tentativi longRetry sono interlinea da longRetryInterval. Se è necessario specificare un'ora tra tentativi, utilizzare longRetry. Se sono specificati Riprova e longRetry, ogni tentativo di longRetry include tentativi e il numero massimo di tentativi è Riprova * longRetry.<br/><br/>Ad esempio, se si hanno le seguenti impostazioni nei criteri di attività:<br/>Riprovare: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Si supponga che non esiste una sola sezione eseguire (stato è in attesa) e l'esecuzione di attività non riesce ogni volta. Inizialmente non vi sarà 3 tentativi di esecuzione consecutivi. Dopo ogni tentativo, lo stato di sezione corrisponderebbe Riprova. Dopo aver tentativi prima di tutto 3 sopra, lo stato di sezione sarebbe LongRetry.<br/><br/>Dopo un'ora (valore, del longRetryInteval), non vi sarà un'altra coppia di 3 tentativi di esecuzione consecutivi. In seguito, non più necessario tentativi da riuscita lo stato di sezione. Pertanto complessivo 6 tentativi effettuati.<br/><br/>Se qualsiasi esecuzione ha avuto esito positivo, lo stato di sezione è già pronti e non ulteriori tentativi.<br/><br/>longRetry può essere utilizzato nelle situazioni in cui i dati dipendenti arrivano momenti deterministici o ambiente complessivo instabili in cui l'elaborazione dei dati che si verifica. In tal caso, eseguire tentativi una dopo l'altra non possono essere utili e in questo modo dopo un intervallo di tempo dei risultati in output desiderato.<br/><br/>Attenzione: non impostare i valori alti per longRetry o longRetryInterval. In genere, i valori più alti implicano gli altri problemi di sistemiche. 
longRetryInterval | TimeSpan | 00:00:00 | Il ritardo tra tentativi lungo 


## <a name="next-steps"></a>Passaggi successivi

- Comprendere [la programmazione e l'esecuzione in Azure Data Factory](data-factory-scheduling-and-execution.md).  
- Leggere le informazioni relative di [spostamento](data-factory-data-movement-activities.md) e [le funzionalità di trasformazione dei dati](data-factory-data-transformation-activities.md) in Azure Data Factory
- Informazioni su [Gestione e il monitoraggio di Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Compilare e distribuire la pipeline di prima](data-factory-build-your-first-pipeline.md). 
