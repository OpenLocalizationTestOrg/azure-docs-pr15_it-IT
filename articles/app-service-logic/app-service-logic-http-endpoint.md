<properties
   pageTitle="Logica App come endpoint chiamare"
   description="Come creare e configurare gli endpoint trigger e utilizzarli in un'app di logica nel servizio App Azure"
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


# <a name="logic-apps-as-callable-endpoints"></a>App logica come endpoint chiamare

Logica App a livello nativo può esporre un endpoint HTTP icona del come trigger.  Utilizzare il modello di chiamare endpoint per richiamare logica App come annidata flusso di lavoro tramite l'azione "flusso di lavoro" in un'App di logica.

Sono disponibili 3 tipi di trigger in grado di ricevere le richieste di:

* Richiesta
* ApiConnectionWebhook
* HttpWebhook

Per il resto dell'articolo, verrà usata **richiesta** dell'esempio, ma tutti i principi applicano agli altri tipi di 2 di trigger.

## <a name="adding-a-trigger-to-your-definition"></a>Aggiunta di un trigger per la definizione di una
Il primo passaggio consiste nell'aggiungere un trigger per la definizione di app logica in grado di ricevere le richieste in arrivo.  È possibile cercare nella finestra di progettazione per "Richiesta HTTP" aggiungere la scheda trigger. È possibile definire un corpo della richiesta JSON Schema e verrà generato token per analizzare e passare i dati da trigger manuale tramite il flusso di lavoro.  È consigliabile utilizzare uno strumento come [jsonschema.net](http://jsonschema.net) per generare uno schema JSON da un payload corpo di esempio.

![Scheda Trigger richiesta][2]

Dopo aver salvato la definizione di una logica App, un URL di callback viene generato analogo al seguente:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Questo URL è contenuta una chiave SA i parametri di query utilizzati per l'autenticazione.

È anche possibile ottenere l'endpoint nel portale di Azure:

![][1]

O chiamando:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Protezione per l'URL del trigger

Logica App callback URL vengono generati in modo sicuro utilizzando una firma di Access condiviso.  La firma passata come parametro di query e deve essere convalidata prima dell'app logica verrà generato.  Viene generato tramite una combinazione univoca di una chiave segreta per app logica, il nome di trigger e l'operazione.  A meno che non si dispone di accesso alla chiave app logica segreta, essi non sarebbe possibile generare una firma valida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Chiamare endpoint del trigger app logica

Dopo aver creato l'endpoint del trigger, è possibile attivare tramite un `POST` all'URL completo. È possibile includere intestazioni aggiuntive e tutto il contenuto nel corpo.

Se il tipo di contenuto è `application/json` saranno in grado di proprietà di riferimento all'interno della richiesta. In caso contrario, verrà considerato come un'unica binaria che può essere passata ad altre API ma non è possibile fare riferimento all'interno del flusso di lavoro senza la conversione del contenuto.  Ad esempio, se si passa `application/xml` contenuto è possibile utilizzare `@xpath()` per eseguire un'estrazione xpath, o `@json()` da convertire da XML in JSON.  Ulteriori informazioni sull'utilizzo di contenuto tipi [possono essere disponibili qui](app-service-logic-content-type.md)

Inoltre, è possibile specificare uno schema JSON nella definizione. In questo modo la finestra di progettazione generare i token che è quindi possibile passare in passaggi.  Ad esempio le operazioni seguenti consentono di rendere un `title` e `name` token disponibile nella finestra di progettazione:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Riferimento ai contenuti della richiesta in arrivo

Il `@triggerOutputs()` funzione verrà restituito il contenuto della richiesta in arrivo. Ad esempio, sarà simile:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

È possibile utilizzare il `@triggerBody()` scelta rapida per accedere alla `body` proprietà in particolare. 

## <a name="responding-to-the-request"></a>Rispondere alla richiesta

Per alcune richieste che si avvia l'app logica, si desidera rispondere con la notifica il contenuto per il chiamante. Esiste un nuovo tipo di azione denominato **risposta** che può essere utilizzato per creare il codice di stato, corpo e intestazioni per la risposta. Nota che se è presenta alcuna forma di **risposta** , l'endpoint di app logica verranno *immediatamente* rispondere con **202 accettato**.

![Azione di risposta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Le risposte sono i seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| statusCode | Codice di stato HTTP in risposta alla richiesta di posta in arrivo. Può trattarsi di un codice di stato validi che inizia con 2xx, 4xx o 5xx. codici di stato 3xx non sono consentiti. | 
| corpo | Oggetto corpo che può essere una stringa, un oggetto JSON o contenuto anche binario fare riferimento da un passaggio precedente. | 
| intestazioni | È possibile definire qualsiasi numero di intestazioni da includere nella risposta | 

Tutti i passaggi necessari per la risposta nell'app logica deve essere completata entro *60 secondi* per la richiesta originale di ricevere la risposta **a meno che non viene chiamato il flusso di lavoro come App logica annidate**. Se non si raggiunge alcuna azione risposta entro 60 secondi quindi richiesta in ingresso scadrà e ricevere una risposta **408 timeout Client** HTTP.  Per le app logica annidata, padre logica App continuerà a attendere una risposta fino al completamento, indipendentemente dalla quantità di tempo impiegato.

## <a name="advanced-endpoint-configuration"></a>Configurazione avanzata dell'endpoint

Logica App dispongono del supporto per l'endpoint di accesso diretto incorporato e Usa sempre la `POST` metodo per avviare un'esecuzione dell'app logica. L'app **Comunicare ascoltatore HTTP** API in precedenza supportata anche modificare i segmenti di URL e il metodo HTTP. È possibile anche configurare rafforzare la sicurezza o un dominio personalizzato, aggiungerlo all'host app API (l'app Web ospitato app API). 

Questa funzionalità è disponibile mediante **la gestione delle API**:
* [Modificare il metodo della richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modificare i segmenti di URL della richiesta](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurare i domini di gestione delle API della scheda **Configura** nel portale di Azure classica
* Impostare i criteri per verificare la presenza di autenticazione di base (**collegamento necessaria**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Riepilogo della migrazione dall'anteprima 01 di 12 2014

|  Anteprima 01 di 12 2014 | 2016-06-01 |
|---------------------|--------------------|
| Fare clic su app **Comunicare ascoltatore HTTP** API | Fare clic su **trigger manuale** (obbligatorio app di API) |
| Comunicare ascoltatore HTTP impostando "*Invia risposta automatica*" | Contengono un'azione di **risposta** o non nella definizione del flusso di lavoro |
| Configurare l'autenticazione di base o OAuth | tramite Gestione API |
| Configurare metodo HTTP | tramite Gestione API |
| Configurare percorso | tramite Gestione API |
| Fare riferimento il corpo in ingresso tramite`@triggerOutputs().body.Content` | Guida di riferimento tramite`@triggerOutputs().body` |
| Azione di **INVIO HTTP risposta** in comunicare ascoltatore HTTP | Fare clic su **risposta alla richiesta HTTP** (obbligatorio app di API)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
