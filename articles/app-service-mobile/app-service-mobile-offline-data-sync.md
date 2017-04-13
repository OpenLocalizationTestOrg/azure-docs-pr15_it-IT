<properties
    pageTitle="Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure | Microsoft Azure"
    description="Panoramica della funzionalità di sincronizzazione di dati non in linea per App Mobile Azure e Guida di riferimento concettuale"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure

## <a name="what-is-offline-data-sync"></a>Che cos'è sincronizzazione dati offline?

La sincronizzazione dei dati non in linea è un client e server caratteristica SDK dell'App Mobile Azure che semplifica l'individuazione di agli sviluppatori di creare App che funzionano senza una connessione di rete.

Quando l'applicazione è in modalità offline, che possono comunque creare e modificare i dati verranno salvati in un archivio locale. Quando l'app viene eseguita in modalità online, è possibile sincronizzare le modifiche locali con il back-end App Mobile Azure. La caratteristica include anche il supporto per il rilevamento dei conflitti quando viene modificato il record stesso sul client e back-end. Conflitti quindi è possibile gestire nel server o il client.

Sincronizzazione offline è numerosi vantaggi:

* Migliorare la velocità di risposta di app per la memorizzazione nella cache dati server in locale nel dispositivo
* Creazione di applicazioni affidabili che rimangono utile quando sono presenti problemi di rete
* Consentire agli utenti finali di creare e modificare i dati anche quando non è possibile accedere di rete, il supporto di scenari con connettività senza alcuna
* Sincronizzare i dati in più dispositivi e rilevare i conflitti quando lo stesso record viene modificato da due dispositivi
* Limitare l'uso di rete sulle reti a consumo o latenza elevata

Le esercitazioni seguenti illustrano come aggiungere sincronizzazione offline per i client mobili tramite app Mobile Azure:

* [Android: Attivare sincronizzazione offline]
* [iOS: attivare sincronizzazione offline]
* [IOS Xamarin: attivare sincronizzazione offline]
* [Xamarin Android: Attivare sincronizzazione offline]
* [Xamarin.Forms: Sincronizzazione offline Abilita](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Piattaforma Windows universale: Attivare sincronizzazione offline]

## <a name="what-is-a-sync-table"></a>Che cos'è una tabella di sincronizzazione?

Per accedere all'endpoint "/ tabelle", il client Mobile di Azure SDK forniscono interfacce ad esempio `IMobileServiceTable` (client .NET SDK) o `MSTable` (iOS client). Queste API connettono direttamente ai back-end App Mobile Azure e avrà esito negativo se il dispositivo client non ha una connessione di rete.

Per supportare l'utilizzo fuori rete, l'app deve utilizzare la *tabella di sincronizzazione* API, ad esempio `IMobileServiceSyncTable` (client .NET SDK) o `MSSyncTable` (iOS client). Tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) funzionano con tabella sincronizzazione API, ma ora di leggere o scrivere in un *archivio locale*. Prima di possono eseguire le operazioni di tabella di sincronizzazione, è necessario inizializzare l'archivio locale.

## <a name="what-is-a-local-store"></a>Che cos'è un archivio locale?

Un archivio locale è il livello di persistenza dati nel dispositivo client. Gli SDK client App Mobile Azure forniscono un'implementazione di archivio locale predefinito. In Windows, Xamarin e Android, si basa su SQLite; in iOS, si basa sui dati di base.

Per utilizzare l'implementazione basata su SQLite su Windows Phone o Windows Store 8.1, è necessario installare un'estensione SQLite. Per ulteriori informazioni, vedere [universale piattaforma Windows: attivare sincronizzazione offline]. Android e iOS forniti con una versione di SQLite nel sistema operativo del dispositivo, pertanto non è necessario fare riferimento a una versione di SQLite.

Gli sviluppatori possono inoltre implementare un proprio archivio locale. Ad esempio, se si desidera memorizzare i dati in un formato crittografato nel client mobile, è possibile definire un archivio locale che utilizza SQLCipher per la crittografia.

## <a name="what-is-a-sync-context"></a>Che cos'è un contesto di sincronizzazione?

Un *contesto di sincronizzazione* è associata a un oggetto client mobile (ad esempio `IMobileServiceClient` o `MSClient`) e tiene traccia delle modifiche apportate con le tabelle di sincronizzazione. Il contesto di sincronizzazione mantiene una *coda di operazioni* in modo da mantenere un elenco ordinato di operazioni CUD (creazione, aggiornamento, eliminazione) in un secondo momento essere inviati al server.

Un archivio locale è associato il contesto di sincronizzazione con un metodo di inizializzazione come `IMobileServicesSyncContext.InitializeAsync(localstore)` nel [client di .NET SDK].

## <a name="how-sync-works"></a>Modalità offline sincronizzazione funziona

Quando si usano le tabelle di sincronizzazione, codice client controlla quando le modifiche locali verranno sincronizzate con un back-end App Mobile Azure. Non viene inviato back-end fino a quando non c'è una chiamata a modifiche locali *push* . Analogamente, l'archivio locale viene popolata con i nuovi dati solo quando c'è una chiamata per *estrarre* i dati.

* **Push**: Push è un'operazione nel contesto di sincronizzazione e invia tutte le revisioni CUD dall'ultimo push. Si noti che non è possibile inviare solo le modifiche di singoli della tabella, poiché in caso contrario operazioni possono essere inviate nell'ordine corretto. Push esegue una serie di chiamate resto per il back-end App Mobile Azure, che a sua modificherà il database server.

* **Estrarre**: Pull eseguita per ogni tabella e può essere personalizzato con una query per recuperare solo un sottoinsieme dei dati del server. Gli SDK client Mobile di Azure quindi inseriscono i dati risultanti archivio locale.

* **Inserisce implicito**: se un pull viene eseguito su una tabella in cui è in sospeso aggiornamenti locali, il pull eseguirà innanzitutto un push nel contesto di sincronizzazione. Consente di ridurre al minimo i conflitti tra modifiche già accodate e nuovi dati dal server.

* **Sincronizzazione incrementale**: il primo parametro per il funzionamento di pull è un *nome di query* che viene utilizzato solo nel client. Se si usa un nome di query non null, Azure Mobile SDK verrà eseguita automaticamente una *sincronizzazione incrementale*.
  Ogni volta che un'operazione pull restituisce un set di risultati, la versione più aggiornata `updatedAt` archiviare nelle tabelle di sistema locale SDK timestamp tale set di risultati. Operazioni successive pull recupera soltanto i record dopo tale timestamp.

  Per utilizzare la sincronizzazione incrementale, il server deve restituire significativo `updatedAt` i valori e anche deve supportare l'ordinamento in base a questo campo. Tuttavia, poiché SDK aggiunge il proprio ordinamento in base al campo updatedAt, è possibile utilizzare una query di pull che dispone di un `$orderBy$` clausola.

  Il nome della query può essere qualsiasi stringa che scelto, ma deve essere univoco per ogni query logico nell'app.
  In caso contrario, pull diversi Impossibile sovrascrivono lo stesso timestamp sincronizzazione incrementale e le query possono restituire i risultati non corretti.

  Se la query include un parametro, per creare un nome univoco query è possibile incorporare il valore del parametro.
  Ad esempio, se si sta filtrando ID utente, il nome della query potrebbe essere come indicato di seguito (in c#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se si desidera rifiutare esplicitazione sincronizzazione incrementale, passare `null` come l'ID di query. In questo caso, verranno recuperati tutti i record su tutte le chiamate a `PullAsync`, potenzialmente eccessiva.

* **Purging**: È possibile cancellare il contenuto dell'archivio locale utilizzando `IMobileServiceSyncTable.PurgeAsync`.
  Potrebbe essere necessario se si dispone di dati non aggiornati nel database client o se si desidera annullare tutte le modifiche in sospeso.

  Cancellazione eliminerà una tabella dall'archivio locale. Se esistono operazioni in sospeso la sincronizzazione con il database di server, l'eliminazione verrà generata un'eccezione a meno che non è impostato il parametro *forza Elimina* .

  Ad esempio di dati obsoleti sul client, se nell'esempio "elenco" periferica 1 recupera solo gli elementi che non sono stati completati. Quindi, un todoitem "Acquistare latte" è completata nel server da un altro dispositivo. Tuttavia, periferica 1 avranno ancora todoitem "Acquista latte" nell'archivio locale in quanto sono solo il pull e gli elementi che non vengono contrassegnati come completati. Cancellazione eliminerà questo elemento non aggiornato.

## <a name="next-steps"></a>Passaggi successivi

* [iOS: attivare sincronizzazione offline]
* [IOS Xamarin: attivare sincronizzazione offline]
* [Xamarin Android: Attivare sincronizzazione offline]
* [Piattaforma Windows universale: Attivare sincronizzazione offline]

<!-- Links -->
[Client di .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Attivare sincronizzazione offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: attivare sincronizzazione offline]: app-service-mobile-ios-get-started-offline-data.md
[IOS Xamarin: attivare sincronizzazione offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Attivare sincronizzazione offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Piattaforma Windows universale: Attivare sincronizzazione offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
