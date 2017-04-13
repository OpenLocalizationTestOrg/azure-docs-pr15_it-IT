<properties
    pageTitle="Azure Active Directory e applicazioni: guidare gli sviluppatori | Microsoft Azure"
    description="Scrittura per i professionisti IT, in questo articolo vengono fornite le linee per l'integrazione di applicazioni Azure con Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure Active Directory e applicazioni: sviluppare applicazioni line-of-business

Questa guida viene fornita una panoramica di sviluppo di applicazioni (Line) line-of-business per Azure Active Directory (AD). I destinatari sono gli amministratori globali di Active Directory/Office 365.

## <a name="overview"></a>Panoramica

Creazione di applicazioni integrate con Azure Active Directory consente agli utenti dell'organizzazione single sign-on con Office 365. Se l'applicazione in consente di Azure Active Directory di controllare il criterio di autenticazione per l'applicazione. Per ulteriori informazioni sulle condizionale access e su come proteggere App con l'autenticazione a più fattori (MFA) vedere [regole di accesso di configurazione](active-directory-conditional-access-azuread-connected-apps.md).

Registrare l'applicazione di utilizzare Azure Active Directory. Registrazione dell'applicazione significa che gli sviluppatori possono utilizzare Azure Active Directory per autenticare gli utenti e richiedere l'accesso alle risorse di utente, ad esempio posta elettronica, calendario e i documenti.

Dei membri della directory (non ospiti) è possibile registrare un'applicazione, nota anche come *si crea un oggetto applicazione*.

Registrazione di un'applicazione consente agli utenti di eseguire le operazioni seguenti:

- Ottenere un'identità per l'applicazione riconosce Azure Active Directory
- Ottenere uno o più informazioni riservate/tasti che l'applicazione può utilizzare per l'autenticazione per Active Directory
- Personalizzare l'applicazione nel portale di Azure con un nome personalizzato, il logo e così via.
- Applicare la funzionalità di autorizzazione Azure Active Directory a app, tra cui:
  - Controllo dell'accesso basato sui ruoli (RBAC)
  - Azure Active Directory come server autorizzazione oAuth (secure un'API esposta dall'applicazione)

- Dichiarare autorizzazioni necessarie per l'applicazione alla funzione come previsto, tra cui:-le autorizzazioni dell'App (solo amministratori globali). Ad esempio: appartenenza ai ruoli in un'altra appartenenza di Azure Active Directory applicazione o un ruolo rispetto a una risorsa Azure, gruppo risorse o l'abbonamento - delegati (qualsiasi utente). Ad esempio: Azure Active Directory, accesso e del profilo di lettura


> [AZURE.NOTE]Per impostazione predefinita, tutti i membri possono registrare un'applicazione. Per informazioni su come limitare le autorizzazioni per la registrazione delle applicazioni a specifici membri, vedere [come applicazioni vengono aggiunti all'Azure Active Directory](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Ecco cosa, l'amministratore globale, occorre fare per consentire agli sviluppatori di verificare le applicazioni di produzione:

- Configurare regole di accesso (access criteri/MFA)
- Configurare app per richiedere l'assegnazione di utenti e assegnare agli utenti
- Eliminare l'esperienza di consenso utente predefinito

## <a name="configure-access-rules"></a>Configurare regole di accesso

Configurare regole di accesso per ogni applicazione alle App SaaS. Ad esempio, è possibile richiedere MFA o consentire l'accesso agli utenti unicamente su reti attendibili. I dettagli sono disponibili nel documento [le regole di accesso di configurazione](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurare app per richiedere l'assegnazione di utenti e assegnare agli utenti

Per impostazione predefinita, gli utenti possono accedere applicazioni che non dispongono di. Tuttavia, se i ruoli esposti dall'applicazione o se si desidera che venga visualizzato nel Pannello di accesso dell'utente, è necessario chiedere assegnazione utente.

[Richiesta di assegnazione utente](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Se si è un server di sottoscrizione di Azure Active Directory Premium o Enterprise mobilità famiglia di prodotti (EMS), è consigliabile con i gruppi. Assegnazione di gruppi per l'applicazione consente di delegare la gestione di accesso costante al proprietario del gruppo. È possibile creare il gruppo oppure richiedere assistenza responsabile dell'organizzazione per creare il gruppo utilizzando la funzionalità di gestione di gruppo.

[Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md)  
[Assegnazione di gruppi a un'applicazione](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Eliminare il consenso dell'utente

Per impostazione predefinita, ogni utente attraversa un'esperienza di consenso effettuare l'accesso. L'esperienza di consenso, richiedere agli utenti di concedere le autorizzazioni per un'applicazione, può essere aggiunto per gli utenti non hanno familiarità con tali decisioni.

Per semplificare l'esperienza utente per applicazioni che si ritiene attendibile, acconsentire all'applicazione per conto dell'organizzazione.

Per ulteriori informazioni sull'ambiente di consenso in Azure e il consenso dell'utente, vedere [Le applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Articoli correlati

- [Abilitare l'accesso remoto sicuro alle applicazioni locali con Azure Active Directory applicazione Proxy](active-directory-application-proxy-get-started.md)
- [Anteprima di Azure accesso condizionato per le applicazioni SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Gestione dell'accesso alle App con Azure Active Directory](active-directory-managing-access-to-apps.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
