<properties
    pageTitle="Iniziare a utilizzare l'autenticazione per App Mobile in Xamarin iOS"
    description="Informazioni su come usare le app Mobile autenticare gli utenti dell'app iOS Xamarin tramite un'ampia gamma di provider di identità, inclusi AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Aggiungere l'autenticazione all'app Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In questo argomento viene illustrato come autenticare gli utenti di un'App Mobile servizio App dall'applicazione client. In questa esercitazione, aggiungere l'autenticazione del progetto di Guida introduttiva Xamarin.iOS tramite un provider di identità è supportato dal servizio di App. Una volta che viene correttamente autenticato autorizzati all'applicazione Mobile, verrà visualizzato il valore di ID utente e sarà possibile accedere ai dati di tabella con restrizioni.

È necessario completare l'esercitazione [creare un'app Xamarin.iOS]. Se non si utilizza il progetto di server introduttive scaricato, è necessario aggiungere il pacchetto di estensione autenticazione al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrare l'app per l'autenticazione e configurare servizi di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. in Visual Studio o Xamarin Studio, eseguire il progetto client in un dispositivo o emulatore. Verificare che viene generata un'eccezione non gestita con un codice di stato di 401 (non autorizzato) dopo l'avvio dell'applicazione. L'errore è stato effettuato l'accesso alla console di verrà. In Visual Studio, pertanto verrà visualizzato l'errore nella finestra di output.

&nbsp;&nbsp;Questo errore effettuando l'accesso non autorizzato avviene perché l'app tenta di accedere al back-end App Mobile come un utente autenticato. La tabella *TodoItem* richiede l'autenticazione.

Successivamente, si aggiornerà l'applicazione client per richiedere le risorse da back-end App Mobile con un utente autenticato.

##<a name="add-authentication-to-the-app"></a>Aggiungere l'autenticazione all'app

In questa sezione verrà modificato l'app per visualizzare una schermata di accesso prima di visualizzare dati. Quando si avvia l'app, non si connetterà non del servizio di App e non vengono visualizzati tutti i dati. Dopo la prima volta che l'utente esegue la combinazione di aggiornamento, verrà visualizzata la schermata di accesso. Dopo l'accesso con esito positivo verrà visualizzato l'elenco degli elementi todo.

1. Nel progetto client, aprire il file **QSTodoService.cs** e aggiungere la seguente istruzione e `MobileServiceUser` con funzione di accesso per la classe QSTodoService:

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Aggiungere nuovo metodo denominato **autentica** a **QSTodoService** con la definizione seguente:


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Se si utilizza un provider di identità diverso da un Facebook, modificare il valore passato a **LoginAsync** sopra a una delle operazioni seguenti: _MicrosoftAccount_, _Twitter_, _Google_o _WindowsAzureActiveDirectory_.

3. Aprire **QSTodoListViewController.cs**. Modificare la definizione del metodo di **ViewDidLoad** rimuovere la chiamata a **RefreshAsync()** verso la fine:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modificare il metodo **RefreshAsync** per eseguire l'autenticazione se la proprietà **dell'utente** è null. Aggiungere il codice seguente nella parte superiore della definizione del metodo:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. In Visual Studio o Xamarin Studio connessa l'Host creare Xamarin nel Mac, eseguire il progetto client un dispositivo o emulatore. Verificare che l'app viene visualizzato senza dati.

    Eseguire il pull e verso il basso l'elenco degli elementi che impedirà la schermata di accesso visualizzare la combinazione di aggiornamento. Dopo aver immesso correttamente credenziali valide, l'app verrà visualizzato l'elenco degli elementi todo ed è possibile effettuare aggiornamenti ai dati.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Creare un'app Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
