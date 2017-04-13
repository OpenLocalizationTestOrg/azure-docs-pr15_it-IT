<properties
    pageTitle="Guida introduttiva di Azure AD accedere e disconnettersi utilizzando Node"
    description="Modalità di compilazione di un Node Express MVC Web App che si integra con Azure Active Directory per accedere."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS Web accesso dell'App e accedere con Azure Active Directory in uscita


Di seguito si userà Passport per:

- Accedere all'utente l'app con Azure Active Directory.
- Visualizzare le informazioni relative all'utente.
- Eseguire l'accesso all'utente dall'app.

**Passport** è middleware autenticazione per Node. Estremamente flessibile e modulare, Passport possono essere in modo discreto rilasciati a qualsiasi basati su Express o Resitify applicazione web. Una serie completa di strategie di supportare l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Una strategia abbiamo sviluppato per Microsoft Azure Active Directory. Verranno installati in questo modulo e quindi aggiungere Microsoft Azure Active Directory `passport-azure-ad` plug-in.

Per eseguire questa operazione, è necessario:

1. Registrare un'app.
2. Configurare l'app da usare la strategia di Active Directory di azure Passport.
3. Utilizzare Passport per inoltrare le richieste di accesso e disconnessione di Azure Active Directory.
4. Stampare i dati relativi all'utente.

Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) o duplicare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Applicazione completata viene fornito alla fine dell'esercitazione anche.

## <a name="1-register-an-app"></a>1. registro un'App
- Accedere al portale di gestione Azure.
- Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**.
- Selezionare il tenant nel punto in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **applicazioni** e fare clic su Aggiungi nel cassetto inferiore.
- Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    - Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   L' **URL di accesso** è l'URL di base dell'app.  Il valore predefinito della struttura è ' http://localhost:3000/auth/openid/invio '.
    - **App ID URI** è un identificatore univoco dell'applicazione.  La convenzione consiste nell'usare `https://<tenant-domain>/<app-name>`, ad esempio`https://contoso.onmicrosoft.com/my-first-aad-app`
- Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda Configura.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. aggiungere pre-requisities alla directory

Dalla riga di comando, passare alla cartella radice se non è già presente ed eseguire i comandi seguenti:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Inoltre, è necessario il nostro `passport-azure-ad` anche:

- `npm install passport-azure-ad`

Verrà installato con le relative raccolte dipendono da tale annuncio di azure passport.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurare l'app da usare strategia js di nodo passport
In questo caso, si verrà configurare middleware Express per utilizzare il protocollo di autenticazione OpenID connettersi.  Passport verrà utilizzato per eseguire le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente, tra l'altro.

-   Per iniziare, aprire la `config.js` file nella radice del progetto, quindi immettere valori di configurazione dell'applicazione di `exports.creds` sezione.
    -   Il `clientID:` corrisponde all' **Id applicazione** assegnata all'app nel portale di registrazione.
    -   Il `returnURL` è il **Reindirizzamento Uri** immesso nel portale.
    - Il `clientSecret` è il segreto generato nel portale

- Quindi aprire `app.js` file nella radice del progetto e aggiungere la chiamata seguenti per richiamare il `OIDCStrategy` strategia fornito con`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Successivamente, utilizzare la strategia è stato fatto riferimento solo per gestire le richieste di accesso

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utilizza un modello simile per tutti i relativi strategie (Twitter, Facebook, ecc.) che soddisfano i requisiti di tutti gli autori di strategia. Esaminare la strategia di vedere che vengono passati un Function che contiene un token e una fine come parametri. La strategia di perdersi tornerà a Contattaci dopo tutto avviene il lavoro. Una volta caso si desidera archiviare l'utente e nasconde il token in modo che non occorre chiedere queste informazioni per il nuovo.


> [AZURE.IMPORTANT] 
Il codice precedente consente di accedere tutti gli utenti che si verifica per eseguire l'autenticazione per il server. Tale valore è noto come la registrazione automatica. In server di produzione voler consentire a chiunque senza prima loro elaborata si decide di un processo di registrazione. Si tratta in genere il modello che viene visualizzato nelle applicazioni consumer che consentono di eseguire la registrazione con Facebook ma quindi richiesto di immettere informazioni aggiuntive. Se questa operazione non è un'applicazione di esempio, si dall'oggetto token restituito viene chiesto di immettere informazioni aggiuntive potremmo sono estratti solo la posta elettronica. Poiché si tratta di un server di test è sufficiente aggiungerli al database in memoria.

- Successivamente, aggiungere i metodi che consentiranno di tenere traccia dell'accesso degli utenti in base alle esigenze dal passaporto. Sono inclusi serializzando e deserializzando le informazioni dell'utente:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Successivamente, aggiungere il codice per caricare il motore di express. Di seguito viene visualizzato usiamo /views predefinito e fornisce motivo /routes che Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Infine, aggiungere le route che verranno trasferito le richieste di accesso effettivi per le `passport-azure-ad` motore:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. utilizzare Passport per inoltrare le richieste di accesso e disconnessione di Azure Active Directory

L'app è ora configurato correttamente per comunicare con l'endpoint v 2.0 mediante il protocollo di autenticazione OpenID connettersi.  `passport-azure-ad`è gestita tutti i dettagli di problemi di creazione di messaggi di autenticazione, la convalida token da Azure Active Directory e gestione sessione utente.  È ora per consentire agli utenti l'accesso, eseguire la disconnessione e raccogliere informazioni aggiuntive sull'utente connesso.

- Prima di tutto, consente di aggiungere i metodi di impostazione predefinita, accesso, account e la disconnessione al nostro `app.js` file:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Esaminiamo questi elementi in dettaglio:
    -   Il `/` route verrà reindirizzati alla visualizzazione index.ejs passando l'utente nella richiesta (se presente)
    - Il `/account` route verrà prima ***Assicurarsi che si sta autenticati*** (che implementare sotto) e quindi passare all'utente nella richiesta in modo da ottenere ulteriori informazioni sull'utente.
    - Il `/login` route eseguiranno una chiamata il nostro autenticatore azuread openidconnect da `passport-azuread` e se che non è riuscita verrà reindirizza l'utente /login
    - Il `/logout` verrà semplicemente chiamare il logout.ejs (e instradare) che consente di cancellare i cookie e quindi tornare all'utente al index.ejs


- Per l'ultima parte del `app.js`, aggiungere il metodo EnsureAuthenticated che viene utilizzato in `/account` sopra.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- Infine, effettivamente creare lo stesso server in `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. creare visualizzazioni e indirizza in express per visualizzare l'utente del sito Web

Abbiamo il nostro `app.js` completo. A questo punto, è sufficiente aggiungere tutte le visualizzazioni che verranno visualizzate informazioni è accedere all'utente, nonché quadratino indirizza il `/logout` e `/login` indirizza abbiamo creato.

- Creare il `/routes/index.js` percorso della cartella radice.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Creare il `/routes/user.js` percorso della cartella radice

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Queste semplici route passerà semplicemente lungo la richiesta per le visualizzazioni, tra cui l'utente se presente.

- Creare il `/views/index.ejs` vista sotto la directory radice. si tratta di una pagina semplice che verrà chiamare i metodi di accesso e disconnessione e consentono di acquisire informazioni sull'account. Si noti che è possibile utilizzare condizionale `if (!user)` come utente passato tramite la richiesta risulti sono connessi in utente.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Creare il `/views/account.ejs` visualizzazione della cartella radice in modo che è possibile visualizzare informazioni aggiuntive che `passport-azuread` è inserito nella richiesta dell'utente.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Infine, si verificare questo aspetto molto mediante l'aggiunta di un layout. Creare il ' / visualizzazione degli views/layout.ejs nella directory radice

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Infine, creare ed eseguire l'app! 

Eseguire `node app.js` e passare a`http://localhost:3000`


Accedere con un Account Microsoft personale oppure un account aziendale o dell'istituto di istruzione e notare come l'identità dell'utente viene riflesso nell'elenco /account.  È ora disponibile un'app web protetta tramite i protocolli standard che è possono autenticare gli utenti con il proprio account personali e di lavoro o dell'istituto di istruzione.

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


È possibile procedere in argomenti più avanzati.  È consigliabile provare:

[Proteggere un Web API con Azure Active Directory >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
