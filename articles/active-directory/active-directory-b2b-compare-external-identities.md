<properties
   pageTitle="Confronto tra le funzionalità per la gestione delle identità esterne con Azure Active Directory | Microsoft Azure"
   description="Contiene un confronto di collaborazione di Azure Active Directory B2B, B2C e App multi-tenant per supportare l'autenticazione e l'autorizzazione per le identità esterne"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Confronto tra le funzionalità per la gestione delle identità esterne con Azure Active Directory

Oltre a gestire l'accesso alle App SaaS gli utenti mobili, Azure Active Directory (Azure Active Directory) consente all'organizzazione condividano risorse business partner e distribuire applicazioni per aziende e clienti.

## <a name="developing-applications-for-businesses"></a>Sviluppo di applicazioni per le aziende

Fornire un servizio o applicazione, ad esempio un servizio di gestione delle retribuzioni, per le aziende? Azure Active Directory fornisce la piattaforma identità che consente di creare applicazioni che integrano con milioni di organizzazioni che hanno già configurato Azure Active Directory come parte di distribuzione di Office 365 o altri servizi dell'organizzazione.

**Esempio:** Una distribuzione pharmaceutical fornisce medici e sistemi di informazioni per il settore sanitario. Sono necessari per un'applicazione analitica consigliate medici e clienti desiderati per gestire le proprie identità di campo. La società ha scelto Azure Active Directory come la piattaforma identità per l'applicazione di gestione esercitazione, fornire identità di Azure Active Directory ai propri clienti simbolo chiocciola backup quando necessario. Per ulteriori informazioni, vedere [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Abilitazione dell'accesso business partner per le risorse aziendali

Viene offerto business partner o gli altri utenti all'esterno della società che hanno l'esigenza di accedere le risorse dell'organizzazione, ad esempio un sito di SharePoint o il sistema ERP? Azure AD Abilita amministratori per concedere agli utenti esterni (che non esistente o in Azure Active Directory) single sign-on accesso alle applicazioni aziendali. Questa migliora la protezione come utenti perdere l'accesso quando lasciano l'organizzazione partner, mentre è possibile controllare i criteri di accesso all'interno dell'organizzazione. Questa operazione anche semplifica l'amministrazione non necessarie gestire un partner esterno o per le relazioni federazione partner.

**Esempio:** Una società di acquisizione immagini fornisce rivenditori con foto di acquisizione immagini di servizi e opera la rete retail più grande di chioschi. Hanno scelto di Azure Active Directory per abilitare migliaia di utenti e partner commerciali al dettaglio come utilizzare le proprie credenziali per scaricare i materiali di marketing partner più recenti e riordinare le foto elaborazione forniture dal fornitore della società extranet. Per ulteriori informazioni, vedere [la collaborazione di Azure Active Directory B2B](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Sviluppo di applicazioni per gli utenti

È necessario in modo sicuro e conveniente pubblicare applicazioni online, ad esempio archivio anteriore al dettaglio, milioni di clienti? Azure Active Directory fornisce una piattaforma attivazione di social networking e nome utente e la password di accesso, iscrizione Self-Service personalizzato e la reimpostazione della password self-service per gli utenti dell'applicazione. In questo modo aumenta semplicità di utilizzo per i clienti riducendo carico agli sviluppatori.

**Esempio:** Il \#1 Sport franchising nel mondo necessario per avviare direttamente tra gli appassionati relativa 450 milioni. A tale scopo, sono progettate un'app per dispositivi mobili con Azure Active Directory per l'archiviazione di autenticazione e il profilo utente. Ventole ottenere semplificata di registrazione e accesso tramite l'utilizzo dell'account di social come Facebook o utilizzano il nome utente/password tradizionali per un'esperienza integrata tra iOS, Android e Windows telefoni. Sulla base di Fondazione piattaforma Azure Active Directory notevolmente ridotto codice personalizzato durante la personalizzazione e alleviare i problemi relativi alla sicurezza, le violazioni di dati e scalabilità dato il franchising personalizzati. Per ulteriori informazioni, vedere [B2C di Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Confronto delle funzionalità di Azure Active Directory

Tutti gli scenari già illustrati in questo articolo è indirizzato dalle funzionalità all'interno di Azure Active Directory. In questa tabella dovrebbe chiarire quali funzionalità sono più importanti per l'utente:

| **Valutare la possibilità di questo prodotto...**       | [Azure Active Directory multi-tenant SaaS app](active-directory-developers-guide.md)    | [Azure Active Directory B2B collaborazione](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Se è necessario fornire...** | un servizio per le aziende | accesso dei partner ai App personali  | un servizio ai clienti |
| **E sono simile a...**  | Registrazione al Pharma distributor      | Acquisizione di immagini società            | Franchising sport       |
| **Distribuire un'app per...**  | Gestione di esercitazione     | Fornitore extranet          | Appassionati di calcio            |
| **L'assegnazione del...**        | Uffici medico        | Partner autorizzato | Chiunque disponga di posta elettronica      |
| **Quando...**      | Autorizzazioni di amministratore dei clienti | Invita personale amministratore           | L'utente si iscrive      |
