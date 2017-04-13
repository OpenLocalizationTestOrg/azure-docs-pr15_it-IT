<properties
   pageTitle="Cenni preliminari sulla protezione di gestione delle identità Azure | Microsoft Azure"
   description=" Microsoft access e identità di gestione soluzioni Guida IT proteggere l'accesso alle applicazioni e risorse attraverso il centro di dati aziendale e nel cloud, per consentire ulteriori livelli di convalida, ad esempio l'autenticazione a più fattori e criteri di accesso condizionale. In questo articolo viene fornita una panoramica delle principali funzionalità di sicurezza di Azure agevolare la gestione delle identità. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Cenni preliminari sulla protezione di gestione delle identità Azure

Microsoft access e identità di gestione soluzioni Guida IT proteggere l'accesso alle applicazioni e risorse attraverso il centro di dati aziendale e nel cloud, per consentire ulteriori livelli di convalida, ad esempio l'autenticazione a più fattori e criteri di accesso condizionale. Monitoraggio attività sospetto tramite protezione avanzata dei report, il controllo e gli avvisi consente di ridurre i potenziali problemi di sicurezza. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) include servizio single sign-on a migliaia di cloud (SaaS) App e accedere alle App web di eseguire in locale.

Vantaggi di sicurezza di Azure Active Directory (AD) consentono di:

- Creare e gestire un'unica identità per ogni utente nella propria organizzazione ibrida, sincronizzazione utenti, gruppi e i dispositivi
- Fornire l'accesso single sign-on alle applicazioni inclusi migliaia di App SaaS pre-integrate
- Attivare l'accesso applicazione attivando basate sulle regole di autenticazione a più fattori per entrambi locali e cloud applicazioni
- Effettuare il provisioning di accesso remoto protetto per le applicazioni web locale tramite Proxy di applicazione Azure Active Directory

L'obiettivo di questo articolo è fornita una panoramica delle principali funzionalità di sicurezza di Azure agevolare la gestione delle identità. Inoltre, sono disponibili collegamenti ad articoli che otterranno i dettagli di ogni caratteristica in modo che si acquisiscono informazioni.  

L'articolo analizza le seguenti funzionalità di gestione delle identità Azure principali:

- Il Single sign-on
- Proxy inverso
- Autenticazione a più fattori
- Il monitoraggio della protezione, avvisi e report basati su risorse computer
- Gestione delle identità e accesso consumer
- Registrazione periferica
- Gestione delle identità privilegi
- Protezione dell'identità
- Gestione delle identità ibrido

## <a name="single-sign-on"></a>Il Single sign-on

Single sign-on (SSO) indica che la possibilità di accedere a tutte le applicazioni e risorse che si vuole semplicemente business, effettuando l'accesso in una sola volta utilizzando un singolo account utente. Dopo aver effettuato l'accesso, è possibile accedere a tutte le applicazioni senza richiesto per eseguire l'autenticazione (ad esempio, digitare una password) una seconda volta.

Molte organizzazioni si basano sulle software come applicazioni di servizio, ad esempio Office 365, scatola e Salesforce per la produttività degli utenti finali. Storicamente, il personale IT necessarie per creare e aggiornare gli account utente in ogni applicazione SaaS singolarmente ed era necessario ricordare la password per ogni applicazione SaaS.

Azure Active Directory oltrepassa Active Directory locale nel cloud, consentendo agli utenti di utilizzare il proprio account aziendale principale non solo accedere ai dispositivi dominio e società risorse, ma anche tutte le applicazioni web e SaaS necessaria per il proprio lavoro.

Non solo gli utenti non è necessario gestire più set di nomi utente e password, l'accesso dell'applicazione può essere automaticamente di provisioning o deselezionare provisioning gruppi dell'organizzazione in base e lo stato di un dipendente. Azure Active Directory vengono presentate sicurezza e accedere ai controlli di governance che consentono di gestire centralmente accesso degli utenti tra le applicazioni SaaS.

Ulteriori informazioni:

- [Panoramica del Single Sign-On](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Integrare Azure Active Directory single sign-on con SaaS App](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inverso

Proxy di applicazione Azure Active Directory consente di pubblicare applicazioni locale, ad esempio siti di [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)e [IIS](http://www.iis.net/)-basata App all'interno della rete privata e fornisce l'accesso sicuro agli utenti esterni alla rete. Proxy applicazione offre accesso remoto e single sign-on (SSO) per molti tipi di applicazioni web locale con le migliaia di applicazioni SaaS che supporta Azure Active Directory. I dipendenti possono accedere le app da home nei propri dispositivi e l'autenticazione tramite questo proxy basato sul cloud.

Ulteriori informazioni:

- [Attivazione Proxy di applicazione di Azure Active Directory](../active-directory/active-directory-application-proxy-enable.md)
- [Pubblicare le applicazioni che utilizzano Proxy di applicazione Azure Active Directory](../active-directory/active-directory-application-proxy-publish.md)
- [Single sign-on con Proxy dell'applicazione](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Utilizzo di access condizionale](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori
Autenticazione a più fattori Azure (MFA) è un metodo di autenticazione che richiede l'uso di più di un metodo di verifica e si aggiunge un critico secondo livello di sicurezza per gli accessi utente e le transazioni. MFA consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Offre autenticazione tramite un intervallo delle opzioni di verifica, telefonata, messaggio di testo o app per dispositivi mobili notifica o verifica codice e 3o festa OAuth token.

Ulteriori informazioni:

- [Autenticazione a più fattori](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Che cos'è l'autenticazione a più fattori Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Funzionamento dell'autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Il monitoraggio della protezione, avvisi e report basati su risorse computer

Il monitoraggio della protezione e gli avvisi e report basati su risorse computer che identificano i modelli di accesso non coerente consentono di proteggere l'azienda. È possibile utilizzare access Azure Active Directory e i report sull'utilizzo di ottenere la visibilità sull'integrità e la sicurezza della directory dell'organizzazione. Con queste informazioni, un amministratore di directory può determinare meglio nel punto in cui può trovarsi possibili rischi di sicurezza in modo da potervi adeguatamente a ridurre i rischi.

Nel portale di Azure classico report sono classificati nei modi seguenti:

- Report anomalia-contenere gli eventi che sono stati rilevati da anomala di accesso. Il nostro obiettivo è informare l'utente di attività e consentono di essere in grado di un'operazione sulle sospetto un evento.
- Report di applicazione integrato – forniscono approfondimenti in uso delle applicazioni cloud all'interno dell'organizzazione. Azure Active Directory offre l'integrazione con migliaia di applicazioni cloud.
- Report di errore: indicare gli errori che possono verificarsi durante il provisioning di account in applicazioni esterne.
- Report specifici dell'utente: visualizzare dispositivo/accesso nei dati di attività per un utente specifico.
- Registri delle attività: contengono una registrazione di tutti gli eventi controllati all'interno delle ultime 24 ore, ultimi 7 giorni, o ultimi 30 giorni, nonché modifiche dell'attività di gruppo e attività di registrazione e reimpostare la password.

Ulteriori informazioni:

- [Visualizzare i report di access e l'uso](../active-directory/active-directory-view-access-usage-reports.md)
- [Guida introduttiva di Azure Active Directory creazione di report](../active-directory/active-directory-reporting-getting-started.md)
- [Guida alla creazione di report di Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestione delle identità e accesso consumer

Azure B2C Directory Active è un servizio di gestione identità globale, disponibilità per applicazioni connesse a consumer scale centinaia di milioni di identità. Può essere integrato in dispositivi mobili e web piattaforme. I consumatori possono accedere a tutte le applicazioni tramite esperienze personalizzabile usando il proprio account di social networking esistente oppure creare nuove credenziali.

In passato, gli sviluppatori di applicazioni che desiderano iscriversi e consumatori nelle applicazioni l'accesso dovrebbe scrivere il proprio codice. E sarebbe utilizzate sistemi o database locale per archiviare i nomi utente e password. Azure B2C Directory Active offre l'organizzazione un modo migliore per integrare applicazioni con l'aiuto di una piattaforma sicura e basate su standard e un elevato numero di extensible criteri di gestione delle identità consumer.

Quando si utilizza Azure Active Directory B2C, i clienti possono iscriversi per le applicazioni utilizzando il proprio account di social networking esistente (Facebook, Google, Amazon, LinkedIn) o tramite la creazione di nuove credenziali (indirizzo di posta elettronica e password, o il nome utente e password).

Ulteriori informazioni:

- [Che cos'è Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Anteprima di Azure Active Directory B2C: accedere verso l'alto ed effettuare l'accesso consumatori nelle applicazioni](../active-directory-b2c/active-directory-b2c-overview.md)
- [Anteprima B2C Azure Active Directory: Tipi di applicazioni](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registrazione periferica

Registrazione di dispositivo Azure Active Directory è la base per gli scenari basati su dispositivo [accesso condizionato](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Quando un dispositivo è registrato, registrazione di Azure Active Directory dispositivo fornisce il dispositivo con un'identità che viene utilizzato per eseguire l'autenticazione il dispositivo quando l'utente accede. Il dispositivo autenticato e gli attributi del dispositivo, quindi utilizzabile per applicare i criteri di accesso condizionato per le applicazioni presenti nel cloud e locale.

In combinazione con una soluzione di gestione (MDM) di un dispositivo mobile, ad esempio Intune, gli attributi di dispositivo in Azure Active Directory vengono aggiornati con informazioni aggiuntive sul dispositivo. In questo modo è possibile creare regole di accesso condizionato che consentono l'accesso da dispositivi per soddisfare agli standard di sicurezza e conformità.

Ulteriori informazioni:

- [Guida introduttiva di Azure Active Directory dispositivo registrazione](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Registrazione automatica dispositivo con i dispositivi di dominio di Azure Active Directory per Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Gestione delle identità privilegi
Azure Active Directory (AD) privilegi di gestione delle identità consente di gestire, controllare e monitorare le identità privilegi e accedere alle risorse di Azure Active Directory e altri servizi online di Microsoft Office 365 o Microsoft Intune.

Può succedere che gli utenti devono eseguire operazioni con privilegi di Azure o Office 365 risorse o altre App SaaS. Spesso sono organizzazioni concedere l'accesso con privilegi permanente in Azure Active Directory. In questo modo crescenti rischi di protezione per le risorse cloud ospitato organizzazioni non sono sufficientemente eseguire il monitoraggio delle operazioni con i privilegi di amministratore tali utenti. Inoltre, se un account utente con privilegi di accesso è compromessa, tale una violazione potrebbe avere un impatto loro protezione cloud globale. Gestione delle identità di Azure Active Directory privilegi consente di risolvere il rischio.

Gestione delle identità di Azure Active Directory privilegi consente di:

- Vedere quali utenti sono gli amministratori di Azure Active Directory
- Abilitare le connessioni, "just in time" accesso amministrativo a Microsoft Online Services come Office 365 e Intune
- Ricevere report Cronologia di accesso di amministratore e le modifiche nelle assegnazioni di amministratore
- Ricevere avvisi sull'accesso a un ruolo privilegiato

Ulteriori informazioni:

- [Gestione delle identità privilegi di Azure Active Directory](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Ruoli di Azure Active Directory con privilegi di gestione delle identità](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure Active Directory privilegi di gestione delle identità: Come aggiungere o rimuovere un ruolo utente](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Protezione dell'identità
Protezione di Azure Active Directory identità è un servizio di sicurezza che fornisce una visualizzazione consolidata di eventi di rischio e potenziali vulnerabilità che interessano identità dell'organizzazione. Protezione dell'identità utilizza funzionalità di rilevamento anomalia esistente Azure Active Directory (disponibile tramite il report attività anomala di Azure Active Directory) e sono disponibili nuovi tipi di evento di rischio in grado di rilevare alterazioni in tempo reale.

Ulteriori informazioni:

- [Protezione dell'identità di Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canale 9: Azure AD e Mostra identità: anteprima di protezione di identità](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gestione delle identità ibrido

Approccio di Microsoft identità riguardano locale e nel cloud, la creazione di una singola identità utente per l'autenticazione e l'autorizzazione per tutte le risorse, indipendentemente dalla posizione.

Ulteriori informazioni:

- [White paper relativo ibrido identità](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blog del Team di Active Directory](https://blogs.technet.microsoft.com/ad/)
