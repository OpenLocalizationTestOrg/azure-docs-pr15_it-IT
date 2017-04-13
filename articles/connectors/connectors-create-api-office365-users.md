<properties
    pageTitle="Aggiungere il connettore gli utenti di Office 365 in logica Apps | Microsoft Azure"
    description="Panoramica del connettore gli utenti di Office 365 con i parametri di API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Iniziare a utilizzare il connettore gli utenti di Office 365

Connettersi agli utenti di Office 365 per ottenere i profili, Cerca utenti e altro. 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema.

Con gli utenti di Office 365, è possibile:

- Definire il flusso di business in base ai dati che viene visualizzato dagli utenti di Office 365. 
- È possibile ottenere le azioni di utilizzo che ottenere dipendenti diretti dell'utente, profilo utente del responsabile e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Ad esempio, ottenere diretti di una persona, quindi eseguire queste informazioni e aggiornare un database SQL Azure. 

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Il connettore gli utenti di Office 365 include le seguenti azioni disponibili. Non esistono alcun trigger.

| Trigger | Azioni|
| --- | --- |
|Nessuno | <ul><li>Ottenere manager</li><li>Ottenere profilo personale</li><li>Ottenere dipendenti diretti dell'utente</li><li>Ottenere il profilo utente</li><li>Cerca utenti</li></ul>|

Tutti i connettori supportano dati nei formati XML e JSON. 


## <a name="create-a-connection-to-office-365-users"></a>Creare una connessione agli utenti di Office 365

Quando si aggiunge il connettore alle App logica, è necessario accesso al proprio account Office 365 utenti e consentire App logica per connettersi al proprio account.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Dopo aver creato la connessione, immettere le proprietà di utenti di Office 365, ad esempio l'ID utente. Il **riferimento all'API REST** in questo argomento sono descritte le proprietà.

>[AZURE.TIP] È possibile utilizzare la stessa connessione gli utenti di Office 365 in altre applicazioni di logica.


## <a name="office-365-users-rest-api-reference"></a>Riferimento all'API REST agli utenti di Office 365
Si applica alla versione: 1.0.

### <a name="get-my-profile"></a>Ottenere profilo personale 
Recupera il profilo per l'utente corrente.  
```GET: /users/me``` 

Non sono presenti parametri per la chiamata.

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


### <a name="get-user-profile"></a>Ottenere il profilo utente 
Recupera un profilo utente specifico.  
```GET: /users/{userId}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID utente|stringa|Sì|percorso|Nessuno|Id di posta elettronica o nome dell'entità utente|

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


### <a name="get-manager"></a>Ottenere manager 
Recupera profilo utente per il responsabile dell'utente specificato.  
```GET: /users/{userId}/manager``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID utente|stringa|Sì|percorso|Nessuno|Id di posta elettronica o nome dell'entità utente|

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



### <a name="get-direct-reports"></a>Ottenere dipendenti diretti dell'utente 
È possibile ottenere dipendenti diretti dell'utente.  
```GET: /users/{userId}/directReports``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID utente|stringa|Sì|percorso|Nessuno|Id di posta elettronica o nome dell'entità utente|

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



### <a name="search-for-users"></a>Cerca utenti 
Recupera i risultati dei profili utente di ricerca.  
```GET: /users``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|searchTerm|stringa|No|query|Nessuno|Stringa di ricerca (si applica a: visualizzare nome, nome, cognome, posta elettronica, posta elettronica alternativo e principale del nome utente)|

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



## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="user-user-model-class"></a>Utente: Classe modello utente

|Nome della proprietà | Tipo di dati |Obbligatorio
|---|---|---|
|DisplayName|stringa|No|
|GivenName|stringa|No|
|Cognome|stringa|No|
|Posta elettronica|stringa|No|
|MailNickname|stringa|No|
|TelephoneNumber|stringa|No|
|AccountEnabled|valore booleano|No|
|ID|stringa|Sì
|UserPrincipalName|stringa|No|
|Reparto|stringa|No|
|JobTitle|stringa|No|
|mobilePhone|stringa|No|


## <a name="next-steps"></a>Passaggi successivi

[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
