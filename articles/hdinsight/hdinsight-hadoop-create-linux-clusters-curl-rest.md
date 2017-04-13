<properties
    pageTitle="Creare cluster Hadoop, HBase o eccesso su Linux in uso curvatura e le API REST di Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster basati su Linux HDInsight mediante curvatura, Manager delle risorse Azure modelli e le API REST di Azure. È possibile specificare il tipo di grafico (Hadoop, HBase o eccesso) oppure utilizzare gli script per installare componenti personalizzati."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Creare cluster basati su Linux in uso curvatura e le API REST di Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

API REST Azure consente di eseguire operazioni di gestione dei servizi ospitate piattaforma Azure, inclusa la creazione di nuove risorse, ad esempio cluster basati su Linux HDInsight. In questo documento verrà descritto come creare modelli di gestione di risorse Azure per configurare un cluster di HDInsight e di archiviazione associato, quindi usare curvatura per distribuire il modello all'API REST Azure per creare un nuovo cluster HDInsight.

> [AZURE.IMPORTANT] La procedura descritta in questo documento utilizza il numero predefinito dei nodi di lavoro (4) per un cluster di HDInsight. Se si prevede di più di 32 nodi di lavoro, al momento della creazione cluster o modificando il cluster dopo la creazione, è necessario selezionare una dimensione di nodo principale con almeno 8 core e 14GB di ram.
>
> Per ulteriori informazioni su costi associati e le dimensioni massime nodo, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure CLI__. CLI Azure viene utilizzata per creare un servizio principale, che verrà utilizzato per generare i token di autenticazione per le richieste all'API REST Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __voltare__. Questa utilità è disponibile tramite il sistema di gestione dei pacchetti o può essere scaricata dal [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Se si utilizza PowerShell per eseguire i comandi nel documento, è necessario rimuovere prima la `curl` alias creati per impostazione predefinita. Questo alias utilizza WebRequest richiama, un cmdlet di PowerShell, anziché curvatura quando si utilizza il `curl` comando da un prompt dei comandi PowerShell e restituirà errori per molti dei comandi utilizzati in questo documento.
    > 
    > Per rimuovere l'alias, utilizzare la seguente dal prompt dei comandi PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Dopo aver rimosso l'alias, dovrebbe essere possibile usare la versione di curvatura installata nel sistema.

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Creare un modello

Modelli di gestione delle risorse Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse al suo interno (ad esempio HDInsight.) Questo approccio modello in base a consente di definire tutte le risorse necessarie per HDInsight in un modello e per gestire le modifiche al gruppo intero tramite __distribuzioni__ da applicare le modifiche al gruppo.

Modelli sono in genere in due parti; il modello e un file di parametri che è popolare con valori specifici per la configurazione. Esempio del metodo, il nome del cluster, nome dell'amministratore e la password. Quando si utilizza direttamente all'API REST, è necessario combinare in un file. Il formato di questo documento JSON è:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Ad esempio, di seguito è una fusione i file di modello e i parametri del [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), che consente di creare un cluster basato su Linux con una password per proteggere l'account utente SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

In questo esempio verrà utilizzato nei passaggi descritti in questo documento. È necessario sostituire i segnaposto _valori_ nella sezione __parametri__ alla fine del documento con i valori che si desidera utilizzare per il cluster.

##<a name="login-to-your-azure-subscription"></a>Accesso al proprio abbonamento Azure

Seguire la procedura descritta in [connessione a un abbonamento Azure dall'interfaccia della riga di comando Azure Azure CLI ()](../xplat-cli-connect.md) e connettersi al abbonamento mediante la `azure login` comando.

##<a name="create-a-service-principal"></a>Creare un'entità di servizio

> [AZURE.NOTE] Questa procedura è una versione ridotta delle informazioni disponibili nella sezione _servizio di autenticazione dell'entità con una password - CLI Azure_ del documento [autenticazione un'entità servizio Gestione risorse Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Questa procedura crea un nuovo servizio principale che può essere utilizzato per eseguire l'autenticazione richieste API REST usate per creare Azure risorse, ad esempio un cluster di HDInsight.

1. Dal prompt dei comandi sessione terminal o shell, usare il comando seguente per visualizzare un elenco delle sottoscrizioni Azure.

        azure account list
        
    Nell'elenco, selezionare l'abbonamento che si desidera utilizzare e prendere nota della colonna __Id__ . Questo è l' __ID di abbonamento__ e verrà utilizzato nella maggior parte dei passaggi descritti in questo documento.

2. Creare una nuova applicazione di Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Sostituire i valori per il `--name`, `--home-page`, e `--identifier-uris` con valori personalizzati. Fornire una password per la nuova voce di Active Directory.
    
    > [AZURE.NOTE] Poiché si sta creando questa applicazione per l'autenticazione tramite un'identità di servizio, il `--home-page` e `--identifier-uris` valori non necessarie fare riferimento a una pagina web effettivo ospitato su internet. solo devono essere URI univoco.
    
    I dati restituiti, salvare il valore di __AppId__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Creare un servizio principale utilizzando il valore di __AppId__ restituito in precedenza.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     I dati restituiti, salvare il valore di __Id oggetto__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Assegnare il ruolo di __proprietario__ per il servizio principale utilizzando il valore di __ID oggetto__ restituito in precedenza. È anche necessario usare l' __ID abbonamento__ che è stato acquistato in precedenza.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Al termine di questo comando, il servizio principale ha ora accesso proprietario all'ID della sottoscrizione specificata.

##<a name="get-an-authentication-token"></a>Ottenere un token di autenticazione

1. Utilizzare le operazioni seguenti per trovare l' __ID Tenant__ per l'abbonamento.

        azure account show -s <subscription ID>
        
    I dati restituiti, individuare l' __ID Tenant__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Generare un nuovo token tramite l'API REST di Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Sostituire __TenantID__, __AppID__e __la password__ con i valori ottenuto o utilizzato in precedenza.

    Se la richiesta ha esito positivo, si riceverà una risposta 200 serie e il corpo della risposta conterrà un documento JSON.

    Documento JSON restituito da questa richiesta conterrà un elemento denominato __Access__; il valore di questo elemento è il token di accesso che è necessario utilizzare per l'autenticazione richieste utilizzate nelle sezioni successive di questo documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Creare un gruppo di risorse

Utilizzare le operazioni seguenti per creare un nuovo gruppo di risorse. È necessario creare prima di tutto il gruppo prima di poter creare le risorse, ad esempio cluster HDInsight. 

* Sostituire __SubscriptionID__ con l'ID di abbonamento ricevuto durante la creazione dell'entità servizio.
* Sostituire __AccessToken__ con il token di accesso ottenuto nel passaggio precedente.
* Sostituire __DataCenterLocation__ nell'interfaccia di dati che si desidera creare il gruppo di risorse e le risorse, in. Ad esempio, 'Sud centrale US'. 
* Sostituire __ResourceGroupName__ con il nome che si desidera utilizzare per il gruppo:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se la richiesta ha esito positivo, si riceverà una risposta 200 serie e il corpo della risposta conterrà un documento JSON contenente le informazioni sul gruppo. Il `"provisioningState"` elemento conterrà un valore di `"Succeeded"`.

##<a name="create-a-deployment"></a>Creare una distribuzione

Utilizzare le operazioni seguenti per distribuire la configurazione del cluster (valori di modello e un parametro,) per il gruppo di risorse.

* Sostituire __SubscriptionID__ e __AccessToken__ con i valori utilizzati in precedenza. 
* Sostituire __ResourceGroupName__ con il nome del gruppo di risorse creata nella sezione precedente.
* Sostituire __DeploymentName__ con il nome che si desidera utilizzare per la distribuzione.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Se è stato salvato il documento JSON contenente il modello e i parametri in un file, è possibile utilizzare le seguenti anziché `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se la richiesta ha esito positivo, si riceverà una risposta 200 serie e il corpo della risposta conterrà un documento JSON contenente le informazioni sull'operazione di distribuzione.

> [AZURE.IMPORTANT] Si noti che la distribuzione è stata inviata ma non è stata completata al momento. È possibile richiedere alcuni minuti, in genere circa 15 per la distribuzione completare.

##<a name="check-the-status-of-a-deployment"></a>Controllare lo stato di una distribuzione

Per controllare lo stato della distribuzione, utilizzare le operazioni seguenti:

* Sostituire __SubscriptionID__ e __AccessToken__ con i valori utilizzati in precedenza. 
* Sostituire __ResourceGroupName__ con il nome del gruppo di risorse creata nella sezione precedente.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Informazioni viene restituito un documento JSON contenente le informazioni sull'operazione di distribuzione. Il `"provisioningState"` elemento conterrà lo stato di distribuzione. Se questa pagina contiene un valore di `"Succeeded"`, quindi la distribuzione è stata completata. A questo punto, il cluster dovrebbe essere disponibile per l'uso.

##<a name="next-steps"></a>Passaggi successivi

Ora che è stato creato correttamente un cluster di HDInsight, utilizzare le operazioni seguenti per informazioni su come lavorare con i cluster. 

###<a name="hadoop-clusters"></a>Hadoop cluster

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase cluster

* [Guida introduttiva a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppo di applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Cluster eccesso

* [Sviluppare topologie Java per eccesso in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizzare i componenti di Python in eccesso in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare topologie con eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
