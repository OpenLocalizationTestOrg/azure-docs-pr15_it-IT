<properties
    pageTitle="L'aggiornamento alla versione 2 del Analitica testo API | Microsoft Azure"
    description="Azure apprendimento testo Analitica - aggiornamento alla versione 2"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>L'aggiornamento alla versione 2 del Analitica testo API #

Questa guida vengono illustrate il processo di aggiornamento del codice da tramite la [prima versione dell'API](../machine-learning/machine-learning-apps-text-analytics.md) tramite la seconda versione. 

Se si utilizza l'API e si desidera per altre informazioni, è possibile **[ottenere ulteriori informazioni sui API di](//go.microsoft.com/fwlink/?LinkID=759711)** o **[seguire Quick Start Guide](//go.microsoft.com/fwlink/?LinkID=760860)**. Per informazioni tecniche, consultare la **[Definizione dell'API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Iniziare una nuova chiave ###

Prima di tutto, è necessario iniziare una nuova chiave dell'API dal **Portale di Azure**:

1. Passare al servizio Analitica testo tramite la [Raccolta di Business Intelligence Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). In questo caso, sono anche disponibili collegamenti a esempi documentazione e codice.

1. Fare clic su **Iscriviti**. Questo collegamento è comunque il portale di gestione di Azure, in cui è possibile iscriversi al servizio.

1. Selezionare un piano. È possibile selezionare il **livello gratuito per le transazioni 5.000/mese**. Come è un piano gratuito, non ti per l'utilizzo del servizio. Sarà necessario eseguire l'accesso all'abbonamento Azure. 

1. Dopo che ci si iscrive a testo Analitica, verranno presentate una **Chiave dell'API**. Copiare questa chiave, come sarà necessario quando si utilizzano i servizi di API.

### <a name="part-2-update-the-headers"></a>Parte 2. Aggiornare le intestazioni ###

Aggiornare i valori di intestazione inviato come illustrato di seguito. Si noti che la chiave account non viene codificata.

**Versione 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versione 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Aggiornare l'URL di base ###

**Versione 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versione 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4. Aggiornare i formati per la valutazione in, le frasi principali e lingue ###

#### <a name="endpoints"></a>Punti finali ####

OTTENERE i punti finali sono ora obsoleti, in modo che tutti gli input è necessario indicare come una richiesta POST. Aggiornare i punti finali a quelle riportate di seguito.

| |Endpoint singolo versione 1|Endpoint batch versione 1|Endpoint versione 2|
|---|---|---|---|
|Tipo di chiamata|Ottieni|Inserisci|Inserisci|
|Valutazione in|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Frasi principali|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Lingue|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formati di input ####

Nota Questo formato POST solo a questo punto viene accettato, in modo che è necessario riformattare qualsiasi tipo di input che in precedenza utilizzava di conseguenza i punti finali singolo documento. Input non sono tra maiuscole e minuscole.

**Versione 1 (batch)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versione 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Valutazione nell'output ####

**Versione 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Output le frasi principali ####

**Versione 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Output lingue ####


**Versione 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Parte 4b. Aggiornare i formati per gli argomenti ###

#### <a name="endpoints"></a>Punti finali ####

| |Endpoint versione 1 | Endpoint versione 2|
|---|---|---|
|Invia per il rilevamento argomento (POST)|```StartTopicDetection```|```topics```|
|Recuperare i risultati argomento (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formati di input ####

**Versione 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versione 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Risultati invio ####

**Versione 1 (POST)**

In precedenza, al termine del processo, riceverai l'output JSON seguente, in cui gli ID processo sarebbero vengano aggiunte a un URL per recuperare l'output.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versione 2 (POST)**

La risposta a questo punto conterrà un valore di intestazione come indicato di seguito, dove `operation-location` viene utilizzato come il punto finale per sondaggio per i risultati:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Risultati di un'operazione ####

**Versione 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2 (GET)**

Come prima, viene restituito **il polling l'output** (periodo suggerito è minuto) finché l'output. 

API di argomenti al termine, di lettura di stato `succeeded` verrà restituito. Questo includeranno quindi i risultati di output nel formato illustrato di seguito:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Eseguire il test! ###

È ora possibile continuare! Testare il codice con un piccolo esempio per garantire che è possibile elaborare correttamente i dati.
