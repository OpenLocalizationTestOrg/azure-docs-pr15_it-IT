
Nell'esempio precedente è stato uno standard di accessohttp, che richiede il client di contattare il provider di identità e il servizio di Azure back-end ogni volta che l'avvio dell'applicazione. Non solo questo metodo è inefficace, che possono verificarsi problemi di utilizzo devono molti clienti tenta di avviare si app nello stesso momento. Un approccio più efficace consiste nel memorizzare nella cache il token di autorizzazione restituito dal servizio Azure e provare a utilizzare questo primo prima di utilizzare un accesso aggiuntivo basato sul provider. 

>[AZURE.NOTE]È possibile memorizzare i token emessi back-end Azure servizio indipendentemente dal fatto che si utilizza l'autenticazione client gestiti o servizio gestito. In questa esercitazione utilizza l'autenticazione servizio gestito.


1. Aprire il file ToDoActivity.java e aggiungere le seguenti istruzioni di importazione:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Aggiungere i membri seguenti per la `ToDoActivity` classe.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. Nel file ToDoActivity.java aggiungere la seguente definizione per il `cacheUserToken` metodo.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Questo metodo archivia l'id utente e il token in un file delle preferenze contrassegnati come privato. Questa operazione deve proteggere l'accesso alla cache in modo da altre applicazioni sul dispositivo non hanno accesso al token poiché la preferenza è in modalità sandbox per l'app. Tuttavia, se un utente accede al dispositivo, è possibile in cui possono accedono alla cache token in altro modo. 

    >[AZURE.NOTE]È possibile proteggere ulteriormente il token con la crittografia se token accesso ai dati altamente confidenziali e un utente può accedere al dispositivo. Tuttavia, una soluzione completamente protetta è lo scopo di questa esercitazione e dipendente ai propri requisiti di sicurezza.


4. Nel file ToDoActivity.java aggiungere la seguente definizione per il `loadUserTokenCache` metodo.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. Nel file *ToDoActivity.java* sostituire il `authenticate` metodo con il metodo seguente che utilizza una cache token. Modificare il provider di accesso se si desidera utilizzare un account diverso da Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Creare l'autenticazione di app e test utilizzando un account valido. Eseguire almeno due volte. Durante l'esecuzione del prima, si riceverà un messaggio per accedere e creare la cache dei token. Successivamente, ogni esecuzione tenterà di caricare la cache dei token per l'autenticazione e non deve essere chiesto di accedere.



