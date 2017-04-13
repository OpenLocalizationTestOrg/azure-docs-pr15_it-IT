<properties
    pageTitle="Introduzione a node web apps nel servizio App Azure"
    description="Informazioni su come distribuire un'applicazione di Node in un'app web nel servizio App Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introduzione a node web apps nel servizio App Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In questa esercitazione viene illustrato come creare una semplice applicazione [Node] e distribuirlo al [Servizio di Azure App] da un ambiente della riga di comando, ad esempio cmd.exe o bash. Le istruzioni disponibili in questa esercitazione possono essere seguite dal sistema operativo che è possibile eseguire Node.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Prerequisiti

- [Node]
- [Bower]
- [Yeoman]
- [Operazioni]
- [CLI Azure]
- Un account di Microsoft Azure. Se non si dispone di un account, è possibile [iscriversi per una versione di valutazione gratuita] o [attivare i vantaggi della propria sottoscrizione Visual Studio].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Creare e distribuire un'app web Node semplice

1. Aprire terminale della riga di comando preferito e installare [Express generatore per Yeoman].

        npm install -g generator-express

2. `CD`per una cartella di lavoro e generare un'app express utilizzando la sintassi seguente:

        yo express
        
    Scegliere le opzioni seguenti quando viene chiesto se:  

    `? Would you like to create a new directory for your project?`**Sì**  
    `? Enter directory name`**{NomeApplicazione}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Nessuno**  
    `? Select a database to use:`**Nessuno**  
    `? Select a build tool to use:`**Noioso**

3. `CD`per la directory radice della nuova app e avviarlo per assicurarsi che viene eseguito nell'ambiente di sviluppo:

        npm start

    Nel browser passare al <http://localhost:3000> per assicurarsi che è possibile vedere la home page Express. Dopo la verifica dell'esecuzione dell'app correttamente, utilizzare `Ctrl-C` per interromperlo.
    
1. Modificare la modalità ASM e accedere a Azure (è necessario [Azure CLI](#prereq)):

        azure config mode asm
        azure login

    Eseguire la richiesta di continuare l'account di accesso in un browser con un account Microsoft che contiene l'abbonamento Azure.

2. Verificare che si trova ancora nella cartella principale dell'app e quindi crea la risorsa app servizio App in Azure con un nome univoco app con il comando successivo. Ad esempio: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Eseguire la richiesta di selezione di un'area per la distribuzione di Azure. Se le credenziali di distribuzione fra/FTP mai stato configurato per l'abbonamento Azure, verrà richiesto anche per crearle.

3. Aprire il file./config/config.js dalla radice dell'applicazione e modificare la porta di produzione per `process.env.port`; il `production` proprietà la `config` oggetto avrà un aspetto simile al seguente:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    In questo modo l'app Node rispondere per web richieste sulla porta predefinita che iisnode in attesa.
    
4. Aprire./package.json e aggiungere la `engines` proprietà per [specificare la versione Node desiderata](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Salvare le modifiche, quindi utilizzare fra per distribuire l'app in Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Il generatore di Express fornisce già un file .gitignore, pertanto la `git push` non utilizzare larghezza di banda si prova a caricare il node_modules / directory.

5. Infine, avviare l'app Azure live nel browser:

        azure site browse

    Verrà visualizzata un'app web Node esecuzione dinamica in Azure App servizio.
    
    ![Esempio di esplorazione all'applicazione distribuita.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Aggiornare un'app web Node

Per apportare gli aggiornamenti a un'applicazione web Node in esecuzione nell'applicazione del servizio, è sufficiente eseguire `git add`, `git commit`, e `git push` come si è fatto per la distribuzione prima di tutto un'app web.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Come servizio App distribuisce l'app Node

Azure App utilizza [iisnode] per eseguire le applicazioni di Node. CLI Azure e il motore di Kudu (distribuzione fra) interagiscono per fornire un'esperienza semplificata quando si sviluppa e distribuire apps Node dalla riga di comando. 

- `azure site create --git`riconosce il modello Node comune di server.js o app.js e crea un iisnode.yml nella directory radice. È possibile utilizzare il file di personalizzazione di iisnode.
- In `git push azure master`, Kudu consente di automatizzare le attività di distribuzione seguenti:

    - Se package.json nella radice archivio, eseguire `npm install --production`.
    - Generare un config per iisnode che fa riferimento a uno script start in package.json (ad esempio server.js o app.js).
    - Personalizzare config per preparare l'app per il debug con nodo controllo.
    
## <a name="use-a-nodejs-framework"></a>Utilizzare una struttura Node

Se si utilizza un framework Node comuni, ad esempio [Sails.js] [ SAILSJS] o [MEAN.js] [ MEANJS] per sviluppare App, è possibile distribuire i servizi di App. Framework Node popolari hanno loro quirks specifici e le relative dipendenze pacchetto mantenere Guida aggiornate. Servizio di App invece i log stdout e stderr disponibili, in modo che è possibile controllarlo esattamente con l'app e apportare modifiche a seconda dei casi. Per ulteriori informazioni, vedere [accedere ai log stdout e stderr da iisnode](#iisnodelog).

Le esercitazioni seguenti illustrano come per l'uso con un framework specifico nel servizio App:

- [Distribuire un'app web Sails.js al servizio App Azure]
- [Creare un'applicazione di chat Node con Socket.IO nel servizio App Azure]
- [Come utilizzare io.js con Azure App servizio Web Apps]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Utilizzare un modulo Node specifico

Il flusso di lavoro tipico, si indica servizio App da utilizzare un modulo Node specifico come di consueto in package.json.
Per esempio:

    "engines": {
        "node": "6.6.0"
    }, 

Motore di distribuzione di Kudu determina quale motore Node da utilizzare nell'ordine seguente:

- Prima di tutto, esaminare iisnode.yml per verificare se `nodeProcessCommandLine` specificato. In caso affermativo, quindi usare.
- Successivamente, esaminare package.json per verificare se `"node": "..."` specificato nel `engines` oggetto. In caso affermativo, quindi usare.
- Scegliere una versione di Node predefinita per impostazione predefinita.

>[AZURE.NOTE] Si consiglia di definire in modo esplicito il modulo Node desiderato. Modifica la versione di Node predefinita e potrebbero verificarsi errori nelle applicazioni web Azure poiché la versione di Node predefinita non è appropriata per l'app.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Accedere ai log stdout e stderr da iisnode

Per leggere i registri iisnode, seguire questa procedura.

> [AZURE.NOTE] Dopo aver completato la procedura seguente, file di log potrebbero non essere fino a quando non si verifica un errore.

1. Aprire il file iisnode.yml che fornisce CLI Azure.

2. Impostare i due parametri seguenti: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Insieme, dicono iisnode nel servizio di App per inserire l'output stdout e stderror nella D:\home\site\wwwroot\**iisnode** directory.

3. Salvare le modifiche, quindi inviare le modifiche apportate ai Azure con i comandi seguenti operazioni:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode è ora configurato. Passaggi successivi viene illustrato come accedere a questi file di log.
     
4. Nel browser accedere alla console di debug Kudu per l'app, si trova in:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Questo URL è diverso da URL web app per l'aggiunta di "*.scm.*" il nome DNS. Se si omette tale aggiunta all'URL, verrà visualizzato un errore 404.

5. Passare a D:\home\site\wwwroot\iisnode

    ![Spostarsi al percorso del file di log iisnode.][iislog-kudu-console-find]

6. Fare clic sull'icona di **Modifica** per il log che si vuole leggere. È anche possibile fare clic su **Download** o **eliminare** se si desidera.

    ![Apertura di un file di log iisnode.][iislog-kudu-console-open]

    A questo punto è possibile visualizzare il log per facilitare il debug della distribuzione del App.
    
    ![Esaminare un file di log iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Eseguire il debug l'app con nodo controllo

Se si Usa controllo nodo per eseguire il debug le app Node, è possibile utilizzare per l'applicazione di servizio App live. Controllo nodo è preinstallato in installazione iisnode servizio di App. E se si distribuisce tramite fra, config generato automaticamente dal Kudu contiene già tutta la configurazione che è necessario attivare nodo controllo.

Per abilitare controllo nodo, procedere come segue:

1. Aprire iisnode.yml in principale dell'archivio e specificare i parametri seguenti: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Salvare le modifiche, quindi inviare le modifiche apportate ai Azure con i comandi seguenti operazioni:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. A questo punto, semplicemente passare al file iniziale dell'app come specificato dallo script avvia il package.json con l'opzione /debug aggiunto all'URL. Per esempio

        http://{appname}.azurewebsites.net/server.js/debug
    
    Oppure
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Altre risorse

- [Specifica di una versione Node in un'applicazione di Azure](../nodejs-specify-node-version-azure-apps.md)
- [Procedure consigliate e la Guida alla risoluzione dei problemi per le applicazioni Node su Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Come eseguire il debug di un'app web Node nel servizio App Azure](web-sites-nodejs-debug.md)
- [Utilizzo di moduli Node con applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure App servizio Web App: node](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro per sviluppatori Node](/develop/nodejs/)
- [Guida introduttiva a App web di servizio App Azure](app-service-web-get-started.md)
- [Esplorazione Console di Debug Kudu Super segreta]

<!-- URL List -->

[CLI Azure]: ../xplat-cli-install.md
[Servizio App Azure]: ../app-service/app-service-value-prop-what-is.md
[attivare i vantaggi della propria sottoscrizione Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Creare un'applicazione di chat Node con Socket.IO nel servizio App Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Distribuire un'app web Sails.js al servizio App Azure]: ./app-service-web-nodejs-sails.md
[Esplorazione Console di Debug Kudu Super segreta]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Generatore Express per Yeoman]: https://github.com/petecoop/generator-express
[Operazioni]: http://www.git-scm.com/downloads
[Come utilizzare io.js con Azure App servizio Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[iscriversi a una versione di valutazione gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
