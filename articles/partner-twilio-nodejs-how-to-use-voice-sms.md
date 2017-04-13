<properties 
    pageTitle="Utilizzo di Twilio per vocali, le chiamate VoIP e SMS messaggistica in Azure" 
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Esempi di codice scritti in Node." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Utilizzare Twilio per vocali, le chiamate VoIP e SMS messaggistica in Azure

Questa guida viene illustrato come creare App comunicare con Twilio e Node in Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>Che cos'è Twilio?

Twilio è una piattaforma API che semplifica l'individuazione di agli sviluppatori di effettuare e ricevere chiamate telefoniche, inviare e ricevere messaggi di testo e incorporare chiamate VoIP nelle applicazioni per dispositivi mobili basate su browser e native.  Di seguito brevemente il funzionamento prima.

### <a name="receiving-calls-and-text-messages"></a>Ricevere telefonate e messaggi di testo

Twilio consente agli sviluppatori di [acquistare i numeri di telefono programmabile] [ purchase_phone] che può essere utilizzato a inviare e ricevere chiamate e messaggi di testo.  Quando un numero Twilio riceve una chiamata in ingresso o testo, Twilio invierà l'applicazione web un HTTP POST o una richiesta GET, in cui viene richiesto per istruzioni su come gestire la chiamata o testo.  Il server risponderà alla richiesta HTTP del Twilio con [TwiML][twiml], un semplice insieme di tag XML che contengono istruzioni su come gestire una chiamata o il testo.  Esempi di TwiML vedremo in un attimo.

### <a name="making-calls-and-sending-text-messages"></a>Effettuare chiamate e invio di messaggi di testo

Impostando le richieste HTTP a API del servizio web Twilio, gli sviluppatori possono inviare messaggi di testo o avviare le chiamate in uscita.  Per le chiamate in uscita, lo sviluppatore specificare un URL che restituisce TwiML istruzioni per la procedura gestire la chiamata in uscita dopo che è connesso.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporare le funzionalità VoIP nel codice dell'interfaccia utente (JavaScript, iOS o Android)

Twilio fornisce un SDK lato client per convertire qualsiasi web browser desktop, app iOS o Android app in un telefono VoIP.  In questo articolo verranno evidenziate come usare le chiamate VoIP chiamare nel browser.  Oltre a quelli JavaScript Twilio in esecuzione nel browser, un'applicazione sul lato server (applicazione Node) da utilizzare per emettere un "token di funzionalità" al client JavaScript.  È possibile leggere altre informazioni sull'uso di VoIP con Node [nel blog dev Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Iscriversi per Twilio (Microsoft sconto)

Prima di utilizzare i servizi Twilio, è necessario prima [iscriversi a un account][signup].  I clienti di Microsoft Azure uno sconto speciale, [assicurarsi di Iscriviti ora][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Creare e distribuire un sito Web Azure Node

Successivamente, sarà necessario creare un sito Web Node in esecuzione in Azure.  [Consultare la documentazione ufficiale per eseguire questa operazione si trova in][azure_new_site].  Un alto livello, è necessario effettuare le operazioni seguenti:

* Iscrizione a un account Azure, se non è già disponibile
* Creare un nuovo sito Web tramite la console di amministrazione Azure
* Aggiunta di supporto per il controllo di origine (si supponga usato fra)
* Creazione di un file `server.js` con un'applicazione web Node semplice
* Distribuzione di questa semplice applicazione in Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurare il modulo Twilio

Successivamente, si inizierà a scrivere un'applicazione di Node semplice che semplifica l'uso delle API Twilio.  Prima di iniziare, è necessario configurare le credenziali dell'account Twilio.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurazione delle credenziali Twilio in variabili di sistema

Per rendere le richieste autenticate contro Twilio back-end, è necessario il nostro account SID e token di autenticazione, la funzione come il nome utente e la password impostata per l'account Twilio. Il modo più sicuro per configurare questi per l'utilizzo con il modulo di nodo in Azure consiste nell'utilizzare variabili di ambiente sistema, è possibile impostare direttamente nella console di amministrazione di Azure.

Selezionare il sito Web Node e fare clic sul collegamento "Configura".  Se scorrere verso il basso un po', verrà visualizzata un'area in cui è possibile impostare le proprietà di configurazione dell'applicazione.  Immettere le credenziali dell'account Twilio ([trovato nel dashboard Twilio][twilio_dashboard]) come illustrato, assicurarsi di nomi "TWILIO_ACCOUNT_SID" e "TWILIO_AUTH_TOKEN", rispettivamente:

![Console di amministrazione di Azure][azure-admin-console]

Dopo aver configurato queste variabili, riavviare l'applicazione nella console di Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Dichiarazione di modulo Twilio in package.json

È necessario creare un package.json per gestire le dipendenze modulo nodo tramite [npm].  Allo stesso livello del file "server.js" creata in esercitazione Azure/node.js, creare un file denominato "package.json".  All'interno di file, inserire le operazioni seguenti:

  {"nome": "nome dell'applicazione", "versione": "0.0.1", "privato": true, "script": {"start": "server di nodo"}, "dipendenze": {"express": "3.1.0", "ejs": "*", "twilio": "*"}}

Il modulo twilio questo dichiara come una dipendenza, nonché i più diffusi [framework web express] [ express] e il motore del modello EJS.  Passiamo ora è stiamo tutti set - punto scrivere del codice!

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Effettuare una chiamata in uscita

Creare una maschera semplice in cui verrà inserito una chiamata a un numero che abbiamo scelto.  Aprire la server.js e immettere il codice riportato di seguito.  Nota in cui è visualizzato "CHANGE_ME" - inserire il nome del sito Web azure sono:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Successivamente, creare una directory denominata "viste" - all'interno di questa directory, creare un file denominato "index.ejs" con il contenuto seguente:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

A questo punto, distribuire il sito Web in Azure e aprire la home page.  Dovrebbe essere possibile immettere il numero di telefono nel campo di testo e ricevere una chiamata dal numero di Twilio!

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Inviare un messaggio SMS

A questo punto, impostare su un'interfaccia utente e la logica di gestione dei moduli per inviare un messaggio di testo.  Aprire la "server.js" e aggiungere il codice seguente dopo l'ultima chiamata a "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

In "views/index.ejs", aggiungere un'altra maschera in quella prima di inviare un messaggio di testo e un numero:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Ridistribuire all'applicazione di Azure e a questo punto dovrebbe essere possibile inviare che formano e inviare a se stessi (o uno degli amici di più vicina) un messaggio di testo.

<a id="nextsteps"/>
## <a name="next-steps"></a>Passaggi successivi

Introduzione all'uso Node e Twilio per la creazione di applicazioni comunicano appreso.  Ma in questi esempi vengono poco spazio superficie potenzialità con Twilio e Node.  Per ulteriori informazioni sull'utilizzo Twilio con Node, consultare le risorse seguenti:

* [Documenti modulo ufficiale][docs]
* [Esercitazione su VoIP con le applicazioni di Node][voipnode]
* [Votr - un SMS in tempo reale voto applicazione con Node e CouchDB (tre parti)][votr]
* [Programmazione di coppia nel browser con Node][pair]

Ci auguriamo che già abituati pirateria informatica Node e Twilio su Azure!

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



