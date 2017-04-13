<properties 
    pageTitle="Eseguire l'autenticazione con Active Directory locale nell'app Azure | Microsoft Azure" 
    description="Informazioni sulle diverse opzioni per le applicazioni line-of-business in Azure App servizio per eseguire l'autenticazione con Active Directory locale" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Eseguire l'autenticazione con Active Directory locale nell'app Azure #

In questo articolo viene illustrato come eseguire l'autenticazione con Active Directory (AD) in [Azure App servizio](../app-service/app-service-value-prop-what-is.md)locale. Un'applicazione Azure è ospitata nel cloud, ma sono disponibili modi per eseguire l'autenticazione locale agli utenti di Active Directory in modo sicuro. 

## <a name="authenticate-through-azure-active-directory"></a>Eseguire l'autenticazione tramite Azure Active Directory
Può essere rappresentato da un tenant di Azure Active Directory directory sincronizzate con un locale Active Directory. Questo approccio consente agli utenti di Active Directory di accedere l'App da internet e di autenticazione tramite le proprie credenziali locale. Inoltre, servizio App Azure offre una [chiave attiva soluzione per questo metodo](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Bastano pochi clic di un pulsante, è possibile abilitare l'autenticazione con un tenant di sincronizzazione directory per l'app Azure. Questo approccio è i seguenti vantaggi:

-   Non è necessario un codice di autenticazione nell'app. Consentire servizio App eseguire l'autenticazione dell'utente e il tempo per fornire funzionalità nell'app.
-   [API di Azure Active Directory grafico](http://msdn.microsoft.com/library/azure/hh974476.aspx) consente l'accesso ai dati di directory dell'App Azure.
-   Vengono fornite Single Sign-on per [tutte le applicazioni supportate da Azure Active Directory](/marketplace/active-directory/), tra cui Office 365, Dynamics CRM Online, Microsoft Intune e delle migliaia di applicazioni non Microsoft cloud. 
-   Azure Active Directory supporta il controllo dell'accesso basato sui ruoli. È possibile utilizzare il modello [Authorize(Roles="X")] con modifiche minime al codice.

Per informazioni su come scrivere un'app di Azure line-of-business per l'autenticazione con Azure Active Directory, vedere [creare un'app di Azure del settore con l'autenticazione di Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Eseguire l'autenticazione tramite un servizio token di sicurezza locale
Se si dispone di un locale protetto servizio token () come Active Directory Federation Services (ADFS), è possibile utilizzare che la federazione di autenticazione per l'app Azure. Questo approccio è consigliato quando criteri aziendali impedisce che l'archiviazione in Azure dati Active Directory. Tuttavia, tenere presente quanto segue:

-   Servizio token di sicurezza topologia deve essere distribuiti in locale, con il sovraccarico di costi e gestione.
-   Solo gli amministratori di ADFS possono configurare [regole Richiedi e componente trust di terze parti](http://technet.microsoft.com/library/dd807108.aspx), che possono limitare le opzioni per gli sviluppatori. Mano, è possibile gestire e personalizzare le [domande](http://technet.microsoft.com/library/ee913571.aspx) in ogni applicazione.
-   Accesso a locale dati Active Directory richiedono una soluzione separata attraverso il firewall aziendale.

Per informazioni su come scrivere un'app di Azure line-of-business per l'autenticazione con un servizio token di sicurezza locale, vedere [creare un'app di Azure del settore con l'autenticazione di ADFS](web-sites-dotnet-lob-application-adfs.md).
 
