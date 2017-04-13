<properties
  pageTitle="Gestione dell'accesso alle App utilizzando Azure Active Directory |  Microsoft Azure"
  description="Descrive come Azure Active Directory consente alle organizzazioni di specificare le App in cui ogni utente abbia accesso."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Gestione dell'accesso alle App

Gestione degli accessi in corso, valutazione utilizzo e creazione di report continuano a essere un'operazione impegnativa dopo un'app è integrata con il sistema di identità dell'organizzazione. In molti casi, è necessario eseguire un ruolo attivo in corso nella gestione dell'accesso alle App gli amministratori IT o l'helpdesk. In alcuni casi assegnazione viene eseguita da un team IT generale o di reparto. Spesso, la decisione di assegnazione deve essere delegata al dirigente aziendale, che richiede l'approvazione prima di IT rendono l'assegnazione.  Altre organizzazioni investono in integrazione con un automatizzato identità e accessi gestione sistema esistente, ad esempio controllo dell'accesso basato sui ruoli (RBAC) o controllo dell'accesso basato su attributi (ABAC). L'integrazione e sviluppo regole sono in genere in specializzata e costosa. Monitoraggio o creazione di report in entrambi gli approcci di gestione è il proprio investimento separato costosa e complessa.

## <a name="how-does-azure-active-directory-help"></a>Informazioni Azure Active Directory

 Azure Active Directory supporta la gestione di ampio accesso per le applicazioni configurate, consentendo alle organizzazioni di ottenere facilmente i criteri di accesso appropriata compreso automatica, basato su attributi assegnazione (scenari ABAC o RBAC) mediante la delega e inclusi gestione di amministratore. Con Azure Active Directory può facilmente ottenere criteri complessi, la combinazione di più modelli di gestione per una singola applicazione e possono anche essere riutilizzati regole di gestione applicazioni con i gruppi di destinatari stesso.

 - [Aggiunta di nuovi elementi o le applicazioni esistenti](active-directory-sso-integrate-saas-apps.md)


 Assegnazione di applicazioni Azure Active Directory è incentrata su due modalità di assegnazione primaria:

- **Singola assegnazione** Un amministratore IT con autorizzazioni di amministratore globale directory selezionare singoli account utente e concedere loro l'accesso all'applicazione.
- **Assegnazione basate su gruppo (pagata Azure Active Directory solo)** Un amministratore IT con autorizzazioni di amministratore globale di directory è possibile assegnare un gruppo all'applicazione. Se sono i membri del gruppo al momento che tentano di accedere all'applicazione determina l'accesso degli utenti specifici. In altre parole, un amministratore può creare in modo efficace una regola di assegnazione con l'indicazione "tutti i membri del gruppo assegnato ha accesso all'applicazione". Utilizzare questa opzione di assegnazione, gli amministratori possono trarre vantaggio da una qualsiasi delle opzioni di gestione gruppo Azure Active Directory, inclusa la [basato su attributi gruppi dinamici](active-directory-accessmanagement-manage-groups.md), i gruppi di sistema esterno (ad esempio, Active Directory locale o lavorativo) o i gruppi gestite dall'amministratore o gestito a automatico ammissione. Un singolo gruppo può essere facilmente assegnato alle app di più, verificare che le applicazioni con affinità assegnazione possono condividere le regole di assegnazione, la riduzione della complessità di gestione globale. Si noti che gruppo nidificato appartenenze non sono supportate per l'assegnazione al gruppo in base alle applicazioni al momento.

Usa queste modalità di due assegnazione, gli amministratori possono ottenere qualsiasi approccio di gestione delle assegnazioni opportuno.

Con Azure Active Directory, l'utilizzo e la segnalazione di un'assegnazione è completamente integrato, che consente agli amministratori facilmente segnalazione dello stato delle assegnazioni, gli errori di assegnazione e l'uso pari.

## <a name="complex-application-assignment-with-azure-ad"></a>Assegnazione di applicazioni complesse con Azure Active Directory

Valutare la possibilità di un'applicazione come Salesforce. In molte organizzazioni, Salesforce usato prevalentemente organizzazioni delle vendite e marketing. Spesso, i membri del team di marketing dispongano di privilegi elevati accesso a Salesforce, mentre i membri del team di vendita hanno accesso limitato. In molti casi, un ampio popolazione gli information worker accesso limitato dell'applicazione. Eccezioni a queste regole complicare il tutto. È spesso potere dei team leadership marketing o delle vendite per concedere l'accesso degli utenti o modificare i ruoli indipendente le regole di tipo generiche.

Con Azure Active Directory, applicazioni, ad esempio Salesforce può essere preconfigurato per single sign-on (SSO) e il provisioning automatico. Dopo l'applicazione è configurata, un amministratore può eseguire le operazioni di copia unica per creare e assegnare gruppi appropriati. In questo esempio, un amministratore può eseguire le assegnazioni seguenti:

- [Gruppi dinamici](active-directory-accessmanagement-manage-groups.md) possono essere definiti per rappresentare automaticamente tutti i membri dei team delle vendite e marketing utilizzando gli attributi come reparto o ruolo:

    - Tutti i membri dei gruppi di marketing da assegnate al ruolo "marketing" in Salesforce

    - Tutti i membri del team di vendita gruppi vengono assegnati a ruolo "vendito" in Salesforce. È inoltre possibile Impossibile utilizzare più gruppi che rappresentano il team di vendita internazionali assegnato ai ruoli Salesforce diversi.

- Per abilitare il meccanismo, è Impossibile creare un gruppo modalità self-service per ogni ruolo. Ad esempio, il gruppo "Salesforce marketing eccezione" può essere creato a livello di gruppo modalità self-service. Gruppo può essere assegnato al ruolo marketing Salesforce e la leadership marketing può essere eseguita proprietari. I membri del team di marketing leadership Impossibile aggiungere o rimuovere utenti, impostato un criterio di join, o anche approvare o respingere le richieste di singoli utenti a partecipare. Questa è supportata tramite un appropriato esperienza degli information worker che non richiede formazione specializzata per i proprietari o membri.

In questo caso, tutti gli utenti assegnati da eseguire il provisioning automatico di Salesforce, quando vengono aggiunti a diversi gruppi che loro assegnazione di ruolo verranno aggiornata nel Salesforce. Gli utenti sarebbero in grado di rilevare e accedere Salesforce tramite il pannello di accesso dell'applicazione di Microsoft, client web di Office, o persino passando alla pagina di accesso corrispondente Salesforce dell'organizzazione. Gli amministratori sarebbero in grado di facilmente visualizzare lo stato di utilizzo e sulle assegnazioni tramite reporting Azure Active Directory.

Gli amministratori possono utilizzare [access condizionale Azure Active Directory](active-directory-conditional-access.md) per impostare i criteri di accesso per ruoli specifici. Questi criteri possono includere se è consentito l'accesso all'esterno di ambiente aziendale e requisiti anche l'autenticazione a più fattori o un dispositivo per ottenere accesso in vari modi.

## <a name="how-can-i-get-started"></a>Come è possibile iniziare?

Prima di tutto, se non si usa già Azure Active Directory e si è un amministratore IT:

 - [Provalo!](https://azure.microsoft.com/trial/get-started-active-directory/) -è possibile iscriversi per un 30 giorni di valutazione e distribuire la soluzione cloud prima in 5 minuti attraverso il collegamento

Azure Active Directory che abilitare la condivisione di account includono:

- [Assegnazione al gruppo](active-directory-accessmanagement-self-service-group-management.md)
- Aggiunta di applicazioni di Azure Active Directory
- Guida introduttiva di assegnazione
- Assegnazione di applicazioni domande frequenti
- [Dashboard/report sull'uso di App](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Dove è possibile ulteriori?

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Protezione di applicazioni con accesso condizionale](active-directory-conditional-access.md)
- [Gestione/SSAA gruppo modalità self-service](active-directory-accessmanagement-self-service-group-management.md)
