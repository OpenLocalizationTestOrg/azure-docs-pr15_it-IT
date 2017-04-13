<properties
    pageTitle="Attivare sincronizzazione offline per l'App Mobile Azure (iOS)"
    description="Informazioni su come utilizzare App Mobile servizio App alla cache e sincronizzazione dei dati non in linea nell'applicazione di iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Attivare la sincronizzazione offline per le app per dispositivi mobili iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione è descritta la caratteristica di sincronizzazione offline di Azure Mobile App per iOS. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo è in linea, queste modifiche vengono sincronizzate con la copia nel server remoto.

Se si verifica questo problema prima delle App Mobile Azure, è necessario completare prima di tutto l'esercitazione [creare un'App per iOS]. Se non si utilizza il progetto di server introduttive scaricato, è necessario aggiungere i pacchetti di estensione accesso dati al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per ulteriori informazioni sulla caratteristica di sincronizzazione offline, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure].

## <a name="review-sync"></a>Esaminare il codice di sincronizzazione client

Il progetto client scaricati per l'esercitazione [creare un'App per iOS] già contiene codice di supporto della sincronizzazione offline utilizzando un database locale basato su dati principali. In questa sezione è riportato un riepilogo dei quali è già incluso il codice dell'esercitazione. Per una panoramica della caratteristica, vedere [Offline sincronizzazione dei dati nell'App Mobile Azure].

La caratteristica di sincronizzazione di sincronizzazione di dati non in linea dell'App Mobile Azure consente agli utenti finali di interagire con un database locale quando non è possibile accedere alla rete. Per utilizzare queste funzionalità nell'app, si inizializzare il contesto di sincronizzazione del `MSClient` e fanno riferimento a un archivio locale. Fare riferimento la tabella tramite il `MSSyncTable` interfaccia.

1. Nella finestra **QSTodoService.m** (obiettivo-C) o **ToDoTableViewController.swift** (Swift), controllare il tipo del membro `syncTable` è `MSSyncTable`. Sincronizzazione offline utilizza questa interfaccia di tabella sincronizzazione anziché `MSTable`. Quando viene utilizzata una tabella di sincronizzazione, tutte le operazioni di passare all'archivio locale e vengono sincronizzate solo con back-end remote con operazioni push e pull esplicite.

    Per ottenere un riferimento a una tabella di sincronizzazione, utilizzare il metodo `syncTableWithName` in `MSClient`. Per rimuovere la funzionalità di sincronizzazione offline, utilizzare `tableWithName` invece.

2. Prima di possono eseguire le operazioni di tabella, è necessario inizializzare l'archivio locale. Ecco il codice corrispondente. 
    
    **Obiettivo-C**:
    
    Nel `QSTodoService.init` metodo:
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    Nel `ToDoTableViewController.viewDidLoad` metodo:
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Consente di creare un archivio locale mediante l'interfaccia `MSCoreDataStore`, che viene fornito in SDK App Mobile. È possibile invece una specifica un archivio locale diverso mediante l'implementazione di `MSSyncContextDataSource` protocollo. 
    
    Inoltre, il primo parametro del `MSSyncContext` consente di specificare un gestore conflitto. Dal momento che è stato passato `nil`, si otterrà il gestore di conflitto predefinito, si verifica un errore in conflitto.
    
3. A questo punto, verrà eseguire l'operazione di sincronizzazione effettivo e recuperare dati da back-end remoto.

    **Obiettivo-C**:
    
    `syncData`prima di tutto inserisce nuove modifiche, quindi chiama `pullData` per recuperare dati da back-end remoto. A sua volta, il metodo `pullData` Ottiene nuovi dati che corrispondono a una query:
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    Nella versione obiettivo C in `syncData`, è innanzitutto necessario chiamare `pushWithCompletion` nel contesto di sincronizzazione. Questo metodo è un membro del `MSSyncContext` (piuttosto che la sincronizzazione della tabella Items) perché sposterà modifiche tra tutte le tabelle. Solo i record che sono stati modificati in modo localmente (attraverso operazioni CUD) verranno inviati al server. Quindi il supporto per la `pullData` è chiamato, quali chiamate `MSSyncTable.pullWithQuery` per recuperare dati remoti e archiviare nel database locale.
    
    Nella versione rapida non esiste nessuna chiamata a `pushWithCompletion`. Ciò avviene perché l'operazione push non è indispensabile. Se sono state apportate modifiche in sospeso nel contesto di sincronizzazione per la tabella che esegue un'operazione push, pull sempre problemi prima di tutto un push. Tuttavia, se si dispone di più di una tabella di sincronizzazione, è meglio chiamare in modo esplicito push per assicurarsi che tutto sia coerenza in tabelle correlate.
    
    Nelle versioni a C obiettivo e Swift, il metodo `pullWithQuery` consente di specificare una query per filtrare i record che si desidera recuperare. In questo esempio, semplicemente tutti i record recuperati in remoto `TodoItem` tabella.
    
    Il secondo parametro di `pullWithQuery` un ID di query viene utilizzato per *la sincronizzazione incrementale*. Sincronizzazione incrementale recupera solo i record modificati dopo l'ultima sincronizzazione con il record `UpdatedAt` timestamp (denominata `updatedAt` nell'archivio locale.) L'ID di query deve essere una stringa descrittiva che sia univoca per ogni query logico nell'app. Per non ricevere messaggi di sincronizzazione incrementale passare `nil` come l'ID di query. Si noti che può essere potenzialmente inefficace, dal momento che recupera tutti i record in ogni operazione pull.

5. Sincronizza app obiettivo C quando si modificano o si aggiungono dati, un utente esegue la combinazione di aggiornamento, quindi nella barra di avvio. L'app rapido Sincronizza quando un utente esegue l'azione di aggiornamento e nella barra di avvio. 

Poiché il Sincronizza app ogni volta che dati modificati (obiettivo-C) oppure ogni volta che l'avvio dell'applicazione (obiettivo C & Swift), l'app presuppone che l'utente è online. In un'altra sezione, Microsoft aggiornerà l'app in modo che gli utenti possono modificare anche quando sono offline.

## <a name="review-core-data"></a>Esaminare il modello di dati di base

Quando si usa l'archivio dati principali offline, è necessario definire particolare tabelle e campi in un modello di dati. Applicazione di esempio include già un modello di dati con il formato appropriato. In questa sezione Esamineremo queste tabelle e le modalità di utilizzo.

- Aprire **QSDataModel.xcdatamodeld**. Sono disponibili quattro tabelle definite tre utilizzati da SDK, e gli elementi di una tabella per la todo sole:     *MS_TableOperations: per tenere traccia di elementi che devono essere sincronizzati con il server    * MS_TableOperationErrors: per rilevare eventuali errori che si verificano durante la sincronizzazione offline     *MS_TableConfig: per tenere traccia dell'ultimo aggiornamento ora per l'ultima operazione di sincronizzazione per tutte le operazioni pull    * TodoItem : Per archiviare gli elementi todo. Il sistema colonne **createdAt**, **updatedAt**e **la versione** sono proprietà-sistema facoltativo.

>[AZURE.NOTE] Azure Mobile App SDK riserva i nomi delle colonne, vale a dire con "**``**". Non è necessario utilizzare questo prefisso su un valore diverso da colonne di sistema, in caso contrario verranno modificati i nomi di colonna quando si usa la copia nel server remoto.

- Quando si utilizza la caratteristica di sincronizzazione offline, è necessario definire le tabelle di sistema, come illustrato di seguito.

    ### <a name="system-tables"></a>Tabelle di sistema

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Attributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Numero intero 64  |
  	| itemId     | Stringa      |
  	| proprietà | Dati binari |
  	| tavolo      | Stringa      |
  	| tableKind  | Numero intero 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Attributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Stringa      |
  	| operationId | Numero intero 64 |
  	| proprietà | Dati binari |
  	| tableKind  | Numero intero 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Attributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Stringa      |
  	| chiave        | Stringa      |
  	| keyType    | Numero intero 64  |
  	| tavolo      | Stringa      |
  	| valore      | Stringa      |

    ### <a name="data-table"></a>Tabella dati

    **TodoItem**

  	| Attributo    |  Tipo   | Nota                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| ID           | Stringa obbligatoria  | chiave primaria nell'archivio remoto                            |
  	| completare     | Valore booleano | campo della voce TODO                                        |
  	| testo         | Stringa  | campo della voce TODO                                        |
  	| createdAt | Data    | (facoltativo) esegue il mapping a proprietà di sistema createdAt         |
  	| updatedAt | Data    | (facoltativo) esegue il mapping a proprietà di sistema updatedAt         |
  	| Versione   | Stringa  | (facoltativo) utilizzati per rilevare conflitti, mappe alla versione |


## <a name="setup-sync"></a>Modificare il comportamento di sincronizzazione dell'app

In questa sezione verrà modificato l'app in modo che non vengono sincronizzati nella schermata start app o quando l'inserimento e aggiornamento degli elementi, ma solo quando viene eseguito tramite il pulsante di azione Aggiorna.

**Obiettivo-C**:

1. In **QSTodoListViewController.m**, modificare il metodo **viewDidLoad** per rimuovere la chiamata a `[self refresh]` alla fine del metodo. A questo punto, i dati non essere sincronizzati con il server all'avvio di app, ma saranno possibile il contenuto dell'archivio locale.

2. In **QSTodoService.m**, modificare la definizione di `addItem` in modo che esso non è possibile sincronizzare dopo l'inserimento dell'elemento. Rimuovere il `self syncData` bloccare e sostituire con le operazioni seguenti:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modificare la definizione di `completeItem` come illustrato in precedenza; rimuovere il blocco per `self syncData` e sostituire con le operazioni seguenti:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. In `viewDidLoad` **ToDoTableViewController.swift**commento queste due righe per interrompere la sincronizzazione nella schermata start di app. Al momento della stesura di questo articolo, l'app Todo Swift non vengono aggiornate del servizio quando qualcuno aggiunge o un elemento, solo all'avvio dell'applicazione non viene completata.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Verificare l'app

In questa sezione, si è connessi a un URL non valido per simulare uno scenario offline. Quando si aggiungono elementi di dati, sono verranno contenuti nell'archivio dati principali locale, ma non sincronizzati con i dispositivi mobili back-end.

1. Modificare l'URL di App Mobile in **QSTodoService.m** in un URL non valido ed eseguire di nuovo l'app:

    **Obiettivo C** in QSTodoService.m:
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** in ToDoTableViewController.swift:

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Aggiungere alcuni elementi todo. Chiudere il simulatore (o forzare l'app) e riavviare. Verificare che le modifiche sono state mantenute.

3. Visualizzare il contenuto della tabella TodoItem remota:

    + Per un back-end Node, passare al [portale di Azure](https://portal.azure.com/)e nell'App Mobile back-end fare clic su **Tabelle facile** > **TodoItem** per visualizzare il contenuto del `TodoItem` tabella.
    + Per un back-end .NET, consente di visualizzare il contenuto della tabella con uno strumento SQL, ad esempio SQL Server Management Studio o un client resto, ad esempio Fiddler o Postman.

    Verificare che i nuovi elementi *non* sono state sincronizzate nel server:

4. Modificare l'URL per il corretto in **QSTodoService.m** ed eseguire nuovamente l'app. Eseguire l'azione Aggiorna il pull e verso il basso l'elenco degli elementi. Verrà visualizzata una casella di selezione lo stato di avanzamento.

5. Visualizzare i dati TodoItem nuovamente. TodoItems nuove e modificate dovrebbero essere visualizzati.

## <a name="summary"></a>Riepilogo

Per supportare la caratteristica di sincronizzazione offline, è stato usato il `MSSyncTable` l'interfaccia e inizializzato `MSClient.syncContext` con un archivio locale. In questo caso l'archivio locale è stato un database basato su dati principali.

Quando si utilizza un archivio locale dati principali, è necessario definire diverse tabelle con le [Proprietà-sistema corretto](#review-core-data).

Le operazioni CRUD normale per Azure Mobile App funzionano come se l'app è ancora connessi ma tutte le operazioni essere eseguita per l'archivio locale.

Quando si desidera sincronizzare l'archivio locale con il server, è stato usato il `MSSyncTable.pullWithQuery`metodo.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]

* [Cloud copertina: sincronizzazione Offline in Azure servizi mobili] \(Nota: è il video su Mobile Services, ma funzionamento della sincronizzazione offline in modo analogo nell'App Mobile Azure\)

<!-- URLs. -->


[Creare un'App per iOS]: app-service-mobile-ios-get-started.md
[Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Copertina del manuale cloud: Sincronizzazione Offline in Azure servizi per dispositivi mobili]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
