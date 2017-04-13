<properties
    pageTitle="API di risorse macchina: Testo Analitica | Microsoft Azure"
    description="Machine Learning testo Analitica API Microsoft possono essere utilizzate per l'analisi di testo non strutturato per l'analisi di valutazione in, estrazione di una frase chiave, rilevamento della lingua e il rilevamento argomento."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API di apprendimento computer: Testo Analitica per valutazione in, estrazione di una frase, rilevamento della lingua e chiave rilevamento argomento

>[AZURE.NOTE] Questa guida è rivolta versione 1 dell'API. Per la versione 2, [**fare riferimento al documento**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Versione 2 è ora la versione preferita dell'API.

## <a name="overview"></a>Panoramica

L'API di Analitica testo è una famiglia di prodotti di testo analitica [servizi web](https://datamarket.azure.com/dataset/amla/text-analytics) create con Azure apprendimento. L'API possono essere utilizzato per analizzare testo non strutturato per le attività, ad esempio analisi di valutazione in, estrazione di una frase chiave, rilevamento della lingua e il rilevamento di argomento. Nessun dato formazione è necessario per utilizzare questa API: visualizzare solo i dati di testo. Questa API utilizza avanzate in linguaggio naturale elaborazione tecniche per recapitare procedure nelle stime di classe.

È possibile vedere analitica di testo in azione sul nostro [sito demo](https://text-analytics-demo.azurewebsites.net/), in cui si possono trovare [esempi](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) su come implementare analitica testo in c# e Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Analisi di valutazione in

L'API restituisce un punteggio numerico compreso tra 0 e 1. Punteggi vicino 1 indicano valutazione in positivo, mentre i punteggi vicino 0 indicano valutazione in negativo. Punteggio di valutazione in viene generato utilizzando alcune tecniche di classificazione. Le funzionalità di input per l'utilità di classificazione includono n-g, caratteristiche generate da parte di riconoscimento tag e incorporamenti di word. Attualmente, inglese è l'unico linguaggio supportato.
 
## <a name="key-phrase-extraction"></a>Estrazione di una frase chiave

L'API restituisce un elenco di stringhe indicano i punti chiave chiave nel testo di input. Abbiamo ricorrere a tecniche di toolkit di elaborazione linguaggio naturale sofisticate di Microsoft Office. Attualmente, inglese è l'unico linguaggio supportato.

## <a name="language-detection"></a>Rilevamento della lingua

L'API restituisce lingua rilevata e un punteggio numerico tra 0 e 1. Punteggi vicino 1 indicano 100% certezza che la lingua identificata è vera. Un totale di 120 lingue sono supportate.

## <a name="topic-detection"></a>Rilevamento di argomenti

Verrà visualizzata una nuova versione dell'API che restituisce nella parte superiore rilevato argomenti per un elenco di invio dei record di testo. Un argomento viene identificato con una frase chiave, che può essere uno o più correlati parole. L'API richiede un minimo di 100 record di testo da presentare, ma è progettato per rilevare argomenti centinaia a migliaia di record. Si noti che questa API addebita 1 transazione per ogni record di testo inviato. L'API è progettato per funzionare correttamente per testo scritto breve, risorse umano, ad esempio le revisioni e commenti degli utenti.

---

## <a name="api-definition"></a>Definizione dell'API

### <a name="headers"></a>Intestazioni

Assicurarsi di includere le intestazioni corrette nella convocazione, sarà simile alla seguente:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

È possibile trovare la chiave account dal proprio account [Azure Data Market](https://datamarket.azure.com/account/keys). Si noti che è stata accettata JSON attualmente solo per i formati di input e di output. XML non è supportata.

---

## <a name="single-response-apis"></a>API di risposta singola

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Richiesta di esempio**

Nella chiamata sotto, ti chiediamo analisi di valutazione in per la frase "Hello World":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Una risposta verrà restituito come indicato di seguito:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Richiesta di esempio**

Nella chiamata, ti chiediamo che frasi chiave trovata nel testo "è un ottimo sistemazioni rimanga, con decorazione univoco e descrittivo personale":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Una risposta verrà restituito come indicato di seguito:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Richiesta di esempio**

Nella chiamata GET ti chiediamo di valutazione in per le frasi chiave in testo *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Una risposta verrà restituito come indicato di seguito:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Parametri facoltativi**

`NumberOfLanguagesToDetect`è un parametro facoltativo. Il valore predefinito è 1.

---

## <a name="batch-apis"></a>API batch

Il servizio di testo Analitica consente di valutazione in ed estrazione di un'espressione chiave in modalità batch. Si noti che ogni record totalizza conteggiato come un'unica transazione. Ad esempio, se si richiede di valutazione in per 1000 record in un'unica chiamata verranno dedotta transazioni 1000.

Si noti che l'ID immesso nel sistema sono gli ID restituiti dal sistema. Il servizio web non verifica che gli ID siano univoci. È responsabilità del chiamante per verificare l'univocità. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Richiesta di esempio**

Nella chiamata POST sotto, ti chiediamo per rispettivi delle frasi "Hello World", "Hello Foo World" e "Hello personale World" nel corpo della richiesta:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo della richiesta:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Nella risposta, viene visualizzato l'elenco di punteggi associato al testo ID:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Richiesta di esempio**

In questo esempio, ti chiediamo per l'elenco dei rispettivi per le frasi principali in testi seguenti: 

* "È un ottimo albergo rimanga, con decorazione univoco e descrittivo personale"
* "È una conferenza genera report, con comunica molto interessanti"
* "Il traffico è stata ad, ho trascorso tre ore passando all'aeroporto"

Questa richiesta come una chiamata di POST per il punto finale:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo della richiesta:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Nella risposta, viene visualizzato l'elenco di frasi chiave associato al testo ID:

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Inglese",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Francese",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>API di rilevamento argomento

Verrà visualizzata una nuova versione dell'API che restituisce nella parte superiore rilevato argomenti per un elenco di invio dei record di testo. Un argomento viene identificato con una frase chiave, che può essere uno o più correlati parole. Si noti che questa API addebita 1 transazione per ogni record di testo inviato.

L'API richiede un minimo di 100 record di testo da presentare, ma è progettato per rilevare argomenti centinaia a migliaia di record.


### <a name="topics--submit-job"></a>Argomenti: processo di invio

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Richiesta di esempio**


Nella chiamata POST ti chiediamo argomenti di un insieme di 100 articoli, in cui vengono visualizzati gli articoli di input nome e il cognome e due StopPhrases sono inclusi.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corpo della richiesta:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Nella risposta, viene visualizzato il processo per il processo inviato:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Elenco di singola parola o più frasi che non devono essere restituite come argomenti. Può essere utilizzato per filtrare gli argomenti molto generici. Ad esempio, in un set di dati sulle recensioni albergo "albergo" e "hostel" può essere sensibile Interrompi frasi.  

### <a name="topics--poll-for-job-results"></a>Argomenti: sondaggio per risultati ottimali processo

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Richiesta di esempio**

Passare il processo restituito dal passaggio il processo di invio per recuperare i risultati. Si consiglia di chiamare l'endpoint minuto fino a stato = 'Completamento' nella risposta. Sarà necessaria circa 10 minuti per un processo di completamento o allungare per i processi con migliaia di record.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Durante l'elaborazione, la risposta sarà come indicato di seguito:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


L'API restituisce output in formato JSON nel formato seguente:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Le proprietà per ogni parte della risposta sono i seguenti:

**Proprietà TopicInfo**

| Chiave | Descrizione |
|:-----|:----|
| TopicId | Identificatore univoco per ogni argomento. |
| Punteggio | Numero di record assegnati all'argomento. |
| KeyPhrase | Una parola o frase per l'argomento in. Può essere 1 o più parole. |

**Proprietà TopicAssignment**

| Chiave | Descrizione |
|:-----|:----|
| ID | Identificatore del record. Corrisponde all'ID incluse nell'input. |
| TopicId | ID argomento che il record è stato assegnato a. |
| Distanza | Confidenza che il record appartiene all'argomento. Distanza a zero indica una probabilità superiore. |
