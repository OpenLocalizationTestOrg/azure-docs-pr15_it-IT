<properties 
    pageTitle="Le domande del Database DocumentDB - domande frequenti | Microsoft Azure" 
    description="Ottenere risposte alle domande frequenti su Azure DocumentDB un servizio di database NoSQL documento per JSON. Risposte alle domande database sulla capacità, prestazioni e scalabilità." 
    keywords="Domande sui database, domande frequenti, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Domande frequenti su DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Domande sui database sulle nozioni fondamentali su Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>Che cos'è Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB è una scala mondo NoSQL documento database-come-a-service che offre la query avanzate su dati senza schema, consente di offrire prestazioni ottimali configurabili e affidabile e consente un rapido sviluppo intera una piattaforma gestita da potenza e raggiunga di Microsoft Azure rapidamente, eccezionali. DocumentDB è la soluzione ideale per gioco per dispositivi mobili, web, applicazioni IoT quando sono prevedibili velocità, disponibilità, bassa latenza e un modello di dati senza schema principali requisiti. DocumentDB offre flessibilità schema e RTF indicizzazione tramite un modello di dati JSON nativo e include il supporto delle transazioni più documenti con JavaScript integrate.  
  
Per altre domande sui database, le risposte e istruzioni su come distribuire e utilizzare questo servizio, vedere la [pagina della documentazione DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Qual è il database è DocumentDB?
DocumentDB è un database di orientato documento NoSQL contenente dati in formato JSON.  DocumentDB supporta strutture annidate, disattivare contained dati che è possono eseguire una query tramite una DocumentDB RTF [grammatica query SQL](documentdb-sql-query.md). DocumentDB prestazioni elevate transazione elaborazione del lato server JavaScript tramite [stored procedure, trigger e funzioni definite dall'utente](documentdb-programming.md). Il database supporta anche i livelli di coerenza regolabili sviluppo con associati [livelli di prestazioni](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>Tabelle simili a un database relazionale (RDBMS) è necessario eseguire DocumentDB database?
No, DocumentDB archivia i dati in raccolte di documenti JSON.  Per informazioni sulle risorse DocumentDB, vedere [concetti e il modello di risorse DocumentDB](documentdb-resources.md). Per ulteriori informazioni sulle differenziano tra le soluzioni NoSQL, ad esempio DocumentDB e soluzioni relazionali, vedere [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>Database DocumentDB supportano dati senza schema?
Sì, DocumentDB consente alle applicazioni archiviare i documenti JSON non autorizzati senza definizione dello schema o suggerimenti. Dati sono immediatamente disponibili per query tramite l'interfaccia di query SQL DocumentDB.   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB supporta transazioni ACID?
Sì, DocumentDB supporta le transazioni tra documenti espresse come JavaScript stored procedure e trigger. Le transazioni sono nell'ambito di una partizione singola all'interno di ogni raccolta ed eseguite con semantica ACID tutti o niente isolato da altre richieste attualmente in esecuzione di codice e utente  Se vengono generate eccezioni attraverso l'esecuzione di lato server di codice JavaScript dell'applicazione, l'intera transazione verrà riportata. Per ulteriori informazioni sulle transazioni, vedere [transazioni del Database](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Quali sono i casi di utilizzo tipico per DocumentDB?  
DocumentDB è un'ottima scelta per nuovo gioco per dispositivi mobili, web, applicazioni e di IoT nel punto in cui ridimensionamento automatico, prestazioni ottimali prevedibili, fast ordine millisecondi dei tempi di risposta e la possibilità di query su dati senza schema importanti. DocumentDB consente di sviluppo rapido e che supporta l'iterazione continuo dei modelli di dati dell'applicazione. Applicazioni per la gestione dei dati e i contenuti generati dall'utente sono [casi di utilizzo comune per DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Come DocumentDB offre prestazioni prevedibili?
Un' [unità richiesta](documentdb-request-units.md) rappresenta la misura di trasmissione in DocumentDB. 1 RU corrisponde alla produttività della Guida di un documento di 1KB. Ogni operazione in DocumentDB, inclusi lettura, scrittura, le query SQL ed esecuzioni delle stored procedure contiene un valore RU deterministico in base alla velocità effettiva richiesta per completare l'operazione. Invece di pensare CPU, IO e della memoria e ognuna di esse impatto l'efficienza delle applicazioni, è possibile pensare in termini di una singola misura RU.

È possibile prenotare ogni raccolta DocumentDB fino a provisioning in termini di destinatari di velocità al secondo. Per le applicazioni di qualsiasi dimensione, è possibile valutare singole richieste per misurare i valori di RU e raccolte di provisioning per gestire il totale di unità richiesta in tutte le richieste. È anche possibile scalare o scalare verso il basso la velocità di trasmissione dell'insieme, come le esigenze dei evolve applicazione. Per ulteriori informazioni sulle unità richiesta e per la Guida determinazione della raccolta deve, leggere [gestire le prestazioni e capacità](documentdb-manage.md) e provare la [Calcolatrice effettiva](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>È conforme DocumentDB HIPAA?
Sì, DocumentDB è conforme HIPAA. HIPAA stabilisce i requisiti per l'utilizzo, la divulgazione e protezione delle informazioni sanitarie individualmente identificabili. Per ulteriori informazioni, vedere il [Centro protezione di Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>Quali sono i limiti di archiviazione di DocumentDB? 
Non esiste alcun limite teorico la quantità totale di dati che possono essere archiviati insieme in DocumentDB. Se si desidera memorizzare oltre 250 GB di dati all'interno di un'unica raccolta, [contattare il supporto](documentdb-increase-limits.md) per disporre la quota di account maggiore. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Quali sono i limiti di velocità di DocumentDB? 
Se il carico di lavoro può essere distribuito circa uniformemente tra un numero sufficientemente elevato di chiavi partizione non esiste alcun limite teorico la quantità totale di trasmissione in grado di supportare in DocumentDB, una raccolta. Se si desidera superano 250.000 richiesta unità/secondo per ogni raccolta o account, [contattare il supporto](documentdb-increase-limits.md) per disporre la quota di account maggiore. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Quanto costa Microsoft Azure DocumentDB?
Fare riferimento alla pagina di [informazioni sui prezzi DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) per informazioni dettagliate. Costi di utilizzo DocumentDB dipendono dal numero di raccolte in uso, il numero di ore gli insiemi di file sono stati online, e l'archiviazione consumata e provisioning velocità per ogni raccolta. 

### <a name="is-there-a-free-account-available"></a>È disponibile un account gratuito?
Se ha familiarità con Azure, è possibile iscriversi a un [account gratuito Azure](https://azure.microsoft.com/free/), che consente di 30 giorni e $200 per provare a tutti i servizi Azure. In alternativa, se si ha un abbonamento Visual Studio, si è idonei per [$150 in gratuito crediti Azure al mese](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) di utilizzare qualsiasi servizio di Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Come è possibile ottenere ulteriore assistenza con DocumentDB?
Se occorre una Guida, raggiungere Contattaci in [Overflow dello Stack](http://stackoverflow.com/questions/tagged/azure-documentdb), i [Forum per sviluppatori MSDN di Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)o pianificare una [chat 1:1 con il team di progettazione DocumentDB](http://www.askdocdb.com/). Per mantenersi aggiornati sulle funzionalità e ultime notizie DocumentDB, seguire Contattaci [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurare Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Come si iscrizione per Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB è disponibile nel [Portale di Azure][azure-portal].  Prima di tutto è necessario iscriversi per una sottoscrizione di Microsoft Azure.  Quando si effettua l'iscrizione per un abbonamento a Microsoft Azure, è possibile aggiungere un account DocumentDB all'abbonamento Azure. Per ulteriori informazioni sull'aggiunta di un account DocumentDB, vedere [creare un account di database DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>Che cos'è una chiave master?
Una chiave master è un token di sicurezza per accedere a tutte le risorse per un account. Gli utenti con il tasto hanno accesso lettura e scrittura a tutte le risorse nella finestra account di database. Distribuzione di chiavi master con cautela. Chiave master primaria e chiave secondaria schema sono disponibili in e il **chiavi **del [Portale di Azure][azure-portal]. Per ulteriori informazioni sulle chiavi, vedere [visualizzazione, copia e rigenera i tasti di scelta](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>Creazione di un database
È possibile creare i database tramite il [Portale di Azure]() , come descritto in [creare un database DocumentDB](documentdb-create-database.md), uno dei [DocumentDB SDK](documentdb-sdk-dotnet.md)o tramite l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>Che cos'è una raccolta?
Una raccolta è un contenitore di documenti JSON e la logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, in cui il [costo](documentdb-performance-levels.md) è determinato dalla velocità ed storaged utilizzato. Insiemi estesi a uno o più partizioni/server che possono essere personalizzate per gestire volumi praticamente illimitati di spazio di archiviazione o effettiva.

Raccolte sono anche l'entità di fatturazione per DocumentDB. Ogni raccolta viene effettuata ogni ora in base a effettiva provisioning e lo spazio di archiviazione utilizzato. Per ulteriori informazioni, vedere [DocumentDB prezzi](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Impostare come utenti e autorizzazioni
È possibile creare utenti e autorizzazioni utilizzando uno dei [DocumentDB SDK](documentdb-sdk-dotnet.md) o tramite l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Domande sui database sullo sviluppo rispetto a Microsoft Azure DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Come eseguire è possibile avviare sviluppo DocumentDB?
[SDK](documentdb-sdk-dotnet.md) sono disponibili per .NET, Python, Node, JavaScript e Java.  Gli sviluppatori possono inoltre sfruttare le [API REST di HTTP](https://msdn.microsoft.com/library/azure/dn781481.aspx) per interagire con le risorse DocumentDB da una varietà di piattaforme e lingue. 

Esempi di DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node](documentdb-nodejs-samples.md)e [Python](documentdb-python-samples.md) SDK sono disponibili in GitHub.

### <a name="does-documentdb-support-sql"></a>DocumentDB supporta SQL?
Il linguaggio di query SQL DocumentDB è un sottoinsieme migliorato le funzionalità di query supportati da SQL. Il linguaggio di query SQL DocumentDB offre RTF gerarchico e operatori relazionali ed estensibilità tramite JavaScript in base a utente definita funzioni dall'utente. Grammatica JSON consente di modellazione di documenti JSON come alberi con etichette come nodi, che viene utilizzato da tecniche di indicizzazione automatiche DocumentDB oltre al tipo di query SQL di DocumentDB.  Per informazioni dettagliate su come utilizzare la grammatica SQL, vedere la [Query DocumentDB] [ query] articolo.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Quali sono i tipi di dati supportati da DocumentDB?
I tipi di dati di base supportati in DocumentDB sono le stesse JSON. JSON è un sistema di tipo semplice costituito da stringhe, numeri (IEEE754 a precisione doppia) e valori booleani - true, false e null.  Tipi di dati più complessi come DateTime, Guid, Int64 e geometria possono essere rappresentati in JSON e DocumentDB tramite la creazione di oggetti nidificati utilizzando l'operatore {} e le matrici uso dell'operatore []. 

### <a name="how-does-documentdb-provide-concurrency"></a>In che modo DocumentDB offre concorrenza
DocumentDB supporta ottimistica controllo (OCC) tramite HTTP entità tag o ETag. Tutte le risorse DocumentDB ha un etag etag è impostata nel server ogni volta che viene aggiornato un documento. L'intestazione etag e il valore corrente sono inclusi in tutti i messaggi di risposta. ETag è utilizzabile con l'intestazione If-Match per consentire al server decidere se una risorsa deve essere aggiornata. Il valore di corrispondenza se è il valore etag per essere confrontati con. Se il valore etag associa il valore di etag server, la risorsa verrà aggiornata. Se l'etag non è aggiornato, il server rifiuta l'operazione con un "HTTP 412 condizione preliminare di errore" codice di risposta. Il client, quindi è necessario refetch la risorsa per acquisire il valore etag corrente per la risorsa. Inoltre, ETag utilizzabile con intestazione If-None-Match per determinare se è necessario nuovamente il recupero di una risorsa. 

Per usare ottimistica in .NET, utilizzare la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Per un esempio di .NET, vedere [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) nel campione DocumentManagement su github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Funzionamento delle transazioni in DocumentDB
DocumentDB supporta le transazioni integrata lingua tramite JavaScript stored procedure e trigger. Tutte le operazioni di database all'interno di script vengono eseguite in isolamento dello snapshot nell'ambito alla raccolta se è una singola partizione, o un documento con lo stesso valore di chiave partizione all'interno di una raccolta, se la raccolta è suddiviso. Uno snapshot le versioni del documento (eTag) viene eseguito all'inizio della transazione e approvato solo se lo script ha avuto esito positivo. Se il codice JavaScript genera un errore, le transazioni vengono ripristinati. Per ulteriori informazioni, vedere [programmazione DocumentDB sul lato server](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Come è possibile blocco Inserisci documenti in DocumentDB? 
Esistono tre modi di massa Inserisci documenti in DocumentDB:

- Lo strumento di migrazione di dati, come descritto in [Importa dati per DocumentDB](documentdb-import-data.md).
- Documento Explorer nel portale di Azure, come descritto in [documenti con Esplora risorse documento Aggiungi in blocco](documentdb-view-json-document-explorer.md#BulkAdd).
- Stored procedure, come descritto nella [programmazione DocumentDB sul lato server](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB supporta la memorizzazione nella cache di risorse collegamento?
Sì, perché DocumentDB è un servizio REST, collegamenti a risorse non sono modificabili e possono essere memorizzati nella cache. Client DocumentDB possibile specificare un'intestazione "If-None-Match" per operazioni di lettura di una risorsa, ad esempio documenti o raccolta e aggiornamento copie loro locali solo quando è modificare la versione nel server. 

### <a name="is-a-local-instance-of-documentdb-available"></a>È disponibile un'istanza locale di DocumentDB?
In questo momento un'istanza locale di DocumentDB non è disponibile. È possibile tenere traccia dello stato di un emulatore locale di voto per renderla nel [forum di commenti e suggerimenti](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
