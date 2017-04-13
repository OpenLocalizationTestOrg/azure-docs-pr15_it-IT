<properties
    pageTitle="Aggiungere l'autenticazione all'app universale Windows piattaforma (UWP) | Azure App per dispositivi mobili"
    description="Informazioni su come usare app di Azure App servizio Mobile per autenticare gli utenti dell'app universale Windows piattaforma (UWP) con una vasta gamma di provider di identità, tra cui: AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Aggiungere l'autenticazione per l'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In questo argomento viene illustrato come aggiungere l'autenticazione basata su cloud per le app per dispositivi mobili. In questa esercitazione, aggiungere l'autenticazione del progetto di Guida introduttiva universale Windows piattaforma (UWP) per App Mobile tramite un provider di identità supportate dal servizio di App Azure. Al termine viene autenticato e autorizzato dal back-end App Mobile, viene visualizzato il valore di ID utente.

In questa esercitazione si basa sulla Guida introduttiva App Mobile. È necessario completare l'esercitazione [Introduzione App Mobile](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio di App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Limitare le autorizzazioni per gli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A questo punto, è possibile verificare che l'accesso anonimo per il back-end è stata disattivata. Con il progetto di app UWP imposta come progetto di avvio, distribuisce ed eseguita l'app; Verificare che viene generata un'eccezione non gestita con un codice di stato di 401 (non autorizzato) dopo l'avvio dell'applicazione. Infatti l'app tenta di accedere al codice App Mobile come un utente autenticato, ma la tabella *TodoItem* ora richiede l'autenticazione.

Successivamente, sarà necessario aggiornare l'app per l'autenticazione degli utenti prima di richiedere risorse dal servizio di App.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

1. Nel UWP app MainPage.cs file di progetto e aggiungere il frammento di codice seguenti per la classe MainPage:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Questo codice autentica l'utente con un account di accesso di Facebook. Se si utilizza un provider di identità diverso da Facebook, impostare il valore di **MobileServiceAuthenticationProvider** sopra il valore relativo al proprio provider.

3. Commento in uscita o eliminare la chiamata al metodo **ButtonRefresh_Click** (o il metodo **InitLocalStoreAsync** ) nell'override del metodo **OnNavigatedTo** esistente. I dati non è caricato prima che l'utente autenticato. Successivamente, si verrà aggiunto un pulsante di **accesso** all'app che attiva l'autenticazione.

4. Aggiungere il frammento di codice seguenti per la classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Aprire il file di progetto MainPage, individuare l'elemento che definisce il pulsante **Salva** e sostituirlo con il codice seguente:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Premere F5 per eseguire l'app, fare clic sul pulsante di **accesso** e accedere all'app con il provider di identità scelto. Dopo il segno aggiuntivo viene eseguito correttamente, l'app viene eseguito senza errori e sarà possibile eseguire una query il back-end ed effettuare aggiornamenti ai dati.


##<a name="tokens"></a>Archiviare il token di autenticazione nel client

Nell'esempio precedente è stato uno standard di accessohttp, che richiede il client di contattare il provider di identità e il servizio di App ogni volta che l'avvio dell'applicazione. Non solo è questo metodo inefficace, è possibile eseguire in utilizzo-si riferisce problemi dovrebbero molti clienti tenta di avviare si app nello stesso momento. Un approccio più efficace consiste nel memorizzare nella cache il token di autorizzazione restituito dal servizio di App e provare a utilizzare questo primo prima di utilizzare un accesso aggiuntivo basato sul provider.

>[AZURE.NOTE]È possibile memorizzare il token emesso dai servizi App indipendentemente dal fatto che si utilizza l'autenticazione client gestiti o servizio gestito. In questa esercitazione utilizza l'autenticazione servizio gestito.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Passaggi successivi

Ora che è stata completata questa esercitazione l'autenticazione di base, è consigliabile continuare a uno dei seguenti esercitazioni:

+ [Aggiungere le notifiche push applicazione in uso.](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Informazioni su come aggiungere push notifiche supportano l'app e configurare il back-end App Mobile per l'utilizzo di Azure notifica hub per inviare le notifiche push.

+ [Attivare la sincronizzazione offline per l'app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Informazioni su come aggiungere l'app utilizzando un back-end App Mobile supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

