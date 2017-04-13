<properties
    pageTitle="Creare un'applicazione di chat Node con Socket.IO nel servizio App Azure"
    description="Esercitazione in cui viene illustrato come utilizzare socket.io in un'app web Node ospitata in Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Creare un'applicazione di chat Node con Socket.IO nel servizio App Azure

Socket.IO consente la comunicazione in tempo reale tra il server Node e i clienti utilizzando WebSockets. Inoltre, supporta fallback per altri trasporti (ad esempio lungo polling) compatibili con browser meno recenti. In questa esercitazione verrà scorrere l'hosting di un'applicazione di chat Socket.IO in base a come un'app web Azure e viene illustrato come ridimensionare l'applicazione utilizzando [Azure Redis Cache]. Per ulteriori informazioni sulla Socket.IO, vedere <http://socket.io/>.

> [AZURE.NOTE] Le procedure in questa attività si applicano alle [App servizio Web Apps]; per i servizi Cloud, vedere [creare un'applicazione di Chat Node con Socket.IO in un servizio Cloud di Azure].

## <a name="download-the-chat-example"></a>Scaricare l'esempio di chat

Per questo progetto, verrà usata l'esempio di chat dal [repository Socket.IO GitHub]. Per scaricare l'esempio e aggiungerlo al progetto che è stato creato in precedenza, procedere come segue.

1.  Scaricare un [file ZIP o GZ archiviate release] del progetto Socket.IO (versione 1.3.5 è stata utilizzata per questo documento)

1.  Estrarre l'archivio e copiare il **esempi\\chat** directory in una nuova posizione. Ad esempio ** \\nodo\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Modificare app.js e installare i moduli

1.  Rinominare il file **index.js** in **app.js**. In questo modo Azure rilevare che si tratta di un'applicazione di Node.

1.  Aprire il file **app.js** in un editor di testo. Modificare la riga contenente `var io = require('../..')(server);` come illustrato di seguito:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Aprire il file **package.json** e aggiungere un riferimento a socket.io in `dependencies`, come illustrato di seguito:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. Dalla riga di comando, impostare il ** \\nodo\\chat** directory e l'utilizzo npm a installare i moduli necessari per questa applicazione:

        npm install

    I moduli verrà installato in una sottocartella denominata **node_modules**.

## <a name="create-an-azure-web-app"></a>Creare un'App Web Azure

Seguire questa procedura per creare un'app web Azure, attivare la pubblicazione fra e quindi attivare il supporto di WebSocket per l'applicazione web.

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Versione di valutazione gratuita di Azure</a>.

1. Installare l'interfaccia della riga di comando di Azure (Azure CLI) e connettersi al proprio abbonamento Azure. Vedere [installare e configurare CLI Azure](../xplat-cli-install.md).

1. Se si tratta prima ora configurando un archivio di Azure, è necessario creare le credenziali di accesso. Da CLI Azure, immettere il comando seguente:

        azure site deployment user set [username] [password]

1. Impostare il ** \\node\chat** directory e usare il comando seguente per creare un nuovo Azure web app e un repository fra locale. Questo comando crea anche un fra remoto denominato "azure".

        azure site create mysitename --git

    'Mysitename' deve essere sostituito con un nome univoco per un'app web.

1. Eseguire il commit file esistenti all'archivio locale, utilizzare i comandi seguenti:

        git add .
        git commit -m "Initial commit"

1. Inserire i file di archivio di Azure Web Apps con il comando seguente:

        git push azure master

    Quando richiesto, immettere le credenziali dal passaggio 2. Si riceveranno messaggi di stato come moduli vengono importati nel server. Al termine di questo processo, l'applicazione sarà ospitato in un'app web Azure.

    > [AZURE.NOTE] Durante l'installazione di modulo, è possibile notare gli errori che ' il progetto importato... non trovata ". Questi può essere ignorati.

1. Socket.IO utilizza WebSockets non attivata per impostazione predefinita in Azure. Per abilitare sockets web, utilizzare il comando seguente:

        azure site set -w

    Se richiesto, immettere il nome dell'applicazione web.

    >[AZURE.NOTE]
    >La 'sito azure set -w' comando verranno solo con versione 0.7.4 o superiore dell'interfaccia della riga di comando Azure. È anche possibile attivare il supporto di WebSocket tramite il [Portale di Azure](https://portal.azure.com).
    >
    >Per abilitare WebSockets tramite il portale di Azure, fare clic su app web da e il Web Apps, fare clic su **tutte le impostazioni** > **le impostazioni dell'applicazione**. In **Sockets Web**, fare clic **su**. Fare clic su **Salva**.

1. Per visualizzare l'applicazione web Azure, usare il comando seguente per avviare il browser e passare all'app web ospitato:

        azure site browse

L'applicazione è in esecuzione in Azure e può inoltrare i messaggi chat tra diversi clienti utilizzando Socket.IO.

## <a name="scale-out"></a>Scalabilità

Possono essere ridimensionate Socket.IO applicazioni utilizza un __adattatore__ per distribuire messaggi ed eventi tra più istanze dell'applicazione. Mentre sono presenti più schede, la scheda [socket.io redis] può essere utilizzata facilmente con la funzionalità di Azure Redis Cache.

> [AZURE.NOTE] Un ulteriore requisito per il ridimensionamento di una soluzione Socket.IO è supporto per le sessioni permanenti. Le sessioni permanenti sono attivate per impostazione predefinita per le applicazioni Web Azure richiedere instradamento Azure. Per ulteriori informazioni, vedere [Affinità istanza di siti Web di Azure].

### <a name="create-a-redis-cache"></a>Creare una cache Redis

È possibile eseguire la procedura descritta in [Crea una cache nella Cache Redis Azure] per creare una nuova cache.

> [AZURE.NOTE] Salvare il __nome Host__ e __la chiave primaria__ per la cache, come questi saranno necessarie nei passaggi successivi.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Aggiungere i moduli di socket.io redis e redis

1. Da una riga di comando, impostare il __ \\nodo\\chat__ directory e usare il comando seguente.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] Le versioni specificate in questo comando sono le versioni utilizzate per la verifica in questo articolo.

1. Modificare il file __app.js__ per aggiungere le seguenti linee immediatamente dopo`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Sostituire __redishostname__ e __rediskey__ con il nome host e la chiave per la cache di Redis.

    Verrà creare una pubblicazione e sottoscrizione client per la cache di Redis creato in precedenza. I client vengono quindi utilizzati con la scheda per configurare Socket.IO per l'utilizzo della cache Redis per il trasferimento di messaggi e gli eventi tra istanze dell'applicazione

    > [AZURE.NOTE] Mentre la scheda __socket.io redis__ può comunicare direttamente ai Redis, la versione corrente non supporta l'autenticazione richiesta dalla cache di Azure Redis. Pertanto la connessione iniziale viene creata utilizzando il modulo __redis__ , quindi al client viene inviato alla scheda __socket.io redis__ .
    >
    > Mentre Azure Redis Cache supporta connessioni protette tramite porta 6380, i moduli utilizzati in questo esempio non supportano le connessioni sicure prima del 14/7/2014. Il codice precedente utilizza l'impostazione predefinita, porta non protetta del 6379.

1. Salvare modificate __app.js__

### <a name="commit-changes-and-redeploy"></a>Confermare le modifiche e ridistribuire

Dalla riga di comando nel __ \\nodo\\chat__ directory, utilizzare i comandi seguenti per applicare le modifiche e ridistribuire l'applicazione.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Una volta le modifiche sono state inserite nel server, è possibile ridimensionare il sito tra più istanze utilizzando il comando seguente.

    azure site scale instances --instances #

Dove __#__ è il numero di istanze da creare.

È possibile connettersi all'applicazione web da più browser o computer per verificare che i messaggi vengono inviati correttamente a tutti i client.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="connection-limits"></a>Limiti di connessione

Azure Web Apps è disponibile in più SKU che determinano le risorse disponibili per il sito. Include il numero di connessioni WebSocket consentite. Per ulteriori informazioni, vedere la [pagina Web App prezzi].

### <a name="messages-arent-being-sent-using-websockets"></a>Non vengono inviati messaggi con WebSockets

Se il browser client mantenerla fallback della tempo polling invece di usare WebSockets, potrebbe essere a causa di una delle operazioni seguenti.

* **È possibile limitare il trasporto a solo WebSockets**

    Affinché Socket.IO da utilizzare WebSockets come trasporto dei messaggi, client e server deve supportare WebSockets. Se uno o l'altro, non Socket.IO verrà negoziare un altro trasporto, ad esempio polling lungo. Elenco predefinito dei trasporti utilizzato da Socket.IO ` websocket, htmlfile, xhr-polling, jsonp-polling`. È possibile forzare, è possibile utilizzare solo WebSockets aggiungendo il codice seguente al file **app.js** dopo la riga contenente `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Si noti che browser meno recenti che non supportano WebSockets non saranno in grado di connettersi al sito, mentre il codice è attivo, come blocca le comunicazioni a WebSockets solo.

* **Utilizzo di SSL**

    WebSockets si basa su alcune inferiore intestazioni HTTP comuni, ad esempio l'intestazione **di aggiornamento** . Alcuni dispositivi di rete intermedio, ad esempio proxy web, è possono rimuovere queste intestazioni. Per evitare questo problema, è possibile stabilire la connessione WebSocket su SSL.

    Un modo semplice per eseguire questa operazione consiste nel configurare Socket.IO a `match origin protocol`. Questo indica Socket.IO per proteggere la comunicazione WebSockets diverso da quello di richiesta di HTTP/HTTPS origine per la pagina web. Se un browser utilizza un URL HTTPS per visitare il sito Web, comunicazioni WebSocket successive tramite Socket.IO verranno protette tramite SSL.

    Per modificare questo esempio per abilitare questa configurazione, aggiungere il codice seguente al file **app.js** dopo la riga contenente `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Verificare le impostazioni di config**

    App web Azure che ospitano applicazioni Node utilizzare **config** per indirizzare le richieste in arrivo per l'applicazione Node. Per WebSockets a funzionare correttamente con le applicazioni Node **config** deve contenere la voce seguente.

        <webSocket enabled="false"/>

    Consente di disattivare il modulo IIS WebSockets, che include l'implementazione di WebSockets e conflitti con Node moduli WebSocket specifici, ad esempio Socket.IO. Se questa riga non è presenta, o è impostata su `true`, è possibile il motivo per cui il trasporto WebSocket non funziona per l'applicazione.

    In genere, applicazioni Node non include **un config,** in modo che i siti Web di Azure genera automaticamente una per le applicazioni Node quando vengono distribuiti. Poiché il file viene generato automaticamente nel server, è necessario utilizzare il FTP o Invia URL per il sito Web per visualizzare il file. Sono disponibili i FTP e invia gli URL per il sito del portale classica selezionando un'app web e quindi il collegamento al **Dashboard** . Gli URL vengono visualizzati nella sezione **visualizzazione rapida** .

    > [AZURE.NOTE] **Config** viene generato da un sito Web di Azure solo se l'applicazione non è incluso in. Se si specifica **un config nella radice del progetto di applicazione** , verranno usato dalle App Web di Azure.

    Se non è presenta la voce o è impostata su un valore di `true`, allora è necessario creare un **config** nella radice dell'applicazione Node e specificare un valore di `false`.  Per riferimento, di seguito è riportato un predefinito **config** per un'applicazione che utilizza **app.js** come punto di ingresso.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Se l'applicazione utilizza un punto di ingresso diverso da **app.js**, è necessario sostituire tutte le occorrenze di **app.js** con il punto di ingresso corretto. Ad esempio, sostituire **app.js** con **server.js**.

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare], in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare un'applicazione di chat ospitata in un'app web Azure. È anche possibile ospitare questa applicazione come un servizio Cloud di Azure. Per informazioni sulle procedure eseguire questa operazione, vedere [creare un'applicazione di Chat Node con Socket.IO in un servizio Cloud di Azure].

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori Node].

## <a name="whats-changed"></a>Novità

* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla servizi Azure esistenti].

<!-- URL List -->

[Cache Redis Azure]: /documentation/services/redis-cache/
[Servizio di App Web App]: http://go.microsoft.com/fwlink/?LinkId=529714
[Pagina Web App prezzi]: http://go.microsoft.com/fwlink/?LinkId=511643
[Creare un'applicazione di Chat Node con Socket.IO in un servizio Cloud Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Servizio App Azure e l'impatto sulla servizi Azure esistenti]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro per sviluppatori Node]: /develop/nodejs/
[Provare il servizio di App]: http://go.microsoft.com/fwlink/?LinkId=523751
[Affinità istanza nei siti Web Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Creare una cache nella Cache Redis Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[Socket.IO redis]: https://github.com/socketio/socket.io-redis
[Socket.IO GitHub archivio]: https://github.com/socketio/socket.io
[ZIP o GZ archiviati release]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
