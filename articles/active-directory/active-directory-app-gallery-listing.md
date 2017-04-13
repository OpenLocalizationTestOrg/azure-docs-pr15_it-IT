<properties
   pageTitle="Voce dell'applicazione nella raccolta di applicazione di Azure Active Directory"
   description="Come visualizzare un elenco di un'applicazione che supporta il single sign-on nella raccolta di Azure Active Directory | Microsoft Azure"
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


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Voce dell'applicazione nella raccolta di applicazione di Azure Active Directory

Per visualizzare un'applicazione che supporta single sign-on con Azure Active Directory nella [raccolta di Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/), l'applicazione prima di tutto necessario implementare una delle seguenti modalità di integrazione:

* **Connettere OpenID** - integrazione diretta con Azure Active Directory utilizzando OpenID connessione per l'autenticazione e l'API di consenso Azure Active Directory per la configurazione. Se si sta avviando solo un'integrazione e l'applicazione non supporta SAML, questa è la modalità di consigliare.

* **SAML** – già l'applicazione è in grado di configurare i provider di identità di terze parti mediante il protocollo SAML.

Requisiti di voce per ogni modalità sono sotto.

##<a name="openid-connect-integration"></a>Integrazione di connettersi OpenID

Per integrare l'applicazione con Azure Active Directory, seguire le [istruzioni per sviluppatori](active-directory-authentication-scenarios.md). Quindi completare le domande seguenti e inviare a waadpartners@microsoft.com.

* Specificare le credenziali per un account o un tenant di test con l'applicazione che può essere utilizzato dal team di Azure Active Directory per testare l'integrazione.  

* Le istruzioni su come il team di Azure Active Directory possa accedere e connettere un'istanza di Azure Active Directory all'applicazione utilizzando il [framework di consenso Azure Active Directory](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Le istruzioni eventuali ulteriori operazioni necessarie per il team di Azure Active Directory testare single sign-on con l'applicazione. 

* Specificare le informazioni seguenti:

> Nome della società:
> 
> Sito Web della società:
> 
> Nome dell'applicazione:
> 
> Descrizione dell'applicazione (limite di 256 caratteri):
> 
> Sito Web dell'applicazione (informativo):
> 
> Sito Web supporto tecnico di applicazione o informazioni di contatto personali:
> 
> ID client dell'applicazione, come illustrato nei dettagli dell'applicazione in https://manage.windowsazure.com:
> 
> URL di abbonamento a un servizio dell'applicazione in cui i clienti passare a iscriversi a e/o acquistare l'applicazione:
> 
> Scegliere fino a tre categorie per l'applicazione venga elencato sotto (per categorie disponibili, vedere Azure Active Directory Marketplace):
> 
> Allegare applicazione piccola icona (file PNG, 45px da 45px, colore di sfondo a tinta unita):
> 
> Allegare applicazione icone grandi (file PNG, 215px da 215px, colore di sfondo a tinta unita):
> 
> Allegare Logo dell'applicazione (file PNG, 150px da 122px, colore di sfondo trasparente):

##<a name="saml-integration"></a>Integrazione di SAML

Qualsiasi applicazione che supporta SAML 2.0 può essere integrato direttamente con un tenant di Azure Active Directory mediante [queste istruzioni per aggiungere un'applicazione personalizzata](active-directory-saas-custom-apps.md). Dopo aver verificato che l'integrazione delle applicazioni funziona con Azure Active Directory, inviare le informazioni seguenti per <waadpartners@microsoft.com>.

* Specificare le credenziali per un account o un tenant di test con l'applicazione che può essere utilizzato dal team di Azure Active Directory per testare l'integrazione.  

* Fornire i valori di (servizio consumer asserzione) SAML Sign-On URL, URL emittente (ID entità) e URL di risposta per l'applicazione, come descritto [di seguito](active-directory-saas-custom-apps.md). Se è in genere fornire questi valori come parte di un file di metadati SAML, quindi inviare che anche.

* Fornire una breve descrizione della configurazione di Azure Active Directory come provider di identità in un'applicazione con SAML 2.0. Se l'applicazione supporta la configurazione di Azure AD come provider di identità tramite un portale di amministrazione self-service, quindi verificare le credenziali fornite di sopra figurano la possibilità di impostare.

* Specificare le informazioni seguenti:

> Nome della società:
> 
> Sito Web della società:
> 
> Nome dell'applicazione:
> 
> Descrizione dell'applicazione (limite di 256 caratteri):
> 
> Sito Web dell'applicazione (informativo):
> 
> Sito Web supporto tecnico di applicazione o informazioni di contatto personali:
> 
> URL di abbonamento a un servizio dell'applicazione in cui i clienti passare a iscriversi a e/o acquistare l'applicazione:
> 
> Scegliere fino a tre categorie per l'applicazione venga elencato sotto (per categorie disponibili, vedere [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Allegare applicazione piccola icona (file PNG, 45px da 45px, colore di sfondo a tinta unita):
> 
> Allegare applicazione icone grandi (file PNG, 215px da 215px, colore di sfondo a tinta unita):
> 
> Allegare Logo dell'applicazione (file PNG, 150px da 122px, colore di sfondo trasparente):
