<properties 
    pageTitle="Come configurare i punti finali apprendimento Azure in flusso Analitica | Microsoft Azure" 
    description="Funzioni definite dall'utente linguaggio macchina in flusso Analitica"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Lavorare integrazione di apprendimento in flusso Analitica

Flusso Analitica supporta le funzioni definite dall'utente che effettua una chiamata a endpoint di apprendimento Azure. Supporto API REST per questa funzionalità è illustrato nella [raccolta API REST Analitica flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx). In questo articolo fornisce informazioni supplementari necessarie per corretta implementazione di questa funzionalità di flusso Analitica. Un'esercitazione è stata registrata anche e sono disponibile [qui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Panoramica: Terminologia di Azure apprendimento

Microsoft Azure Machine Learning offre uno strumento di collaborazione, trascinamento della selezione che è possibile utilizzare per creare, testare e distribuire soluzioni analitica stima sui dati. Questo strumento è denominato *Azure Machine Learning Studio*. Studio viene utilizzato per interagire con le risorse di formazione computer facilmente creare, testare e continuerà a lavorare sulla struttura. Queste risorse e le relative definizioni sono inferiori.

- **Area di lavoro**: l' *area di lavoro* è un contenitore che contiene tutte le altre risorse di formazione computer insieme in un contenitore per la gestione e controllo.
- **Prova**: *esperimenti* vengono creati scienziati dati utilizzare set di dati e formare un modello di apprendimento computer.
- **Punto finale**: *endpoint* sono oggetto Azure Machine formazione di utilizzare caratteristiche come input, applicare un modello di apprendimento computer specificato per restituire l'output punteggio.
- **Servizio di classificazione**: un *servizio di classificazione* è una raccolta di endpoint come descritto in precedenza.

Ogni endpoint dispone di API per esecuzione batch e l'icona del esecuzione. Flusso Analitica utilizza l'icona del esecuzione. Il servizio è un [Servizio di richiesta/risposta](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) di studio AzureML.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Apprendimento risorse necessarie per i processi di flusso Analitica

Per quanto riguarda flusso Analitica l'elaborazione di, un endpoint di richiesta/risposta, un [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)e una definizione di swagger sono tutte le necessarie per la corretta esecuzione. Flusso Analitica dispone di un endpoint aggiuntivo che genera l'url per l'endpoint swagger, Cerca l'interfaccia e restituisce una definizione di utente predefinito all'utente.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurare un flusso Analitica e apprendimento automatico utente tramite API REST

Utilizzando le API REST è possibile configurare il processo per chiamare le funzioni di linguaggio macchina Azure. I passaggi sono i seguenti:

1. Creare un processo di flusso Analitica
2. Definire un input
3. Definire un output
4. Creare una funzione definita dall'utente (utente)
5. Scrivere una trasformazione flusso Analitica che chiama l'utente
6. Avviare il processo

## <a name="creating-a-udf-with-basic-properties"></a>Creazione di un'utente con le proprietà di base

Ad esempio, il codice di esempio seguente viene creato un'utente scalare denominata *newudf* associato a un endpoint di apprendimento Azure. Si noti che l' *endpoint* (servizio URI) sono disponibili nella pagina della Guida all'API per il servizio scelto e *apiKey* sono disponibili nella pagina principale dei servizi.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corpo della richiesta esempio:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chiamata RetrieveDefaultDefinition endpoint per impostazione predefinita utente

Dopo la struttura viene creato l'utente è necessaria la definizione completa dell'utente. L'endpoint RetreiveDefaultDefinition consente di ottenere la definizione predefinita per una funzione scalare associata a un endpoint di apprendimento Azure. Payload riportata di seguito è necessario ottenere la definizione di utente predefinito per una funzione scalare associata a un endpoint di apprendimento Azure. Non consente di specificare l'endpoint effettivo mentre è già stata fornita durante una richiesta di caricamento. Flusso Analitica chiama il punto finale fornito nella richiesta se viene fornito in modo esplicito. In caso contrario utilizza quello originale a cui fa riferimento. Di seguito accetta utente un'unica stringa parametro (una frase) e restituisce un unico output di tipo stringa che indica l'etichetta "valutazione in" per tale frase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corpo della richiesta esempio:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un esempio di output di questo sarà simile di sotto.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>UTENTE patch con la risposta 

A questo punto l'utente deve essere aggiornati con la risposta precedente, come illustrato di seguito.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo della richiesta (l'Output RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementare trasformazione flusso Analitica chiamare l'utente

Ora query utente (qui denominato scoreTweet) per ogni evento di input e scrivere una risposta per l'evento a un output.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
