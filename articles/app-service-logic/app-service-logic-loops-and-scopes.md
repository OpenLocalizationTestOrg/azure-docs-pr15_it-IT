<properties
   pageTitle="Logica App cicli ambiti e Debatching | Microsoft Azure"
   description="Esegui ciclo continuo App logica, limitare l'ambito e debatching concetti"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logica App cicli, ambiti e Debatching
  
>[AZURE.NOTE] Questa versione dell'articolo si applica a schema logica App 2016-04-01-anteprima e versioni successive.  Concetti sono simili per gli schermi precedenti, ma gli ambiti sono disponibili solo per questo schema e versioni successive.
  
## <a name="foreach-loop-and-arrays"></a>Ciclo ForEach e le matrici
  
Logica App consente di eseguire un ciclo in un set di dati ed eseguire un'azione per ogni elemento.  Questo è possibile tramite il `foreach` azione.  Nella finestra di progettazione, è possibile specificare per aggiungere un per ciascuno di essi.  Dopo aver selezionato la matrice che si desidera scorrere, è possibile iniziare ad aggiungere azioni.  Attualmente l'utente è limitato a solo un'azione per ciclo foreach ma verrà eliminata questa limitazione prossime settimane.  Una volta all'interno del ciclo è possibile iniziare specificare quali dovrebbe essere eseguita ogni valore della matrice.

Se tramite la visualizzazione di codice, è possibile specificare una per ogni ciclo come sotto.  Questo è un esempio di una per ogni ciclo che invia un messaggio di posta elettronica per ogni indirizzo di posta elettronica che contiene "microsoft.com":

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` azione è possibile scorrere over matrici fino a 5.000 righe.  Ogni iterazione può eseguire in parallelo, in modo che potrebbe essere necessario aggiungere i messaggi a una coda se è necessario un controllo di flusso.
  
## <a name="until-loop"></a>Fino a ciclo continuo
  
  È possibile eseguire un'azione o una serie di azioni finché non viene soddisfatta una condizione.  Lo scenario più comune per questa è la chiamata a un endpoint fino a ottenere la risposta che si sta cercando.  Nella finestra di progettazione, è possibile specificare per aggiungere un fino a ciclo continuo.  Dopo aver aggiunto azioni all'interno del ciclo, è possibile impostare la condizione di uscita, nonché il ciclo limiti.  Si verifica un ritardo minuto tra i cicli di ciclo continuo.
  
  Se tramite la visualizzazione di codice, è possibile specificare un fino a ciclo continuo come sotto.  Si tratta di un esempio di chiamata un endpoint HTTP fino a quando la risposta è il valore 'Completato'.  Il test verrà eseguito quando uno dei due 
  
  * Risposta HTTP ha lo stato 'Completato'
  * Ha tentato per 1 ora
  * Essa loopback 100 volte
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e Debatching

Può succedere che un trigger che venga visualizzato una matrice di elementi che si desidera debatch e avviare un flusso di lavoro per ogni elemento.  Questa operazione può essere eseguita tramite il `spliton` comando.  Per impostazione predefinita, se il swagger trigger specifica un payload che è una matrice, un `spliton` verrà aggiunto e avviare un'esecuzione per ogni elemento.  SplitOn possono essere aggiunti solo a un trigger.  Ciò essere configurato manualmente o sovrascritti nella visualizzazione codice definizione.  Attualmente è possibile debatch SplitOn matrici fino a 5.000 elementi.  Non è possibile specificare un `spliton` e implementare anche il modello di risposta sincrono.  Qualsiasi flusso di lavoro denominato che presenta un `response` azione oltre a `spliton` verrà eseguito maniera e inviare un immediato `202 Accepted` risposta.  

SplitOn può essere specificato nella visualizzazione codice come nell'esempio seguente.  Questo riceve una matrice di elementi e debatches in ogni riga.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Ambiti

È possibile raggruppare una serie di azioni collaborare usando un ambito.  Questa operazione è particolarmente utile per l'implementazione di eccezioni.  Nella finestra di progettazione è possibile aggiungere un nuovo ambito e iniziare ad aggiungere tutte le azioni all'interno di essa.  È possibile definire gli ambiti nella visualizzazione codice simile al seguente:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
