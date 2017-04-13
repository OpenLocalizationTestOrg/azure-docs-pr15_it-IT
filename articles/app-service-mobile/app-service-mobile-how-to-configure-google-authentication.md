<properties
    pageTitle="Come configurare l'autenticazione di Google per l'applicazione Servizi di App"
    description="Informazioni su come configurare l'autenticazione di Google per l'applicazione Servizi di App."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Come configurare l'applicazione di servizio App a utilizzare accesso di Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare il servizio di App Azure per usare Google come un provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Google che contiene un indirizzo di posta elettronica verificato. Per creare un nuovo account di Google, passare a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrare l'applicazione di Google

1. Accedere al [portale di Azure]e passare all'applicazione. Copiare l' **URL**, che è utilizzato in un secondo momento per configurare l'app Google.

2. Passare al sito Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , accedere con le credenziali dell'account di Google, fare clic su **Crea progetto**, specificare un **nome del progetto**, quindi fare clic su **Crea**.

3. Selezionare **Google + API** e quindi **abilitare** **API di social networking** .

4. Nel riquadro di spostamento sinistro, **credenziali** > **OAuth consenso dello schermo**, quindi selezionare l' **indirizzo di posta elettronica**, immettere un **Nome prodotto**e fare clic su **Salva**.

5. Nella scheda **credenziali** , fare clic su **Crea credenziali** > **ID client OAuth**, quindi selezionare **l'applicazione Web**.

6. Incollare l' App servizio **URL** copiato precedentemente **Autorizzato origini JavaScript**, quindi incollare il reindirizzamento URI **Autorizzato URI reindirizzare la chiamata**. Reindirizza URI è l'URL dell'applicazione con il percorso _/.auth/login/google/callback_. Ad esempio `https://contoso.azurewebsites.net/.auth/login/google/callback`. Verificare che si usa lo schema HTTPS. Fare clic su **Crea**.

7. Nella schermata successiva, prendere nota dei valori dell'ID client e di segreto del client.


    > [AZURE.IMPORTANT]
    Il segreto client è una credenziali di sicurezza importanti. Non condividere questa segreto con chiunque o distribuire un'applicazione client.


## <a name="secrets"> </a>Le informazioni di Google aggiungere all'applicazione

8. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni**e quindi **autenticazione / autorizzazione**.

9. Se l'autenticazione / autorizzazione caratteristica non è abilitata, l'opzione su **On**.

10. Fare clic su **Google**. Incollare i valori ID App e segreto App che è stato acquistato in precedenza e se lo si desidera abilitare tutti gli ambiti che richiede l'applicazione. Fare clic su **OK**.

    ![][1]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

17. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da Google, impostare **l'azione da eseguire quando richiesta non viene autenticato** su **Google**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate a Google per l'autenticazione.

12. Fare clic su **Salva**.

A questo punto si è pronti a utilizzare Google per l'autenticazione nell'app.

## <a name="related-content"> </a>Contenuto correlato

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portale di Azure]: https://portal.azure.com/

