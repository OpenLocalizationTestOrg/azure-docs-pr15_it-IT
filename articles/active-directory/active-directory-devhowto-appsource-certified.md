<properties
   pageTitle="Come ottenere AppSource certified per Azure Active Directory | Microsoft Azure"
   description="Informazioni dettagliate su come ottenere l'applicazione AppSource certificati per Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Come ottenere AppSource Certified per Azure Active Directory (AD) 

Per ottenere la certificazione AppSource per Azure Active Directory, l'applicazione necessario implementare l'accesso multi-tenant nel modello con Azure Active Directory utilizzando i protocolli OpenID connettersi, OAuth 2.0 o SAML 2.0. 

Se non si ha familiarità con accesso di Azure Active Directory o sviluppo di applicazioni multi-tenant:

1. Prima di tutto lettura sul [Browser per scenari Web App in scenari di autenticazione per Azure Active Directory][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. Successivamente, estrarre il Azure AD [avvio rapido applicazione web Guide][AAD-QuickStart-Web-Apps], che illustrano come implementare l'accesso ed esempi di codice associato. 

    > [AZURE.TIP] Provare l'anteprima del nuovo [portale per sviluppatori](https://identity.microsoft.com/Docs/Web) che consentono di iniziare a utilizzarlo con Azure Active Directory in pochi minuti!  Portale per sviluppatori di verrà descritto il processo di registrazione di un'app e integrazione del codice di Azure Active Directory.  Al termine, si avrà una semplice applicazione che può eseguire l'autenticazione di utenti al tenant e back-end che possono accettare i token ed eseguire la convalida.

3. Per informazioni su come implementare il criterio di accesso di multi-tenant con Azure Active Directory, vedere [come effettuare l'accesso a tutti gli utenti di Azure Active Directory (AD) utilizzando il modello di applicazione multi-tenant][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>Contenuti correlati
Per ulteriori informazioni sulla creazione di applicazioni che supportano accessohttp Azure Active Directory o per ottenere assistenza e supporto, consultare la [Guida per gli sviluppatori di Azure Active Directory][AAD-Dev-Guide].

Utilizzare la sezione commenti Disqus seguito in questo articolo per inviare commenti e suggerimenti degli perfezionare e definire il contenuto.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










