<properties
    pageTitle="Come utilizzare lo spazio di archiviazione coda da Node | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di coda di Azure per creare ed eliminare code e inserire, ottenere ed eliminare i messaggi. Esempi scritti in Node."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Come utilizzare lo spazio di archiviazione coda da Node

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando il servizio di Microsoft Azure coda. Gli esempi sono scritti con l'API Node. Gli scenari coperti includono **l'inserimento**, **la visualizzazione**, **ottenere**e **l'eliminazione** coda messaggi, nonché **Creazione ed eliminazione di code**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node

Creare un'applicazione Node vuota. Per istruzioni sulla creazione di un'applicazione Node, vedere [creare un'app web Node nel servizio App Azure], [compilare e distribuire un'applicazione di Node a un servizio Cloud di Azure] usando Windows PowerShell o [compilare e distribuire un'app web Node in Azure tramite Web matrice].

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per lo spazio di archiviazione di Access

Per utilizzare lo spazio di archiviazione Azure, è necessario Azure archiviazione SDK per Node, che include un set di raccolte semplicità di utilizzo che comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Consente di ottenere il pacchetto di nodo pacchetto Manager (NPM)

1.  Utilizzare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix), passare alla cartella in cui è stato creato l'applicazione di esempio.

2.  Nella finestra di comando, digitare **npm installare archiviazione azure** . Output del comando è simile al seguente.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  È possibile eseguire manualmente il comando **ls** per verificare che un **nodo\_moduli** cartella è stata creata. All'interno di tale cartella si trova il pacchetto di **archiviazione di azure** , che contiene le librerie che è necessario accedere all'archiviazione.

### <a name="import-the-package"></a>Importare il pacchetto

Usa il blocco note o un altro editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione in cui si prevede di utilizzare lo spazio di archiviazione:

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Configura una connessione di archiviazione Azure

Modulo di azure leggerà le variabili di ambiente AZURE\_lo spazio di archiviazione\_ACCOUNT e AZURE\_lo spazio di archiviazione\_accesso\_chiave o AZURE\_lo spazio di archiviazione\_connessione\_stringa le informazioni necessarie per connettersi all'account di archiviazione Azure. Se non sono tali variabili, è necessario specificare le informazioni sull'account quando si chiama **createQueueService**.

Per un esempio di impostazione le variabili di ambiente nel [Portale di Azure](https://portal.azure.com) per un sito Web di Azure, vedere [utilizzo del servizio di tabella Azure Node web app].

## <a name="how-to-create-a-queue"></a>Procedura: Creare una coda

Il codice seguente viene creato un oggetto **QueueService** , che consente di utilizzare code.

    var queueSvc = azure.createQueueService();

Utilizzare il metodo **createQueueIfNotExists** che restituisce coda specificata, se già esistente o crea una nuova coda con il nome specificato, se non esiste già.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Se coda è stata creata, `result.created` è vera. Se coda è presente, `result.created` è false.

### <a name="filters"></a>Filtri

Operazioni di filtro facoltative possono essere applicate alle operazioni eseguite mediante **QueueService**. Operazioni di filtro, è possono includere registrazione automaticamente la ripetizione, e così via. Filtri sono oggetti che implementano un metodo con la firma:

    function handle (requestOptions, next)

Una volta eseguita questa relativa pre-elaborazione sulle opzioni di richiesta, il metodo deve chiamare "Avanti" passando un callback con la firma seguente:

    function (returnObject, finalCallback, next)

In questo callback e dopo l'elaborazione returnObject (la risposta della richiesta al server), il callback richiede di richiamare Avanti esistenza per continuare l'elaborazione di altri filtri o semplicemente richiamare in caso contrario finalCallback per terminare la chiamata del servizio.

Due filtri che implementano ritentare sono inclusi in Azure SDK per Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Di seguito crea un oggetto **QueueService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: Inserire una coda di un messaggio

Per inserire un messaggio in una coda, utilizzare il metodo **createMessage** per creare un nuovo messaggio e aggiungerlo alla coda.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Procedura: Leggere il messaggio successivo

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda utilizzando il metodo **peekMessages** . Per impostazione predefinita, **peekMessages** legge un singolo messaggio.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

Il `result` contiene il messaggio.

> [AZURE.NOTE] Usa **peekMessages** quando sono presenti che nessun messaggio nella coda verrà restituito un errore, tuttavia non ricevono i messaggi.

## <a name="how-to-dequeue-the-next-message"></a>Procedura: Rimuovere il messaggio successivo

Elaborazione di un messaggio è un processo in due fasi:

1. Rimuovere il messaggio.

2. Eliminare il messaggio.

Per annullare l'accodamento di un messaggio, utilizzare **getMessages**. In questo modo, i messaggi invisibili nella coda, in modo che nessun altro client può elaborare loro. Dopo l'applicazione ha elaborato un messaggio, chiamare **deleteMessage** per eliminare dalla coda. Nell'esempio seguente riceve un messaggio, quindi eliminarlo:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] Per impostazione predefinita, un messaggio è nascosto solo per 30 secondi, trascorso il quale è visibile agli altri client. È possibile specificare un valore diverso tramite `options.visibilityTimeout` con **getMessages**.

> [AZURE.NOTE]
> Usa **getMessages** quando sono presenti che nessun messaggio nella coda verrà restituito un errore, tuttavia non ricevono i messaggi.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: Modificare il contenuto di un messaggio in coda

È possibile modificare il contenuto di un messaggio sul posto nella coda utilizzando **updateMessage**. Nell'esempio seguente aggiorna il testo di un messaggio:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedura: Opzioni aggiuntive per l'annullamento dell'Accodamento messaggi

Esistono due modi per personalizzare il recupero dei messaggi da una coda:

* `options.numOfMessages`-Recuperare un batch di messaggi (fino a 32).
* `options.visibilityTimeout`: Consente di impostare un timeout o allungare invisibility.

Nell'esempio seguente viene utilizzato il metodo **getMessages** per ottenere 15 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo for. Imposta inoltre il timeout invisibility a cinque minuti per tutti i messaggi restituiti da questo metodo.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Procedura: Ottenere la lunghezza della coda

**GetQueueMetadata** restituisce i metadati relativi coda, tra cui il numero approssimativo dei messaggi nella coda di attesa.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Come: Accoda elenco

Per recuperare un elenco di code, utilizzare **listQueuesSegmented**. Per recuperare un elenco filtrato in base a un prefisso specifico, utilizzare **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Se non è possibile restituire tutte le code, `result.continuationToken` può essere utilizzato come primo parametro del **listQueuesSegmented** o il secondo parametro **listQueuesSegmentedWithPrefix** per recuperare altri risultati.

## <a name="how-to-delete-a-queue"></a>Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **deleteQueue** sull'oggetto coda.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

Per cancellare tutti i messaggi da una coda senza eliminarlo, utilizzare **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Procedura: lavorare con le firme di accesso condiviso

Condiviso accesso firme (SA) sono un modo protetto per consentire l'accesso granulare alle code senza fornire il nome dell'account di archiviazione o i codici. SA spesso vengono utilizzati per garantire un accesso limitato alle code, ad esempio consentendo un'app per dispositivi mobili inoltrare i messaggi.

Un'applicazione attendibile, ad esempio un servizio basato su cloud genera una SA utilizzando **generateSharedAccessSignature** di **QueueService**e fornisce a un'applicazione non attendibile o semi-trusted. Ad esempio un'app per dispositivi mobili. Le SA viene generata utilizzando un criterio, che descrive le date di inizio e fine in cui le SA sono valida, nonché il livello di accesso concesso al titolare SA.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà all'alloggiamento del SA aggiungere i messaggi nella coda e scadenza 100 minuti dopo la creazione.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Si noti che le informazioni sull'host deve essere fornite, inoltre, come richiesto quando il titolare SA tenta di accedere alla coda.

Per eseguire operazioni su coda, quindi l'applicazione client utilizza le associazioni di protezione con **QueueServiceWithSAS** . Nell'esempio seguente si connette alla coda e crea un messaggio.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Poiché le SA stata generata con Aggiungi l'accesso, se sono stato apportato un tentativo di leggere, aggiornare o eliminare i messaggi, verrà restituito un errore.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

È anche possibile usare un elenco di controllo di accesso (ACL) per impostare il criterio di accesso per un SA. Ciò è utile se si desidera consentire a più client accedere alla coda, ma fornisce criteri di accesso diversi per ogni cliente.

Un ACL è implementato utilizzando una matrice di criteri di accesso con un ID associato a ogni criterio. Nell'esempio seguente viene definito due criteri; uno per 'utente 1' e uno per 'Utente2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

Nell'esempio seguente ottiene gli ACL per **myqueue**, quindi aggiunge nuovi criteri utilizzando **setQueueAcl**. In questo modo:

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Dopo avere configurato l'ACL, è possibile creare un SA in base all'ID per un criterio. Nell'esempio seguente viene creato un nuove SA per 'Utente2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione coda, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse.

-   Visitare il [Blog del Team di archiviazione Azure][].
-   Visitare l'archivio di [Azure SDK lo spazio di archiviazione per nodo][] su GitHub.

  [Spazio di archiviazione di Azure SDK per nodo]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Creare un'app web Node nel servizio di App Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [App web Node mediante il servizio di tabella Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Compilare e distribuire un'applicazione di Node a un servizio Cloud di Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Compilare e distribuire un'app web Node Azure tramite Web matrice]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
