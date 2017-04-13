<properties 
    pageTitle="Creare definizioni di logica App | Microsoft Azure" 
    description="Informazioni su come scrivere la definizione di JSON per le applicazioni di logica" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Definizioni di logica App autore
In questo argomento viene illustrato come utilizzare le definizioni di [Applicazioni di logica di Azure](app-service-logic-what-are-logic-apps.md) , un linguaggio di JSON semplice e dichiarativo. Se non è fatto ancora, vedere [come creare una nuova app logica](app-service-logic-create-a-logic-app.md) prima di tutto. È anche possibile leggere il [materiale di riferimento completo del linguaggio di definizione su MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Alcuni passaggi che si ripetono su un elenco

È possibile utilizzare il [tipo di foreach](app-service-logic-loops-and-scopes.md) per ripetere su una matrice di fino a 10 KB elementi ed eseguire un'azione per ogni.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Un passaggio di gestione degli errori in caso di errori

In genere si desidera poter scrivere un *passaggio di monitoraggio e aggiornamento* , ovvero una logica che esegue, se non è riuscita **e solo se**, uno o più delle chiamate. In questo esempio abbiamo stiamo recupero dati da una varietà di posizioni, ma in caso contrario, si desidera inviare un messaggio in un punto qualsiasi in modo che è possibile esaminare in un secondo momento l'errore:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

È possibile apportare uso del `runAfter` proprietà per specificare il `postToErrorMessageQueue` deve essere eseguita solo dopo `readData` è **non riuscito**.  Potrebbe anche trattarsi di un elenco di valori possibili, in modo `runAfter` potrebbe essere `["Succeeded", "Failed"]`.

Infine, poiché è stata gestita a questo punto l'errore, è non è più contrassegnare Esegui **non riuscito**. Come può vedere di seguito, quest'esecuzione è **completato** anche se un passaggio non riuscito, perché è stato scritto il passaggio per gestire l'errore.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>(Più) passaggi eseguiti in parallelo

Disporre di più esecuzione di azioni in parallelo, il `runAfter` proprietà devono essere equivalenti in fase di esecuzione. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Come si può vedere nell'esempio precedente, entrambi `branch1` e `branch2` siano impostati da eseguire dopo `readData`. Di conseguenza, entrambi questi rami verranno eseguite in parallelo:

![Parallelo](./media/app-service-logic-author-definitions/parallel.png)

È possibile visualizzare che il timestamp per entrambe le diramazioni è identico. 

## <a name="join-two-parallel-branches"></a>Unire due diramazioni parallele

È possibile unire due azioni che sono state impostate per l'esecuzione in parallelo mediante l'aggiunta di elementi dalla `runAfter` proprietà simile a sopra.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallelo](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Mappatura di elementi in un elenco a alcune operazioni di configurazione diversi

Successivamente, si supponga che si desidera ricevere contenuto completamente diverso a seconda di un valore di una proprietà. È possibile creare una mappa di valori di destinazioni come parametro:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

In questo caso, è prima di tutto ottenere un elenco di articoli e quindi il secondo passaggio Cerca in una mappa, in base alla categoria definite come parametro, l'URL per ottenere il contenuto da. 

Due elementi di prestare attenzione qui: il [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) funzione viene utilizzata per verificare se la categoria corrisponde a una delle categorie note definite. In secondo luogo, quando si riceve la categoria, è possibile estrarre l'elemento della mappa utilizzando parentesi quadre: `parameters[...]`. 

## <a name="working-with-strings"></a>Utilizzo di stringhe

Esistono diversi tipi di funzioni che possono essere usati per gestire stringa. Di seguito è illustrato un esempio in cui si dispone di una stringa che si desidera passare a un sistema, ma attualmente non sono sicuri che la codifica dei caratteri verrà gestito correttamente. È possibile base 64 codificare stringa. Tuttavia, per evitare il carattere di escape in un URL che verranno sostituire alcuni caratteri. 

Vogliamo anche una sottostringa la l'ordine nome perché non vengono utilizzati i primi 5 caratteri.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Lavorare in modo completo:

1. Ottenere il [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) del nome dell'autore ordine, viene restituito il numero totale di caratteri

2. Sottrarre 5 (perché è consigliabile una stringa più breve)

3. Richiedere il [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Iniziamo indice `5` e passare il resto della stringa.

4. Convertire sottostringa a un [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) stringa

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)tutte le `+` con i caratteri`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)tutte le `/` con i caratteri`_`

## <a name="working-with-date-times"></a>Lavorare con i tempi di data

Data volte può essere utile, soprattutto quando si tenta di estrarre dati da un'origine dati che non supporta naturalmente **trigger**.  È anche possibile utilizzare Data volte per stabilire per quanto tempo vari passaggi siano impiegando. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

In questo esempio è in corso l'estrazione di `startTime` del passaggio precedente. Quindi stiamo ottenere l'ora corrente e sottrazione di un secondo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (è possibile utilizzare altre unità di tempo, ad esempio `minutes` o `hours`). Infine, è possibile confrontare questi due valori. Se il primo è minore del secondo, quindi ciò significa più di un secondo trascorso dall'ordine di stato prima inserito. 

Si noti inoltre che è possibile utilizzare formattatori stringa per formattare date: nella stringa di query si usa [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) per ottenere il RFC1123. Tutti data formattazione [descritte su MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Utilizzo di parametri in fase di distribuzione per ambienti diversi

È necessario un ciclo di vita di distribuzione in cui si dispone di un ambiente di sviluppo, un ambiente di gestione temporanea e un ambiente di produzione comune. In tutte le regole possono vuole la stessa definizione, ma utilizzare database diversi, ad esempio. Analogamente, è consigliabile utilizzare la stessa definizione nelle diverse aree molti disponibilità elevata, ma ogni istanza di app logica di comunicare con i database dell'area. 

Si noti che questa è la differenza tra impiegando parametri diversi al *runtime*, che è necessario utilizzare la `trigger()` funzionare come callout sopra. 

È possibile iniziare con una definizione semplice analogo al seguente:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Quindi, nel effettivo `PUT` richiesta per l'app logica è possibile fornire il parametro `uri`. Si noti come viene a mancare un valore predefinito questo parametro è obbligatorio nel payload app logica:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

In ogni ambiente è quindi possibile fornire un valore diverso per il `connection` parametro. 

Vedere la [documentazione API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) per tutte le opzioni che disponibili per creare e gestire App logica. 
