<properties
pageTitle="Aggiungere il connettore Yammer nelle applicazioni logica | Microsoft Azure"
description="Panoramica del connettore Yammer con i parametri di API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-yammer-connector"></a>Iniziare a utilizzare il connettore di Yammer

Connettersi a Yammer alle conversazioni di accesso in rete aziendale.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema.

Con Yammer, è possibile:

- Definire il flusso di business in base ai dati che viene visualizzato da Yammer. 
- Usare attiva per cui è presente un nuovo messaggio in un gruppo o un feed le operazioni seguenti.
- Usare azioni per inviare un messaggio, visualizzare tutti i messaggi e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Ad esempio, quando viene visualizzato un nuovo messaggio, è possibile inviare un messaggio di posta elettronica con Office 365.

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Yammer include i trigger e le azioni seguenti. 

Trigger | Azioni
--- | ---
<ul><li>Quando è disponibile un nuovo messaggio in un gruppo</li><li>In caso di un nuovo messaggio personale seguito feed</li></ul>| <ul><li>Ottenere tutti i messaggi</li><li>Ottiene i messaggi in un gruppo</li><li>Ottiene che i messaggi provenienti da personale seguenti feed</li><li>Invia messaggio</li><li>Quando è disponibile un nuovo messaggio in un gruppo</li><li>In caso di un nuovo messaggio personale seguito feed</li></ul>

Tutti i connettori supportano dati nei formati XML e JSON. 

## <a name="create-a-connection-to-yammer"></a>Creare una connessione a Yammer
Per utilizzare il connettore Yammer, è prima di tutto creare una **connessione** quindi fornire i dettagli per queste proprietà: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali di Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="yammer-rest-api-reference"></a>Guida di riferimento all'API REST di Yammer
Questa documentazione è per la versione: 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Ottenere tutti i messaggi pubblici in rete Yammer dell'utente connesso
Corrisponde a "Tutti" conversazioni nell'interfaccia di web Yammer.  
```GET: /messages.json```

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|older_then|numero intero|No|query|Nessuno|Restituisce i messaggi antecedenti l'ID di messaggio specificato come stringa numerica. Ciò è utile per l'impaginazione messaggi. Ad esempio, se si sta visualizzando 20 messaggi e all'ultima il numero 2912, è possibile aggiungere "? older_than = 2912″ alla richiesta di ricevere i 20 messaggi prima di quelle viene visualizzato.|
|newer_then|numero intero|No|query|Nessuno|Restituisce i messaggi più recente di ID messaggio specificato come stringa numerica. Dovrebbe essere usata per il polling dei nuovi messaggi. Se si cercano messaggi e il messaggio più recente restituito è. 3516, è possibile effettuare una richiesta con il parametro "? newer_than = 3516″ per accertarsi che non si ottengono duplicate copie dei messaggi già nella pagina.|
|limite|numero intero|No|query|Nessuno|Restituisce il numero specificato di messaggi.|
|pagina|numero intero|No|query|Nessuno|È possibile ottenere la pagina specificata. Se restituito dati sono maggiori del limite, è possibile utilizzare questo campo per accedere alla pagina successiva|


### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|408|Timeout della richiesta|
|429|Troppe richieste|
|500|Errore interno del Server. Errore sconosciuto|
|503|Servizio di Yammer non disponibile|
|504|Timeout gateway|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Inviare un messaggio a un gruppo o il Feed intera società
Se l'ID di gruppo, messaggio verrà inviato al gruppo specificato altro che verranno registrato in tutte le società Feed.    
```POST: /messages.json``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|input| |Sì|corpo|Nessuno|Richiesta di messaggio post|


### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|201|Creato|



## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="message-yammer-message"></a>Messaggio: Messaggio di Yammer

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|ID|numero intero|No|
|content_excerpt|stringa|No|
|sender_id|numero intero|No|
|replied_to_id|numero intero|No|
|created_at|stringa|No|
|network_id|numero intero|No|
|message_type|stringa|No|
|sender_type|stringa|No|
|URL|stringa|No|
|web_url|stringa|No|
|group_id|numero intero|No|
|corpo|non è definito|No|
|thread_id|numero intero|No|
|direct_message|valore booleano|No|
|client_type|stringa|No|
|client_url|stringa|No|
|lingua|stringa|No|
|notified_user_ids|in forma di matrice|No|
|Informativa sulla privacy|stringa|No|
|liked_by|non è definito|No|
|system_message|valore booleano|No|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Rappresenta una richiesta di post per Yammer connettore pubblicare in yammer

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|corpo|stringa|Sì|
|group_id|numero intero|No|
|replied_to_id|numero intero|No|
|direct_to_id|numero intero|No|
|trasmissione|valore booleano|No|
|argomento1|stringa|No|
|argomento2|stringa|No|
|topic3|stringa|No|
|topic4|stringa|No|
|topic5|stringa|No|
|topic6|stringa|No|
|topic7|stringa|No|
|topic8|stringa|No|
|topic9|stringa|No|
|topic10|stringa|No|
|topic11|stringa|No|
|topic12|stringa|No|
|topic13|stringa|No|
|topic14|stringa|No|
|topic15|stringa|No|
|topic16|stringa|No|
|topic17|stringa|No|
|topic18|stringa|No|
|topic19|stringa|No|
|topic20|stringa|No|

#### <a name="messagelist-list-of-messages"></a>MessageList: Elenco dei messaggi

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|messaggi|in forma di matrice|No|


#### <a name="messagebody-message-body"></a>MessageBody: Corpo del messaggio

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|analizzati predefiniti|stringa|No|
|normale|stringa|No|
|RTF|stringa|No|

#### <a name="likedby-liked-by"></a>LikedBy: Mi da

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|Conta. numeri|numero intero|No|
|nomi|in forma di matrice|No|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: Mi da

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|tipo|stringa|No|
|ID|numero intero|No|
|full_name|stringa|No|


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
