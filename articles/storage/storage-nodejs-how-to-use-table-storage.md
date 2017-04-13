<properties
    pageTitle="Come utilizzare lo spazio di archiviazione tabella Azure da Node | Microsoft Azure"
    description="Archiviare dati strutturati nel cloud tramite archivio tabelle Azure, un archivio di dati NoSQL."
    services="storage"
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
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Come utilizzare lo spazio di archiviazione tabella Azure da Node

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

In questo argomento viene illustrato come eseguire gli scenari comuni utilizzando il servizio di Azure tabella in un'applicazione di Node.

Esempi di codice in questo argomento presuppongono che si dispone già di un'applicazione di Node. Per informazioni su come creare un'applicazione Node in Azure, vedere uno qualsiasi di questi argomenti:

- [Creare un'app web Node nel servizio di App Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Compilare e distribuire un'app web Node Azure con WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Compilare e distribuire un'applicazione di Node in un servizio Cloud di Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (con Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurare l'applicazione per accedere all'archiviazione Azure

Per utilizzare lo spazio di archiviazione di Azure, è necessario Azure archiviazione SDK per Node, che include una serie di librerie di comodità in grado di comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Consente di installare il pacchetto nodo pacchetto Manager (NPM)

1.  Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione.

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

Aggiungere il codice seguente nella parte superiore del file **server.js** nell'applicazione:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione Azure

Modulo di azure leggerà le variabili di ambiente AZURE\_lo spazio di archiviazione\_ACCOUNT e AZURE\_lo spazio di archiviazione\_ACCESS\_chiave o AZURE\_lo spazio di archiviazione\_connessione\_stringa le informazioni necessarie per connettersi all'account di archiviazione Azure. Se non sono tali variabili, è necessario specificare le informazioni sull'account quando si chiama **TableService**.

Per un esempio di impostazione le variabili di ambiente nel [Portale di Azure](https://portal.azure.com) per un sito Web di Azure, vedere [utilizzo del servizio di tabella Azure Node web app].

## <a name="create-a-table"></a>Creare una tabella

Il codice seguente viene creato un oggetto **TableService** e viene utilizzata per creare una nuova tabella. Aggiungere quanto segue nella parte superiore **server.js**.

    var tableSvc = azure.createTableService();

Se non esiste già, la chiamata a **createTableIfNotExists** creerà una nuova tabella con il nome specificato. Nell'esempio seguente viene creata una nuova tabella denominata "tabella" Se non esiste già:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

Il `result.created` sarà `true` se viene creata una nuova tabella, e `false` se la tabella esiste già. Il `response` conterrà informazioni relative alla richiesta.

### <a name="filters"></a>Filtri

Operazioni di filtro facoltative possono essere applicate alle operazioni eseguite mediante **TableService**. Operazioni di filtro, è possono includere registrazione automaticamente la ripetizione, e così via. Filtri sono oggetti che implementano un metodo con la firma:

    function handle (requestOptions, next)

Una volta eseguita questa relativa pre-elaborazione sulle opzioni di richiesta, il metodo deve chiamare "Avanti" passando callback con la firma seguente:

    function (returnObject, finalCallback, next)

In questo callback e dopo l'elaborazione di returnObject (la risposta della richiesta al server), il callback deve richiamare Avanti esistenza per continuare l'elaborazione di altri filtri o semplicemente richiamare in caso contrario finalCallback per terminare la chiamata al servizio.

Due filtri che implementano ritentare sono inclusi in Azure SDK per Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Di seguito crea un oggetto **TableService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto che consente di definire le proprietà di entità. Tutte le entità devono contenere un **PartitionKey** e **RowKey**, che sono identificatori univoci per l'entità.

* **PartitionKey** - determina la partizione l'entità archiviata in

* **RowKey** - identifica in modo univoco l'entità all'interno della partizione

**PartitionKey** e **RowKey** devono essere valori stringa. Per ulteriori informazioni, vedere [informazioni sul modello di dati del servizio di tabella](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Di seguito è illustrato un esempio di definizione di un'entità. Si noti che **in un'espressione** è definito come un tipo di **EDM**. Che specifica il tipo è facoltativa e tipi di verranno dedotta se non specificati.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] È inoltre disponibile un campo di **Data e ora** per ogni record, impostare da Azure quando un'entità viene inserita o aggiornata.

È anche possibile utilizzare **entityGenerator** per creare entità. Nell'esempio seguente viene creata la stessa entità attività utilizzando **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Per aggiungere un'entità alla tabella, passare l'oggetto entità al metodo **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Se l'operazione ha esito positivo, `result` conterrà [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del record inserito e `response` conterrà informazioni sull'operazione.

Risposta di esempio:

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Per impostazione predefinita, **insertEntity** non restituisce l'entità inserito come parte del `response` le informazioni. Se si prevede di eseguire altre operazioni in questa entità o si desidera le informazioni nella cache, può essere utile affinché venga restituito come parte di `result`. È possibile eseguire questa operazione, consentendo **echoContent** come indicato di seguito:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Aggiornare un'entità

Sono disponibili diversi metodi disponibili per aggiornare un'entità esistente:

* **replaceEntity** - consente di aggiornare un'entità esistente sostituendo

* **mergeEntity** - aggiornato un'entità esistente per l'unione di nuovi valori di proprietà dell'entità esistente

* **insertOrReplaceEntity** - aggiorna un'entità esistente sostituendolo. Se è presente alcuna entità, viene inserita un'altra

* **insertOrMergeEntity** - aggiornato un'entità esistente per l'unione di nuovi valori di proprietà in esistente. Se è presente alcuna entità, viene inserita un'altra

Nell'esempio riportato di seguito viene illustrato l'aggiornamento di un'entità utilizzando **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Per impostazione predefinita, l'aggiornamento di un'entità non consente di verificare se i dati vengono aggiornati in precedenza sono stati modificati da un altro processo. Per supportare gli aggiornamenti simultanei:
>
> 1. È possibile ottenere ETag dell'oggetto da aggiornare. Viene restituito come parte della `response` per qualsiasi entità correlate operazione e possono essere recuperata tramite `response['.metadata'].etag`.
>
> 2. Quando si esegue un'operazione di aggiornamento su un'entità, aggiungere le informazioni di ETag recuperate precedentemente alla nuova entità. Per esempio:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Eseguire l'operazione di aggiornamento. Se l'entità è stato modificato dopo si recupera il valore ETag, ad esempio un'altra istanza dell'applicazione, un `error` verrà restituito che informa che non è stata soddisfatta la condizione di aggiornamento specificata nella richiesta.

Con **replaceEntity** e **mergeEntity**, se l'entità dell'aggiornamento non è presente, quindi l'operazione di aggiornamento non viene eseguito. Pertanto se si desidera archiviare un'entità indipendentemente se esiste già, utilizzare **insertOrReplaceEntity** o **insertOrMergeEntity**.

il `result` per l'aggiornamento ha esito positivo operazioni conterrà **Etag** dell'entità aggiornata.

## <a name="work-with-groups-of-entities"></a>Uso dei gruppi di entità

In alcuni casi è opportuno inviare operazioni più insieme in un batch per assicurarsi che atomica elaborazione dal server. Per eseguire questa operazione, utilizzare la classe **TableBatch** per creare un batch di e quindi utilizzare il metodo **executeBatch** della **TableService** per eseguire le operazioni in batch.

 Nell'esempio seguente viene illustrato l'invio di due entità in un batch di:

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Per le operazioni batch ha esito positivo, `result` conterrà informazioni per ogni operazione nel batch.

### <a name="work-with-batched-operations"></a>Utilizzo delle operazioni in batch

Aggiungere a un batch di operazioni possono essere esaminate visualizzando il `operations` proprietà. È inoltre possibile utilizzare metodi seguenti per lavorare con le operazioni:

* **deselezionare** - Cancella tutte le operazioni da un batch

* **getOperations** - Ottiene un'operazione dal lotto

* **hasOperations** - restituisce VERO se il batch contiene operazioni

* **removeOperations** - rimuove un'operazione

* **dimensioni** - restituisce il numero di operazioni nel batch

## <a name="retrieve-an-entity-by-key"></a>Recuperare un'entità dalla chiave

Per restituire un'entità specifica in base alle **PartitionKey** e **RowKey**, utilizzare il metodo di **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Una volta completata, questa operazione `result` conterrà l'entità.

## <a name="query-a-set-of-entities"></a>Un set di entità della query

Per eseguire una query una tabella, utilizzare l'oggetto **TableQuery** per creare un'espressione di query utilizzando le clausole seguenti:

* **Selezionare** - i campi restituiti dalla query

* **dove** - where clausola

    * **e** - un `and` condizione where

    * **o** - un `or` condizione where

* **superiore** : il numero di elementi per recuperare i


Nell'esempio seguente consente di creare una query che restituirà le primi cinque gli elementi con una PartitionKey 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Poiché non è utilizzata **Selezionare** , verranno restituiti tutti i campi. Per eseguire query su una tabella, usare **queryEntities**. Nell'esempio seguente usa la query restituisca entità 'tabella'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

In caso contrario, `result.entries` conterrà una matrice di entità che corrispondono alla query. Se la query non è riuscita restituire tutte le entità, `result.continuationToken` sarà non*null* e può essere utilizzato come il terzo parametro **queryEntities** per recuperare più risultati. Per la query iniziale, utilizzare *null* per il terzo parametro.

### <a name="query-a-subset-of-entity-properties"></a>Un sottoinsieme di entità proprietà della query

Una query in una tabella è possibile recuperare solo alcuni campi da un'entità.
Ciò riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. Utilizzare la clausola **select** e passare i nomi dei campi da recuperare. Ad esempio, la query seguente restituirà solo i campi **Descrizione** e **in un'espressione.** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Eliminare un'entità

È possibile eliminare un'entità utilizzando i tasti partizione e riga. In questo esempio, l'oggetto **Attività1** contiene i valori **RowKey** e **PartitionKey** dell'entità da eliminare. L'oggetto viene restituito al metodo **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Valutare la possibilità di utilizzo eTag durante l'eliminazione di elementi, per assicurarsi che non è stato modificato l'elemento da un altro processo. Per informazioni sull'uso di eTag, vedere [aggiornare un'entità](#update-an-entity) .

## <a name="delete-a-table"></a>Eliminare una tabella

Il codice seguente elimina una tabella da un account di archiviazione.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Se non si è certi se è presente la tabella, usare **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Utilizzare i token di continuazione

Quando si esegue la query di tabelle per grandi quantità di risultati, cercare i token di continuazione. Potrebbe essere disponibile per la query in modo non se non si creano facilmente riconoscibile quando è presenta un token di continuazione grandi quantità di dati.

Oggetto i risultati restituiti durante l'esecuzione di query set di entità un `continuationToken` proprietà quando è presenta un token di questo. È possibile quindi utilizzare questo quando si esegue una query per continuare a spostarsi tra le entità partizione e una tabella.

Quando la query, è possibile specificare un parametro continuationToken tra l'istanza di oggetto di query e la funzione di callback:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se controlla il `continuationToken` oggetto, si noterà proprietà come `nextPartitionKey`, `nextRowKey` e `targetLocation`, che può essere utilizzato per scorrere i risultati ottenuti.

È inoltre disponibile un esempio di continuazione all'interno di repo Node lo spazio di archiviazione di Azure in GitHub. Cercare `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Lavorare con le firme di accesso condiviso

Accesso condiviso firme (SA) sono un modo protetto per consentire l'accesso granulare alle tabelle senza fornire il nome dell'account di archiviazione o i codici. SA spesso vengono utilizzati per garantire un accesso limitato per i dati, ad esempio che consente di un'app per dispositivi mobili ai record di query.

Un'applicazione attendibile, ad esempio un servizio basato su cloud genera una SA utilizzando **generateSharedAccessSignature** di **TableService**e fornisce a un'applicazione non attendibile o semi-trusted, ad esempio un'app per dispositivi mobili. Le SA viene generata utilizzando un criterio, che descrive le date di inizio e fine in cui le SA sono valida, nonché il livello di accesso concesso al titolare SA.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà all'alloggiamento del SA alla query ("r") nella tabella e scadenza 100 minuti dopo la creazione.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Si noti che le informazioni sull'host deve essere fornite, inoltre, come richiesto quando il titolare SA tenta di accedere alla tabella.

Quindi l'applicazione client utilizza le associazioni di protezione con **TableServiceWithSAS** per eseguire operazioni su tabella. Nell'esempio seguente si connette alla tabella ed esegue una query.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Poiché le SA stata generata con solo l'accesso query, se sono stato apportato un tentativo di inserire, aggiornare o eliminare entità, verrà restituito un errore.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

È anche possibile usare un elenco di controllo di accesso (ACL) per impostare il criterio di accesso per un SA. Ciò è utile se si desidera consentire a più client per la tabella di access, ma fornisce criteri di accesso diversi per ogni cliente.

Un ACL è implementato utilizzando una matrice di criteri di accesso con un ID associato a ogni criterio. Nell'esempio seguente viene definito due criteri, uno per utente '1' e uno per 'Utente2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

Nell'esempio seguente ottiene ACL corrente per la tabella **hometasks** e quindi aggiunge nuovi criteri utilizzando **setTableAcl**. In questo modo:

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Dopo avere configurato l'ACL, è possibile creare un SA in base all'ID per un criterio. Nell'esempio seguente viene creato un nuove SA per 'Utente2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti.

-   [Blog del Team di archiviazione azure][].
-   Archivio di [Azure SDK lo spazio di archiviazione per nodo][] in GitHub.
-   [Centro per sviluppatori Node](/develop/nodejs/)

  [Spazio di archiviazione di Azure SDK per nodo]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [App web Node mediante il servizio di tabella Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
