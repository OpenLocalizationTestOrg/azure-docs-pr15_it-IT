<properties
pageTitle=" Utilizzare il connettore margine di flessibilità nelle applicazioni logica | Microsoft Azure"
description="Per iniziare a usare il connettore margine di flessibilità nelle applicazioni logica del servizio di Microsoft Azure App"
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

# <a name="get-started-with-the-slack-connector"></a>Iniziare a utilizzare il connettore margine di flessibilità

Margine di flessibilità è uno strumento di comunicazione del team, che riunisce tutte le comunicazioni tra team in uno posizionare istantaneamente che supportano le ricerche e disponibile ovunque.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema.

Con il margine di flessibilità connettore, è possibile:

* Utilizzare per creare App logica

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Informazioni sul trigger e azioni

Connettore margine di flessibilità può essere utilizzato come un'azione. non esistono alcun trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore margine di flessibilità è le seguenti azioni e/o trigger disponibili:

### <a name="slack-actions"></a>Azioni del margine di flessibilità
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|PostMessage|Inviare un messaggio a uno specifico canale.|
## <a name="create-a-connection-to-slack"></a>Creare una connessione a un margine di flessibilità
Per utilizzare il connettore margine di flessibilità, è prima di tutto creare una **connessione** quindi fornire i dettagli per queste proprietà: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali margine di flessibilità|

Seguire questi passaggi per accedere a un margine di flessibilità e completare la configurazione del margine di flessibilità **connessione** nell'app logica:

1. Selezionare **ricorrenza**
2. Selezionare una **frequenza** e immettere un **intervallo**
3. Selezionare **Aggiungi un'azione**  
![Configurare il margine di flessibilità][1]  
4. Immettere il margine di flessibilità nella casella di ricerca e attendere che la ricerca restituire tutte le voci con il margine di flessibilità nel nome
5. Selezionare **il margine di flessibilità - Invia messaggio**
6. Selezionare **accedere al margine di flessibilità**:  
![Configurare il margine di flessibilità][2]
7. Specificare le credenziali per accedere a autorizzare l'applicazione del margine di flessibilità    
![Configurare il margine di flessibilità][3]  
8. Verrà reindirizzati alla Log dell'organizzazione nella pagina. **Autorizzare** Margine di flessibilità per interagire con l'app logica:      
![Configurare il margine di flessibilità][5] 
9. Al termine dell'autorizzazione si verrà reindirizzati logica all'applicazione in uso per completare configurando la sezione **margine di flessibilità - ottenere tutti i messaggi** . Aggiungere altri eventi e le azioni che è necessario.  
![Configurare il margine di flessibilità][6]
10. Salvare il lavoro scegliendo **Salva** sulla barra dei menu precedente.


>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="slack-rest-api-reference"></a>Guida di riferimento all'API REST margine di flessibilità
#### <a name="this-documentation-is-for-version-10"></a>Questa documentazione è per la versione: 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Inviare un messaggio a uno specifico canale.
**```POST: /chat.postMessage```** 



| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|canale|stringa|Sì|query|Nessuno|Canale, gruppo privato o canale di messaggistica Istantanea per inviare messaggi a. Può essere un nome (ex: #general) o un ID codificato.|
|testo|stringa|Sì|query|Nessuno|Testo del messaggio da inviare. Per le opzioni di formattazione, vedere https://api.slack.com/docs/formatting.|
|nome utente|stringa|No|query|Nessuno|Nome del bot|
|as_user|valore booleano|No|query|Nessuno|Passare true per inviare il messaggio come utente autenticato, anziché come un bot|
|analisi|stringa|No|query|Nessuno|Modificare la modalità vengono gestiti messaggi. Per informazioni dettagliate, vedere https://api.slack.com/docs/formatting.|
|link_names|numero intero|No|query|Nessuno|Trovare e collegare i nomi dei canali e i nomi utente.|
|unfurl_links|valore booleano|No|query|Nessuno|Passare true Abilita unfurling principalmente contenuto basato su testo.|
|unfurl_media|valore booleano|No|query|Nessuno|Passare false per disabilitare unfurling di contenuti multimediali.|
|icon_url|stringa|No|query|Nessuno|URL di un'immagine da utilizzare come un'icona per il messaggio|
|icon_emoji|stringa|No|query|Nessuno|Emoji da utilizzare come un'icona per il messaggio|


### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Ok|
|400|Richiesta non valida|
|408|Timeout della richiesta|
|429|Troppe richieste|
|500|Errore interno del Server. Errore sconosciuto|
|503|Margine di flessibilità servizio non disponibile|
|504|Timeout gateway|
|impostazione predefinita|Operazione non è riuscita.|
------



## <a name="object-definitions"></a>Definizioni di oggetto: 

 **Messaggio**: messaggio di Yammer

Proprietà necessarie per messaggio:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|ID|numero intero|
|content_excerpt|stringa|
|sender_id|numero intero|
|replied_to_id|numero intero|
|created_at|stringa|
|network_id|numero intero|
|message_type|stringa|
|sender_type|stringa|
|URL|stringa|
|web_url|stringa|
|group_id|numero intero|
|corpo|non è definito|
|thread_id|numero intero|
|direct_message|valore booleano|
|client_type|stringa|
|client_url|stringa|
|lingua|stringa|
|notified_user_ids|in forma di matrice|
|Informativa sulla privacy|stringa|
|liked_by|non è definito|
|system_message|valore booleano|



 **PostOperationRequest**: rappresenta una richiesta di post per Yammer connettore pubblicare in yammer

Proprietà necessarie per PostOperationRequest:

corpo

**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|corpo|stringa|
|group_id|numero intero|
|replied_to_id|numero intero|
|direct_to_id|numero intero|
|trasmissione|valore booleano|
|argomento1|stringa|
|argomento2|stringa|
|topic3|stringa|
|topic4|stringa|
|topic5|stringa|
|topic6|stringa|
|topic7|stringa|
|topic8|stringa|
|topic9|stringa|
|topic10|stringa|
|topic11|stringa|
|topic12|stringa|
|topic13|stringa|
|topic14|stringa|
|topic15|stringa|
|topic16|stringa|
|topic17|stringa|
|topic18|stringa|
|topic19|stringa|
|topic20|stringa|



 **MessageList**: elenco dei messaggi

Proprietà necessarie per MessageList:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|messaggi|in forma di matrice|



 **MessageBody**: corpo del messaggio

Proprietà necessarie per MessageBody:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|analizzati predefiniti|stringa|
|normale|stringa|
|RTF|stringa|



 **LikedBy**: mi da

Proprietà necessarie per LikedBy:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Conta. numeri|numero intero|
|nomi|in forma di matrice|



 **YammmerEntity**: mi da

Proprietà necessarie per YammmerEntity:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|tipo|stringa|
|ID|numero intero|
|full_name|stringa|


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Definizioni di oggetto: 

 **WebResultModel**: risultati di ricerca web Bing

Proprietà necessarie per WebResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Titolo|stringa|
|Descrizione|stringa|
|DisplayUrl|stringa|
|ID|stringa|
|FullUrl|stringa|



 **VideoResultModel**: i risultati di ricerca di video di Bing

Proprietà necessarie per VideoResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Titolo|stringa|
|DisplayUrl|stringa|
|ID|stringa|
|MediaUrl|stringa|
|Fase di esecuzione|numero intero|
|Anteprima|non è definito|



 **ThumbnailModel**: proprietà anteprima dell'elemento multimedia

Proprietà necessarie per ThumbnailModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|MediaUrl|stringa|
|ContentType|stringa|
|Larghezza|numero intero|
|Altezza|numero intero|
|FileSize|numero intero|



 **ImageResultModel**: risultati della ricerca immagini Bing

Proprietà necessarie per ImageResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Titolo|stringa|
|DisplayUrl|stringa|
|ID|stringa|
|MediaUrl|stringa|
|SourceUrl|stringa|
|Anteprima|non è definito|



 **NewsResultModel**: risultati della ricerca di Bing news

Proprietà necessarie per NewsResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Titolo|stringa|
|Descrizione|stringa|
|DisplayUrl|stringa|
|ID|stringa|
|Origine|stringa|
|Data|stringa|



 **SpellResultModel**: Bing ortografia suggerimenti per i risultati

Proprietà necessarie per SpellResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|ID|stringa|
|Valore|stringa|



 **RelatedSearchResultModel**: Bing relativi risultati della ricerca

Proprietà necessarie per RelatedSearchResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Titolo|stringa|
|ID|stringa|
|BingUrl|stringa|



 **CompositeSearchResultModel**: risultati della ricerca composte di Bing

Proprietà necessarie per CompositeSearchResultModel:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|WebResultsTotal|numero intero|
|ImageResultsTotal|numero intero|
|VideoResultsTotal|numero intero|
|NewsResultsTotal|numero intero|
|SpellSuggestionsTotal|numero intero|
|WebResults|in forma di matrice|
|ImageResults|in forma di matrice|
|VideoResults|in forma di matrice|
|NewsResults|in forma di matrice|
|SpellSuggestionResults|in forma di matrice|
|RelatedSearchResults|in forma di matrice|


## <a name="object-definitions"></a>Definizioni di oggetto: 

 **PostOperationResponse**: rappresenta risposta dell'operazione di post del margine di flessibilità connettore per la registrazione per il margine di flessibilità

Proprietà necessarie per PostOperationResponse:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|Ok|valore booleano|
|canale|stringa|
|Servizi terminal|stringa|
|Messaggio|non è definito|
|Errore|stringa|



 **MessageItem**: un messaggio di canale.

Proprietà necessarie per MessageItem:


Le proprietà non sono più necessari. 


**Tutte le proprietà**: 


| Nome | Tipo di dati |
|---|---|
|testo|stringa|
|ID|stringa|
|utente|stringa|
|creato|numero intero|
|eliminare is_user|valore booleano|


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
