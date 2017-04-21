
1. Aprire il file di progetto condiviso MainPage.cs e aggiungervi classe MainPage il frammento di codice seguenti:
    
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

3. Commento in uscita o eliminare la chiamata al metodo **RefreshTodoItems** nell'override del metodo **OnNavigatedTo** esistente.

    I dati non è caricato prima che l'utente autenticato. Successivamente, si verrà aggiunto un pulsante di **accesso** all'app che attiva l'autenticazione.

4. Aggiungere il frammento di codice seguenti per la classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Nel progetto app di Windows Store, aprire il file di progetto MainPage e aggiungere il seguente elemento **pulsante** prima dell'elemento che definisce il pulsante **Salva** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Nel progetto app Store di Windows Phone, aggiungere il seguente elemento **pulsante** nella **ContentPanel**, dopo l'elemento di **casella di testo** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Aprire il file di progetto App.xaml.cs condiviso e aggiungere il codice seguente:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Se il metodo **OnActivated** esiste già, è sufficiente aggiungere il `#if...#endif` blocco di codice.

9. Premere F5 per eseguire l'app di Windows Store, fare clic sul pulsante di **accesso** e accedere all'app con il provider di identità scelto. 

    Quando si è eseguito l'accesso in correttamente, l'app deve essere eseguita senza errori e dovrebbe essere possibile eseguire una query il back-end ed effettuare aggiornamenti ai dati.

10. Fare clic sul progetto app Store di Windows Phone, fare clic su **Imposta come progetto di avvio**, quindi ripetere il passaggio precedente per verificare che l'archivio di Windows Phone app anche viene eseguito correttamente.  

 