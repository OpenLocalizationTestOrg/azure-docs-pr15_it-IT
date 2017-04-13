<properties 
    pageTitle="Programmazione DocumentDB: Stored procedure, trigger di database e funzioni definite dall'utente | Microsoft Azure" 
    description="Informazioni su come utilizzare DocumentDB per scrivere stored procedure, trigger di database e funzioni definite dall'utente (funzioni definite dall'utente) in JavaScript. È possibile ottenere suggerimenti di programmazione di database e altro ancora." 
    keywords="Trigger di database, stored procedure, stored procedure, programma per database, stored procedure, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programmazione sul lato server DocumentDB: Stored procedure, trigger di database e funzioni definite dall'utente

Informazioni su come lingua di Azure DocumentDB integrata, transazione esecuzione di JavaScript consente agli sviluppatori di scrivere **stored procedure**, **trigger** e **funzioni definite dall'utente** a livello nativo in JavaScript. In questo modo è possibile scrivere la logica dell'applicazione di database applicazione che può essere inviata ed eseguita direttamente sulle partizioni dello spazio di archiviazione di database 

Si consiglia di iniziare, guardare il video seguente, in cui Andrew Liu fornisce una breve introduzione al modello di programmazione di DocumentDB database sul lato server. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Quindi, tornare a questo articolo, in cui si apprenderanno le risposte alle domande seguenti:  

- Come scrivere un una stored procedure, trigger o utente utilizzando JavaScript?
- Come DocumentDB garantire acido?
- Funzionano di transazioni in DocumentDB
- Quali sono già attiva e post-attiva e come si scrive?
- Come eseguire la registrazione ed eseguire una stored procedure, trigger o utente in modo REST mediante il protocollo HTTP
- Cosa DocumentDB SDK sono disponibili per creare ed eseguire stored procedure, trigger e funzioni definite dall'utente?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduzione alle Stored Procedure e programmazione utente

Questo approccio di *"JavaScript come giorno moderno Transact-SQL"* libera gli sviluppatori di applicazioni dalla complessità della mancata di sistema e le tecnologie di mapping relazionale a oggetti. Offre molti vantaggi intrinseci che può essere utilizzata per la compilazione di applicazioni:  

-   **Logica:** JavaScript come un livello elevato linguaggio di programmazione offre un'interfaccia familiare e RTF per esprimere logica aziendale. È possibile eseguire complesse sequenze di operazioni ai dati.

-   **Transazioni atomiche:** DocumentDB garantisce che database operazioni eseguite all'interno di una singola stored procedure o trigger atomica. In questo modo un'applicazione di combinare operazioni correlate in un unico batch in modo che tutti gli elementi riuscire o nessuna di esse esito positivo. 

-   **Prestazioni:** Il fatto che JSON per sé siano associati al sistema di tipi di linguaggio Javascript ed è l'unità di base di spazio di archiviazione in DocumentDB consente di un numero di ottimizzazioni come Prato verificarsi del documenti JSON in pool di buffer e renderli disponibili su richiesta al codice in esecuzione. Esistono altre prestazioni associati spedizione logica al database:
    -   Batch: sviluppatori di raggruppare le operazioni come inserimenti e inviarli in blocco. La latenza il traffico di rete di costo e il sovraccarico di archiviazione per creare transazioni separate sono ridotte in modo significativo. 
    -   Pre-compilazione – DocumentDB precompilato stored procedure, trigger e funzioni definite dall'utente (funzioni definite dall'utente) per evitare i costi di compilazione JavaScript per ciascuna chiamata. Il sovraccarico di compilazione del codice byte per la logica procedurale viene ammortizzato su un valore minimo.
    -   Sequenza-necessità di operazioni molti un effetto ("trigger") che potenzialmente a altro comporta l'esecuzione di uno o più operazioni archivio secondario. Oltre a atomicità, si tratta offre prestazioni migliori quando spostato nel server. 
-   **Incapsulamento:** Stored procedure possono essere utilizzate per raggruppare le regole di business in un'unica posizione. Si presenta due vantaggi:
    -   Aggiunge un livello di astrazione sopra i dati non elaborati, che consente a progettisti di dati ad evolversi delle applicazioni in modo indipendente dai dati. Questa operazione è particolarmente utile quando i dati sono minore di schema, a causa di ipotesi fragile che devono essere baked nell'applicazione se hanno gestire i dati direttamente.  
    -   Questa astrazione consente alle aziende proteggere i dati semplificando l'accesso dagli script.  

La creazione e l'esecuzione di trigger di database, stored procedure e operatori di query personalizzati è supportata tramite [l'API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)e [client SDK](documentdb-sdk-dotnet.md) in molte piattaforme tra .NET, Node e JavaScript.

**In questa esercitazione utilizza [SDK Node con domande](http://azure.github.io/azure-documentdb-node-q/) ** per illustrare la sintassi e l'utilizzo della stored procedure, trigger e funzioni definite dall'utente.   

## <a name="stored-procedures"></a>Stored procedure

### <a name="example-write-a-simple-stored-procedure"></a>Esempio: Scrivere una stored procedure semplice 
Iniziamo con una semplice stored procedure che restituisce una risposta "Hello World".

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Stored procedure vengono registrate in insieme e può essere utilizzata per i documenti e allegato presenta in tale raccolta. Il frammento di codice seguente viene illustrato come registrare stored procedure helloWorld con una raccolta. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Una volta registrata stored procedure, abbiamo futuro sarà possibile eseguire sull'insieme e leggere i risultati al client. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


L'oggetto di contesto consente l'accesso a tutte le operazioni che può essere eseguita su DocumentDB lo spazio di archiviazione, oltre che l'accesso agli oggetti richiesta e risposta. In questo caso, l'oggetto di risposta è stato usato per impostare il corpo della risposta viene inviato al client. Per ulteriori informazioni, fare riferimento al [server DocumentDB JavaScript documentazione SDK](http://azure.github.io/azure-documentdb-js-server/).  

Possiamo espandere in questo esempio e aggiungere che altre funzionalità correlate alla stored procedure di database. Stored procedure possono creare, aggiornare, leggere, eseguire una query ed eliminare documenti e gli allegati all'interno della raccolta.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Esempio: Scrivere una stored procedure per creare un documento 
Il frammento di codice successivo viene illustrato come utilizzare l'oggetto di contesto per interagire con le risorse DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Questa stored procedure accetta come input documentToCreate, il corpo del documento per creare la raccolta corrente. Tutte queste operazioni sono asincrone e dipendono richiamate funzione JavaScript. La funzione di callback con due parametri, uno per l'oggetto di errore in caso che di esito negativo e uno per l'oggetto creato. All'interno di callback, gli utenti possono gestire l'eccezione o genera un errore. Nel caso in cui non viene fornito un callback e si verifica un errore, il runtime DocumentDB genera un errore.   

Nell'esempio precedente, il callback genera un errore se l'operazione non è riuscita. In caso contrario, viene impostata l'id del documento come corpo della risposta al client. Ecco la modalità di esecuzione di questa stored procedure con parametri di input.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Si noti che è possibile modificare questa stored procedure per acquisire una matrice di enti documento come input e crearli tutti nello stesso esecuzione di stored procedure anziché più richieste di rete per creare ciascuno di essi singolarmente. Può essere utilizzato per implementare un'unità di importazione in blocco efficace per DocumentDB (descritto più avanti in questa esercitazione).   

Nell'esempio descritto è stato illustrato come utilizzare stored procedure. Verranno illustrate le caratteristiche trigger e funzioni definite dall'utente (funzioni definite dall'utente) in un secondo momento nell'esercitazione.

## <a name="database-program-transactions"></a>Transazioni del database
Transazioni in un database tipico possono essere definita come una sequenza di operazioni eseguite in una singola unità logica di lavoro. Ogni transazione garantisce **l'acido**. ACIDO è un noto l'acronimo di quattro proprietà -, la coerenza, isolamento e atomicità.  

In breve, atomicità garantisce che tutto il lavoro all'interno di una transazione viene considerato come un'unica in uno dei due tutti che ne viene eseguito il commit o nessuno. La coerenza garantisce che i dati siano sempre in buono stato interno tra le transazioni. Isolamento in modo che nessun due transazioni interferiscano con i dipendenti, in genere, nella maggior parte dei sistemi specificare più livelli di isolamento che possono essere usati in base alle esigenze dell'applicazione. Durata garantisce che qualsiasi modifica che viene eseguito il commit nel database viene mantenuto presenta.   

In DocumentDB, JavaScript è ospitato nello stesso spazio della memoria del database. Di conseguenza, le richieste di effettuati all'interno di stored procedure e trigger vengono eseguiti nello stesso ambito di una sessione di database. In questo modo DocumentDB garantire acido per tutte le operazioni che fanno parte di un singolo stored procedure o trigger. Valutare la possibilità di definizione della stored procedure seguenti:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Questa stored procedure utilizza le transazioni all'interno di un'app di gioco agli elementi trade tra due lettori in un'unica operazione. Stored procedure tenta di leggere due documenti ogni corrispondenti agli ID lettore passato come argomento. Se si trovano entrambi i documenti di Windows Media player, stored procedure aggiorna i documenti scambiando gli elementi. Se si verificano errori lungo il percorso, verrà generata un'eccezione JavaScript che in modo implicito interrompe la transazione.

Se la raccolta stored procedure è registrata su è una raccolta singola partizione, quindi transazione ambito è impostata su tutti i docuemnts all'interno della raccolta. Se la raccolta è suddiviso, stored procedure vengono eseguite nell'ambito della chiave di partizione singola transazione. Ogni stored esecuzione della procedura quindi deve includere un valore di chiave partizione corrispondente all'ambito della transazione deve essere eseguito in. Per ulteriori informazioni, vedere [DocumentDB partizioni](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Commit e ripristino
Le transazioni vengono in profondità e a livello nativo integrate nel modello di programmazione JavaScript del DocumentDB. All'interno di una funzione JavaScript, tutte le operazioni vengono automaticamente inclusi in una sola transazione. Se il codice JavaScript viene completata senza le eventuali eccezioni, le operazioni al database sono approvate. In effetti, le istruzioni "Iniziare transazione", "eseguire il COMMIT" nei database relazionali sono implicite in DocumentDB.  
 
Se c'è un'eccezione che viene propagata dello script, runtime JavaScript del DocumentDB riporterà l'intera transazione. Come mostrato nell'esempio precedente, generare un'eccezione è in effetti equivalente a un'operazione"Ripristina" in DocumentDB.
 
### <a name="data-consistency"></a>Coerenza dei dati
Stored procedure e trigger vengono sempre eseguite sulla replica principale della raccolta DocumentDB. In questo modo che lettura all'interno stored procedure offerta sicuro coerenza. Query con funzioni definite dall'utente possono essere eseguite in primario o qualsiasi replica secondario, ma per garantire il per soddisfare il livello di coerenza richiesto scegliendo replica appropriata.

## <a name="bounded-execution"></a>Esecuzione limitata
Tutte le operazioni di DocumentDB deve essere completata entro il server specificato richiedere tempo. Il vincolo riguarda anche le funzioni JavaScript (stored procedure, trigger e funzioni definite dall'utente). Se un'operazione non viene completata con tale termine, la transazione verrà riportata. Funzioni JavaScript devono terminare entro il limite di tempo o implementare un modello di continuazione in base a per batch/curriculum esecuzione.  

Per semplificare lo sviluppo di stored procedure e trigger per la gestione dei limiti di tempo, tutte le funzioni nell'oggetto insieme (per creare, leggere, Sostituisci ed eliminazione di documenti e gli allegati) restituiranno un valore Boolean che indica se tale operazione verrà completata. Se il valore è FALSO, è l'indicazione che il limite di tempo sta per scadere e che la procedura è necessario disporre di esecuzione.  Operazioni prima coda per la prima operazione di archiviazione non accettate vengono necessariamente completare se stored procedure viene completata nel tempo e non coda ulteriori richieste.  

Funzioni JavaScript sono inoltre limitate sul consumo delle risorse. DocumentDB riserva produttività per insieme in base alla dimensione provisioning di un account di database. Produttività è espresso in termini di un'unità normalizzata di CPU e memoria consumo IO denominato unità richiesta o destinatari. Funzioni JavaScript possono comportare l'utilizzo di un numero elevato di destinatari in breve e che venga visualizzato tasso limitata se è stato raggiunto il limite dell'insieme. Potrebbero inoltre messo in quarantena di stored procedure in modo intensivo delle risorse per assicurare la disponibilità di operazioni di base del database.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Esempio: Blocco importazione di dati in un programma per database
Di seguito è illustrato un esempio di una stored procedure scritta per l'importazione in blocco-documenti in una raccolta. Si noti come stored procedure gestisce esecuzione limitata selezionando il valore booleano restituito da createDocument, quindi viene utilizzato il conteggio dei documenti inseriti in ogni chiamata della stored procedure per tenere traccia di presentazione e curriculum vitae lo stato di avanzamento in batch.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Trigger di database
### <a name="database-pre-triggers"></a>Pre-trigger di database
DocumentDB fornisce i trigger per essere eseguiti o attivati tramite un'operazione su un documento. Ad esempio, è possibile specificare un pre-trigger quando si crea un documento, questo pre-trigger verrà eseguite prima che il documento viene creato. Di seguito è riportato un esempio di come è possibile utilizzare i pre-trigger per convalidare le proprietà di un documento che si sta creando:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


E il codice di registrazione lato client Node corrispondente per il trigger:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Pre-trigger non può contenere qualsiasi parametro di input. L'oggetto richiesta può essere utilizzato per modificare il messaggio di richiesta associato all'operazione. In questo caso, viene eseguito il pre-trigger con la creazione di un documento e il corpo del messaggio di richiesta contiene il documento da creare nel formato JSON.   

Quando vengono registrati trigger, gli utenti possono specificare le operazioni che è possibile eseguire con. Questo trigger è stato creato con TriggerOperation.Create, vale a dire che il seguente non è consentita.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Post-trigger di database
Post-trigger, ad esempio pre-trigger, sono associati a un'operazione su un documento e non eseguire qualsiasi parametro di input. Vengono eseguite **dopo** che l'operazione completata e si dispone dell'accesso al messaggio di risposta viene inviato al client.   

Nell'esempio seguente mostra i post-trigger di in azione:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Come illustrato nell'esempio seguente, è possibile registrare il trigger.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Questo trigger una query per il documento di metadati e aggiorna con i dettagli del nuovo documento.  

Un aspetto importante tenere presente è l'esecuzione di trigger in DocumentDB **transazione** . Questo post-trigger viene eseguito come parte della stessa transazione la creazione del documento originale. Pertanto, se è un'eccezione da post-trigger (ad esempio se non è possibile aggiornare il documento di metadati), l'intera transazione avrà esito negativo ed è possibile annullare. Non verrà creato alcun documento e verrà restituita un'eccezione.  

##<a id="udf"></a>Funzioni definite dall'utente
Funzioni definite dall'utente (funzioni definite dall'utente) vengono utilizzate per estendere la grammatica del linguaggio di query SQL DocumentDB e implementare regole business personalizzate. Può essere chiamati solo all'interno di query. Utenti che non si ha accesso all'oggetto contesto e deve essere utilizzata come JavaScript solo calcolo. Di conseguenza, è possibile eseguire funzioni definite dall'utente in secondarie repliche del servizio DocumentDB.  
 
Nell'esempio seguente viene crea un'utente per calcolare il numero reddito in base alle tariffe per diverse tra parentesi quadre entrate e viene utilizzata all'interno di una query per trovare tutte le persone che pagato più di $20.000 imposte.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


L'utente può essere utilizzato nelle query come nell'esempio seguente:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>Query integrate linguaggio JavaScript API
Oltre a eseguire una query utilizzando la sintassi SQL di DocumentDB, SDK sul lato server consente di eseguire query ottimizzata mediante un'interfaccia Office fluent di JavaScript senza alcuna conoscenza di SQL. La query JavaScript che API consente di creare a livello di programmazione query passando funzioni predicati nella funzione chainable chiama, con una sintassi familiare del ECMAScript5 da una coppia in forma di matrice e librerie JavaScript popolari come lodash. Le query vengono analizzate da runtime JavaScript da eseguire in modo efficiente con gli indici di DocumentDB.

> [AZURE.NOTE]`__` (carattere di sottolineatura doppia) è un alias per `getContext().getCollection()`.
> <br/>
> In altre parole, è possibile utilizzare `__` o `getContext().getCollection()` per accedere la query JavaScript API.

Funzioni supportate includono:
<ul>
<li>
<b>Chain(). valore ([callback] [, opzioni])</b>
<ul>
<li>
Avvia una chiamata concatenata che deve terminare con value ().
</li>
</ul>
</li>
<li>
<b>filtro (predicateFunction [, opzioni] [, callback])</b>
<ul>
<li>
I filtri di input utilizzando una funzione predicativa che restituisce VERO/FALSO per filtrare i documenti di input all'interno/esterno nel set di risultante. Questo comportamento simili a una clausola WHERE in SQL.
</li>
</ul>
</li>
<li>
<b>mappa (transformationFunction [, opzioni] [, callback])</b>
<ul>
<li>
Consente di applicare una proiezione assegnata una funzione di trasformazione viene eseguito il mapping di ogni elemento di input a un oggetto JavaScript o valore. Questo comportamento simili a una clausola SELECT in SQL.
</li>
</ul>
</li>
<li>
<b>Pluck ([NomeProprietà] [, opzioni] [, callback])</b>
<ul>
<li>
Si tratta di una scelta rapida per una mappa che estrae il valore di una singola proprietà da ogni elemento di input.
</li>
</ul>
</li>
<li>
<b>Converti ([isShallow] [, opzioni] [, callback])</b>
<ul>
<li>
Combina e semplifica matrici da ogni elemento input in a un'unica matrice. Questo comportamento simili a SelectMany in LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicato] [, opzioni] [, callback])</b>
<ul>
<li>
Generare un nuovo set di documenti per ordinare i documenti all'interno del flusso di documento di input in modo crescente utilizzando il predicato specificato. Questo comportamento simili a una clausola ORDER BY in SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicato] [, opzioni] [, callback])</b>
<ul>
<li>
Generare un nuovo set di documenti per ordinare i documenti all'interno del flusso di input documento decrescente utilizzando il predicato specificato. Questo comportamento simili a una clausola ORDER BY x DESC in SQL.
</li>
</ul>
</li>
</ul>


Quando è inclusa all'interno delle funzioni predicato e/o selettore, seguenti costrutti di JavaScript visualizzato automaticamente ottimizzati per l'esecuzione direttamente su indici DocumentDB:

* Operatori semplici: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Valori letterali, tra cui l'oggetto letterale: {}
* var restituito

I seguenti costrutti di JavaScript non ottenere ottimizzati per indici DocumentDB:

* Flusso di controllo (ad esempio, se, mentre)
* Chiamate di funzione

Per ulteriori informazioni, vedere il nostro [JSDocs sul lato Server](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Esempio: Scrivere una stored procedure utilizzando la query JavaScript API

Nell'esempio seguente è illustrato un esempio della modalità di utilizzo dell'API di Query JavaScript nel contesto di una stored procedure. Stored procedure consente di inserire un documento, fornito da un parametro di input e aggiorna dei metadati del documento, utilizzando il `__.filter()` metodo minSize, maxSize e totalSize in base alla proprietà dimensioni del documento di input.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL Javascript irregolarità foglio
Nella tabella seguente vengono illustrati varie query SQL e le query JavaScript corrispondente.

Come con le query SQL, chiavi di proprietà del documento (ad esempio `doc.id`) tra maiuscole e minuscole.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>Nella Query API JavaScript</th>
<th>Dettagli</th>
</tr>
<tr>
<td>
<pre>
Selezionare * da documenti
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {restituito doc;});
</pre>
</td>
<td>I risultati in tutti i documenti (impaginati con token di continuazione) come è.</td>
</tr>
<tr>
<td>
<pre>
Selezionare docs.id, docs.message come msg, docs.actions da documenti
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {restituito {id: doc.id, msg: doc.message, azioni: doc.actions};});
</pre>
</td>
<td>Progetti id, messaggio (alias a msg) e azione da tutti i documenti.</td>
</tr>
<tr>
<td>
<pre>
Selezionare * da documenti dove docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {restituito doc.id === "X998_Y998;"});
</pre>
</td>
<td>Query per i documenti con il predicato: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
Selezionare * da documenti in cui ARRAY_CONTAINS(docs. Contrassegni, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {restituito x.Tags & & x.Tags.indexOf(123) > -1;});
</pre>
</td>
<td>Query per i documenti che hanno una proprietà tag e tag è una matrice contenente il valore 123.</td>
</tr>
<tr>
<td>
<pre>
Selezionare docs.id, docs.message come documenti da msg dove docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {restituito doc.id === "X998_Y998;"}) .map(function(doc) {restituito {id: doc.id, msg: doc.message};}) .value();
</pre>
</td>
<td>Query per i documenti con un predicato id = "X998_Y998" e quindi progetti id e il messaggio (alias a msg).</td>
</tr>
<tr>
<td>
<pre>
Documenti da di tag selezionare valore JOIN tag nei documenti. Tag ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {documento restituito. Tag & & Array.isArray (doc. Tag). .sortBy(function(doc)}) {restituito doc._ts;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtri per i documenti che hanno una proprietà di matrice, contrassegni e Ordina documenti di stampa per la proprietà del sistema timestamp _ts i progetti + semplifica la matrice tag.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Supporto di runtime
[Sul lato server DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) fornisce supporto per la maggior parte delle funzionalità del linguaggio JavaScript mainstream come standardizzato da [ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Sicurezza
JavaScript stored procedure e trigger sono in modalità sandbox in modo che gli effetti di uno script non divulgare a altra senza scorrere isolamento transazione dello snapshot al livello del database. Gli ambienti di runtime sono pool ma puliti del contesto dopo ogni esecuzione. Pertanto sono vengono garantiti come sicuri di eventuali conseguenze impreviste tra loro.

### <a name="pre-compilation"></a>Pre-compilazione
Stored procedure, trigger e funzioni definite dall'utente sono in modo implicito precompilato nel formato di codice byte per evitare i costi di compilazione in fase di ciascuna chiamata di script. In questo modo le chiamate di stored procedure sono veloci e hanno un impatto basso.

## <a name="client-sdk-support"></a>Supporto per i client SDK
Oltre ai client [Node](documentdb-sdk-node.md) DocumentDB supporta [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)e [Python SDK](documentdb-sdk-python.md). Stored procedure, trigger e funzioni definite dall'utente possono essere create ed eseguita utilizzando uno di questi SDK anche. Nell'esempio seguente viene illustrato come creare ed eseguire una stored procedure utilizzando il client .NET. Si noti come i tipi di .NET sono passati stored procedure come JSON e riprodurre.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


In questo esempio viene illustrato come utilizzare [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) per creare un pre- trigger e creare un documento con trigger attivato. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


E nell'esempio seguente viene illustrato come creare una funzione definita dall'utente (utente) e usarla in una [query SQL DocumentDB](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Tutte le operazioni di DocumentDB possono essere eseguite in modo REST. Stored procedure, trigger e funzioni definite dall'utente possono essere registrate in una raccolta tramite HTTP POST. Di seguito è illustrato un esempio di come registrare una stored procedure:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Stored procedure registrata eseguendo una richiesta POST contro la URI dbs/testdb/colls/testColl/stored procedure con corpo contenente stored procedure da creare. Trigger e funzioni definite dall'utente possono essere registrati in modo analogo inviando un POST da /trigger e /udfs rispettivamente.
Questa stored procedure può quindi essere eseguita da una richiesta di POST in base del collegamento corrispondente risorsa:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


In questo caso, l'input per stored procedure viene passato nel corpo della richiesta. Si noti che l'input viene passato come matrice JSON dei parametri di input. Stored procedure accetta il primo input come un documento da una risposta. La risposta che è visualizzato è come indicato di seguito:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Trigger, diversamente da quanto succede stored procedure, non può essere eseguito direttamente. Invece di esecuzione come parte di un'operazione su un documento. È possibile specificare i trigger per l'esecuzione con una richiesta utilizzando le intestazioni HTTP. Di seguito è richiesta per creare un documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Di seguito pre-trigger per l'esecuzione con la richiesta viene specificato nell'intestazione x-ms-documentdb-pre-trigger-include. Analogamente, nell'intestazione x-ms-documentdb-post-trigger-include sono indicati i post trigger. Si noti che sia regole e post-trigger può essere specificato per una determinata richiesta.

## <a name="sample-code"></a>Codice di esempio

È possibile trovare ulteriori esempi di codice sul lato server (inclusi [eliminazione in blocco](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)e [aggiornare](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) nel nostro [archivio Github](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Se si vuole condividere stored procedure amici? Inviare una richiesta di pull! 

## <a name="next-steps"></a>Passaggi successivi

Dopo avere inserito una o più stored procedure, trigger e funzioni definite dall'utente create, è possibile caricarli e visualizzarle nel portale di Azure tramite Esplora Script. Per ulteriori informazioni, vedere [visualizzazione stored procedure, trigger e funzioni definite dall'utente in Esplora risorse Script DocumentDB](documentdb-view-scripts.md).

È inoltre possibile trovare i riferimenti e le risorse seguenti utile nel percorso per ulteriori informazioni sulla programmazione sul lato server DocumentDB:

- [SDK DocumentDB Azure](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [Studio DocumentDB](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript: sistema di tipo JSON](http://www.json.org/js.html) 
- [Estensibilità Database sicura e portatili](http://dl.acm.org/citation.cfm?id=276339) 
- [Architettura di Database orientato ai servizi](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Hosting del Runtime .NET in Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
