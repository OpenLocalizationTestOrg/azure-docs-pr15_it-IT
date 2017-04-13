<properties
    pageTitle="Report attività di accesso di Azure Active Directory riferimento all'API | Microsoft Azure"
    description="Guida di riferimento per l'API di report di attività di accesso di Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Report attività di accesso di Azure Active Directory riferimento all'API


In questo argomento fa parte di un insieme di argomenti su Azure Active Directory API di segnalazione.  
Reporting di Azure Active Directory offre un'API che consente di accedere accesso attività dati del report tramite codice o gli strumenti correlati.
L'ambito di questo argomento è necessario fornirgli informazioni di riferimento l' **API di report attività in accesso**.

Vedere:

- [Attività di accesso](active-directory-reporting-azure-portal.md#sign-in-activities) per ulteriori informazioni
- [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md) per ulteriori informazioni sull'API dei report.

Per eventuali domande, problemi o commenti e suggerimenti, contattare il [Report consente AAD](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Chi può accedere ai dati dell'API?

- Utenti del ruolo di amministratore di sicurezza o lettore di sicurezza

- Amministratori globali

- Qualsiasi app che è autorizzato ad accedere all'API (autorizzazione app può essere imposta solo in base alle autorizzazioni di amministratore globale)



## <a name="prerequisites"></a>Prerequisiti

Per accedere a questo rapporto tramite l'API di creazione di report, è necessario disporre di:

- Un [Azure Active Directory Premium P1 o P2 edition](active-directory-editions.md)

- Completare i [Prerequisiti per accedere a Azure AD API di segnalazione](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Accesso alle API

Accedere questa API tramite [Esplora grafico](https://graphexplorer2.cloudapp.net) o a livello di programmazione, ad esempio tramite PowerShell. Affinché PowerShell interpretare correttamente la sintassi di filtro OData utilizzata per le chiamate AAD grafico resto, è necessario usare l'apice inverso (nello specifico: accento grave) carattere per il carattere $ "escape". Il carattere apice inverso viene utilizzato come [carattere di escape PowerShell](https://technet.microsoft.com/library/hh847755.aspx), che consente di PowerShell per eseguire un'interpretazione letterale del carattere $ ed evitare di confondere come nome di una variabile di PowerShell (ad esempio: $filter).

Esplora aree di grafico è attivo di questo argomento. Per un esempio di PowerShell, vedere questo [script di PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>API Endpoint

È possibile accedere a questa API utilizzando l'URI di base seguente:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



A causa del volume dei dati, questa API ha un limite di un milione i record restituiti. 

La chiamata restituisce i dati in batch. Ogni batch con un massimo di 1000 record.  
Per ottenere il batch di record successivo, utilizzare il collegamento successivo. È possibile ottenere le informazioni [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) dal primo set di record restituiti. Token salta verrà alla fine del risultato impostato.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtri supportati

È possibile limitare il numero di record restituiti da un'API chiamare nel modulo di un filtro.  
Per accesso API relativi dati, sono supportati i filtri seguenti:

- **$top =\<numero di record da restituire\> ** - per limitare il numero di record restituiti. Si tratta di un'operazione complessa. Utilizzare questo filtro non se si desidera tornare migliaia di oggetti.  
- **$filter =\<estratto filtro\> ** - per specificare in base ai campi filtro supportata, il tipo di record che interessano



## <a name="supported-filter-fields-and-operators"></a>Campi filtro supportate e operatori

Per specificare il tipo di record che è rilevante, è possibile creare un'istruzione di filtro che può contenere uno o una combinazione di campi filtro seguenti:

- [signinDateTime](#signindatetime) - definisce una data o l'intervallo di date

- [ID utente](#userid) - consente di definire uno specifico utente in base a ID. dell'utente

- [userPrincipalName](#userprincipalname) - consente di definire uno specifico utente in base a nome dell'entità utente dell'utente (UPN)

- [appId](#appid) - definisce una determinata app in base a ID dell'applicazione

- [appDisplayName](#appdisplayname) - definisce una determinata app in base a nome visualizzato dell'applicazione

- [loginStatus](#loginStatus) - definisce lo stato degli account di accesso (successo / errore)


> [AZURE.NOTE] Quando si utilizza Explorer grafico, è necessario utilizzare lettere maiuscole o minuscole per ogni lettera è i campi filtro.


Per restringere l'ambito dei dati restituiti, è possibile creare combinazioni dei filtri supportati e campi dei filtri. Ad esempio, l'istruzione seguente restituisce i primi 10 record tra 2016 1 ° luglio e 2016 6a luglio:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Gli operatori supportati**: eq, grandi, le, gt, lt

**Esempio**:

Con una data specifica

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



Utilizzo di un intervallo di date    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Note**:

Il parametro datetime deve essere in formato UTC 


----------

### <a name="userid"></a>ID utente

**Gli operatori supportati**: eq

**Esempio**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Note**:

Il valore dell'ID utente è un valore stringa



----------

### <a name="userprincipalname"></a>userPrincipalName

**Gli operatori supportati**: eq

**Esempio**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Note**:

Il valore dell'attributo userPrincipalName è un valore stringa

----------

### <a name="appid"></a>appId

**Gli operatori supportati**: eq

**Esempio**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Note**:

Il valore di appId è un valore stringa

----------


### <a name="appdisplayname"></a>appDisplayName

**Gli operatori supportati**: eq

**Esempio**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Note**:

Il valore di appDisplayName è un valore stringa

----------

### <a name="loginstatus"></a>loginStatus

**Gli operatori supportati**: eq

**Esempio**:

    $filter=loginStatus+eq+'1'  


**Note**:

Sono disponibili due opzioni per la loginStatus: 0 - 1 - errore positivo

----------



## <a name="next-steps"></a>Passaggi successivi

- Si vuole vedere esempi per le attività di accesso filtrate? Esaminare gli [esempi di report API attività di accesso di Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).

- Si desidera ottenere ulteriori informazioni su Azure AD API di segnalazione? Vedere [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md).