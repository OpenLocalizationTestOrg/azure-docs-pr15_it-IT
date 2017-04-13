<properties
    pageTitle="La condivisione di account Azure Active Directory utilizzando |  Microsoft Azure"
    description="Descrive come Azure Active Directory consente alle organizzazioni di condividere in modo sicuro gli account per locale applicazioni e servizi cloud consumer."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Condividere account con Azure Active Directory

## <a name="overview"></a>Panoramica
Può succedere che le organizzazioni devono usare un nome e una password per più utenti. Si verifica in genere in due casi:

- Quando si accede applicazioni che richiedono un account di accesso univoco e una password per ogni utente, se App locale o consumer servizi cloud (ad esempio un account di social network aziendale).
- Quando si creano ambienti multiutente. Si dispone di un account locale che dispone di privilegi elevati e può essere utilizzato per principali attività di configurazione, amministrazione e ripristino (ad esempio, l'account locali "amministratore globale" per Office 365) oppure con l'account radice in Salesforce.

In genere, questi account verrebbero condivise per distribuire le credenziali (nome utente e password) a destra persone o archiviarli in un percorso condiviso accessibile più agenti attendibili.

Modello di condivisione tradizionale è numerosi svantaggi:

- Consentire l'accesso a nuove applicazioni richiede di distribuire le eventuali credenziali a tutti gli utenti che l'esigenza di accedervi.
- Ogni applicazione condivisa può richiedere univoco le proprie credenziali condivise, richiedere agli utenti di memorizzare più set di credenziali. Quando gli utenti devono ricordare molte credenziali, il rischio aumenta che verranno ricorrere a rischiose consigliate. (ad esempio, annotare le password).
- Non è possibile stabilire chi ha accesso a un'applicazione.
- Non è possibile stabilire chi ha *accesso* un'applicazione.
- Quando è necessario rimuovere l'accesso a un'applicazione, è necessario aggiornare le credenziali e distribuire nuovamente a tutti gli utenti che richiede l'accesso a tale applicazione.

## <a name="azure-active-directory-account-sharing"></a>Account la condivisione di Azure Active Directory

Azure Active Directory fornisce un approccio nuovo all'utilizzo di account condivisi che elimina questi inconvenienti.

L'amministratore di Azure Active Directory consente di configurare le applicazioni di un utente può accedere mediante il pannello di accesso e scegliendo il tipo di single sign-on più adatto per tale applicazione. Uno di questi tipi di *basate su password single sign - in*, consente di Azure AD agire come un tipo di "gestore" durante il processo di iscrizione per tale applicazione.

Effettuare l'accesso agli utenti una volta con il proprio account aziendale. Questo è lo stesso account che utilizzano regolarmente per accedere a desktop o messaggio di posta elettronica. Possono individuare e accedere a solo le applicazioni che sono state assegnate a. Con gli account condivisi, in questo elenco di applicazioni possibile includere qualsiasi numero di credenziali condivise. L'utente finale non è necessario ricordare o scrivere i vari account che utilizzano.

Gli account condivisi non solo aumentano la supervisione e migliorare la facilità di utilizzo, sono anche per migliorare la protezione. Gli utenti con autorizzazioni di utilizzare le credenziali non vengono visualizzate le password condivisa ma piuttosto ottengono le autorizzazioni per utilizzarla come parte di un flusso di autenticazione orchestrato. Inoltre, con alcune applicazioni di Single Sign-on password, è possibile disporre di Azure Active Directory periodicamente attivazione (aggiornamento) la password utilizzando le password complesse di grandi dimensioni, aumentare la sicurezza di account. L'amministratore può facilmente concedere o revocare l'accesso a un'applicazione e anche sapere chi ha accesso all'account e ha avuto accesso in passato.

Azure Active Directory supporta gli account condivisi per qualsiasi azienda mobilità famiglia di prodotti EMS (), Premium o base utenti autorizzati, in tutti i tipi di password singola accedere alle applicazioni. È possibile condividere gli account per le migliaia di applicazioni pre-integrate nella raccolta di applicazione e aggiungere l'applicazione di autenticazione password con [App personalizzate Single Sign-on](active-directory-sso-integrate-saas-apps.md).

Azure Active Directory che abilitare la condivisione di account includono:

- [Password il single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Agente password single sign-on
- [Assegnazione al gruppo](active-directory-accessmanagement-self-service-group-management.md)
- App Password personalizzata
- [Dashboard/report sull'uso di App](active-directory-passwords-get-insights.md)
- Portali di accesso degli utenti finali
- [Proxy di App](active-directory-application-proxy-get-started.md)
- [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Condivisione di un account
Per utilizzare Azure Active Directory per condividere un account che è necessario:

- Aggiungere un'applicazione [raccolta app](https://azure.microsoft.com/marketplace/active-directory/) o [applicazione personalizzata](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurare l'applicazione per la password, Single Sign-On (SSO)
- Utilizzare [gruppo in base a un'assegnazione](active-directory-accessmanagement-group-saasapps.md) e selezionare l'opzione per immettere le credenziali condivisa
- Facoltativo: in alcune applicazioni, ad esempio Facebook, Twitter o LinkedIn, è possibile attivare l'opzione per [password automatizzato di Azure Active Directory del](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

È anche possibile sfruttare l'account condiviso più sicura con l'autenticazione a più fattori (MFA) (per altre informazioni sulla [protezione delle applicazioni con Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started.md)) e possono delegare la possibilità di gestire chi ha accesso all'applicazione utilizzando Gestione gruppo [Self-Service di Azure Active Directory](active-directory-accessmanagement-self-service-group-management.md) .

## <a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Protezione di applicazioni con accesso condizionale](active-directory-conditional-access.md)
- [Gestione/SSAA gruppo modalità self-service](active-directory-accessmanagement-self-service-group-management.md)
