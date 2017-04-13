###<a name="server-auth"></a>Procedura: eseguire l'autenticazione con un Provider di servizi (flusso di Server)

Per gestire il processo di autenticazione nell'app App Mobile, è necessario registrare l'app con il provider di identità. Nel servizio di App Azure, quindi è necessario configurare ID e il segreto forniti dal provider.
Per ulteriori informazioni, vedere l'esercitazione [Aggiungi autenticazione all'app].

Dopo aver registrato il provider di identità, semplicemente il metodo .login() con il nome del provider. Ad esempio, effettuare l'accesso con Facebook usare il codice riportato di seguito.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se si utilizza un provider di identità diverso da Facebook, modificare il valore passato per il metodo di accesso precedente a una delle operazioni seguenti: `microsoftaccount`, `facebook`, `twitter`, `google`, o `aad`.

In questo caso, il servizio di App Azure gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione servizio App dopo l'accesso con esito positivo con il provider di identità. La funzione di accesso, al termine, restituisce un JSON oggetto (utente) esposti ID utente e App servizio token di autenticazione nei campi ID utente e authenticationToken, rispettivamente. Questo token può essere memorizzati nella cache e utilizzato nuovamente fino alla scadenza.

###<a name="client-auth"></a>Procedura: eseguire l'autenticazione con un Provider di servizi (flusso di Client)

L'app può anche in modo indipendente contattare il provider di identità e quindi specificare il token restituito al servizio di App per l'autenticazione. Questo flusso client consente di fornire un'esperienza single sign-in per gli utenti o per recuperare dati aggiuntivi relativi all'utente dal provider di identità.

#### <a name="social-authentication-basic-example"></a>Esempio di sequenza di autenticazione social networking

In questo esempio viene utilizzato il client di Facebook SDK per l'autenticazione:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
In questo esempio si presuppone che il token fornito dal provider rispettivi SDK è memorizzato nella variabile di token.

#### <a name="microsoft-account-example"></a>Esempio di Account Microsoft

Nell'esempio seguente viene utilizzata Live SDK, supporta single sign-on per le applicazioni di Windows Store con l'Account Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

In questo esempio Ottiene un token da Live effettua la connessione, che viene fornito tramite la funzione di accesso al servizio di App.

###<a name="auth-getinfo"></a>Procedura: ottenere informazioni relative all'utente autenticato

Le informazioni di autenticazione per l'utente corrente possono essere recuperate dalla `/.auth/me` endpoint utilizzando il metodo AJAX.  Assicurarsi che si imposta la `X-ZUMO-AUTH` intestazione per il token di autenticazione.  Il token di autenticazione è archiviato `client.currentUser.mobileServiceAuthenticationToken`.  Ad esempio, per utilizzare il recupero API:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Recupero è disponibile come pacchetto di npm oppure per CDNJS il download del browser. È anche possibile utilizzare jQuery o un'altra API AJAX per recuperare le informazioni.  Riceverà i dati come oggetto JSON.
