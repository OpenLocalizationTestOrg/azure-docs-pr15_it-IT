<properties 
    pageTitle="Utilizzo dei dati geospaziali in Azure DocumentDB | Microsoft Azure" 
    description="Imparare a creare, indice e query spaziali oggetti con Azure DocumentDB." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Utilizzo dei dati geospaziali in Azure DocumentDB

In questo articolo è un'introduzione alle funzionalità geospaziali in [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Dopo aver letto questa operazione, sarà possibile rispondere alle domande seguenti:

- Come memorizzare I dati spaziali in Azure DocumentDB
- Come è possibile eseguire query i dati geospaziali in DocumentDB Azure SQL e LINQ?
- Come abilitare o disabilitare l'indicizzazione spaziale nella DocumentDB

Vedere il [progetto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) per esempi di codice.

## <a name="introduction-to-spatial-data"></a>Introduzione ai dati spaziali

Dati spaziali descrivono la posizione e la forma degli oggetti nello spazio. Nella maggior parte delle applicazioni, ovvero corrispondono agli oggetti sulla terra, ovvero i dati geospaziali. Dati spaziali possono essere utilizzati per rappresentare la posizione di una persona, un luogo di interesse o il bordo di una città o un lake. Casi di utilizzo comuni spesso implicano l'invio di query prossimità, ad esempio, "ricerca bar tutti nella posizione corrente". 

### <a name="geojson"></a>GeoJSON
DocumentDB supporta l'indicizzazione e le query di dati decimale geospaziali rappresentato utilizzando la [specifica GeoJSON](http://geojson.org/geojson-spec.html). Strutture di dati GeoJSON sono sempre oggetti JSON validi, in modo che possono essere memorizzati e individuato utilizzando DocumentDB senza strumenti specifici o raccolte. SDK DocumentDB forniscono supporto classi e metodi che rendono più semplice da utilizzare dati spaziali. 

### <a name="points-linestrings-and-polygons"></a>Punti, oggetti linestring e poligono
Un **punto** indica un'unica posizione nello spazio. Nella casella dati geospaziali un punto rappresenta la posizione esatta, che può essere un indirizzo di un Negozio, continua, un'automobile o una città.  Un punto viene rappresentato nel coppia GeoJSON e DocumentDB, utilizzando le coordinate o longitudine e latitudine. Ecco un esempio JSON per un punto.

**Punti di DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] GeoJSON specifica longitudine prima e latitudine secondo. Ad esempio in altre applicazioni di mapping, latitudine e longitudine sono angoli ed espresso in gradi. Valori longitudine misurati dal primo meridiano e sono compresi tra -180 e 180.0 gradi e latitudine valori misurati dell'equatore e costruite da-90.0 e 90.0 gradi. 
>
> DocumentDB interpreta coordinate rappresentati per il sistema di riferimento WGS 84. Vedere di seguito per altri dettagli sui sistemi di coordinate di riferimento.

Può essere incorporato in un documento DocumentDB come illustrato nell'esempio seguente di un profilo utente contenente dati sulla posizione:

**Utilizzare profilo con posizione archiviati in DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Oltre ai punti, GeoJSON supporta anche oggetti linestring e poligono. **Oggetti linestring** rappresentare una serie di due o più punti nello spazio e i segmenti di linea per la connessione. Nei dati geospaziali, oggetti linestring vengono in genere utilizzati per rappresentare autostrade o fiumi. Un **poligono** è un limite di punti connessi che costituisce una linestring di tipo geografico chiuso. Poligono vengono in genere utilizzati per rappresentare formazioni naturale come laghi o uffici politiche come stati e città. Ecco un esempio di un poligono in DocumentDB. 

**Poligono in DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] Specifica GeoJSON è necessario che per poligono valido, l'ultima coppia di coordinate fornito deve essere uguale la prima, per creare una forma chiusa.
>
>Nell'ordine in senso antiorario, è necessario specificare punti all'interno di un poligono. Un poligono specificato nell'ordine in senso orario rappresenta l'inversa della regione all'interno di essa.

Oltre al punto, linestring di tipo geografico e poligono, GeoJSON specifica anche la rappresentazione per come raggruppare più posizioni geospaziali, nonché come associare georilevazione come una **caratteristica**di proprietà non autorizzata. Poiché questi oggetti sono JSON valido, possono tutti essere archiviati e DocumentDB di elaborazione. Tuttavia DocumentDB supporta solo l'indicizzazione automatica di punti.

### <a name="coordinate-reference-systems"></a>Sistemi di Guida di riferimento di coordinate

Poiché la forma della terra irregolare, viene rappresentato coordinate dei dati geospaziali in molti sistemi di coordinate di riferimento (CRS), ognuna con i propri frame di riferimento e unità di misura. Ad esempio, il "nazionali griglia di Gran Bretagna" è un sistema di riferimento è molto appropriato per il Regno Unito, ma non all'esterno. 

Oggi CRS più popolari in uso è il sistema geodetico World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivi GPS e molti servizi di mapping tra cui mappe Google e Bing Maps API usano WGS 84. DocumentDB supporta l'indicizzazione e le query di uso CRS 84 WGS solo i dati geospaziali. 

## <a name="creating-documents-with-spatial-data"></a>Creazione di documenti con dati spaziali
Quando si creano documenti che contengono valori GeoJSON, essi vengono indicizzati automaticamente con un indice spaziale in base ai criteri di indicizzazione della raccolta. Se si sta lavorando con un SDK DocumentDB in una lingua digitata in modo dinamico come Python o Node, è necessario creare GeoJSON valido.

**Creare documenti con i dati geospaziali in Node**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Se si sta lavorando con .NET (o linguaggio) SDK, è possibile utilizzare le nuove classi punto e poligono all'interno dello spazio dei nomi Microsoft.Azure.Documents.Spatial di incorporare le informazioni sulla posizione all'interno di oggetti dell'applicazione. Queste classi sono utili per semplificare la serializzazione e deserializzazione di dati spaziale in GeoJSON.

**Creare documenti con i dati geospaziali in .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Se non si dispongano di informazioni latitudine e longitudine, ma ha indirizzi fisici o un nome della località ad esempio città o paese, è possibile cercare le coordinate effettive utilizzando un servizio la geocodifica come Bing Maps resto servizi. Acquisire familiarità con la geocodifica Bing Maps [qui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Tipi di spaziali query

Ora che sono state osservare come inserire i dati geospaziali, ecco come ottenere i dati utilizzando DocumentDB utilizzando SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Funzioni SQL spaziale
DocumentDB supporta le funzioni predefinite Apri geospaziali Consortium (OGC) seguenti per eseguire la query geospaziali. Per informazioni dettagliate sull'insieme completo di funzioni in linguaggio SQL, consultare [DocumentDB Query](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Restituisce la distanza tra le due espressioni punto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Restituisce un'espressione booleana che indica se il punto di GeoJSON specificato nel primo argomento è all'interno del poligono GeoJSON nel secondo argomento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Restituisce un valore Boolean che indica se l'espressione specificata di punto o poligono GeoJSON è valido.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Restituisce un valore JSON contenente un valore Boolean valore se l'espressione specificata di punto o poligono GeoJSON è valido e se non valido, inoltre il motivo come valore stringa.</td>
</tr>
</table>

Funzioni spaziale possono essere utilizzate per eseguire query prossimità sui dati spaziale. Ecco, ad esempio, una query che restituisce tutti i documenti della famiglia che si trovano entro 30 km della posizione specificata, utilizzando la funzione incorporata ST_DISTANCE. 

**Query**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

Se si include l'indicizzazione spaziale nei criteri di indicizzazione, quindi "query distanza" verranno servite in modo efficiente attraverso l'indice. Per ulteriori informazioni sul processo di indicizzazione spaziale, vedere la sezione seguente. Se non si dispone di un indice spaziale per i percorsi specificati, è comunque possibile eseguire query spaziale specificando `x-ms-documentdb-query-enable-scan` intestazione richiesta con il valore impostato su "true". In .NET ciò è possibile passando l'argomento facoltativo **FeedOptions** alle query con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) impostato su true. 

ST_WITHIN può essere utilizzato per verificare se un punto si trova all'interno di un poligono. Poligono vengono utilizzate per rappresentare i bordi delle come codici postali, limiti di stato o formazioni naturale. Nuovo se si include l'indicizzazione spaziale nei criteri di indicizzazione, quindi "amore" query verranno servite in modo efficiente attraverso l'indice. 

Argomenti poligono ST_WITHIN possono contenere un solo anello, vale a dire il poligono non deve contenere aree vuote. 

**Query**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Risultati**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Simile a come non corrispondenti works tipi di query DocumentDB, se il valore del percorso specificato nell'argomento non è corretta o non validi, quindi verrà valutata per **definito** e il documento valutato ignorati dai risultati della query. Se la query non restituisce risultati, eseguire ST_ISVALIDDETAILED per debug perché il tipo di spatail è valido.     

DocumentDB supporta anche l'esecuzione di query inversa, ad esempio, è possibile indicizzare poligono o più righe DocumentDB e quindi eseguire una query per le aree che contengono un punto specifico. Questo modello viene generalmente utilizzato in logistica per identificare, ad esempio quando un carrello attiva o disattiva un'area designata. 

**Query**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Risultati**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED possono essere utilizzati per verificare la validità di un oggetto spaziale. Ad esempio, la query seguente controlla la validità di un punto di un messaggio del valore latitudine intervallo (-132.8). ST_ISVALID restituisce solo un valore Boolean e ST_ISVALIDDETAILED restituisce il valore booleano e una stringa contenente il motivo per è considerato non valido.

**Query**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Risultati**

    [{
      "$1": false
    }]

Queste funzioni possono anche essere utilizzate per convalidare poligono. Ad esempio, di seguito è utilizzare ST_ISVALIDDETAILED per convalidare un poligono che non viene chiusa. 

**Query**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Risultati**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>Invio di query in .NET SDK LINQ

DocumentDB .NET SDK anche provider stub metodi `Distance()` e `Within()` per l'utilizzo all'interno di espressioni LINQ. Il provider di DocumentDB LINQ converte le chiamate per le chiamate di funzione incorporata SQL equivalente (ST_DISTANCE e ST_WITHIN rispettivamente). 

Ecco un esempio di una query LINQ per trovare tutti i documenti nella raccolta DocumentDB il cui valore di "posizione" è all'interno di un raggio di 30km dell'oggetto specificato scegliere utilizzando LINQ.

**Query LINQ per distanza**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Analogamente, ecco una query per trovare tutti i documenti cui "posizione" è all'interno del casella/poligono specificato. 

**Per la query LINQ all'interno**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Ora che sono state osservare come documenti utilizzando LINQ e SQL della query, ecco come configurare DocumentDB per l'indicizzazione spaziale.

## <a name="indexing"></a>L'indicizzazione

Come descritto in questo documento [Schema indipendente dall'indicizzazione con DocumentDB Azure](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , è progettata motore di database di DocumentDB sia effettivamente indipendente dal schema e fornire supporto prima classe per JSON. Motore di database di scrittura ottimizzata DocumentDB riconosce a livello nativo dati spaziali (punti, poligono e linee) rappresentati in standard GeoJSON.

In breve, la geometria è previsto dalle coordinate geodetici su di un piano 2D quindi gradualmente suddivisa celle usando un **quadtree**. Queste celle mappate a 1D in base alla posizione della cella all'interno di una **curva di riempimento spazio Hilbert**, che consente di mantenere la località di punti. Inoltre quando verrà indicizzati dati sulla posizione, passano attraverso un processo noto come **mosaico**, vale a dire tutte le celle che si intersecano una posizione sono identificate e archiviate come chiavi dell'indice DocumentDB. In fase di query, argomenti, ad esempio punti e poligono sono inoltre tassellati per estrarre gli intervalli di ID cella appropriata e quindi utilizzati per recuperare dati dall'indice.

Se si specifica un criterio di indicizzazione che include indice spaziale per / * (tutti i percorsi), quindi tutti i punti di trovare all'interno dell'insieme sono indicizzati per query spaziale efficienti (ST_WITHIN e ST_DISTANCE). Gli indici spaziali non dispone di un valore di precisione e utilizza sempre un valore di precisione predefinito.

>[AZURE.NOTE] DocumentDB supporta l'indicizzazione automatica di punti, poligono e oggetti linestring

Il frammento JSON seguente mostra un criterio di indicizzazione con spaziale l'indicizzazione è abilitata, vale a dire indicizzare qualsiasi punto di GeoJSON trovato all'interno di documenti per la ricerca spaziale. Se si sta modificando il criterio di indicizzazione tramite il portale di Azure, è possibile specificare le seguenti JSON per i criteri di indicizzazione per abilitare l'indicizzazione nella raccolta spaziale.

**Raccolta indicizzazione criteri JSON con spaziale abilitati per i punti e poligono**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Ecco un frammento di codice in .NET che illustra come creare una raccolta con l'indicizzazione spaziale attivato per tutti i percorsi contenente punti. 

**Creare una raccolta con l'indicizzazione spaziale**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Ed ecco come è possibile modificare un insieme esistente per sfruttare i vantaggi dell'indicizzazione spaziale su tutti i punti vengono memorizzati all'interno di documenti.

**Modificare un insieme esistente con l'indicizzazione spaziale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Se il percorso GeoJSON valore all'interno del documento è errato o non validi, quindi non ottenere indicizzato per la ricerca spaziale. È possibile convalidare i valori di posizione usando ST_ISVALID e ST_ISVALIDDETAILED.
>
> Se la definizione di una raccolta include una chiave di partizione, indicizzazione lo stato di avanzamento di trasformazione non viene segnalato. 

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata apprendere su come iniziare a utilizzare il supporto di geospaziali in DocumentDB, è possibile:

- Iniziare a programmare con [.NET geospaziali esempi di codice su Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Ottenere mani su con geospaziali query in [Query di DocumentDB test](http://www.documentdb.com/sql/demo#geospatial)
- Ulteriori informazioni sulle [DocumentDB Query](documentdb-sql-query.md)
- Ulteriori informazioni sui [Criteri di indicizzazione DocumentDB](documentdb-indexing-policies.md)
