<properties
    pageTitle="Utilizzo di raccolte affidabile | Microsoft Azure"
    description="Procedure consigliate per l'utilizzo di raccolte affidabile."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Utilizzo di raccolte affidabile

Servizio tessuti offre un modello di programmazione informazioni sullo stato disponibile per gli sviluppatori di .NET tramite raccolte affidabile. In particolare, tessuti servizio fornisce dizionario affidabile e classi coda affidabile. Quando si utilizzano le classi, lo stato è suddiviso (per scalabilità), replicato (disponibilità) e transazione all'interno di una partizione (per semantica ACID). Di seguito esaminare un tipico utilizzo di un oggetto dizionario affidabile e osservare quali relativi effetti.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Tutte le operazioni sugli oggetti dizionario affidabile (ad eccezione di ClearAsync che non può essere annullata), è necessario un oggetto ITransaction. L'oggetto è associato uno e tutte le modifiche che si sta tentando di qualsiasi dizionario affidabile e/o affidabile accodare oggetti all'interno di una singola partizione. Acquisite un ITransaction oggetto chiamando la partizione del metodo CreateTransaction del StateManager.
 
Nel codice, l'oggetto ITransaction viene passato al metodo AddAsync del dizionario affidabile. Metodi di dizionario che accetta una chiave internamente, richiedere un blocco di lettura/scrittura associato alla chiave. Se il metodo consente di modificare il valore della chiave, il metodo ha un blocco di scrittura alla chiave e se il metodo legge solo dal valore della chiave, un blocco di lettura viene accettato per la chiave. Poiché AddAsync modifica il valore della chiave per il nuovo valore passato, viene accettata blocco di scrittura della chiave. Sì, se thread 2 (o più) tenta di aggiungere i valori con la stessa chiave contemporaneamente, un thread acquisirà il blocco di scrittura e altri thread si bloccano. Per impostazione predefinita, blocco di metodi per un massimo di quattro secondi per acquisire il blocco; dopo 4 secondi, i metodi di generano un TimeoutException. Overload esistono che consente di passare un valore di timeout espliciti se si preferisce.
 
In genere, scrivere il codice per rispondere a un TimeoutException intercettazione e ritentare l'intera operazione (come illustrato nel codice precedente). Il codice semplice, che si sta chiamando appena Task.Delay passando ogni volta che 100 millisecondi. Ma in realtà, potrebbe essere preferibile usando un tipo di ritardo esponenziale back-off risorse.
 
Una volta acquisito il blocco, AddAsync aggiunge riferimenti a un dizionario temporaneo interno associato all'oggetto ITransaction oggetto chiave e il valore. In questo modo per fornire la semantica di lettura-your-proprietari-scrittura. Vale a dire dopo la chiamata AddAsync, una chiamata successiva al TryGetValueAsync (con lo stesso oggetto ITransaction) restituirà il valore anche se non si è ancora commit transazione. Successivamente, AddAsync serializza la chiave e valore gli oggetti in matrici di byte e aggiunge queste matrici byte a un file di log sul nodo locale. Infine, AddAsync invia le matrici di byte per ogni la replica secondario in modo che le stesse informazioni chiave/valore. Anche se le informazioni chiave/valore sono stato scritto in un file di log, le informazioni non è considerate parte del dizionario finché non è stato eseguito il commit della transazione sono associate. 

Nel codice, la chiamata a CommitAsync esegue il commit di tutte le operazioni di transazioni. In particolare, aggiunge confermano le informazioni contenute nel file di log sul nodo locale e invia anche il record di commit a ogni la replica secondario. Dopo una base (maggior parte) replica ha risposto, tutte le modifiche di dati sono considerate permanente e tutti i blocchi associati a tasti che sono stati modificati tramite l'oggetto ITransaction vengono rilasciati in modo che altri thread/transazioni è possibile modificare gli stessi tasti e i rispettivi valori.

Se CommitAsync non è chiamato (in genere a causa di un'eccezione), l'oggetto ITransaction viene eliminato. Quando eliminando un oggetto ITransaction Commit, tessuti servizio aggiunge interruzione informazioni nel file di log del nodo locale e non è necessario essere inviate a ogni replica secondario. E quindi, in cui vengono rilasciati blocchi associati a tasti che sono stati modificati tramite la transazione.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Problemi comuni e come evitarli
Dopo avere appreso funzionano internamente raccolte affidabile, esaminiamo un alcuni abusi comuni di esse. Visualizzare il codice riportato di seguito:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Quando si utilizza un dizionario di .NET regolare, è possibile aggiungere un chiave/valore nel dizionario e quindi modificare il valore di una proprietà (ad esempio LastLogin). Tuttavia, questo codice non funzionerà correttamente con un dizionario affidabile. Tenere presente da discussione precedenti, la chiamata a AddAsync serializza gli oggetti chiave/valore in matrici di byte e quindi Salva le matrici in un file locale e invia anche alle repliche secondarie. Se in un secondo momento si modifica una proprietà, questa operazione modificherà il valore della proprietà in memoria di sola lettura. non influisce il file locale o i dati inviati alle repliche. Se il processo si blocca, novità in memoria viene eliminata. Quando si avvia un nuovo processo o un'altra replica diventa primaria, il valore di proprietà precedente è disponibili. 

Impossibile sottolineare sufficientemente quanto sia semplice per verificare il tipo di errore visualizzato sopra. E, per solo informazioni sull'errore se/quando che il processo è inattivo. Il modo corretto per scrivere il codice è semplicemente per invertire le due righe:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Ecco un altro esempio che mostra un errore comune:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Fare di nuovo mediante i dizionari di .NET normali, il codice riportato sopra funziona correttamente ed è un modello comune: lo sviluppatore utilizza una chiave per cercare un valore. Se il valore è presente, lo sviluppatore modifica un valore di proprietà. Tuttavia, con le raccolte affidabile, questo codice presenta lo stesso problema come già discusso: __non è necessario modificare un oggetto dopo avere assegnato a una raccolta affidabile.__
 
La modalità di aggiornamento di un valore in una raccolta affidabile corretta è per ottenere un riferimento al valore esistente e prendere in considerazione l'oggetto a cui fa riferimento il riferimento non modificabile. Creare un nuovo oggetto che una copia esatta dell'oggetto originale. A questo punto, è possibile modificare lo stato di questo nuovo oggetto e scrivere il nuovo oggetto nell'insieme, in modo che ottiene serializzato in matrici di byte aggiunto al file locale e inviate alle repliche. Dopo aver confermare le modifiche, gli oggetti in memoria, il file locale e ogni la replica disponibile lo stesso stato esatto. È buona!

Il codice riportato di seguito mostra la modalità di aggiornamento di un valore in una raccolta affidabile corretta:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definire i tipi di dati non modificabili per impedire un errore del programmatore

Se possibile, si preferisce al compilatore di segnalazione degli errori quando si produce accidentalmente codice che modifica stesso mano dello stato di un oggetto che si deve per considerare non modificabile. Tuttavia, il compilatore c# non ha la possibilità di eseguire questa operazione. Pertanto, per evitare potenziali bug programmatore, si consiglia di definire i tipi di utilizzare con le raccolte affidabile come tipi non modificabili. In particolare, significa che continuare a tipi di valore di base (ad esempio numeri [Int32, UInt64 e così via], DateTime, Guid, TimeSpan e così via). E, naturalmente, è inoltre possibile utilizzare stringa. È consigliabile evitare di proprietà di una raccolta come serializzazione e la relativa deserializzazione possibile domande possono influire negativamente sulle prestazioni. Tuttavia, se si desidera utilizzare proprietà di una raccolta, è consigliabile l'utilizzo di. Libreria di raccolte non modificabile della rete (System.Collections.Immutable). Questa raccolta è disponibile per il download da http://nuget.org. È anche consigliabile chiusura le classi e creazione di campi di sola lettura ogni volta che è possibile.

Il tipo di informazioni utente riportata di seguito viene illustrato come definire un tipo non modificabile a trarre vantaggio da consigli sopra.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

Il tipo di ItemId è anche un tipo non modificabile, come illustrato di seguito:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Controllo delle versioni schema (aggiornamenti)

Internamente, raccolte affidabile serializzare gli oggetti utilizzando. DataContractSerializer della rete. Oggetti serializzati vengono mantenuti sul disco locale della replica principale e anche trasmesse alle repliche secondarie. Come mano che matura del servizio, è probabile che sarà necessario modificare il tipo di dati (schema) richiede il servizio. È necessario avvicinarsi il controllo delle versioni dei dati con molta cautela. Prima di tutto, è necessario essere sempre possibile deserializzare dati precedenti. In particolare, significa che il codice di deserializzazione deve essere compatibile all'infinito: 333 versione del codice servizio deve essere in grado di operare sui dati inseriti in un insieme affidabile dalla versione 1 del codice servizio 5 anni.

Inoltre, codice servizio è aggiornato un dominio di aggiornamento contemporaneamente. Affermativo, durante l'aggiornamento, si dispongono di due versioni diverse di codice del servizio in esecuzione contemporaneamente. È necessario evitare la nuova versione del codice servizio utilizzare il nuovo schema come le versioni precedenti del codice del servizio potrebbero non essere possibile gestire il nuovo schema. Se possibile, è necessario progettare ogni versione del servizio per essere compatibile con le versioni con 1 versione. In particolare, significa che V1 del codice servizio deve essere possibile ignorare semplicemente elementi schema che non gestisce in modo esplicito. Tuttavia, deve essere possibile salvare i dati che non in modo esplicito riconosce e semplicemente riscriverli durante l'aggiornamento di una chiave del dizionario o un valore. 

>[AZURE.WARNING] Mentre è possibile modificare lo schema di una chiave, è necessario assicurarsi che siano stabili codice hash della chiave e degli algoritmi uguale a. Se si modifica uno di questi algoritmi funzionamento, non sarà possibile cercare la chiave all'interno del dizionario affidabile mai nuovamente.
  
In alternativa, è possibile eseguire vengono in genere indicati come un aggiornamento fase 2. Con un aggiornamento fase 2, l'aggiornamento del servizio da V1 a V2: V2 contiene il codice in grado di gestire la modifica dello schema nuovo ma non esegue questo codice. Quando il codice V2 legge i dati V1, opera su di esso e scrive dati V1. Quindi, al termine dell'aggiornamento tutti aggiornamento domini, è possibile qualche segnalare per le istanze in esecuzione V2 che l'aggiornamento è stato completato. (Un modo per segnale si tratta di implementare un aggiornamento di configurazione, si tratta di ciò che crea un aggiornamento fase 2) A questo punto, le istanze di V2 possano leggere i dati V1, convertirla in dati V2, operare su di esso e scrivere come dati V2. Quando altre istanze leggere i dati V2, non è necessario convertirla, è sufficiente operare su di esso e scrivere dati V2.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla creazione contratti inoltrare dati compatibili, vedere [I contratti di dati compatibili](https://msdn.microsoft.com/library/ms731083.aspx).

Per altre procedure consigliate per i contratti di dati il controllo delle versioni, vedere [Il controllo delle versioni contratto dati](https://msdn.microsoft.com/library/ms731138.aspx). 

Per informazioni su come implementare contratti dati tolleranza di versione, vedere [Callback serializzazione indipendente dalla versione](https://msdn.microsoft.com/library/ms733734.aspx). 

Per informazioni su come creare una struttura di dati che può interagire tra più versioni, vedere [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
