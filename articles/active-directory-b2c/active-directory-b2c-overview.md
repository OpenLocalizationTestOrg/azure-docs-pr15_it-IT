<properties
    pageTitle="Azure Active Directory B2C: Panoramica | Microsoft Azure"
    description="Sviluppo di applicazioni consumer esposto con Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure B2C Directory attiva: Iscriversi e consumatori nelle applicazioni l'accesso

Azure B2C Directory Active è una soluzione di gestione di identità cloud completo per il web esposto consumer e applicazioni per dispositivi mobili. Si tratta di un servizio globale disponibilità che scale centinaia di milioni di identità consumer. Basato su una piattaforma aziendale sicura, Azure Active Directory B2C mantiene le applicazioni, l'azienda e i consumatori protetti.

In passato, gli sviluppatori di applicazioni che desiderano iscriversi e consumatori nelle applicazioni l'accesso dovrebbe scrivere il proprio codice. E sarebbe utilizzate sistemi o database locale per archiviare i nomi utente e password. Azure B2C Directory Active offre un modo migliore per integrare nelle applicazioni con l'aiuto di una piattaforma sicura e basate su standard e una vasta gamma di extensible criteri di gestione delle identità consumer. Quando si utilizza Azure Active Directory B2C, i consumatori iscriversi per ottenere le applicazioni utilizzando il proprio account di social networking esistente (Facebook, Google, Amazon, LinkedIn) o tramite la creazione di nuove credenziali (indirizzo di posta elettronica e password, o il nome utente e password); si chiama quest'ultimo "account locali."

## <a name="get-started"></a>Guida introduttiva

Per creare un'applicazione che accetta consumer iscrizione e accesso, è necessario prima necessario registrare l'applicazione di un B2C di Azure Active Directory del tenant. È possibile ottenere il proprio tenant usando la procedura descritta in [creare un tenant di Azure Active Directory B2C](active-directory-b2c-get-started.md).

Se si sceglie di inviare messaggi di protocollo direttamente tramite [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) o [Connettersi ID aperto](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), o utilizzando le raccolte per svolgere il lavoro dell'utente, è possibile scrivere un'applicazione in base al servizio di Azure Active Directory B2C. Scegliere la piattaforma preferita nella tabella seguente e iniziare a.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Quali sono le novità

Seleziona qui spesso per conoscere le modifiche successive il B2C di Azure Active Directory. È necessario anche tweet sugli eventuali aggiornamenti utilizzando @AzureAD.

- Informazioni sulla [politica extensible](active-directory-b2c-reference-policies.md) nostro e sui tipi di criteri che è possibile creare e utilizzare nelle applicazioni.
- Aggiungere un segnalibro il [blog di servizio](https://blogs.msdn.microsoft.com/azureadb2c/) per le notifiche su problemi del servizio secondarie, aggiornamenti, stato e riduzioni disponibili. Continuare a monitorare anche il [dashboard di stato Azure](https://azure.microsoft.com/status/) .
- Corrente [vincoli, restrizioni e limitazioni del servizio](active-directory-b2c-limitations.md).
- Infine, un [codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) con Azure Active Directory B2C & ASP.NET di base.

## <a name="how-to-articles"></a>Pratici

Informazioni su come usare le funzionalità di Azure Active Directory B2C specifiche:

- Configurare gli account di [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [account Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)e [LinkedIn](active-directory-b2c-setup-li-app.md) da utilizzare nelle applicazioni consumer esposto.
- [Gli attributi personalizzati di utilizzo per raccogliere informazioni sui consumatori](active-directory-b2c-reference-custom-attr.md).
- [Autenticazione a più fattori Azure abilitare nelle applicazioni consumer esposto](active-directory-b2c-reference-mfa.md).
- [Configurare la modalità self-service reimpostazione della password per i clienti](active-directory-b2c-reference-sspr.md).
- [Personalizzare l'aspetto di iscrizione, accedi e altre pagine consumer](active-directory-b2c-reference-ui-customization.md) provenienti da Azure Active Directory B2C.
- [Usare l'API di grafico Azure Active Directory per creare, leggere, aggiornare ed eliminare gli utenti](active-directory-b2c-devquickstarts-graph-dotnet.md) nel tenant di Azure Active Directory B2C.

## <a name="next-steps"></a>Passaggi successivi

Tali collegamenti saranno utili per esplorare il servizio in antero-posteriore:

- Visualizzare le [informazioni sui prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- È possibile ottenere assistenza in Overflow dello Stack utilizzando [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal) tag.
- Fornire le riflessioni utilizzando [Voce utente](https://feedback.azure.com/forums/169401-azure-active-directory/)-Vogliamo sentire! Utilizzare la frase "AzureADB2C:" nel titolo del post in modo che è possibile trovare.
- Esaminare il [riferimento al protocollo B2C di Azure Active Directory](active-directory-b2c-reference-protocols.md).
- Esaminare il [riferimento Token di Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).
- Leggere le [domande frequenti B2C Azure Active Directory](active-directory-b2c-faqs.md).
- [File supportano le richieste di Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
