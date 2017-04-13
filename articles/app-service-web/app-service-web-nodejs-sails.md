<properties
    pageTitle="Distribuire un'app web Sails.js al servizio App Azure"
    description="Informazioni su come distribuire un'applicazione di Node servizio App Azure. In questa esercitazione viene illustrato come distribuire un'app web Sails.js."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Distribuire un'app web Sails.js al servizio App Azure

In questa esercitazione viene illustrato come distribuire un'app Sails.js al servizio App Azure. Durante il processo, è possibile raccogliere conoscenza generale su come configurare l'app node per l'esecuzione del servizio di App. 

Avere familiarità con di Sails.js. In questa esercitazione non è destinata consentono di sui problemi correlati all'esecuzione Sail.js in generale.


## <a name="prerequisites"></a>Prerequisiti

- [Node](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [Operazioni](http://www.git-scm.com/downloads)
- [CLI Azure](../xplat-cli-install.md)
- Un account di Microsoft Azure. Se non si dispone di un account, è possibile [iscriversi per una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o [attivare i vantaggi della propria sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Per visualizzare il servizio di App Azure in azione prima di iscriversi a un account Azure, accedere al [Servizio di App provare](http://go.microsoft.com/fwlink/?LinkId=523751). Non esiste, è possibile creare immediatamente un'app starter breve nel servizio di App, ovvero non carta di credito obbligatorio, senza impegni.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Passaggio 1: Creare un'app Sails.js localmente

Prima di tutto creare velocemente un'app Sails.js predefinito nel proprio ambiente di sviluppo procedendo come segue:

1. Aprire terminale della riga di comando preferito e `CD` a una cartella di lavoro.

2. Creare un'app Sails.js ed eseguirlo:

        sails new <appname>
        cd <appname>
        sails lift

    Assicurarsi che è possibile passare alla home page predefinita in http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Passaggio 2: Creare la risorsa applicazione Azure

Creare la risorsa servizio App in Azure. Si desidera distribuire l'app Sails.js a tale in un secondo momento.

1. accedere a mi piace Azure così:
1. In terminale stessa, modificare la modalità ASM e accedere al Azure:

        azure config mode asm
        azure login

    Eseguire la richiesta di continuare l'account di accesso in un browser con un account Microsoft che contiene l'abbonamento Azure.

2. Verificare che si è ancora presente nella cartella principale del progetto Sails.js. Creare la risorsa app servizio App in Azure con un nome univoco app con il comando successivo. URL dell'applicazione web è http://&lt;NomeApplicazione >. azurewebsites.net.

        azure site create --git <appname>

    Eseguire la richiesta di selezione di un'area per la distribuzione di Azure. Se le credenziali di distribuzione fra/FTP mai stato configurato per l'abbonamento Azure, verrà richiesto anche per crearle.

    Dopo aver creata la risorsa app servizio App:

    - App Sails.js è inizializzato fra,
    - L'archivio locale inizializzato fra sia connesso alla nuova app App servizio come un fra remoto, denominata "azure" e
    - E iisnode.yml file viene creato nella directory radice. È possibile utilizzare il file per configurare [iisnode](https://github.com/tjanczuk/iisnode), quale servizio App viene utilizzato per eseguire Node app.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Passaggio 3: Configurare e distribuire l'app Sails.js

 Utilizzo di un'app Sails.js nel servizio App è costituito da tre passaggi principali:

 - Configurare l'applicazione per l'esecuzione del servizio di App
 - Distribuire al servizio di App
 - Lettura dei registri stderr e stdout per la risoluzione dei problemi di distribuzione

Procedere come segue:

1. Aprire il nuovo file di iisnode.yml nella directory radice e aggiungere due righe seguenti:

        loggingEnabled: true
        logDirectory: iisnode

    La registrazione è abilitata per iisnode. Per ulteriori informazioni sul funzionamento di questa operazione, vedere  [accedere ai log stdout e stderr da iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Aprire config/env/production.js per configurare l'ambiente di produzione e impostare `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Documentazione di queste impostazioni di configurazione è disponibile nella  [Documentazione Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    È necessario assicurarsi che sia compatibile con le unità di rete di Azure [noioso](https://www.npmjs.com/package/grunt) . Versioni noioso minore 1.0.0 utilizza un pacchetto non aggiornate [glob](https://www.npmjs.com/package/glob) (minore 5.0.14), che non supporta le unità di rete. 

3. Aprire package.json e modificare il `grunt` versione `1.0.0` e rimuovere tutti `grunt-*` pacchetti. Il `dependencies` proprietà dovrebbe risultare analoga alla seguente:

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. In package.json, aggiungere la seguente `engines` proprietà per impostare la versione Node su quello che si desidera.

        "engines": {
            "node": "6.6.0"
        },

6. Salvare le modifiche e verificare le modifiche per assicurarsi che l'app viene ancora eseguito in locale. A tale scopo, eliminare il `node_modules` cartella e quindi eseguire:

        npm install
        sails lift

4. A questo punto, utilizzare fra per distribuire l'app in Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Infine, avviare l'app Azure live nel browser:

        azure site browse

    Viene visualizzato la stessa Sails.js home page.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Risolvere i problemi di distribuzione

Se l'applicazione Sails.js non riesce per qualche motivo nel servizio di App, trovare i log stderr per semplificare la soluzione.
Per ulteriori informazioni, vedere [accedere ai log stdout e stderr da iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Se è stato avviato correttamente, log stdout risulterà familiare messaggio:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

È possibile controllare granularità dei registri stdout nel file [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Connettersi a un database in Azure

Per connettersi a un database di Azure, creare il database di propria scelta in Azure, ad esempio Database SQL Azure, MySQL, MongoDB, Cache Azure (Redis) e così via e usare corrispondente [scheda archivio dati](https://github.com/balderdashy/sails#compatibility) per la connessione. La procedura descritta in questa sezione mostra come connettersi a un database MySQL in Azure.

1. Eseguire le esercitazioni [di seguito](../store-php-create-mysql-database.md) per creare un database MySQL in Azure.

2. Dal terminale della riga di comando, installare la scheda MySQL:

        npm install sails-mysql --save

3. Aprire config/connections.js e aggiungere il seguente oggetto connessione all'elenco: 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Per ogni variabile di ambiente (`process.env.*`), è necessario impostare nel servizio di App. A tale scopo, eseguire i comandi seguenti dal terminale. Tutte le informazioni di connessione, è necessario sono nel portale di Azure (vedere [connessione al database MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Inserire le impostazioni in Impostazioni applicazione Azure mantiene dati riservati fuori controllo del codice sorgente (fra). Punto, configurare l'ambiente di sviluppo per usare le stesse informazioni di connessione.

4. Aprire config/local.js e aggiungere l'oggetto connessioni seguenti:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Questa configurazione sostituisce le impostazioni nel file di config/connections.js per l'ambiente locale. In questo file viene escluso .gitignore predefinito di un progetto in modo che non verranno archiviato in fra. A questo punto, comunque in grado di connettersi al database MySQL da un'app web Azure e dall'ambiente di sviluppo locale.

4. Aprire config/env/production.js per configurare l'ambiente di produzione e aggiungere quanto segue `models` oggetto:

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Aprire config/env/development.js per configurare l'ambiente di sviluppo e aggiungere quanto segue `models` oggetto:

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`Consente di utilizzare funzionalità di migrazione del database per creare e aggiornare le tabelle di database il MySQL facilmente. Tuttavia, `migrate: 'safe'` viene utilizzato per l'ambiente di Azure (produzione) perché Sails.js non è possibile utilizzare `migrate: 'alter'` in un ambiente di produzione (vedere la  [Documentazione Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Da terminale, [Genera](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) un Sails.js [API progetto](http://sailsjs.org/documentation/concepts/blueprints) nel modo verrebbe normalmente, quindi eseguito `sails lift` per creare il database con la migrazione del database Sails.js. Per esempio:

         sails generate api mywidget
         sails lift

    Il `mywidget` modello generato da questo comando non contiene alcun dato, ma è possibile utilizzare per mostrare che è disponibile la connettività di database.
    Quando si esegue `sails lift`, viene creato tabelle mancante per i modelli di applicazione ottimali.

6. Accedere a quella API appena creato nel browser. Per esempio:

        http://localhost:1337/mywidget/create
    
    L'API deve restituire la voce creata al mittente nella finestra del browser, il che significa che il database viene creato correttamente.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. A questo punto, inviare le modifiche apportate ai Azure e individuare l'app per verificare che il corretto funzionamento.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Accedere a quella API dell'app web Azure. Per esempio:

        http://<appname>.azurewebsites.net/mywidget/create

    Se l'API restituisce un'altra nuova voce, un'applicazione web Azure sta parlando al database MySQL.

## <a name="more-resources"></a>Altre risorse

- [Introduzione a node web apps nel servizio App Azure](app-service-web-nodejs-get-started.md)
- [Utilizzo di moduli Node con applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
