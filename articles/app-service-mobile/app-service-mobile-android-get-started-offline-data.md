<properties
    pageTitle="Attivare sincronizzazione offline per l'App Mobile Azure (Android)"
    description="Informazioni su come utilizzare App Mobile servizio App alla cache e sincronizzazione dei dati non in linea nell'applicazione Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Attivare la sincronizzazione offline per le app per dispositivi mobili Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione è descritta la funzionalità di sincronizzazione offline di App di Azure Mobile per Android. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete. Le modifiche vengono memorizzate in un database locale. Quando il dispositivo è in linea, queste modifiche vengono sincronizzate con la copia nel server remoto.

Se si verifica questo problema prima delle App Mobile Azure, è necessario completare prima di tutto l'esercitazione [creare un'App di Android]. Se non si utilizza il progetto di server introduttive scaricato, è necessario aggiungere i pacchetti di estensione accesso dati al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per ulteriori informazioni sulla caratteristica di sincronizzazione offline, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Aggiornare l'app per supportare sincronizzazione offline

Sincronizzazione offline, leggere e scrivere da una *tabella di sincronizzazione* (mediante l'interfaccia *IMobileServiceSyncTable* ), che fa parte di un database **SQLite** nel dispositivo.

Per inserire e recuperare modifiche tra il dispositivo e servizi di Windows Azure Mobile, si utilizza un *contesto di sincronizzazione* (*MobileServiceClient.SyncContext*), che inizializzare con il database locale per archiviare i dati in locale.

1. In `TodoActivity.java`, commento la definizione esistente di `mToDoTable` e rimuovere il commento per la versione di tabella sincronizzazione:

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Nel `onCreate` metodo, commento esistente inizializzare `mToDoTable` e rimuovere il commento per la definizione di:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. In `refreshItemsFromTable` commento la definizione di `results` e rimuovere il commento per la definizione di:

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Commento la definizione di `refreshItemsFromMobileServiceTable`.

5. Rimuovere il commento per la definizione di `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Rimuovere il commento per la definizione di `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Verificare l'app

In questa sezione, verificare il funzionamento con WiFi e quindi disattivare WiFi per creare uno scenario offline.

Quando si aggiungono elementi di dati, sono contenuti nell'archivio SQLite locale, ma non sincronizzati con il servizio mobile finché non si preme il pulsante **Aggiorna** . Altre App può avere diverse esigenze relative ai dati, quando devono essere sincronizzata ma ai fini della demo in questa esercitazione è l'utente in modo esplicito richiesta.

Quando si preme il pulsante, verrà avviata una nuova attività in background. Primo inserisce tutte le modifiche apportate all'archivio locale utilizzando contesto di sincronizzazione, quindi dati pull tutti modificato da Azure alla tabella locale.

### <a name="offline-testing"></a>Test non in linea

1. Spostare il dispositivo o simulatore in *Modalità aereo*. Verrà creato uno scenario offline.

2. Aggiungere alcuni elementi *ToDo* o contrassegnare alcuni elementi come completato. Chiudere il dispositivo o simulatore (o forzare l'app) e riavviare. Verificare che le modifiche sono state mantenute nel dispositivo perché sono presenti nell'archivio SQLite locale.

3. Visualizzare il contenuto della tabella Azure *TodoItem* con SQL strumento, ad esempio *SQL Server Management Studio*o un client resto, ad esempio *Fiddler* o *Postman*. Verificare che i nuovi elementi _non_ sono state sincronizzate nel server

    + Per un back-end Node, passare al [portale di Azure](https://portal.azure.com/)e nell'App Mobile back-end fare clic su **Tabelle facile** > **TodoItem** per visualizzare il contenuto del `TodoItem` tabella.
    + Per un back-end .NET, consente di visualizzare il contenuto della tabella con uno strumento SQL, ad esempio *SQL Server Management Studio*o un client resto, ad esempio *Fiddler* o *Postman*.

4. Attivare WiFi nel dispositivo o simulatore. Successivamente, fare clic sul pulsante **Aggiorna** .

5. Visualizzare nuovamente i dati TodoItem nel portale di Azure. TodoItems nuove e modificate dovrebbero essere visualizzati.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]

* [Cloud copertina: sincronizzazione Offline in Azure servizi mobili] \(Nota: è il video su Mobile Services, ma funzionamento della sincronizzazione offline in modo analogo nell'App Mobile Azure\)


<!-- URLs. -->

[Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]: app-service-mobile-offline-data-sync.md

[Creare un'App di Android]: app-service-mobile-android-get-started.md

[Copertina del manuale cloud: Sincronizzazione Offline in Azure servizi per dispositivi mobili]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

