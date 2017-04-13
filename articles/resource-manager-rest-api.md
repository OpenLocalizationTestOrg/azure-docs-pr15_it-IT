<properties
   pageTitle="API REST Manager delle risorse | Microsoft Azure"
   description="Cenni preliminari gli esempi di autenticazione e l'uso di API REST Manager delle risorse"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>API REST Manager delle risorse

> [AZURE.SELECTOR]
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [Portale](./azure-portal/resource-group-portal.md) 
- [API REST](resource-manager-rest-api.md)

Dietro ogni chiamata per gestione di risorse di Azure dietro ogni modello distribuito, dietro tutti gli account di archiviazione configurato non esiste una o più chiamate all'API REST del Manager delle risorse Azure. In questo argomento è dedicato a tali API e come è possibile chiamare senza utilizzare qualsiasi SDK tutto. Può essere molto utile se si desidera che il controllo completo di tutte le richieste di Azure o se SDK per la lingua desiderata non è disponibile o non supporta le operazioni da eseguire.

In questo articolo non consente di passare a ogni API esposto in Azure, ma anziché usare alcuni ad esempio come procedere e connettersi a tali account. Se si conoscono le nozioni di base è possibile procedere e leggere il [Riferimento all'API REST di Azure Manager delle risorse](https://msdn.microsoft.com/library/azure/dn790568.aspx) per informazioni dettagliate su come utilizzare il resto delle API.

## <a name="authentication"></a>Autenticazione
Autenticazione di ARM viene gestita da Azure Active Directory (AD). Per connettersi a qualsiasi API è innanzitutto necessario eseguire l'autenticazione con Azure Active Directory per ricevere un token di autenticazione che è possibile passare a ogni richiesta. Come si stiamo descrivere una chiamata pura direttamente all'API REST, si presuppone anche che non si vuole eseguire l'autenticazione con una password username normale nel punto in cui un pop-configurazione-schermo potrebbero Richiedi nome utente e password e forse anche altri metodi di autenticazione utilizzati in due scenari di autenticazione fattore. Di conseguenza, verrà creato cosiddetta applicazione Azure Active Directory e un'entità servizio che verrà utilizzato per eseguire l'accesso con. Ma tenere presente che alcune procedure di autenticazione di supporto di Azure Active Directory e tutti gli elementi possono essere utilizzati per recuperare tale token di autenticazione necessari per le successive richieste API.
Seguire [creare Azure Active Directory applicazione e servizio principio](./resource-group-create-service-principal-portal.md) per istruzioni dettagliate.

### <a name="generating-an-access-token"></a>Generare un Token di accesso 
Autenticazione sulla base di Azure Active Directory viene eseguita chiamate a Azure Active Directory, che si trova in login.microsoftonline.com. Per eseguire l'autenticazione è necessario disporre le informazioni seguenti:

* ID Tenant di Azure Active Directory (il nome di tale Azure Active Directory in uso eseguire l'accesso, spesso la stessa società ma non è necessario)
* ID applicazione (prelevati durante la creazione dell'applicazione di Azure Active Directory)
* Password (che è stata selezionata durante la creazione dell'applicazione di Active Directory Azure)

Nel sotto richiesta HTTP assicurarsi di sostituire "Azure Active Directory Tenant ID", "ID applicazione" e "Password" con i valori corretti.

**Richiesta HTTP generico:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... verrà (se l'autenticazione ha avuto esito positivo) come risultato una risposta simile alla seguente:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Access nella risposta precedente è stato abbreviato per migliorare la leggibilità)

**Generazione di token di accesso tramite Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generazione di token di accesso tramite PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La risposta contiene un Token di accesso, informazioni sulla durata del token è valido e informazioni su quali risorse è possibile utilizzare tale token per.
Il token di accesso ricevuti nella chiamata HTTP precedente deve essere passato per tutte le richieste all'API ARM come intestazione denominata "Autorizzazione" con il valore "Titolare YOUR_ACCESS_TOKEN". Si noti lo spazio tra "Titolare" e il Token di accesso.

Come può vedere dal risultato HTTP sopra, il token è valido per un determinato periodo di tempo durante il quale si deve memorizzare nella cache e riutilizzare tale token stesso. Anche se è possibile autenticare Azure Active Directory per ciascuna chiamata API, sarebbe estremamente scarse.

## <a name="calling-arm-rest-apis"></a>API REST di si chiama ARM

[Di seguito vengono descritte le API REST di Azure Manager delle risorse](https://msdn.microsoft.com/library/azure/dn790568.aspx) e 's fuori ambito per questa esercitazione per l'uso di ogni e ogni del documento. Questa documentazione utilizzerà solo alcune API per illustrare l'utilizzo di base delle API e dopo che si è consultare la documentazione ufficiale.

### <a name="list-all-subscriptions"></a>Elenco di tutte le sottoscrizioni

Una delle operazioni più semplice che è inoltre possibile verrà elencate le sottoscrizioni disponibili che è possibile accedere. Nel sotto richiesta viene visualizzato come Token di accesso viene passato come un'intestazione.

(Sostituire YOUR_ACCESS_TOKEN con i Token di accesso effettivo).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

… e di conseguenza, viene visualizzato un elenco delle sottoscrizioni capitale questo servizio è autorizzato ad accedere

(ID abbonamento riportata di seguito è stato abbreviato per migliorare la leggibilità)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Elenco tutti i gruppi di risorse di una sottoscrizione specifica

Tutte le risorse disponibili con l'API ARM sono nidificate all'interno di un gruppo di risorse. Procedere alla query ARM per i gruppi di risorse esistenti la sottoscrizione tramite il sotto richiesta HTTP GET. Si noti come l'ID di abbonamento viene passato come parte dell'URL questa volta.

(Sostituire YOUR_ACCESS_TOKEN e ID_SOTTOSCRIZIONE con l'effettivo Token di accesso e ID abbonamento)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La risposta viene visualizzato dipende dal modo se si dispone di gruppi di risorse definiti e in caso affermativo, il numero.

(ID abbonamento riportata di seguito è stato abbreviato per migliorare la leggibilità)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Finora è stata solo stato query API ARM per informazioni, è ora abbiamo invece creare alcune risorse e iniziare l'esercitazione con la più semplice di esse in tutti, un gruppo di risorse. La seguente richiesta HTTP crea un nuovo gruppo di risorse in un paese/nel percorso desiderato e aggiungervi uno o più tag (esempio riportato di seguito effettivamente aggiunge un tag).

(Sostituire YOUR_ACCESS_TOKEN, ID_SOTTOSCRIZIONE, RESOURCE_GROUP_NAME con l'effettivo Token di accesso, ID abbonamento e il nome del gruppo di risorse che si desidera creare)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

In caso contrario, si riceverà una risposta simile alla seguente

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

È stata creata correttamente un gruppo di risorse in Azure. Congratulazioni!

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Distribuire le risorse in un gruppo di risorse utilizzando un modello di ARM

Con ARM, è possibile distribuire le risorse utilizzando modelli ARM. Un modello di ARM definisce diverse risorse e le relative dipendenze. Per questa sezione si appena presuppone si ha familiarità con i modelli di ARM ed è sufficiente illustra come effettuare la chiamata API per iniziare la distribuzione di uno. Documentazione di ARM modelli sono disponibili qui.

Distribuzione di un modello ARM non variare molto a come si chiama altre API. Un aspetto importante viene che la distribuzione di un modello può richiedere molto tempo, a seconda di cosa si trova all'interno del modello e la chiamata API restituirà solo per l'utente come agli sviluppatori di query per stato della distribuzione per sapere quando è stata completata la distribuzione.

In questo esempio si userà un modello di ARM esposti pubblicamente disponibile in [GitHub](https://github.com/Azure/azure-quickstart-templates). Il modello che verranno utilizzare verrà distribuito un VM Linux all'area occidentale degli Stati Uniti. Anche se questo modello avrà il modello è disponibile in un repository pubblico come GitHub, è anche possibile selezionare per passare il modello completo come parte della richiesta. Si noti che sono disponibili i valori dei parametri come parte della richiesta che verrà utilizzata all'interno del modello usato.

(Sostituire ID_SOTTOSCRIZIONE, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP valori appropriati per la richiesta)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La risposta JSON abbastanza tempo per la richiesta sono stati omessi per migliorare la leggibilità di questa documentazione. La risposta conterrà informazioni sulla distribuzione basato su modelli appena creata.

