<properties
   pageTitle="Limiti di richiesta di gestione risorse Azure | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare la limitazione con le richieste di gestione di risorse Azure quando sono stati raggiunti i limiti di sottoscrizione."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>Limitazione delle richieste di Manager delle risorse

Per ogni abbonamento e tenant, limiti di Manager delle risorse richieste per 15.000 orarie richieste lettura e scrittura per 1.200 orarie. Se l'applicazione o script raggiunge questi limiti, è necessario limitare le richieste. In questo argomento viene illustrato come determinare le rimanenti richieste che ha raggiunto il limite e come rispondere quando è stato raggiunto il limite.

Quando si giunge al limite, si riceve il codice di stato HTTP **troppi 429 molte richieste**.

Il numero di richieste ambito è l'abbonamento o il tenant. Se si dispone di più applicazioni concorrenti effettuare richieste nel proprio abbonamento, le richieste di tali applicazioni vengono aggiunti insieme per determinare il numero di richieste rimanenti.

Le richieste di sottoscrizione nell'ambito sono quelli coinvolte passando l'id di abbonamento, ad esempio il recupero della risorsa gruppi nell'abbonamento. Le richieste di tenant nell'ambito non includono l'id di abbonamento, ad esempio il recupero dei percorsi di Azure validi.

## <a name="remaining-requests"></a>Richieste rimanenti

È possibile determinare il numero di richieste rimanente esaminando le intestazioni di risposta. Ogni richiesta include i valori per il numero di lettura rimanente e le richieste di scrittura. Nella tabella seguente sono descritte le intestazioni di risposta per che è possibile esaminare quei valori:

| Intestazione di risposta | Descrizione |
| --------------- | ----------- |
| x-ms-ratelimit-Remaining-Subscription-Reads | Abbonamento nell'ambito legge rimanente |
| x-ms-ratelimit-Remaining-Subscription-Writes | Abbonamento nell'ambito scrive rimanente |
| x-ms-ratelimit-Remaining-tenant-Reads | Tenant nell'ambito legge rimanente |
| x-ms-ratelimit-Remaining-tenant-Writes | Tenant nell'ambito scrive rimanente |
| x-ms-ratelimit-Remaining-Subscription-Resource-Requests | Abbonamento nell'ambito le richieste di tipo risorse rimanenti.<br /><br />Se un servizio è sostituita il limite predefinito, viene restituito solo il valore dell'intestazione. Manager delle risorse aggiunge questo valore invece che tramite l'abbonamento legge o scrive. |
| x-ms-ratelimit-Remaining-Subscription-Resource-Entities-Read | Abbonamento nell'ambito richieste raccolta tipo risorsa rimanente.<br /><br />Se un servizio è sostituita il limite predefinito, viene restituito solo il valore dell'intestazione. Questo valore fornisce il numero di richieste di tipo raccolta rimanenti (risorse elenco). |
| x-ms-ratelimit-Remaining-tenant-Resource-Requests | Tenant nell'ambito richieste di tipo rimanente della risorsa.<br /><br />Questa intestazione viene aggiunto solo per le richieste a livello di tenant e solo se un servizio ha eseguito l'override il limite predefinito. Manager delle risorse aggiunge il valore anziché nel tenant legge o scrive. |
| x-ms-ratelimit-Remaining-tenant-Resource-Entities-Read | Altre risorse tipo raccolta richieste l'ambito tenant.<br /><br />Questa intestazione viene aggiunto solo per le richieste a livello di tenant e solo se un servizio ha eseguito l'override il limite predefinito. |

## <a name="retrieving-the-header-values"></a>Recuperare i valori di intestazione

Recupero di questi valori di intestazione nel codice o script è alcuna differenza tra il recupero di un valore qualsiasi intestazione. 

Ad esempio, in **c#**, si recupera il valore dell'intestazione da un oggetto di **HttpWebResponse** denominato **risposta** con il codice seguente:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

**PowerShell**per recuperare il valore dell'intestazione da un'operazione di WebRequest richiama.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
In alternativa, se per visualizzare le rimanenti richieste per il debug, è possibile fornire la **-Debug** parametro sui cmdlet di **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Che restituisce moltissime informazioni, incluso il valore di risposta seguenti:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

In **CLI Azure**, si recupera il valore dell'intestazione tramite l'opzione più dettagliato.

    azure group list -vv --json

Che restituisce moltissime informazioni, tra cui l'oggetto seguente:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>In attesa prima dell'invio richiesta successiva

Quando si giunge al limite di richiesta, Gestione risorse restituisce il codice di stato **429** HTTP e **Riprova dopo** valore nell'intestazione. La **Riprova dopo** valore specifica il numero di secondi di attesa dell'applicazione (o sospensione) prima di inviare la richiesta successiva. Se si invia una richiesta prima che sia trascorso il valore di tentativi, la richiesta non viene elaborata e viene restituito un nuovo valore Riprova.
