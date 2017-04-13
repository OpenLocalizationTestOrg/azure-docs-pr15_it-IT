<properties
 pageTitle="Utilità di pianificazione autenticazione in uscita"
 description="Utilità di pianificazione autenticazione in uscita"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Utilità di pianificazione autenticazione in uscita

Processi di pianificazione potrebbero essere necessario effettua una chiamata a servizi che richiedono l'autenticazione. In questo modo, un servizio chiamato possibile determinare se il processo di pianificazione può accedere alle risorse. Alcuni di questi servizi includono altri servizi di Azure, Salesforce.com, Facebook e siti Web personalizzati protetti.

## <a name="adding-and-removing-authentication"></a>Aggiunta e rimozione di autenticazione

Aggiunta di autenticazione a un processo di pianificazione è semplice: aggiungere un elemento figlio JSON `authentication` per il `request` elemento durante la creazione o aggiornamento di un processo. Informazioni riservate passato al servizio Utilità di una richiesta di caricamento, PATCH o POST-come parte del `authentication` oggetto: mai restituito nelle risposte. Nelle risposte, informazioni segrete sono impostate su null o abbia un token pubblica che rappresenta l'entità autenticato.

Per rimuovere l'autenticazione, spostare o PATCH in modo esplicito, il processo di impostazione di `authentication` oggetto su null. Non si vedranno tutte le proprietà di autenticazione indietro nella risposta.

Non sono attualmente tipi di autenticazione supportati solo il `ClientCertificate` modello (per l'uso di certificati del client SSL/TLS), il `Basic` modello (per l'autenticazione di base) e il `ActiveDirectoryOAuth` modello (per l'autenticazione di Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corpo richiesta per l'autenticazione ClientCertificate

Quando si aggiungono autenticazione tramite il `ClientCertificate` del modello, specificare gli elementi aggiuntivi seguenti nel corpo della richiesta.  

|Elemento|Descrizione|
|:---|:---|
|_autenticazione (elemento padre)_|Oggetto di autenticazione per l'uso di un certificato client SSL.|
|_tipo_|Obbligatorio. Tipo di autenticazione. Per i certificati SSL client il valore deve essere `ClientCertificate`.|
|_PFX_|Obbligatorio. Codifica base 64 contenuto del file PFX.|
|_password_|Obbligatorio. Password per accedere al file PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Risposta per l'autenticazione ClientCertificate

Quando viene inviata una richiesta di informazioni di autenticazione, la risposta contiene gli elementi seguenti relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_autenticazione (elemento padre)_ |Oggetto di autenticazione per l'uso di un certificato client SSL.|
|_tipo_ |Tipo di autenticazione. Per i certificati SSL client il valore è `ClientCertificate`.|
|_certificateThumbprint_ |Identificazione personale del certificato.|
|_certificateSubjectName_ |Nome distinto soggetto del certificato.|
|_certificateExpiration_ |Data di scadenza del certificato.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Richiesta di resto di esempio per l'autenticazione ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Risposta resto di esempio per l'autenticazione ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Corpo della richiesta per l'autenticazione di base

Quando si aggiungono autenticazione tramite il `Basic` del modello, specificare gli elementi aggiuntivi seguenti nel corpo della richiesta.

|Elemento|Descrizione|
|:--|:--|
|_autenticazione (elemento padre)_ |Oggetto l'autenticazione mediante l'autenticazione di base.|
|_tipo_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione di base, il valore deve essere `Basic`.|
|_nome utente_ |Obbligatorio. Nome utente per eseguire l'autenticazione.|
|_password_ |Obbligatorio. Password per l'autenticazione.|

## <a name="response-body-for-basic-authentication"></a>Risposta per l'autenticazione di base

Quando viene inviata una richiesta di informazioni di autenticazione, la risposta contiene gli elementi seguenti relativi all'autenticazione.

|Elemento|Descrizione|
|:--|:--|
|_autenticazione (elemento padre)_ |Oggetto l'autenticazione mediante l'autenticazione di base.|
|_tipo_ |Tipo di autenticazione. Per l'autenticazione di base, il valore è `Basic`.|
|_nome utente_ |Il nome utente autenticato.|

## <a name="sample-rest-request-for-basic-authentication"></a>Richiesta di resto di esempio per l'autenticazione di base

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Risposta resto di esempio per l'autenticazione di base

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corpo richiesta per l'autenticazione ActiveDirectoryOAuth

Quando si aggiungono autenticazione tramite il `ActiveDirectoryOAuth` del modello, specificare gli elementi aggiuntivi seguenti nel corpo della richiesta.

|Elemento |Descrizione |
|:--|:--|
|_autenticazione (elemento padre)_ |Oggetto l'autenticazione mediante l'autenticazione di ActiveDirectoryOAuth.|
|_tipo_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore deve essere `ActiveDirectoryOAuth`.|
|_tenant_ |Obbligatorio. Identificatore tenant per il tenant di Azure Active Directory.|
|_gruppo di destinatari_ |Obbligatorio. Questo è impostato su https://management.core.windows.net/.|
|_clientId_ |Obbligatorio. Fornire l'identificatore di client per l'applicazione di Azure Active Directory.|
|_segreto_ |Obbligatorio. Segreto del client che richiede il token.|

### <a name="determining-your-tenant-identifier"></a>Determinare l'identificatore Tenant

È possibile trovare l'identificatore tenant per il tenant di Azure Active Directory eseguendo `Get-AzureAccount` in Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Risposta per l'autenticazione ActiveDirectoryOAuth

Quando viene inviata una richiesta di informazioni di autenticazione, la risposta contiene gli elementi seguenti relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_autenticazione (elemento padre)_ |Oggetto l'autenticazione mediante l'autenticazione di ActiveDirectoryOAuth.|
|_tipo_ |Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`.|
|_tenant_ |Identificatore tenant per il tenant di Azure Active Directory. |
|_gruppo di destinatari_ |Questo è impostato su https://management.core.windows.net/.|
|_clientId_ |Identificatore di client per l'applicazione di Azure Active Directory.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Richiesta di resto di esempio per l'autenticazione ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Risposta resto di esempio per l'autenticazione ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Vedere anche


 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Azure concetti di utilità di pianificazione, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)
