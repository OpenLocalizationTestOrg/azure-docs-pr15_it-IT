<properties
    pageTitle="Come attivare la pubblicazione di applicazioni client nativi con le applicazioni proxy | Microsoft Azure"
    description="Viene illustrato come attivare le applicazioni client nativi comunicare con Azure Active Directory applicazione Proxy Connector per fornire l'accesso remoto sicuro alle App locale."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Come abilitare applicazioni client nativi interagire con proxy applicazioni

Azure Active Directory applicazione Proxy usato per pubblicare applicazioni browser, ad esempio SharePoint e Outlook Web Access personalizzata applicazioni line-of business. Può inoltre essere utilizzato per pubblicare App native client, che si differenziano da web apps poiché vengono installati in un dispositivo. In tal caso, il supporto di Azure Active Directory rilasciato token inviati nelle intestazioni di autorizzare HTTP standard.

![Relazione tra gli utenti finali, Azure Active Directory e le applicazioni pubblicate](./media/active-directory-application-proxy-native-client/richclientflow.png)

Il metodo consigliato per la pubblicazione di tali applicazioni è utilizzare la libreria di autenticazione Azure Active Directory, che si occupa di tutti i problemi di autenticazione e supporta molti ambienti cliente diverso. Proxy di applicazione si integra con l' [Applicazione nativa a uno scenario di API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). Il processo a tale scopo è come indicato di seguito:

## <a name="step-1-publish-your-application"></a>Passaggio 1: Pubblicare l'applicazione

Pubblicare l'applicazione proxy come si farebbe con qualsiasi altra applicazione, assegnare agli utenti e dare loro premium o base licenze. Per ulteriori informazioni, vedere [le applicazioni di pubblicazione con Proxy dell'applicazione](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Passaggio 2: Configurare l'applicazione

Configurare un'applicazione nativa come segue:

1. Accedere al portale di classica Azure.
2. Selezionare l'icona di Active Directory nel menu a sinistra e quindi selezionare la directory.
3. Nel menu superiore fare clic su **applicazioni**. Se nessuna applicazione sono stati aggiunti alla directory, questa pagina verrà mostrati solo il collegamento **Aggiungi un'App** . Fare clic sul collegamento o in alternativa è possibile fare clic sul pulsante **Aggiungi** nella barra dei comandi.
4. Nella pagina **quali si desidera eseguire** fare clic sul collegamento per **aggiungere un'applicazione di sviluppo di organizzazione**.
5. Nella pagina **inviare commenti e suggerimenti sull'applicazione** specificare un nome per l'applicazione e scegliere **l'applicazione client nativi**. Fare clic sull'icona freccia per continuare.
6. Nella pagina **informazioni sull'applicazione** fornire **Reindirizzare URI** dell'applicazione client nativo, quindi fare clic sul segno di spunta per completare.

L'applicazione è stato aggiunto e verrà applicato alla pagina Guida introduttiva per l'applicazione.

## <a name="step-3-grant-access-to-other-applications"></a>Passaggio 3: Concedere l'accesso a altre applicazioni

Attivare l'applicazione nativa per esporre ad altre applicazioni nella directory:

1. Nel menu superiore, fare clic su **applicazioni**, selezionare la nuova applicazione nativa e quindi fare clic su **Configura**.
2. Scorrere fino alla sezione **autorizzazioni per le altre applicazioni** . Fare clic sul pulsante **Aggiungi applicazione** e selezionare l'applicazione del proxy che si desidera concedere l'accesso applicazione nativa all'e fare clic sul segno di spunta nell'angolo inferiore destro. Dal menu **Delegato autorizzazioni** selezionare l'autorizzazione di nuova.

![Autorizzazioni per la schermata altre applicazioni - aggiungere applicazione](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passaggio 4: Modifica Active Directory Authentication Library

Modificare il codice di applicazione nativa nel contesto di autenticazione della Active Directory autenticazione raccolta (ADAL) per includere i seguenti:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Le variabili devono essere sostituite come indicato di seguito:

- **TenantId** sono disponibili nel GUID nell'URL della pagina di **configurazione** dell'applicazione, subito dopo "/ Directory /".
- **URL front-end** è l'URL di front-end immesso nell'applicazione Proxy e sono disponibili nella pagina **configurazione** dell'applicazione proxy.
- **Id Client** dell'applicazione nativa sono disponibili nella pagina **Configura** dell'applicazione nativa.
- **Reindirizzare URI dell'applicazione nativa** sono disponibili nella pagina **Configura** dell'applicazione nativa.

![Nuova applicazione nativa configurare schermata della pagina](./media/active-directory-application-proxy-native-client/new_native_app.png)

Per ulteriori informazioni sullo svolgimento di applicazione nativa, vedere [applicazione nativa per dell'API web](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Vedere anche

- [Pubblicare le applicazioni che utilizzano il nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Utilizzo di applicazioni sulle attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
