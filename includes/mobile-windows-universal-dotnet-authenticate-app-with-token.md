
1. Nel file di progetto MainPage.xaml.cs aggiungere le istruzioni **using** seguenti:

        using System.Linq;      
        using Windows.Security.Credentials;

2. Sostituire il metodo **AuthenticateAsync** con il codice seguente:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    In questa versione di **AuthenticateAsync**, l'app tenta di usare le credenziali memorizzate in **PasswordVault** per accedere al servizio. Un segno di aggiuntivo regolare viene eseguito anche quando è presente alcuna credenziale archiviate.

    >[AZURE.NOTE]Un token nella cache potrebbe essere scaduto e scadenza token può verificarsi anche dopo l'autenticazione quando l'applicazione è in uso. Per informazioni su come determinare se un token è scaduto, vedere [verificare la presenza di token di autenticazione scaduto](http://aka.ms/jww5vp). Per una soluzione di gestione degli errori di autorizzazione relativi ai token alla scadenza, vedere il post di [memorizzazione nella cache e gestiscono i token scaduti in servizi di Windows Azure Mobile gestite SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 

3. Riavviare l'app due volte.

    Si noti che nel primo avvio, effettuare l'accesso con il provider di nuovamente necessario. Tuttavia, al riavvio secondo vengono utilizzate le credenziali memorizzate nella cache e accesso viene ignorato. 
