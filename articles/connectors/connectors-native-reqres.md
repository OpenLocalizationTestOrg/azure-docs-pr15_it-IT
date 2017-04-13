<properties
    pageTitle="Usare azioni richiesta e risposta | Microsoft Azure"
    description="Panoramica dei trigger richiesta e risposta e azione in un'app di Azure logica"
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Guida introduttiva a componenti richiesta e risposta

Con i componenti di richiesta e risposta in un'app di logica, è possibile rispondere agli eventi in tempo reale.

Ad esempio, è possibile:

- Rispondere a una richiesta HTTP con i dati da un database locale tramite un'app di logica.
- Attivare un'app di logica di un evento webhook esterni.
- Chiamare una app logica con un'azione richiesta e risposta all'interno di un'altra app logica.

Per iniziare a usare le azioni richiesta e risposta in un'app di logica, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utilizzare trigger richiesta HTTP

Un trigger è un evento che può essere utilizzato per avviare il flusso di lavoro definite in un'app di logica. [Ulteriori informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare una richiesta HTTP nella finestra di progettazione di App logica.

1. Aggiungere il trigger **richiesta - richiesta HTTP un quando si riceve** nell'app logica. È facoltativamente possibile specificare uno schema JSON (utilizzando uno strumento come [JSONSchema.net](http://jsonschema.net)) per il corpo della richiesta. In questo modo la finestra di progettazione generare i token per le proprietà nella richiesta HTTP.
2. Aggiungere un'altra azione, in modo che è possibile salvare l'app logica.
3. Dopo aver salvato l'app logica, è possibile ottenere l'URL della richiesta HTTP dalla scheda richiesta.
4. Un POST HTTP (è possibile utilizzare uno strumento come [Postman](https://www.getpostman.com/)) all'URL attiva l'app logica.

>[AZURE.NOTE] Se non è possibile definire un'azione di risposta, un `202 ACCEPTED` viene restituita immediatamente al chiamante. È possibile utilizzare l'azione di risposta per personalizzare una risposta.

![Trigger di risposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Usare l'azione di risposta HTTP

L'azione di risposta HTTP è valida solo quando si utilizza in un flusso di lavoro viene attivata da una richiesta HTTP. Se non è possibile definire un'azione di risposta, un `202 ACCEPTED` viene restituita immediatamente al chiamante.  È possibile aggiungere un'azione di risposta in un passaggio all'interno del flusso di lavoro. L'app logica mantiene solo aperta la richiesta in arrivo per un minuto di una risposta.  Dopo un minuto, se è stata inviata alcuna risposta dal flusso di lavoro (ed esiste un'azione di risposta nella definizione del), un `504 GATEWAY TIMEOUT` restituita al chiamante.

Ecco come aggiungere un'azione di risposta HTTP:

1. Selezionare il pulsante **Nuova azione** .
2. Scegliere **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **risposta** per l'azione di risposta dell'elenco.

    ![Selezionare l'azione di risposta](./media/connectors-native-reqres/using-action-1.png)

4. Aggiungere i parametri necessari per il messaggio di risposta HTTP.

    ![Completare l'operazione di risposta](./media/connectors-native-reqres/using-action-2.png)

5. Fare clic su nell'angolo superiore sinistro della barra degli strumenti per salvare e l'app logica salvarli e pubblicare (attiva).

## <a name="request-trigger"></a>Richiesta di trigger

Ecco i dettagli del trigger che supporta il connettore. Esiste un trigger singola richiesta.

|Trigger|Descrizione|
|---|---|
|Richiesta|Si verifica quando si riceve una richiesta HTTP|

## <a name="response-action"></a>Azione di risposta

Ecco i dettagli per l'azione che supporta il connettore. C'è un'azione singola risposta che può essere utilizzata solo quando è accompagnato da un trigger di richiesta.

|Azione|Descrizione|
|---|---|
|Risposta|Restituisce una risposta alla richiesta HTTP correlata|

### <a name="trigger-and-action-details"></a>Informazioni dettagliate su trigger e azione

Nelle tabelle seguenti vengono descrivono i campi di input per i trigger e azione e il corrispondente output dettagli.

#### <a name="request-trigger"></a>Richiesta di trigger
Di seguito è un campo di input per i trigger da una richiesta HTTP in arrivo.

|Nome visualizzato|Nome della proprietà|Descrizione|
|---|---|---|
|Schema JSON|schema|Lo schema JSON del corpo della richiesta HTTP|
<br>

**Dettagli output**

Di seguito sono i dettagli di output per la richiesta.

|Nome della proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Intestazioni|oggetto|Le intestazioni di richiesta|
|Corpo|oggetto|Richiedere l'oggetto|

#### <a name="response-action"></a>Azione di risposta

Di seguito sono campi di input per l'azione di risposta HTTP. Risposte * significa che si tratti di un campo obbligatorio.

|Nome visualizzato|Nome della proprietà|Descrizione|
|---|---|---|
|Codice di stato *|statusCode|Il codice di stato HTTP|
|Intestazioni|intestazioni|Oggetto JSON le intestazioni di risposta da includere|
|Corpo|corpo|Corpo della risposta|

## <a name="next-steps"></a>Passaggi successivi

A questo punto, provare a usare la piattaforma e [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare altri connettori disponibili nelle App logica esaminando il nostro [elenco API](apis-list.md).
