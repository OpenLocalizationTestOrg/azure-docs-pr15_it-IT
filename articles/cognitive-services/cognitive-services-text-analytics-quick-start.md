<properties
    pageTitle="Guida introduttiva: Machine Learning testo Analitica API | Microsoft Azure"
    description="Testo Analitica - Guida introduttiva di apprendimento Azure"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Guida introduttiva con le API Analitica testo per il rilevamento di valutazione in, le frasi principali, argomenti e della lingua

<a name="HOLTop"></a>

Questo documento descrive come a integrata del servizio o dell'applicazione si utilizzano le [API Analitica di testo](//go.microsoft.com/fwlink/?LinkID=759711).
È possibile utilizzare queste API per il rilevamento di valutazione in, le frasi principali, argomenti e lingua dal testo. [Fare clic qui per visualizzare una dimostrazione interattiva dell'ambiente.](//go.microsoft.com/fwlink/?LinkID=759712)

Consultare le [definizioni delle API](//go.microsoft.com/fwlink/?LinkID=759346) di documentazione tecnica per le API.

Questa guida è rivolta versione 2 di API. Per informazioni dettagliate sulla versione 1 di API, [fare riferimento al documento](../machine-learning/machine-learning-apps-text-analytics.md).

Al termine di questa esercitazione, sarà possibile rilevare a livello di programmazione:

- **Valutazione in** - è di tipo testo numero positivo o negativo?

- **Frasi chiave** - quali sono le persone che illustrano in un unico articolo?

- **Gli argomenti** - quali sono le persone che illustrano tra molti articoli?

- **Lingue** - lingue con le quali sono un testo scritto in?

Si noti che questa API costi 1 transazione per ogni documento inviato. Ad esempio, se si richiede di valutazione in per i documenti di 1000 in un'unica chiamata verranno dedotta transazioni 1000.



<a name="Overview"></a>
## <a name="general-overview"></a>Informazioni generali ##

In questo documento è una Guida dettagliata. Il nostro obiettivo è per scorrere i passaggi necessari per formare un modello e per scegliere le risorse che consentono di mettere in produzione. Questo esercizio richiederà circa 30 minuti.

Per queste attività, sarà necessario un editor e chiamare endpoint REST nella lingua scelta.

Iniziamo!

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Attività 1 - firma per le API Analitica testo ####

In questa attività si verrà effettua l'iscrizione al servizio analitica testo.

1. Passare a **Servizi Cognitive** nel [Portale di Azure](//go.microsoft.com/fwlink/?LinkId=761108) e verificare **Che Analitica testo** sia selezionato come tipo di API' '.

1. Selezionare un piano. È possibile selezionare il **livello gratuito per le transazioni 5.000/mese**. Come è un piano gratuito, non ti per l'utilizzo del servizio. Sarà necessario eseguire l'accesso all'abbonamento Azure. 

1. Completare gli altri campi e creare l'account.

1. Dopo che ci si iscrive a testo Analitica, trovare la **Chiave dell'API**. Copiare la chiave primaria come sarà necessario quando si utilizzano i servizi di API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Attività 2: rilevare valutazione in, le frasi principali e lingue ####

È facile rilevare valutazione in, le frasi principali e lingue il testo. Si otterrà a livello di programmazione gli stessi risultati ottenuti come l' [esperienza demo](//go.microsoft.com/fwlink/?LinkID=759712) restituisce.

>[AZURE.TIP] Per l'analisi di valutazione in, è consigliabile dividere il testo in frasi. Questa operazione comporta in genere una maggiore precisione le stime di valutazione in.

Si noti che le lingue supportate sono i seguenti:

| Caratteristica | Codici di lingua supportata |
|:-----|:----|
| Valutazione in | `en`(In inglese), `es` (spagnolo) `fr` (francese) `pt` (Portogallo) |
| Frasi principali | `en`(In inglese), `es` (spagnolo) `de` (tedesco) `ja` (giapponese) |


1. È necessario impostare le intestazioni di seguito. Si noti che JSON attualmente è il formato di input accettato solo per le API. XML non è supportata.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Successivamente, formattare le righe di input in JSON. Per la valutazione in, le frasi principali e lingua, il formato è lo stesso. Si noti che tutti gli ID deve essere univoco e verrà restituito dal sistema l'ID. La dimensione massima di un singolo documento che può essere inviato è 10KB e la dimensione massima totale di input inviato è 1MB. Non più di 1.000 documenti possono essere presentati in una chiamata. Limitazione della velocità esiste pari a 100 chiamate al minuto, è pertanto consigliabile inviare grandi quantità di documenti in un'unica chiamata. Lingua è un parametro facoltativo che è necessario specificare se analisi testo inglese. Viene illustrato un esempio di input seguente, in cui il parametro facoltativo `language` di valutazione in è inclusa l'estrazione di una frase analisi o chiave:

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Effettuare una chiamata **POST** al sistema con l'input per la valutazione in, le frasi principali e lingua. URL di un aspetto come indicato di seguito:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. La chiamata restituirà un JSON formattate risposta con l'ID e rilevate proprietà. Seguito è riportato un esempio di output per valutazione in (con dettagli sull'errore escluse). In caso di valutazione in, verrà restituito un punteggio compreso tra 0 e 1 per ogni documento:

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Attività 3: rilevare argomenti corpus del testo ####

Verrà visualizzata una nuova versione dell'API che restituisce nella parte superiore rilevato argomenti per un elenco di invio dei record di testo. Un argomento viene identificato con una frase chiave, che può essere uno o più correlati parole. L'API è progettato per funzionare correttamente per testo scritto breve, risorse umano, ad esempio le revisioni e commenti degli utenti.

L'API richiede **un minimo di 100 record di testo** da presentare, ma è progettato per rilevare argomenti centinaia a migliaia di record. Qualsiasi record localizzata o con meno di 3 parole verrà eliminato e pertanto non verranno assegnate ad argomenti. Per il rilevamento argomento, la dimensione massima di un singolo documento che può essere inviato è 30KB e la dimensione massima totale di input inviato è 30MB. Rilevamento argomento è limitato a 5 invii ogni 5 minuti di frequenza.

Esistono due parametri di input aggiuntivi di tipo **facoltativo** che possono risultare utili per migliorare la qualità dei risultati:

- **Interrompere le parole.**  Questi errori ortografici e i moduli Chiudi (ad esempio plurali) verranno esclusi dalla pipeline di rilevamento di tutto l'argomento. Questa scheda per le parole comuni (ad esempio, "problema", "errore" e "utente" potrebbe essere scelte appropriate per reclami sulle software). Ogni stringa deve essere una singola parola.
- **Interrompere frasi** - queste frasi verranno esclusi dall'elenco di argomenti restituiti. Consente di escludere generico agli argomenti a cui non si desidera visualizzare nei risultati. Ad esempio "Microsoft" e "Azure" saranno scelte appropriate per gli argomenti da escludere. Le stringhe possono contenere più parole.

Seguire questa procedura per rilevare argomenti in testo.

1. Formattare l'input in JSON. In questo caso, è possibile definire parole non significative e interrompere frasi.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Utilizzo delle intestazioni di stesso come definito nel passaggio 2, effettuare un **POST di** chiamata per l'endpoint di argomenti:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Verrà restituito un `operation-location` come intestazione nella risposta, in cui il valore è l'URL per eseguire query per gli argomenti risultante:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Query restituito `operation-location` periodicamente con una richiesta **GET** . Una volta al minuto è consigliabile.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. L'endpoint restituirà una risposta inclusi `{"status": "notstarted"}` prima dell'elaborazione, `{"status": "running"}` durante l'elaborazione e `{"status": "succeeded"}` con l'output una volta completata. È quindi possibile utilizzare l'output che si trovano nel formato seguente (nota dettagli come date e formato errore sono state eliminate da questo esempio):

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Si noti che la risposta corretta per gli argomenti della `operations` endpoint avrà schema riportato di seguito:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Le spiegazioni per ogni parte di questa risposta sono i seguenti:

**argomenti**

| Chiave | Descrizione |
|:-----|:----|
| ID | Identificatore univoco per ogni argomento. |
| punteggio | Conteggio dei documenti assegnato all'argomento. |
| keyPhrase | Una parola o frase per l'argomento in. |

**topicAssignments**

| Chiave | Descrizione |
|:-----|:----|
| documentId | Identificatore per il documento. Corrisponde all'ID incluse nell'input. |
| topicId | ID argomento che il documento è stato assegnato a. |
| distanza | Punteggio di argomento del documento per il rapporto tra 0 e 1. Il valore più basso una distanza punteggio maggiore rapporto argomento. |

**errori**

| Chiave | Descrizione |
|:-----|:----|
| ID | Identificatore univoco documento input l'errore si riferisce a. |
| Messaggio | Messaggio di errore. |

## <a name="next-steps"></a>Passaggi successivi ##

Congratulazioni! Sono stati completati utilizzando analitica testo sui dati. A questo punto, è consigliabile esaminare il strumento, ad esempio [Power BI](//powerbi.microsoft.com) per visualizzare i dati, come automatizzare le informazioni approfondite per viene fornita una visualizzazione in tempo reale dei dati di testo.

Per vedere come le funzionalità Analitica di testo, ad esempio di valutazione in, possono essere utilizzate come parte di un bot, vedere l'esempio [Affettivi Bot](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) nel sito Framework Bot.
