<properties
    pageTitle="Aggiungere il connettore Facebook nelle applicazioni logica | Microsoft Azure"
    description="Panoramica del connettore Facebook con i parametri di API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>Iniziare a utilizzare il connettore Facebook
Connettersi a Facebook e inserire un post in una sequenza temporale, ottenere una pagina feed e altro ancora. 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema.


Con Facebook, è possibile:

- Definire il flusso di business in base ai dati che viene visualizzato da Facebook. 
- Utilizzare un trigger quando si riceve un nuovo post.
- È possibile ottenere le azioni di utilizzo che post alla sequenza temporale, una pagina feed e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Quando arriva un nuovo post sull'indicatore cronologico, ad esempio, è possibile eseguire questo post e push per il feed Twitter. 



Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Facebook include i trigger e le azioni seguenti. 

| Trigger | Azioni|
| --- | --- |
| <ul><li>In caso di un nuovo post nel forum la sequenza temporale</li></ul> |<ul><li>Accedere al feed dalla sequenza temporale</li><li>Registrare la sequenza temporale</li><li>In caso di un nuovo post nel forum la sequenza temporale</li><li>Ottenere pagina feed</li><li>Ottenere utente sequenza temporale</li><li>A pagina</li></ul>

Tutti i connettori supportano dati nei formati XML e JSON.

## <a name="create-a-connection-to-facebook"></a>Creare una connessione a Facebook
Quando si aggiunge il connettore alle App logica, è necessario autorizzare App logica a cui connettersi i Facebook.

1. Accedere al proprio account di Facebook
2. Selezionare **l'autorizzazione**e per consentire le app di logica di connettersi e utilizzare i Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] È possibile utilizzare la stessa connessione a Facebook in altre applicazioni di logica.

## <a name="swagger-rest-api-reference"></a>Guida di riferimento all'API REST swagger
Si applica alla versione: 1.0.

### <a name="get-feed-from-my-timeline"></a>Accedere al feed dalla sequenza temporale
Recupera i feed dalla sequenza temporale connesse dell'utente.  
```GET: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|campi|stringa|No|query|Nessuno |Specificare i campi che si desidera ottenere. Esempio (id, nome e immagine).|
|limite|numero intero|No|query| Nessuno|Numero massimo di post da recuperare|
|con|stringa|No|query| Nessuno|Limitare l'elenco di post a solo quelli con i passaggi associati.|
|filtro|stringa|No|query| Nessuno|Recuperare solo i post che corrispondano a un determinato flusso filtro.|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="post-to-my-timeline"></a>Registrare la sequenza temporale
Inviare un messaggio di stato alla sequenza temporale connesse dell'utente.  
```POST: /me/feed```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|Inserisci|stringa |Sì|corpo|Nessuno |Nuovo messaggio verrà inserito|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>In caso di un nuovo post nel forum la sequenza temporale
Attiva un nuovo flusso di cui è presente un nuovo post nella sequenza temporale connesse dell'utente.  
```GET: /trigger/me/feed```

Non sono presenti parametri. 

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-page-feed"></a>Ottenere pagina feed
È possibile ottenere i post del feed di una pagina specifica.  
```GET: /{pageId}/feed```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|pageId|stringa|Sì|percorso| Nessuno|ID della pagina da cui devono essere recuperate post.|
|limite|numero intero|No|query| Nessuno|Numero massimo di post da recuperare|
|include_hidden|valore booleano|No|query|Nessuno |Se includere o meno i qualsiasi post nascosti dalla pagina|
|campi|stringa|No|query|Nessuno |Specificare i campi che si desidera ottenere. Esempio (id, nome e immagine).|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-user-timeline"></a>Ottenere utente sequenza temporale
È possibile ottenere i post da sequenza temporale dell'utente.  
```GET: /{userId}/feed```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID utente|stringa|Sì|percorso|Nessuno |ID dell'utente il cui sequenza temporale devono essere recuperate.|
|limite|numero intero|No|query|Nessuno |Numero massimo di post da recuperare|
|con|stringa|No|query|Nessuno |Limitare l'elenco di post a solo quelli con i passaggi associati.|
|filtro|stringa|No|query| Nessuno|Recuperare solo i post che corrispondano a un determinato flusso filtro.|
|campi|stringa|No|query| Nessuno|Specificare i campi che si desidera ottenere. Esempio (id, nome e immagine).|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="post-to-page"></a>A pagina
Inviare un messaggio a una Page di Facebook come l'utente ha effettuato l'accesso.  
```POST: /{pageId}/feed```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|pageId|stringa|Sì|percorso|Nessuno |ID della pagina per la pubblicazione.|
|Inserisci|molti |Sì|corpo|Nessuno |Nuovo messaggio verrà inserito.|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="getfeedresponse"></a>GetFeedResponse

|Nome della proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|dati|in forma di matrice|No|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|dati|in forma di matrice|No|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: Una sola voce in un profilo del feed
Potrebbe essere il profilo utente, pagina, app o gruppo. 

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|admin_creator|in forma di matrice|No|
|didascalia|stringa|No|
|created_time|stringa|No|
|Descrizione|stringa|No|
|feed_targeting|non è definito|No|
|Da|non è definito|No|
|icona|stringa|No|
|is_hidden|valore booleano|No|
|is_published|valore booleano|No|
|collegamento|stringa|No|
|Messaggio|stringa|No|
|nome|stringa|No|
|object_id|stringa|No|
|immagine|stringa|No|
|posizione|non è definito|No|
|Informativa sulla privacy|non è definito|No|
|proprietà|in forma di matrice|No|
|origine|stringa|No|
|status_type|stringa|No|
|brano|stringa|No|
|l'assegnazione del|non è definito|No|
|A|in forma di matrice|No|
|tipo|stringa|No|
|updated_time|stringa|No|
|with_tags|non è definito|No|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: Una sola voce in un profilo del feed
Potrebbe essere il profilo utente, pagina, app o gruppo.

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|created_time|stringa|No|
|Da|non è definito|No|
|Messaggio|stringa|No|
|tipo|stringa|No|

#### <a name="adminitem"></a>AdminItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|collegamento|stringa|No|

#### <a name="propertyitem"></a>PropertyItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|nome|stringa|No|
|testo|stringa|No|
|con sottoindirizzo|stringa|No|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Messaggio|stringa|Sì|
|collegamento|stringa|No|
|immagine|stringa|No|
|nome|stringa|No|
|didascalia|stringa|No|
|Descrizione|stringa|No|
|posizione|stringa|No|
|tag|stringa|No|
|Informativa sulla privacy|non è definito|No|
|object_attachment|stringa|No|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Messaggio|stringa|Sì|
|collegamento|stringa|No|
|immagine|stringa|No|
|nome|stringa|No|
|didascalia|stringa|No|
|Descrizione|stringa|No|
|azioni|in forma di matrice|No|
|posizione|stringa|No|
|tag|stringa|No|
|object_attachment|stringa|No|
|l'assegnazione del|non è definito|No|
|feed_targeting|non è definito|No|
|pubblicato|valore booleano|No|
|scheduled_publish_time|stringa|No|
|backdated_time|stringa|No|
|backdated_time_granularity|stringa|No|
|child_attachments|in forma di matrice|No|
|multi_share_end_card|valore booleano|No|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|

#### <a name="profilecollection"></a>ProfileCollection

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|dati|in forma di matrice|No|

#### <a name="useritem"></a>UserItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|nome|stringa|No|
|Cognome|stringa|No|
|nome|stringa|No|
|sesso|stringa|No|
|sulle|stringa|No|

#### <a name="actionitem"></a>ActionItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|nome|stringa|No|
|collegamento|stringa|No|

#### <a name="targetitem"></a>TargetItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|paesi|in forma di matrice|No|
|impostazioni internazionali|in forma di matrice|No|
|aree|in forma di matrice|No|
|Città|in forma di matrice|No|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: Oggetto che controlla news feed destinazione per questo post
Tutti gli utenti in questi gruppi ha più probabilità di vedere il post, altri sono meno probabili. Si applica solo alle pagine.

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|paesi|in forma di matrice|No|
|aree|in forma di matrice|No|
|Città|in forma di matrice|No|
|age_min|numero intero|No|
|age_max|numero intero|No|
|generi|in forma di matrice|No|
|relationship_statuses|in forma di matrice|No|
|interested_in|in forma di matrice|No|
|college_years|in forma di matrice|No|
|interessi|in forma di matrice|No|
|relevant_until|numero intero|No|
|education_statuses|in forma di matrice|No|
|impostazioni internazionali|in forma di matrice|No|

#### <a name="placeitem"></a>PlaceItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|nome|stringa|No|
|overall_rating|numero|No|
|posizione|non è definito|No|

#### <a name="locationitem"></a>LocationItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Città|stringa|No|
|paese|stringa|No|
|latitudine|numero|No|
|located_in|stringa|No|
|longitudine|numero|No|
|nome|stringa|No|
|area geografica|stringa|No|
|stato|stringa|No|
|via e numero civico|stringa|No|
|CAP|stringa|No|

#### <a name="privacyitem"></a>PrivacyItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Descrizione|stringa|No|
|valore|stringa|Sì|
|Consenti|stringa|No|
|negare|stringa|No|
|amici|stringa|No|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|collegamento|stringa|No|
|immagine|stringa|No|
|image_hash|stringa|No|
|nome|stringa|No|
|Descrizione|stringa|No|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|URL|stringa|Sì|
|didascalia|stringa|No|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|Sì|
|post_id|stringa|Sì|

#### <a name="postvideorequest"></a>PostVideoRequest

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|videoData|stringa|Sì|
|Descrizione|stringa|Sì|
|titolo|stringa|Sì|
|uploadedVideoName|stringa|No|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|dati|non è definito|Sì|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|URL|stringa|Sì|
|is_silhouette|valore booleano|Sì|
|altezza|stringa|No|
|Larghezza|stringa|No|

#### <a name="geteventresponse"></a>GetEventResponse

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|dati|in forma di matrice|Sì|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|Sì|
|nome|stringa|Sì|
|ora_avvio|stringa|No|
|end_time|stringa|No|
|fuso orario|stringa|No|
|posizione|stringa|No|
|Descrizione|stringa|No|
|ticket_uri|stringa|No|
|rsvp_status|stringa|Sì|


## <a name="next-steps"></a>Passaggi successivi

[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).
