<properties
    pageTitle="Azure Active Directory B2C: Proteggere un web API utilizzando Node | Microsoft Azure"
    description="Modalità di compilazione di un web Node API che accetta i token da un tenant B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure Active Directory B2C: Proteggere un web API utilizzando Node

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con B2C Azure Active Directory (Azure Active Directory), è possibile proteggere un web API tramite token di accesso OAuth 2.0. Questi token consentono le applicazioni client che consente di eseguire l'autenticazione all'API di Azure Active Directory B2C. In questo articolo viene illustrato come creare un'API "elenco da fare" che consente agli utenti di aggiungere e visualizzazione dell'elenco attività. Web API è protetto con Azure Active Directory B2C e consente solo agli utenti autenticati di gestire il proprio elenco attività.

> [AZURE.NOTE]  In questo esempio è stato scritto a cui connettersi tramite l' [applicazione di esempio B2C iOS](active-directory-b2c-devquickstarts-ios.md). Eseguire la procedura corrente prima di tutto e quindi procedere con l'esempio.

**Passport** è middleware autenticazione per Node. Flessibile e modulare, Passport può essere installato in modo discreto tutte basati su Express o Restify applicazione web. Una serie completa di strategie supporta l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Abbiamo sviluppato una strategia per Azure Active Directory (Azure Active Directory). Installare il modulo e quindi aggiungere Azure AD `passport-azure-ad` plug-in.

Per eseguire questo esempio, è necessario:

1. Registrare un'applicazione di Azure Active Directory.
2. Configurare l'applicazione per utilizzare Passport `azure-ad-passport` plug-in.
3. Configurare un'applicazione client per chiamare dell'API web "elenco da fare".


## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant.  Una directory è un contenitore per tutti gli utenti, App, gruppi e altro ancora.  Se non CE già, [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C che dia Azure AD alcune informazioni che è necessario comunicare in modo sicuro con l'app. In questo caso, web API e applicazione client sono rappresentati da un singolo **ID applicazione**, poiché comprendono un'app logica. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un **web app o web api** nell'applicazione
- Immettere `http://localhost/TodoListService` come **URL di risposta**. È l'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e copiarlo. È necessario questi dati in un secondo momento. Si noti che questo valore deve essere [XML escape](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) prima di utilizzare.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. È necessario questi dati in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). Questa app contiene due esperienze di identità: iscriversi e l'accesso. È necessario creare un criterio di ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Quando si creano i tre criteri, assicurarsi di:

- Scegliere il **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere le richieste di applicazione **nome visualizzato** e **ID** in ogni criterio.  È possibile scegliere anche altri reclami.
- Copiare in basso il **nome** di ogni criterio dopo averlo creato. Deve avere il prefisso `b2c_1_`.  È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo avere creato i tre criteri, si è pronti per creare l'applicazione.

Per informazioni su come funzionano i criteri in Azure Active Directory B2C, iniziare con [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Per creare il campione durante il lavoro, è possibile [scaricare un progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

L'app completa è [disponibile](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) anche come file con estensione zip o sul `complete` ramo dell'archivio stesso.

## <a name="download-nodejs-for-your-platform"></a>Scaricare node per la piattaforma in uso

Per utilizzare correttamente in questo esempio, è necessario un'installazione funzionante di Node. 

Installare Node da [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installare MongoDB per la piattaforma in uso

Per utilizzare correttamente in questo esempio, è necessario un'installazione funzionante di MongoDB. Serve MongoDB per rendere l'API REST permanenti tra più istanze di server.

Installare MongoDB da [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Per eseguire questa procedura utilizzare endpoint di installazione e server predefiniti per MongoDB, ossia in fase di questo documento `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Installare i moduli Restify del Web API

Microsoft utilizza Restify per creare l'API REST. Restify è una struttura di applicazione Node minima e flessibile derivata da Express. Ha un set di caratteristiche per la creazione di API REST nella parte superiore di connettersi efficaci.

### <a name="install-restify"></a>Installa Restify

Dalla riga di comando, cambiare la cartella in `azuread`. Se il `azuread` directory non esiste, crearla.

`cd azuread`o`mkdir azuread;`

Immettere il comando seguente:

`npm install restify`

Questo comando consente di installare Restify.

#### <a name="did-you-get-an-error"></a>Viene visualizzato un errore?

In alcuni sistemi operativi, quando si utilizza `npm`, potrebbe essere visualizzato l'errore `Error: EPERM, chmod '/usr/local/bin/..'` e una richiesta di eseguire l'account con privilegi di amministratore. Se si verifica questo problema, utilizzare la `sudo` comando per l'esecuzione `npm` un livello di privilegi superiore.

#### <a name="did-you-get-a-dtrace-error"></a>Viene visualizzato un errore /DTRACE?

È possibile visualizzato simile al testo quando si installa Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify fornisce un meccanismo avanzato per individua resto chiama utilizzando /DTRACE. Molti sistemi operativi non è disponibile /DTRACE. È possibile ignorare questi errori.

L'output del comando dovrebbe essere simile al testo:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Installare Passport del Web API

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente.

Installare utilizzando il comando seguente:

`npm install passport`

L'output del comando dovrebbe essere simile al testo:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Aggiungere passport azuread al web API

Aggiungere quindi strategia OAuth utilizzando `passport-azuread`, una famiglia di strategie che metta Passport Azure Active Directory. Utilizzare questa strategia per i token del titolare nel campione API REST.

> [AZURE.NOTE] Sebbene OAuth2 offre una struttura in cui è possibile eseguire qualsiasi tipo di token noto, solo determinati tipi di token abbiano acquisito diffusamente. I token per proteggere i punti finali sono token del titolare. Questi tipi di token sono maggiormente emesso in OAuth2. Molte implementazioni presuppongono che i token del titolare sono l'unico tipo di token emessi.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente.

Installare il passaporto `passport-azure-ad` modulo mediante il comando seguente:

`npm install passport-azure-ad`

L'output del comando dovrebbe essere simile al testo:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Aggiungere i moduli MongoDB al web API

In questo esempio Usa MongoDB come archivio dati. Per cui installare Mongoose ampiamente utilizzato plug-in per la gestione dei modelli e gli schemi.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installare moduli aggiuntivi

Successivamente, installare i moduli necessari rimanenti.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Installare i moduli nel `node_modules` directory:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Creare un file server.js con le dipendenze

Il `server.js` file offre la maggior parte delle funzionalità del server dell'API Web. 

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Creare un `server.js` file in un editor. Aggiungere le informazioni seguenti:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salvare il file. Viene riattivata in un secondo momento.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Creare un file di config.js per archiviare le impostazioni di Azure Active Directory

Il file del codice passa i parametri di configurazione dal portale Azure Active Directory per il `Passport.js` file. Quando si aggiungono web API al portale nella prima parte del dimostrativo creato questi valori di configurazione. Viene illustrato cosa inserire i valori dei parametri dopo aver copiato il codice.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Creare un `config.js` file in un editor. Aggiungere le informazioni seguenti:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valori obbligatori

`clientID`: L'ID client dell'applicazione Web API.

`IdentityMetadata`: Si tratta dove `passport-azure-ad` consente di cercare i dati di configurazione per il provider di identità. Cerca anche i tasti per convalidare i token web JSON. 

`audience`: L'URI uniform resource identifier () dal portale di che identifica l'applicazione chiamante. 

`tenantName`: Il nome del tenant (ad esempio **contoso.onmicrosoft.com**).

`policyName`: Il criterio che si desidera convalidare i token provenienti al server. Questo criterio deve essere lo stesso criterio utilizzate sull'applicazione client per l'accesso.

> [AZURE.NOTE] Per scaricare l'anteprima B2C, utilizzare gli stessi criteri tra configurazione di client e server. Se si ha già completato una panoramica e creato questi criteri, non è necessario reinstallarlo. Poiché è stata completata la procedura, non è necessario impostare nuovi criteri per alcune client nel sito.

## <a name="add-configuration-to-your-serverjs-file"></a>Aggiunta di configurazione al file server.js

Per leggere i valori dalla `config.js` file creato, aggiungere il `.config` file come risorsa necessaria nell'applicazione e quindi impostare le variabili globali a quelli disponibili nel `config.js` documento.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Aprire la `server.js` file in un editor. Aggiungere le informazioni seguenti:

```Javascript
var config = require('./config');
```
Aggiungere una nuova sezione a `server.js` che include il codice seguente:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Successivamente, aggiungere alcuni segnaposto per gli utenti possibile visualizzare i dati delle applicazioni di chiamata.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

A questo punto, creare troppo il nostro logger.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Aggiungere le informazioni di modello e schema MongoDB utilizzando Mongoose

Preparazione precedenti paga mentre si porta questi tre file in un servizio API REST.

Per questo dimostrativo utilizzare MongoDB per archiviare le attività, come descritto in precedenza.

Nel `config.js` file è stato chiamato il database **elenco attività**. Nome non anche mettere in fondo la `mongoose_auth_local` URL di connessione. Non è necessario creare il database in anticipo in MongoDB. Viene creato il database per consentire alla prima esecuzione dell'applicazione server.

Dopo è che il server il database MongoDB da utilizzare, è necessario scrivere codice per creare il modello e allo schema per le attività di server aggiuntivo.

### <a name="expand-the-model"></a>Espandere il modello

Questo modello di schema è semplice. È possibile espandere in base alle esigenze.

`owner`: Assegnato all'attività. L'oggetto è una **stringa**.  

`Text`: L'attività stessa. L'oggetto è una **stringa**.

`date`: La data di scadenza dell'attività. L'oggetto è un **datetime**.

`completed`: Se l'attività è stata completata. L'oggetto è un **valore Boolean**.

### <a name="create-the-schema-in-the-code"></a>Creare lo schema nel codice

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Aprire la `server.js` file in un editor. Aggiungere le informazioni seguenti sotto la voce di configurazione:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Creare innanzitutto lo schema e quindi si crea un oggetto di modello che consente di archiviare i dati in tutto il codice quando si definiscono la **route**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Aggiungere route per il server di attività API REST

Dopo aver creato un modello di database per l'uso con, aggiungere route che utilizzare per il server di API REST.

### <a name="about-routes-in-restify"></a>Sui cicli Restify

Indirizza funziona in Restify nello stesso modo in cui lavorano quando utilizzano stack Express. Per definire le route tramite l'URI che si sta aspettando applicazioni client di chiamare. 

Una tipica per una route Restify è:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify ed Express offre molto più approfondita funzionalità, come la definizione di tipi di applicazioni e routing complesso tra i punti finali diversi. Ai fini di questa esercitazione, è semplice queste route.

#### <a name="add-default-routes-to-your-server"></a>Aggiungere route predefinite al server

È ora possibile aggiungere le route CRUD base di **creazione** e **l'elenco** per l'API REST. Sono disponibili altre route nel `complete` diramazione del campione.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

Aprire la `server.js` file in un editor. Di seguito le voci del database creato in precedenza aggiungere le informazioni seguenti:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Aggiunta di gestione degli errori per le route

Aggiungere alcuni gestione degli errori in modo che sia possibile comunicare eventuali problemi al client in modo che in grado di utilizzare.

Aggiungere il codice seguente:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Creare il server

A questo punto si dispone definito il database e inserire i percorsi in posizione. L'ultima operazione è necessario effettuare consiste nell'aggiungere l'istanza di server che gestisce le chiamate.

Restify ed Express offrono la completa personalizzazione per un server di API REST, ma serve la configurazione di base di seguito. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Aggiungere le route al server (senza autenticazione)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Aggiungere l'autenticazione al server API REST

Dopo aver creato un server di API REST in esecuzione, è possibile renderla utili contro Azure Active Directory.

Dalla riga di comando, cambiare la cartella in `azuread`, se non è già presente:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utilizzare OIDCBearerStrategy incluso in Active Directory di azure passport


> [AZURE.TIP]
Quando si scrivono API, è sempre necessario collegare i dati a un nome univoco del token che l'utente non può falsificare. Quando il server archivia elementi ToDo, viene eseguita in modo basato **oid** dell'utente in token (chiamati tramite token.oid), che indirizza nel campo "proprietario". Questo valore garantisce che solo tale utente può accedere i propri elementi ToDo. Non esiste nessuna esposizione dell'API di "proprietario", in modo che un utente esterno può richiedere altrui elementi ToDo anche se vengono autenticati.

Successivamente, utilizzare la strategia del titolare fornito con `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utilizza lo stesso modello per tutte le proprie strategie. Si passa un `function()` con `token` e `done` come parametri. La strategia torna all'utente dopo che si è verificato tutto il lavoro. È quindi necessario archiviare l'utente e salvare il token in modo che non è necessario chiedere queste informazioni per il nuovo.

> [AZURE.IMPORTANT]
Il codice precedente consente di accedere tutti gli utenti che si verifica per eseguire l'autenticazione al server. Questo processo è noto come autoregistration. Nel server di produzione, non consentono in qualsiasi accesso agli utenti l'API senza prima loro elaborata un processo di registrazione. Questo processo è in genere il modello che viene visualizzato nelle applicazioni consumer che consentono di eseguire la registrazione con Facebook ma quindi richiesto di immettere informazioni aggiuntive. Se il programma non è un programma della riga di comando, è possibile estratte messaggio di posta elettronica dall'oggetto token che viene restituita e quindi richiesto agli utenti di immettere informazioni aggiuntive. Poiché si tratta di un campione, si aggiunge a un database in memoria.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Eseguire l'applicazione server per verificare che lo rifiuta si

È possibile utilizzare `curl` per verificare se si ha ora OAuth2 protezione contro gli endpoint. Le intestazioni restituite dovrebbero essere sufficiente per indicare che trova nel percorso verso destra.

Verificare che l'istanza MongoDB sia in esecuzione:

    $sudo mongodb

Passare alla directory ed eseguire il server:

    $ cd azuread
    $ node server.js

In una nuova finestra, eseguire`curl`

Provare un POST di base:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Viene segnalato un errore 401 la risposta desiderata. Indica che il livello di Passport sta tentando di reindirizzamento per l'endpoint di autorizzazione.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Ora è un servizio di API REST che utilizza OAuth2

È stato implementato un API REST utilizzando Restify e OAuth! Ora è sufficiente codice in modo che è possibile continuare a sviluppare il servizio e in questo esempio. Si è fuori come è possibile con il server non si utilizza un client compatibile con OAuth2. Utilizzare un passo aggiuntiva come la procedura dettagliata di [connettersi a un sito web API con iOS con B2C](active-directory-b2c-devquickstarts-ios.md) per il passaggio successivo.


## <a name="next-steps"></a>Passaggi successivi

È possibile spostare argomenti più avanzati, ad esempio:

[Connettersi a un sito web API utilizzando iOS con B2C](active-directory-b2c-devquickstarts-ios.md)