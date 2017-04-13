<properties
   pageTitle="Integrazione di Azure Active Directory con le applicazioni di Guida introduttiva |  Microsoft Azure"
   description="In questo articolo riguarda l'integrazione di Azure Active Directory (AD) con le applicazioni locali e cloud di una Guida introduttiva."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrazione di Azure Active Directory con le applicazioni di Guida introduttiva
## <a name="overview"></a>Panoramica
In questo argomento è destinato a per ottenere una Guida di orientamento per l'integrazione di applicazioni con Azure Active Directory (AD). Ognuna delle sezioni che seguono contengono un breve riepilogo di un argomento più dettagliato in modo che è possibile identificare le parti di questa Guida introduttiva sono importanti per l'utente.  Seguire i collegamenti per ulteriori approfondimenti sugli ogni argomento.

## <a name="before-you-begin-take-inventory"></a>Prima di iniziare, eseguire inventario
Prima di passare all'integrazione di applicazioni con Azure Active Directory, è importante conoscere ci si trovi e in cui si desidera passare.  Gli aspetti seguenti sono destinati consentono di considerare il progetto di integration applicazione Azure Active Directory.

### <a name="application-inventory"></a>Inventario delle applicazioni
- Dove si trovano tutte le applicazioni? Chi è il responsabile?
- Quali tipi di autenticazione richiedono le applicazioni
- Chi deve accesso alle quali applicazioni?
- Si desidera distribuire una nuova applicazione?
  - Verranno creare interni e distribuirlo in un'istanza di elaborazione Azure?
  - Utilizzo di un modello disponibile nella raccolta applicazione Azure.

### <a name="user-and-group-inventory"></a>Inventario di utenti e gruppi
- Nel punto in cui gli account utente si trovano?
 - Active Directory in locale
 - Azure Active Directory
 - All'interno di un database di applicazioni separato che si è proprietari
 - Nelle applicazioni unsanctioned
 - Tutte le risposte sopra
- Quali autorizzazioni e assegnazioni di ruolo singoli utenti si dispone? È necessario controllare l'accesso o si è certi che le assegnazioni di accesso e il ruolo di utente siano appropriate ora?
- Gruppi già definiti in Active Directory locale?
 - Organizzazione delle gruppi
 - Chi sono i membri del gruppo?
 - Quali assegnazioni di autorizzazioni/ruolo i gruppi si dispone?
- È necessario pulire i database utente/gruppo prima di integrare?  (Si tratta di una domanda piuttosto importante. Propagazione delle limitazioni della fuori.)

### <a name="access-management-inventory"></a>Inventario di gestione di Access
- Come si gestiscono attualmente le l'accesso degli utenti alle applicazioni? Che è necessario modificare?  È stato considerato altri modi per gestire l'accesso, ad esempio con [RBAC](role-based-access-control-configure.md) , ad esempio?
- Chi deve accesso ai contenuti?

Forse si non sono disponibili le risposte a tutti di queste domande anticipo ma che non costituisce un problema.  Questa guida consentono di rispondere ad alcune di queste domande e prendere alcune decisioni.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione Azure e una directory di Azure Active Directory.  Se si dispone già di un abbonamento a Azure, è possibile provare Azure gratuitamente per 30 giorni. [Provalo!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integrazione di applicazioni con Azure Active Directory
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Ricerca unsanctioned applicazioni cloud con Cloud App individuazione
Come detto in precedenza, è possibile che le applicazioni non sono gestiti dall'organizzazione fino a questo momento.  Come parte del processo di inventario, è possibile trovare le applicazioni cloud unsanctioned. Vedere [ricerca di applicazioni cloud unsanctioned con individuazione App Cloud](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Tipi di autenticazione
Ognuna delle applicazioni di posta può avere requisiti di autenticazione diverso. Con Azure Active Directory, certificati di firma possono essere utilizzati con le applicazioni che utilizzano SAML 2.0, WS-Federation o OpenID connettersi protocolli, nonché Password Single Sign-On. Per ulteriori informazioni sull'applicazione tipi di autenticazione per l'utilizzo con Azure Active Directory vedere [Gestione dei certificati per federato Single Sign-On di Azure Active Directory](active-directory-sso-certs.md) e [la Password in base al servizio single sign](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Attivazione di Single Sign-On con Azure Active Directory App Proxy
Con Proxy di applicazione di Microsoft Azure Active Directory, è possibile fornire l'accesso alle applicazioni di cui che si trova all'interno della rete privata in modo sicuro, ovunque e da qualsiasi dispositivo. Dopo aver installato un connettore proxy applicazione all'interno dell'ambiente, può essere configurato facilmente con Azure Active Directory.

### <a name="integrating-applications-with-azure-ad"></a>Integrazione di applicazioni con Azure Active Directory
Gli articoli seguenti discutere in diversi modi applicazioni integrano con Azure Active Directory e forniscono alcune informazioni.

- [Determinare quali Active Directory da utilizzare](active-directory-administer.md)
- [Utilizzo di applicazioni nella raccolta applicazione Azure](active-directory-appssoaccess-whatis.md)
- [Integrazione di elenco esercitazioni delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gestione dell'accesso alle applicazioni
Gli articoli seguenti illustrano modi per gestire l'accesso alle applicazioni dopo che sono state integrate con Azure Active Directory utilizzando i connettori di Azure Active Directory e Azure Active Directory.

- [Gestione dell'accesso alle App utilizzando Azure Active Directory](active-directory-managing-access-to-apps.md)
- [Automazione tramite connettori ad angolo Azure Active Directory](active-directory-saas-app-provisioning.md)
- [Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md)
- [Assegnazione di gruppi a un'applicazione](active-directory-applications-guiding-developers-assigning-groups.md)
- [La condivisione di account](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrazione di applicazioni personalizzate
Se si sta creando una nuova applicazione e si desidera aiutare gli sviluppatori di sfruttare la potenza di Azure Active Directory, vedere [gli sviluppatori orientamenti](active-directory-applications-guiding-developers-for-lob-applications.md).

Se si desidera aggiungere l'applicazione personalizzata nella raccolta di applicazione Azure, vedere ["Portare il proprio app" con la configurazione SAML Self-Service di Azure Active Directory](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Vedere anche

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
