<properties
    pageTitle="Come configurare l'autenticazione dell'Account Microsoft per l'applicazione Servizi di App"
    description="Informazioni su come configurare l'autenticazione dell'Account Microsoft per l'applicazione Servizi di App."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Come configurare l'applicazione di servizio di App per utilizzare Microsoft Account di accesso

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare il servizio di App Azure per usare l'Account Microsoft come provider di autenticazione. 

## <a name="register-microsoft-account"> </a>Registrare l'app con Account Microsoft

1. Accedere al [portale di Azure]e passare all'applicazione. Copiare l' **URL**, che in un secondo momento è utilizzato per configurare l'app con Account Microsoft.

2. Passare alla pagina [Applicazioni My] Account Microsoft Developer Center e accedere con l'account Microsoft, se necessario.

3. Fare clic su **Aggiungi un'app**, quindi digitare un nome dell'applicazione e fare clic su **Crea applicazione**.

4. Prendere nota dell' **ID applicazione**, come sarà necessario in un secondo momento. 

5. In "Piattaforme", fare clic su **Aggiungi piattaforma** e selezionare "Web".

6. In "Reindirizzare URI" fornire l'endpoint dell'applicazione e quindi fare clic su **Salva**. 
 
    >[AZURE.NOTE]Il reindirizzamento URI è l'URL dell'applicazione con il percorso _/.auth/login/microsoftaccount/callback_. Ad esempio `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Verificare che si usa lo schema HTTPS.

7. In "Informazioni riservate applicazione", fare clic su **Genera nuova Password**. Prendere nota del valore che viene visualizzato. Una volta che si lascia la pagina, non essere visualizzato nuovamente.


    > [AZURE.IMPORTANT] La password è una credenziale di sicurezza importanti. Non condividere la password con chiunque o distribuire un'applicazione client.

## <a name="secrets"> </a>Informazioni Aggiungi Account Microsoft per l'applicazione di servizio di App

1. Indietro nel [portale di Azure], passare all'applicazione, fare clic su **Impostazioni** > **autenticazione / autorizzazione**.

2. Se l'autenticazione / autorizzazione caratteristica non è abilitata, passare **in**.

3. Fare clic su **Account Microsoft**. Incollare i valori di ID dell'applicazione e la Password che è stato acquistato in precedenza e se lo si desidera abilitare tutti gli ambiti che richiede l'applicazione. Fare clic su **OK**.

    ![][1]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

4. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da account Microsoft, impostare **l'azione da eseguire quando richiesta non viene autenticato** **all'Account Microsoft**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate all'account Microsoft per l'autenticazione.

5. Fare clic su **Salva**.

A questo punto si è pronti usare l'Account Microsoft per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuto correlato

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Le applicazioni]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portale di Azure]: https://portal.azure.com/
