<properties
    pageTitle="Criteri di dispositivo accesso condizionale per i servizi di Office 365 | Microsoft Azure"
    description="Informazioni dettagliate su come basate su dispositivo condizioni controllano l'accesso ai servizi di Office 365. Mentre gli Information Worker (informatico) desidera servizi di accesso a Office 365, ad esempio Exchange e SharePoint Online sul lavoro o dell'istituto di istruzione dai dispositivi personali, amministratore IT richiede l'accesso per gli amministratori di secure.IT possono effettuare il provisioning di criteri di dispositivo accesso condizionato per proteggere risorse aziendali, mentre nello stesso momento consentendo informatico nei dispositivi conformi di accedere ai servizi."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Criteri di dispositivo accesso condizionale per i servizi di Office 365

Il termine "accesso condizionale" ha molte condizioni associate, ad esempio utente autenticato a più fattori, autenticato dispositivo, dispositivo conforme e così via. In questo argomento focusses principalmente condizioni basate su dispositivo per controllare l'accesso ai servizi di Office 365. Mentre gli Information Worker (informatico) desidera per Office 365 accedere a servizi come Exchange e SharePoint Online sul lavoro o dell'istituto di istruzione dai dispositivi personali, amministratore IT richiede l'accesso sicuro. Gli amministratori IT possono effettuare il provisioning di criteri di dispositivo accesso condizionato per proteggere risorse aziendali, mentre nello stesso momento consentendo informatico nei dispositivi conformi di accedere ai servizi. È possibile configurare i criteri di accesso condizionale a Office 365 dal portale di Microsoft Intune accesso condizionato.

Azure Active Directory applica criteri di accesso condizionale per proteggere l'accesso ai servizi di Office 365. Un amministratore del tenant è possibile creare un criterio di accesso condizionale che si blocca un utente in un dispositivo non compatibile dall'accesso a un servizio di Office 365. L'utente deve essere conforme ai criteri di dispositivo della società prima di accedere al servizio. In alternativa, l'amministratore può anche creare un criterio che gli utenti dovranno solo registrare il proprio dispositivo per accedere a un servizio di Office 365. Criteri potrebbero applicati a tutti gli utenti dell'organizzazione, o limitati a pochi gruppi di destinazione e nel tempo per includere i gruppi di destinazione aggiuntivo.

Per applicare i criteri dei dispositivi solo per gli utenti registrare i dispositivi con il servizio di registrazione di Azure Active Directory dispositivo. È possibile scegliere per abilitare l'autenticazione a più fattori (MFA) per la registrazione di dispositivi con il servizio di registrazione di Azure Active Directory dispositivo. MFA è consigliato per il servizio di registrazione di Azure Active Directory dispositivo. Quando è abilitata la MFA, vengono considerati come utenti la registrazione dei dispositivi con il servizio di registrazione di Azure Active Directory dispositivo per la seconda autenticazione fattore.

##<a name="how-does-conditional-access-policy-work"></a>Funzionano dei criteri di accesso condizionato

Quando un utente richiede l'accesso al servizio Office 365 da una piattaforma dispositivo supportato, Azure Active Directory autentica l'utente e il dispositivo da cui l'utente avvia la richiesta. e concede accesso al servizio solo quando l'utente è conforme i criteri impostati per il servizio. Gli utenti che non dispongono di dispositivo registrato vengono forniti istruzioni riparazione registrare e sugli conforme per accedere ai servizi di Office 365 aziendali. Gli utenti di dispositivi iOS e Android saranno necessario registrare il proprio dispositivo tramite applicazione portale aziendale. Quando un utente registra il proprio dispositivo, il dispositivo è registrato con Azure Active Directory e registrato per la conformità e gestione di dispositivi. Clienti è necessario utilizzare il servizio di Azure Active Directory dispositivo registrazione in combinazione con Microsoft Intune per attivare la gestione di dispositivi mobili per servizio Office 365. Registrazione del dispositivo è un prerequisito per gli utenti di Office 365 accedere ai servizi quando vengono applicati criteri per i dispositivi.

Quando un utente registra il proprio dispositivo correttamente, il dispositivo diventa attendibile. Consente di Azure Active Directory Single Sign-On per applicazioni aziendali di access e Applica criteri di accesso condizionato per concedere l'accesso a un tempo di servizio non solo la prima l'utente richiede l'accesso, ma ogni volta che l'utente richiede di rinnovare l'accesso. L'utente verrà negato l'accesso ai servizi quando vengono modificate le credenziali di accesso, dispositivo perso/furto o il criterio non è soddisfatta al momento della richiesta per il rinnovo.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione:
È necessario utilizzare servizio di registrazione di Azure Active Directory dispositivo per la registrazione di dispositivo.

Se gli utenti devono essere autenticate in locale, è necessario Active Directory Federation Services (ADFS) (1.0 e versioni successive). Autenticazione a più fattori per partecipare a luogo di lavoro avrà esito negativo quando il provider di identità non è in grado di autenticazione a più fattori. Ad esempio ADFS 2.0 non è più fattori che supporta l'autenticazione. Amministratore tenant necessario assicurarsi che i locale ADFS è in grado di MFA e un metodo MFA valido è attivato, prima di attivare MFA del servizio di registrazione di Azure Active Directory dispositivo. Ad esempio ADFS in Windows Server 2012 R2 funzionalità MFA. È anche necessario abilitare un metodo di autenticazione valido aggiuntive (MFA) nel server ADFS prima di attivare MFA del servizio di registrazione di Azure Active Directory dispositivo. Per ulteriori informazioni sui metodi MFA supportati in ADFS, vedere Configurare i metodi di autenticazione aggiuntive per AD FS.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti domande frequenti

D: qual è il criterio di esclusione predefinito per le piattaforme di dispositivi non supportati?

R: al momento, criteri di accesso condizionale vengono rispettati in modo selettivo gli utenti di dispositivi iOS e Android. Le applicazioni in altre piattaforme di dispositivi sono, per impostazione predefinita, invariato dai criteri di accesso condizionato per dispositivi iOS e Android. Amministratore tenant di maggio, tuttavia, scegliere ignorare il criterio globale per impedire l'accesso agli utenti piattaforme non supportate.
Si tratta della mappa per estendere Criteri di accesso condizionale agli utenti in altre piattaforme, incluse le finestre.

Q: quando il criterio di accesso condizionale ai servizi di Office 365 essere esteso alle App basata su Browser (ad esempio, OWA, SharePoint basate su browser).

R: al momento, accesso condizionato ai servizi di Office 365 è limitata ai applicazioni sul dispositivo. Si tratta della mappa per estendere Criteri di accesso condizionato per gli utenti che accedono ai servizi da parte dei browser.
