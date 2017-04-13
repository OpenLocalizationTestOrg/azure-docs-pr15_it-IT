<properties
   pageTitle="Eccezioni App logica | Microsoft Azure"
   description="Informazioni su modelli per l'errore e gestione delle eccezioni con le applicazioni di logica di Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Errore di App logico ed eccezioni

Logica App offre una vasta gamma di strumenti e modelli per garantire le integrazioni sono efficaci e flessibili da errori.  Uno dei problemi con qualsiasi architettura di integrazione assicurarsi che i tempi di inattività o problemi dei sistemi dipendenti vengono gestiti in modo appropriato.  Logica App consente di gestione degli errori un'esperienza ottimale, fornendo gli strumenti che è necessario agire su eccezioni e gli errori all'interno di flusso di lavoro.

## <a name="retry-policies"></a>Ripetere i criteri

Tipo di eccezione e la gestione degli errori di base è un criterio di tentativi.  Questo criterio consente di definire se l'azione deve riprendere se richiesta iniziale timeout o non è riuscita (qualsiasi richiesta che ha generato un 429 o risposta 5xx).  Per impostazione predefinita, tutte le azioni tentativi di 4 aggiuntive per gli intervalli di 20 secondi.  Quindi, se la prima richiesta ricevuto un `500 Internal Server Error` risposta, il motore di flusso di lavoro si interrompe per 20 secondi e tentativo di richiesta di nuovo.  Se dopo tutti i tentativi la risposta è ancora un'eccezione o errore, il flusso di lavoro verrà continuare e contrassegnare lo stato dell'azione come `Failed`.

È possibile configurare i criteri di tentativi di **input** di un'azione particolare.  Un criterio di Riprova può essere configurato per provare gli intervalli fino a 4 ore su 1 ora.  Dettagli completi sulle proprietà di input possono essere [disponibili su MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se si desidera utilizzare l'azione HTTP a tentativi di 4 e attendere 10 minuti tra ogni tentativo di avere la definizione seguente:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Per ulteriori informazioni sulla sintassi supportata, visualizzare la [sezione relativa ai criteri Riprova su MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Proprietà RunAfter per rilevare danneggiato

Ogni azione di app logica dichiara le azioni che è necessario completare prima di avviare l'azione.  È possibile pensare di queste come l'ordinamento dei passaggi del flusso di lavoro.  Questo ordinamento è nota come la `runAfter` proprietà nella definizione dell'azione.  Si tratta di un oggetto che descrive le azioni e stati azione da eseguire l'azione.  Per impostazione predefinita, tutte le azioni aggiunte mediante progettazione sono impostate su `runAfter` il passaggio precedente, se è stato il passaggio precedente `Succeeded`.  Tuttavia, è possibile personalizzare questo valore per generare azioni quando sono le operazioni precedenti `Failed`, `Skipped`, o una serie di questi valori possibili.  Se si desidera aggiungere un elemento a un argomento Bus di servizio designato dopo un'azione specifica `Insert_Row` non riesce, è necessario utilizzare le seguenti `runAfter` configurazione:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Avviso di `runAfter` sia impostata su attivato se la `Insert_Row` è un'azione `Failed`.  Per eseguire l'azione, se lo stato dell'azione `Succeeded`, `Failed`, o `Skipped` la sintassi sarebbe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Azioni da eseguire dopo un'azione precedente non è riuscita e completare correttamente, verranno contrassegnate come `Succeeded`.  Questo comportamento indica che è correttamente catture tutti gli errori in un flusso di lavoro Esegui stesso viene contrassegnato come `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Ambiti e i risultati per valutare azioni

Simile a come è possibile eseguire dopo singole azioni, è anche possibile azioni gruppo all'interno di un [ambito](app-service-logic-loops-and-scopes.md) - utilizzati come un raggruppamento logico delle azioni.  Gli ambiti sono utili per organizzare le azioni di applicazione logica e per l'esecuzione di aggregazione valutazioni sullo stato di un ambito.  L'ambito della stessa verrà visualizzato uno stato dopo avere completato tutte le azioni in un ambito.  Lo stato dell'ambito è determinato con gli stessi criteri Esegui - se l'ultima azione in una diramazione esecuzione `Failed` o `Aborted` stato è impostato su `Failed`.

È possibile `runAfter` è stato contrassegnato un ambito `Failed` di azioni specifiche per gli eventuali errori che si è verificato all'interno dell'ambito.  Dopo che si verifica un errore di un ambito consente di creare una singola azione per rilevare danneggiato se hanno esito negativo *le* azioni nell'ambito.

### <a name="getting-the-context-of-failures-with-results"></a>Ottenere il contesto di errori con i risultati

Intercettazione degli errori da un ambito è molto utile, ma è possibile il contesto per comprendere esattamente le azioni che non è riuscite ed eventuali errori o codici di stato che sono stati restituiti.  Il `@result()` funzione del flusso di lavoro fornisce contesto nel risultato di tutte le azioni in un ambito.

`@result()`accetta un singolo parametro, un nome di ambito e restituisce una matrice di tutti i risultati di azioni da tale ambito.  Questi oggetti azione includono gli stessi attributi di `@actions()` oggetto, inclusi ora di inizio azione, data/ora fine azione, lo stato di azione, input azione, ID di correlazione di azione e azione genera.  È possibile associare facilmente un `@result()` è utilizzabile con un `runAfter` inviare contesto di tutte le azioni che non è un ambito.

Se si desidera eseguire un'azione *per ogni* azione in un ambito che `Failed`, è possibile associare `@result()` con un'azione **[In forma di matrice di filtro](../connectors/connectors-native-query.md)** e un ciclo **[ForEach](app-service-logic-loops-and-scopes.md)** .  In questo modo è possibile filtrare la matrice di risultati alle azioni che non è riuscita.  È possibile eseguire la matrice dei risultati filtrati ed esegua un'azione per ogni errore utilizzando il ciclo **ForEach** .  Ecco un esempio riportato di seguito, seguito da una spiegazione dettagliata.  In questo esempio invierà una richiesta HTTP POST con il corpo della risposta di tutte le azioni che non è riuscito all'interno dell'ambito `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Ecco una descrizione dettagliata della panoramica:

1. Azione di **Matrice di filtro** per filtrare la `@result('My_Scope')` per ottenere il risultato di tutte le azioni all'interno`My_Scope`
1. Condizione di **Filtro in forma di matrice** è any `@result()` elemento con lo stato uguale a `Failed`.  Si filtrerà la matrice di tutti i risultati di azione da `My_Scope` a solo una matrice di risultati di azione non riuscita.
1. Eseguire un'azione **per ogni** sugli output **Filtrata in forma di matrice** .  Verranno eseguite *per ogni* Errore azione risultato di un'azione è filtrati in precedenza.
    - In caso di una singola azione nell'ambito che non è riuscita, le azioni nel `foreach` verrebbe eseguito solo una volta.  Molte azioni non riuscite può provocare un'azione per errore.
1. Inviare un POST HTTP `foreach` elemento corpo della risposta, o `@item()['outputs']['body']`.  Il `@result()` forma voce corrisponde la `@actions()` modificare la forma e può essere analizzato nello stesso modo.
1. Inclusi anche due intestazioni personalizzate con il nome dell'azione non riuscito `@item()['name']` ed eseguire il volume client verifica ID `@item()['clientTrackingId']`.

Per riferimento, ecco un esempio di una singola `@result()` elemento.  È possibile visualizzare il `name`, `body`, e `clientTrackingId` proprietà analizzato nell'esempio precedente.  Si noti che all'esterno di un `foreach`, `@result()` restituisce una matrice di tali oggetti.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

È possibile usare le espressioni sopra per eseguire diversi modelli di gestione delle eccezioni.  È possibile scegliere di eseguire un'unica eccezione Gestione azione all'esterno dell'ambito che accetta l'intera matrice filtrata di errori e rimuovere il `foreach`.  È anche possibile includere altre proprietà utili dalla `@result()` risposta indicata sopra.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostica Windows Azure e telemetria

Motivi indicati sono un ottimo modo per gestire errori ed eccezioni all'interno di una sequenza, ma è anche possibile identificare e risolvere gli errori indipendenti dell'esecuzione della stessa.  [Diagnostica Azure](app-service-logic-monitor-your-logic-apps.md) offre un modo semplice per inviare tutti gli eventi del flusso di lavoro (inclusi tutti gli stati di esecuzione e azione) a un account di archiviazione Azure o un Hub di evento Azure.  È possibile monitorare i log e metriche o pubblicarle in uno strumento di monitoraggio che si preferisce, valutare stati Esegui.  Un'opzione potenziale è il flusso di tutti gli eventi mediante Azure evento Hub in [Flusso Analitica](https://azure.microsoft.com/services/stream-analytics/).  In flusso Analitica è possibile scrivere query live di fuori dell'eventuali alterazioni, medie o errori da registri diagnostici.  Flusso Analitica eseguire facilmente l'output ad altre origini dati quali code argomenti, SQL, DocumentDB e Power BI.

## <a name="next-steps"></a>Passaggi successivi
- [Vedere come un cliente integrato affidabili gestione degli errori con logica di App](app-service-logic-scenario-error-and-exception-handling.md)
- [Trovare ulteriori esempi di logica App e scenari](app-service-logic-examples-and-scenarios.md)
- [Informazioni su come creare distribuzioni automatiche delle App logica](app-service-logic-create-deploy-template.md)
- [Progettare e implementare logica app da Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9