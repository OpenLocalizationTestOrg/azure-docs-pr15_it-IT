<properties
pageTitle="Utilizzare il connettore di Office 365 Video nelle applicazioni logica | Microsoft Azure"
description="Per iniziare a usare il connettore di Office 365 Video nelle applicazioni Microsoft Azure App servizio logica"
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

# <a name="get-started-with-the-office365-video-connector"></a>Iniziare a utilizzare il connettore Video di Office 365
Connettersi a Office 365 Video per ottenere informazioni di Office 365 video, ottenere un elenco di video e così via. Il connettore di Office 365 Video può essere usato da:

- App logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. Il connettore non è supportato in tutte le versioni precedenti di schema.

Con Office 365 Video, è possibile:

- Definire il flusso di business in base ai dati che viene visualizzato da Office 365 Video. 
- Usare le azioni che controllare lo stato del portale video, ottenere un elenco di tutti video in un canale e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Ad esempio, è possibile utilizzare il connettore di ricerca Bing per cercare i video di Office 365 e quindi utilizzare il connettore video di Office 365 per ottenere informazioni su tale video. Se il video soddisfino le proprie esigenze, è possibile registrare questo video su Facebook. 

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Il connettore di Office 365 Video è le seguenti azioni disponibili. Non esistono alcun trigger.

| Trigger | Azioni|
| --- | --- |
| Nessuno | <ul><li>Controlla stato portale video</li><li>Ottenere tutti i canali visti dall'utente</li><li>Ottenere l'url di riproduzione del manifesto di servizi multimediali di Windows Azure per un video</li><li>Ottenere il token del titolare per accedere a decrittografare il video</li><li>Ottiene informazioni su un particolare Office 365 video</li><li>Elenca tutti i video di Office 365 presenti in un canale</li></ul>

Tutti i connettori supportano dati nei formati XML e JSON. 

## <a name="create-a-connection-to-office365-video-connector"></a>Creare una connessione a Office 365 Video connettore
Quando si aggiunge il connettore alle App logica, è necessario accesso al proprio account Office 365 Video e consentire App logica per connettersi al proprio account.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Dopo aver creato la connessione, immettere le proprietà di video di Office 365, ad esempio il nome di tenant o di canale ID. Il **riferimento all'API REST** in questo argomento sono descritte le proprietà.

>[AZURE.TIP] È possibile utilizzare la stessa connessione di Office 365 Video in altre applicazioni di logica.

## <a name="swagger-rest-api-reference"></a>Guida di riferimento all'API REST swagger
Si applica alla versione: 1.0.

### <a name="checks-video-portal-status"></a>Controlla stato portale video 
Verificare lo stato del portale video per verificare se sono abilitati servizi video.  
```GET: /{tenant}/IsEnabled``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|



### <a name="get-all-viewable-channels"></a>Ottenere tutti i canali visti dall'utente 
Ottiene tutti i canali a che l'utente ha la visualizzazione di accesso.  
```GET: /{tenant}/Channels``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Elenca tutti i video di Office 365 presenti in un canale 
Elenca tutti i video di Office 365 presenti in un canale.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|
|channelId|stringa|Sì|percorso|Nessuno|L'id di canale da cui è necessario essere recuperati video|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|




### <a name="gets-information-about-a-particular-office365-video"></a>Ottiene informazioni su un particolare Office 365 video 
Ottiene informazioni su un particolare Office 365 video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|
|channelId|stringa|Sì|percorso|Nessuno|L'id di canale|
|videoId|stringa|Sì|percorso|Nessuno|L'id di video|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Ottenere l'url di riproduzione del manifesto di servizi multimediali di Windows Azure per un video 
È possibile ottenere l'url di riproduzione del manifesto di servizi multimediali di Windows Azure per un video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|
|channelId|stringa|Sì|percorso|Nessuno|L'id di canale|
|videoId|stringa|Sì|percorso|Nessuno|L'id di video|
|streamingFormatType|stringa|Sì|query|Nessuno|Tipo di formato flusso. 1 - smooth Streaming o MPEG trattino. 0 - HLS Streaming|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Ottenere il token del titolare per accedere a decrittografare il video 
È possibile ottenere il token del titolare per accedere a decrittografare il video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|stringa|Sì|percorso|Nessuno|Il nome del tenant per la directory l'utente fa parte di|
|channelId|stringa|Sì|percorso|Nessuno|L'id di canale|
|videoId|stringa|Sì|percorso|Nessuno|L'id di video|


#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione completata|
|400|BadRequest|
|401|Non autorizzato|
|404|Non trovato|
|500|Errore interno del Server|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="channel-channel-class"></a>Canale: Classe canale

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|ID|stringa|No|
|Titolo|stringa|No|
|Descrizione|stringa|No|


#### <a name="video"></a>Video 

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|Titolo|stringa|No|
|Descrizione|stringa|No|
|CreationDate|stringa|No|
|Proprietario|stringa|No|
|ThumbnailUrl|stringa|No|
|VideoUrl|stringa|No|
|VideoDuration|numero intero|No|
|VideoProcessingStatus|numero intero|No|
|ViewCount|numero intero|No|


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).
