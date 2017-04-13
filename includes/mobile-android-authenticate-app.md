
1. In **Gestione progetti** di Studio Android, aprire il file ToDoActivity.java e aggiungere le seguenti istruzioni di importazione.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Aggiungere il metodo seguente alla classe **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    In questo modo si crea un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Google. Viene visualizzata una finestra di dialogo che consente di visualizzare l'ID dell'utente autenticato. Non è possibile procedere senza un metodo di autenticazione positivo.

    > [AZURE.NOTE] Se si utilizza un provider di identità diverso da Google, modificare il valore passato per il metodo di **accesso** precedente a una delle operazioni seguenti: _MicrosoftAccount_, _Facebook_, _Twitter_o _windowsazureactivedirectory_.

3. Metodo **onCreate** , aggiungere la riga di codice seguente dopo il codice che crea un'istanza di `MobileServiceClient` oggetto.

        authenticate();

    La chiamata avvia il processo di autenticazione.

4. Spostare il codice rimanente dopo `authenticate();` nel metodo **onCreate** per un nuovo metodo **createTable** che è simile alla seguente:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. Dal menu **Esegui** , quindi fare clic su **Esegui app** per avviare l'app e accedere con il provider di identità scelto. 

    Quando si è eseguito l'accesso in correttamente, l'app deve essere eseguita senza errori e dovrebbe essere possibile eseguire una query il servizio di back-end ed effettuare aggiornamenti ai dati.