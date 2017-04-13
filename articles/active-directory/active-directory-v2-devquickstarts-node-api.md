<properties
    pageTitle="Azure Active Directory v 2.0 NodeJS Web API | Microsoft Azure"
    description="Modalità di compilazione di un API Web NodeJS accetta token da entrambi Account Microsoft personale e account aziendale o dell'istituto di istruzione."
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
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>Proteggere un API Web tramite Node

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

Con l'endpoint v 2.0 Azure Active Directory, è possibile proteggere un API Web tramite token di accesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , che consente agli utenti con account Microsoft personale e aziendale o dell'istituto di istruzione account per l'accesso sicuro l'API Web.

**Passport** è middleware autenticazione per Node. Estremamente flessibile e modulare, Passport possono essere in modo discreto rilasciati a qualsiasi basati su Express o Resitify applicazione web. Una serie completa di strategie di supportare l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Una strategia abbiamo sviluppato per Microsoft Azure Active Directory. Verranno installati in questo modulo e quindi aggiungere Microsoft Azure Active Directory `passport-azure-ad` plug-in.

## <a name="download"></a>Download
Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) o duplicare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Applicazione completata viene fornito alla fine dell'esercitazione anche.


## <a name="1-register-an-app"></a>1. registro un'app
Creare una nuova app [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copia verso il basso l' **Id applicazione** assegnata all'applicazione in uso, sarà necessario immetterla breve.
- Aggiungere la piattaforma **Mobile** per l'app.
- Copiare in basso **Reindirizzare URI** dal portale. È necessario usare il valore predefinito di `urn:ietf:wg:oauth:2.0:oob`.


## <a name="2-download-nodejs-for-your-platform"></a>2: scaricare node per la piattaforma
Per utilizzare correttamente in questo esempio, è necessario disporre un'installazione funzionante di Node.

Installare Node da [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3: MongoDB di installazione è possibile la piattaforma in uso

Per utilizzare correttamente in questo esempio, è necessario disporre un'installazione funzionante di MongoDB. MongoDB verrà usata per rendere il nostro persistenti API REST tra le istanze server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Questa procedura dettagliata presuppone utilizzare endpoint di installazione e server predefiniti per MongoDB, ossia in fase di questo articolo: mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4: installare i moduli Restify per l'API Web

Si utilizzeranno Resitfy per creare l'API REST. Restify è una struttura di applicazione Node minima e flessibile derivata da Express contenente un set di caratteristiche per la creazione di API REST nella parte superiore di connettersi efficaci.

### <a name="install-restify"></a>Installa Restify

Dalla riga di comando, passare alla directory azuread. Se la directory **azuread** non esiste, crearla.

`cd azuread`- oppure -`mkdir azuread;`

Digitare il comando seguente:

`npm install restify`

Questo comando consente di installare Restify.

#### <a name="did-you-get-an-error"></a>Viene visualizzato un errore?

Quando si utilizza npm in alcuni sistemi operativi, venga visualizzato un errore di errore: EPERM, chmod "/ usr locale/bin /..." e una richiesta per provare a eseguire l'account con privilegi di amministratore. In questo caso, utilizzare il comando sudo per eseguire npm un livello di privilegi superiore.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Viene visualizzato un messaggio di errore relativo a /DTRACE

Durante l'installazione di Restify, potrebbe essere visualizzato simile al seguente:

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


Restify fornisce un meccanismo avanzato per tenere traccia delle chiamate resto utilizzando /DTRACE. Molti sistemi operativi non è disponibile /DTRACE. È possibile ignorare questi errori.


L'output del comando dovrebbe essere simile al seguente:


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5: installare Passport.js nel proprio Web API

[Passport](http://passportjs.org/) è middleware autenticazione per Node. Estremamente flessibile e modulare, Passport possono essere in modo discreto rilasciati a qualsiasi basati su Express o Resitify applicazione web. Una serie completa di strategie di supportare l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Abbiamo sviluppato una strategia per Azure Active Directory. Verranno installati in questo modulo e quindi aggiungere la strategia di Azure Active Directory plug-in.

Dalla riga di comando, passare alla directory azuread.

Immettere il comando seguente per installare passport.js

`npm install passport`

L'output del comando dovrebbe essere simile al seguente:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: aggiungere Active Directory di Azure Passport al Web API

A questo punto si aggiungerà strategia OAuth utilizzando passport azuread, una famiglia di strategie che metta Passport Azure Active Directory. Questa strategia verrà usata per i token del titolare in questo esempio API Rest.

> [AZURE.NOTE] Sebbene OAuth2 offre una struttura in cui è possibile eseguire qualsiasi tipo di token noto, solo determinati tipi di token abbiano acquisito wide molto utilizzate. Per proteggere i punti finali, che ha disattivato da token del titolare. I token del titolare sono più rilasciati ampiamente tipo di token di OAuth2 e molte implementazioni presuppongono che i token del titolare sono l'unico tipo di token emessi.

Dalla riga di comando, passare alla directory azuread

Digitare il comando seguente per installare il modulo di Active Directory di azure passport Passport.js:

`npm install passport-azure-ad`

L'output del comando dovrebbe essere simile al seguente:

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: aggiungere MongoDB moduli per l'API Web

Verranno utilizzate MongoDB come il nostro archivio di dati per questo motivo, è necessario installare entrambe le ampiamente utilizzato plug-in per gestire i modelli e gli schemi Mongoose denominati, come il driver di database per MongoDB, detto anche MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: installare moduli aggiuntivi

Successivamente, possiamo installare i moduli necessari rimanenti.


Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`


Immettere i comandi seguenti per installare i moduli seguenti nella directory node_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9: creare un server.js con le dipendenze

Il file server.js fornirà la maggior parte delle funzionalità per il server Web API. La maggior parte del codice verranno aggiunti al file. Ai fini della produzione si preferisce refactoring agli file di dimensioni ridotte, ad esempio route distinte e controller le funzionalità. Per questa demo server.js verrà utilizzato per questa funzionalità.

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Creare un `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Salvare il file. Si verrà viene riattivata subito.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: creare un file di configurazione per archiviare le impostazioni di Azure Active Directory

Il file del codice passa i parametri di configurazione dal portale Azure Active Directory a Passport.js. Vengono creati i valori di configurazione all'API Web è stato aggiunto al portale nella prima parte della procedura dettagliata. Verrà spiegato cosa inserire i valori dei parametri dopo aver copiato il codice.


Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Creare un `config.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Valori obbligatori

*IdentityMetadata*: si tratta in Active Directory di azure passport la ricerca per i dati di configurazione per la IdP, nonché i tasti convalidare i token JWT. Probabile che non si desidera modificare tale valore se con Azure Active Directory.

*partecipanti*: il reindirizzamento URI dal portale.

> [AZURE.NOTE]
Abbiamo implementazione le chiavi a intervalli frequenti. Assicurarsi che siano sempre estrazione da URL "openid_keys" e che l'app di accedere a internet.


## <a name="11-add-configuration-to-your-serverjs-file"></a>11: aggiunta di configurazione al file server.js

È necessario leggere questi valori dal file di configurazione che appena creato attraverso l'applicazione. A tale scopo, è sufficiente aggiungere il file config come una risorsa necessaria nell'applicazione e quindi impostare le variabili globali a quelli del documento config.js

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Aprire il `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
var config = require('./config');
```
Aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>Passaggio 12: Aggiungere il modello MongoDB e informazioni di Schema con Moongoose

Sta per iniziare a pagamento come questi tre file abbiamo vento insieme a un servizio API REST tutti questa preparazione.

Per questa procedura dettagliata si utilizzeranno MongoDB per archiviare le attività, come descritto nel ***passaggio 4***.

Se si ricorda il file config.js creato nel passaggio 11, il database *elenco attività*, è chiamato che venisse eseguito è inserire alla fine dell'URL connessione mogoose_auth_local. Non è necessario creare il database in anticipo in MongoDB, verrà creata questa automaticamente alla prima esecuzione dell'applicazione server (presupponendo che non esiste già).

Ora che è stato indicato il server specificare il database MongoDB si preferisce, è necessario scrivere codice aggiuntivo per creare il modello e dello schema per le attività del server.

#### <a name="discussion-of-the-model"></a>Descrizione del modello

Il modello di Schema è molto semplice e si espande in base alle esigenze.

NAME - nome assegnato all'attività. Una ***stringa***

ATTIVITÀ - attività stessa. Una ***stringa***

Data: la data di scadenza dell'attività. ***DATETIME***

COMPLETATA - se l'attività è completata o meno. Un ***valore BOOLEAN***

#### <a name="creating-the-schema-in-the-code"></a>Creazione dello schema di nel codice


Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Aprire il `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti sotto la voce di configurazione:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Verrà connettersi al server MongoDB e mano nuovamente un oggetto dello Schema per noi.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>Lo Schema consente di creare il nostro modello nel codice

Sotto il codice che scritto in precedenza, aggiungere il codice seguente:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Come è possibile identificarlo dal codice, verrà creato il nostro Schema e quindi si crea un oggetto di modello che verrà usata per archiviare i dati in tutto il codice quando si definiscono il nostro ***route***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Passaggio 13: Aggiungere il nostro route per il server API REST di attività

Ora che è stata creata per l'uso con un modello di database, è possibile aggiungere route che verrà usata per il server API REST.

### <a name="about-routes-in-restify"></a>Sulle route in Restify

Indirizza funziona in Restify nello stesso modo esatto che quello utilizzando stack Express. Per definire le route utilizzando l'URI che si sta aspettando applicazioni client di chiamare. Di solito, per definire i percorsi in un file distinto. Ai fini, si inseriranno il nostro indirizza nel file server.js. È consigliabile che questi fattori propri file per l'utilizzo di produzione.

Una tipica per una Route Restify è:

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


Si tratta del modello a livello di base. Resitfy (ed Express) offrono molto più approfondita functionaltiy, ad esempio la definizione di tipi di applicazioni ed eseguire il routing complesso tra i punti finali diversi. Ai fini, si manterranno tali route in poche parole.

#### <a name="add-default-routes-to-our-server"></a>Aggiungere route predefinite per il server

È ora verranno aggiungere le route CRUD base di creare, recuperare, aggiornare ed eliminare.

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Aprire il `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti sotto voci in un database creato in precedenza:

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>Aggiungere alcuni gestione degli errori per le route

È opportuno aggiungere alcuni gestione degli errori in modo possiamo comunicare al client il problema che si sono verificati in modo in grado di utilizzare.

Aggiungere il codice seguente sotto il codice che è scritto in precedenza:

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


## <a name="step-14-create-your-server"></a>Passaggio 14: Creare il Server!

Abbiamo PostalCode definiti, abbiamo il nostro indirizza in posizione e l'ultima operazione da eseguire è l'istanza di server che gestirà le chiamate per aggiungere.

Restify (ed esprimere) supportano numerose personalizzazione completa, è possibile eseguire per un server di API REST, ma nuovamente verrà usata la configurazione di base ai fini.

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15: aggiunta di route (senza l'autenticazione per ora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16: prima viene aggiunto il supporto OAuth, si esegue il server.

Testare il server prima di aggiungere l'autenticazione

Il modo più semplice per eseguire questa operazione è utilizzando curvatura in una riga di comando. Prima di procedere è, è necessario una semplice utilità che consente di analizzare output come JSON. A tale scopo, installare lo strumento json come usare gli esempi riportata di seguito.

`$npm install -g jsontool`

Si installa lo strumento JSON globale. Ora che è stata eseguita che – riprodurre con il server:

Prima di tutto, assicurarsi che l'istanza di monogoDB sia in esecuzione..

`$sudo mongod`

Passare alla directory, quindi avviare per..

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

È quindi possibile aggiungere un'attività in questo modo:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

La risposta deve essere:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
E sono elencate le attività per Brandon in questo modo:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tutto ciò avviene, si è pronti aggiungere OAuth al server API REST.

**Si dispone di un server di API REST con MongoDB!**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17: aggiungere l'autenticazione per il Server di API REST

Dopo aver creato una API REST in esecuzione (Complimenti, btw!) entriamo rendendo utili contro Azure Active Directory.

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: utilizzare oidcbearerstrategy incluso in Active Directory di azure passport

Fino a questo punto si basano un tipico server resto TODO senza alcun tipo di autorizzazione. Verrà visualizzata nel punto in cui è iniziare a inserire che insieme.

Prima di tutto, è necessario indicare che si desidera utilizzare Passport. Inserire questo diritto dopo la configurazione del server:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Quando si scrive API è sempre devono collegare i dati a un nome univoco del token che l'utente non può falsificare. Quando il server archivia elementi TODO, memorizza loro in base all'ID abbonamento dell'utente nel token (chiamati tramite token.sub) è inseriti nel campo "proprietario". In questo modo che solo tale utente può accedere la sua attività e nessun utente può accedere attività immesso. Non esiste nessuna esposizione dell'API di "proprietario" in modo che un utente esterno può richiedere rispettive attività, anche se vengono autenticati.

Successivamente, utilizzare la strategia del titolare connettersi ID Apri fornito con Active Directory di azure passport. Basta cercare il codice adesso, questo passaggio verrà spiegato subito. Inserire questo dopo i quali si pated sopra:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport utilizza un modello simile per tutti i relativi strategie (Twitter, Facebook, ecc.) che soddisfano i requisiti di tutti gli autori di strategia. Esaminare la strategia di vedere che vengono passati un Function che contiene un token e una fine come parametri. La strategia di perdersi tornerà a Contattaci dopo tutto avviene il lavoro. Una volta caso si desidera archiviare l'utente e nasconde il token in modo che non occorre chiedere queste informazioni per il nuovo.

> [AZURE.IMPORTANT]
Il codice precedente consente di accedere tutti gli utenti che si verifica per eseguire l'autenticazione per il server. Tale valore è noto come la registrazione automatica. In server di produzione voler consentire a chiunque senza prima loro elaborata si decide di un processo di registrazione. Si tratta in genere il modello che viene visualizzato nelle applicazioni consumer che consentono di eseguire la registrazione con Facebook ma quindi richiesto di immettere informazioni aggiuntive. Se questa operazione non è un programma di riga di comando, abbiamo dall'oggetto token restituito viene chiesto di immettere informazioni aggiuntive potremmo sono estratti solo la posta elettronica. Poiché si tratta di un server di test è sufficiente aggiungerli al database in memoria.

### <a name="2-finally-protect-some-endpoints"></a>2. infine proteggere alcune endpoint

Proteggere i punti finali specificando la chiamata passport.authenticate() con il protocollo che si desidera utilizzare.

Modifica il nostro route codice relativo al server per eseguire operazioni più interessanti:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18: eseguire nuovamente l'applicazione server e assicurarsi si rifiuta

Utilizzare `curl` nuovamente per verificare se è ora OAuth2 protezione contro i punti finali. Si esegue questa prima in esecuzione qualsiasi cliente SDK rispetto a questo punto finale. Le intestazioni restituite dovrebbero essere sufficiente per inviare commenti e suggerimenti che sono verso il basso il percorso verso destra.

Prima di tutto, assicurarsi che l'istanza di monogoDB sia in esecuzione..

    $sudo mongod

Passare alla directory, quindi avviare per..

    $ cd azuread
    $ node server.js

Provare un POST di base:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un 401 è la risposta che si sta cercando di seguito, che indica che il livello di Passport sta tentando di reindirizzamento per l'endpoint di autorizzazione, che corrisponde esattamente quello desiderato.


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Congratulazioni! Si dispone di un servizio di API REST utilizzando OAuth2!

È stata causa come è possibile con il server non si utilizza un client compatibile OAuth2. È necessario accedere tramite una procedura dettagliata aggiuntiva.

Se si cerca solo le informazioni su come implementare un API REST utilizzando Restify e OAuth2, è necessario codice più sufficiente per mantenere il servizio di sviluppo e imparare a creare in questo esempio.

## <a name="next-steps"></a>Passaggi successivi

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

È possibile procedere in argomenti più avanzati.  È consigliabile provare:

[Proteggere un'app web Node utilizzando l'endpoint v 2.0 >>](active-directory-v2-devquickstarts-node-web.md)

Per ulteriori risorse, vedere:
- [La Guida di sviluppo v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Tag StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
