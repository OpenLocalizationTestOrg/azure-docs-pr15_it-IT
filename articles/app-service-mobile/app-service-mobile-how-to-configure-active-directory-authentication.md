<properties
    pageTitle="Come configurare l'autenticazione di Azure Active Directory per l'applicazione Servizi di App"
    description="Informazioni su come configurare l'autenticazione di Azure Active Directory per l'applicazione Servizi di App."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Come configurare l'applicazione di servizio di App per utilizzare le credenziali Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare i servizi di Azure App per l'utilizzo di Azure Active Directory come un provider di autenticazione.

## <a name="express"> </a>Configura Azure Active Directory con le impostazioni express

13. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni**e quindi su **Autenticazione/autorizzazione**.

14. Se l'autenticazione / autorizzazione caratteristica non è abilitata, l'opzione su **On**.

15. Fare clic su **Azure Active Directory**e quindi fare clic su **Express** in **Modalità di gestione**.

16. Fare clic su **OK** per registrare l'applicazione di Azure Active Directory. Verrà creata una nuova registrazione. Se si desidera scegliere invece di una registrazione esistente, fare clic su **Seleziona un'app esistente** e quindi cercare il nome di una registrazione creato in precedenza all'interno del tenant.
Fare clic su registrazione per selezionarla e fare clic su **OK**. Quindi scegliere **OK** e l'impostazioni Azure Active Directory.

    ![][0]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

17. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da Azure Active Directory, impostare **l'azione da eseguire quando non viene autenticato richiesta** per **accedere con Azure Active Directory**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate a Azure Active Directory per l'autenticazione.

17. Fare clic su **Salva**.

A questo punto si è pronti a utilizzare Azure Active Directory per l'autenticazione nell'app.

## <a name="advanced"> </a>(Metodo alternativo) configurare manualmente Azure Active Directory con impostazioni avanzate
È anche possibile scegliere di specificare le impostazioni di configurazione manuale. Questa è la soluzione migliore se il tenant AAD che si desidera utilizzare è la differenza tra tenant con cui accedere a Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione di Azure Active Directory e quindi specificare alcuni dei dettagli di registrazione al servizio di App.

### <a name="register"> </a>Registrare l'applicazione con Azure Active Directory

1. Accedere al [portale di Azure]e passare all'applicazione. Copiare l' **URL**. Si utilizzerà per configurare l'app di Azure Active Directory.

3. Accedere al [portale classica Azure] e passare a **Active Directory**.

    ![][2]

4. Selezionare la directory e quindi selezionare la scheda **applicazioni** nella parte superiore. Fare clic su **Aggiungi** nella parte inferiore per creare una nuova registrazione di app.

5. Fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**.

6. Applicazione guidata immettere un **nome** per l'applicazione e fare clic sul tipo di **API Web e/o di applicazione Web** . Fare clic su per continuare.

7. Nella casella **URL di accesso via** incollare l'URL di applicazione copiato in precedenza. Immettere l'URL stesso nella finestra di **App ID URI** . Fare clic su per continuare.

8. Dopo aver aggiunto l'applicazione, fare clic sulla scheda **Configura** . Modificare l' **URL di risposta** in **Single Sign-on** per essere l'URL dell'applicazione con il percorso _/.auth/login/aad/callback_. Ad esempio `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Verificare che si usa lo schema HTTPS.

    ![][3]

9. Fare clic su **Salva**. Copiare l' **ID Client** per l'app. È necessario configurare l'applicazione di utilizzare tale in un secondo momento.

10. Nella barra dei comandi nella parte inferiore fare clic su **Visualizza i punti finali**, quindi copiare l'URL del **Documento di metadati federazione** e scaricare il documento o spostarsi su di esso in un browser.

11. All'interno dell'elemento **EntityDescriptor** radice, deve essere un attributo **entityID** del modulo `https://sts.windows.net/` seguito da un GUID specifico per il tenant (denominato "ID tenant"). Copiare il valore - servite come **URL emittente**. È necessario configurare l'applicazione di utilizzare tale in un secondo momento.

### <a name="secrets"> </a>Le informazioni di aggiunta di Azure Active Directory all'applicazione

13. Nel [portale di Azure], passare all'applicazione. Fare clic su **Impostazioni**e quindi su **Autenticazione/autorizzazione**.

14. Se la caratteristica di autenticazione/autorizzazione non è abilitata, l'opzione su **On**.

15. Fare clic su **Azure Active Directory**e quindi fare clic su **Impostazioni avanzate** in **Modalità di gestione**. Incollare il valore di ID Client e l'URL emittente che è stato acquistato in precedenza. Fare clic su **OK**.

    ![][1]

    Per impostazione predefinita, App servizio fornisce l'autenticazione, ma non limitare l'accesso autorizzato a contenuto del sito e le API. È necessario autorizzare utenti nel codice app.

17. (Facoltativo) Per limitare l'accesso al sito per solo gli utenti autenticati da Azure Active Directory, impostare **l'azione da eseguire quando non viene autenticato richiesta** per **accedere con Azure Active Directory**. È necessario che tutte le richieste di autenticazione e tutte le richieste non autenticate vengono reindirizzate a Azure Active Directory per l'autenticazione.

17. Fare clic su **Salva**.

A questo punto si è pronti a utilizzare Azure Active Directory per l'autenticazione nell'app.

## <a name="optional-configure-a-native-client-application"></a>(Facoltativo) Configurare un'applicazione client nativo

Azure Active Directory consente inoltre di registrare native client, che offre maggiore controllo sul mapping delle autorizzazioni. È necessario se si desidera eseguire gli account di accesso con una raccolta, ad esempio **Active Directory Authentication Library**.

1. Passare a **Active Directory** del [portale classica Azure].

2. Selezionare la directory e quindi selezionare la scheda **applicazioni** nella parte superiore. Fare clic su **Aggiungi** nella parte inferiore per creare una nuova registrazione di app.

3. Fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**.

4. Applicazione guidata immettere un **nome** per l'applicazione e fare clic sul tipo di **Applicazione Client nativa** . Fare clic su per continuare.

5. Nella casella **Reindirizzare URI** immettere endpoint _/.auth/login/done_ del sito, utilizzando lo schema HTTPS. Questo valore deve essere simile a _https://contoso.azurewebsites.net/.auth/login/done_. Se si crea un'applicazione di Windows, utilizzare il [pacchetto SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.

6. Dopo aver aggiunto l'applicazione nativa, fare clic sulla scheda **Configura** . Individuazione dell' **ID Client** e prendere nota di questo valore.

7. Scorrere fino alla sezione **autorizzazioni per le altre applicazioni** e fare clic su **Aggiungi applicazione**.

8. Cercare l'applicazione web che registrata in precedenza e fare clic sull'icona più. Fare clic su controllo per chiudere la finestra di dialogo. Se l'applicazione web non viene trovato, passare alla relativa registrazione e aggiungere un nuovo URL di risposta (ad esempio, la versione HTTP dell'URL di corrente), fare clic su Salva e quindi ripetere questi passaggi: l'applicazione deve apparire nell'elenco.

9. Nella nuova voce che appena aggiunto, aprire l'elenco a discesa **Autorizzazioni di delega** e selezionare **accesso (NomeApplicazione)**. Fare clic su **Salva**.

È stato configurato un'applicazione client nativi che può accedere all'applicazione di servizio di App.

## <a name="related-content"> </a>Contenuto correlato

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
[Portale classica Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced
