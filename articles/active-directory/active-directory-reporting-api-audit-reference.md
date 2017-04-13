<properties
    pageTitle="Controllo di Active Directory Azure riferimento all'API | Microsoft Azure"
    description="Come iniziare con l'API di controllo di Azure Active Directory"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Controllo di Active Directory Azure riferimento all'API

In questo argomento fa parte di un insieme di argomenti su Azure Active Directory API di segnalazione.  
Reporting di Azure Active Directory offre un'API che consente di accedere ai dati di controllo Usa codice o gli strumenti correlati.
L'ambito di questo argomento è necessario fornirgli informazioni di riferimento sulle **API di controllo**.

Vedere:

- [Log di controllo](active-directory-reporting-azure-portal.md#audit-logs) per ulteriori informazioni
- [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md) per ulteriori informazioni sull'API dei report.

Per eventuali domande, problemi o commenti e suggerimenti, contattare il [Report consente AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

- Utenti del ruolo di amministratore di sicurezza o lettore di sicurezza

- Amministratori globali

- Qualsiasi app che è autorizzato ad accedere all'API (autorizzazione app può essere imposta solo in base alle autorizzazioni di amministratore globale)



## <a name="prerequisites"></a>Prerequisiti

Per accedere a questo rapporto tramite l'API dei report, è necessario avere:

- Un [Azure Active Directory gratuita o migliore edition](active-directory-editions.md)

- Completare i [Prerequisiti per accedere a Azure AD API di segnalazione](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Accesso alle API

Accedere questa API tramite [Esplora grafico](https://graphexplorer2.cloudapp.net) o a livello di programmazione, ad esempio tramite PowerShell. Affinché PowerShell interpretare correttamente la sintassi di filtro OData utilizzata per le chiamate AAD grafico resto, è necessario usare l'apice inverso (nello specifico: accento grave) carattere per il carattere $ "escape". Il carattere apice inverso viene utilizzato come [carattere di escape PowerShell](https://technet.microsoft.com/library/hh847755.aspx), che consente di PowerShell per eseguire un'interpretazione letterale del carattere $ ed evitare di confondere come nome di una variabile di PowerShell (ad esempio: $filter).

Esplora aree di grafico è attivo di questo argomento. Per un esempio di PowerShell, vedere questo [script di PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>API Endpoint


È possibile accedere a questa API utilizzando URI seguente:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Non esiste alcun limite al numero di record restituiti dall'API di controllo di Azure Active Directory (usando OData impaginazione).
I limiti di conservazione dei dati dei report, vedere [I criteri di conservazione dei report](active-directory-reporting-retention.md).

La chiamata restituisce i dati in batch. Ogni batch con un massimo di 1000 record.  
Per ottenere il batch di record successivo, utilizzare il collegamento successivo. È possibile ottenere le informazioni skiptoken dal primo set di record restituiti. Token salta verrà alla fine del risultato impostato.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtri supportati

È possibile limitare il numero di record restituiti da un'API chiamare nel modulo di un filtro.  
Per accesso API relativi dati, sono supportati i filtri seguenti:

- **$top =\<numero di record da restituire\> ** - per limitare il numero di record restituiti. Si tratta di un'operazione complessa. Utilizzare questo filtro non se si desidera tornare migliaia di oggetti.     
- **$filter =\<estratto filtro\> ** - per specificare in base ai campi filtro supportata, il tipo di record che interessano



## <a name="supported-filter-fields-and-operators"></a>Campi filtro supportate e operatori

Per specificare il tipo di record che è rilevante, è possibile creare un'istruzione di filtro che può contenere uno o una combinazione di campi filtro seguenti:

- [activityDate](#activitydate) - definisce una data o l'intervallo di date
- [activityType](#activitytype) - definisce il tipo di un'attività
- [attività](#activity) - definire l'attività come stringa  
- [Nome attore /](#actorname) - definisce l'attore nel modulo del nome dell'attore
- [attore/objectid](#actorobjectid) - definisce l'attore nel modulo dell'ID dell'attore   
- [attore/upn](#actorupn) - definisce l'attore nel modulo del nome dell'entità utente (UPN) dell'attore 
- [destinazione/nome](#targetname) - definisce la destinazione nel modulo del nome dell'attore
- [destinazione/objectid](#targetobjectid) - definisce la destinazione nel modulo dell'ID della destinazione  
- [destinazione/upn](#targetupn) - definisce l'attore nel modulo del nome dell'entità utente (UPN) dell'attore   




----------

### <a name="activitydate"></a>activityDate

**Gli operatori supportati**: eq, grandi, le, gt, lt

**Esempio**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Note**:

DateTime deve essere in formato UTC

----------

### <a name="activitytype"></a>activityType

**Gli operatori supportati**: eq

**Esempio**:

    $filter=activityType eq 'User'  

**Note**:

maiuscole/minuscole

----------

### <a name="activity"></a>attività

**Gli operatori supportati**: eq, contiene, startsWith

**Esempio**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Note**:

maiuscole/minuscole

----------

### <a name="actorname"></a>Nome attore /

**Gli operatori supportati**: eq, contiene, startsWith

**Esempio**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Note**:

distinzione tra maiuscole e

    

----------
### <a name="actorobjectid"></a>attore/objectId

**Gli operatori supportati**: eq

**Esempio**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>Nome/destinazione

**Gli operatori supportati**: eq, contiene, startsWith

**Esempio**:

    $filter=targets/any(t: t/name eq 'some name')   

**Note**:

Distinzione tra maiuscole e

----------

### <a name="targetupn"></a>destinazione/upn

**Gli operatori supportati**: eq, startsWith

**Esempio**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Note**:

- Distinzione tra maiuscole e
- È necessario aggiungere spazio dei nomi completo durante la ricerca Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>destinazione/objectId

**Gli operatori supportati**: eq

**Esempio**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>attore/upn

**Gli operatori supportati**: eq, startsWith

**Esempio**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Note**:

- Distinzione tra maiuscole e 
- È necessario aggiungere spazio dei nomi completo durante la ricerca Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Passaggi successivi

- Si vuole vedere esempi per le attività di sistema filtrato? Vedere [esempi di API di controllo di Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Si desidera ottenere ulteriori informazioni su Azure AD API di segnalazione? Vedere [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md).