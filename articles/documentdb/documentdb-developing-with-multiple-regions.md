<properties
   pageTitle="Sviluppo con più aree in DocumentDB | Microsoft Azure"
   description="Informazioni su come accedere ai dati in più aree da Azure DocumentDB, un servizio di database NoSQL completamente gestito."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Sviluppo con un account di DocumentDB più aree

> [AZURE.NOTE] Distribuzione globale dei database DocumentDB è generalmente disponibile e attivata automaticamente per tutti gli account DocumentDB appena creati. Stiamo lavorando per abilitare la distribuzione globale per tutti gli account esistenti, ma nel frattempo, se si desidera distribuzione globale abilitata nel proprio account, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e si verrà attivarla per l'ora.

Per sfruttare della [distribuzione globale](documentdb-distribute-data-globally.md), applicazioni client possono specificare l'elenco ordinato preferenza delle aree da utilizzare per eseguire operazioni di documento. Ciò è possibile impostare il criterio di connessione. In base alla configurazione dell'account Azure DocumentDB, disponibilità internazionali corrente e l'elenco di preferenza specificato, l'endpoint ottimale verrà scelto da SDK eseguire scrittura e le operazioni di lettura. 

In questo elenco preferenza viene specificato al momento dell'inizializzazione di una connessione tramite il client DocumentDB SDK. Gli SDK accettano un parametro facoltativo "PreferredLocations" ovvero un elenco ordinato delle aree Azure.

SDK invierà automaticamente tutte le operazioni di scrittura all'oggetto corrente scrivere area geografica. 

Verranno inviate tutte le operazioni lettura per la prima area disponibile nell'elenco PreferredLocations. Se la richiesta non riesce, il client non riuscire verso il basso l'elenco per l'area successiva e così via. 

Il client di che SDK solo tenterà leggere le aree geografiche PreferredLocations specificato in. Pertanto, ad esempio, se l'Account di Database è disponibile in tre aree, ma il client specifica solo due aree non scrittura per PreferredLocations, quindi non legge verranno servite fuori area scrittura, anche in caso di failover.

L'applicazione è possibile verificare l'endpoint di scrittura corrente e leggere endpoint scelto da SDK selezionando due proprietà, WriteEndpoint e ReadEndpoint, disponibile nella versione SDK 1.8 e versioni successive. 

Se la proprietà PreferredLocations non è impostata, tutte le richieste verranno servite dall'area di scrittura corrente. 


## <a name="net-sdk"></a>.NET SDK
SDK può essere utilizzato senza apportare modifiche al codice. In questo caso, il SDK automaticamente indirizza entrambe le operazioni di lettura in cui scrivere l'area di scrittura corrente. 

Nella versione 1,8 e versioni successive di .NET SDK, il parametro ConnectionPolicy per costruttore DocumentClient è una proprietà denominata Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Questa proprietà è di tipo raccolta `<string>` e deve contenere un elenco di nomi di area geografica. I valori stringa vengono formattati per la colonna nome dell'area sulle [Aree di Azure]  [ regions] pagina, senza spazi prima o dopo il primo e l'ultimo carattere rispettivamente.

La scrittura corrente e i punti finali letti sono disponibili in DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint rispettivamente.

> [AZURE.NOTE] L'URL per i punti finali non deve essere considerato come costanti lunga durate. Il servizio può aggiornare questi in qualsiasi momento. SDK gestisce automaticamente questa modifica.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript e Python SDK
SDK può essere utilizzato senza apportare modifiche al codice. In questo caso, il SDK reindirizza lettura e scrittura all'oggetto corrente scrittura area geografica. 

Nella versione 1,8 e versioni successive di ogni SDK, il parametro ConnectionPolicy per il costruttore DocumentClient una nuova proprietà denominata DocumentClient.ConnectionPolicy.PreferredLocations. Questo parametro è una matrice di stringhe che entrerà in un elenco di nomi di area geografica. I nomi vengono formattati per la colonna nome dell'area nelle [Aree di Azure]  [ regions] pagina. È inoltre possibile utilizzare le costanti predefinite dell'oggetto comodità AzureDocuments.Regions

La scrittura corrente e i punti finali letti sono disponibili in DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint rispettivamente.

> [AZURE.NOTE] L'URL per i punti finali non deve essere considerato come costanti lunga durate. Il servizio può aggiornare questi in qualsiasi momento. SDK gestirà automaticamente questa modifica.

Di seguito è un esempio di codice per NodeJS/Javascript. Python e linguaggio seguiranno lo stesso modello.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTO 
Una volta un account del database è stato reso disponibile in più aree, client possono inviare query la propria disponibilità eseguendo una richiesta GET su URI seguente.

    https://{databaseaccount}.documents.azure.com/

Il servizio restituirà un elenco delle aree geografiche e i corrispondenti endpoint DocumentDB URI per la replica. L'area di scrittura corrente sarà indicata nella risposta. Il client può quindi selezionare l'endpoint appropriato per tutti gli ulteriori richieste API REST come indicato di seguito.

Risposta di esempio

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Le richieste di tutto inserire, POST ed Elimina necessario passare alla scrittura indicato URI
-   Ottiene tutti e altre richieste di sola lettura, ad esempio le query, possono passare a qualsiasi punto finale della scelta del cliente

Scrivere le richieste di aree di sola lettura avrà esito negativo con codice di errore HTTP 403 ("accesso negato").

Se l'area di scrittura viene modificato dopo la fase di rilevamento iniziale del cliente, scrive successive all'area di scrittura precedente avrà esito negativo con codice di errore HTTP 403 ("accesso negato"). Il client quindi dovrebbe ottenere l'elenco delle aree nuovamente per ottenere l'area di scrittura aggiornati.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla distribuzione dei globalmente con DocumentDB nei seguenti articoli:

- [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md)
- [Livelli di coerenza](documentdb-consistency-levels.md)
- [Funzionamento della velocità con più aree](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Aggiungere aree tramite il portale di Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
