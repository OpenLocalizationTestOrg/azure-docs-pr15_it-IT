<properties
    pageTitle="Come configurare l'autenticazione di Twitter per l'applicazione Servizi di App"
    description="Informazioni su come configurare l'autenticazione di Twitter per l'applicazione Servizi di App."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Come configurare l'applicazione di servizio App a utilizzare accesso Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare il servizio di App Azure per utilizzare Twitter come un provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Twitter che contiene un numero di telefono e indirizzo di posta elettronica verificato. Per creare un nuovo account Twitter, passare a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrare l'applicazione di Twitter


1. Accedere al [portale di Azure]e passare all'applicazione. Copiare l' **URL**. Si utilizzerà per configurare l'app Twitter.

2. Passare al sito Web [Gli sviluppatori Twitter] , accedere con le credenziali dell'account Twitter e fare clic su **Crea nuova App**.

3. Digitare il **nome** e una **Descrizione** per la nuova app. Incollare **URL** per il valore di **sito Web dell'applicazione** . Quindi, per l' **URL di Callback**, incollare l' **URL Callback** copiato in precedenza. Si tratta del gateway App Mobile aggiunto con il percorso, _/.auth/login/twitter/callback_. Ad esempio `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Verificare che si usa lo schema HTTPS.

3.  Nella parte inferiore della pagina, leggere e accettare le condizioni. Fare clic su **Crea l'applicazione Twitter**. In questo modo registrata app Visualizza i dettagli dell'applicazione.

4. Fare clic sulla scheda **Impostazioni** , selezionare **Consenti per essere usati per accedere con Twitter questa applicazione**, quindi fare clic su **Impostazioni di aggiornamento**.

5. Selezionare la scheda **tasti e token di accesso** . Prendere nota dei valori di **Chiave Consumer (API chiave)** e **segreto Consumer (API segreto)**.

    > [AZURE.NOTE] Il segreto consumer è una credenziali di sicurezza importanti. Non condividere questa segreto con chiunque o distribuire con l'app.


## <a name="secrets"> </a>Aggiungere Twitter informazioni all'applicazione

13. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni**e quindi **autenticazione / autorizzazione**.

14. Se l'autenticazione / autorizzazione caratteristica non è abilitata, l'opzione su **On**.

15. Fare clic su **Twitter**. Incollare l'ID di App e App segreto valori che è stato acquistato in precedenza. Fare clic su **OK**.

    ![][1]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

17. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da Twitter, impostare **l'azione da eseguire quando richiesta non viene autenticato** su **Twitter**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate a Twitter per l'autenticazione.

17. Fare clic su **Salva**.

A questo punto si è pronti a utilizzare Twitter per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuto correlato

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Sviluppatori di Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portale di Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
