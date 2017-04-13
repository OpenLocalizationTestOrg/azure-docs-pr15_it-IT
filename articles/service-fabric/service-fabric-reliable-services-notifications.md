<properties
   pageTitle="Notifiche di servizi affidabile | Microsoft Azure"
   description="Documentazione concettuale per le notifiche di servizio tessuti affidabile servizi"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Affidabile delle notifiche di servizi

Notifiche consentiranno ai client di tenere traccia delle modifiche apportate a un oggetto che si è interessati. Due tipi di oggetti supportano le notifiche: *Affidabile responsabile stato* e *Affidabile dizionario*.

Motivi comuni per l'uso di notifiche sono:

- Edificio materializzati visualizzazioni, ad esempio gli indici secondari o aggregato visualizzazioni filtrate dello stato della replica. Un esempio è un indice ordinato di tutte le chiavi nel dizionario affidabile.
- Invio monitoraggio di dati, ad esempio il numero di utenti aggiunti nell'ultima ora.

Le notifiche vengono attivate come parte dell'applicazione di operazioni. Per questo motivo, le notifiche devono essere gestite rapidamente eventi possibili e icona del non devono includere operazioni costose.

## <a name="reliable-state-manager-notifications"></a>Notifiche di gestione di stato affidabile

Responsabile stato affidabile vengono fornite notifiche per gli eventi seguenti:

- Transazione
    - Eseguire il commit
- Gestione dello stato
    - Rigenerare
    - Aggiunta di uno stato affidabile
    - Rimozione di uno stato affidabile

Responsabile stato affidabile tiene traccia delle transazioni inflight correnti. L'unica modifica nello stato di transazione che causa una notifica generare è un'operazione di eseguire il commit.

Responsabile stato affidabile mantiene un insieme di stati affidabili come dizionario affidabile e coda affidabile. Responsabile stato affidabile viene attivata notifiche quando questo insieme viene modificato: aggiunta o rimozione di uno stato affidabile o rigenerazione l'intera raccolta.
Raccolta affidabile Manager di stato viene ricostruita in tre casi:

- Ripristino: Avvio di una replica ripristinato lo stato precedente dal disco. Al termine del ripristino utilizza **NotifyStateManagerChangedEventArgs** per generare un evento che contiene l'insieme degli stati affidabili recuperati.
- Copia completa: prima di una replica può partecipare il set di configurazione, è necessario essere compilato. In alcuni casi, è necessario una copia completa dello stato del responsabile stato affidabile da replica primaria da applicare alla replica di secondario inattiva. Responsabile stato affidabile sulla replica secondaria utilizza **NotifyStateManagerChangedEventArgs** per generare un evento che contiene l'insieme degli stati affidabili che acquisito da replica primaria.
- Ripristino: Di emergenza, lo stato della replica può essere ripristinato da un backup tramite **RestoreAsync**. In tal caso, affidabile Manager di stato in replica primaria utilizza **NotifyStateManagerChangedEventArgs** per generare un evento che contiene l'insieme degli stati affidabili che ripristinato dal backup.

Per eseguire la registrazione per le notifiche di transazione e/o le notifiche di gestione di stato, è necessario eseguire la registrazione con gli eventi **TransactionChanged** o **StateManagerChanged** su affidabile Manager di stato. Per registrare con questi gestori viene costruttore del servizio informazioni sullo stato. Quando si registra sul costruttore, da non perdere alcuna notifica causati da una modifica la durata **dell'IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Gestore **TransactionChanged** utilizza **NotifyTransactionChangedEventArgs** per fornire dettagli sull'evento. Contiene la proprietà azione (ad esempio **NotifyTransactionChangedAction.Commit**) che specifica il tipo di modifica. Contiene anche la proprietà della transazione che fornisce un riferimento alla transazione modificato.

>[AZURE.NOTE] Oggi, **TransactionChanged** gli eventi solo se la transazione viene eseguito il commit. L'azione è pari al **NotifyTransactionChangedAction.Commit**. Ma in futuro, eventi potrebbero essere generati per altri tipi di modifiche apportate allo stato di transazione. È consigliabile verificare l'azione ed elaborazione dell'evento solo se si tratta di uno che si sta aspettando.

Di seguito è riportato un gestore eventi **TransactionChanged** di esempio.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Gestore **StateManagerChanged** utilizza **NotifyStateManagerChangedEventArgs** per fornire dettagli sull'evento.
**NotifyStateManagerChangedEventArgs** ha due sottoclassi: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Utilizzare la proprietà di azione in **NotifyStateManagerChangedEventArgs** di eseguirne il cast **NotifyStateManagerChangedEventArgs** alla sottoclasse corretta:

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Di seguito è riportato un gestore di notifica **StateManagerChanged** di esempio.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notifiche di dizionario affidabile

Dizionario affidabile vengono fornite notifiche per gli eventi seguenti:

- Ricostruisci: Chiamato quando **ReliableDictionary** è stato ripristinato il proprio stato in un backup o recuperato o copiato stato locale.
- Cancella: Chiamato quando lo stato di **ReliableDictionary** è stato cancellato tramite il metodo **ClearAsync** .
- Aggiungere: Chiamato quando un elemento è stato aggiunto **ReliableDictionary**.
- Aggiornamento: Chiamato quando un elemento in **IReliableDictionary** è stato aggiornato.
- Rimuovi: Chiamato quando è stato eliminato un elemento in **IReliableDictionary** .

Per ottenere notifiche dizionario affidabile, è necessario eseguire la registrazione con il gestore eventi **DictionaryChanged** sul **IReliableDictionary**. Per registrare con questi gestori viene in **ReliableStateManager.StateManagerChanged** aggiungere la notifica.
La registrazione quando **IReliableDictionary** viene aggiunto a **IReliableStateManager** assicura che non perdere le notifiche.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** è il metodo di esempio che nell'esempio precedente **OnStateManagerChangedHandler** chiama.

Il codice precedente imposta l'interfaccia **IReliableNotificationAsyncCallback** , insieme a **DictionaryChanged**. Poiché **NotifyDictionaryRebuildEventArgs** contiene un'interfaccia **IAsyncEnumerable** - le esigenze aziendali per enumerati asincrono - ricostruire notifiche generato tramite **RebuildNotificationAsyncCallback** anziché **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Nel codice precedente, come parte dell'elaborazione notifica ricostruzione prima di tutto lo stato di aggregato gestito sia deselezionato. Perché è necessario ricreare la raccolta affidabile con un nuovo stato, tutte le notifiche precedente sono rilevanti.

Gestore **DictionaryChanged** utilizza **NotifyDictionaryChangedEventArgs** per fornire dettagli sull'evento.
**NotifyDictionaryChangedEventArgs** include cinque sottoclassi. Utilizzare la proprietà di azione in **NotifyDictionaryChangedEventArgs** di eseguirne il cast **NotifyDictionaryChangedEventArgs** alla sottoclasse corretta:

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Consigli

- *Eseguire* il completamento di eventi di notifica il più presto.
- *Non* eseguire alcuna operazione costose (ad esempio operazioni dei / o) come parte dell'icona del eventi.
- *Eseguire* selezionare il tipo di azione prima di elaborare l'evento. Nuovi tipi di azione possono essere aggiunti in futuro.

Ecco alcuni aspetti da tenere presenti:

- Le notifiche vengono attivate durante l'esecuzione di un'operazione. Ad esempio una notifica di ripristino viene generata come ultimo passaggio di un'operazione di ripristino. Il ripristino non verrà completata fino a quando non viene elaborato l'evento di notifica.
- Poiché le notifiche vengono attivate come parte delle operazioni di applicazione, client vedono solo le notifiche per le operazioni approvate in locale. E perché operazioni sono garantite solo a livello locale commit (in altre parole, collegati), potrebbe o potrebbe non essere in seguito annullate.
- Sul percorso Ripristina una singola notifica è attivata per ogni operazione applicato. Questo errore indica che se transazione T1 include Create(X), Delete(X) e Create(X), si riceverà una notifica per la creazione di X, uno per l'eliminazione e uno per la creazione di nuovo in tale ordine.
- Per le transazioni che contengono più operazioni, vengono applicate le operazioni nell'ordine in cui sono state ricevute sulla replica principale dall'utente.
- Come parte dell'elaborazione lo stato di avanzamento falsa, alcune operazioni possono essere annullate. Le notifiche vengono generate per tali operazioni Annulla ripristino lo stato della replica di un punto stabile. Una differenza importante delle notifiche di annullamento è che gli eventi che hanno chiavi duplicate vengono aggregati. Ad esempio, se transazione T1 è da annullare, si noterà una singola notifica a Delete(X).

## <a name="next-steps"></a>Passaggi successivi

- [Raccolte affidabile](service-fabric-work-with-reliable-collections.md)
- [Guida introduttiva a servizi affidabile](service-fabric-reliable-services-quick-start.md)
- [Servizi affidabili di backup e ripristino (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
- [Guida di riferimento per sviluppatori per raccolte affidabile](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
