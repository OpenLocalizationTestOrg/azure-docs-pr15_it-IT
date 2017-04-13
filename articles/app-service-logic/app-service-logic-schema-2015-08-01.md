<properties 
    pageTitle="Nuovo schema versione 2015-08-01-preview" 
    description="Informazioni su come scrivere la definizione di JSON per la versione più recente dell'App logica" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Nuovo schema versione 2015-08-01-preview

Il nuovo schema e API versione per le applicazioni di logica include numerosi miglioramenti che migliorano le prestazioni e facilità di utilizzo delle App logica. Vi sono 4 importanti differenze:

1. Il tipo di azione **APIApp** è stato aggiornato con un nuovo tipo di azione **APIConnection** .
2. **Ripetere** è stato rinominato in **Foreach**.
3. L'app API **Comunicare ascoltatore HTTP** non è più necessaria.
4. Chiamare i flussi di lavoro figlio utilizza un nuovo schema.

## <a name="1-moving-to-api-connections"></a>1. spostamento connessioni API

Il cambiamento è non è più necessario distribuire le applicazioni di API nell'abbonamento Azure per l'utilizzo dell'API. Modi 2 è possibile utilizzare API:
* API gestita
* L'API Web personalizzato

Ognuna di queste viene gestita in modo leggermente diverso in quanto la gestione e modelli di hosting sono diversi. Offrono il vantaggio di questo modello è che non sta non è più limitato alle risorse che vengono distribuite nel gruppo di risorse. 

### <a name="managed-apis"></a>API gestite

Ci sono diverse dell'API gestiti da Microsoft per conto dell'utente, ad esempio Office 365, Salesforce, Twitter, e così via FTP... Alcune di queste API gestita possono essere utilizzati come-è, ad esempio Bing tradurre, mentre altri richiedono una configurazione. Questa configurazione viene definita una *connessione*.

Ad esempio, quando si utilizza Office 365, è necessario creare una connessione che contiene il token di accesso a Office 365. Questo token verrà sicuro archiviato e aggiornato in modo che l'app logica possa chiamare sempre l'API di Office 365. In alternativa, se si desidera connettersi al server SQL o FTP, è necessario creare una connessione con la stringa di connessione. 

All'interno di definizione di queste azioni vengono chiamate `APIConnection`. Ecco un esempio di una connessione che chiama Office 365 per inviare un messaggio di posta elettronica:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

La parte di input in base alle connessioni API è il `host` oggetto. Questa pagina contiene due parti: `api` e `connection`.

Il `api` sia il runtime è ospitato URL della posizione che API gestita di. È possibile visualizzare tutte le API gestite disponibili per si chiamando `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando si usa un'API, possono o non dispone i **parametri di connessione** definiti. In caso contrario alcuna **connessione** è necessario. In caso affermativo, sarà necessario creare una connessione. Quando si crea la connessione è necessario il nome desiderato e quindi si fare riferimento a tale nel `connection` oggetto all'interno di `host` oggetto. Per creare una connessione in un gruppo di risorse, chiamare:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Con il seguente corpo:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Distribuzione di API gestite in un modello di gestione delle risorse di Azure

È possibile creare un'applicazione completa in un modello di ARM, purché non richiede accessohttp interattivi. Se richiede l'esecuzione di accesso, è possibile configurare tutto il necessario con il modello ARM, ma sarà necessario visitare il portale per autorizzare le connessioni. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

È possibile visualizzare in questo esempio che le connessioni siano solo normale risorse che risiedono nel gruppo di risorse. Fanno riferimento managedAPIs disponibili per l'abbonamento.

### <a name="your-custom-web-apis"></a>L'API Web personalizzato

Se si usa il proprio API (in particolare, quelle non gestite Microsoft), è necessario usare l'azione **HTTP** incorporata per chiamare loro. Per ottenere un ambiente ideale, è necessario esporre un endpoint swagger per l'API. In questo modo la progettazione di applicazioni logica per il rendering di input e output per l'API. Senza un swagger la finestra di progettazione solo sarà possibile visualizzare input e output come oggetti JSON opachi.

Ecco un esempio che mostra i nuovi `metadata.apiDefinitionUrl` proprietà:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Se invece si ospitano l'API Web **App** servizio quindi verrà automaticamente visualizzato nell'elenco delle azioni disponibili nella finestra di progettazione. In caso contrario, è necessario incollare l'URL direttamente. L'endpoint swagger deve essere non autenticato per essere utilizzabili all'interno di progettazione di applicazioni logica (anche se proteggere l'API con qualsiasi metodi sono supportati nel Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Usare le app API già distribuite con anteprima 01 di 08 2015

Se già stato distribuito un'app di API, è possibile chiamare tramite l'azione **HTTP** .

Ad esempio, se si usa Dropbox per elencare i file, potrebbe essere simile al seguente nella definizione della versione di schema **2014-12-01-anteprima** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

È possibile creare l'azione HTTP equivalente come sotto (sezione parametri di logica app definizione rimarrà invariato):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Esame queste proprietà uno alla volta:

| Proprietà di azione |  Descrizione |
| --------------- | -----------  |
| `type` | `Http`Invece di`APIapp` |
| `metadata.apiDefinitionUrl` | Se si desidera usare questa azione nella finestra di progettazione di applicazioni logica, verrà da includere l'endpoint metadati. Questo è costruito da:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Questo è costruito da:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre`POST` |
| `inputs.body` | Identico ai parametri app api | 
| `inputs.authentication` | Identico per l'autenticazione di app api |

Questo approccio dovrebbe funzionare per tutte le azioni app API. Tuttavia, tenere presente che queste App API precedente non sono più supportate che è necessario spostare a uno dei due altre opzioni sopra (API gestita o l'API Web personalizzato di hosting).

## <a name="2-repeat-renamed-to-foreach"></a>2. Ripeti rinominato in Foreach

Per la versione precedente di schema abbiamo ricevuto molte feedback dei clienti che **ripetere** stato confusione e non ha acquisito correttamente che fosse effettivamente una per ciascuno di essi. Di conseguenza, abbiamo abbiamo rinominato **Foreach**. Per esempio:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

A questo punto verrebbero indicate come:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

In precedenza la funzione `@repeatItem()` è stato utilizzato per fare riferimento all'elemento corrente scorso. Ciò è stato semplificato subito `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Riferimento output di Foreach
Per semplificare ulteriormente l'output di azioni **Foreach** non inclusi in un oggetto denominato **repeatItems**. Questo significa che, mentre l'output di ripetizione sopra erano:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

A questo punto occorre:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Quando si fa riferimento questi output, per posizionarsi nel corpo dell'azione è necessario eseguire:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

A questo punto è possibile eseguire:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Con queste modifiche, le funzioni `@repeatItem()`, `@repeatBody()` e `@repeatOutputs()` vengono rimossi.

## <a name="3-native-http-listener"></a>3. nativo comunicare ascoltatore HTTP 
Le funzionalità di comunicare ascoltatore HTTP sono ora predefinite, in modo che non è più necessario distribuire un'app API comunicare ascoltatore HTTP. Informazioni su [tutti i dettagli per rendere l'endpoint di app logica chiamato qui](app-service-logic-http-endpoint.md). 

Con queste modifiche, la funzione `@accessKeys()` viene rimosso ed è stata sostituita con il `@listCallbackURL()` funzione ai fini della Guida endpoint (se necessario). Inoltre, è ora necessario definire almeno un trigger nell'app logica ora. Se si desidera `/run` il flusso di lavoro, è necessario disporre di una di un `manual`, `apiConnectionWebhook` o `httpWebhook` trigger. 

## <a name="4-calling-child-workflows"></a>4. flussi di lavoro figlio si chiama

Chiamare i flussi di lavoro figlio obbligatorio in precedenza, passando il flusso di lavoro, ottenere il token di accesso e quindi incollando l'elemento che nella definizione dell'app logica che si desidera chiamare tale elemento figlio. Con la nuova versione di schema, il motore di App logica genera automaticamente una SA in fase di esecuzione del flusso di lavoro figlio, il che significa che non è necessario incollare le informazioni riservate la definizione.  Ecco un esempio:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Una seconda analisi utilizzo software è che abbiamo verrà da parte di flussi di lavoro figlio accesso completo alla richiesta di posta in arrivo. Ciò significa che è possibile passare parametri nella sezione *query* e nell'oggetto *intestazioni* e che è possibile definire completamente l'intero corpo.

Infine, sono disponibili le modifiche necessarie al flusso di lavoro figlio. Mentre prima che è possibile semplicemente chiamare un flusso di lavoro figlio direttamente. a questo punto, è necessario definire un endpoint trigger del flusso di lavoro per l'elemento padre da chiamare. In genere, significa che è necessario aggiungere un trigger di tipo **manuale** e quindi usare che della definizione padre. Si noti che la `host` proprietà di un `triggerName`, perché è necessario specificare quali trigger si richiama.

## <a name="other-changes"></a>Altre modifiche

### <a name="new-queries-property"></a>Nuova proprietà query
Tutti i tipi di azione supportano un nuovo input denominate **query**. Può trattarsi di un oggetto strutturato anziché dover assemblare manualmente la stringa.

### <a name="parse-function-renamed"></a>funzione Parse rinominata
Come verranno appena aggiunti altri tipi di contenuto, la `parse()` funzione è stato rinominato in `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Disponibile a breve: API di integrazione Enterprise
In questo momento, è ancora non si dispone di gestire le versioni delle API di integrazione Enterprise disponibili (ad esempio AS2). Questi sarà presto descritte nella [Guida di orientamento](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Nel frattempo, è possibile utilizzare il APIs BizTalk distribuito esistenti tramite l'azione HTTP come descritti sopra in "usando le app API già distribuite."
