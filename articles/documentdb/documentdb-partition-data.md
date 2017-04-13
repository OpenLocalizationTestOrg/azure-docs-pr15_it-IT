<properties 
    pageTitle="Suddivisione e il ridimensionamento in Azure DocumentDB | Microsoft Azure"      
    description="Informazioni sulle funzioni come partizioni nella DocumentDB Azure, come configurare partizione e partizione chiavi e come selezionare il tasto destro partizione dell'applicazione."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Suddivisione e il ridimensionamento in Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) consentono di ottenere prestazioni prevedibili e veloce e scala diretta con l'applicazione di si sviluppa. In questo articolo viene fornita una panoramica delle partizioni come opere in DocumentDB e descrive come configurare DocumentDB raccolte per ridimensionare in modo efficace le applicazioni.

Dopo aver letto in questo articolo, sarà possibile rispondere alle domande seguenti:   

- Come funziona partizioni in Azure DocumentDB?
- Come è possibile configurare partizioni DocumentDB
- Quali sono le chiavi partizione e come selezionare il tasto destro partizione per l'applicazione?

Per iniziare con codice, scaricare il progetto dal [DocumentDB prestazioni test Driver di esempio](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Partizioni DocumentDB

In DocumentDB, è possibile archiviare e documenti JSON meno di schema con i tempi di risposta ordine di MS la scala della query. DocumentDB fornisce contenitori per l'archiviazione dei dati denominati **raccolte**. Raccolte rappresentano logico e interessare uno o più partizioni fisiche o server. Il numero delle partizioni è DocumentDB in base alle dimensioni dello spazio di archiviazione e la velocità di provisioning della raccolta. Ogni partizione DocumentDB replicato disponibilità elevata ha un valore fisso di backup SSD lo spazio di archiviazione è associato. Gestione delle partizioni completamente viene gestita da Azure DocumentDB e non si dispone scrivere codice complesso o gestione delle partizioni. Gli insiemi di DocumentDB sono **praticamente illimitato** in termini di spazio di archiviazione e la velocità effettiva. 

Partizione è completamente trasparente all'applicazione. DocumentDB supporta legge veloce e scrive, query SQL e LINQ, transazione logica JavaScript in base a, livelli la coerenza e controllo dell'accesso specifico mediante le chiamate API REST a una risorsa unica raccolta. Il servizio gestisce i dati della distribuzione tra partizioni e routing delle richieste di query alla partizione di destra. 

Qual è il corretto funzionamento? Quando si crea una raccolta in DocumentDB, si noterà che è un valore di configurazione di una **proprietà chiave partizione** che è possibile specificare. Questa è la proprietà JSON (o percorso) all'interno dei documenti che possono essere usati dal DocumentDB per distribuire i dati tra più server o partizioni. DocumentDB verrà hashing il valore di chiave partizione e utilizzare il risultato hashing per determinare la partizione in cui verrà archiviato il documento JSON. Tutti i documenti con la stessa chiave partizione verranno archiviati nella stessa partizione. 

Si consideri ad esempio un'applicazione che contiene dati sui dipendenti e dei relativi servizi in DocumentDB. Di seguito scegliere `"department"` come proprietà chiave partizione per scalare i dati di reparto. Ogni documento DocumentDB deve contenere un obbligatorio `"id"` proprietà che devono essere univoci per ogni documento con lo stesso valore chiave partizione, ad esempio `"Marketing`". Tutti i documenti archiviati in una raccolta devono avere una combinazione univoca di chiave di partizione e id, ad esempio `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, e `{ "Department": "Sales", "id": "0001" }`. In altre parole, la proprietà composta di (id chiave, partizione) è la chiave primaria per la raccolta.

### <a name="partition-keys"></a>Tasti di partizione
La scelta della chiave di partizione è importante decidere che è necessario apportare in fase di progettazione. È necessario scegliere un nome di proprietà JSON offre una vasta gamma di valori che è probabile che hanno uniformemente modelli di accesso. Chiave di partizione viene specificata come percorso JSON, ad esempio `/department` rappresenta il reparto di proprietà. 

Nella tabella seguente è illustrati esempi di definizioni di chiave partizione e i valori JSON corrispondente a ciascuno.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Percorso della chiave di partizione</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Corrisponde a quello JSON di doc.department nel punto in cui documento è il documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>proprietà/nome</p></td>
            <td valign="top"><p>Corrisponde a quello JSON di doc.properties.name dove doc è il documento (proprietà annidate).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Corrisponde a quello JSON di doc.id (id e partizione chiave sono la stessa proprietà).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "Nome reparto"</p></td>
            <td valign="top"><p>Corrisponde a quello JSON di doc ["Nome reparto"] in cui documento è il documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La sintassi per percorso della chiave di partizione è simile al percorso per l'indicizzazione percorsi criteri con la differenza fondamentale che il percorso corrisponde alla proprietà anziché il valore, ovvero non esiste alcun carattere jolly alla fine. Ad esempio, specificare/reparto /? per indicizzare i valori in reparto, ma specificare /department come la definizione della chiave partizione. Il percorso della chiave partizione in modo implicito indicizzato e non è possibile escludere dall'indicizzazione mediante indicizzazione override di criteri.

Diamo un'occhiata come la scelta della chiave di partizione influisce sulle prestazioni dell'applicazione.

### <a name="partitioning-and-provisioned-throughput"></a>Produttività partizioni e provisioning
DocumentDB è progettato per prestazioni ottimali prevedibili. Quando si crea una raccolta, si riserva velocità in termini di ** [unità richiesta](documentdb-request-units.md) (RU) al secondo**. Ogni richiesta viene assegnato costo unitario richiesta proporzionato alla quantità di risorse di sistema come CPU e IO consumata dall'operazione. Lettura di un documento di 1 kB con coerenza sessione utilizza 1 unità richiesta. Lettura è 1 RU indipendentemente dal numero di elementi archiviati o il numero di richieste simultanee in esecuzione allo stesso. Documenti di grandi dimensioni richiedono unità richiesta superiore a seconda delle dimensioni. Se si conosce la dimensione dell'entità e il numero di lettura che è necessario per il supporto per l'applicazione, è possibile effettuare il provisioning il periodo di velocità effettiva richiesta per l'applicazione di leggere esigenze. 

Quando DocumentDB archivia i documenti, essa le distribuisce uniformemente partizioni in base al valore di chiave partizione. La velocità anche distribuita uniformemente tra disponibili partizioni ad esempio la velocità per partizione = (prestazioni più elevate per raccolta) / (numero delle partizioni). 

>[AZURE.NOTE] Per ottenere la velocità completa dell'insieme, è necessario scegliere una chiave di partizione che consente di distribuire uniformemente le richieste tra un numero di valori di chiave partizione distinta.

## <a name="single-partition-and-partitioned-collections"></a>Sola partizione e raccolte partizionate
DocumentDB supporta la creazione delle raccolte singola partizione e partizionate. 

- **Raccolte partizionati** possibile estesi a più partizioni e supporto tecnico grandi quantità di spazio di archiviazione e la velocità effettiva. È necessario specificare una chiave di partizione per la raccolta.
- **Raccolte singola partizione** disponibili opzioni di prezzo inferiore e la possibilità di eseguire query e le transazioni di tutti i dati insieme. Devono avere i limiti dello spazio di archiviazione e scalabilità di una singola partizione. Non è necessario specificare una chiave di partizione per le raccolte. 

![Raccolte partizionate in DocumentDB][2] 

Per scenari che non richiedono grandi quantità di spazio di archiviazione o effettiva, gli insiemi di singola partizione sono una soluzione ideale. Si noti che singola partizione raccolte hanno la scalabilità e limiti di archiviazione di una singola partizione, vale a dire fino a 10 GB di spazio di archiviazione e fino a 10.000 unità richiesta al secondo. 

Raccolte partizionate supportino grandi quantità di spazio di archiviazione e la velocità effettiva. Offerte predefinite tuttavia sono configurate per archiviare fino a 250 GB di spazio di archiviazione e scalare fino a 250.000 unità richiesta al secondo. Se è necessario maggiore spazio di archiviazione o produttività per insieme, contattare [Il supporto di Azure](documentdb-increase-limits.md) per questi maggiore per il proprio account.

Nella tabella seguente vengono elencate le differenze in raccolte partizionate e utilizzo di una singola partizione:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Raccolta singola partizione</strong></p></td>
            <td valign="top"><p><strong>Raccolta partizionata</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave di partizione</p></td>
            <td valign="top"><p>Nessuno</p></td>
            <td valign="top"><p>Obbligatorio</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave primaria di documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chiave composta &lt;chiave di partizione&gt; e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Spazio di archiviazione minima</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Spazio di archiviazione massimo</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Illimitato (250 GB per impostazione predefinita)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produttività minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Massima produttività</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Illimitato (unità di misura richiesta 250.000 al secondo per impostazione predefinita)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versioni di API</p></td>
            <td valign="top"><p>Tutti</p></td>
            <td valign="top"><p>API 2015-12-16 e versioni successive</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Lavorare con gli SDK

Azure DocumentDB il supporto per partizioni automatica con [API REST versione 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Per creare raccolte partizionate, è necessario scaricare versioni SDK 1.6.0 o successiva in una delle piattaforme supportate SDK (.NET, Node, Java, Python). 

### <a name="creating-partitioned-collections"></a>Creazione di raccolte partizionate

Nell'esempio seguente viene illustrato un frammento di .NET per creare una raccolta per memorizzare i dati di telemetria dispositivo di 20.000 unità richiesta al secondo di trasmissione. SDK imposta il valore di OfferThroughput (che a sua volta determina la `x-ms-offer-throughput` intestazione di richiesta di API REST). In questo campo è impostato il `/deviceId` come chiave di partizione. La scelta della chiave di partizione verrà salvata insieme al resto dei metadati insieme ad esempio nome e criteri di indicizzazione.

In questo esempio abbiamo scelto `deviceId` poiché è noto che (a) perché non ci sono un numero elevato di dispositivi, scrive possono essere distribuiti in partizioni in modo uniforme e che consente di ridimensionare il database per il caricamento di grandi quantità di dati e (b) molte delle richieste ad esempio il recupero di lettura più recente per un dispositivo sono limitate a un singolo deviceId e possono essere recuperati da una singola partizione.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Per creare raccolte partizionate, è necessario specificare un valore di velocità di unità di richiesta > 10.000 al secondo. Poiché la velocità di trasmissione in multipli di 100, questo deve essere 10,100 o versione successiva.

Questo metodo esegue un API REST chiamata a DocumentDB e il servizio verrà effettuare il provisioning di un numero di partizioni basata sulla velocità richiesta. È possibile modificare la velocità di una raccolta come le prestazioni esigenze. Per ulteriori informazioni, vedere [Livelli di prestazioni](documentdb-performance-levels.md) .

### <a name="reading-and-writing-documents"></a>Leggere e scrivere documenti

A questo punto, inserire dati in DocumentDB. Ecco un esempio di classe che contiene un dispositivo di lettura e una chiamata a CreateDocumentAsync per inserire un nuovo dispositivo lettura in una raccolta.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Di seguito leggere il documento, la chiave di partizione e id, aggiornarla e come ultimo passaggio, eliminarla da id e la chiave di partizione. Nota che legge includano un valore PartitionKey (corrispondente per il `x-ms-documentdb-partitionkey` intestazione di richiesta di API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Le query di raccolte partizionate

Quando si esegue una query dati in raccolte partizionate, DocumentDB indirizza automaticamente la query per le partizioni corrispondente ai valori di chiave partizione specificati nel filtro (se presente). Ad esempio, la query viene indirizzata solo la partizione contenente la chiave di partizione "0001 estesa".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La query seguente non dispone di un filtro per la chiave di partizione (DeviceId) e disposta a tutte le partizioni nel punto in cui viene eseguita su indice della partizione. Nota è necessario specificare il EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` in API REST) necessario SDK per eseguire una query in partizioni.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Esecuzione della Query in parallelo

Gli SDK DocumentDB 1.9.0 e versioni successive di supporto opzioni di esecuzione query in parallelo, che consentono di eseguire bassa latenza query su raccolte partizionate, anche quando è necessario toccare un numero elevato di partizioni. Ad esempio, la query seguente è configurata per eseguire in parallelo in partizioni.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Per gestire l'esecuzione della query in parallelo, è possibile ottimizzare i parametri seguenti:

- Mediante l'impostazione `MaxDegreeOfParallelism`, è possibile controllare il grado di parallelismo, ovvero il numero massimo di connessioni di rete simultaneamente a partizioni dell'insieme. Se si imposta questo su -1, il grado di parallelismo viene gestito da SDK. Se il `MaxDegreeOfParallelism` non è specificato o impostato su 0, il valore predefinito, si verificherà una connessione di rete singola a partizioni dell'insieme.
- Mediante l'impostazione `MaxBufferedItemCount`, è possibile gestione delle query client e latenza lato della memoria. Se si omette questo parametro o impostare questa proprietà su -1, il numero di elementi buffer durante l'esecuzione di query in parallelo viene gestito da SDK.

Dato lo stesso stato della raccolta, una query in parallelo restituirà risultati nello stesso ordine dell'esecuzione seriale. Quando si esegue una query tra partizione che include l'ordinamento (ORDER BY e/o TOP), SDK DocumentDB esegue la query in parallelo in partizioni e unisce parzialmente ordinati risultati sul lato client per ottenere risultati globalmente ordinati.

### <a name="executing-stored-procedures"></a>Esecuzione di stored procedure

È inoltre possibile eseguire le transazioni atomiche rispetto ai documenti con lo stesso ID di dispositivo, ad esempio, se si gestiscono le aggregazioni o lo stato più recente di un dispositivo in un singolo documento. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Nella sezione successiva si osserva come è possibile spostare alle raccolte partizionate dalle raccolte singola partizione.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migrazione da singola partizione alle raccolte partizionate
Quando un'applicazione utilizzando un insieme di singola partizione deve velocità maggiore (> 10.000 RU/s) o lo spazio di archiviazione di dati più grande (> 10GB), è possibile utilizzare lo [Strumento di migrazione dati DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) per eseguire la migrazione dei dati dalla raccolta singola partizione a una raccolta partizionata. 

Eseguire la migrazione da una raccolta singola partizione a una raccolta partizionata

1. Esportare dati dalla raccolta singola partizione in JSON. Per ulteriori informazioni, vedere [esportare file JSON](documentdb-import-data.md#export-to-json-file) .
2. Importare i dati in una raccolta partizionata creata con una definizione di chiave partizione e più di 10.000 unità richiesta al secondo, come illustrato nell'esempio riportato di seguito. Per ulteriori informazioni, vedere [importazione di DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) .

![La migrazione dei dati a una raccolta di partizionati in DocumentDB][3]  

>[AZURE.TIP] Per velocizzare i tempi di importazione, è possibile aumentare il numero di richieste parallele a 100 o superiore per usufruire di velocità effettiva superiore disponibile per gli insiemi partizionati. 

Ora che abbiamo completato le nozioni di base, esaminiamo alcune importanti considerazioni di progettazione quando si lavora con i tasti di partizione in DocumentDB.

## <a name="designing-for-partitioning"></a>Progettazione per partizione
La scelta della chiave di partizione è importante decidere che è necessario apportare in fase di progettazione. In questa sezione descrive alcuni dei compromessi coinvolti nella selezione di una chiave di partizione per la raccolta.

### <a name="partition-key-as-the-transaction-boundary"></a>Chiave di partizione come limite transazione
Scelta della chiave di partizione considerare la necessità di abilitare l'utilizzo delle transazioni e i requisiti per distribuire l'entità tra più tasti partizione per garantire una soluzione scalable. Un estremo, è possibile impostare la stessa chiave di partizione per tutti i documenti, ma questo potrebbe limitare la scalabilità della soluzione. In altri casi, è possibile assegnare una chiave di partizione univoco per ogni documento che si sarebbe scalabilità ma si impedisce di sfruttare le transazioni del documento cross tramite stored procedure e trigger. Una chiave di partizione ideale corrisponde a uno che consente di utilizzare query efficienti e che ha cardinalità sufficienti per assicurarsi che la soluzione scalable. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Come evitare bottiglia lo spazio di archiviazione e le prestazioni 
È anche importante selezionare una proprietà in modo scrive essere distribuito in un numero di valori distinti. Le richieste alla stessa chiave di partizione non possono superare la velocità di una singola partizione e saranno filtrate. È importante selezionare una chiave di partizione che generano **"sensibili"** all'interno dell'applicazione. Le dimensioni di spazio di archiviazione totale per i documenti con la stessa chiave partizione anche non possono superare 10 GB di spazio di archiviazione. 

### <a name="examples-of-good-partition-keys"></a>Esempi di chiavi partizione valido
Di seguito sono riportati alcuni esempi su come selezionare la chiave di partizione per l'applicazione:

* In caso di implementazione un back-end profilo utente, l'ID utente è un'ottima scelta per chiave di partizione.
* Se si desidera archiviare IoT dati, ad esempio lo stato del dispositivo, un ID dispositivo è un'ottima scelta per chiave di partizione.
* Se si usa DocumentDB per la registrazione dei dati di serie temporale, l'ID di nome host o un processo è un'ottima scelta per chiave di partizione.
* Se si dispone di un'architettura multi-tenant, l'ID tenant è un'ottima scelta per chiave di partizione.

Si noti che in alcuni casi di utilizzo (ad esempio i profili utente e IoT descritti in precedenza), il tasto partizione potrebbe essere diverso da quello di una partita (chiave di documento). In altri ad esempio i dati della serie ora, potrebbe essere una chiave di partizione diverso da quello l'id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partizioni e registrazione/tempo-serie di dati
Uno dei casi di utilizzo più comuni di DocumentDB è per la registrazione e telemetria. È importante selezionare una chiave di partizione valido poiché potrebbe essere necessario lettura/scrittura grandi quantità di dati. La scelta verrà dipendono la lettura e scrittura tariffe e tipi di query che si prevede di eseguire. Ecco alcuni suggerimenti su come scegliere una chiave di partizione valido.

- Se il caso di utilizzo prevede un tasso di piccole dimensioni di scrive acculumating su un periodo di tempo e necessità di query da intervalli di data e ora e altri filtri, quindi utilizzando il totale complessivo del timestamp, ad esempio data come chiave di partizione è un buon approccio. In questo modo è a query su tutti i dati per una data da una singola partizione. 
- Se il carico di lavoro è Auto di scrittura, che è in genere più comune, è necessario utilizzare una chiave di partizione che non è basata su timestamp in modo che DocumentDB scrive distribuzione uniforme su un numero di partizioni. Di seguito un nome host, ID processo, attività o un'altra proprietà con cardinalità elevata è un'ottima scelta. 
- Un terzo approccio è un ibrido uno nel punto in cui si dispone di più raccolte, uno per ogni giorno/mese e la chiave di partizione è una proprietà granulare come nome host. In questo modo i che è possibile impostare i livelli di prestazioni diversi in base a intervallo di tempo, ad esempio la raccolta per il mese corrente viene eseguito il provisioning con maggiore produttività poiché serve lettura e scrittura, mentre mesi precedenti con abbassare velocità dal momento che hanno solo lettura.

### <a name="partitioning-and-multi-tenancy"></a>Suddivisione e affitto multiple
Se si implementare un'applicazione multi-tenant mediante DocumentDB, sono disponibili due motivi principali per l'implementazione affitto con DocumentDB-chiave di una partizione per ogni tenant e una raccolta per tenant. Di seguito sono i vantaggi e svantaggi per ogni:

* Una chiave di partizione per ogni tenant: In questo modello, ad esempio, i tenant sono associare all'interno di un'unica raccolta. Ma rispetto a una singola partizione è possibile eseguire query e viene inserito per i documenti all'interno di un singolo tenant. È anche possibile implementare logica transazione in tutti i documenti all'interno di un tenant. Poiché più tenant condividono un insieme, è possibile salvare i costi di archiviazione e la velocità di trasmissione tramite il pool di risorse per tenant all'interno di un'unica raccolta anziché il provisioning di espansione aggiuntive per ogni tenant. Lo svantaggio è che non si dispone di isolamento delle prestazioni per ogni tenant. Aumenta/prestazioni riguarda vs intero insieme aumenta target per tenant.
* Una raccolta per ogni tenant: ogni tenant è il proprio insieme. In questo modello, è possibile prenotare delle prestazioni per ogni tenant. Con nuovo consumo in base a prezzo modello del DocumentDB, questo modello è più economico per le applicazioni multi-tenant con un numero limitato di tenant.

È anche possibile utilizzare una combinazione/livelli collocates tenant small, che consente di migrare tenant maggiore alla propria raccolta.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo abbiamo descritto come partizioni works in Azure DocumentDB, come è possibile creare raccolte partizionate e come è possibile selezionare una chiave di partizione valido per l'applicazione. 

-   Eseguire la scala e delle prestazioni con DocumentDB. Per un campione, vedere [prestazioni e scala test con Azure DocumentDB](documentdb-performance-testing.md) .
-   Per iniziare la codifica con [SDK](documentdb-sdk-dotnet.md) o l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Informazioni sulle [velocità provisioning in DocumentDB](documentdb-performance-levels.md)
-   Se si preferisce personalizzare le prestazioni partizione dell'applicazione, è possibile collegare la propria implementazione partizioni lato client. Vedere [lato Client partizione supporto](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
