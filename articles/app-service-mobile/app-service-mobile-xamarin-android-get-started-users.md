<properties
    pageTitle="Iniziare a utilizzare l'autenticazione per le App per dispositivi mobili in Xamarin Android"
    description="Informazioni su come usare le app Mobile autenticare gli utenti dell'app Xamarin Android attraverso una varietà di provider di identità, inclusi AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Aggiungere l'autenticazione all'app Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In questo argomento viene illustrato come autenticare gli utenti di un'App Mobile dall'applicazione client. In questa esercitazione si aggiunge l'autenticazione progetto Guida introduttiva tramite un provider di identità supportate da app Mobile Azure. Al termine viene autenticato e autorizzato nell'App Mobile, viene visualizzato il valore di ID utente.

In questa esercitazione si basa sulla Guida introduttiva App Mobile. È necessario anche completare l'esercitazione [creare un'app Xamarin.Android]. Se non si utilizza il progetto di server introduttive scaricato, è necessario aggiungere il pacchetto di estensione autenticazione al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare servizi di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Visual Studio o Xamarin Studio, eseguire il progetto client in un dispositivo o emulatore. Verificare che viene generata un'eccezione non gestita con un codice di stato di 401 (non autorizzato) dopo l'avvio dell'applicazione. Infatti l'app tenta di accedere al back-end App Mobile come un utente autenticato. La tabella *TodoItem* richiede l'autenticazione.

Successivamente, si aggiornerà l'applicazione client per richiedere le risorse da back-end App Mobile con un utente autenticato.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

L'app viene aggiornato per richiedere agli utenti di toccare il pulsante di **accesso** e autenticare prima di visualizzazione dei dati.

1. Aggiungere il codice seguente alla classe **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    In questo modo si crea un nuovo metodo di autenticazione utente e un gestore del metodo per un nuovo pulsante di **accesso** . Nell'esempio precedente viene autenticato tramite un account di accesso di Facebook. Una finestra di dialogo viene utilizzata per visualizzare l'ID utente dopo l'autenticazione.

    > [AZURE.NOTE] Se si utilizza un provider di identità diverso da Facebook, modificare il valore passato a **LoginAsync** sopra a una delle operazioni seguenti: _MicrosoftAccount_, _Twitter_, _Google_o _WindowsAzureActiveDirectory_.

3. Metodo **OnCreate** , eliminare o commento-esterna per una riga di codice seguente:

        OnRefreshItemsSelected ();

4. Nel file Activity_To_Do.axml, aggiungere la definizione di pulsante *LoginUser* seguente prima di pulsante *AddItem* esistente:

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Aggiungere il file di risorse Strings l'elemento seguente:

        <string name="login_button_text">Sign in</string>

6. In Visual Studio o Xamarin Studio, eseguire il progetto client in un dispositivo o emulatore e accedere con il provider di identità scelto.

    Quando si è eseguito l'accesso in correttamente, l'app visualizzerà l'ID di accesso e l'elenco degli elementi todo ed è possibile effettuare aggiornamenti ai dati.


<!-- URLs. -->
[Creare un'app Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
