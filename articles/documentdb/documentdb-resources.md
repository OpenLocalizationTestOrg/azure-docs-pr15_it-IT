<properties 
    pageTitle="DocumentDB modello di risorse gerarchico e concetti | Microsoft Azure" 
    description="Informazioni sul modello gerarchico di DocumentDB del database, raccolte, funzione definita dall'utente (utente), documenti, le autorizzazioni per gestire le risorse e molto altro."
    keywords="Modello gerarchico, documentdb azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Concetti e modello di risorse gerarchico DocumentDB

Le entità del database che DocumentDB gestisce vengono definite **risorse**. Ogni risorsa viene identificata da un URI logico. È possibile interagire con le risorse utilizzando verbi HTTP standard, le intestazioni di richiesta/risposta e i codici di stato. 

Per leggere l'articolo, sarà possibile rispondere alle domande seguenti:

- Che cos'è il modello di risorse del DocumentDB?
- Quali sono sistema definito risorse anziché risorse definite dall'utente?
- Come indirizzare una risorsa
- Funzionamento con le raccolte
- Funzionamento con stored procedure, trigger e funzioni definite dall'utente (funzioni definite dall'utente)

## <a name="hierarchical-resource-model"></a>Modello di risorse gerarchici
Come illustrato nel diagramma seguente, il gerarchico DocumentDB **modello di risorse** è costituito da insiemi di risorse in un account di database, ogni utilizzabili tramite un URI logico e stabile. Un set di risorse verrà denominato a un **feed** in questo articolo. 

>[AZURE.NOTE] DocumentDB offre un efficiente protocollo TCP che corrisponde all'interno del modello di comunicazione disponibile tramite il [client di .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)RESTful.

![Modello di risorse gerarchico DocumentDB][1]  
**Modello di risorse gerarchici**   

Per iniziare a lavorare con le risorse, è necessario [creare un account di database DocumentDB](documentdb-create-account.md) tramite l'abbonamento Azure. Un account del database può essere composti da un set di **database**, ciascuno contenente più **raccolte**, ciascuna delle quali contengono, a sua volta **stored procedure, trigger, funzioni definite dall'utente, documenti** e relativi **allegati** (funzione di anteprima). Un database associata anche **gli utenti**, ognuno con un set di **autorizzazioni** di accesso a raccolte, stored procedure, trigger, funzioni definite dall'utente, documenti o allegati. Mentre i database, gli utenti, autorizzazioni e alle raccolte sono definite dal sistema di risorse con schemi note, documenti e gli allegati contengono non autorizzato, contenuto JSON definite dall'utente.  

|Risorsa   |Descrizione
|-----------|-----------
|Account del database   |Un account del database è associato a un insieme di database e un valore fisso di spazio di archiviazione blob per gli allegati (funzione di anteprima). È possibile creare uno o più account di database con l'abbonamento Azure. Per ulteriori informazioni, visitare i [prezzi pagina](https://azure.microsoft.com/pricing/details/documentdb/).
|Database   |Un database è un contenitore logico di archiviazione dei documenti suddiviso in raccolte. È anche un contenitore di utenti.
|Utente   |Spazio dei nomi logico per definire l'ambito delle autorizzazioni. 
|Autorizzazione |Un token di autorizzazione associato a un utente per l'accesso a una risorsa specifica.
|Raccolta |Una raccolta è un contenitore di documenti JSON e la logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, nel punto in cui il [costo](documentdb-performance-levels.md) è determinato dal livello di prestazioni associato alla raccolta. Insiemi estesi a uno o più partizioni/server che possono essere personalizzate per gestire volumi praticamente illimitati di spazio di archiviazione o effettiva.
|Stored Procedure   |Logica dell'applicazione scritta in JavaScript registrato con una raccolta che tramite transazioni eseguite all'interno del motore di database.
|Trigger    |La logica dell'applicazione scritta in JavaScript eseguite prima o dopo un inserimento, sostituire o eliminare un'operazione.
|UTENTE    |Logica dell'applicazione scritta in JavaScript. Funzioni definite dall'utente consentono di modellare un operatore di query personalizzata e quindi estendere il linguaggio di query DocumentDB core.
|Documento   |Contenuto JSON (non autorizzato) definite dall'utente. Per impostazione predefinita, nessuno schema deve essere definito né gli indici secondari devono essere fornito per tutti i documenti aggiunti a una raccolta.
|(Anteprima) Allegato   |Un allegato è un documento speciale contenente riferimenti e i metadati associati per blob/multimediale esterno. Lo sviluppatore può scegliere di avere blob gestito da DocumentDB o archiviare i dati con un provider di servizi esterni blob come OneDrive, Dropbox e così via. 


## <a name="system-vs-user-defined-resources"></a>Sistema e le risorse definite dall'utente
Risorse, ad esempio gli account di database, database, raccolte, gli utenti, autorizzazioni, stored procedure, trigger e funzioni definite dall'utente - tutti dispongono di uno schema fisso e vengono chiamate risorse di sistema. Invece risorse, ad esempio documenti e gli allegati non presentano restrizioni sullo schema e sono riportati alcuni esempi di risorse definite dall'utente. In DocumentDB definite dall'utente e sistema risorse sono rappresentate e gestite come JSON conforme standard. Tutte le risorse, sistema impostati dall'utente o, dispongono le proprietà comuni seguenti.

> [AZURE.NOTE] Nota tutti sistema generato proprietà in una risorsa sono preceduti da un carattere di sottolineatura (_) nella relativa rappresentazione JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Proprietà</strong></p></td>
            <td valign="top"><p><strong>Utente che è possibile impostare o generato dal sistema?</strong></p></td>
            <td valign="top"><p><strong>Scopo</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Generato dal sistema</p></td>
            <td valign="top"><p>Generato dal sistema, identificatore univoco e gerarchico della risorsa</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Generato dal sistema</p></td>
            <td valign="top"><p>eTag della risorsa necessaria per il controllo ottimistica</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Generato dal sistema</p></td>
            <td valign="top"><p>Ultimo aggiornamento timestamp della risorsa</p></td>
        </tr>
        <tr>
            <td valign="top"><p>self</p></td>
            <td valign="top"><p>Generato dal sistema</p></td>
            <td valign="top"><p>URI utilizzabili univoco della risorsa</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Generato dal sistema</p></td>
            <td valign="top"><p>Nome univoco della risorsa (con lo stesso valore chiave partizione) definite dall'utente. Se l'utente non è specificato un id, un id sarà generato dal sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Rappresentazione filo delle risorse
DocumentDB non sarà più tutte le estensioni proprietari per le codifiche di standard o speciali JSON; funziona con standard documenti compatibili con JSON.  
 
### <a name="addressing-a-resource"></a>Indirizzare una risorsa
Tutte le risorse sono URI utilizzabili. Il valore della proprietà **self** di una risorsa rappresenta l'URI relativo della risorsa. Il formato dell'URI costituito il /\<feed\>/ segmenti di tracciato {_rid}:  

|Valore di self |Descrizione
|-------------------|-----------
|/DBS   |Feed di database in un account del database
|/DBS/ {dbName}  |Database con un id che corrispondono al valore {dbName}
|/colls/ /DBS/ {dbName}   |Feed delle raccolte in un database
|/colls/ /DBS/ {dbName} {collName} |Raccolta con un id che corrispondono al valore {collName}
|/colls/ /DBS/ {dbName} {collName} / documenti    |Feed di documenti in una raccolta
|/docs/ /colls/ {collName} /DBS/ {dbName} {docId}    |Documento con un id che corrispondono al valore {doc}
|/utenti/ /DBS/ {dbName}   |Feed di utenti in un database
|/utenti/ /DBS/ {dbName} {ID utente}   |Utenti che hanno un id che corrispondono al valore {utente}
|/utenti/ /DBS/ {dbName} {ID utente} / autorizzazioni   |Feed delle autorizzazioni in un utente
|/permissions/ /utenti/ {ID utente} /DBS/ {dbName} {permissionId}    |Autorizzazione con un id che corrispondono al valore {autorizzazione}
  
Ogni risorsa ha un nome definito dall'utente univoco esposto tramite la proprietà id. Nota: per i documenti, se l'utente non è specificato un id, il nostro SDK supportati verrà generato automaticamente un id univoco per il documento. L'id è una stringa definite dall'utente, di un massimo di 256 caratteri che sia univoco all'interno del contesto di una risorsa padre specifico. 

Ogni risorsa è installato anche un identificatore di risorsa gerarchico generato dal sistema (denominato anche un RID), che è disponibile tramite la proprietà _rid. RID codifica l'intera gerarchia di una determinata risorsa e si tratta di una rappresentazione interna comoda utilizzata per applicare l'integrità referenziale in maniera distribuita. RID sia univoco all'interno di un account del database e viene utilizzato internamente da DocumentDB per il routing efficiente senza partizione cross ricerche. I valori delle proprietà _rid e il Self sono alternative e canoniche rappresentazioni di una risorsa. 

Le API REST DocumentDB supporta indirizzamento delle risorse e il routing di richieste, l'id e le proprietà _rid.

## <a name="database-accounts"></a>Account di database
È possibile eseguire il provisioning di uno o più account database DocumentDB tramite l'abbonamento Azure.

È possibile [creare e gestire gli account di database DocumentDB](documentdb-create-account.md) tramite il portale di Azure in [http://portal.azure.com/](https://portal.azure.com/). Creazione e gestione di un account del database richiede l'accesso amministrativo e può essere eseguita solo in abbonamento Azure. 

### <a name="database-account-properties"></a>Proprietà del database account
Durante il provisioning e la gestione di un account del database è possibile configurare e leggere le proprietà seguenti:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome della proprietà</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>La coerenza criteri</p></td>
            <td valign="top"><p>Impostare questa proprietà per configurare il livello di coerenza predefinito per tutte le raccolte con il proprio account di database. È possibile ignorare il livello di uniformità per ogni richiesta utilizzando l'intestazione della richiesta [x ms-coerenza livello]. <p><p>Si noti che questa proprietà si applica solo a <i>risorse definite dall'utente</i>. Sistema tutte le risorse definite siano configurate per supportare legge/query con coerenza sicura.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Tasti di autorizzazione</p></td>
            <td valign="top"><p>Queste sono le chiavi primarie e secondarie master e di sola lettura che forniscono accesso amministrativo a tutte le risorse con l'account di database.</p></td>
        </tr>
    </tbody>
</table>

Si noti che oltre il provisioning, configurare e gestire l'account di database dal portale di Azure, è possibile creare e gestire gli account di database DocumentDB utilizzando [Le API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) , nonché i [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)anche a livello di programmazione.  

## <a name="databases"></a>Database
Un database DocumentDB è un contenitore logico di uno o più raccolte e gli utenti, come illustrato nella figura seguente. È possibile creare qualsiasi numero di database con un account di database DocumentDB soggetto a limiti di offerta.  

![Modello gerarchico database account e raccolte][2]  
**Un Database è un contenitore logico di utenti e raccolte**

Un database può contenere archiviazione dei documenti praticamente illimitata suddivise da raccolte che formano i domini transazione per i documenti contenuti al loro interno. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Scalabilità flessibile di un database DocumentDB
Un database DocumentDB è flessibile per impostazione predefinita, compreso tra GB pochi e petabyte di archiviazione dei documenti SSD eseguito il backup e la velocità effettiva provisioning. 

A differenza di un database RDBMS tradizionale, un database in DocumentDB non è definito per un singolo computer. Con DocumentDB, come la scala dell'applicazione deve aumentare le dimensioni, è possibile creare ulteriori raccolte, database o entrambe. In effetti, diverse applicazioni di terze parti prima all'interno di Microsoft utilizza DocumentDB proporzioni consumer creando estremamente grandi database DocumentDB ogni contengono migliaia di raccolte con TB di spazio di archiviazione di documenti. È possibile aumentare o ridurre un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scala dell'applicazione. 

È possibile creare qualsiasi numero di raccolte all'interno di un database soggetto all'offerta. Ogni raccolta dispone SSD sottoposta lo spazio di archiviazione e la velocità effettiva viene completato il provisioning automaticamente a seconda del livello di prestazioni selezionata.

Un database DocumentDB è anche un contenitore di utenti. Un utente, a sua volta, è uno spazio dei nomi logico per un set di autorizzazioni che fornisce un'autorizzazione e accesso alle raccolte documenti e allegati.  
 
Come con altre risorse nel modello di risorse DocumentDB database possono essere creati, sostituito, eliminati, leggere o enumerate facilmente tramite [Le API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o i [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garantisce coerenza avanzata per la lettura o la ricerca dei metadati di una risorsa di database. Eliminazione di un database automaticamente garantisce che non è possibile accedere a una delle raccolte o gli utenti contenuti al suo interno.   

## <a name="collections"></a>Raccolte
Un insieme di DocumentDB è un contenitore per i documenti JSON. Una raccolta è un'unità di scala per le transazioni e alle query. 

### <a name="elastic-ssd-backed-document-storage"></a>Archiviazione di documenti SSD sottoposta flessibile
Una raccolta per sé flessibile: si espande automaticamente e visualizzato quando si aggiungono o si rimuovono documenti. Raccolte rappresentano logico e interessare uno o più partizioni fisiche o server. Il numero delle partizioni all'interno di una raccolta è DocumentDB in base alle dimensioni dello spazio di archiviazione e la velocità di provisioning della raccolta. Ogni partizione DocumentDB replicato disponibilità elevata ha un valore fisso di backup SSD lo spazio di archiviazione è associato. Gestione delle partizioni completamente viene gestita da Azure DocumentDB e non si dispone scrivere codice complesso o gestione delle partizioni. Gli insiemi di DocumentDB sono **praticamente illimitato** in termini di spazio di archiviazione e la velocità effettiva. 

### <a name="automatic-indexing-of-collections"></a>L'indicizzazione automatica delle raccolte
DocumentDB è un sistema di rendere disponibile dello schema del database. Non presuppongono o Richiedi qualsiasi schema per i documenti JSON. Mentre si aggiungono i documenti a una raccolta, viene automaticamente indicizzato DocumentDB e sono disponibili per la query. L'indicizzazione automatica dei documenti senza schema o gli indici secondari è una funzionalità chiave di DocumentDB e abilitato per le tecniche di manutenzione indice ottimizzato scrittura, senza blocchi e strutturati log. DocumentDB supporta volume velocità di scrittura estremamente veloce durante ancora la gestione delle query coerenti. Archiviazione di documenti e indice vengono utilizzati per calcolare l'archiviazione utilizzato da ogni raccolta. È possibile controllare lo spazio di archiviazione e le prestazioni compromessi associati dell'indicizzazione mediante la configurazione del criterio indicizzazione per una raccolta. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurazione dei criteri di indicizzazione di una raccolta
I criteri di indicizzazione di ogni raccolta consentono di verificare le prestazioni e lo spazio di archiviazione compromessi associati di indicizzazione. Sono disponibili nell'ambito della configurazione dell'indicizzazione seguenti opzioni:  

-   Scegliere se la raccolta viene automaticamente indicizzato tutti i documenti o meno. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente. È possibile scegliere di disattivare l'indicizzazione automatica e aggiungere in modo selettivo solo i documenti specifici per l'indice. Al contrario, è possibile scegliere in modo selettivo escludere solo i documenti specifici. È possibile ottenere questo impostando la proprietà automatica sia true o false in indexingPolicy di una raccolta e utilizzando l'intestazione della richiesta [x-ms-indexingdirective] durante l'inserimento, la sostituzione o eliminazione di un documento.  
-   Scegliere se includere o escludere percorsi specifici o motivi nei documenti dall'indice. È possibile ottenere questa impostazione includedPaths ed excludedPaths in indexingPolicy di una raccolta rispettivamente. È inoltre possibile configurare l'archiviazione e le prestazioni compromessi per intervallo che per le query per motivi di percorso specifico. 
-   Scegliere tra icona del (coerente) e aggiornamento asincrono dell'indice (lenta). Per impostazione predefinita, l'indice viene aggiornato in modo sincrono ogni Inserisci, Sostituisci o eliminazione di un documento alla raccolta. In questo modo le query rispettare allo stesso livello coerenza a quella di lettura documento. Mentre DocumentDB scrittura ottimizzato e supporta volumi velocità di scrittura documento oltre la manutenzione degli indici icona del e la gestione delle query coerente, è possibile configurare determinate raccolte per aggiornare l'indice lentamente. L'indicizzazione Prato aumenta ulteriormente le prestazioni di scrittura ed è ideale per gli scenari di acquisizione in blocco per principalmente gli insiemi di auto di lettura.

Il criterio di indicizzazione può essere modificato eseguendo un'inserire nella raccolta. Questo può essere ottenuto tramite il [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx), il [Portale di Azure](https://portal.azure.com) o le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>La ricerca di una raccolta
I documenti all'interno di una raccolta possono avere schemi non autorizzati ed è possibile eseguire query in una raccolta documenti senza fornire informazioni schema o spese indici secondari. È possibile richiedere l'insieme usando la [sintassi SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), che fornisce RTF gerarchici, relazionali e operatori spaziali ed estensibilità tramite basato su JavaScript funzioni definite dall'utente. Grammatica JSON consente di modellazione di documenti JSON come alberi con etichette dei nodi di struttura. Questo è sfruttare sia mediante tecniche di indicizzazione automatiche del DocumentDB come linguaggio SQL del DocumentDB. Il linguaggio di query DocumentDB è costituito da tre aspetti principali:   

1.  Un piccolo gruppo di operazioni di query che mappare naturalmente alla struttura inclusi previsioni e query gerarchica. 
2.  Un sottoinsieme di operazioni relazionali inclusi composizione, filtro, previsioni, le aggregazioni e self-join. 
3.  Pure JavaScript in base a funzioni definite dall'utente che funzionano con (1) e (2).  

Il modello di query DocumentDB tenta di stabilire un equilibrio tra funzionalità, l'efficienza e semplicità. Motore di database DocumentDB a livello nativo compila ed esegue le istruzioni di query SQL. È possibile richiedere una raccolta utilizzando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o i [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). .NET SDK viene fornito con un provider LINQ.

> [AZURE.TIP] È possibile provare DocumentDB ed eseguire query SQL sul set di dati in [Query di test](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transazioni di più documenti
Le transazioni del database costituiscono un modello di programmazione attendibile e prevedibile per gestire le modifiche simultanee ai dati. In RDBMS, la modalità tradizionale scrivere logica aziendale è scrivere **stored procedure** e/o **trigger** e spedire al server di database per l'esecuzione transazione. In RDBMS, programmatore applicazione è richiesto per gestire i due linguaggi di programmazione diversi: 

- L'applicazione (meno) programmazione lingua (ad esempio JavaScript, Python, c#, Java e così via)
- T-SQL, il linguaggio di programmazione transazione che viene eseguito a livello nativo dal database

Per il proprio impegno completa JavaScript e JSON direttamente all'interno del motore di database, DocumentDB fornisce un modello di programmazione intuitivo per l'esecuzione di JavaScript in base a applicazione logica direttamente negli insiemi di file in termini di stored procedure e trigger. In questo modo per entrambe le operazioni seguenti:

- Controllare l'efficacia del concorrenza, ripristino automatico indicizzazione dei grafici degli oggetti JSON direttamente nel motore di database
- Naturalmente esprimere il flusso di controllo, variabili definizione dell'ambito, assegnazione e l'integrazione di eccezione primitive con le transazioni del database direttamente in termini di JavaScript linguaggio di programmazione

Logica JavaScript registrata un livello di raccolta quindi possibile eseguire operazioni di database sui documenti dell'insieme specificato. DocumentDB vada a capo in modo implicito JavaScript in base a stored procedure e trigger all'interno di un ambiente transazioni ACID viene applicato in tutti i documenti all'interno di una raccolta. Nel corso della relativa esecuzione, se il codice JavaScript genera un'eccezione, quindi l'intera transazione viene interrotta. Il modello di programmazione risultante è molto semplice potenti. Gli sviluppatori JavaScript ottenere un modello di programmazione "permanente" mentre si utilizza i propri familiari costrutti e primitive raccolta.   

La possibilità di eseguire JavaScript direttamente all'interno del motore di database nello stesso spazio di indirizzi come pool di buffer consente efficienti e transazione esecuzione delle operazioni di database con i documenti di una raccolta. Inoltre, motore di database DocumentDB rende impegno completa per la JSON e JavaScript elimina qualsiasi mancata corrispondenza tra i sistemi di tipo di applicazione e il database.   

Dopo la creazione di una raccolta, è possibile registrare stored procedure, trigger e funzioni definite dall'utente con una raccolta utilizzando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o i [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Dopo la registrazione, è possibile fare riferimento ed eseguirli. Prendere in considerazione la seguente stored procedure interamente scritta in JavaScript, il codice riportato di seguito accetta due argomenti (nome della Rubrica e il nome dell'autore) e crea un nuovo documento, una query per un documento e quindi aggiornato – tutte all'interno di una transazione ACID implicita. In qualsiasi momento durante l'esecuzione, se viene generata un'eccezione JavaScript, l'intera viene interrotta.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Il cliente può "spedire" la logica JavaScript sopra per il database per l'esecuzione transazione mediante HTTP POST. Per ulteriori informazioni sull'utilizzo dei metodi HTTP, vedere [REST interazioni con il DocumentDB risorse](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Si noti che poiché il database riconosce a livello nativo JSON e JavaScript, non esiste alcun tipo non corrispondente di sistema, senza "OR mapping" o strumento di generazione codice obbligatorio.   

Stored procedure e trigger interagire con una raccolta e i documenti in una raccolta tramite un modello a oggetti ben che espone il contesto di raccolta corrente.  

Le raccolte in DocumentDB possono essere create, eliminare, leggere o enumerato facilmente utilizzando le [API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o i [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB fornisce sempre la coerenza avanzata per la lettura o la ricerca dei metadati di una raccolta. Eliminazione di una raccolta automaticamente garantisce che non può accedere ai documenti, gli allegati, stored procedure, trigger e funzioni definite dall'utente in esso contenuti.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Stored procedure, trigger e funzioni definite dall'utente (FDU)
Come descritto nella sezione precedente, è possibile scrivere la logica dell'applicazione da eseguire direttamente all'interno di una transazione all'interno il motore di database. La logica dell'applicazione può essere scritti interamente in JavaScript e modello come una stored procedure, trigger o un'utente. Il codice JavaScript all'interno di una stored procedure o un trigger può inserire, sostituire, eliminare, documenti di lettura o la query in una raccolta. Mano, JavaScript all'interno di un utente non è possibile inserire, sostituire o eliminare documenti. Funzioni definite dall'utente consente di enumerare i documenti del set di risultati della query e produrre un altro set di risultati. Per più affitto, DocumentDB vengono applicate governance una risorsa strict prenotazione in base a. Stored procedure di ogni, trigger o un'utente ottiene quantum fissi di risorse del sistema operativo per il proprio lavoro. Inoltre, stored procedure, trigger o funzioni definite dall'utente non è possibile collegarsi librerie JavaScript esterne e disattivato se superano i preventivi delle risorse assegnati a tali. È possibile eseguire la registrazione, annullamento della registrazione stored procedure, trigger o funzioni definite dall'utente con una raccolta tramite le API REST.  Al momento della registrazione una stored procedure, trigger o un'utente è precompilata e memorizzata come codice byte che viene eseguito in un secondo momento. La sezione seguente viene illustrato come è possibile utilizzare il SDK JavaScript DocumentDB per registrare, eseguire e annullare la registrazione di una stored procedure, trigger e un'utente. SDK JavaScript è un semplice spaziale [API REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registrazione di una stored procedure
Registrazione di una stored procedure crea una nuova risorsa stored procedure in una raccolta tramite HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Eseguire una stored procedure
Esecuzione di una stored procedure viene eseguita inviando un POST HTTP rispetto a una risorsa stored procedure esistente passando i parametri per la procedura descritta nel corpo della richiesta.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Annullamento della registrazione di una stored procedure
Annullamento della registrazione di una stored procedure viene eseguita semplicemente inviando un HTTP DELETE rispetto a una risorsa stored procedure esistente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrazione di un pre-trigger
Registrazione di un trigger viene effettuata mediante la creazione di una nuova risorsa trigger in una raccolta tramite HTTP POST. È possibile specificare se il trigger è una versione preliminare o un trigger di inserimento e il tipo di operazione può essere associato (ad esempio, creare, Sostituisci, eliminazione o tutti).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Esecuzione di un pre-trigger
Esecuzione di un trigger specificando il nome di un trigger esistente al momento dell'invio della richiesta di inserire/POST/eliminazione di una risorsa di documento tramite l'intestazione della richiesta.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Annullamento della registrazione di un pre-trigger
Annullamento della registrazione di un trigger viene eseguita semplicemente tramite emissione HTTP DELETE rispetto a una risorsa trigger esistente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrazione di un'utente
Registrazione di un'utente viene effettuata mediante la creazione di una nuova risorsa utente in una raccolta tramite HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Esecuzione di una funzione come parte della query
Un'utente può essere specificata come parte della query SQL e viene utilizzata come un modo per estendere il [linguaggio di query SQL di DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)core.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Annullamento della registrazione di un'utente 
Annullamento della registrazione di un'utente viene eseguita semplicemente inviando un HTTP DELETE rispetto a una risorsa utente esistente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Sebbene i frammenti sopra cui sono visualizzati registrazione (POST), annullamento della registrazione (inserire), in lettura/elenco (GET) ed esecuzione (POST) tramite [DocumentDB JavaScript SDK](https://github.com/Azure/azure-documentdb-js), è possibile utilizzare le [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o altri [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documenti
È possibile inserire, sostituire, eliminare, leggere, consente di enumerare e query non autorizzati documenti JSON in una raccolta. DocumentDB non sarà più tutti gli schemi e non richiede indici secondari per supportare le query sui documenti in una raccolta.   

Da un servizio di database aperto realmente, DocumentDB di creare qualsiasi tipo di dati specifico (ad esempio Data ora) o codifiche specifiche per i documenti JSON. Si noti che DocumentDB non richiede le convenzioni JSON speciali a codificare le relazioni tra i vari documenti. la sintassi SQL di DocumentDB fornisce potente query gerarchica e relazionale operatori ai documenti di progetto e query senza annotazioni speciali o necessità di codificare le relazioni tra documenti utilizzando distinguono proprietà.  
 
Come con tutte le altre risorse, i documenti possono essere creati, sostituito, eliminati, lettura, enumerato e query facilmente tramite API REST o uno dei [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). L'eliminazione di un documento all'istante liberare la quota corrispondente a tutti gli allegati annidati. Il livello di coerenza di lettura di documenti che segue i criteri di coerenza sull'account di database. Questo criterio può essere ignorato in base al richiesta a seconda delle esigenze di applicazione la coerenza dei dati. Quando una query su documenti, la coerenza lettura segue la modalità di indicizzazione impostare la raccolta. Per "coerenti" è conforme criteri coerenza dell'account. 

## <a name="attachments-and-media"></a>Allegati ed elementi multimediali
>[AZURE.NOTE] Risorse allegato ed elementi multimediali sono caratteristiche di anteprima.
 
DocumentDB consente di archiviare binario BLOB/media con DocumentDB o all'archivio multimediale remoto. Consente inoltre rappresentare i metadati di un file multimediali in termini di un documento speciale denominato allegati. Allegato di DocumentDB è un documento (JSON) speciale che fa riferimento a elementi multimediali/blob memorizzate altrove. Un allegato è semplicemente un documento speciale che acquisisce i metadati (ad esempio posizione, autore e così via) di un file multimediali archiviato in archiviazione un supporto remoto. 

Valutare la possibilità di un'applicazione di social networking lettura che utilizza DocumentDB per archiviare le annotazioni a penna e i metadati inclusi i commenti, evidenzia, segnalibri, valutazioni, mi piace/preferenze di un libro e di un determinato utente associato e così via.   

-   Il contenuto della Rubrica stessa memorizzato nell'archivio media sia disponibile come parte dell'account di database DocumentDB o un archivio di supporto remoto. 
-   Un'applicazione potrebbe archiviare metadati di ogni utente come documento distinto: ad esempio metadati di Fausto per Cartel1 archiviato in un documento di cui fanno riferimento /colls/joe/docs/book1. 
-   Allegati che puntano alle pagine di contenuto di un determinato libro di un utente sono archiviati nel documento corrispondente, ad esempio /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 e così via. 

Si noti che gli esempi riportati sopra elencati utilizzano ID descrittivo per comunicare la gerarchia delle risorse. Le risorse sono accessibili tramite l'API REST tramite gli ID univoci delle risorse. 

Per i file multimediali che viene gestiti da DocumentDB, la proprietà Media dell'allegato farà riferimento gli elementi multimediali dal relativo URI. DocumentDB garantisce a garbage raccogliere gli elementi multimediali quando vengono eliminati tutti i riferimenti in sospeso. DocumentDB automaticamente quando si carica il nuovo supporto genera l'allegato e compila Media in modo che puntino ai contenuti multimediali appena aggiunti. Se si sceglie di archiviare gli elementi multimediali in un archivio blob remoti gestito dall'utente (ad esempio OneDrive, lo spazio di archiviazione di Azure DropBox e così via), è possibile usare ancora allegati per fare riferimento gli elementi multimediali. In questo caso, si verrà creare manualmente l'allegato e popolare la relativa proprietà Media.   

Come con tutte le altre risorse allegati possono essere creati, sostituito, eliminati, leggere o enumerate facilmente tramite API REST o uno dei client SDK. Come con i documenti, il livello di coerenza di lettura di allegati segue i criteri di coerenza sull'account di database. Questo criterio può essere ignorato in base al richiesta a seconda delle esigenze di applicazione la coerenza dei dati. Quando si cercano allegati, la coerenza lettura segue la modalità di indicizzazione impostare la raccolta. Per "coerenti" è conforme criteri coerenza dell'account. 
 
## <a name="users"></a>Utenti
Un utente DocumentDB rappresenta una logica dello spazio dei nomi per il raggruppamento delle autorizzazioni. Un utente DocumentDB può corrispondere a un utente in un sistema di gestione delle identità o un ruolo di applicazione predefiniti. Per DocumentDB, un utente è sufficiente rappresenta un'astrazione per raggruppare un set di autorizzazioni in un database.   

Per l'implementazione affitto multiple nell'applicazione, è possibile creare utenti in DocumentDB che corrisponde a utenti effettivi o tenant dell'applicazione. È quindi possibile creare le autorizzazioni per un determinato utente che corrispondono al controllo dell'accesso su varie raccolte, documenti, gli allegati e così via.   

Come le applicazioni necessitano ridimensionare con la crescita di utente, è possibile adottare diverse modalità di condiviso i dati. È possibile modellare ogni utente come indicato di seguito:   

-   Ogni utente esegue il mapping a un database.
-   Ogni utente esegue il mapping a una raccolta. 
-   Documenti che corrispondono a più utenti passare a una raccolta dedicata. 
-   Documenti che corrispondono a più utenti passare a un insieme di raccolte.   

Indipendentemente dalla strategia sharding specifica che scelta, è possibile modellare gli utenti effettivi come gli utenti DocumentDB database e associare granulare correttamente le autorizzazioni per ogni utente.  

![Insiemi di utenti][3]  
**Strategie di sharding e gli utenti di modellazione**

Ad esempio tutte le altre risorse, gli utenti in DocumentDB possono essere creati, sostituito, eliminati, leggere o enumerati facilmente tramite API REST o uno dei client SDK. DocumentDB fornisce sempre la coerenza avanzata per la lettura o la ricerca dei metadati di una risorsa utente. È opportuno sottolineare che l'eliminazione di un utente automaticamente garantisce che non è possibile accedere le autorizzazioni contenute al suo interno. Anche se il DocumentDB recupera la quota delle autorizzazioni come parte dell'utente eliminato in background, le autorizzazioni eliminate è disponibile all'istante nuovamente per l'uso.  

## <a name="permissions"></a>Autorizzazioni
Da una prospettiva di controllo di accesso, risorse, ad esempio gli account di database, database, gli utenti e autorizzazioni sono considerati risorse *amministrative* poiché che richiedono autorizzazioni amministrative. Mano, risorse tra le raccolte documenti, gli allegati, stored procedure, trigger e funzioni definite dall'utente sono nell'ambito in un determinato database e considerate *risorse dell'applicazione*. Corrispondente a due tipi di risorse e i ruoli che accedervi (ovvero l'amministratore e utente), il modello di autorizzazione definisce due tipi di *tasti di scelta*: *chiave master* e *chiave della risorsa*. La chiave master è una parte dell'account del database e viene specificata per sviluppo (o amministratore) che è il provisioning di account del database. Questo tasto di schema con semantica di amministratore, che può essere utilizzato per autorizzare l'accesso alle risorse di amministrazione e dell'applicazione. In una chiave di risorsa invece un tasto di scelta granulare che consente l'accesso a una risorsa applicazione *specifica* . In questo modo, acquisisce la relazione tra un utente di un database e le autorizzazioni utente per una risorsa specifica (ad esempio insieme, documento, allegato, stored procedure, trigger o utente).   

L'unico modo per ottenere una chiave di risorsa è mediante la creazione di una risorsa di autorizzazione in un determinato utente. Si noti che per la creazione o recuperare un'autorizzazione, deve essere presentata una chiave master nell'intestazione di autorizzazione. Una risorsa di autorizzazione Collega la risorsa, l'accesso e l'utente. Dopo aver creato una risorsa di autorizzazione, l'utente deve solo presentare la chiave della risorsa associato per accedere alla risorsa necessaria. Di conseguenza, una chiave di risorsa può essere visualizzata come una rappresentazione in formato compatto e logici della risorsa autorizzazione.  

Come con tutte le altre risorse, le autorizzazioni di DocumentDB possono essere create, sostituito, eliminati, leggere o enumerati facilmente tramite API REST o uno dei client SDK. DocumentDB fornisce sempre la coerenza avanzata per la lettura o la ricerca dei metadati di un'autorizzazione. 

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sull'utilizzo delle risorse utilizzando i comandi HTTP in [REST interazioni con il DocumentDB risorse](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

