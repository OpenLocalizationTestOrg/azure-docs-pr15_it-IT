<properties 
    pageTitle="La connessione di Database SQL Azure a Azure ricerca usando indicizzatori | Microsoft Azure | Indicizzatori" 
    description="Informazioni su come estrarre dati da Database SQL Azure in un indice di ricerca di Azure tramite indicizzatori." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/27/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>La connessione di Database SQL Azure alla ricerca di Azure usando indicizzatori

Servizio di ricerca di Azure è un servizio di ricerca cloud ospitato che semplifica l'individuazione assicurare un'esperienza di ricerca efficace. È possibile eseguire ricerche, è necessario compilare un indice di ricerca di Azure con i dati. Se i dati si trovano in un database SQL Azure, nuovo **indicizzatore Azure ricerca per il Database di SQL Azure** (o **dell'indicizzatore di SQL Azure** in breve) è possibile automatizzare il processo di indicizzazione. Di conseguenza, che si dispone di scrivere meno codice e meno infrastruttura per interessano.

Questo articolo illustra i meccanismi di utilizzo degli indicizzatori, ma viene inoltre le caratteristiche che sono disponibili solo con database di SQL Azure (ad esempio integrata rilevamento delle modifiche). Ricerca Azure supporta anche altre origini dati, ad esempio DocumentDB Azure, archiviazione blob e archiviazione tabella. Se si vuole vedere supporto per le origini dati aggiuntive, indicare i [forum di commenti e suggerimenti di ricerca di Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indicizzatori e le origini dati

È possibile installare e configurare un indicizzatore di SQL Azure con: 

- Importazione guidata dati nel [portale di Azure](https://portal.azure.com)
- Ricerca Azure [SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)
- Ricerca Azure [API REST](http://go.microsoft.com/fwlink/p/?LinkID=528173)

In questo articolo si userà API REST in cui viene illustrato come creare e gestire **indicizzatori** e **origini dati**. 

Un' **origine dati** specifica i dati da indicizzare credenziali necessarie per accedere ai dati e criteri che identificano in modo efficiente modifiche apportate ai dati (righe nuove, modificate o eliminate). È definito come risorsa indipendente in modo che possa essere utilizzato da più indicizzatori.

Un **indicizzatore** rappresenta una risorsa che si connette di origini dati con gli indici di ricerca di destinazione. Un indicizzatore viene utilizzato nei modi seguenti:
 
- Eseguire una copia unica dei dati per popolare un indice.
- Aggiornare un indice con le modifiche apportate nell'origine dati in base alla pianificazione.
- Eseguire su richiesta per aggiornare un indice in base alle esigenze. 

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizzare indicizzatore SQL Azure

In base a più fattori relativi ai dati, l'utilizzo di SQL Azure indicizzatore potrebbe o potrebbe non essere appropriato. Se i dati vengono inseriti i requisiti seguenti, è possibile utilizzare SQL Azure indicizzatore: 

- Tutti i dati provengono da una singola tabella o visualizzazione
    - Se i dati sono distribuiti più tabelle, è possibile creare una visualizzazione e utilizzare la visualizzazione con l'indicizzatore. Tuttavia, se si usa una visualizzazione, non sarà possibile usare il rilevamento delle modifiche integrata di SQL Server. Per ulteriori informazioni, vedere [questa sezione](#CaptureChangedRows). 
- I tipi di dati utilizzati nell'origine dati sono supportati dall'indicizzatore. Sono supportati la maggior parte delle ma non in tutti i tipi di SQL. Per informazioni dettagliate, vedere [il mapping dei tipi di dati di Azure ricerca](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Non è necessario prossimità aggiornamenti in tempo reale all'indice quando si modifica una riga. 
    - L'indicizzatore reindicizzare la tabella al massimo ogni 5 minuti. Se è necessario essere riflettano nell'indice analitico in secondi o minuti singole i dati vengono modificati spesso e le modifiche, è consigliabile utilizzare [API indice di ricerca Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) direttamente. 
- Se dispone di un set di dati di grandi dimensioni e si prevede di eseguire indicizzatore puntuale lo schema consente di identificare in modo efficiente modificato (ed eliminata, se applicabile) righe. Per ulteriori informazioni, vedere "L'acquisizione ed eliminati righe modificate" riportata di seguito. 
- Le dimensioni dei campi indicizzati in una riga non superano la dimensione massima di una ricerca di Azure indicizzazione richiesta, 16 MB. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Creare e utilizzare un indicizzatore SQL Azure

Prima di tutto, creare l'origine dati: 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


È possibile ottenere la stringa di connessione dal [Portale classica Azure](https://portal.azure.com); Utilizzare la `ADO.NET connection string` opzione.

Se non è già disponibile, creare l'indice di ricerca di Azure di destinazione. È possibile creare un indice tramite il [portale dell'interfaccia utente](https://portal.azure.com) o [Creare API di indice](https://msdn.microsoft.com/library/azure/dn798941.aspx). Verificare che lo schema dell'indice di destinazione sia compatibile con lo schema della tabella di origine, vedere [mapping tra SQL e Azure tipi di dati di ricerca](#TypeMapping).

Infine, creare l'indicizzatore, assegnare un nome e fare riferimento l'indice di origine e destinazione dati:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Un indicizzatore creato in questo modo non è disponibile una pianificazione. Viene eseguito automaticamente una volta al momento della creazione. È possibile eseguire in qualsiasi momento usando una richiesta di **eseguire indicizzatore** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

È possibile personalizzare diversi aspetti del comportamento dell'indicizzatore, ad esempio le dimensioni del batch e il numero di documenti è possibile saltare prima un'esecuzione dell'indicizzatore non riesce. Per ulteriori informazioni, vedere [Creare API dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Potrebbe essere necessario consentire ai servizi di Azure per connettersi al database. Per istruzioni su come eseguire questa operazione, vedere [Connessione da Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) .

Per controllare lo stato di indicizzatore e l'esecuzione della cronologia (numero di elementi indicizzati, errori e così via), utilizzare una richiesta di **stato dell'indicizzatore** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

La risposta dovrebbe essere simile al seguente: 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Cronologia esecuzione contiene fino a 50 delle esecuzioni completate più di recente, vengono ordinate in ordine cronologico inverso (in modo che l'esecuzione del più recente per primo nella risposta). Sono disponibili ulteriori informazioni sulla risposta di [Ottenere lo stato dell'indicizzatore](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Esecuzione indicizzatori pianificata

È inoltre possibile ordinare indicizzatore eseguire periodicamente in base alla pianificazione. A tale scopo, aggiungere la proprietà di **pianificazione** durante la creazione o aggiornamento dell'indicizzatore. Nell'esempio seguente viene mostrata una richiesta di caricamento per aggiornare l'indicizzatore:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Il parametro **intervallo** è obbligatorio. L'intervallo si intende il tempo tra l'inizio di due esecuzioni indicizzatore consecutivi. Intervallo minimo consentito è 5 minuti; più lunga è un giorno. È necessario essere formattato come un valore di "dayTimeDuration" XSD (un sottoinsieme di un valore di [Durata ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) con restrizioni). Il modello per questa operazione è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` per ogni 15 minuti, `PT2H` per ogni 2 ore.

Facoltativo **ora di inizio** indica quando devono iniziare le esecuzioni pianificate. Se viene omesso, viene utilizzata l'ora UTC corrente. Questa volta può essere in passato: in cui è programmata la prima esecuzione di maiuscole/minuscole come se è in esecuzione dell'indicizzatore continuamente dopo l'ora di inizio.  

Sola esecuzione di un indicizzatore può eseguire alla volta. Se un indicizzatore è in esecuzione quando è programmato l'esecuzione, l'esecuzione viene posticipata fino a quella successiva ora pianificata.

Si consideri un esempio per rendere più concreto. Si supponga che è la seguente pianificazione oraria configurata: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Ecco cosa accade: 

1. La prima esecuzione di indicizzatore inizia in, o attorno 1 marzo 2015 12.00 ORA UTC.
1. Si presume che l'esecuzione accetti 20 minuti (o qualsiasi momento minore di 1 ora).
1. La seconda esecuzione inizia in, o attorno 1 marzo 2015 1.00 
1. Si supponga che l'esecuzione ha più di un'ora, ad esempio 70 minuti – in modo che il completamento circa 10 alle 02:
1. È ora 2:00:00, ora per l'esecuzione del terzo iniziare. Tuttavia, in quanto la seconda esecuzione da 01. è ancora in esecuzione, l'esecuzione del terzo viene ignorato. Verrà avviata l'esecuzione del terzo 3 ore

È possibile aggiungere, modificare o eliminare una pianificazione per un indicizzatore esistente utilizzando una richiesta di **inserire indicizzatore** . 

<a name="CaptureChangedRows">, /a >
## <a name="capturing-new-changed-and-deleted-rows"></a>Acquisizione di nuovo, modificato e righe eliminate

Se la tabella contiene più righe, è necessario utilizzare un criterio di rilevamento modifiche di dati. Il rilevamento delle modifiche consente un utilizzo efficiente recuperare solo le righe nuove o modificate senza dover reindicizzare l'intera tabella.

### <a name="sql-integrated-change-tracking-policy"></a>SQL integrata rilevamento dei criteri

Se il database SQL supporta [il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb933875.aspx), è consigliabile usare **SQL integrato modificare verifica dei criteri**. Questo è il modo più efficiente criterio. Inoltre, consente la ricerca di Azure per identificare le righe eliminate senza che sia necessario aggiungere una colonna espliciti "Elimina contorni" alla tabella.

Il rilevamento delle modifiche integrata è supportato a partire con le versioni di database SQL Server seguenti:
 
- SQL Server 2008 R2 e versioni successive, se si usa SQL Server in macchine virtuali di Azure. 
- Azure SQL Database V12, se si usa il Database di SQL Azure.

Quando tramite SQL integrata rilevamento dei criteri, non si specifica un criterio di rilevamento l'eliminazione di dati distinto: questo criterio è incorporato il supporto per l'identificazione di eliminare righe.

Questo criterio può essere utilizzato solo con tabelle. non può essere utilizzato con le viste. È necessario attivare rilevamento delle modifiche per la tabella in uso prima di poter usare questo criterio. Per istruzioni, vedere [attivare e disattivare il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb964713.aspx) . 

Per utilizzare il criterio, creare o aggiornare l'origine dati al seguente:
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

<a name="HighWaterMarkPolicy"></a>
### <a name="high-water-mark-change-detection-policy"></a>Criteri il rilevamento delle modifiche limite massimo

Mentre si consiglia di criteri SQL integrato il rilevamento delle modifiche, può essere utilizzato solo con tabelle, viste non. Se si usa una visualizzazione, utilizzare il criterio limite massimo. Questo criterio può essere utilizzato se la tabella o visualizzazione contiene una colonna che soddisfano i criteri seguenti:

- Tutti gli inserimenti specificare un valore per la colonna. 
- Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna. 
- Il valore della colonna aumenta con ogni modifica apportata.
- Query con le operazioni seguenti nel punto in cui e clausole ORDER BY possono essere eseguite in modo efficiente: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

Ad esempio, una colonna indicizzata **rowversion** è un candidato ideale per la colonna limite massimo. Per utilizzare il criterio, creare o aggiornare l'origine dati al seguente: 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

> [AZURE.WARNING] Se la tabella di origine non dispone di un indice sulla colonna limite massimo, le query usate dall'indicizzatore SQL potrebbe verificarsi un timeout. In particolare, il `ORDER BY [High Water Mark Column]` clausola richiede un indice per l'esecuzione in modo efficiente quando la tabella contiene molte righe. 

Se si verificano errori di timeout, è possibile utilizzare il `queryTimeout` impostazione di configurazione dell'indicizzatore per impostare il timeout di query su un valore maggiore di timeout di 5 minuti predefinito. Ad esempio, per impostare il timeout a 10 minuti, creare o aggiornare l'indicizzatore con le seguenti operazioni: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

È anche possibile disabilitare la `ORDER BY [High Water Mark Column]` clausola. Tuttavia, questo non è consigliabile perché se l'esecuzione dell'indicizzatore viene interrotta da un errore, l'indicizzatore deve rielaborare tutte le righe se non si verifica in un secondo momento, anche se l'indicizzatore è già elaborato quasi tutte le righe nel momento in cui è stata interrotta. Per disabilitare il `ORDER BY` clausola, utilizzare la `disableOrderByHighWaterMarkColumn` impostazione nella definizione del indicizzatore:  

    {
     ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Criteri di eliminare il rilevamento di eliminazione colonna contorni

Quando le righe vengono eliminate dalla tabella di origine, probabilmente si desidera eliminare le righe da anche l'indice di ricerca. Se si utilizza il rilevamento delle integrata SQL criteri, questo viene gestito automaticamente. Tuttavia, il limite massimo rilevamento dei criteri non consentono di con righe eliminate. cosa fare? 

Se le righe fisica vengono rimossi dalla tabella, Azure ricerca non è possibile dedurre la presenza di record che non esistono più.  Tuttavia, è possibile utilizzare la tecnica "sfumato Elimina" per eliminare le righe in modo logico senza rimuoverli dalla tabella. Aggiungere una colonna per le righe di tabella o visualizzazione e Segna come eliminati con tale colonna.

Quando si usa la tecnica Elimina sfumato, è possibile specificare lo sfumato eliminare criterio come indicato di seguito durante la creazione o aggiornamento dell'origine dati: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

**SoftDeleteMarkerValue** deve essere una stringa, utilizzare la rappresentazione di stringa tra il valore effettivo. Se si dispone di una colonna di numeri interi in cui le righe eliminate vengono contrassegnate con il valore 1, ad esempio usare `"1"`. Se si dispone di una colonna BIT nel punto in cui le righe eliminate sono contrassegnate con il valore booleano true, utilizzare `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Il mapping tra tipi di dati SQL e i tipi di dati ricerca Azure

|Tipo di dati SQL | Tipi di campo consentiti indice di destinazione |Note 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| margine di flessibilità real, |Double, Edm.String | |
| smallmoney, numerico decimal denaro | Edm.String| Ricerca Azure non supporta la conversione di tipi decimali in EDM perché questo comporterebbe la perdita di precisione |
| Char, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Una stringa SQL può essere utilizzata per popolare un campo Collection(Edm.String) se la stringa rappresenta una matrice JSON di stringhe:`["red", "white", "blue"]` | 
|smalldatetime, datetime, datetime2, data, datetimeoffset |DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|area geografica | Edm.GeographyPoint | Sono supportate solo le istanze di area geografica di tipo POINT con 4326 SRID (ovvero l'impostazione predefinita) | | 
|RowVersion| N/D |Colonne di versione di riga non possono essere memorizzate nell'indice di ricerca, ma può essere usate per il rilevamento delle modifiche | |
| ora, timespan, binario, varbinary, immagini, xml, geometria e tipi di CLR | N/D |Non è supportata |

## <a name="configuration-settings"></a>Impostazioni di configurazione

Indicizzatore SQL esposti diverse impostazioni di configurazione: 

|Impostazione |Tipo di dati | Scopo | Valore predefinito |
|--------|----------|---------|---------------|
| queryTimeout | stringa | Imposta il timeout per l'esecuzione di query SQL | 5 minuti ("00: 05:00") |
| disableOrderByHighWaterMarkColumn | bool | Imposta la query SQL utilizzata dai criteri di limite massimo di omettere la clausola ORDER BY. Vedere [i criteri di limite massimo](#HighWaterMarkPolicy) | FALSO | 

Queste impostazioni vengono usate nel `parameters.configuration` oggetto nella definizione dell'indicizzatore. Ad esempio, per impostare il timeout delle query a 10 minuti, creare o aggiornare l'indicizzatore con le seguenti operazioni: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Domande frequenti

**Q:** È possibile usare indicizzatore SQL Azure con i database SQL in esecuzione in macchine virtuali IaaS in Azure?

R: Sì. Tuttavia, è necessario consentire il servizio di ricerca per la connessione al database. Per ulteriori informazioni, vedere [configurare una connessione da un indicizzatore di ricerca di Azure a SQL Server in macchine Virtuali un Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).


**Q:** È possibile usare indicizzatore SQL Azure con i database SQL in esecuzione in locale? 

R: si sconsiglia e non supporta la verifica, come questa operazione è necessario aprire i database per il traffico Internet. 

**Q:** È possibile usare indicizzatore SQL Azure con i database diverso da SQL Server in esecuzione in IaaS in Azure? 

R: non supportano questo scenario, perché non sono stati testati indicizzatore con qualsiasi database diverso da SQL Server.  

**Q:** È possibile creare più indicizzatori in esecuzione in una pianificazione? 

R: Sì. Tuttavia, un solo indicizzatore è possibile eseguire su un nodo contemporaneamente. Se è necessario più indicizzatori contemporaneamente in esecuzione, è consigliabile scalabilità il servizio di ricerca a più unità di ricerca. 

**Q:** Esecuzione di un indicizzatore influisce il carico di lavoro di query? 

R: Sì. Indicizzatore viene eseguito su uno dei nodi nel servizio di ricerca e le risorse del nodo vengono condivise tra l'indicizzazione e servizio il traffico di query e le richieste di altre API. Se si esegue in modo intensivo carichi di lavoro di indicizzazione e query e riscontrare un valore elevato di 503 errori o aumentare i tempi di risposta, è consigliabile scalabilità il servizio di ricerca.
