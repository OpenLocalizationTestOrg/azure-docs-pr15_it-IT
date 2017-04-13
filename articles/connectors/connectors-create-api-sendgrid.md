<properties
pageTitle="SendGrid | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Provider di connessione SendGrid consente di inviare posta elettronica e gestire gli elenchi di destinatari."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Iniziare a utilizzare il connettore SendGrid

Provider di connessione SendGrid consente di inviare posta elettronica e gestire gli elenchi di destinatari.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore SendGrid può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore SendGrid è le seguenti azioni disponibili. Non esistono alcun trigger.

### <a name="sendgrid-actions"></a>Azioni SendGrid
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Consente di inviare un messaggio di posta elettronica utilizzando l'API SendGrid (limitato a 10.000 destinatari)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Aggiungere un singolo destinatario a un elenco di destinatari|


## <a name="create-a-connection-to-sendgrid"></a>Creare una connessione a SendGrid
Per creare App logica con SendGrid, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|ApiKey|Sì|Specificare la chiave dell'Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

Dopo aver creato la connessione, è possibile utilizzare per eseguire le azioni e attendere trigger descritto in questo articolo.

## <a name="reference-for-sendgrid"></a>Guida di riferimento per SendGrid
Si applica alla versione: 1.0

## <a name="sendemail"></a>SendEmail
Inviare posta elettronica: invia un messaggio di posta elettronica utilizzando l'API SendGrid (limitato a 10.000 destinatari) 

```POST: /api/mail.send.json``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|richiesta| |Sì|corpo|Nessuno|Messaggio di posta elettronica da inviare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|429|Richiesta di un numero eccessivo|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Aggiungere un destinatario all'elenco: aggiungere un singolo destinatario a un elenco di destinatari 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|listId|stringa|Sì|percorso|Nessuno|Id univoco dell'elenco di destinatari|
|recipientId|stringa|Sì|percorso|Nessuno|Id univoco del destinatario|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti 

### <a name="emailrequest"></a>EmailRequest


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Da|stringa|Sì |
|FromName|stringa|No |
|A|stringa|Sì |
|toname|stringa|No |
|Oggetto|stringa|Sì |
|corpo|stringa|Sì |
|ishtml|valore booleano|No |
|cc|stringa|No |
|ccname|stringa|No |
|destinatari in Ccn|stringa|No |
|bccname|stringa|No |
|Rispondi|stringa|No |
|Data|stringa|No |
|intestazioni|stringa|No |
|file|in forma di matrice|No |
|nomi di file|in forma di matrice|No |



### <a name="emailresponse"></a>EmailResponse


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Messaggio|stringa|No |



### <a name="recipientlists"></a>RecipientLists


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|elenchi|in forma di matrice|No |



### <a name="recipientlist"></a>RecipientList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|nome|stringa|No |
|recipient_count|numero intero|No |



### <a name="recipients"></a>Destinatari


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|destinatari|in forma di matrice|No |



### <a name="recipient"></a>Destinatario


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Posta elettronica|stringa|No |
|Cognome|stringa|No |
|nome|stringa|No |
|ID|stringa|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)