<properties
    pageTitle="App web Node mediante il servizio di tabella Azure"
    description="Questa esercitazione viene illustrato come utilizzare il servizio di Azure tabella per memorizzare i dati da un'applicazione di Node ospitato in Azure App servizio Web Apps."
    tags="azure-portal"
    services="app-service\web, storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="nodejs-web-app-using-the-azure-table-service"></a>App web Node mediante il servizio di tabella Azure

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio di tabella fornito da Gestione dei dati di Azure per archiviare e accedere ai dati da un'applicazione di [nodo] ospitata in [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. In questa esercitazione si presuppone alcuni già esperienza nell'uso di nodo e [operazioni].

Si apprenderà:

* Come usare npm (gestore pacchetto nodi) per installare i moduli di nodo

* Come lavorare con il servizio di tabella di Azure

* Informazioni su come usare CLI Azure per creare un'app web.

Seguendo questa esercitazione si creerà un semplice basato sul web applicazione "elenco da fare" che consente la creazione, il recupero e il completamento di attività. Le attività sono archiviate nel servizio di tabella.

Ecco l'applicazione completata:

![Una pagina web contenente un elenco di attività vuota][node-table-finished]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="prerequisites"></a>Prerequisiti

Prima di seguendo le istruzioni fornite in questo articolo, verificare che si siano installati i seguenti:

* [nodo] versione 0.10.24 o versioni successive

* [Operazioni]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione Azure. L'app utilizzerà l'account per archiviare elementi attività.

1.  Accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic sull'icona di **Nuovo** in basso a sinistra del portale e quindi fare clic su **dati + spazio di archiviazione** > **lo spazio di archiviazione**. Specificare un nome univoco per l'account di archiviazione e creare un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) per renderla.

    ![Pulsante nuovo](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

    Quando l'account di archiviazione è stato creato, il pulsante **notifiche** verrà flash verde **successo** e blade dell'account di archiviazione è aperto per mostrare appartiene il nuovo gruppo di risorse che è stato creato.

5. In blade dell'account di archiviazione, fare clic su **Impostazioni** > **tasti**. Copiare la chiave primaria access negli Appunti.

    ![Tasto di scelta rapida][portal-storage-access-keys]


##<a name="install-modules-and-generate-scaffolding"></a>Installare moduli e generare supporto temporaneo

In questa sezione verranno creare una nuova applicazione di nodo e utilizzare npm per aggiungere pacchetti di modulo. Per questa applicazione occorre usare i moduli [Express] e [Azure] . Il modulo Express fornisce un Controller di visualizzazione del modello per nodo, mentre i moduli di Azure consente la connessione al servizio di tabella.

### <a name="install-express-and-generate-scaffolding"></a>Installare express e generare supporto temporaneo

1. Dalla riga di comando, creare una nuova directory denominata **elenco attività** e passare alla directory.  

2. Immettere il comando seguente per installare il modulo Express.

        npm install express-generator@4.2.0 -g

    A seconda del sistema operativo, potrebbe essere necessario inserire 'sudo' prima del comando:

        sudo npm install express-generator@4.2.0 -g

    Verrà visualizzato l'output simile al seguente:

        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)

    > [AZURE.NOTE] La "-g' parametro consente di installare il modulo a livello globale. In questo modo, possiamo usare **express** per generare pagine di supporto temporaneo app web senza dover immettere le informazioni sul percorso aggiuntive.

4. Per creare pagine di supporto temporaneo per l'applicazione, immettere il comando **express** :

        express

    L'output di questo comando è simile all'esempio seguente:

           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www

           install dependencies:
             $ cd . && npm install

           run the app:
             $ DEBUG=my-application ./bin/www

    È ora possibile diverse nuove directory e i file nella directory **elenco attività** .

### <a name="install-additional-modules"></a>Installare moduli aggiuntivi

Uno dei file di tale **express** crea è **package.json**. Questo file contiene un elenco delle dipendenze modulo. In un secondo momento, quando si distribuisce un'applicazione di App del servizio Web Apps, questo file determina quali moduli è necessario installare in Azure.

Dalla riga di comando, immettere il comando seguente per installare i moduli descritti nel file **package.json** . Potrebbe essere necessario utilizzare 'sudo'.

    npm install

L'output di questo comando è simile all'esempio seguente:

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


Successivamente, immettere il comando seguente per installare i moduli di [azure], [nodo uuid], [nconf] e [asincrono] :

    npm install azure-storage node-uuid async nconf --save

Il **-salvare** contrassegno aggiunge voci per i moduli del file **package.json** .

L'output di questo comando è simile all'esempio seguente:

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>Creare l'applicazione

Ora si è pronti per creare l'applicazione.

### <a name="create-a-model"></a>Creare un modello

Un *modello* è un oggetto che rappresenta i dati dell'applicazione. Per l'applicazione, l'unico modello è un oggetto di attività, che rappresenta un elemento nell'elenco di cose da fare. Attività saranno disponibili i campi seguenti:

- PartitionKey
- RowKey
- nome (stringa)
- categoria (stringa)
- completate (Boolean)

**PartitionKey** e **RowKey** vengono utilizzati dal servizio tabella come chiavi di tabelle. Per ulteriori informazioni, vedere [informazioni sul modello di dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).


1. Nella directory **dell'elenco attività** , creare una nuova directory denominata **modelli**.

2. Creare un nuovo file denominato **task.js**nella cartella **modelli** . In questo file conterrà il modello per le attività create dall'applicazione in uso.

3. All'inizio del file **task.js** , aggiungere il codice seguente per fare riferimento alle librerie necessari:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Aggiungere il codice seguente per definire ed esportare l'oggetto di attività. L'oggetto è responsabile per la connessione alla tabella.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Aggiungere il codice riportato di seguito per definire altri metodi nell'oggetto di attività, che consentono interazioni con i dati archiviati nella tabella:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Salvare e chiudere il file **task.js** .

### <a name="create-a-controller"></a>Creare un controller

Un *controller di* gestione delle richieste HTTP ed esegue il rendering risposta HTML.

1. Nella directory **dell'elenco attività/indirizza** , creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il codice seguente **tasklist.js**. Consente di caricare i moduli vengono utilizzati da **tasklist.js**azure e asincrono. Definisce anche la funzione **elenco attività** , che verrà passata un'istanza dell'oggetto **attività** definita in precedenza:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

3. Definire un oggetto **elenco attività** .

        function TaskList(task) {
          this.task = task;
        }


4. Aggiungere **l'elenco attività**di metodi seguenti:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this;
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }


### <a name="modify-appjs"></a>Modificare app.js

1. Aprire il file **app.js** directory **elenco attività** . Il file è stato creato in precedenza, eseguire il comando **express** .

2. All'inizio del file, aggiungere le operazioni seguenti per caricare il modulo di azure, impostare il nome della tabella, chiave di partizione e impostare le credenziali di spazio di archiviazione utilizzate in questo esempio:

        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");

    > [AZURE.NOTE] nconf verrà caricato i valori di configurazione da variabili di ambiente o il file **config.json** , verrà creato in un secondo momento.

3. Nel file app.js, scorrere fino a nel punto in cui è visualizzata la riga seguente:

        app.use('/', routes);
        app.use('/users', users);

    Sostituire le righe sopra con il codice riportato di seguito. Verrà inizializzato un'istanza di <strong>attività</strong> con una connessione al proprio account di archiviazione. Viene passato per l' <strong>elenco attività</strong>, che verrà utilizzato per comunicare con il servizio di tabella:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4. Salvare il file **app.js** .

### <a name="modify-the-index-view"></a>Modificare la visualizzazione di indice analitico

1. Aprire il file **tasklist/views/index.jade** in un editor di testo.

2. Sostituire l'intero contenuto del file con il codice riportato di seguito. Definisce una visualizzazione in cui vengono visualizzate le attività esistenti, nonché un modulo per l'aggiunta di nuove attività e il contrassegno di quelli esistenti come completato.

        extends layout

        block content
          h1= title
          br

          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if (typeof tasks === "undefined")
                tr
                  td
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="item[name]", type="textbox")
            label Item Category:
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Salvare e chiudere il file **index.jade** .

### <a name="modify-the-global-layout"></a>Modificare il layout globale

Il file **layout.jade** nella directory **visualizzazioni** è un modello globale per gli altri file **.jade** . In questo passaggio verrà modificato in modo da utilizzare [Avvio Twitter](https://github.com/twbs/bootstrap), ovvero un toolkit che consente di progettare un'app web dall'aspetto interessante facilmente.

Scaricare ed estrarre i file per [l'Avvio Twitter](http://getbootstrap.com/). Copiare il file **bootstrap.min.css** dalla cartella avvio **css** nella directory **pubblico/fogli di stile** dell'applicazione.

Dalla cartella **visualizzazioni** aprire **layout.jade** e sostituire l'intero contenuto con le operazioni seguenti:

    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
          a.navbar-brand(href='/') My Tasks
        block content

### <a name="create-a-config-file"></a>Creare un file di configurazione

Per eseguire l'app in locale, si verranno inserire le credenziali di archiviazione Azure in un file di configurazione. Creare un file denominato * *config.json* *con JSON seguenti:

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Sostituire **il nome di account di archiviazione** con il nome dell'account di archiviazione creata in precedenza e sostituire **il tasto di scelta di spazio di archiviazione** con la chiave primaria accesso per il proprio account di archiviazione. Per esempio:

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Salvare questo file *un livello di directory superiore* rispetto alla directory **elenco attività** , come segue:

    parent/
      |-- config.json
      |-- tasklist/

Il motivo per eseguire questa operazione consiste nell'evitare la verifica di file di configurazione nel controllo origine, in cui potrebbero verificarsi pubblico. Quando si distribuisce l'app in Azure, verrà usata variabili invece di un file di configurazione.


## <a name="run-the-application-locally"></a>Eseguire l'applicazione localmente

Per testare l'applicazione sul computer locale, procedere come segue:

1. Dalla riga di comando, passare alla directory **elenco attività** .

2. Usare il comando seguente per avviare l'applicazione in locale:

        npm start

3. Aprire un web browser e passare a http://127.0.0.1:3000.

    Viene visualizzata una pagina web simile al seguente.

    ![Una pagina Web la visualizzazione di un elenco di attività vuota][node-table-finished]

4. Per creare un nuovo elemento di attività, immettere un nome e una categoria e fare clic su **Aggiungi elemento**. 

6. Per contrassegnare un'attività come completata e fare clic su **Aggiorna attività** **completata** .

    ![Immagine del nuovo elemento nell'elenco delle attività][node-table-list-items]

Anche se l'applicazione è in esecuzione in locale, della memorizzazione dei dati nel servizio di Azure tabella.

## <a name="deploy-your-application-to-azure"></a>Distribuire l'applicazione in Azure

La procedura descritta in questa sezione utilizza gli strumenti della riga di comando Azure per creare una nuova app web nel servizio di App e quindi utilizzarla fra per distribuire l'applicazione. Per eseguire questa procedura è necessario disporre di un abbonamento a Azure.

> [AZURE.NOTE] Questa procedura può essere eseguita anche tramite il [Portale di Azure](https://portal.azure.com/). Vedere [creare e distribuire un'app web Node in Azure App servizio].
>
> Se questo è il primo web app che è stato creato, è necessario utilizzare il portale di Azure per distribuire questa applicazione.

Per iniziare, installare [Azure CLI] immettendo il comando seguente dalla riga di comando:

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>Importare le impostazioni di pubblicazione

In questo passaggio scaricato un file contenente le informazioni sull'abbonamento.

1. Immettere il comando seguente:

        azure account download

    Questo comando viene avviato un browser e consente di accedere alla pagina di download. Se richiesto, accedere con l'account associato all'abbonamento Azure.

    <!-- ![The download page][download-publishing-settings] -->

    Il download di file viene avviato automaticamente. in caso contrario, è possibile fare clic sul collegamento all'inizio della pagina per scaricare manualmente il file. Salvare il file e prendere nota del percorso file.

2. Immettere il comando seguente per importare le impostazioni:

        azure account import <path-to-file>

    Specificare il percorso e il nome del file di Impostazioni pubblicazione che è stato scaricato nel passaggio precedente.

3. Dopo avere importate le impostazioni, eliminare il file di impostazioni di pubblicazione. Non è più necessario e contiene informazioni riservate sull'abbonamento Azure.

### <a name="create-an-app-service-web-app"></a>Creare un'app web App servizio

1. Dalla riga di comando, passare alla directory **elenco attività** .

2. Usare il comando seguente per creare una nuova app web.

        azure site create --git

    Verrà richiesto per il nome dell'app web e il percorso. Specificare un nome univoco e selezionare nella stessa posizione geografica dell'account di archiviazione Azure.

    Il `--git` parametro crea un repository fra su Azure per questa applicazione web. Un repository fra nella directory corrente viene inizializzato anche se non è presente e aggiunge [fra remota] denominata 'azure, che viene utilizzato per pubblicare l'applicazione in Azure. Infine, viene creato un file **config** , che contiene le impostazioni usate da Azure per applicazioni nodo host. Se si omette il `--git` parametro ma la directory contiene un repository fra, il comando ancora creerà azure' ' remoto.

    Al termine di questo comando, si vedrà output simile al seguente. Si noti che la riga che inizia con **sito Web creato in** contiene l'URL per l'applicazione web.

        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK

    > [AZURE.NOTE] Se questo è il primo servizio App web app per l'abbonamento, verranno salvati come utilizzare il portale di Azure per creare l'applicazione web. Per ulteriori informazioni, vedere [creare e distribuire un'app web Node in Azure App servizio].

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

In questo passaggio si aggiungerà variabili alla configurazione web app in Azure.
Dalla riga di comando, immettere quanto segue:

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Sostituire **<storage account name>** con il nome dello spazio di archiviazione account creato in precedenza e sostituire **<storage access key>** con la chiave primaria accesso per il proprio account di archiviazione. Utilizzare gli stessi valori del file config.json creato in precedenza.

In alternativa, è possibile impostare le variabili di ambiente nel [Portale di Azure](https://portal.azure.com/):

1.  Aprire blade dell'applicazione web facendo clic su **Sfoglia** > **Web Apps** > il nome dell'app web.

1.  In blade dell'applicazione web, fare clic su **Tutte le impostazioni** > **Le impostazioni dell'applicazione**.

    <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  Scorrere fino alla sezione **impostazioni di App** e aggiungere coppie di parole chiave/valore.

    ![Impostazioni dell'App](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. Fare clic su **Salva**.


### <a name="publish-the-application"></a>Pubblicare l'applicazione

Per pubblicare l'app, eseguire il commit fra i file di codice e quindi push azure/schema.

1. Impostare le credenziali di distribuzione.

        azure site deployment user set <name> <password>

2. Aggiungere e salvare i file dell'applicazione.

        git add .
        git commit -m "adding files"

3. Inserire il commit a App servizio web app:

        git push azure master

    Utilizzare **master** come diramazione di destinazione. Alla fine della distribuzione, verrà visualizzata un'istruzione simile al seguente:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

4. Una volta completata l'operazione push, passare all'URL app web restituito in precedenza il `azure create site` comando per visualizzare l'applicazione.


## <a name="next-steps"></a>Passaggi successivi

Mentre in questo articolo viene descritta mediante il servizio di tabella per memorizzare le informazioni, è anche possibile usare [MongoDB](https://mlab.com/azure/). 

## <a name="additional-resources"></a>Risorse aggiuntive

[CLI Azure]

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Compilare e distribuire un'app web Node nel servizio App Azure]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[nodo]: http://nodejs.org
[Operazioni]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Operazioni remote]: http://git-scm.com/docs/git-remote

[CLI Azure]: ../xplat-cli-install.md

[Azure]: https://github.com/Azure/azure-sdk-for-node
[nodo uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[asincrono]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 
<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
