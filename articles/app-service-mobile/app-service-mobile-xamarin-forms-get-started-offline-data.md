<properties
    pageTitle="Attivare sincronizzazione offline per l'App Mobile Azure (Xamarin.Forms) | Microsoft Azure"
    description="Informazioni su come utilizzare App servizio Mobile App per cache e sincronizzazione dei dati non in linea nell'applicazione Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Attivare la sincronizzazione offline per le app per dispositivi mobili Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione introduce la caratteristica di sincronizzazione offline dell'App Mobile Azure per Xamarin.Forms. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili - visualizzazione, aggiunta o la modifica dei dati, anche quando non vi è alcuna connessione di rete. Le modifiche vengono memorizzate in un database locale. Quando il dispositivo è in linea, queste modifiche vengono sincronizzate con il servizio remoto.

In questa esercitazione si basa sulla soluzione Xamarin.Forms Guida introduttiva per le App per dispositivi mobili che si crea una volta completata l'esercitazione [Crea un'app per iOS Xamarin]. La soluzione Guida introduttiva per Xamarin.Forms contiene il codice per supportare sincronizzazione offline che solo deve essere abilitata. In questa esercitazione si aggiorna la soluzione Guida introduttiva per attivare le caratteristiche non in linea di App Mobile Azure. È anche possibile evidenziare il codice di non in linea specifica nell'app. Se non si utilizza la soluzione scaricato Guida introduttiva, è necessario aggiungere i pacchetti di estensione accesso dati al progetto. Per ulteriori informazioni sui pacchetti di estensione server, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure][1].

Per ulteriori informazioni sulla caratteristica di sincronizzazione offline, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Attivare la funzionalità di sincronizzazione offline della soluzione di Guida introduttiva

Il codice di sincronizzazione offline è incluso nel progetto tramite preprocessore c#. Quando il **OFFLINE\_sincronizzazione\_abilitata** simbolo è definito, questi percorsi di codice sono inclusi nella build. Per le applicazioni di Windows, è necessario installare anche la piattaforma SQLite.

1. In Visual Studio, fare doppio clic la soluzione > **Gestisci pacchetti NuGet per soluzione...**, quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** per tutti i progetti della soluzione.

2. In Esplora soluzioni aprire il file TodoItemManager.cs dal progetto con **Portable** il nome, ossia progetto libreria di classi portatile, quindi rimuovere il commento per la preprocessore seguente:

        #define OFFLINE_SYNC_ENABLED

4. (Facoltativo) Per supportare dispositivi Windows, installare uno dei pacchetti di runtime SQLite seguenti:

    * **Runtime Windows 8.1:** Installare [SQLite per Windows 8.1][3].
    * **Windows Phone 8.1:** Installare [SQLite per Windows Phone 8.1][4].
    * **Piattaforma Windows universale** Installare [SQLite per Universal Windows universale][5].

    Sebbene non sia presente un progetto Windows universale la Guida introduttiva, la piattaforma Windows universale è supportata con i moduli di Xamarin.

5. (Facoltativo) In ogni progetto app di Windows, fare doppio clic **riferimenti** > **Aggiungi riferimento**, espandere la cartella di **Windows** > **estensioni**.
    Abilitare appropriato **SQLite per Windows** SDK insieme **Visual C++ 2013 Runtime per Windows** SDK.
    I nomi SQLite SDK leggermente con ogni piattaforma Windows.

## <a name="review-the-client-sync-code"></a>Esaminare il codice di sincronizzazione client

Ecco una breve panoramica delle informazioni è già incluso nel codice di esercitazioni all'interno di `#if OFFLINE_SYNC_ENABLED` direttive. La funzionalità di sincronizzazione offline è nel file di progetto TodoItemManager.cs nel progetto libreria di classi portabile. Per una panoramica della caratteristica, vedere [Offline sincronizzazione dei dati nell'App Mobile Azure][2].

* Prima di possono eseguire le operazioni di tabella, è necessario inizializzare l'archivio locale. Il database di archivio locale inizializzato nel costruttore della classe **TodoItemManager** utilizzando il codice seguente:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Questo codice crea un nuovo database locale SQLite utilizzando la classe **MobileServiceSQLiteStore** .

    Il metodo **DefineTable** crea una tabella nell'archivio locale corrispondente ai campi di tipo specificato.  Il tipo non è necessario includere tutte le colonne presenti nel database remoto. È possibile archiviare un sottoinsieme di colonne.

* Nel campo **todoTable** **TodoItemManager** è un tipo di **IMobileServiceSyncTable** anziché **IMobileServiceTable**. La classe utilizza il database locale per tutti di creare, leggere, Aggiorna ed eliminazione (CRUD) tabella. È possibile decidere se queste modifiche vengono inviate a back-end App Mobile chiamando **PushAsync** su **IMobileServiceSyncContext**. Il contesto di sincronizzazione è possibile mantenere le relazioni tra tabelle, verifica e inserendo le modifiche in tutte le tabelle che un'applicazione client modificato quando si chiama **PushAsync** .

    Comando si chiama il metodo **SyncAsync** seguente per eseguire la sincronizzazione con back-end App Mobile:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    In questo esempio viene usata semplici gestione degli errori con il gestore di sincronizzazione predefinito. Un'applicazione reale necessario gestire i diversi errori come condizioni di rete e server è in conflitto con un'implementazione **IMobileServiceSyncHandler** personalizzata.

## <a name="offline-sync-considerations"></a>Considerazioni sulla sincronizzazione offline

Nell'esempio, il metodo **SyncAsync** è chiamato solo all'avvio e quando viene richiesta una sincronizzazione.  Per avviare una sincronizzazione in un'app di Android o iOS, estrarre verso il basso nell'elenco di elementi. per Windows, utilizzare il pulsante **Sincronizza** . In un'applicazione reale, è possibile creare i trigger di sincronizzazione quando lo stato della rete.

Se un pull viene eseguito in una tabella in cui è in sospeso aggiornamenti locali verrà registrata in base al contesto che recuperano operazione automaticamente trigger un push contesto precedente. Quando l'aggiornamento, aggiunta e il completamento di elementi in questo esempio, è possibile omettere la chiamata **PushAsync** esplicita.

Nel codice fornito, tutti i record nella tabella TodoItem remota vengono eseguite query, ma è anche possibile filtrare i record passando un id di query e query alla **PushAsync**. Per ulteriori informazioni, vedere la sezione *Sincronizzazione incrementale* in [Modalità Offline sincronizzazione dei dati nell'App Mobile Azure][2].

## <a name="run-the-client-app"></a>Eseguire l'applicazione client

Dopo aver attivato sincronizzazione offline, eseguire l'applicazione client almeno una volta a seconda della piattaforma per popolare il database di archivio locale. In un secondo momento, simulare uno scenario offline e modificare i dati nell'archivio locale, mentre l'app è offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aggiornare il comportamento di sincronizzazione dell'applicazione client

In questa sezione, modificare il progetto di client per simulare uno scenario offline utilizzando un URL dell'applicazione non valida per il back-end. In alternativa, è possibile disattivare le connessioni di rete spostando il dispositivo "Modalità aereo".  Quando si aggiungono o modificare elementi di dati, queste modifiche sono conservate nell'archivio locale, ma non sincronizzate con l'archivio di dati back-end finché non viene ripristinata la connessione.

1. In Esplora soluzioni aprire il file di progetto Constants.cs dal progetto **portatile** e impostare il valore di `ApplicationURL` in modo che puntino a un URL non valido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Aprire il file TodoItemManager.cs dal progetto **portatile** , quindi Aggiungi **intercettare** per la classe di **eccezione** base per il blocco **try... catture** in **SyncAsync**. Questo blocco **intercettare** scrive il messaggio dell'eccezione nella console, come indicato di seguito:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Creare ed eseguire l'applicazione client.  Aggiungere alcuni elementi di nuovi. Si noti che ha eseguito l'accesso di un'eccezione nella console per ogni tentativo di sincronizzazione con il back-end. Questi nuovi elementi sono presenti solo nell'archivio locale fino a quando non può essere inseriti nel back-end per dispositivi mobili. L'applicazione client si comporta come se è connesso al back-end, supporto che tutte creazione, lettura, aggiornamento, le operazioni di eliminazione (CRUD).

4. Chiudere l'applicazione e riavviarla per verificare che i nuovi elementi creati vengono mantenuti all'archivio locale.

5. (Facoltativo) Utilizzare Visual Studio per visualizzare la tabella di Database SQL Azure per verificare che i dati nel database back-end non è sono modificato.

    In Visual Studio, aprire **Esplora Server**. Passare al database in **Azure**->**Database SQL**. Il pulsante destro del database e selezionare **Apri in Esplora oggetti di SQL Server**. A questo punto è possibile passare a tabella di database SQL e il relativo contenuto.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aggiornare l'app di client per riconnettersi il back-end per dispositivi mobili

In questa sezione, riconnettere l'app alla back-end per dispositivi mobili simulare app fedeli a uno stato online. Quando si esegue l'azione di aggiornamento, dati sono sincronizzati con i dispositivi mobili back-end.

1. Riaprire Constants.cs. Correggere la `applicationURL` in modo che puntino all'URL corretto.

2. Rigenerare ed eseguire l'applicazione client. L'app tenta di sincronizzare con back-end app per dispositivi mobili dopo l'avvio. Verificare che si è connessi senza eccezioni nella console di debug.

3. (Facoltativo) Visualizzare i dati aggiornati tramite Esplora oggetti di SQL Server o uno strumento resto come Fiddler o [Postman][6]. Si noti che i dati sono stati sincronizzati tra il database back-end e l'archivio locale.

    Si noti i dati sono stati sincronizzati tra il database e l'archivio locale e contenente gli elementi che aggiunti durante l'app è stata interrotta.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure][2]
* [PROCEDURA di SDK .NET Azure App per dispositivi mobili][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md