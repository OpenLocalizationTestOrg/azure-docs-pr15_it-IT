<properties 
    pageTitle="Come implementare client lato partizioni con gli SDK | Microsoft Azure" 
    description="Informazioni su come usare gli SDK DocumentDB Azure alle richieste di dati e la distribuzione partizione (condiviso) in più raccolte" 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>Come dati partizione con supporto lato client in DocumentDB

Azure DocumentDB supporta [automatica partizione delle raccolte](documentdb-partition-data.md). Tuttavia, esistono casi di utilizzo in cui è utile mantenere un controllo granulare su partizioni comportamento. Per ridurre il codice di stampa o editoriale necessario per partizioni attività, sono stati aggiunti funzionalità in .NET, Node e SDK linguaggio che facilita la creazione di applicazioni scalabilità in più raccolte.

In questo articolo è necessario esaminare le classi e le interfacce .NET SDK e come è possibile utilizzare per lo sviluppo di applicazioni partizionate. Altri SDK come linguaggio, Node e Python supporto metodi e interfacce simili per partizioni lato client.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>Sul lato client partizioni con SDK DocumentDB

Prima di analizzare più approfondita le partizioni, si riepilogo alcuni concetti di base DocumentDB correlate alle partizioni. Ogni DocumentDB Azure database account costituito da un set di database, ognuno contenente più raccolte, ognuno dei quali può contenere stored procedure, trigger, funzioni definite dall'utente, documenti e allegati correlati. Raccolte possono essere singola partizione o partizionata se stessi e le proprietà seguenti:

- Raccolte offrono isolamento di prestazioni. Pertanto non esiste un miglioramento delle prestazioni in confronto di documenti simili nella stessa raccolta. Ad esempio, per i dati di serie ora, è consigliabile inserire i dati per l'ultimo mese, spesso a query, all'interno di una raccolta con maggiore produttività provisioning mentre meno recenti viene inserito all'interno delle raccolte con bassa velocità effettiva di provisioning.
- Transazioni ACID ovvero stored procedure e trigger non è possibile intervallo di una raccolta. Le transazioni sono limitate all'interno di un valore di chiave partizione singolo all'interno di una raccolta.
- Raccolte non si applicano uno schema, in modo che possono essere utilizzati per i documenti JSON dello stesso tipo o tipi diversi.

A partire dalla versione [1.5 SDK DocumentDB Azure](documentdb-sdk-dotnet.md), è possibile eseguire le operazioni di documento direttamente in un database. [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) utilizza internamente PartitionResolver specificate dall'utente per il database per indirizzare le richieste all'insieme appropriato.

>[AZURE.NOTE] [Sul lato server partizione](documentdb-partition-data.md) introdotti 1.6.0+ resto API 2015-12-16 e SDK condanna l'approccio del Risolutore partizione lato client per casi di semplice utilizzo. Sul lato client partizione tuttavia più flessibile e consente di controllare isolamento prestazioni tra tasti partizione, controllare grado di parallelismo durante la lettura di risultati da più partizioni e utilizzare intervallo/spaziale partizione approcci e hash.

Ad esempio, in .NET, ogni classe PartitionResolver è un'implementazione concreta di un'interfaccia [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) che prevede tre metodi - [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) e [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Query LINQ e iteratori ReadFeed utilizzano il metodo ResolveForRead internamente per scorrere tutte le raccolte che corrispondono alla chiave di partizione per la richiesta. Analogamente, creare operazioni utilizza il metodo ResolveForCreate per instradare crea alla partizione di destra. Sono state apportate modifiche necessarie per Sostituisci, eliminare e leggere dal momento che utilizzano documenti, che contengano già il riferimento alla raccolta corrispondente.

Gli SDK include anche due classi che supportano i due tecniche partizioni canoniche dei ricerche hashing e intervallo, tramite un [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e un [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). È possibile utilizzare queste classi facilmente l'aggiunta di logica partizione all'applicazione.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Aggiungere logica di partizione e registrare il PartitionResolver 

Ecco un frammento che mostra come creare un [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e registrare con DocumentClient per un database.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Creazione di documenti in una partizione  

Una volta registrata il PartitionResolver, è possibile eseguire crea e query direttamente nel database, come illustrato di seguito. In questo esempio SDK utilizza il PartitionResolver per estrarre l'ID utente, l'hashing e quindi usare il valore per instradare l'operazione di creazione della raccolta corretta.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Creare query su partizioni  

È possibile eseguire una query utilizzando il metodo [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) passando il database e una chiave di partizione. La query restituisce un singolo set di risultati su tutte le raccolte nel database di cui eseguire il mapping a chiave di partizione.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Creare query su tutte le raccolte nel database 

È anche possibile richiedere tutte le raccolte all'interno del database e consente di enumerare i risultati come indicato di seguito, per ignorare l'argomento chiave partizione.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Hashing partizione del Risolutore
Con hash partizione, partizioni vengono assegnate in base al valore di una funzione hash, che consente di distribuire uniformemente richieste e dati in un numero di partizioni. Questo approccio viene generalmente utilizzata per partizione dati prodotti o utilizzati da un numero elevato di client distinti e può essere utile per l'archiviazione dei profili utente, gli elementi del catalogo e i dati di telemetria IoT ("Internet di elementi"). Partizione hash anche è utilizzata dal supporto di partizioni sul lato server del DocumentDB all'interno di una raccolta.

**Partizionamento hash:**
![diagramma che illustra come partizione hash uniformemente distribuite richieste partizioni](media/documentdb-sharding/partition-hash.png)

Un semplice hash partizione combinazione tra raccolte *N* , è possibile utilizzare qualsiasi documento, calcolare *il resto di hash(d) N* per determinare quale insieme è stato inserito nella. Ma un problema con questa tecnica semplice che non funziona anche quando si aggiungono nuove raccolte, o rimuovere raccolte come questa richiedono quasi tutti i dati per ottenere reshuffled. [Hashing coerente] (http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) è un algoritmo noto che risolve questo problema mediante l'implementazione di uno schema di hashing che consente di ridurre la quantità di spostamento dei dati necessario durante l'aggiunta o rimozione di raccolte.

La classe [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implementata logica per creare un anello hash coerente tramite la funzione hash specificata nell'interfaccia di [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx) . Per impostazione predefinita, la HashPartitionResolver viene utilizzata una funzione di hash MD5, ma è possibile scambiare capire con l'implementazione hashing. Il HashPartitionResolver 16 hash o "nodi virtuali" all'interno di inoltrare la chiamata hash per ogni raccolta crea internamente per ottenere una distribuzione dei documenti più uniforme tra gli insiemi di file, ma è possibile modificare il numero di trade disattivare grado di asimmetria di dati con la quantità di calcolo sul lato client.

**L'hashing coerenti con HashPartitionResolver:**
![diagramma che illustra come HashPartitionResolver crea un anello hash](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Intervallo partizioni del Risolutore

In intervallo partizione, partizioni vengono assegnate a seconda che il tasto partizione sia in un determinato intervallo. Questa operazione viene generalmente utilizzata per partizioni con il comando proprietà indicatore di data e ora (ad esempio eventTime comprese tra il 1 aprile 2015 e 14 aprile 2015). La classe [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) consente di mantenere un mapping tra un intervallo\<T\> e raccolta self-creare un collegamento. 

[Intervallo\<T\> ](https://msdn.microsoft.com/library/azure/mt126048.aspx) è una classe semplice che consente di gestire gli intervalli di qualsiasi tipo che implementa IComparable\<T\> e IEquatable\<T\> come stringhe o numeri. Per legge e crea, è possibile passare in un intervallo qualsiasi e il sistema di risoluzione identifica tutte le raccolte di blocchi identificando gli intervalli delle partizioni che si intersecano con l'intervallo richiesto. Questa funzionalità può essere utile durante l'esecuzione di query di intervallo su tempo serie dati.

**Intervallo partizioni:**  

![ Diagramma che illustra come intervallo partizione uniformemente le richieste in partizioni](media/documentdb-sharding/partition-range.png)  

Un caso particolare delle partizioni intervallo è quando l'intervallo è un unico distinte valore, a volte chiamato "partizione di ricerca". Questa operazione viene generalmente utilizzata per partizioni per area geografica (ad esempio la partizione per Scandinavia contiene Norvegia, Danimarca e Svezia) o per partizioni tenant situati in un'applicazione multi-tenant.

## <a name="samples"></a>Esempi 

Esaminiamo il [progetto DocumentDB partizione esempi Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning) contenenti frammenti di codice su come utilizzare queste PartitionResolvers e li estendono per implementare propri sistemi di risoluzione per adattarla casi di utilizzo specifici, ad esempio le operazioni seguenti: 

* Come specificare un'espressione lambda non autorizzato per GetPartitionKey e usarlo per implementare le chiavi composte partizioni o per suddividere in modo diverso tipi diversi di oggetti.
* Come creare un semplice [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) che utilizza una tabella di ricerca manuale per eseguire partizioni. Questo modello viene generalmente utilizzato per partizioni in base ai valori distinti come l'area geografica, ID tenant o applicazione nome.
* Come creare un [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) che consente di creare raccolte automaticamente in base a un modello che consente di definire uno schema di denominazione, IndexingPolicy e stored procedure che devono essere registrati rispetto a nuove raccolte.
* Come creare una combinazione di meno [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) che crea semplicemente nuove raccolte come riempire gli insiemi di file precedente.
* Come serializzare e deserializzare lo stato di PartitionResolver come JSON, in modo da poterla condividere tra i processi e in arresto. È possibile mantenere questi file di configurazione, o persino di un insieme di DocumentDB.
* Una classe [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) per l'aggiunta e la rimozione di partizioni a un database suddiviso in modo dinamico in base a hashing coerenti. Internamente utilizza un [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) per instradare la lettura e scrittura durante la migrazione utilizzando uno dei quattro modalità - leggere il vecchio schema di partizione (ReadCurrent), quello nuovo (ReadNext), unire i risultati da entrambe (ReadBoth) o non essere disponibili durante la migrazione (nessuno).

Gli esempi sono Apri origine e si consiglia di inviare richieste pull con contributi che possono trarre vantaggio altri sviluppatori DocumentDB. Fare riferimento alle [linee guida contributo](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) per indicazioni su come collaborare.  

>[AZURE.NOTE] Crea raccolta sono tasso limitata da DocumentDB, in modo che alcuni metodi di esempio illustrati di seguito può richiedere alcuni minuti per completare.

##<a name="faq"></a>Domande frequenti
**DocumentDB supporta partizioni sul lato server?**

Sì, DocumentDB supporta [partizioni sul lato server](documentdb-partition-data.md). DocumentDB supporta anche sul lato client partizioni tramite sistemi di risoluzione partizione lato client per casi di utilizzo più avanzati.

**Quando è utile usare lato server e lato client partizioni?**
Per la maggior parte dei casi di utilizzo, è consigliabile l'utilizzo di lato server partizione poiché gestisce le attività amministrative di suddivisione dati e il routing delle richieste. Tuttavia, se è necessario partizioni di intervallo o in un caso di utilizzo specializzati di isolamento di prestazioni tra diversi valori delle chiavi partizione, quindi sul lato client partizioni potrebbe essere l'approccio ottimale.

**Come aggiungere o rimuovere una raccolta per la combinazione di partizione**

Esaminare l'implementazione di DocumentClientHashPartitioningManager nel progetto esempi per un esempio di come è possibile implementare il ripartizionamento.

**Come mantenere o condividere la configurazione di partizione con altri client**

È possibile serializzare lo stato di partitioner come JSON e archiviare nei file di configurazione, o persino all'interno delle raccolte DocumentDB. Esaminare il metodo RunSerializeDeserializeSample nel progetto esempi per un esempio.

**Come per bicicletta varie tecniche partizioni?**

È possibile concatenare PartitionResolvers mediante l'implementazione propri IPartitionResolver che internamente utilizza uno o più sistemi di risoluzione esistente. Da un'occhiata TransitionHashPartitionResolver nel progetto esempi per un esempio.

##<a name="references"></a>Riferimenti
* [Partizioni DocumentDB sul lato server](documentdb-partition-data.md)
* [Raccolte DocumentDB e livelli di prestazioni](documentdb-performance-levels.md)
* [Esempi di codice partizioni su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Documentazione di SDK .NET DocumentDB a MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Esempi di .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Limiti di DocumentDB](documentdb-limits.md)
* [Suggerimenti per DocumentDB Blog sulle prestazioni](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 
