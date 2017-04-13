<properties
  pageTitle="Esercitazione NoSQL node per DocumentDB | Microsoft Azure"
  description="Esercitazione Node NoSQL che consente di creare un'applicazione di database e console nodo utilizzando SDK Node DocumentDB. DocumentDB è un database NoSQL per JSON."
    keywords="esercitazione Node, database del nodo"
  services="documentdb"
  documentationCenter="node.js"
  authors="AndrewHoh"
  manager="jhubbard"
  editor="monicar"/>

<tags
  ms.service="documentdb"
  ms.workload="data-services"
  ms.tgt_pltfrm="na"
  ms.devlang="node"
  ms.topic="hero-article"
  ms.date="08/11/2016"
  ms.author="anhoh"/>

# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Esercitazione NoSQL Node: applicazione console DocumentDB Node  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node](documentdb-nodejs-get-started.md)

Introduzione all'esercitazione node per Azure DocumentDB Node SDK! Dopo aver seguito in questa esercitazione, è necessario un'applicazione console viene creato e query DocumentDB risorse, incluso un database di nodo.

Verranno illustrate:

- Creazione e la connessione a un account DocumentDB
- Impostazione dell'applicazione
- Creazione di un database di nodo
- Creazione di una raccolta
- Creazione di documenti JSON
- La ricerca della raccolta
- Sostituzione di un documento
- Eliminazione di un documento
- Eliminare il database di nodo

Non ha tempo? Non preoccuparti! La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Per istruzioni rapide, vedere [ottenere la soluzione completa](#GetSolution) .

Dopo aver completato l'esercitazione Node, usare i pulsanti di voto superiore e inferiore della pagina per inviare il feedback. Se si desidera contattare direttamente, è possibile includere il proprio indirizzo di posta elettronica nei commenti.

A questo punto per iniziare.

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Prerequisiti per l'esercitazione Node

Assicurarsi che sono i seguenti:

- Un account Azure attivo. Se non si dispone di uno, è possibile iscriversi per una [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node](https://nodejs.org/) versione v0.10.29 o versione successiva.

## <a name="step-1-create-a-documentdb-account"></a>Passaggio 1: Creare un account DocumentDB

Creare un account DocumentDB. Se si dispone già di un account che si desidera utilizzare, può procedere alla [configurazione dell'applicazione Node](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Passaggio 2: Configurazione dell'applicazione Node

1. Aprire il terminale preferito.
2. Individuare la cartella o directory in cui si desidera salvare l'applicazione Node.
3. Creare due i file JavaScript vuoti con i comandi seguenti:
  - Windows:
      * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
  - Linux/OS x:
      * ```touch app.js```
        * ```touch config.js```
4. Installare il modulo documentdb tramite npm. Usare il comando seguente:
    * ```npm install documentdb --save```

Risposta corretta. Ora che aver completato la configurazione, iniziare l'esercitazione scrivendo del codice.

## <a id="Config"></a>Passaggio 3: Impostare le configurazioni dell'applicazione

Apri ```config.js``` in un editor di testo.

Quindi, copia e Incolla il frammento di codice seguente e impostare le proprietà ```config.endpoint``` e ```config.primaryKey``` a uri endpoint DocumentDB e chiave primaria. Entrambe le configurazioni sono disponibili nel [Portale di Azure](https://portal.azure.com).

![Esercitazione Node - schermata del portale di Azure, con un account DocumentDB con hub attivo evidenziato, il pulsante tasti evidenziato nella e conto DocumentDB e i valori URI, chiave primaria e chiave SECONDARIA evidenziati nella e tasti - database del nodo][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copiare e incollare la ```database id```, ```collection id```, e ```JSON documents``` per il ```config``` oggetto riportata di seguito in cui è impostato il ```config.endpoint``` e ```config.authKey``` proprietà. Se si dispone già di dati che si desidera memorizzare nel database, è possibile usare del DocumentDB [strumento di migrazione di dati](documentdb-import-data.md) piuttosto che aggiungere le definizioni di documento.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Database, raccolte e le definizioni di documento verranno utilizzato come il DocumentDB ```database id```, ```collection id```e i dati dei documenti.

Infine, esportare il ```config``` oggetto, in modo che è possibile fare riferimento all'interno di ```app.js``` file.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

##<a id="Connect"></a>Passaggio 4: Connettersi a un account DocumentDB

Aprire il vuoto ```app.js``` file nell'editor di testo. Copiare e incollare il codice riportato di seguito per importare le ```documentdb``` modulo e il nuovo ```config``` modulo.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copiare e incollare il codice per usare salvate in precedenza ```config.endpoint``` e ```config.primaryKey``` per creare un nuovo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Dopo aver creato il codice per inizializzare il client documentdb, diamo un'occhiata di utilizzo delle risorse DocumentDB.

## <a name="step-5-create-a-node-database"></a>Passaggio 5: Creare un database di nodo
Copiare e incollare il codice riportato di seguito per impostare lo stato HTTP per non viene trovato, l'url di database e l'url della raccolta. Questi URL sono come troveranno client DocumentDB il database corretto e insieme.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Un [database](documentdb-resources.md#databases) può essere create mediante la funzione [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient** . Un database è il contenitore logico di archiviazione dei documenti suddiviso in raccolte.

Copiare e incollare la funzione **getDatabase** per creare un nuovo database nel file app.js con la ```id``` specificato nel ```config``` oggetto. La funzione verifica se il database con lo stesso ```FamilyRegistry``` id non esiste già. Se esiste, si tornerà tale database anziché crearne uno nuovo.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copiare e incollare il codice seguente nel punto in cui si imposta la funzione **getDatabase** per aggiungere il supporto funzione **uscire** che verrà stampato il messaggio di uscita e la chiamata alla funzione **getDatabase** .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! Un database DocumentDB completata.

##<a id="CreateColl"></a>Passaggio 6: Creare una raccolta  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creerà un nuovo insieme, che ha prezzi implicazioni. Per ulteriori informazioni, visitare i [prezzi pagina](https://azure.microsoft.com/pricing/details/documentdb/).

Una [raccolta](documentdb-resources.md#collections) può essere create mediante la funzione [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient** . Una raccolta è un contenitore di documenti JSON e logica di applicazione JavaScript associata.

Copiare e incollare la funzione **getCollection** sotto la funzione **getDatabase** per creare il nuovo insieme con la ```id``` specificato nel ```config``` oggetto. Nuovo, sarà necessario verificare assicurarsi che una raccolta con lo stesso ```FamilyCollection``` id non esiste già. Se esiste, si tornerà tale raccolta anziché crearne uno nuovo.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copiare e incollare il codice seguente la chiamata a **getDatabase** per eseguire la funzione **getCollection** .

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! La creazione di un insieme di DocumentDB è stata completata.

##<a id="CreateDoc"></a>Passaggio 7: Creare un documento
Un [documento](documentdb-resources.md#documents) può essere create mediante la funzione [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient** . I documenti sono contenuti JSON (non autorizzato) definite dall'utente. DocumentDB a questo punto è possibile inserire un documento.

Copiare e incollare la funzione **getFamilyDocument** di sotto della funzione **getCollection** per la creazione di documenti che contengono i dati JSON salvati nel ```config``` oggetto. Nuovamente, è necessario verificare che non ne esiste già un documento con lo stesso id.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copiare e incollare il codice seguente la chiamata a **getCollection** per eseguire la funzione **getFamilyDocument** .

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! La creazione di un documento DocumentDB è stata completata.

![Database - diagramma che illustra la relazione tra l'account, il database, la raccolta e i documenti - nodo esercitazioni Node](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Passaggio 8: Query DocumentDB risorse

DocumentDB supporta [query avanzate](documentdb-sql-query.md) su JSON documenti archiviati in ogni raccolta. Il codice di esempio seguente mostra una query che è possibile eseguire sui documenti nella raccolta.

Copiare e incollare la funzione **queryCollection** sotto la funzione **getFamilyDocument** . DocumentDB supporta query SQL, come illustrato di seguito. Per ulteriori informazioni sulla creazione di query complesse, vedere la [documentazione della query](documentdb-sql-query.md)e [Di test Query](https://www.documentdb.com/sql/demo) .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Il diagramma seguente illustra la sintassi della query SQL DocumentDB è richiamo sull'insieme è creato.

![Database - diagramma che illustra l'ambito e il significato della query - nodo esercitazioni Node](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md#from-clause) è facoltativa nella query perché query DocumentDB già sono limitate a un'unica raccolta. Di conseguenza, "Da famiglie f" possono essere scambiati con "Da r principale" o qualsiasi altra variabile il nome di scegliere. DocumentDB verranno dedurre tale famiglie, radice o il nome della variabile che scelto, fare riferimento all'insieme corrente per impostazione predefinita.

Copiare e incollare il codice seguente la chiamata a **getFamilyDocument** per eseguire la funzione **queryCollection** .

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! Query DocumentDB documenti.

##<a id="ReplaceDocument"></a>Passaggio 9: Sostituire un documento
DocumentDB supporta la sostituzione JSON documenti.

Copiare e incollare la funzione **replaceDocument** sotto la funzione **queryCollection** .

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiare e incollare il codice seguente la chiamata a **queryCollection** per eseguire la funzione **replaceDocument** . Inoltre, aggiungere il codice per chiamare **queryCollection** nuovamente per verificare che il documento ha modificato.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! È stato sostituito correttamente un documento DocumentDB.

##<a id="DeleteDocument"></a>Passaggio 10: Eliminare un documento
DocumentDB supporta documenti JSON eliminazione.

Copiare e incollare la funzione **deleteDocument** sotto la funzione **replaceDocument** .

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiare e incollare il codice seguente la chiamata alla seconda **queryCollection** per eseguire la funzione **deleteDocument** .

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Congratulazioni! Un documento DocumentDB eliminata.

##<a id="DeleteDatabase"></a>Passaggio 11: Eliminare il database di nodo

Eliminare il database creato comporta la rimozione del database e tutte le risorse figli (raccolte, documenti e così via).

Copiare e incollare il frammento di codice seguenti (funzione **pulizia**) per rimuovere il database e tutte le risorse di figli.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copiare e incollare il codice seguente la chiamata a **deleteDocument** per eseguire la funzione di **pulizia** .

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a>Passaggio 12: Esegui applicazione Node tutte insieme!

Completamente, la sequenza per chiamare le funzioni deve simile al seguente:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale, individuare il ```app.js``` file ed eseguire il comando:```node app.js```

Verrà visualizzato l'output dell'app introduttiva get. L'output deve corrispondere il testo di esempio riportato di seguito.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Congratulazioni! È stata creata è stata completata l'esercitazione Node e ha l'applicazione di console DocumentDB prima!

## <a id="GetSolution"></a>Ottenere la soluzione esercitazione Node completa
Per creare la soluzione GetStarted che contiene tutti gli esempi di questo articolo, è necessario quanto segue:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponibile in GitHub.

Installare il modulo **documentdb** tramite npm. Usare il comando seguente:
* ```npm install documentdb --save```

Successivo, nel ```config.js``` file, aggiornare i valori config.endpoint e config.authKey come descritto nel [passaggio 3: impostare le configurazioni dell'applicazione](#Config).

## <a name="next-steps"></a>Passaggi successivi

-   L'esigenza di un campione Node più complesso? Vedere [creare un'applicazione web Node utilizzando DocumentDB](documentdb-nodejs-application.md).
-  Informazioni su come [monitor di un account DocumentDB](documentdb-monitor-accounts.md).
-  Eseguire query sul set di dati di esempio in [Query di test](https://www.documentdb.com/sql/demo).
-  Ulteriori informazioni sul modello di programmazione nella sezione elaborazione della [pagina della documentazione DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
