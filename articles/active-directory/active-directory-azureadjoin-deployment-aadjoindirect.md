<properties
    pageTitle="Scenari di utilizzo e considerazioni sulla distribuzione di Azure Active Directory partecipare | Microsoft Azure"
    description="Spiega come gli amministratori possono configurare partecipare di Azure Active Directory per utenti finali (dipendenti, studenti, altri utenti). Inoltre, vengono descritti i diversi scenari reali per l'uso di Azure Active Directory partecipare."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< tag ms.service= "active directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/27/2016"

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scenari di utilizzo e considerazioni sulla distribuzione per partecipare a Azure Active Directory

## <a name="usage-scenarios-for-azure-ad-join"></a>Scenari di utilizzo per partecipare a Azure Active Directory
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scenario 1: Imprese ampiamente nel cloud

Azure Active Directory partecipare (Azure Active Directory partecipare) possono trarre vantaggio si se attualmente funzionare e gestione delle identità per l'azienda nel cloud o passare al cloud breve. È possibile usare un account che è stato creato in Azure AD effettuare l'accesso a Windows 10. [Il](active-directory-azureadjoin-user-frx.md)processo prima esperienza fase (FRX) o eseguendo il join di Azure Active Directory dal [menu Impostazioni](active-directory-azureadjoin-user-upgrade.md), gli utenti possono partecipare nei computer di Azure Active Directory.  Gli utenti possono inoltre sfrutteranno single sign-on (SSO) accesso alle risorse cloud come Office 365 nel browser o nelle applicazioni di Office.

### <a name="scenario-2-educational-institutions"></a>Scenario 2: Istituti di istruzione

Istituti di istruzione in genere hanno due tipi di utente: docenti e studenti. I membri di istituti di istruzione sono considerati più lungo termine membri dell'organizzazione. Creazione di account locali per poterli può essere utile. Ma studenti sono shorter-term membri dell'organizzazione e il proprio account possono essere gestiti in Azure Active Directory. Questo errore indica che è possibile inserire scala directory nel cloud anziché vengano archiviati in locale. Inoltre, significa che gli studenti saranno in grado di accedere a Windows con il proprio account Azure Active Directory e accesso alle risorse di Office 365 nelle applicazioni di Office.

### <a name="scenario-3-retail-businesses"></a>Scenario 3: Rivenditori

Rivenditori sono stagionali e dipendenti a lungo termine. È in genere creare account locali e usare macchine di dominio per i dipendenti più lungo termine. Ma stagionale sono shorter-term membri dell'organizzazione ed è utile per gestire il proprio account in cui le licenze utente possono essere più facilmente spostate. Quando si crea l'account utente nel cloud con licenze di Office 365, questi utenti è possibile ottenere vantaggi dell'accesso a Windows e Office applicazioni con un account Azure Active Directory, mentre si mantiene una maggiore flessibilità con le licenze una volta terminata.

### <a name="scenario-4-additional-scenarios"></a>Scenario 4: Altri scenari

Insieme ai vantaggi in precedenza, è possibile utilizzare determinate con gli utenti di partecipare ai dispositivi di Azure Active Directory a causa di una funzionalità di unione semplificate, gestione di dispositivi efficiente, la registrazione di gestione automatica dispositivo mobile e servizio single sign-on di Azure Active Directory e le risorse locali.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Considerazioni sulla distribuzione per partecipare a Azure Active Directory

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Consentire agli utenti di partecipare a un dispositivo appartenenti alla società direttamente a Azure Active Directory


Aziende possono fornire account basata solo su cloud per le organizzazioni e partner. Questi partner quindi accedere facilmente App società e le risorse con single sign-on. Questo scenario si applica agli utenti che accedono alle risorse principalmente nel cloud, ad esempio le applicazioni di Office 365 o SaaS basate su Azure Active Directory per l'autenticazione.

### <a name="prerequisites"></a>Prerequisiti
**Il livello di organizzazione (amministratore)**

*   Sottoscrizione Azure con Azure Active Directory  

**Livello di utente**

*   Windows 10 (edizioni Professional ed Enterprise)

### <a name="administrator-tasks"></a>Attività di amministrazione
* [Configurare la registrazione periferica](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Attività definite dall'utente
* [Configurare un dispositivo Windows 10 nuovo con Azure Active Directory durante l'installazione](active-directory-azureadjoin-user-frx.md)
* [Configurare un dispositivo Windows 10 con Azure Active Directory dal menu impostazioni](active-directory-azureadjoin-user-upgrade.md)
* [Partecipare a un dispositivo Windows 10 personale dell'organizzazione](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Abilitare BYOD all'interno dell'organizzazione per Windows 10
Per utilizzare i dispositivi di Windows (BYOD) per accedere a società App personali e le risorse, è possibile impostare configurati gli utenti e dipendenti. Gli utenti possono aggiungere gli account di Azure Active Directory (account aziendale o dell'istituto di istruzione) di un dispositivo Windows personale per accedere alle risorse in modo sicuro e conforme.

### <a name="prerequisites"></a>Prerequisiti
**Il livello di organizzazione (amministratore)**

*   Sottoscrizione di Azure Active Directory

**Livello di utente**

*   Windows 10 (edizioni Professional ed Enterprise)


### <a name="administrator-tasks"></a>Attività di amministrazione

* [Configurare la registrazione periferica](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Attività definite dall'utente
* [Partecipare a un dispositivo Windows 10 personale dell'organizzazione](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password tramite Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
