##<a name="create-client"></a>Creare una connessione Client

Creare una connessione client mediante la creazione di un `WindowsAzure.MobileServiceClient` oggetto.  Sostituire `appUrl` con l'URL per l'App per dispositivi mobili.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Usare le tabelle

Accesso o aggiornare i dati, creare un riferimento alla tabella back-end. Sostituire `tableName` con il nome della tabella

```
var table = client.getTable(tableName);
```

Dopo avere inserito un riferimento di tabella, è possibile lavorare ulteriormente con la tabella:

* [Query di una tabella](#querying)
  * [Filtro dei dati](#table-filter)
  * [Spostamento dati](#table-paging)
  * [Ordinamento dei dati](#sorting-data)
* [Inserimento di dati](#inserting)
* [La modifica dei dati](#modifying)
* [Eliminazione dei dati](#deleting)

###<a name="querying"></a>Procedura: riferimento a una tabella di Query

Dopo avere inserito un riferimento di tabella, è possibile utilizzare per eseguire una query per i dati nel server.  Le query vengono eseguite in una lingua "LINQ-tipo mi piace".
Per restituire tutti i dati della tabella, usare le operazioni seguenti:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La funzione di successo viene chiamata con i risultati.   Non utilizzare `for (var i in results)` il successo funzionare come che iterazione informazioni incluso nei risultati quando altre funzioni di query (ad esempio `.includeTotalCount()`) vengono utilizzati.

Per ulteriori informazioni sulla sintassi per Query, fare riferimento alla [Query oggetto documentazione del].

####<a name="table-filter"></a>Filtrare i dati nel server

È possibile utilizzare una `where` clausola nel riferimento di tabella:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

È anche possibile utilizzare una funzione che filtra l'oggetto.  In questo caso il `this` variabile viene assegnata all'oggetto corrente da filtrare.  Di seguito è equivalente all'esempio precedente:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Spostamento dati

Utilizzare i metodi take() e skip().  Ad esempio, se si desidera dividere la tabella in record 100 riga:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Il `.includeTotalCount()` consente di aggiungere un campo totalCount all'oggetto risultati.  Il campo totalCount viene inserito il numero totale di record che verrà restituito se non viene utilizzata suddivisione in pagine.

È quindi possibile utilizzare la variabile di pagine e alcuni pulsanti dell'interfaccia utente per fornire un elenco di pagina; Utilizzare loadPage() per caricare i nuovi record per ogni pagina.  È necessario implementare sorta di memorizzazione nella cache di accesso rapido ai record che sono già stati caricati.


####<a name="sorting-data"></a>Procedura: restituire dati ordinati

Utilizzare i metodi di query .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Per ulteriori informazioni sull'oggetto di Query, fare riferimento alla [documentazione di oggetto Query].

###<a name="inserting"></a>Procedura: inserire i dati

Creare un oggetto JavaScript con la data appropriata e chiamare table.insert() in modo asincrono:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

All'inserimento ha esito positivo, viene restituito l'elemento inserito con i campi aggiuntivi necessari per le operazioni di sincronizzazione.  È necessario aggiornare il proprio cache con queste informazioni per gli aggiornamenti successivi.

Si noti che Azure Mobile App Node Server SDK supporta schema dinamico in fase di sviluppo.
In caso di schema dinamico, lo schema della tabella viene aggiornato in tempo reale, che consente di aggiungere colonne alla tabella appena specificando loro in un'istruzione insert o operazione di aggiornamento.  È consigliabile disattivare dinamiche dello schema prima di passare all'applicazione di produzione.

###<a name="modifying"></a>Procedura: modifica dei dati

Simile al metodo .insert(), è necessario creare un oggetto di aggiornamento, quindi chiamare .update().  Aggiorna oggetto dovrà contenere l'ID del record da aggiornare - oggi utilizzato durante la lettura del record o quando si chiama .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Procedura: eliminare i dati

Il metodo .del() per eliminare un record.  Passare l'ID di riferimento a un oggetto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
