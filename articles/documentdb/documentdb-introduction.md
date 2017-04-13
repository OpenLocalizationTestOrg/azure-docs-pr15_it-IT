<properties 
    pageTitle="Introduzione a DocumentDB, un database JSON | Microsoft Azure" 
    description="Informazioni sulle DocumentDB Azure, un database NoSQL JSON. Questo database documento viene creato per dati, flessibile, disponibilità e scalabilità elevate." 
    keywords="database JSON, database di documento"
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
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introduzione a DocumentDB: un Database NoSQL JSON

##<a name="what-is-documentdb"></a>Che cos'è DocumentDB?

DocumentDB è un servizio di database NoSQL completamente gestito progettato per prestazioni veloci e prevedibili, disponibilità, flessibile proporzioni dei caratteri, distribuzione globale e facilità di sviluppo. Come un database NoSQL gratuiti schema DocumentDB offre funzionalità di query SQL RTF e familiarità con latenza bassa coerente dati JSON - verificare che il 99% delle operazioni di lettura sono resi disponibili in 10 millisecondi e il 99% della scrittura vengono fornite in 15 millisecondi. Effettuare questi vantaggi unici DocumentDB una grande adatta per web per dispositivi mobili, gioco e IoT e molte altre applicazioni che necessitano scala continua e replica globale.

## <a name="how-can-i-learn-about-documentdb"></a>Come è possibile trovare informazioni sul DocumentDB? 

Un modo rapido per ottenere informazioni DocumentDB e vedere in azione consiste nell'eseguire questi tre passaggi: 

1. Guardare il minuto due [Novità DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) video, che presenta vantaggi dell'uso delle DocumentDB.
2. Guardare il minuto tre [Creare DocumentDB su Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video, che evidenzia come iniziare a usare DocumentDB tramite il portale di Azure.
3. Visitare il [Test di Query](http://www.documentdb.com/sql/demo), in cui è possibile scorrere di informazioni sulle funzionalità di query avanzate disponibili in DocumentDB diverse attività. Quindi sede sopra alla scheda Sandbox ed eseguire query SQL personalizzate e sperimentare DocumentDB.

Quindi, tornare a questo articolo, in cui è necessario approfondire.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Quali caratteristiche e funzionalità principali offre DocumentDB?  

Azure DocumentDB offre le seguenti funzionalità chiave e i vantaggi:

-   **Velocità illimitate scalable e lo spazio di archiviazione:** Facilmente scalare o rimpicciolire il database DocumentDB JSON per soddisfare le esigenze dell'applicazione. I dati vengono archiviati dischi stato a tinta unita (SSD) per bassa latenza prevedibili. DocumentDB supporta contenitori per l'archiviazione dei dati JSON denominati raccolte che vanno a velocità provisioning e le dimensioni massime dello spazio di archiviazione praticamente illimitata. È possibile illimitate scalare DocumentDB con prestazioni prevedibili diretta dell'applicazione. 

-   **Replica più aree:** DocumentDB trasparente replica i dati in tutte le aree che è stata associata al proprio account DocumentDB, che consente di sviluppo di applicazioni che richiedono l'accesso globale ai dati fornendo compromessi tra la coerenza, disponibilità e prestazioni ottimali, tutti con garanzie corrispondente. DocumentDB fornisce failover internazionali trasparente con API più assegnazione di una posizione e la possibilità di scalare illimitate velocità e lo spazio di archiviazione in tutto il mondo. Informazioni su altre in [distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md).

-   **Query ad hoc con nota sintassi SQL:** Archiviare documenti JSON eterogenei all'interno di DocumentDB e questi documenti tramite una nota sintassi SQL della query. DocumentDB utilizza un lucchetto altamente simultaneo gratuito, log strutturati tecnologia di indicizzazione indicizzare automaticamente tutto il contenuto di documento. In questo modo RTF query in tempo reale senza la necessità di specificare suggerimenti schema, gli indici secondari o visualizzazioni. Altre informazioni nel [DocumentDB Query](documentdb-sql-query.md). 

-   **Esecuzione di JavaScript all'interno del database:** Esprimere la logica dell'applicazione come stored procedure, trigger e funzioni definite dall'utente (funzioni definite dall'utente) utilizzando JavaScript standard. In questo modo la logica dell'applicazione operare sui dati senza preoccuparsi di mancata corrispondenza tra l'applicazione e lo schema del database. DocumentDB consente l'esecuzione completa transazione JavaScript logica dell'applicazione direttamente all'interno del motore di database. Integrazione di JavaScript consente l'esecuzione di inserimento, Sostituisci, Elimina e le operazioni di selezione da un programma JavaScript come transazione isolata. Altre informazioni nel [DocumentDB la programmazione lato server](documentdb-programming.md).

-   **Livelli coerenza regolabili:** Selezionare i quattro livelli coerenza ben al ottenere ottimale compromesso tra la coerenza e prestazioni. Per le query e le operazioni di lettura, DocumentDB offre quattro livelli di coerenza distinti: sessione sicura obsolescenza delimitata, e finale. Questi livelli la coerenza granulare, ben consentono di rendere audio compromesso tra la coerenza, disponibilità e latenza. Informazioni su altre funzionalità in [utilizzando i livelli la coerenza per ottimizzare la disponibilità e prestazioni ottimali in DocumentDB](documentdb-consistency-levels.md).

-   **Completamente gestite:** Eliminare la necessità di gestire le risorse database e computer. Come un servizio di Microsoft Azure completamente gestito, non è necessario gestire macchine virtuali, distribuire e configurare il software, gestire le proporzioni dei caratteri o gestire gli aggiornamenti a livello di dati complessi. Tutti i database automaticamente backup e protetti da errori internazionali. Facilmente, è possibile aggiungere un account DocumentDB e provisioning capacità in base alle necessità, consentendo di focalizzare l'attenzione sull'applicazione anziché operativo e la gestione del database. 

-   **Aperta da una struttura:** Per iniziare rapidamente, utilizzare le competenze esistenti e gli strumenti. Programmazione per DocumentDB è semplice, accessibile e non è necessario adottare nuovi strumenti o rispettare le estensioni personalizzate JSON o JavaScript. È possibile accedere a tutte le funzionalità di database inclusi CRUD, query e JavaScript elaborazione in una semplice interfaccia REST HTTP. DocumentDB comprende i formati esistenti, lingue e standard offrendo valore ad alta capacità di database nella parte superiore di loro.

-   **Indicizzazione automatica:** Per impostazione predefinita, DocumentDB [Indicizza automaticamente](documentdb-indexing.md) tutti i documenti nel database e non aspettarsi o richiedere la creazione di indici secondari o di schema. Non si vuole indicizzare tutti gli elementi? Niente paura, è possibile anche [rifiutare esplicitazione percorsi dei file JSON](documentdb-indexing-policies.md) .

##<a name="data-management"></a>Come DocumentDB gestire dati?

Azure DocumentDB gestisce i dati JSON tramite risorse ben del database. Queste risorse vengono replicate per disponibilità e sono in modo univoco utilizzabile dagli loro URI logico. DocumentDB offre che un semplice HTTP basato sul modello di programmazione REST per tutte le risorse. 

L'account di database DocumentDB è uno spazio dei nomi univoco che consente di accedere a DocumentDB Azure. Prima di creare un account del database, è necessario disporre di un abbonamento Azure, che consente di accedere a una serie di servizi di Azure. 

Tutte le risorse all'interno di DocumentDB sono modellare e archiviate come documenti JSON. Le risorse vengono gestite come elementi, quali sono JSON i documenti che contiene i metadati e come feed, ossia le raccolte di elementi. Set di elementi che sono contenuti nei rispettivi feed.

L'immagine riportata di seguito sono riportate le relazioni tra le risorse DocumentDB:

![La relazione tra le risorse in DocumentDB, un database NoSQL JSON][1] 

Un account del database è costituito da un set di database, ognuno contenente più raccolte, ognuno dei quali può contenere stored procedure, trigger, funzioni definite dall'utente, documenti e allegati correlati. Un database associata anche gli utenti, ognuno con un set di autorizzazioni per accedere a varie altre raccolte, stored procedure, trigger, funzioni definite dall'utente, documenti o allegati. Mentre i database, utenti, autorizzazioni e raccolte sono definite dal sistema di risorse con schemi note, documenti, stored procedure, trigger, funzioni definite dall'utente e gli allegati contengono non autorizzato, contenuto JSON definite dall'utente.  

##<a name="develop"></a>Come è possibile sviluppare App con DocumentDB?

Azure DocumentDB espone risorse tramite un API REST che possono essere chiamati da qualsiasi linguaggio in grado di eseguire le richieste HTTP/HTTPS. Inoltre, DocumentDB offre le librerie di programmazione per diverse lingue popolari. Queste librerie di semplificare molti aspetti dell'utilizzo di Azure DocumentDB gestendo i dettagli, ad esempio indirizzo memorizzazione nella cache, la gestione delle eccezioni, tentativi automatici e così via. Raccolte sono attualmente disponibili per le piattaforme e le lingue seguenti:  

Download | Documentazione
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [Libreria di .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Raccolta Node](http://azure.github.io/azure-documentdb-node/)
[Linguaggio SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Raccolta di linguaggio](http://azure.github.io/azure-documentdb-java/)
[SDK JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Libreria JavaScript](http://azure.github.io/azure-documentdb-js/)
n/d | [Sul lato server JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Raccolta di Python](http://azure.github.io/azure-documentdb-python/)

Oltre base creare, leggere, aggiornare e le operazioni di eliminazione, DocumentDB offre un'interfaccia di query SQL RTF per il recupero dei documenti JSON e supporto tecnico sul lato server per l'esecuzione transazione JavaScript logica dell'applicazione. Le interfacce di esecuzione query e script sono disponibili tutte le raccolte di piattaforma, nonché le API REST. 

### <a name="sql-query"></a>Query SQL
Azure DocumentDB supporta documenti query utilizzando un linguaggio SQL che si trova nel sistema di tipo JavaScript ed espressioni con supporto per le query relazionale, gerarchiche e spaziale. Il linguaggio di query DocumentDB è un'interfaccia semplice ma funzionale ai documenti JSON query. La lingua supporta un sottoinsieme di sintassi SQL ANSI e aggiunge integrazione dell'oggetto JavaScript, matrici, la costruzione di oggetti e chiamata di funzione. DocumentDB fornisce un modello di query senza qualsiasi schema esplicito o suggerimenti indicizzazione dello sviluppatore del.

Funzioni definite dall'utente (funzioni definite dall'utente) sono registrate con DocumentDB e fare riferimento come parte di una query SQL, estendendo così il controllo grammaticale per supportare la logica dell'applicazione personalizzato. Queste funzioni definite dall'utente vengono scrittura come JavaScript programmi e l'esecuzione all'interno del database. 

Per gli sviluppatori di .NET DocumentDB offre un provider di query LINQ come parte di [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Le transazioni e l'esecuzione di JavaScript
DocumentDB consente di scrivere la logica dell'applicazione come programmi denominati interamente scritti in JavaScript. Questi programmi registrati per una raccolta e possono eseguire le operazioni di documenti all'interno di un determinato insieme database. JavaScript possono essere registrati per l'esecuzione come un trigger, stored procedure o funzione definita dall'utente. Stored procedure e trigger possono creare, leggere, aggiornare ed eliminare i documenti mentre funzioni definite dall'utente eseguono come parte della logica di esecuzione query senza accesso in scrittura alla raccolta.

Esecuzione di JavaScript all'interno di DocumentDB si basa concetti supportati da sistemi di database relazionali, con JavaScript come sostituzione moderna, ideale per Transact-SQL. Tutta la logica JavaScript viene eseguita all'interno di una transazione ACID ambiente con isolamento dello snapshot. Nel corso della relativa esecuzione, se il codice JavaScript genera un'eccezione, quindi l'intera transazione viene interrotta.

## <a name="next-steps"></a>Passaggi successivi
Ha già un account Azure? Quindi per iniziare con DocumentDB nel [Portale di Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) mediante la [creazione di un account di database DocumentDB](documentdb-create-account.md).

Non hanno un account Azure? Si può:

- Iscriversi a una [versione di valutazione gratuita Azure](https://azure.microsoft.com/free/), che consente di 30 giorni e $200 per provare a tutti i servizi Azure. 
- Se si dispone di un abbonamento MSDN, sono idonei per [$150 in gratuito crediti Azure al mese](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) usare qualsiasi servizio di Azure. 

Quindi, quando si è pronti per ottenere ulteriori informazioni, visitare il [percorso di formazione](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per passare tutte le risorse di formazione disponibili per l'utente. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
