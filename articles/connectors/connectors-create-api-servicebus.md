<properties
pageTitle="Informazioni su come usare il connettore Bus di servizio Azure nelle applicazioni logica | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Connettersi a Bus di servizio Azure per inviare e ricevere messaggi. È possibile eseguire azioni, ad esempio invia a coda inviare all'argomento, dalla coda la ricezione dalla sottoscrizione."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Iniziare a utilizzare il connettore Bus di servizio Azure

Connettersi a Bus di servizio Azure per inviare e ricevere messaggi. È possibile eseguire azioni, ad esempio invia a coda inviare all'argomento, dalla coda la ricezione dalla sottoscrizione.

Per utilizzare [un connettore](./apis-list.md), è innanzitutto necessario creare un'app di logica. È possibile iniziare creando [un'app di logica ora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Connettersi a Bus di servizio

Prima che l'app logica poter accedere a qualsiasi servizio, è innanzitutto necessario creare una connessione al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app di logica e un altro servizio.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Utilizzare un trigger Bus di servizio

Un trigger è un evento che può essere utilizzato per avviare il flusso di lavoro definita in un'app di logica. [Ulteriori informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Utilizzare un'azione Bus di servizio

Un'azione è un'operazione effettuata dal flusso di lavoro definita in un'app di logica. [Altre informazioni sulle operazioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Dettagli tecnici

Ecco i dettagli sui trigger, azioni e le risposte che supporta la connessione.

### <a name="service-bus-triggers"></a>Trigger Bus di servizio

Servizio Bus sono previsti i trigger seguenti:  

|Trigger | Descrizione|
|--- | ---|
|[Quando si riceve un messaggio in una coda](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Questa operazione attiva un flusso quando si riceve un messaggio in una coda.|
|[Quando si riceve un messaggio in una sottoscrizione argomento](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Questa operazione attiva un flusso quando si riceve un messaggio in una sottoscrizione argomento.|


### <a name="service-bus-actions"></a>Azioni Bus di servizio

Servizio Bus sono le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Invia messaggio](connectors-create-api-servicebus.md#send-message)|Questa operazione Invia un messaggio a una coda o un argomento.|
### <a name="action-and-trigger-details"></a>Informazioni dettagliate su trigger e azione

Ecco i dettagli per le azioni e trigger per il connettore insieme relative risposte.



#### <a name="send-message"></a>Invia messaggio

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|ContentData *|Contenuto|Contenuto del messaggio.|
|ContentType|Tipo di contenuto|Tipo di contenuto del contenuto del messaggio.|
|Proprietà|Proprietà|Coppie di parole chiave valore per ogni negoziate proprietà.|
|NomeEntità *|Nome coda/argomento|Nome della coda o argomento.|

Queste avanzate parametri sono inoltre disponibili:

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|ID messaggio|Id messaggio|Valore definite dall'utente Bus di servizio consente di identificare i messaggi duplicati, se abilitata.|
|A|A|Indirizzo a cui inviare a.|
|Rispondi|Rispondere a|Indirizzo della coda per rispondere.|
|ReplyToSessionId|Risposta a Id sessione|Identificatore della sessione di rispondere ai.|
|Etichetta|Etichetta|Etichetta specifici dell'applicazione.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Data e ora in formato UTC, quando il messaggio verrà aggiunto alla coda.|
|ID sessione|Id sessione|Identificatore della sessione.|
|CorrelationId|Id di correlazione|Identificatore del correlazione.|
|TimeToLive|Durata|La durata in segni di graduazione, che un messaggio è valido. La durata inizia da quando il messaggio viene inviato al servizio Bus.|



Un * indica che è necessaria una proprietà.


#### <a name="when-a-message-is-received-in-a-queue"></a>Quando si riceve un messaggio in una coda

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|Coda *|Nome coda|Nome della coda.|


Un * indica che è necessaria una proprietà.


##### <a name="output-details"></a>Dettagli output

ServiceBusMessage: Questo oggetto presenta il contenuto e le proprietà di un messaggio Bus di servizio.


| Nome della proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ContentData|stringa|Contenuto del messaggio.|
|ContentType|stringa|Tipo di contenuto del contenuto del messaggio.|
|Proprietà|oggetto|Coppie di parole chiave valore per ogni negoziate proprietà.|
|ID messaggio|stringa|Valore definite dall'utente Bus di servizio consente di identificare i messaggi duplicati, se abilitata.|
|A|stringa|Inviare all'indirizzo.|
|Rispondi|stringa|Indirizzo della coda per rispondere.|
|ReplyToSessionId|stringa|Identificatore della sessione di rispondere ai.|
|Etichetta|stringa|Etichetta specifici dell'applicazione.|
|ScheduledEnqueueTimeUtc|stringa|Data e ora in formato UTC, quando il messaggio verrà aggiunto alla coda.|
|ID sessione|stringa|Identificatore della sessione.|
|CorrelationId|stringa|Identificatore del correlazione.|
|TimeToLive|stringa|La durata in segni di graduazione, che un messaggio è valido. La durata inizia da quando il messaggio viene inviato al servizio Bus.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Quando si riceve un messaggio in una sottoscrizione argomento

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|topicName *|Nome argomento|Nome dell'argomento.|
|subscriptionName *|Nome dell'abbonamento argomento|Nome della sottoscrizione argomento.|


Un * indica che è necessaria una proprietà.


##### <a name="output-details"></a>Dettagli output

ServiceBusMessage: Questo oggetto presenta il contenuto e le proprietà di un messaggio Bus di servizio.


| Nome della proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ContentData|stringa|Contenuto del messaggio.|
|ContentType|stringa|Tipo di contenuto del contenuto del messaggio.|
|Proprietà|oggetto|Coppie di parole chiave valore per ogni negoziate proprietà.|
|ID messaggio|stringa|Valore definite dall'utente Bus di servizio consente di identificare i messaggi duplicati, se abilitata.|
|A|stringa|Inviare all'indirizzo.|
|Rispondi|stringa|Indirizzo della coda per rispondere.|
|ReplyToSessionId|stringa|Identificatore della sessione di rispondere ai.|
|Etichetta|stringa|Etichetta specifici dell'applicazione.|
|ScheduledEnqueueTimeUtc|stringa|Data e ora in formato UTC, quando il messaggio verrà aggiunto alla coda.|
|ID sessione|stringa|Identificatore della sessione.|
|CorrelationId|stringa|Identificatore del correlazione.|
|TimeToLive|stringa|La durata in segni di graduazione, che un messaggio è valido. La durata inizia da quando il messaggio viene inviato al servizio Bus.|



### <a name="http-responses"></a>Risposte HTTP

Le azioni e i trigger precedente può restituire una o più delle seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|Ok|
|202|Accettato dall'utente|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Errore sconosciuto.|
|impostazione predefinita|Operazione non è riuscita.|

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).
