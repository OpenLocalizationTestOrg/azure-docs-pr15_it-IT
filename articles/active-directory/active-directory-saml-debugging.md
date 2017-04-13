<properties 
    pageTitle="Come eseguire il debug basata su SAML single sign-on alle applicazioni di Azure Active Directory | Microsoft Azure" 
    description="Informazioni su come eseguire il debug basata su SAML single sign-on alle applicazioni di Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Come eseguire il debug basata su SAML single sign-on alle applicazioni di Azure Active Directory

Quando il debug un'integrazione basata su SAML applicazione, è spesso utile usare uno strumento come [Fiddler](http://www.telerik.com/fiddler) per visualizzare la richiesta SAML, la risposta SAML e il token SAML emesso per l'applicazione. Esaminando il token SAML, è possibile assicurarsi che tutti gli attributi necessari, il nome utente il cui oggetto SAML e autorità URI provengano come previsto.

![][1]

La risposta da Azure Active Directory che contiene il token SAML è in genere quello che si verifica dopo che un HTTP 302 reindirizzare da https://login.windows.net e viene inviata a configurato **URL di risposta** dell'applicazione. 
 
È possibile visualizzare il token SAML selezionando questa riga, quindi selezionare il **controlli > Web Form** scheda nel Pannello di destra. A questo punto, mouse il valore di **SAMLResponse** e scegliere **Invia a TextWizard**. Selezionare quindi **Base 64** dal menu **di trasformazione** di decodificare il token e visualizzarne il contenuto.
 
**Nota**: per visualizzare il contenuto della richiesta HTTP, Fiddler potrebbe richiedere di configurare decrittografia del traffico HTTPS, sarà necessario eseguire.

## <a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Configurazione single sign-on per le applicazioni che non sono presenti nella raccolta di applicazione di Azure Active Directory](active-directory-saas-custom-apps.md)
- [Personalizzazione delle attestazioni rilasciate in Token SAML per App pre-integrate](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
