<properties
pageTitle="Project Online | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Project Online è una soluzione online flessibile per la gestione del portfolio di progetti (PPM) e quotidiane da Microsoft. Recapitato mediante Office 365, Project Online consente alle organizzazioni di iniziare subito a utilizzare la funzionalità di gestione progetto potenti per pianificare, priorità e gestire gli investimenti portfolio di progetti e progetti, ovvero praticamente ovunque in qualsiasi dispositivo."
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

# <a name="get-started-with-the-projectonline-connector"></a>Iniziare a utilizzare il connettore di Project Online

Project Online è una soluzione online flessibile per la gestione del portfolio di progetti (PPM) e quotidiane da Microsoft. Recapitato mediante Office 365, Project Online consente alle organizzazioni di iniziare subito a utilizzare la funzionalità di gestione progetto potenti per pianificare, priorità e gestire gli investimenti portfolio di progetti e progetti, ovvero praticamente ovunque in qualsiasi dispositivo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

Connettore Project Online può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 Il connettore di Project Online è le seguenti azioni e/o trigger disponibili:

### <a name="projectonline-actions"></a>Azioni di Project Online
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Elenca i progetti nel sito di project online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crea un nuovo progetto nel sito di project online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crea una nuova attività di progetto|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crea un risorse dell'organizzazione nel sito di project online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Vengono elencate le attività pubblicata in un progetto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Estrae un progetto nel sito|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Archiviare e pubblicare ed esistente nel sito progetto|
### <a name="projectonline-triggers"></a>Trigger di Project Online
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando viene creato un nuovo progetto|Attiva un flusso di ogni volta che viene creato un nuovo progetto|
|Quando si crea una nuova risorsa|Attiva un nuovo flusso di quando si crea una nuova risorsa|
|Quando si crea una nuova attività|Attiva un flusso quando si crea una nuova attività|


## <a name="create-a-connection-to-projectonline"></a>Creare una connessione a Project Online
Per creare App logica con Project Online, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali di Project Online|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="reference-for-projectonline"></a>Guida di riferimento per Project Online
Si applica alla versione: 1.0

## <a name="onnewproject"></a>OnNewProject
Quando viene creato un nuovo progetto: genera un flusso di ogni volta che viene creato un nuovo progetto 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Quando si crea una nuova risorsa: attiva un nuovo flusso di quando si crea una nuova risorsa 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Quando si crea una nuova attività: attiva un flusso quando si crea una nuova attività 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Elenco progetti: Elenca i progetti nel sito di project online 

```GET: /_api/ProjectServer/Projects``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Crea nuovo progetto: crea un nuovo progetto nel sito di project online 

```POST: /_api/ProjectServer/Projects``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |Sì|corpo|Nessuno|Nuovo progetto da creare|

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


## <a name="createtask"></a>CreateTask
Crea nuova attività: crea una nuova attività di progetto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|stringa|Sì|percorso|Nessuno|ID univoco del progetto per aggiungere l'attività|
|attività| |Sì|corpo|Nessuno|Nuova attività da aggiungere al progetto|

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


## <a name="createresource"></a>CreateResource
Creare una nuova risorsa: crea un risorse dell'organizzazione nel sito di project online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|risorsa| |Sì|corpo|Nessuno|Nuova risorsa dell'organizzazione da aggiungere al progetto|

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


## <a name="listtasks"></a>ListTasks
Vengono elencate le attività: vengono elencate le attività pubblicata in un progetto 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|stringa|Sì|percorso|Nessuno|ID univoco del progetto per recuperare le attività|

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


## <a name="checkoutproject"></a>CheckoutProject
Estrarre un progetto: estrae un progetto nel sito 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|stringa|Sì|percorso|Nessuno|ID univoco del progetto per aggiungere l'attività|

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


## <a name="publishproject"></a>PublishProject
Archiviazione e pubblicazione di progetto: archiviare e pubblicare ed esistente nel sito progetto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|stringa|Sì|query|Nessuno|Principali url del sito del sito di progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|stringa|Sì|percorso|Nessuno|ID univoco del progetto da archiviare|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="triggerproject"></a>TriggerProject


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ProjectStartDate|stringa|No |
|ProjectFinishDate|stringa|No |
|ProjectCreatedDate|stringa|No |
|ProjectId|stringa|No |
|ProjectModifiedDate|stringa|No |
|ProjectType|numero intero|No |
|NomeProgetto|stringa|No |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="triggerresource"></a>TriggerResource


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ResourceId|stringa|No |
|ResourceBaseCalendar|stringa|No |
|ResourceBookingType|numero intero|No |
|ResourceCanLevel|valore booleano|No |
|ResourceCostPerUse|numero|No |
|ResourceCreatedDate|stringa|No |
|ResourceEarliestAvailableFrom|stringa|No |
|ResourceEmail|stringa|No |
|ResourceInitials|stringa|No |
|ResourceIsActive|valore booleano|No |
|ResourceIsGeneric|valore booleano|No |
|ResourceLatestAvailableTo|stringa|No |
|ResourceModifiedDate|stringa|No |
|ResourceName|stringa|No |
|ResourceStatsuName|stringa|No |
|Tipo di risorsa|numero intero|No |
|TypeDescription|stringa|No |
|TypeName|stringa|No |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="triggertask"></a>TriggerTask


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ProjectId|stringa|No |
|ID attività|stringa|No |
|NomeProgetto|stringa|No |
|Nome operazione|stringa|No |
|TaskCreatedDate|stringa|No |
|TaskModifieddate|stringa|No |
|TaskStartDate|stringa|No |
|TaskFinishDate|stringa|No |
|TaskPriority|numero intero|No |
|TaskIsActive|valore booleano|No |



### <a name="newproject"></a>NewProject


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|stringa|Sì |
|Descrizione|stringa|No |
|Inizio|stringa|No |



### <a name="newreource"></a>NewReource


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|stringa|Sì |
|IsBudget|valore booleano|No |
|IsGeneric|valore booleano|No |
|IsInactive|valore booleano|No |



### <a name="project"></a>Progetto


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ApprovedStart|stringa|No |
|ApprovedEnd|stringa|No |
|CheckedOutDate|stringa|No |
|CheckOutDescription|stringa|No |
|ID di estrazione|stringa|No |
|Valori CreatedDate|stringa|No |
|ID|stringa|No |
|IsCheckedOut|valore booleano|No |
|LastPublishedDate|stringa|No |
|LastSavedDate|stringa|No |
|OptimizerDecision|numero intero|No |
|PlannerDecision|numero intero|No |
|ProjectType|numero intero|No |
|Nome|stringa|No |
|WinprojVersion|stringa|No |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="newtask"></a>Nuova attività


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|parametri|non è definito|Sì |



### <a name="taskparameters"></a>TaskParameters


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|stringa|Sì |
|Note|stringa|No |
|Inizio|stringa|No |
|Durata|stringa|No |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|CanLevel|valore booleano|No |
|Codice|stringa|No |
|CostAccrual|numero intero|No |
|CostCenter|stringa|No |
|Creato|stringa|No |
|DefaultBookingType|numero intero|No |
|Posta elettronica|stringa|No |
|ExternalId|stringa|No |
|Gruppo|stringa|No |
|HireDate|stringa|No |
|ID|stringa|No |
|Iniziali|stringa|No |
|IsActive|valore booleano|No |
|IsBudget|valore booleano|No |
|IsCheckedOut|valore booleano|No |
|IsGeneric|valore booleano|No |
|IsTeam|valore booleano|No |
|MaterialLabel|stringa|No |
|Modificato|stringa|No |
|Nome|stringa|No |
|Fonemi|stringa|No |
|Tipo di risorsa|numero intero|No |
|TerminationDate|stringa|No |



### <a name="taskswrapper"></a>TasksWrapper


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|valore|in forma di matrice|No |



### <a name="task"></a>Attività


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Creato|stringa|No |
|Modificato|stringa|No |
|Inizio|stringa|No |
|Fine|stringa|No |
|Nome|stringa|No |
|ID|stringa|No |
|Priorità|numero intero|No |
|Percentuale di completamento|numero intero|No |
|Note|stringa|No |
|Contatto|stringa|No |


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)