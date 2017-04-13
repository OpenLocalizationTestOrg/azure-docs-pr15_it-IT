<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Connettore Outlook.com consente di gestire la posta elettronica, calendari e contatti. È possibile eseguire diverse operazioni, ad esempio Invia messaggio, pianificare riunioni, aggiungere contatti e così via."
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

# <a name="get-started-with-the-outlookcom-connector"></a>Iniziare a utilizzare il connettore Outlook.com

Connettore Outlook.com consente di gestire la posta elettronica, calendari e contatti. È possibile eseguire diverse operazioni, ad esempio Invia messaggio, pianificare riunioni, aggiungere contatti e così via.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore Outlook.com può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore Outlook.com è le seguenti azioni e/o trigger disponibili:

### <a name="outlookcom-actions"></a>Azioni di Outlook.com
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera i messaggi di posta elettronica da una cartella|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Consente di inviare un messaggio di posta elettronica|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Consente di eliminare un messaggio di posta elettronica facendo id|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Contrassegna un messaggio di posta elettronica come letti|
|[Rispondi](connectors-create-api-outlook.md#ReplyTo)|Rispondere al messaggio di posta elettronica|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Allegato di posta elettronica recupera per id|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Inviare un messaggio di posta elettronica con più opzioni e attendere che il destinatario rispondere con una delle opzioni|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Inviare un messaggio di posta elettronica di approvazione e attendere una risposta dal destinatario|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera i calendari|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera elementi da un calendario|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crea un nuovo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera un elemento specifico da un calendario|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Elimina un elemento del calendario|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Parzialmente aggiornato un elemento del calendario|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera cartelle di contatti|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera i contatti da una cartella di contatti|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crea un nuovo contatto|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera un contatto specifico da una cartella di contatti|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Elimina un contatto|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Parzialmente aggiorna un contatto|
### <a name="outlookcom-triggers"></a>Trigger di Outlook.com
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Sull'evento avvio breve|Attiva un flusso all'avvio di un evento del calendario imminenti|
|Nel nuovo messaggio di posta elettronica|Attiva un flusso quando arriva un nuovo messaggio|
|Nuovi elementi|Attivato quando viene creato un nuovo elemento del calendario|
|In elementi aggiornati|Attivato quando viene modificato un elemento del calendario|


## <a name="create-a-connection-to-outlookcom"></a>Creare una connessione a Outlook.com
Per creare App logica con Outlook.com, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali di Outlook.com|
Dopo aver creato la connessione, è possibile utilizzare per eseguire le azioni e attendere trigger descritto in questo articolo.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.  

## <a name="reference-for-outlookcom"></a>Guida di riferimento per Outlook.com
Si applica alla versione: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Sull'evento avvio breve: genera un flusso all'avvio di un evento del calendario imminenti 

```GET: /Events/OnUpcomingEvents``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|query|Nessuno|Identificatore univoco del calendario|
|lookAheadTimeInMinutes|numero intero|No|query|15|Ora (in minuti) per visualizzare in anticipo gli eventi imminenti|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|202|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getemails"></a>GetEmails
Ottenere messaggi di posta elettronica: recupera i messaggi di posta elettronica da una cartella 

```GET: /Mail``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|stringa|No|query|Posta in arrivo|Percorso della cartella per recuperare i messaggi di posta elettronica (impostazione predefinita: "Posta in arrivo")|
|In alto|numero intero|No|query|10|Numero di messaggi di posta elettronica per recuperare (impostazione predefinita: 10)|
|fetchOnlyUnread|valore booleano|No|query|vero|Recuperare solo i messaggi non letti?|
|includeAttachments|valore booleano|No|query|FALSO|Se è impostato su true, allegati anche da recuperare insieme messaggio di posta elettronica|
|searchQuery|stringa|No|query|Nessuno|Query di ricerca per filtrare i messaggi di posta elettronica|
|Ignora|numero intero|No|query|0|Numero di messaggi di posta elettronica per ignorare (impostazione predefinita: 0)|
|skipToken|stringa|No|query|Nessuno|Passare token a recupero nuova pagina|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="sendemail"></a>SendEmail
Inviare posta elettronica: invia un messaggio di posta elettronica 

```POST: /Mail``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|emailMessage| |Sì|corpo|Nessuno|Posta elettronica|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="deleteemail"></a>DeleteEmail
Eliminare la posta elettronica: consente di eliminare un messaggio di posta elettronica facendo id 

```DELETE: /Mail/{messageId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID messaggio|stringa|Sì|percorso|Nessuno|ID del messaggio di posta elettronica da eliminare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="markasread"></a>MarkAsRead
Segna come già letto: contrassegna un messaggio di posta elettronica come letti 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID messaggio|stringa|Sì|percorso|Nessuno|ID della posta elettronica a essere contrassegnati come letti|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="replyto"></a>Rispondi
Rispondi al messaggio di posta elettronica: rispondere al messaggio di posta elettronica 

```POST: /Mail/ReplyTo/{messageId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID messaggio|stringa|Sì|percorso|Nessuno|ID della posta elettronica a rispondere a|
|commento|stringa|Sì|query|Nessuno|Commento di risposta|
|Rispondi a tutti|valore booleano|No|query|FALSO|Rispondere a tutti i destinatari|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getattachment"></a>GetAttachment
Ottenere allegato: allegato di posta elettronica recupera per id 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID messaggio|stringa|Sì|percorso|Nessuno|ID del messaggio di posta elettronica|
|attachmentId|stringa|Sì|percorso|Nessuno|ID dell'allegato per il download|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="onnewemail"></a>OnNewEmail
Nel nuovo messaggio di posta elettronica: attiva un flusso quando arriva un nuovo messaggio 

```GET: /Mail/OnNewEmail``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|stringa|No|query|Posta in arrivo|Cartella di posta elettronica da recuperare (impostazione predefinita: posta in arrivo)|
|A|stringa|No|query|Nessuno|Indirizzi di posta elettronica del destinatario|
|Da|stringa|No|query|Nessuno|Da indirizzo|
|importanza|stringa|No|query|Visualizzazione normale|Priorità del messaggio di posta elettronica (ad alta, normale, bassa) (impostazione predefinita: normale)|
|fetchOnlyWithAttachment|valore booleano|No|query|FALSO|Recuperare solo i messaggi di posta elettronica con un allegato|
|includeAttachments|valore booleano|No|query|FALSO|Includere allegati|
|subjectFilter|stringa|No|query|Nessuno|Stringa da ricercare nell'oggetto|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|202|Accettato dall'utente|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Inviare posta elettronica con le opzioni: inviare un messaggio di posta elettronica con più opzioni e attendere che il destinatario rispondere con una delle opzioni 

```POST: /mailwithoptions/$subscriptions``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Sì|corpo|Nessuno|Richiesta di sottoscrizione per la posta elettronica di opzioni|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|201|Sottoscrizione creata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Inviare posta elettronica di approvazione: inviare un messaggio di posta elettronica di approvazione e attendere una risposta dal destinatario 

```POST: /approvalmail/$subscriptions``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Sì|corpo|Nessuno|Richiesta di sottoscrizione per la posta elettronica di approvazione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|201|Sottoscrizione creata|
|400|BadRequest|
|401|Non autorizzato|
|403|Accesso negato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendargettables"></a>CalendarGetTables
Ottenere i calendari: recupera i calendari 

```GET: /datasets/calendars/tables``` 

Non sono presenti parametri per la chiamata
#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendargetitems"></a>CalendarGetItems
Ottenere eventi: Recupera elementi da un calendario 

```GET: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco del calendario per recuperare|
|$filter|stringa|No|query|Nessuno|Una query di filtro ODATA per limitare il numero di voci|
|$orderby|stringa|No|query|Nessuno|Una query di orderBy ODATA per specificare l'ordine delle voci|
|$skip|numero intero|No|query|Nessuno|Numero di voci per ignorare (impostazione predefinita = 0)|
|$top|numero intero|No|query|Nessuno|Numero massimo di voci per recuperare (impostazione predefinita = 256)|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendarpostitem"></a>CalendarPostItem
Creazione di evento: crea un nuovo evento 

```POST: /datasets/calendars/tables/{table}/items``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|elemento| |Sì|corpo|Nessuno|Elemento del calendario da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendargetitem"></a>CalendarGetItem
Ottenere evento: recupera un elemento specifico da un calendario 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco di un elemento del calendario per recuperare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Eliminare l'evento: elimina un elemento del calendario 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco dell'elemento del calendario da eliminare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Evento di aggiornamento: parzialmente aggiornato un elemento del calendario 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco dell'elemento di calendario da aggiornare|
|elemento| |Sì|corpo|Nessuno|Elemento di calendario da aggiornare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Nuovi elementi: attivato quando viene creato un nuovo elemento del calendario 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|$filter|stringa|No|query|Nessuno|Una query di filtro ODATA per limitare il numero di voci|
|$orderby|stringa|No|query|Nessuno|Una query di orderBy ODATA per specificare l'ordine delle voci|
|$skip|numero intero|No|query|Nessuno|Numero di voci per ignorare (impostazione predefinita = 0)|
|$top|numero intero|No|query|Nessuno|Numero massimo di voci per recuperare (impostazione predefinita = 256)|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
In articoli aggiornati nel documento: attivata quando si modifica un elemento del calendario 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di un calendario|
|$filter|stringa|No|query|Nessuno|Una query di filtro ODATA per limitare il numero di voci|
|$orderby|stringa|No|query|Nessuno|Una query di orderBy ODATA per specificare l'ordine delle voci|
|$skip|numero intero|No|query|Nessuno|Numero di voci per ignorare (impostazione predefinita = 0)|
|$top|numero intero|No|query|Nessuno|Numero massimo di voci per recuperare (impostazione predefinita = 256)|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactgettables"></a>ContactGetTables
Visualizzare cartelle di contatti: recupera cartelle dei contatti 

```GET: /datasets/contacts/tables``` 

Non sono presenti parametri per la chiamata
#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactgetitems"></a>ContactGetItems
Recuperare i contatti: recupera i contatti da una cartella di contatti 

```GET: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco della cartella di contatti per recuperare|
|$filter|stringa|No|query|Nessuno|Una query di filtro ODATA per limitare il numero di voci|
|$orderby|stringa|No|query|Nessuno|Una query di orderBy ODATA per specificare l'ordine delle voci|
|$skip|numero intero|No|query|Nessuno|Numero di voci per ignorare (impostazione predefinita = 0)|
|$top|numero intero|No|query|Nessuno|Numero massimo di voci per recuperare (impostazione predefinita = 256)|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactpostitem"></a>ContactPostItem
Crea contatto: crea un nuovo contatto 

```POST: /datasets/contacts/tables/{table}/items``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di una cartella di contatti|
|elemento| |Sì|corpo|Nessuno|Contatto per creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactgetitem"></a>ContactGetItem
Ottenere contatti: recupera un contatto specifico da una cartella di contatti 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di una cartella di contatti|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco di un contatto da recuperare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Elimina contatto: elimina un contatto 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di una cartella di contatti|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco del contatto da eliminare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="contactpatchitem"></a>ContactPatchItem
Aggiorna contatto: parzialmente aggiorna un contatto 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tavolo|stringa|Sì|percorso|Nessuno|Identificatore univoco di una cartella di contatti|
|ID|stringa|Sì|percorso|Nessuno|Identificatore univoco del contatto per l'aggiornamento|
|elemento| |Sì|corpo|Nessuno|Contatto per l'aggiornamento|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [stringa, oggetto]]


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="object"></a>Oggetto


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Allegati|in forma di matrice|No |
|Da|stringa|No |
|Cc|stringa|No |
|Destinatari in Ccn|stringa|No |
|Oggetto|stringa|Sì |
|Corpo|stringa|Sì |
|Importanza|stringa|No |
|IsHtml|valore booleano|No |
|A|stringa|Sì |



### <a name="sendattachment"></a>SendAttachment


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|@odata.type|stringa|No |
|Nome|stringa|Sì |
|ContentBytes|stringa|Sì |



### <a name="receivemessage"></a>ReceiveMessage


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|No |
|IsRead|valore booleano|No |
|HasAttachment|valore booleano|No |
|DateTimeReceived|stringa|No |
|Allegati|in forma di matrice|No |
|Da|stringa|No |
|Cc|stringa|No |
|Destinatari in Ccn|stringa|No |
|Oggetto|stringa|Sì |
|Corpo|stringa|Sì |
|Importanza|stringa|No |
|IsHtml|valore booleano|No |
|A|stringa|Sì |



### <a name="receiveattachment"></a>ReceiveAttachment


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|Sì |
|ContentType|stringa|Sì |
|@odata.type|stringa|No |
|Nome|stringa|Sì |
|ContentBytes|stringa|Sì |



### <a name="digestmessage"></a>DigestMessage


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Corpo|stringa|No |
|Importanza|stringa|No |
|Classificazione|in forma di matrice|Sì |
|Allegati|in forma di matrice|No |
|A|stringa|Sì |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabulare|non è definito|No |
|BLOB|non è definito|No |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|origine|stringa|No |
|displayName|stringa|No |
|codificando gli|stringa|No |
|tableDisplayName|stringa|No |
|tablePluralName|stringa|No |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|origine|stringa|No |
|displayName|stringa|No |
|codificando gli|stringa|No |



### <a name="tablemetadata"></a>TableMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|nome|stringa|No |
|titolo|stringa|No |
|autorizzazione di ms x|stringa|No |
|funzionalità di ms x|non è definito|No |
|schema|non è definito|No |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sortRestrictions|non è definito|No |
|filterRestrictions|non è definito|No |
|filterFunctions|in forma di matrice|No |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ordinabile|valore booleano|No |
|unsortableProperties|in forma di matrice|No |
|ascendingOnlyProperties|in forma di matrice|No |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|possibile filtrare|valore booleano|No |
|nonFilterableProperties|in forma di matrice|No |
|requiredProperties|in forma di matrice|No |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|NotificationUrl|stringa|No |
|Messaggio|non è definito|No |



### <a name="messagewithoptions"></a>MessageWithOptions


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Opzioni|stringa|Sì |
|Corpo|stringa|No |
|Importanza|stringa|No |
|Allegati|in forma di matrice|No |
|A|stringa|Sì |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|No |
|risorsa|stringa|No |
|notificationType|stringa|No |
|notificationUrl|stringa|No |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|NotificationUrl|stringa|No |
|Messaggio|non è definito|No |



### <a name="approvalmessage"></a>ApprovalMessage


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Opzioni|stringa|Sì |
|Corpo|stringa|No |
|Importanza|stringa|No |
|Allegati|in forma di matrice|No |
|A|stringa|Sì |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|SelectedOption|stringa|No |



### <a name="tableslist"></a>TablesList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="table"></a>tavolo


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|stringa|No |
|DisplayName|stringa|No |



### <a name="item"></a>Elemento


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|stringa|No |



### <a name="calendaritemslist"></a>CalendarItemsList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="calendaritem"></a>CalendarItem


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|stringa|No |



### <a name="contactitemslist"></a>ContactItemsList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="contactitem"></a>ContactItem


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|stringa|No |



### <a name="datasetslist"></a>DataSetsList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="dataset"></a>Set di dati


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|stringa|No |
|DisplayName|stringa|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)