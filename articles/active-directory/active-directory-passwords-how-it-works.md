<properties
    pageTitle="Come funziona: gestione delle Password Azure Active Directory | Microsoft Azure"
    description="Informazioni sui diversi componenti di Azure Active Directory gestione delle Password, inclusi nel punto in cui gli utenti registrare, reimpostare e cambiano le password e in cui gli amministratori di configurano, creare un report sul e attivare la gestione delle password di Active Directory locale."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="how-password-management-works"></a>Funzionamento della gestione delle Password

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Gestione delle password di Azure Active Directory è costituita da diversi componenti logici che sono descritti di seguito.  Fare clic su ogni collegamento per altre informazioni su tale componente.

- [**Portale di configurazione della gestione della password**](#password-management-configuration-portal) , gli amministratori possono controllare diversi aspetti delle modalità di gestione di password in loro tenant passando alla scheda Configura loro della directory nel [Portale di gestione Azure](https://manage.windowsazure.com).
- [**Portale di registrazione utente**](#user-registration-portal) : utenti la registrazione per la reimpostazione tramite il portale web della password.
- [**Portale di reimpostazione Password utente**](#user-password-reset-portal) : gli utenti possono reimpostare la propria password utilizzando un numero di diversi problemi in base ai criteri di reimpostazione password controllata dall'amministratore
- [**Portale di modificare la Password utente**](#user-password-change-portal) : gli utenti possano cambiare la propria password in qualsiasi momento immettendo la vecchia password e selezionando una nuova password tramite il portale web
- [**I report di gestione delle password**](#password-management-reports) , gli amministratori possono visualizzare e analizzare l'attività di registrazione e reimpostare la password nel tenant di passare alla sezione "Attività report" della scheda "Report" loro della directory nel [Portale di gestione Azure](https://manage.windowsazure.com)
- [**Password Writeback componente di Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) : gli amministratori possono se lo si desidera attivare la caratteristica Writeback Password se l'installazione di Azure AD Connect per la gestione dei federato o password sincronizzate le password degli utenti dal cloud.

## <a name="password-management-configuration-portal"></a>Portale di configurazione della gestione della password
È possibile configurare i criteri di gestione delle Password per una directory specifica tramite il [Portale di gestione Azure](https://manage.windowsazure.com) , passare alla sezione **Criteri di reimpostazione Password utente** nella scheda **Configura** della directory.  Da questa pagina di configurazione, è possibile controllare molti aspetti delle modalità di gestione delle password parte dell'organizzazione, tra cui:

- Attivazione e disattivazione di reimpostazione della password per tutti gli utenti in una directory
- Impostare il numero dei problemi (uno o due) di un utente deve elaborata per reimpostare la password
- Impostazione di tipi specifici di problemi che si desidera abilitare per gli utenti dell'organizzazione delle opzioni seguenti:
 - Telefono cellulare (un codice di verifica tramite testo o una chiamata vocale)
 - Telefono ufficio (una chiamata VoIP)
 - Posta elettronica alternativo (un codice di verifica tramite posta elettronica)
 - Domande sulla protezione (autenticazione basata su knowledge)
- Impostazione del numero di domande un utente deve eseguire la registrazione per utilizzare il metodo autenticazione domande sicurezza (visibile solo se sono abilitate le domande di sicurezza)
- Impostazione del numero di domande un utente deve fornire durante la reimpostazione usare sicurezza domande metodo di autenticazione (visibile solo se sono abilitate le domande di sicurezza)
- Se si utilizza domande di sicurezza predefiniti, localizzati, che un utente può scegliere di utilizzare durante la registrazione per la password reimpostate (visibile solo se sono abilitate le domande di sicurezza)
- Definire le domande di sicurezza personalizzata che un utente può scegliere di utilizzare durante la registrazione per la password, reimpostare (visibile solo se sono abilitate le domande di sicurezza)
- Richiedere agli utenti di eseguire la registrazione per la reimpostazione quando accedono all'applicazione Pannello di accesso in [http://myapps.microsoft.com](http://myapps.microsoft.com)della password.
- Agli utenti che richiede di confermare nuovamente i dati registrati in precedenza dopo un numero di giorni che vanno configurabile hanno superato (visibili solo se è abilitata la registrazione imposta)
- Fornire un messaggio di posta elettronica dell'help desk personalizzato o un URL che verrà visualizzato agli utenti in caso di problemi di reimpostazione password
- Abilitare o disabilitare la funzionalità di Writeback di Password (quando Writeback di Password è stato distribuito con AAD connessione)
- Visualizzare lo stato dell'agente di Password Writeback (quando il Writeback di Password è stato distribuito con AAD connessione)
- Attivazione delle notifiche di posta elettronica agli utenti quando la propria password sono stati opportunamente reimpostati (disponibile nella sezione **notifiche** del [Portale di gestione Azure](https://manage.windowsazure.com))
- Se si Abilita notifiche tramite posta elettronica per gli amministratori quando altri amministratori reimpostare la propria password (disponibile nella sezione **notifiche** del [Portale di gestione Azure](https://manage.windowsazure.com)
- Personalizzazione la password dell'utente reimpostare portale e messaggi di posta elettronica con il logo e il nome dell'organizzazione la reimpostazione della password utilizzando il tenant di personalizzazione delle funzionalità di personalizzazione (disponibile nella sezione **Proprietà Directory** del [Portale di gestione Azure](https://manage.windowsazure.com)

Per ulteriori informazioni sulla configurazione di gestione delle Password all'interno dell'organizzazione, vedere [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portale di registrazione utente
Prima che gli utenti in grado di utilizzare la reimpostazione della password, è necessario aggiornarli degli account utente cloud con i dati di autenticazione corrette per assicurarsi che passano attraverso il numero di problemi di reimpostazione password definite dall'amministratore.  Gli amministratori possono inoltre definire queste informazioni di autenticazione per conto dell'utente con DirSync portali web Azure o Office / Azure AD Connect o Windows PowerShell.

Tuttavia, se invece si desidera utilizzare gli utenti registrare i propri dati, è possibile utilizzare una pagina web che gli utenti possono accedere a per fornire queste informazioni.  Questa pagina consente agli utenti di specificare le informazioni di autenticazione secondo la password reimpostare i criteri che sono stati abilitati all'interno dell'organizzazione.  Dopo la verifica, questi dati vengono archiviato in account utente cloud per essere utilizzato per il ripristino di account in un secondo momento. Ecco cosa la registrazione del portale aspetto:

  ![][001]

Per ulteriori informazioni, vedere [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md) e [procedure consigliate: gestione delle Password di Azure Active Directory](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portale di reimpostazione Password utente
Dopo aver attivato password self-service reimpostare, impostare i criteri di reimpostazione password self-service dell'organizzazione e garantire che gli utenti sono i dati dei contatti appropriati nella directory, gli utenti dell'organizzazione saranno in grado di reimpostare la propria password automaticamente da una pagina web che utilizza un account aziendale o dell'istituto di istruzione per l'accesso (ad esempio [portal.microsoftonline.com](https://portal.microsoftonline.com)). Nelle pagine, ad esempio questi, gli utenti vedranno un **non è possibile accedere all'account?** collegamento.

  ![][002]

Fare clic su questo collegamento verrà avviato il portale di reimpostazione password self-service.

  ![][003]

Per ulteriori informazioni su come gli utenti possono reimpostare la propria password, vedere [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portale di modificare la Password utente
Se gli utenti desiderano modificare la propria password, è possibile farlo tramite il portale di modificare la password in qualsiasi momento.  Gli utenti possono accedere al portale di Cambia password tramite la pagina del profilo comando oppure facendo clic sul collegamento "Modifica password" all'interno delle applicazioni di Office 365.  Nel caso alla scadono delle password, gli utenti verranno inoltre chiesto di modificarle automaticamente durante l'accesso.

  ![][004]

In entrambi i casi, se Writeback di Password è stato attivato e l'utente è federato o dalla sincronizzazione delle password, queste password modificate vengono scritte a di Active Directory locale. Ecco cosa la password modifica portale aspetto:

  ![][005]

Per ulteriori informazioni su come gli utenti possano cambiare la propria password di Active Directory locale, vedere [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Report di gestione delle password
Passando alla scheda **report** e la ricerca nella sezione **Log di attività** , verranno visualizzati due report di gestione delle Password: **reimpostazione della Password attività** e **attività di registrazione la reimpostazione della Password**.  Usa queste due report, è possibile ottenere una visualizzazione di utenti registrazione e l'utilizzo dell'organizzazione reimpostazione della password. Ecco l'aspetto questi report nel [Portale di gestione Azure](https://manage.windowsazure.com):

  ![][006]

Per ulteriori informazioni, vedere [ottenere informazioni approfondite: report di gestione di Azure Active Directory Password](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Componente di Writeback di password di Azure AD Connect
Se le password degli utenti dell'organizzazione derivano dall'ambiente locale (tramite la federazione o la sincronizzazione delle password), è possibile installare la versione più recente di Azure AD Connect per attivare l'aggiornamento tali password direttamente dal cloud.  Questo significa che quando gli utenti dimenticano o si desidera modificare la password di Active Directory, che può eseguire in modo direttamente dal web.  Ecco dove trovare Password Writeback nell'installazione guidata di Azure AD Connect:

  ![][007]

Per ulteriori informazioni su Azure AD Connect, vedere [Guida introduttiva: Azure AD Connect](active-directory-aadconnect.md). Per ulteriori informazioni sulla Password Writeback, vedere [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Collegamenti a password reimpostare documentazione
Di seguito sono disponibili collegamenti a tutte le pagine della documentazione di reimpostazione della Password di Azure Active Directory:

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - informazioni su come consentire agli utenti di reimpostare e cambiare le password cloud o locale
* [**Personalizza**](active-directory-passwords-customize.md) - informazioni su come personalizzare l'aspetto e l'aspetto e comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md) - informazioni su come distribuire rapidamente e gestire in modo efficiente password all'interno dell'organizzazione
* [**Ottenere informazioni approfondite**](active-directory-passwords-get-insights.md) - informazioni sulla funzionalità di creazione di report integrata
* [**Domande frequenti**](active-directory-passwords-faq.md) - risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md) - informazioni su come risolvere rapidamente i problemi con il servizio
* [**Altre informazioni**](active-directory-passwords-learn-more.md) - profondità passare direttamente ai dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
