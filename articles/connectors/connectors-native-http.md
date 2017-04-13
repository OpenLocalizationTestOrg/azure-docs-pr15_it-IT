<properties
    pageTitle="Aggiungere l'azione HTTP nelle App logica | Microsoft Azure"
    description="Panoramica dell'azione HTTP con il comando proprietà"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Iniziare a utilizzare l'azione HTTP

Con l'azione HTTP, è possibile estendere i flussi di lavoro per l'organizzazione e comunicare a qualsiasi endpoint su HTTP.

Si può:

- Creare una logica app i flussi di lavoro attiva (trigger) quando si blocca un sito Web in cui vengono gestiti.
- Comunicare a qualsiasi endpoint su HTTP per estendere i flussi di lavoro in altri servizi.

Per iniziare a usare l'azione HTTP in un'app di logica, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Utilizzare trigger HTTP

Un trigger è un evento che può essere utilizzato per avviare il flusso di lavoro definite in un'app di logica. [Ulteriori informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare i trigger HTTP nella finestra di progettazione di App logica.

1. Aggiungere il trigger HTTP nell'app logica.
2. Immettere i parametri per l'endpoint HTTP che si desidera sondaggio.
3. Modificare l'intervallo di ricorrenza sulla frequenza deve sondaggio.
4. L'app logica viene attivata ora con il contenuto che viene restituito durante ogni controllo.

![Trigger HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Come funziona il trigger HTTP

Trigger HTTP effettua una chiamata a un endpoint HTTP a intervalli ricorrenti. Per impostazione predefinita, le risposte HTTP codice minore di 300 risultati in un'app di logica eseguire. È possibile aggiungere una condizione nella visualizzazione codice che verrà valutata dopo la chiamata HTTP per determinare se l'app logica dovrebbe essere attivato. Ecco un esempio di un trigger HTTP che viene attivata quando il codice di stato restituito è maggiore o uguale a `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

I dettagli completi sui parametri di trigger HTTP sono disponibili sul [sito Web MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Usare l'azione HTTP

Un'azione è un'operazione che viene eseguita dal flusso di lavoro definite in un'app di logica. [Altre informazioni sulle operazioni](connectors-overview.md).

1. Selezionare il pulsante **Nuova azione** .
2. Scegliere **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **http** per elencare l'azione HTTP.

    ![Selezionare l'azione HTTP](./media/connectors-native-http/using-action-1.png)

4. Aggiungere i parametri necessari per la chiamata HTTP.

    ![Completare l'azione HTTP](./media/connectors-native-http/using-action-2.png)

5. Fare clic su nell'angolo superiore sinistro della barra degli strumenti per salvare. L'app logica salvarli e pubblicare (attiva).

## <a name="http-trigger"></a>Trigger HTTP

Ecco i dettagli del trigger che supporta il connettore. Il connettore HTTP ha un trigger.

|Trigger|Descrizione|
|---|---|
|HTTP|Effettua una chiamata HTTP e restituisce il contenuto di risposta.|

## <a name="http-action"></a>Azione HTTP

Ecco i dettagli per l'azione che supporta il connettore. Il connettore HTTP include un'opzione possibile.

|Azione|Descrizione|
|---|---|
|HTTP|Effettua una chiamata HTTP e restituisce il contenuto di risposta.|

## <a name="http-details"></a>Dettagli HTTP

Nelle tabelle seguenti vengono descrivono i campi di input obbligatori e facoltativi per l'azione e i corrispondenti dettagli output associate tramite l'azione.


#### <a name="http-request"></a>Richiesta HTTP
Di seguito sono campi di input per l'azione, semplificando così una richiesta HTTP in uscita.
Risposte * significa che si tratti di un campo obbligatorio.

|Nome visualizzato|Nome della proprietà|Descrizione|
|---|---|---|
|Metodo *|metodo|Il verbo HTTP da utilizzare|
|URI *|URI|URI per la richiesta HTTP|
|Intestazioni|intestazioni|Oggetto JSON di intestazioni HTTP da includere|
|Corpo|corpo|Nel corpo della richiesta HTTP|
|Autenticazione|autenticazione|Dettagli nella sezione [autenticazione](#authentication)|
<br>

#### <a name="output-details"></a>Dettagli output

Di seguito sono i dettagli di output per la risposta HTTP.

|Nome della proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Intestazioni|oggetto|Intestazioni di risposta|
|Corpo|oggetto|Oggetto risposta|
|Codice di stato|int|Codice di stato HTTP|

## <a name="authentication"></a>Autenticazione

La funzionalità di logica applicazioni di servizio App Azure consente di usare diversi tipi di autenticazione tramite i punti finali HTTP. È possibile utilizzare l'autenticazione con i connettori **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**e **[HTTP Webhook](./connectors-native-webhook.md)** . I tipi di autenticazione seguenti possono essere configurati:

* [Autenticazione di base](#basic-authentication)
* [Autenticazione certificato client](#client-certificate-authentication)
* [Azure Active Directory (Azure Active Directory) OAuth autenticazione](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticazione di base

L'oggetto di autenticazione seguenti è necessarie per l'autenticazione di base.
Risposte * significa che si tratti di un campo obbligatorio.

|Nome della proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Tipo *|tipo|Tipo di autenticazione (deve essere `Basic` per l'autenticazione di base)|
|Nome utente *|nome utente|Nome utente per eseguire l'autenticazione|
|Password *|password|Password per l'autenticazione|

>[AZURE.TIP] Se si desidera utilizzare una password che non può essere recuperata dalla definizione, usare un `securestring` parametro e il `@parameters()` [funzione definizione del flusso di lavoro](http://aka.ms/logicappdocs).

Creare in modo un oggetto come illustrato nel campo autenticazione:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticazione certificato client

L'oggetto di autenticazione seguenti è necessarie per l'autenticazione certificato client. Risposte * significa che si tratti di un campo obbligatorio.

|Nome della proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Tipo *|tipo|Il tipo di autenticazione (deve essere `ClientCertificate` per i certificati SSL client)|
|PFX *|PFX|Il contenuto con codifica base 64 del file di informazioni PFX (Personal Exchange)|
|Password *|password|La password per accedere al file PFX|

>[AZURE.TIP] È possibile utilizzare una `securestring` parametro e il `@parameters()` la [funzione di definizione del flusso di lavoro](http://aka.ms/logicappdocs) per utilizzare un parametro non è leggibile nella definizione del dopo aver salvato l'app logica.

Per esempio:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure Active Directory OAuth autenticazione

L'oggetto di autenticazione seguenti è necessarie per l'autenticazione di Azure Active Directory OAuth. Risposte * significa che si tratti di un campo obbligatorio.

|Nome della proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Tipo *|tipo|Il tipo di autenticazione (deve essere `ActiveDirectoryOAuth` per Azure Active Directory OAuth)|
|Tenant *|tenant|L'identificatore tenant per il tenant di Azure Active Directory|
|Gruppo di destinatari *|gruppo di destinatari|Impostare su`https://management.core.windows.net/`|
|Client ID *|clientId|L'identificatore di client per l'applicazione di Azure Active Directory|
|Segreto *|segreto|Il segreto del client che richiede il token|

>[AZURE.TIP] È possibile utilizzare una `securestring` parametro e il `@parameters()` la [funzione di definizione del flusso di lavoro](http://aka.ms/logicappdocs) per utilizzare un parametro non è leggibile nella definizione del dopo il salvataggio.

Per esempio:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto, provare a usare la piattaforma e [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare altri connettori disponibili nelle App logica esaminando il nostro [elenco API](apis-list.md).
