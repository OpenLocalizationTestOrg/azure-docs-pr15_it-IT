<properties 
    pageTitle="Web app con archiviazione tabella (Node) | Microsoft Azure" 
    description="Esercitazione basato sul Web App con tutorial Express mediante l'aggiunta di servizi di archiviazione di Azure e il modulo di Azure." 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>Applicazione Web che Node utilizza lo spazio di archiviazione

## <a name="overview"></a>Panoramica

In questa esercitazione, sarà possibile estendere l'applicazione creata nell'esercitazione [Applicazione Web che Node utilizza Express] con le raccolte di Client Microsoft Azure per node per lavorare con i servizi di gestione dati. Sarà possibile estendere l'applicazione per creare un'applicazione di elenco di attività basate sul web che è possibile distribuire in Azure. Elenco attività consente di recuperare le attività, aggiungere nuove attività e contrassegnare le attività come completata.

Le attività vengono archiviate in archiviazione Azure. Spazio di archiviazione Azure consente l'archiviazione di dati non strutturati tolleranza e altamente disponibile. Spazio di archiviazione Azure include diverse strutture di dati in cui è possibile archiviare e accedere ai dati ed è possibile sfruttare i servizi di archiviazione da API incluse in Azure SDK per Node o tramite API REST. Per ulteriori informazioni, vedere [memorizzazione e accesso ai dati di Azure].

In questa esercitazione si presuppone che sia stata completata [l'Applicazione Web Node] e [Node con Express][applicazione Web Node mediante Express] (esercitazioni).

Si apprenderà:

-   Come funzionano con il motore di modello Jade
-   Come usare i servizi di gestione dei dati di Azure

Schermata dell'applicazione completata è inferiore a:

![La pagina web completa in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Impostazione delle credenziali di spazio di archiviazione in config

Per accedere all'archiviazione Azure, è necessario passare le credenziali di spazio di archiviazione. A tale scopo, si utilizzano le impostazioni dell'applicazione Web. config.
Queste impostazioni verranno passate come variabili di ambiente al livello di nodo, che vengono quindi letti da Azure SDK.

> [AZURE.NOTE] Le credenziali di spazio di archiviazione vengono utilizzate solo quando l'applicazione viene distribuito in Azure. Durante l'esecuzione nell'emulatore, l'applicazione utilizzerà emulatore lo spazio di archiviazione.

Per recuperare le credenziali dell'account di archiviazione e aggiungerle a impostazioni config, procedere come segue:

1.  Se non è già aprire, avviare Azure PowerShell dal menu di **avvio** , espandere **tutti i programmi, Azure**, rapida **PowerShell Azure**e quindi selezionare **Esegui come amministratore**.

2.  Passare alla cartella contenente l'applicazione. Ad esempio, c\\nodo\\elenco attività\\WebRole1.

3.  Nella finestra di Powershell Azure immettere il seguente cmdlet per recuperare le informazioni sull'account di archiviazione:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Consente di recuperare l'elenco di account di archiviazione e account chiavi associato con il servizio di hosting.

    > [AZURE.NOTE] Poiché Azure SDK crea un account di archiviazione quando si distribuisce un servizio, un account di archiviazione deve essere già presente dalla distribuzione dell'applicazione in Guide precedente.

4.  Aprire il file **ServiceDefinition.csdef** contenente le impostazioni dell'ambiente utilizzati quando l'applicazione viene distribuito in Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Inserire il blocco seguente in elemento **dell'ambiente** , sostituendo {ACCOUNT di archiviazione} e {lo spazio di archiviazione tasto} con il nome dell'account e la chiave primaria per l'account di archiviazione che si desidera utilizzare per la distribuzione:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Il contenuto del file web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salvare il file e chiudere il blocco note.

### <a name="install-additional-modules"></a>Installare moduli aggiuntivi

2. Utilizzare il comando seguente per installare [azure], [nodo uuid], [nconf] e [asincrono] moduli localmente anche salvare una voce per tali file **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    L'output del comando dovrebbe essere simile al seguente:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>Utilizzo del servizio tabelle in un'applicazione di nodo

In questa sezione sarà possibile estendere l'applicazione di base creata con il comando **express** mediante l'aggiunta di un file di **task.js** che contiene il modello per le attività. Verrà inoltre modificare esistente **app.js** e creare un nuovo file **tasklist.js** che viene utilizzato il modello.

### <a name="create-the-model"></a>Creare il modello

1. Creare una nuova directory denominata **modelli**nella directory **WebRole1** .

2. Creare un nuovo file denominato **task.js**nella cartella **modelli** . In questo file conterrà il modello per le attività create dall'applicazione in uso.

3. All'inizio del file **task.js** , aggiungere il codice seguente per fare riferimento alle librerie necessari:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Aggiungere, il codice per definire ed esportare l'oggetto di attività. L'oggetto è responsabile per la connessione alla tabella.

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

5. Successivamente, aggiungere il codice riportato di seguito per definire altri metodi nell'oggetto di attività, che consentono interazioni con i dati archiviati nella tabella:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

### <a name="create-the-controller"></a>Creare il controller

1. Nella directory **WebRole1/indirizza** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il codice seguente **tasklist.js**. Consente di caricare i moduli vengono utilizzati da **tasklist.js**azure e asincrono. Definisce anche la funzione **elenco attività** , che verrà passata un'istanza dell'oggetto **attività** definita in precedenza:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Continuare ad aggiungere il file **tasklist.js** aggiungendo i metodi utilizzati per **showTasks**, **addTask**e **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
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

3. Salvare il file **tasklist.js** .

### <a name="modify-appjs"></a>Modificare app.js

1. Nella directory **WebRole1** , aprire il file **app.js** in un editor di testo. 

2. All'inizio del file, aggiungere le operazioni seguenti per caricare il modulo di azure e impostare la chiave di nome e una partizione di tabella:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. Nel file app.js, scorrere fino a nel punto in cui è visualizzata la riga seguente:

        app.use('/', routes);
        app.use('/users', users);

    Sostituire le righe sopra con il codice riportato di seguito. Verrà inizializzato un'istanza di <strong>attività</strong> con una connessione al proprio account di archiviazione. Viene passato per l' <strong>elenco attività</strong>, che verrà utilizzato per comunicare con il servizio di tabella:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Salvare il file **app.js** .

### <a name="modify-the-index-view"></a>Modificare la visualizzazione di indice analitico

1. Passare alla directory **visualizzazioni** e aprire il file **index.jade** in un editor di testo.

2. Sostituire il contenuto del file **index.jade** con il codice riportato di seguito. Definisce la visualizzazione per la visualizzazione delle attività esistenti, nonché un modulo per l'aggiunta di nuove attività e il contrassegno di quelli esistenti come completato.

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
              if tasks != []
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

Il file **layout.jade** nella directory **visualizzazioni** viene utilizzato come modello globale per gli altri file **.jade** . In questo passaggio verrà modificato in modo da utilizzare [Avvio Twitter](https://github.com/twbs/bootstrap), ovvero un toolkit che semplifica la progettazione di un sito Web dall'aspetto interessante.

1. Scaricare ed estrarre i file per [l'Avvio Twitter](http://getbootstrap.com/). Copiare il file **bootstrap.min.css** dalla **avvio\\dist\\css** cartella in cui il **pubblico\\fogli di stile** directory dell'applicazione elenco attività.

2. Dalla cartella **visualizzazioni** aprire **layout.jade** nell'editor di testo e sostituire il contenuto con le operazioni seguenti:

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

3. Salvare il file **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Eseguire l'applicazione nell'emulatore

Utilizzare il comando seguente per avviare l'applicazione nell'emulatore.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Il browser verrà aperto e viene visualizzata la pagina seguente:

![Un web paginato intitolato elenco attività personale con una tabella contenente le attività e campi per aggiungere una nuova attività.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Utilizzare la forma per aggiungere elementi o rimuovere gli elementi esistenti contrassegnandolo come completata.

## <a name="publishing-the-application-to-azure"></a>Pubblicare l'applicazione su Azure


Nella finestra di Windows PowerShell, chiamare il seguente cmdlet per ridistribuire il servizio ospitato in Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Sostituire **myuniquename** con un nome univoco per questa applicazione. Sostituire **datacentername** con il nome di un centro di dati di Azure, ad esempio **US ovest**.

Una volta completata la distribuzione, verrà visualizzata una risposta simile al seguente:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Come prima, poiché è stato specificato il **-avvio** opzione, il browser si apre e visualizza l'applicazione in esecuzione in Azure una volta completata la pubblicazione.

![Una finestra del browser che visualizza la pagina attività personali. L'URL indica che la pagina a questo punto si trovano in Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Arresto ed eliminazione dell'applicazione

Dopo aver distribuito l'applicazione, può essere necessario disattivarlo in modo da evitare i costi o per creare e distribuire altre applicazioni entro il periodo di valutazione gratuita.

Fatture Azure web istanze del ruolo orarie del tempo di server utilizzato.
Ora del server viene utilizzata una volta distribuita l'applicazione, anche se le istanze non sono in esecuzione e sono in stato di interruzione.

La procedura seguente viene illustrato come interrompere ed eliminare l'applicazione.

1.  Nella finestra di Windows PowerShell, interrompere la distribuzione di servizio creata nella sezione precedente con il seguente cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Interruzione del servizio può richiedere diversi minuti. Quando viene arrestato, si riceve un messaggio che indica che è stata interrotta.

3.  Per eliminare il servizio, chiamare il seguente cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Quando richiesto, immettere **Y** per eliminare il servizio.

    Eliminare il servizio può richiedere diversi minuti. Dopo aver eliminato il servizio viene visualizzato un messaggio che indica che il servizio è stato eliminato.

  [Applicazione Web che Node utilizza Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Archiviazione e accesso ai dati di Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Applicazione Web Node]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
