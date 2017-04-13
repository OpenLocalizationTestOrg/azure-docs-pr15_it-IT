<properties 
    pageTitle="Modello di progettazione DocumentDB: app di Social Networking | Microsoft Azure" 
    description="Informazioni su un modello di progettazione per Social Network sfruttando flessibilità dello spazio di archiviazione di DocumentDB e altri servizi di Azure." 
    keywords="app di social networking"
    services="documentdb" 
    authors="ealsur" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="mimig"/>

# <a name="going-social-with-documentdb"></a>Passando social con DocumentDB

Che vivono in una società market interconnessi significa che, in un punto di vita, si diventa parte di un **social network**. Serve social network per rimanere in contatto con amici, colleghi, familiari o talvolta condividere intento con altre persone con interessi comuni.

Come ingegneri o sviluppatori abbiamo potremmo hanno sapere quale impatto come queste reti archiviare e collegamento dei dati, o possono hanno ancora ricevuto il compito per creare o progettare un nuovo social network per una specifica lusso personalmente. Ovvero quando si verifica la domanda grande: tutti i dati archiviazione?

Si supponga che si sta creando un nuovo e brillante social network, in cui gli utenti possono inserire post articoli correlati multimediali come immagini, video o musica pari. Gli utenti possono commentare i post e inviare punti per valutazioni. Si verificherà un feed di post che gli utenti vedranno e sarà possibile interagire con la pagina di destinazione sito Web principale. Non costituisce realmente complessa (inizialmente), ma per semplicità, si fermarsi (Impossibile inviare un feed di utente personalizzate incidono relazioni, ma supera lo scopo di questo articolo).

Quindi, in che modo vengono archiviate in questo e dove?

Molti degli utenti hanno esperienza nel database SQL o disporre almeno delle concetto di [modellazione dei dati relazionali](https://en.wikipedia.org/wiki/Relational_model) e possono essere utilizzati per iniziare a disegnare simile al seguente:

![Diagramma che mostra un modello relazionale relativo](./media/documentdb-social-media-apps/social-media-apps-sql.png) 

Una struttura di dati perfettamente normalizzato e abbastanza... che non scalare. 

Non è possibile ottenere me errato modificati con i database SQL tutti mia vita, sono fantastiche, ma come ogni piattaforma motivo, esercitazione e software, non è ideale per ogni scenario.

Perché non è SQL la scelta migliore in questo scenario? Osservare la struttura di un messaggio, se desidera visualizzare questo post in un sito Web o un'applicazione, che è necessario eseguire una query con... 8 join delle tabelle (!) solo per visualizzare un singolo post, a questo punto, immagine di un flusso di post in modo dinamico caricare e vengono visualizzate sullo schermo ed è possibile che venga visualizzato nel punto in cui verrà.

Possibile, naturalmente, utilizzare un'istanza di SQL enorme con potenza sufficiente per risolvere migliaia di query con questi molti join per servire i contenuti, ma davvero, non è quando non esiste una soluzione più semplice?

## <a name="the-nosql-road"></a>Guida di orientamento NoSQL

Esistono database grafico speciale che è possono [eseguire su Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure) ma non sono economici e richiedere servizi IaaS (Infrastructure as Service, macchine virtuali principalmente) e la manutenzione. Ora imposto a scopo di questo articolo in una soluzione di costo inferiore che funziona per la maggior parte dei casi, in esecuzione nel database NoSQL di Azure [DocumentDB](https://azure.microsoft.com/services/documentdb/). Con un approccio [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , la memorizzazione dei dati nel formato JSON e applicando [denormalizzazione](https://en.wikipedia.org/wiki/Denormalization)post in precedenza complesso può essere trasformato in un singolo [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

E può essere ottenuto con una singola query e con senza linee di join. Questo è molto più semplice e semplice e budget-wise, richiede l'esecuzione di un numero minore di risorse per ottenere risultati migliori.

DocumentDB Azure consente di verificare che tutte le proprietà siano indicizzate con l' [indicizzazione automatica](documentdb-indexing.md), che possono anche essere [personalizzato](documentdb-indexing-policies.md). L'approccio senza schema consente di archiviare i documenti con diversi e dinamiche le strutture, cercando domani che vogliamo post da un elenco di categorie o hashtags associate, DocumentDB consente di gestire i nuovi documenti con gli attributi aggiunti senza lavoro aggiuntivi necessari affinché ci.

I commenti in un post possono essere considerati come qualsiasi altra post con una proprietà padre (semplifica il mapping di oggetto). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

E tutte le interazioni sociale possono essere memorizzate in un oggetto separato come contatori:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Creazione di feed è sufficiente la creazione di documenti che possono contenere un elenco di post ID con un ordine di importanza assegnato:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Con i post ordinati per data di creazione di un flusso "ultimo", un flusso con i post con "interessanti" mi piace più nelle ultime 24 ore, è anche possibile implementare un flusso personalizzato per ogni utente in base alla logica come follower e gli interessi e sarebbe ancora un elenco di post. Si tratta della modalità di compilazione di questi elenchi, ma le prestazioni di lettura rimangono unhindered. Una volta abbiamo acquisire uno di questi elenchi, si rilascia una singola query DocumentDB utilizza l' [operatore IN](documentdb-sql-query.md#where-clause) per ottenere le pagine di post in un momento.

I flussi di feed è possibile creare con i processi in background [Servizi App di Windows Azure](https://azure.microsoft.com/services/app-service/) : [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Dopo aver creato un post, elaborazione in background può essere attivato utilizzando [Lo spazio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) [code](../storage/storage-dotnet-how-to-use-queues.md) e Webjobs attivato utilizzando [Azure Webjobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md), l'implementazione propagazione post all'interno di flussi in base alle proprie logica personalizzata. 

Punti e mi piace su un post possono essere elaborati in modalità posticipata utilizzando la stessa tecnica per creare un ambiente coerenza alla fine.

Follower sono più complessi. DocumentDB ha un limite di dimensioni documento di 512Kb, in modo che si potrebbe pensare sull'archiviazione follower come un documento con la struttura:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Questo potrebbe funzionare per un utente con pochi migliaia follower, ma se alcuni personaggi noti unisce il nostro classifica, questo verrà approccio non hanno un limite raggiunto il delimitatore di dimensione documento.

Per risolvere questo problema, è possibile utilizzare un approccio misto. Come parte del documento statistiche utente che è possibile archiviare il numero di follower:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

E il grafico effettivo di follower può essere archiviato in tabelle di spazio di archiviazione di Azure mediante un' [estensione](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) che consente di archiviazione "Segue-a-B" semplice e il recupero. In questo modo è possibile delegare il processo di recupero dell'elenco dei follower esatta (quando è necessario) alle tabelle di spazio di archiviazione di Azure, ma per una ricerca rapida dei numeri, si continuiamo utilizzando DocumentDB.

## <a name="the-ladder-pattern-and-data-duplication"></a>La duplicazione di modello e dati "Scala"

Come si sarà notato nel documento JSON che fa riferimento a un post, esistono più occorrenze di un utente. E si preferisce immaginare destra, che significa che le informazioni che rappresenta un utente, con questa denormalizzazione, potrebbero essere presente in più di un'unica posizione.

Per consentire query più veloce, si comportano la duplicazione dei dati. Il problema con questo effetto è che se per un'azione, modifiche ai dati dell'utente, è necessario trovare tutte le attività mai stato e aggiornarli tutti. Non suono molto pratica, a destra?

Grafico database risolvono nel proprio modo, che verranno risolvere identificando gli attributi di chiave di un utente contenenti nell'applicazione per ogni attività. Se si visivamente Mostra un post nell'applicazione e Mostra solo il creatore nome e l'immagine, perché archiviare tutti i dati dell'utente nell'attributo "createdBy"? Se per ogni commento è sufficiente mostrare l'immagine dell'utente, abbiamo bisogno il resto del suo informazioni. Ecco dove è che chiamare "motivo scala" entra in gioco.

Di seguito è illustrato informazioni utente ad esempio:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }
    
Esaminando queste informazioni sono futuro sarà possibile rilevare rapidamente che informazioni importanti e che non è, creando così una "scala":

![Diagramma di una serie di scala](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

Il passaggio più piccolo è denominato un UserChunk, la parte minima di informazioni che identificano un utente e che venga usato per la duplicazione dei dati. Riducendo le dimensioni dei dati duplicati solo le informazioni "illustreremo" è ridurre la possibilità di aggiornamenti rilevanti.

Il secondo passaggio si chiama l'utente, ma i dati completi che verranno utilizzati nella maggior parte delle query prestazioni basate su DocumentDB più accessibile e critico. Include informazioni rappresentate da un UserChunk.

Il valore più grande è l'utente esteso. Include tutte le informazioni critiche degli utenti e altri dati che non richiedono molto leggere rapidamente o l'uso della è eventuale (ad esempio la procedura di accesso). Possono essere archiviati all'esterno di DocumentDB nel Database SQL Azure o tabelle di archiviazione Azure.

Perché si è dividere l'utente e anche archiviare queste informazioni in posizioni diverse? Perché lo spazio di archiviazione in DocumentDB [non infinito](documentdb-limits.md) e da un prestazioni punto della visualizzazione, più grande dei documenti, le costlier le query. Conservare documenti sottile, con le informazioni giuste per eseguire tutte le query di prestazioni dipendente per il proprio social network e archiviare altre informazioni aggiuntive per gli scenari eventuale come profilo completo modifiche, gli account di accesso, anche data mining per analitica l'utilizzo e iniziative di dati. È davvero non ha importanza se il raccolta per il data mining di dati sono lenti perché è in esecuzione nel Database di SQL Azure, si dispone di riguardano attraverso che gli utenti abbiano rapido e sottile. Un utente, archiviato in DocumentDB, sarà simile alla seguente:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

E sarà simile a un Post:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

E quando una modifica si verifica uno degli attributi del blocco nel punto in cui viene modificato, è facile trovare i documenti interessati tramite query che puntano alle attributi indicizzati (selezionare * FROM pubblica p dove p.createdBy.id = = "edited_user_id") e quindi aggiornando i blocchi.

## <a name="the-search-box"></a>La casella di ricerca

Gli utenti genererà per fortuna, una grande quantità di contenuto. E dovrebbe essere possibile fornire la possibilità di cercare e trovare contenuti che potrebbero non essere direttamente nel loro flussi di contenuti, ad esempio perché è non seguire i creatori o cercando solo in corso trovare che post precedente abbiamo 6 mesi.

Per fortuna e poiché si sta utilizzando DocumentDB Azure, è possibile implementare facilmente un motore di ricerca utilizzando [Azure eseguire una ricerca](https://azure.microsoft.com/services/search/) in un paio di minuti e senza digitare una sola riga di codice (ad eccezione di evidenti, il processo di ricerca e dell'interfaccia utente).

Perché è facile?

Ricerca Azure implementa [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx), processi in background che collegano nei repository dati chiamano e automaticamente aggiungere, aggiornare o rimuovere gli oggetti negli indici. Supportano un Database SQL Azure, [indicizzatori](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/) [indicizzatori BLOB Azure](../search/search-howto-indexing-azure-blob-storage.md) e fortuna [indicizzatori DocumentDB Azure](../documentdb/documentdb-search-indexer.md). La transizione di informazioni da DocumentDB alla ricerca di Azure semplice, come le informazioni di archivio in formato JSON, occorre per [creare l'indice](../search/search-create-index-portal.md) e mappa gli attributi dai documenti che si desidera indicizzata e, in pochi minuti (dipende le dimensioni dei dati), tutti i contenuti saranno disponibili da cercare, la soluzione migliore di ricerca come servizio infrastruttura cloud. 

Per ulteriori informazioni sulla ricerca di Azure, è possibile usare la [Guida Hitchhiker alla ricerca](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>La conoscenza sottostante

Dopo aver memorizzato tutto il contenuto che si espande e diventa ogni giorno, potrebbe risultare noi pensare: operazioni possibili con tutti questo flusso di informazioni dagli utenti?

La risposta è semplice: inserire in modo da usare e informazioni del file.

Ma ciò che possiamo si apprende? Alcuni esempi di facile includono [analisi di valutazione in](https://en.wikipedia.org/wiki/Sentiment_analysis)consigli contenuti in base alle preferenze dell'utente o anche un automatizzato contenuto moderatore che assicura che tutto il contenuto pubblicati dal social network è al sicuro per la famiglia.

Ora che ho è associato, si verrà pensare probabilmente necessarie alcune dottore di ricerca di scienze matematiche per estrarre questi modelli e informazioni dal database semplici e file, ma è errato.

[Apprendimento azure](https://azure.microsoft.com/services/machine-learning/), parte della [Famiglia di prodotti Business Intelligence Cortana](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), è di un servizio cloud completamente gestita che consente di creare flussi di lavoro tramite algoritmi in una semplice interfaccia di trascinamento e codice personalizzati algoritmi [R](https://en.wikipedia.org/wiki/R_(programming_language)) o utilizzare alcune delle già creata e pronto per l'uso di API ad esempio: [Testo Analitica](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Moderatore contenuto](https://www.microsoft.com/moderator) o [consigli](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Per ottenere uno di questi scenari di apprendimento, abbiamo futuro sarà possibile usare [Lake di dati di Azure](https://azure.microsoft.com/services/data-lake-store/) per acquisire le informazioni provenienti da origini diverse e utilizzare [U SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) per elaborare le informazioni e genera un output elaborati da Azure apprendimento.

Un'altra opzione disponibile consiste nell'utilizzare [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) per analizzare il contenuto gli utenti. non solo possiamo è comprenderne meglio (analisi scrivono con [Testo Analitica API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ma è anche possibile rileva modifiche indesiderato o consolidato contenuto e agisce di conseguenza con [API degli obiettivi del Computer](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Servizi cognitivi includono numerose soluzioni di casella che non richiedono qualsiasi tipo di conoscenza di apprendimento da usare.

## <a name="conclusion"></a>Conclusioni

In questo articolo tenta di chiarire in alternative di creazione completamente social network in Azure con servizi di basso costo e fornire risultati ottimali, favorire l'utilizzo di una distribuzione di soluzioni e i dati a più livelli dello spazio di archiviazione denominata "Scala".

![Diagramma di interazione tra servizi Azure per social networking](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

La verità è che non c'è alcun punti argento per questo tipo di scenari, è la grande creata da una combinazione di grande servizi che consentono la creazione di esperienze: la velocità e libertà di Azure DocumentDB per fornire un'applicazione di social networking fantastica, intelligence dietro una soluzione di ricerca costrutto quali la ricerca di Azure, la flessibilità di servizi di Windows Azure App all'host nemmeno applicazioni indipendente dalla lingua ma processi in background potenti e lo spazio di archiviazione di Azure espandibile and Database di SQL Azure per archiviazione di grandi quantità di dati e la potenza analitica di apprendimento Azure per creare knowledge e business intelligence in grado di fornire commenti e suggerimenti a processi e Guida Contattaci recapitare i contenuti giusti agli utenti destro.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su come la modellazione, leggere l'articolo di [modellazione dei dati in DocumentDB](documentdb-modeling-data.md) dati. Se si è interessati in altri casi di utilizzo per DocumentDB, vedere [DocumentDB comuni use case](documentdb-use-cases.md).

O ulteriori informazioni sulle DocumentDB seguendo il [Percorso di apprendimento DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).
