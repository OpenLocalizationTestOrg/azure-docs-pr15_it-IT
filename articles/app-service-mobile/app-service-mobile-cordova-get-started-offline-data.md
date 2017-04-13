<properties
    pageTitle="Attivare sincronizzazione offline per l'App Mobile Azure (Cordova) | Microsoft Azure"
    description="Informazioni su come utilizzare App servizio Mobile App per cache e sincronizzazione dei dati non in linea nell'applicazione Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Attivare la sincronizzazione offline per le app per dispositivi mobili Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

In questa esercitazione introduce la caratteristica di sincronizzazione offline dell'App Mobile Azure per Cordova. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo è in linea, queste modifiche vengono sincronizzate con il servizio remoto.

In questa esercitazione si basa sulla soluzione Guida introduttiva Cordova per App Mobile che si crea una volta completata l'esercitazione [Cordova Apache quick start]. In questa esercitazione si aggiorna la soluzione Guida introduttiva per aggiungere caratteristiche non in linea dell'App Mobile Azure. Verranno inoltre evidenziati il codice offline specifico nell'app.

Per ulteriori informazioni sulla caratteristica di sincronizzazione offline, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure]. Per informazioni dettagliate di uso di API, vedere il file [Leggimi] il plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Aggiunge la sincronizzazione offline per la soluzione Guida introduttiva

Il codice di sincronizzazione offline deve essere aggiunta all'app. Sincronizzazione offline richiede plug-in archiviazione di sqlite cordova, viene aggiunto automaticamente l'app quando il plug-in app di Azure Mobile è incluso nel progetto. Il progetto di Guida introduttiva include entrambi i plug-in.

1. In Esplora soluzioni di Visual Studio, aprire index.js e sostituire il seguente codice

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    con questo codice:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Successivamente, sostituire il codice seguente:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    con questo codice:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Le aggiunte codice precedente inizializzare l'archivio locale e definire una tabella locale che soddisfa i valori della colonna utilizzati nel Azure back-end. (Non è necessario includere tutti i valori di colonna in questo codice.)

    Viene visualizzato un riferimento al contesto della sincronizzazione chiamando **getSyncContext**. Il contesto di sincronizzazione è possibile mantenere le relazioni tra tabelle, verifica e inserendo le modifiche in tutte le tabelle che un'applicazione client modificato quando si chiama **push** .

3. Aggiornare l'URL dell'applicazione per l'URL di applicazione App Mobile.

4. Successivamente, sostituire il codice:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    con questo codice:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Il codice precedente consente di inizializzare il contesto di sincronizzazione e quindi chiama getSyncTable (anziché getTable) per ottenere un riferimento alla tabella locale.

    Questo codice viene utilizzato il database locale per tutti di creare, leggere, Aggiorna ed eliminazione (CRUD) tabella.

    In questo esempio esegue errori semplice gestione per i conflitti di sincronizzazione. Un'applicazione reale è necessario gestire i diversi errori come condizioni di rete, il server è in conflitto e gli altri utenti. Per esempi di codice, vedere l' [esempio di sincronizzazione offline].

5. Aggiungere quindi questa funzione per eseguire la sincronizzazione effettiva.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    È possibile decidere quando push delle modifiche in back-end App Mobile chiamando **push** sull'oggetto **syncContext** utilizzato dal client. Ad esempio, è possibile aggiungere una chiamata a **syncBackend** a un gestore eventi pulsante nell'app, ad esempio un nuovo pulsante di sincronizzazione, oppure è possibile aggiungere la chiamata alla funzione **addItemHandler** per sincronizzare ogni volta che viene aggiunto un nuovo elemento.

##<a name="offline-sync-considerations"></a>Considerazioni sulla sincronizzazione offline

Nell'esempio, il metodo **push** di **syncContext** viene chiamato solo all'avvio di app nella funzione callback per l'accesso.  In un'applicazione reale, è possibile effettuare questa funzionalità di sincronizzazione attivate manualmente o quando cambia lo stato di rete.

Se un pull viene eseguito in una tabella in cui è in sospeso aggiornamenti locali tracciato in base al contesto, tale operazione pull attiverà automaticamente un push contesto precedente. Durante l'aggiornamento, aggiunta e il completamento di elementi in questo esempio, è possibile omettere la chiamata esplicita **push** , poiché potrebbe essere ridondante (primo controllo [file Leggimi] per lo stato della caratteristica corrente).

Il codice specificato, tutti i record nella tabella todoItem remote vengono eseguite query, ma è anche possibile filtrare i record passando un id di query e query da **inserire**. Per ulteriori informazioni, vedere la sezione *Sincronizzazione incrementale* in [Modalità Offline sincronizzazione dei dati nell'App Mobile Azure].

## <a name="optional-disable-authentication"></a>(Facoltativo) Disabilitare l'autenticazione

Se non è stato già configurato la autenticazione e non si vuole impostare l'autenticazione prima della sincronizzazione offline test, la funzione di callback per l'accesso come commento, ma lasciare il codice all'interno della funzione callback rimuovere.

Il codice dovrebbe risultare analoga alla seguente dopo commento le linee di accesso.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

A questo punto, l'app verrà sincronizzata con Azure back-end quando si esegue l'app anziché all'accesso.

## <a name="run-the-client-app"></a>Eseguire l'applicazione client

Sincronizzazione offline dopo aver attivato, è ora possibile eseguire l'applicazione client almeno una volta a seconda della piattaforma per popolare il database di archivio locale. In un secondo momento, si simulare uno scenario offline e modificare i dati nell'archivio locale, mentre l'app è offline.

## <a name="optional-test-the-sync-behavior"></a>(Facoltativo) Verificare il comportamento di sincronizzazione

In questa sezione, si modificherà il progetto di client per simulare uno scenario offline utilizzando un URL dell'applicazione non valida per il back-end. Quando si aggiungono o modificare elementi di dati, queste modifiche verranno conservate nell'archivio locale, ma non sincronizzate con l'archivio di dati back-end finché non viene ripristinata la connessione.

1. In Esplora soluzioni aprire il file di progetto index.js e modificare l'URL dell'applicazione in modo che puntino a un URL non valido, ad esempio le operazioni seguenti:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. In Index, aggiornare il CSP `<meta>` elemento con lo stesso URL non validi.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Creare ed eseguire l'applicazione client e notare che verrà registrata nella console di un'eccezione durante il tentativo di sincronizzazione con la copia nel server dopo l'accesso con l'app. I nuovi elementi che si aggiungono saranno presente solo nell'archivio locale fino a quando non può essere inseriti nel back-end per dispositivi mobili. L'applicazione client si comporta come se è connesso al back-end, supporto che tutte creazione, lettura, aggiornamento, le operazioni di eliminazione (CRUD).

4. Chiudere l'applicazione e riavviarla per verificare che i nuovi elementi creati vengono mantenuti all'archivio locale.

5. (Facoltativo) Utilizzare Visual Studio per visualizzare la tabella di Database SQL Azure per verificare che i dati nel database back-end non è sono modificato.

    In Visual Studio, aprire **Esplora Server**. Passare al database in **Azure**->**Database SQL**. Il pulsante destro del database e selezionare **Apri in Esplora oggetti di SQL Server**. A questo punto è possibile passare a tabella di database SQL e il relativo contenuto.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facoltativo) Testare la riconnessione per il back-end per dispositivi mobili

In questa sezione riconnessione l'app alla back-end per dispositivi mobili simulare app fedeli a uno stato online. Quando si accede dati verranno sincronizzati con i dispositivi mobili back-end.

1. Riaprire index.js e correggere l'URL dell'applicazione in modo che puntino all'URL corretto.

2. Riaprire index e correggere l'URL dell'applicazione in CSP `<meta>` elemento.

3. Rigenerare ed eseguire l'applicazione client. L'app tenta di sincronizzare con back-end app per dispositivi mobili dopo l'accesso. Verificare che si è connessi senza eccezioni nella console di debug.

4. (Facoltativo) Visualizzare i dati aggiornati tramite Esplora oggetti di SQL Server o uno strumento resto come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end e l'archivio locale.

    Si noti i dati sono stati sincronizzati tra il database e l'archivio locale e contenente gli elementi che aggiunti durante l'app è stata interrotta.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]

* [Cloud copertina: sincronizzazione Offline in Azure servizi mobili] \(Nota: è il video su Mobile Services, ma funzionamento della sincronizzazione offline in modo analogo nell'App Mobile Azure\)

* [Strumenti di Visual Studio per Apache Cordova]

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le funzionalità di sincronizzazione offline più avanzate, ad esempio la risoluzione dei conflitti nel [campione sincronizzazione offline]
* Esaminare la sincronizzazione offline di riferimento all'API nel [file Leggimi]

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Guida introduttiva Cordova Apache]: app-service-mobile-cordova-get-started.md
[FILE LEGGIMI]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[esempio di sincronizzazione offline]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]: app-service-mobile-offline-data-sync.md
[Copertina del manuale cloud: Sincronizzazione Offline in Azure servizi per dispositivi mobili]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Strumenti di Visual Studio per Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
