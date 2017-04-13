<properties
    pageTitle="Azure Active Directory NodeJS introduzione | Microsoft Azure"
    description="Modalità di compilazione di un API Web REST Node che si integra con Azure Active Directory per l'autenticazione."
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

# <a name="getting-started-with-web-api-for-node"></a>Guida introduttiva di API WEB per nodo

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** è middleware autenticazione per Node. Estremamente flessibile e modulare, Passport possono essere in modo discreto rilasciati a qualsiasi basati su Express o Resitify applicazione web. Una serie completa di strategie di supportare l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Una strategia abbiamo sviluppato per Microsoft Azure Active Directory. Verranno installati in questo modulo e quindi aggiungere Microsoft Azure Active Directory `passport-azure-ad` plug-in.

Per eseguire questa operazione, è necessario:

1. Registrare un'applicazione di Azure Active Directory
2. Configurare l'app da usare azure da Active Directory-passport in Passport plug-in.
3. Configurare un'applicazione client per chiamare la per eseguire elenco API Web

Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] In questo articolo non viene illustrata come implementare l'accesso, per l'abbonamento e gestione dei profili con Azure Active Directory B2C.  È incentrata su web chiamata API dopo l'utente è già autenticato.  Se non è ancora disponibile, iniziare con [l'integrazione con Azure Active Directory documento](./develop/active-directory-how-to-integrate.md) per informazioni sui concetti di base di Azure Active Directory.


È stato rilasciato tutto il codice sorgente in questo esempio in esecuzione in GitHub in una licenza MIT, in modo liberamente duplicato (o, ancora meglio, divisione!) e inviare commenti e suggerimenti e inserire le richieste di.

## <a name="about-nodejs-modules"></a>Informazioni sui moduli Node

Moduli Node verranno utilizzate in questa procedura dettagliata. I moduli sono che può essere caricati pacchetti JavaScript che offrono funzionalità specifiche per l'applicazione. È in genere installare moduli utilizzando lo strumento della riga di comando NPM node nella directory di installazione NPM, ma sono inclusi alcuni moduli, ad esempio il modulo HTTP, il pacchetto di Node core.
Moduli installati vengono salvati nella directory node_modules nella radice della directory di installazione di Node. Ogni modulo nella directory node_modules mantiene il proprio directory node_modules che contiene i moduli da cui dipende e tutti i moduli necessari ha una directory node_modules. La struttura di directory ricorsiva rappresenta catena delle dipendenze.

La struttura di catena dipendenza genera una superficie applicazione maggiore, ma garantisce che vengano soddisfatte tutte le dipendenze e che la versione dei moduli utilizzato in fase di sviluppo verrà utilizzata anche in produzione. In questo modo il comportamento di app di produzione più prevedibili e impedisce problemi che potrebbero interessare gli utenti.

## <a name="1-register-a-azure-ad-tenant"></a>1. registrare un Tenant di Azure Active Directory

Per utilizzare questo esempio è necessario un Tenant di Azure Active Directory. Se non si sa quali tenant o modalità che si otterrebbe una, vedere la sezione [modalità per ottenere un Azure Active Directory del tenant](active-directory-howto-tenant.md).

## <a name="2-create-an-application"></a>2. creare un'applicazione

A questo punto è necessario creare un'app nella directory, che offre un Azure AD alcune informazioni che è necessario comunicare in modo sicuro con l'app.  Applicazione client sia dell'API web rappresentati da un singolo **ID applicazione** in questo caso, dal momento che vi sono comprese un'app logica.  Per creare un'app, seguire [queste istruzioni](active-directory-how-applications-are-added.md). Se si sta creando una linea di Business app [queste istruzioni aggiuntive possono risultare utili](active-directory-applications-guiding-developers-for-lob-applications.md).

Assicurarsi di:

- Accedere al portale di gestione Azure.
- Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**.
- Selezionare il tenant nel punto in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **applicazioni** e fare clic su Aggiungi nel cassetto inferiore.
- Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    - Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    - L' **URL di accesso** è l'URL di base dell'app.  Il valore predefinito del codice di esempio è `https://localhost:8080`.
    - **App ID URI** è un identificatore univoco dell'applicazione.  La convenzione consiste nell'usare `https://<tenant-domain>/<app-name>`, ad esempio`https://contoso.onmicrosoft.com/my-first-aad-app`
- Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda Configura.

- PROMEMORIA: creare un' **Applicazione segreto** per l'applicazione e copiarla verso il basso.  Sarà necessario più avanti.
- PROMEMORIA: Copia verso il basso l' **ID dell'applicazione** che sono state assegnate a un'applicazione.  Sarà anche necessario subito.


## <a name="3-download-nodejs-for-your-platform"></a>3. scaricare node per la piattaforma
Per utilizzare correttamente in questo esempio, è necessario disporre un'installazione funzionante di Node.

Installare Node da [http://nodejs.org](http://nodejs.org).

## <a name="4-install-mongodb-on-to-your-platform"></a>4. installarlo MongoDB la piattaforma in uso

Per utilizzare correttamente in questo esempio, è necessario disporre un'installazione funzionante di MongoDB. MongoDB verrà usata per rendere il nostro persistenti API REST tra le istanze server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Questa procedura dettagliata presuppone utilizzare endpoint di installazione e server predefiniti per MongoDB, ossia in fase di questo articolo: mongodb://localhost


## <a name="5-install-the-restify-modules-in-to-your-web-api"></a>5. installare i moduli Restify l'API Web

Si utilizzeranno Resitfy per creare l'API REST. Restify è una struttura di applicazione Node minima e flessibile derivata da Express contenente un set di caratteristiche per la creazione di API REST nella parte superiore di connettersi efficaci.

### <a name="install-restify"></a>Installa Restify

Dalla riga di comando, passare alla directory azuread. Se la directory **azuread** non esiste, crearla.

`cd azuread - or- mkdir azuread; cd azuread`

Digitare il comando seguente:

`npm install restify`

Questo comando consente di installare Restify.

#### <a name="did-you-get-an-error"></a>VIENE VISUALIZZATO UN ERRORE?

Quando si utilizza npm in alcuni sistemi operativi, venga visualizzato un errore di errore: EPERM, chmod "/ usr locale/bin /..." e una richiesta per provare a eseguire l'account con privilegi di amministratore. In questo caso, utilizzare il comando sudo per eseguire npm un livello di privilegi superiore.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>VIENE VISUALIZZATO UN MESSAGGIO DI ERRORE RELATIVO A /DTRACE

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
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="6-install-passportjs-in-to-your-web-api"></a>6. installare Passport.js in API Web

[Passport](http://passportjs.org/) è middleware autenticazione per Node. Estremamente flessibile e modulare, Passport possono essere in modo discreto rilasciati a qualsiasi basati su Express o Resitify applicazione web. Una serie completa di strategie di supportare l'autenticazione tramite un nome utente e password, Facebook, Twitter e altro ancora. Abbiamo sviluppato una strategia per Azure Active Directory. Verranno installati in questo modulo e quindi aggiungere la strategia di Azure Active Directory plug-in.

Dalla riga di comando, passare alla directory azuread.

Immettere il comando seguente per installare passport.js

`npm install passport`

L'output del comando dovrebbe essere simile al seguente:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="7-add-passport-azure-ad-to-your-web-api"></a>7. aggiungere Active Directory di Azure Passport al Web API

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



## <a name="8-add-mongodb-modules-to-your-web-api"></a>8. aggiungere MongoDB moduli per l'API Web

Verranno utilizzate MongoDB come il nostro archivio di dati per questo motivo, è necessario installare entrambe le ampiamente utilizzato plug-in per gestire i modelli e gli schemi Mongoose denominati, come il driver di database per MongoDB, detto anche MongoDB.


* `npm install mongoose`

## <a name="9--install-additional-modules"></a>9. installare moduli aggiuntivi

Successivamente, possiamo installare i moduli necessari rimanenti.


Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`


Immettere i comandi seguenti per installare i moduli seguenti nella directory node_modules:

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## <a name="10-create-a-serverjs-with-your-dependencies"></a>10. creare un server.js con le dipendenze

Il file server.js fornirà la maggior parte delle funzionalità per il server Web API. La maggior parte del codice verranno aggiunti al file. Ai fini della produzione si preferisce refactoring agli file di dimensioni ridotte, ad esempio route distinte e controller le funzionalità. Per questa demo server.js verrà utilizzato per questa funzionalità.

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Creare un `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
    'use strict';

    /**
    * Module dependencies.
    */

    var fs = require('fs');
    var path = require('path');
    var util = require('util');
    var assert = require('assert-plus');
    var bunyan = require('bunyan');
    var getopt = require('posix-getopt');
    var mongoose = require('mongoose/');
    var restify = require('restify');
    var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salvare il file. Abbiamo restituirà a tale subito.

## <a name="11-create-a-config-file-to-store-your-azure-ad-settings"></a>11:. Creare un file di configurazione per archiviare le impostazioni di Azure Active Directory

Il file del codice passa i parametri di configurazione dal portale Azure Active Directory a Passport.js. Vengono creati i valori di configurazione all'API Web è stato aggiunto al portale nella prima parte della procedura dettagliata. Verrà spiegato cosa inserire i valori dei parametri dopo aver copiato il codice.


Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Creare un `config.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Salvare il file.

## <a name="12-add-configuration-to-your-serverjs-file"></a>12. aggiungere configurazione al file server.js

È necessario leggere questi valori dal file di configurazione che appena creato attraverso l'applicazione. A tale scopo, è sufficiente aggiungere il file config come una risorsa necessaria nell'applicazione e quindi impostare le variabili globali a quelli del documento config.js

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

Aprire il `server.js` dei file nell'editor preferito e aggiungere le informazioni seguenti:

```Javascript
var config = require('./config');
```
Aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        },
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Salvare il file.



## <a name="13-add-the-mongodb-model-and-schema-information-using-moongoose"></a>13. aggiungere tutte le informazioni di Schema con Moongoose MongoDB modello

Sta per iniziare a pagamento come questi tre file abbiamo vento insieme a un servizio API REST tutti questa preparazione.

Per questa procedura dettagliata si utilizzeranno MongoDB per archiviare le attività, come descritto nel ***passaggio 4***.

Se si ricorda la `config.js` file creato nel ***passaggio 11*** è chiamato PostalCode `tasklist` che venisse eseguito è inserire alla fine dell'URL connessione mogoose_auth_local. Non è necessario creare il database in anticipo in MongoDB, verrà creata questa automaticamente alla prima esecuzione dell'applicazione server (presupponendo che non esiste già).

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
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

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

## <a name="14-add-our-routes-for-our-task-rest-api-server"></a>14. aggiungere il nostro route per il server API REST di attività

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

### <a name="1-add-default-routes-to-our-server"></a>1. aggiungere route predefinite per il server

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
        req.log.warn('createTodo: missing task');
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

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
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

### <a name="2-next-lets-add-some-error-handling-in-our-apis"></a>2. successivo, aggiungere alcune gestione degli errori nelle API:

```

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


## <a name="15-create-your-server"></a>15. creare il Server!

Abbiamo PostalCode definiti, abbiamo il nostro indirizza in posizione e l'ultima operazione da eseguire è l'istanza di server che gestirà le chiamate per aggiungere.

Restify (ed esprimere) supportano numerose personalizzazione completa, è possibile eseguire per un server di API REST, ma nuovamente verrà usata la configurazione di base ai fini.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
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
```

## <a name="16-adding-the-routes-to-the-server-without-authentication-for-now"></a>16. aggiungendo le route al server (senza l'autenticazione per ora)

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

## <a name="17-before-we-add-oauth-support-lets-run-the-server"></a>17. prima che viene aggiunto il supporto OAuth, si esegue il server.

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
HTTP/1.1 200 OK
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

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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


## <a name="18-add-authentication-to-our-rest-api-server"></a>18. aggiungere l'autenticazione per il Server di API REST

Dopo aver creato una API REST in esecuzione (Complimenti, btw!) entriamo rendendo utili contro Azure Active Directory.

Dalla riga di comando, passare alla cartella **azuread** se non è già presente:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: utilizzare OIDCBearerStrategy incluso in Active Directory di azure passport

Fino a questo punto si basano un tipico server resto TODO senza alcun tipo di autorizzazione. Verrà visualizzata nel punto in cui è iniziare a inserire che insieme.

Prima di tutto, è necessario indicare che si desidera utilizzare Passport. Inserire questo diritto dopo la configurazione del server:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Quando si scrive API è sempre devono collegare i dati a un nome univoco del token che l'utente non può falsificare. Quando il server archivia elementi TODO, memorizza in base all'ID dell'utente nel token (chiamati tramite token.oid) è inseriti nel campo "proprietario". In questo modo che solo tale utente può accedere la sua attività e nessun utente può accedere attività immesso. Non esiste nessuna esposizione dell'API di "proprietario" in modo che un utente esterno può richiedere rispettive attività, anche se vengono autenticati.

Successivamente, utilizzare la strategia del titolare fornito con Active Directory di azure passport. Basta cercare il codice adesso, questo passaggio verrà spiegato subito. Inserire questo dopo i quali si pated sopra:

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


var bearerStrategy = new BearerStrategy(options,
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

passport.use(bearerStrategy);
```

Passport utilizza un modello simile per tutti i relativi strategie (Twitter, Facebook, ecc.) che soddisfano i requisiti di tutti gli autori di strategia. Esaminare la strategia di vedere che vengono passati un Function che contiene un token e una fine come parametri. La strategia di perdersi tornerà a Contattaci dopo tutto avviene il lavoro. Una volta caso si desidera archiviare l'utente e nasconde il token in modo che non occorre chiedere queste informazioni per il nuovo.

> [AZURE.IMPORTANT]
Il codice precedente consente di accedere tutti gli utenti che si verifica per eseguire l'autenticazione per il server. Tale valore è noto come la registrazione automatica. In server di produzione voler consentire a chiunque senza prima loro elaborata si decide di un processo di registrazione. Si tratta in genere il modello che viene visualizzato nelle applicazioni consumer che consentono di eseguire la registrazione con Facebook ma quindi richiesto di immettere informazioni aggiuntive. Se questa operazione non è un programma di riga di comando, abbiamo dall'oggetto token restituito viene chiesto di immettere informazioni aggiuntive potremmo sono estratti solo la posta elettronica. Poiché si tratta di un server di test è sufficiente aggiungerli al database in memoria.

### <a name="2-finally-protect-some-endpoints"></a>2. infine proteggere alcune endpoint

Per proteggere i punti finali che specifica il `passport.authenticate()` chiamata con il protocollo che si desidera utilizzare.

Modifica il nostro route codice relativo al server per eseguire operazioni più interessanti:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19. eseguire nuovamente l'applicazione server e assicurarsi che si rifiuta

Utilizzare `curl` nuovamente per verificare se è ora OAuth2 protezione contro i punti finali. Si esegue questa prima in esecuzione qualsiasi cliente SDK rispetto a questo punto finale. Le intestazioni restituite dovrebbero essere sufficiente per inviare commenti e suggerimenti che sono verso il basso il percorso verso destra.

Prima di tutto, verificare che l'istanza di monogoDB sia in esecuzione:

  $sudo mongod

Passare alla directory, quindi avviare per..

  $ cd azuread $ nodo server.js

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

Se si è interessati nei passaggi successivi nel percorso ADAL, ecco alcuni client ADAL supportati è consigliabile per poter continuare a lavorare:

Duplicare semplicemente per difetto a computer di sviluppo e configurare come indicato nella procedura dettagliata.

[ADAL per iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
