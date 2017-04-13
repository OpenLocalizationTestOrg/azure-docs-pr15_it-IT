<properties
    pageTitle="Attivare la sincronizzazione offline per l'app universale Windows piattaforma (UWP) con App Mobile | Servizio App Azure"
    description="Informazioni su come utilizzare un'App Mobile Azure cache e sincronizzazione dei dati non in linea nell'app universale Windows piattaforma (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Attivare la sincronizzazione offline per un'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come aggiungere un'app universale Windows piattaforma (UWP) usando un back-end App Mobile Azure supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili - visualizzazione, aggiunta o la modifica dei dati, anche quando non vi è alcuna connessione di rete. Le modifiche vengono memorizzate in un database locale. Quando il dispositivo è in linea, queste modifiche vengono sincronizzate con la copia nel server remoto.

In questa esercitazione si aggiorna il progetto di app UWP esercitazioni [Crea un'app di Windows] per supportare le funzionalità non in linea dell'App Mobile Azure. Se non si utilizza il progetto di server introduttive scaricato, è necessario aggiungere i pacchetti di estensione accesso dati al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per ulteriori informazioni sulla caratteristica di sincronizzazione offline, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure].

## <a name="requirements"></a>Requisiti

In questa esercitazione richiede i prerequisiti seguenti:

* Visual Studio 2013 in esecuzione in Windows 8.1 o versione successiva.
* Completamento di [creare un'app di Windows][creare un'app di windows].
* [Azure Mobile Services SQLite archivio][sqlite store nuget]
* [SQLite per lo sviluppo di piattaforma Windows universale](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Aggiornare l'app di client per supportare le funzionalità non in linea

Caratteristiche non in linea di Azure App Mobile consentono di interagire con un database locale quando ci si trova in uno scenario offline. Per utilizzare queste funzionalità nell'app, si inizializzare un [SyncContext] [ synccontext] in un archivio locale. Fare riferimento la tabella tramite l'interfaccia di[IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite viene utilizzato come archivio locale nel dispositivo.

1. Installare il [runtime SQLite per la piattaforma Windows universale](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. In Visual Studio, aprire la gestione di pacchetti NuGet per il progetto app UWP completate nell'esercitazione [Crea un'app di Windows] .
    Cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. In Esplora soluzioni fare doppio clic **riferimenti** > **Aggiungi riferimento**  >  **Windows universale** > **estensioni**, quindi attivare **SQLite per universale piattaforma Windows** e **Visual C++ 2015 Runtime per le app universale piattaforma Windows**.

    ![Aggiungere un riferimento SQLite UWP][1]

5. Aprire il file MainPage.xaml.cs e rimuovere il commento per il `#define OFFLINE_SYNC_ENABLED` definizione.

6. In Visual Studio, premere **F5 per ricompilare ed eseguire l'applicazione client** . L'app funziona esattamente come prima di attivare sincronizzazione offline. Il database locale popolato con i dati che possono essere usati in uno scenario offline.

## <a name="update-sync"></a>Aggiornare l'app per disconnettersi da back-end

In questa sezione, si interrompe la connessione al back-end App Mobile per simulare una situazione offline. Quando si aggiungono elementi di dati, il gestore di eccezioni indica che l'app viene eseguita in modalità offline. In questo stato nuovi elementi aggiunti nell'archivio locale e vengono sincronizzati con back-end app per dispositivi mobili quando push successivo viene eseguito in stato di connessione.

1. Modificare App.xaml.cs nel progetto condiviso. Commento inizializzare **MobileServiceClient** e aggiungere la riga seguente, che utilizza un URL di app per dispositivi mobili non validi:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    È possibile illustrare il comportamento non in linea disabilitando wifi e reti cellulari sul dispositivo o utilizzare la modalità aereo.

2. Premere **F5** per generare ed eseguire l'app. Si noti la sincronizzazione non è riuscita dopo l'aggiornamento all'avvio dell'app.

3. Inserire le nuove voci e notare che push ha esito negativo e uno stato [CancelledByNetworkError] ogni volta che si fa clic su **Salva**. Tuttavia, i nuovi elementi todo esistono nell'archivio locale fino a quando non può essere inseriti nel back-end app per dispositivi mobili.  In un'app di produzione, se si eliminano queste eccezioni dell'applicazione client si comporta come se è ancora connesso al back-end app per dispositivi mobili.

4. Chiudere l'applicazione e riavviarla per verificare che i nuovi elementi creati vengono mantenuti all'archivio locale.

5. (Facoltativo) In Visual Studio, aprire **Esplora Server**. Passare al database in **Azure**->**Database SQL**. Il pulsante destro del database e selezionare **Apri in Esplora oggetti di SQL Server**. A questo punto è possibile passare a tabella di database SQL e il relativo contenuto. Verificare che i dati nel database back-end non è sono modificato.

6. (Facoltativo) Utilizzare uno strumento resto, ad esempio Fiddler o Postman per ottenere tramite query il back-end per dispositivi mobili, utilizzando una query GET nel formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aggiornare l'app per riconnettersi il back-end App Mobile

In questa sezione, si riconnette l'app alla back-end app per dispositivi mobili. Queste modifiche consente di simulare una riconnessione di rete di App.

Quando si esegue prima dell'applicazione di `OnNavigatedTo` chiamate gestore eventi `InitLocalStoreAsync`. Questo metodo chiama a sua volta `SyncAsync` per sincronizzare l'archivio locale con il database back-end. L'app tenta di sincronizzare all'avvio.

1. Aprire App.xaml.cs nel progetto condiviso e rimuovere il commento per il precedente inizializzare `MobileServiceClient` utilizzerà il corretto l'URL di app per dispositivi mobili.

2. Premere **F5 per ricompilare ed eseguire l'app** . L'app Sincronizza le modifiche locali con Azure Mobile App back-end tramite operazioni push e pull quando il `OnNavigatedTo` gestore eventi viene eseguito.

3. (Facoltativo) Visualizzare i dati aggiornati tramite Esplora oggetti di SQL Server o uno strumento resto come Fiddler. Avviso i dati è stato sincronizzato tra il database back-end App Mobile Azure e l'archivio locale.

4. Nell'app, fare clic sulla casella di controllo accanto a alcuni elementi per completarle nell'archivio locale.

  `UpdateCheckedTodoItem`chiamate `SyncAsync` all'elemento sincronizzazione ogni completata con back-end App Mobile. `SyncAsync`chiama push e pull. Tuttavia, **ogni volta che si esegue un pull rispetto a una tabella in cui il cliente ha apportato modifiche a un push viene eseguito automaticamente**. Questo comportamento rimangono tutte le tabelle nell'archivio locale insieme relazioni coerenti. Questo comportamento potrebbe causare un push imprevisto.  Per ulteriori informazioni su questo comportamento, vedere [Offline sincronizzazione dei dati nell'App Mobile Azure].


##<a name="api-summary"></a>API di riepilogo

Per supportare le funzionalità non in linea di servizi di telefonia mobile, utilizzare l'interfaccia [IMobileServiceSyncTable] e inizializzato [MobileServiceClient.SyncContext] [ synccontext] con un database SQLite locale. Si è offline, le operazioni CRUD normale per le App per dispositivi mobili funzionano come se l'app è ancora connessi mentre si verificano le operazioni nell'archivio locale. Per sincronizzare l'archivio locale con il server vengono utilizzati i metodi seguenti:

*  **[PushAsync]** Poiché questo metodo è un membro del [IMobileServicesSyncContext], le modifiche apportate tutte le tabelle vengono inserite nel back-end. Solo i record con le modifiche locali vengono inviati al server.

* **[PullAsync] ** 
   un pull viene avviato da un [IMobileServiceSyncTable]. Quando sono presenti revisioni nella tabella, un push implicita viene eseguita per garantire che tutte le tabelle nell'archivio locale insieme relazioni coerenti. Il parametro *pushOtherTables* controlla se altre tabelle nel contesto vengono inseriti in un push impliciti. Il parametro di *query* accetta un [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   o stringa di query OData per filtrare i dati restituiti. Il parametro *queryId* viene utilizzato per definire sincronizzazione incrementale. Per ulteriori informazioni, vedere  [Offline sincronizzazione dei dati nell'App Mobile Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** L'app deve essere chiamata periodicamente questo metodo per eliminare i dati non aggiornati dall'archivio locale. Utilizzare il parametro *forzare* quando è necessario eliminare le modifiche che non sono ancora state sincronizzate.

Per ulteriori informazioni su questi concetti, vedere [Offline sincronizzazione dei dati nell'App Mobile Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Altre informazioni

Gli argomenti seguenti vengono fornite ulteriori informazioni sulla funzionalità di sincronizzazione offline dell'App Mobile:

* [Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]
* [PROCEDURA di SDK .NET Azure App per dispositivi mobili][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]: app-service-mobile-offline-data-sync.md
[creare un'app di windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
