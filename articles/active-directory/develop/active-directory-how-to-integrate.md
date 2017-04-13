<properties
   pageTitle="Integrazione con Azure Active Directory di | Microsoft Azure"
   description="Guida ai vantaggi e risorse per l'integrazione con Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Integrazione con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory offre alle organizzazioni di gestione delle identità aziendale per le applicazioni cloud.  Integrazione di Azure Active Directory consente agli utenti un'esperienza di accesso semplificata e consente l'applicazione è conforme ai criteri IT.

## <a name="how-to-integrate"></a>Come integrare

Esistono diversi modi per l'applicazione per l'integrazione con Azure Active Directory.  Sfruttare il numero o di un numero minimo di questi scenari come appropriato per l'applicazione.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Supporto tecnico di Azure Active Directory come un modo per accedere all'applicazione

**Ridurre Accedi attrito e ridurre i costi di supporto.** Utilizzando Azure Active Directory per accedere all'applicazione, gli utenti non dispongono di un altro nome e la password da ricordare.  Gli sviluppatori, è necessario un meno password per archiviare e proteggere.  Non è necessario gestire la reimpostazione della password dimenticata potrebbero essere significativamente singolarmente.  Azure Active Directory potenza Accedi per alcune applicazioni cloud più usate al mondo, tra cui Office 365 e Microsoft Azure.  Con centinaia di milioni agli utenti di milioni di organizzazioni, è molto probabile che l'utente è già stato effettuato l'accesso a Azure Active Directory.  Altre informazioni [sull'aggiunta del supporto per la connessione di Azure Active Directory](../active-directory-authentication-scenarios.md).

**Semplificare il segno di configurazione per l'applicazione.**  Durante l'iscrizione per l'applicazione, Azure AD inviare informazioni essenziali di un utente in modo da poter pre-compilare il modulo di iscrizione o eliminare completamente.  Gli utenti possono iscriversi per l'applicazione utilizzando il proprio account Azure Active Directory mediante un'esperienza di consenso familiari simile a quelli del social media e applicazioni per dispositivi mobili.  Tutti gli utenti possono iscriversi e accedere a un'applicazione che è integrata con Azure Active Directory senza il coinvolgimento di personale.  Altre informazioni [sull'accesso a backup l'applicazione per Azure AD Account di accesso](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Ricerca di utenti, gestire il Provisioning dell'utente e controllare l'accesso all'applicazione

**Ricerca di utenti nella directory.**  Utilizzare l'API di grafico per aiutare gli utenti a eseguire una ricerca e cercare altri utenti dell'organizzazione quando si invitano altre persone o indirizzi di concessione dell'accesso, anziché dover digitare posta elettronica.  Gli utenti possono visualizzare mediante un'interfaccia di stile libro indirizzo familiare, inclusa la visualizzazione dei dettagli della gerarchia dell'organizzazione.  Ulteriori informazioni sull' [API di grafico](../active-directory-graph-api.md).

**Riutilizzare gruppi di Active Directory e liste di distribuzione che già gestione di un cliente.**  Azure Active Directory contiene i gruppi che il cliente è già usando per la distribuzione di posta elettronica e la gestione dell'accesso.  Tramite l'API di grafico, riutilizzare questi gruppi invece che richiedono il cliente creare e gestire un set di gruppi nell'applicazione separato.  Informazioni sul gruppo possono anche essere inviati all'applicazione nel token di accesso.  Ulteriori informazioni sull' [API di grafico](../active-directory-graph-api.md).

**Utilizzare Azure Active Directory per controllare chi ha accesso all'applicazione.**  Proprietari di applicazione in Azure Active Directory e gli amministratori possono assegnare accesso alle applicazioni a specifici utenti e gruppi.  Tramite l'API di grafico, è possibile leggere questo elenco e usarlo per controllare il provisioning e deselezionare il provisioning di risorse e accedere all'interno dell'applicazione.

**Controllo dell'accesso basato usare Azure Active Directory per i ruoli.**  Proprietari di applicazioni e gli amministratori possono assegnare utenti e gruppi ai ruoli definite dall'utente quando si registra l'applicazione in Azure Active Directory.  Informazioni sui ruoli viene inviate all'applicazione nel token di accesso e possono anche essere letti utilizzando l'API di grafico.  Ulteriori informazioni [sull'utilizzo di Azure Active Directory per l'autorizzazione](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Accedere al profilo dell'utente, calendario, posta elettronica, contatti, file e altro ancora

**Azure Active Directory è il server di autorizzazione per Office 365 e altri servizi professionali Microsoft.**  Se si supporta Azure Active Directory per accedere all'applicazione o al supporto per gli account utente corrente il collegamento agli account utente di Azure Active Directory utilizzando OAuth 2.0, è possibile richiedere l'accesso in lettura e scrittura per un profilo utente, calendario, posta elettronica, contatti, file e altre informazioni.  È facile scrivere eventi al calendario dell'utente e leggere o scrivere i file nel proprio OneDrive.  Ulteriori informazioni [sull'accesso a Office 365 API](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promuovere l'applicazione di Azure Marketplace di Office 365

**Alzare di livello applicazione a milioni di organizzazioni che usano già Azure Active Directory.**  Gli utenti che cercare ed esplorare tali Marketplace già utilizza uno o più servizi cloud, rendendoli qualificati ai clienti del servizio cloud.  Ulteriori informazioni su promuovere l'applicazione in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando gli utenti effettua l'iscrizione per l'applicazione, verrà visualizzato nel Pannello di accesso di Azure Active Directory di avvio delle app di Office 365.**  Gli utenti potranno in modo semplice e rapido tornare all'applicazione in un secondo momento, migliorando coinvolgimento utente.  Ulteriori informazioni su [Azure AD accedere al pannello](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicazione sicura dispositivo al servizio e al servizio

**Utilizzare Azure Active Directory per la gestione delle identità di servizi e dei dispositivi riduce il codice che è necessario scrivere e consente IT per gestire l'accesso.**  Ottenere i token da Azure Active Directory utilizzando OAuth e utilizzare tali token per accedere a web API servizi e dispositivi.  Utilizzando Azure Active Directory è possibile evitare la scrittura di codice di autenticazione complesse.  Poiché le identità dei servizi e i dispositivi sono archiviate in Azure Active Directory, IT possono gestire chiavi e revoca in un'unica posizione invece di dover eseguire questa operazione separatamente nell'applicazione.

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Integrazione con Azure Active Directory viene fornito con vantaggi che non richiedono scrivere codice aggiuntivo.

### <a name="integration-with-enterprise-identity-management"></a>Integrazione con la gestione delle identità dell'organizzazione

**Sono utili per l'applicazione si attenga a criteri IT.**  Organizzazioni integrano i sistemi di gestione delle identità dell'organizzazione con Azure Active Directory, in modo quando un utente lascia un'organizzazione, vengono automaticamente perderà l'accesso all'applicazione senza la necessità di eseguire operazioni aggiuntive IT.  IT possono gestire chi può accedere all'applicazione e determinare quali criteri di accesso necessari - esempio autenticazione a più fattori - ridurre la necessità di scrivere codice per rispettare i criteri aziendali complessi.  Azure Active Directory consente agli amministratori di un file di log di controllo dettagliate di che hanno effettuato l'accesso all'applicazione in modo IT possibile tenere traccia dell'utilizzo.

**Azure Active Directory estende Active Directory nel cloud, in modo che l'applicazione può essere integrato con Active Directory.**  Molte organizzazioni in tutto il mondo utilizzano Active Directory come loro principali accesso e il sistema di gestione delle identità e richiedono applicazioni per l'uso con Active Directory.  Integrazione con Azure Active Directory, l'app si integra con Active Directory.

### <a name="advanced-security-features"></a>Funzionalità di sicurezza avanzate

**Autenticazione a più fattori.**  Azure Active Directory fornisce autenticazione a più fattori nativo.  Gli amministratori IT possono richiedere l'autenticazione a più fattori per accedere all'applicazione, in modo che non si dispone per il supporto di codice se stessi.  Altre informazioni [sull'Autenticazione a più fattori](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Accedi anomala rilevamento.**  Azure Active Directory elabora gli accessi più di un miliardi al giorno, durante l'utilizzo degli algoritmi risorse computer per rilevare attività sospetto e la notifica agli amministratori IT di possibili problemi.  Per supportare accessohttp Azure Active Directory, l'applicazione viene assegnato il vantaggio di questo tipo di protezione. Ulteriori informazioni su come [visualizzare Azure Active Directory accedere report](../active-directory-view-access-usage-reports.md).

**Accesso condizionato.**  Oltre l'autenticazione a più fattori, gli amministratori possono richiedere specifiche condizioni che precede gli utenti possono accedere all'applicazione.  Condizioni che possono essere impostate includono l'intervallo di indirizzi IP di sui dispositivi client, appartenenza ai gruppi specificati e lo stato del dispositivo in uso per l'accesso.  Altre informazioni [sull'accesso condizionato Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Sviluppo semplificata

**Protocolli standard.**  Microsoft si impegna a standard di supporto.  Azure Active Directory supporta i protocolli di autenticazione SAML 2.0, OpenID Connect 1.0, 2.0 OAuth e WS-Federation 1.2.  L'API grafico è 4.0 OData conforme.  Se l'applicazione già supporta il protocollo SAML 2.0 o OpenID Connect 1.0 per accedere federato, aggiunta del supporto per Azure Active Directory può essere molto semplice.  Ulteriori informazioni su [protocolli di autenticazione di Azure Active Directory è supportato](../active-directory-authentication-protocols.md).

**Aprire le raccolte di origine.**  Microsoft offre le raccolte completamente supportato Apri origine per le piattaforme di sviluppo di velocità e linguaggi.  Il codice sorgente è concesso in licenza in Apache 2.0 e si tratta di divisione e collaborare ai progetti.  Ulteriori informazioni sulle [raccolte di autenticazione di Azure Active Directory](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Informazioni sulla presenza in tutto il mondo e disponibilità

**Azure Active Directory viene distribuito nei data center tutto il mondo seduti gestite e monitorate nell'arco.**  Azure Active Directory è il sistema di gestione delle identità per Microsoft Azure e in Office 365 e viene distribuito nei data 28 Center tutto il mondo.  Dati di directory necessariamente essere replicate in almeno tre Data Center.  Servizi di bilanciamento del carico globale garantire l'accesso agli utenti la copia più vicina di Azure Active Directory che contiene i dati e automaticamente reindirizzare le richieste di altri centri dati se viene rilevato un problema.

## <a name="next-steps"></a>Passaggi successivi

[Iniziare a scrivere codice](../active-directory-developers-guide.md#getting-started).

[Utenti di accedere con Azure Active Directory](../active-directory-authentication-scenarios.md)
