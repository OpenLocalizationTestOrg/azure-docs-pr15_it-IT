<properties
pageTitle="RSS | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Connettore RSS consente agli utenti di pubblicare e recuperare gli elementi feed. Consente inoltre agli utenti di impostare un trigger operazioni quando viene pubblicato un nuovo elemento per il feed."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>Iniziare a utilizzare il connettore RSS
RSS è un formato di syndication popolari web utilizzato per pubblicare contenuti aggiornati frequentemente, come post di blog e notizie.  Molti autori di contenuto offrono un feed RSS per consentire agli utenti per la sottoscrizione.  Utilizzare il connettore RSS per recuperare i flussi di informazioni e trigger feed quando nuovi elementi sono pubblicati in un feed RSS.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore RSS può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore RSS è le seguenti azioni e/o trigger disponibili:

### <a name="rss-actions"></a>Azioni RSS
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|È possibile ottenere RSS tutti gli elementi del feed.|
### <a name="rss-triggers"></a>Trigger RSS
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando un nuovo elemento feed pubblicato|Attiva un flusso di lavoro quando viene pubblicato un newsfeed|


## <a name="create-a-connection-to-rss"></a>Creare una connessione a RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="reference-for-rss"></a>Guida di riferimento per RSS
Si applica alla versione: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Quando un nuovo elemento feed pubblicato: genera un flusso di lavoro quando viene pubblicato un newsfeed 

```GET: /OnNewFeed``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|feedUrl|stringa|Sì|query|Nessuno|Url del feed|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|202|Accettato dall'utente|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="listfeeditems"></a>ListFeedItems
Elencare tutti RSS feed elementi.: ottenere RSS tutti gli elementi del feed. 

```GET: /ListFeedItems``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|feedUrl|stringa|Sì|query|Nessuno|Url del feed|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|202|Accettato dall'utente|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="feeditem"></a>FeedItem


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|Sì |
|titolo|stringa|Sì |
|contenuto|stringa|Sì |
|collegamenti|in forma di matrice|No |
|updatedOn|stringa|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)