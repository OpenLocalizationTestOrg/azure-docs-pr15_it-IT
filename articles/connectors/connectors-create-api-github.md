<properties
pageTitle="GitHub | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. GitHub è un archivio di operazioni basate sul web servizio di hosting. Sono disponibili tutte le funzionalità revisione distribuito controllo e origine codice gestione (servizi) di operazioni, nonché aggiungere i proprio caratteristiche."
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

# <a name="get-started-with-the-github-connector"></a>Iniziare a utilizzare il connettore GitHub

GitHub è un archivio di operazioni basate sul web servizio di hosting. Sono disponibili tutte le funzionalità revisione distribuito controllo e origine codice gestione (servizi) di operazioni, nonché aggiungere i proprio caratteristiche.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore GitHub può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore GitHub è le seguenti azioni e/o trigger disponibili:

### <a name="github-actions"></a>Azioni GitHub
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crea un problema|
### <a name="github-triggers"></a>Trigger GitHub
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Apertura di un problema|Apertura di un problema|
|Quando si chiude un problema|Chiusura di un problema|
|Quando viene assegnato un problema|Viene assegnato un problema|


## <a name="create-a-connection-to-github"></a>Creare una connessione a GitHub
Per creare App logica con GitHub, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali GitHub|
Dopo aver creato la connessione, è possibile utilizzare per eseguire le azioni e attendere trigger descritto in questo articolo. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="reference-for-github"></a>Guida di riferimento per GitHub
Si applica alla versione: 1.0

## <a name="createissue"></a>CreateIssue
Creare un problema: crea un problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|repositoryOwner|stringa|Sì|percorso|Nessuno|Proprietario repository|
|repositoryName|stringa|Sì|percorso|Nessuno|Nome archivio|
|issueBasicDetails| |Sì|corpo|Nessuno|Dettagli del problema|

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


## <a name="issueopened"></a>IssueOpened
Apertura di un problema: apertura di un problema 

```GET: /trigger/issueOpened``` 

Non sono presenti parametri per la chiamata
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


## <a name="issueclosed"></a>IssueClosed
Quando si chiude un problema: un problema viene chiuso 

```GET: /trigger/issueClosed``` 

Non sono presenti parametri per la chiamata
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


## <a name="issueassigned"></a>IssueAssigned
Quando viene assegnato un problema: viene assegnato un problema 

```GET: /trigger/issueAssigned``` 

Non sono presenti parametri per la chiamata
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|titolo|stringa|Sì |
|corpo|stringa|Sì |
|assegnatario|stringa|Sì |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|titolo|stringa|Sì |
|corpo|stringa|Sì |
|assegnatario|stringa|Sì |
|numero|stringa|No |
|stato|stringa|No |
|created_at|stringa|No |
|repository_url|stringa|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)