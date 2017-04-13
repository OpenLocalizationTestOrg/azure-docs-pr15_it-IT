<properties
    pageTitle="Come configurare l'autenticazione di Facebook per l'applicazione Servizi di App"
    description="Informazioni su come configurare l'autenticazione di Facebook per l'applicazione Servizi di App."
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

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Come configurare l'applicazione di servizio App a utilizzare accesso Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare il servizio di App Azure per l'utilizzo di Facebook come un provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook che contiene un indirizzo di posta elettronica verificato e un numero di telefono cellulare. Per creare un nuovo account di Facebook, passare a [facebook.com].

## <a name="register"> </a>Registrare l'applicazione con Facebook

1. Accedere al [portale di Azure]e passare all'applicazione. Copiare l' **URL**. Si utilizzerà per configurare l'app Facebook.

2. In un'altra finestra del browser, passare al sito Web [Gli sviluppatori di Facebook] ed effettuare l'accesso con le credenziali dell'account di Facebook.

3. (Facoltativo) Se dispone non è registrato, fare clic su **App** > **registrare come uno sviluppatore**, quindi accettare i criteri e seguire la procedura di registrazione.

4. Fare clic su **App personali** > **aggiungere una nuova App** > **sito Web** > **ignorare e creare App ID**. 

5. In **Nome visualizzato**digitare un nome univoco per l'app, digitare il **Messaggio di posta elettronica contatto**, scegliere una **categoria** per l'app, quindi fare clic su **Crea ID App** e completare il controllo di sicurezza. Verrà visualizzato il dashboard di sviluppo per la nuova app Facebook.

6. In "Facebook Login", fare clic su **Per iniziare**. Aggiungere dell'applicazione **Reindirizzare URI** reindirizzare **OAuth valido URI**, quindi fare clic su **Salva modifiche**. 

    > [AZURE.NOTE] Il reindirizzamento URI è l'URL dell'applicazione con il percorso _/.auth/login/facebook/callback_. Ad esempio `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Verificare che si usa lo schema HTTPS.

6. Nel riquadro di spostamento sinistra, fare clic su **Impostazioni**. Nel campo **Segreto App** , fare clic su **Mostra**, immettere la password se richiesto, quindi prendere nota dei valori degli **ID App** e **App segreto**. Utilizzare questi in un secondo momento per configurare l'applicazione in Azure.

    > [AZURE.IMPORTANT] Il segreto app è una credenziali di sicurezza importanti. Non condividere questa segreto con chiunque o distribuire un'applicazione client.

7. L'account di Facebook che è stato utilizzato per registrare l'applicazione è un amministratore dell'app. A questo punto, solo gli amministratori possono accedere a questa applicazione. Per eseguire l'autenticazione di altri account di Facebook, fare clic su **Revisione App** e abilitare **< your di nome app > Rendi pubblico** abilitare l'accesso pubblico generale mediante l'autenticazione di Facebook.

## <a name="secrets"> </a>Le informazioni di Facebook aggiungere all'applicazione

1. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni** > **autenticazione / autorizzazione**e assicurarsi che **L'autenticazione del servizio App** sia **attivata**.

2. Fare clic su **Facebook**, incollare i valori ID App e segreto App che è stato acquistato in precedenza, se lo si desidera abilitare tutti gli ambiti necessari per l'applicazione, quindi fare clic su **OK**.

    ![][0]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

3. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da Facebook, impostare **l'azione da eseguire quando richiesta non viene autenticato** a **Facebook**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate a Facebook per l'autenticazione.

4. Al termine l'autenticazione di configurazione, fare clic su **Salva**.

A questo punto si è pronti a utilizzare Facebook per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuto correlato

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Sviluppatori di Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portale di Azure]: https://portal.azure.com/
