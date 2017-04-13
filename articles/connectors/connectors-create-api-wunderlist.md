<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Wunderlist offrono un responsabile di elenco e attività todo per aiutare gli utenti loro comodo.  Se si condivide una lista della spesa con una persona cara, su un progetto, o si pianifica una vacanza Wunderlist semplifica acquisire, condividere e completare il to¬dos. Wunderlist Sincronizza istantaneamente tra il computer, tablet e telefono, è possibile accedere a tutte le attività da qualsiasi posizione."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Iniziare a utilizzare il connettore Wunderlist

Wunderlist offrono un responsabile di elenco e attività todo per aiutare gli utenti loro comodo.  Se si condivide una lista della spesa con una persona cara, su un progetto, o si pianifica una vacanza Wunderlist semplifica acquisire, condividere e completare il to¬dos. Wunderlist Sincronizza istantaneamente tra il computer, tablet e telefono, è possibile accedere a tutte le attività da qualsiasi posizione.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore Wunderlist può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore Wunderlist è le seguenti azioni e/o trigger disponibili:

### <a name="wunderlist-actions"></a>Azioni Wunderlist
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recuperare gli elenchi associati all'account.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Creare un elenco.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recuperare le attività da un elenco specifico.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Creare un'attività|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recuperare le sottoattività da un elenco specifico o da un'attività specifica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Creare una sottoattività all'interno di un'attività specifica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recuperare le note per uno specifico elenco o di un'attività specifica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Aggiungere una nota a un'attività specifica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recuperare i commenti delle attività per un elenco specifico o un'attività specifica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Aggiungere un commento a un'attività specifica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recuperare i promemoria per un elenco specifico o un'attività specifica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Impostare un promemoria.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recuperare i file per un elenco specifico o un'attività specifica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera un elenco specifico|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Elimina un elenco|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Aggiornare un elenco specifico|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera un'attività specifica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Aggiorna una specifica attività|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Elimina una specifica attività|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera una sottoattività specifica|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Aggiorna una sottoattività specifica|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Elimina una sottoattività specifica|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recuperare una nota specifica|
|[UpdateNota](connectors-create-api-wunderlist.md#updatenote)|Aggiornare una nota specifica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Eliminare una nota specifica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recuperare un commento specifico|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Aggiornare un promemoria specifico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Eliminare un promemoria specifico|
### <a name="wunderlist-triggers"></a>Trigger Wunderlist
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|In un'attività è dovuta|Attiva un nuovo flusso alla scadenza di un'attività nell'elenco|
|Quando si crea una nuova attività|Attiva un nuovo flusso di quando si crea una nuova attività nell'elenco|
|Quando si verifica un promemoria|Attiva un nuovo flusso di quando si verifica un promemoria|


## <a name="create-a-connection-to-wunderlist"></a>Creare una connessione a Wunderlist
Per creare App logica con Wunderlist, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali Wunderlist|
Dopo aver creato la connessione, è possibile utilizzare per eseguire le azioni e attendere trigger descritto in questo articolo. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="reference-for-wunderlist"></a>Guida di riferimento per Wunderlist
Si applica alla versione: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
In un'attività è dovuta: avvia un nuovo flusso alla scadenza di un'attività nell'elenco 

```GET: /trigger/tasksdue``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|


## <a name="triggertasknew"></a>TriggerTaskNew
Quando si crea una nuova attività: avvia un nuovo flusso di creazione di una nuova attività nell'elenco 

```GET: /trigger/tasksnew``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|


## <a name="triggerreminder"></a>TriggerReminder
Quando si verifica un promemoria: avvia un nuovo flusso di quando si verifica un promemoria 

```GET: /trigger/reminders``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|


## <a name="retrievelists"></a>RetrieveLists
Ottenere elenchi: recuperare gli elenchi associati all'account. 

```GET: /lists``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createlist"></a>CreateList
Creare un elenco: creare un elenco. 

```POST: /lists``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Nuovo elenco da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="listtasks"></a>ListTasks
Ottenere attività: recuperare le attività da un elenco specifico. 

```GET: /tasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|completato|valore booleano|No|query|Nessuno|Completato|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createtask"></a>CreateTask
Creare un'attività: creare un'attività 

```POST: /tasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Creare la nuova attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|201|Creato|


## <a name="listsubtasks"></a>ListSubTasks
Ottenere le sottoattività: recuperare sottoattività da uno specifico elenco o da un'attività specifica. 

```GET: /subtasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|
|completato|valore booleano|No|query|Nessuno|Completato|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createsubtask"></a>CreateSubTask
Creare una sottoattività: creare una sottoattività all'interno di un'attività specifica 

```POST: /subtasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Nuova sottoattività da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|201|Creato|


## <a name="listnotes"></a>ListNotes
Ottenere le note: recuperare le note per uno specifico elenco o di un'attività specifica. 

```GET: /notes``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createnote"></a>CreateNote
Creare una nota: aggiungere una nota a un'attività specifica 

```POST: /notes``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Nuova nota da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|201|Creato|


## <a name="listcomments"></a>ListComments
Accedere ai commenti: recuperare i commenti delle attività per un elenco specifico o un'attività specifica. 

```GET: /task_comments``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createcomment"></a>CreateComment
Aggiungere un commento a un'attività: aggiungere un commento a un'attività specifica 

```POST: /task_comments``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Nuovo commento di attività da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|201|Creato|


## <a name="retrievereminders"></a>RetrieveReminders
Promemoria: recuperare i promemoria per un elenco specifico o un'attività specifica. 

```GET: /reminders``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createreminder"></a>CreateReminder
Impostare un promemoria: impostare un promemoria. 

```POST: /reminders``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci| |Sì|corpo|Nessuno|Nuovo promemoria da creare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="retrievefiles"></a>RetrieveFiles
Trasferire i file: recuperare i file per un elenco specifico o un'attività specifica. 

```GET: /files``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|valore TASK_ID|numero intero|No|query|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|Richiesta non valida|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getlist"></a>GetList
Ottenere l'elenco: recupera un elenco specifico 

```GET: /lists/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|ID dell'elenco|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="deletelist"></a>DeleteList
Eliminare elenco: elimina un elenco 

```DELETE: /lists/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID dell'elenco|
|revisione|numero intero|Sì|query|Nessuno|Revisione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## <a name="updatelist"></a>UpdateList
Aggiornare un elenco: aggiornare un elenco specifico 

```PATCH: /lists/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID dell'elenco|
|Inserisci| |Sì|corpo|Nessuno|Dettagli elenco|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="gettask"></a>GetTask
Attività: recupera un'attività specifica 

```GET: /tasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|ID|numero intero|Sì|percorso|Nessuno|ID attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="updatetask"></a>UpdateTask
Aggiornare un'attività: aggiorna una specifica attività 

```PATCH: /tasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|ID|numero intero|Sì|percorso|Nessuno|ID attività|
|Inserisci| |Sì|corpo|Nessuno|Dettagli attività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="deletetask"></a>DeleteTask
Eliminare attività: Elimina una specifica attività 

```DELETE: /tasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list_id|numero intero|Sì|query|Nessuno|ID dell'elenco|
|ID|numero intero|Sì|percorso|Nessuno|ID attività|
|revisione|numero intero|Sì|query|Nessuno|Revisione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## <a name="getsubtask"></a>GetSubTask
Ottenere sottoattività: recupera una sottoattività specifica 

```GET: /subtasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|ID sottoattività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="updatesubtask"></a>UpdateSubTask
Aggiornare una sottoattività: aggiorna una sottoattività specifica 

```PATCH: /subtasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID sottoattività|
|Inserisci| |Sì|corpo|Nessuno|Dettagli sottoattività|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="deletesubtask"></a>DeleteSubTask
Eliminare una sottoattività: Elimina una sottoattività specifica 

```DELETE: /subtasks/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID sottoattività|
|revisione|numero intero|Sì|query|Nessuno|Revisione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## <a name="getnote"></a>GetNote
Ottenere una nota: recuperare una nota specifica 

```GET: /notes/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Nota ID|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="updatenote"></a>UpdateNota
Aggiornare una nota: aggiornare una nota specifica 

```PATCH: /notes/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|Nota ID|
|Inserisci| |Sì|corpo|Nessuno|Nota dettagli|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="deletenote"></a>DeleteNote
Eliminare una nota: eliminare una nota specifica 

```DELETE: /notes/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|Nota ID|
|revisione|numero intero|Sì|query|Nessuno|Revisione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## <a name="getcomment"></a>GetComment
Ottenere commento: recuperare un commento specifico 

```GET: /task_comments/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|ID di commento|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="updatereminder"></a>UpdateReminder
Aggiornare un promemoria: aggiornare un promemoria specifico 

```PATCH: /reminders/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID di promemoria|
|Inserisci| |Sì|corpo|Nessuno|Dettagli promemoria|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|


## <a name="deletereminder"></a>DeleteReminder
Eliminare un promemoria: eliminare un promemoria specifico 

```DELETE: /reminders/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|numero intero|Sì|percorso|Nessuno|ID del promemoria.|
|revisione|numero intero|Sì|query|Nessuno|Revisione|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## <a name="object-definitions"></a>Definizioni di oggetti 

### <a name="list"></a>Elenco


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|created_at|stringa|No |
|titolo|stringa|No |
|list_type|stringa|No |
|tipo|stringa|No |
|revisione|numero intero|No |



### <a name="createdlist"></a>CreatedList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|created_at|stringa|No |
|titolo|stringa|No |
|revisione|numero intero|No |
|tipo|stringa|No |



### <a name="task"></a>Attività


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|assignee_id|numero intero|No |
|assigner_id|numero intero|No |
|created_at|stringa|No |
|created_by_id|numero intero|No |
|scadenza|stringa|No |
|list_id|numero intero|No |
|revisione|numero intero|No |
|stella|valore booleano|No |
|titolo|stringa|No |



### <a name="subtask"></a>Sottoattività


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|valore TASK_ID|numero intero|No |
|created_at|stringa|No |
|created_by_id|numero intero|No |
|revisione|stringa|No |
|titolo|stringa|No |



### <a name="note"></a>Nota


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|valore TASK_ID|numero intero|No |
|contenuto|stringa|No |
|created_at|stringa|No |
|updated_at|stringa|No |
|revisione|numero intero|No |



### <a name="comment"></a>Commento


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|valore TASK_ID|numero intero|No |
|revisione|numero intero|No |
|testo|stringa|No |
|tipo|stringa|No |
|created_at|stringa|No |



### <a name="reminder"></a>Promemoria


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|Data|stringa|No |
|valore TASK_ID|numero intero|No |
|revisione|numero intero|No |
|tipo|stringa|No |
|created_at|stringa|No |
|updated_at|stringa|No |



### <a name="createdreminder"></a>CreatedReminder


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|Data|stringa|No |
|valore TASK_ID|numero intero|No |
|revisione|numero intero|No |
|created_at|stringa|No |
|updated_at|stringa|No |



### <a name="file"></a>File


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|numero intero|No |
|URL|stringa|No |
|valore TASK_ID|numero intero|No |
|list_id|numero intero|No |
|USER_ID|numero intero|No |
|nome_file|stringa|No |
|Content_Types|stringa|No |
|FILE_SIZE|numero intero|No |
|local_created_at|stringa|No |
|created_at|stringa|No |
|updated_at|stringa|No |
|tipo|stringa|No |
|revisione|numero intero|No |



### <a name="newtask"></a>Nuova attività


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list_id|numero intero|Sì |
|titolo|stringa|Sì |
|assignee_id|numero intero|No |
|completato|valore booleano|No |
|recurrence_type|stringa|No |
|recurrence_count|numero intero|No |
|scadenza|stringa|No |
|stella|valore booleano|No |



### <a name="newlist"></a>NewList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|titolo|stringa|Sì |



### <a name="newsubtask"></a>NewSubtask


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list_id|numero intero|Sì |
|valore TASK_ID|numero intero|Sì |
|titolo|stringa|Sì |
|completato|valore booleano|No |



### <a name="newnote"></a>NewNote


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list_id|numero intero|Sì |
|valore TASK_ID|numero intero|Sì |
|contenuto|stringa|Sì |



### <a name="newcomment"></a>NuovoCommento


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list_id|numero intero|Sì |
|valore TASK_ID|numero intero|Sì |
|testo|stringa|Sì |



### <a name="newreminder"></a>NewReminder


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list_id|numero intero|Sì |
|valore TASK_ID|numero intero|Sì |
|Data|stringa|Sì |



### <a name="updatetask"></a>UpdateTask


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revisione|numero intero|No |
|titolo|stringa|No |
|assignee_id|numero intero|No |
|completato|valore booleano|No |
|recurrence_type|stringa|No |
|recurrence_count|numero intero|No |
|scadenza|stringa|No |
|stella|valore booleano|No |



### <a name="updatelist"></a>UpdateList


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revisione|numero intero|No |
|titolo|stringa|No |



### <a name="updatesubtask"></a>UpdateSubtask


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revisione|numero intero|No |
|titolo|stringa|No |
|completato|valore booleano|No |



### <a name="updatenote"></a>UpdateNota


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revisione|numero intero|No |
|contenuto|stringa|No |



### <a name="updatereminder"></a>UpdateReminder


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Data|stringa|No |
|revisione|numero intero|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)