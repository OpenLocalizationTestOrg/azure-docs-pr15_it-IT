<properties 
pageTitle="Le operazioni dell'indicizzatore (API REST servizio di ricerca Azure: anteprima 28 di 02 2015) | Anteprima di ricerca Azure API" 
description="Le operazioni dell'indicizzatore (API REST servizio di ricerca Azure: anteprima 28 di 02 2015)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Le operazioni dell'indicizzatore (API REST servizio di ricerca Azure: anteprima 28 di 02 2015)#

> [AZURE.NOTE] In questo articolo vengono illustrati indicizzatori dell' [API REST anteprima 28 di 02 2015](search-api-2015-02-28-preview.md). Questa versione dell'API aggiunge versioni precedenti indicizzatore archiviazione Blob Azure con l'estrazione di documenti e di indicizzatore archivio tabelle Azure, nonché altri miglioramenti. L'API supporta anche indicizzatori (GA) generalmente disponibili, tra cui indicizzatori per il Database di SQL Azure, SQL Server in macchine virtuali di Azure e Azure DocumentDB.

## <a name="overview"></a>Panoramica ##

Ricerca Azure possibile integrare direttamente con alcune origini dati comuni, senza la necessità di scrivere codice per indicizzare i dati. Per impostare questa configurazione, è possibile chiamare l'API di ricerca di Azure per creare e gestire **indicizzatori** e **origini dati**. 

Un **indicizzatore** rappresenta una risorsa che si connette di origini dati con gli indici di ricerca di destinazione. Un indicizzatore viene utilizzato nei modi seguenti: 

- Eseguire una copia unica dei dati per popolare un indice.
- Sincronizzare un indice con le modifiche apportate nell'origine dati in base alla pianificazione. La pianificazione fa parte della definizione dell'indicizzatore.
- Richiamare su richiesta per aggiornare un indice in base alle esigenze. 

Un **indicizzatore** è utile quando si desidera regolari aggiornamenti a un indice. È possibile impostare una pianificazione all'interno del testo come parte di una definizione dell'indicizzatore o eseguire su richiesta utilizzando [Eseguire indicizzatore](#RunIndexer). 

Un' **origine dati** consente di specificare quali dati devono essere indicizzate, le credenziali per accedere ai dati e i criteri di attivare la ricerca di Azure identificare in modo efficiente le modifiche dei dati (ad esempio modificare o eliminare righe in una tabella di database). È definito come risorsa indipendente in modo che possa essere utilizzato da più indicizzatori.

Origini dati seguenti sono attualmente supportate:

- **Database SQL Azure** e **SQL Server in macchine virtuali di Azure**. Per una panoramica di destinazione, vedere [questo articolo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **DocumentDB azure**. Per una panoramica di destinazione, vedere [questo articolo](../documentdb/documentdb-search-indexer.md). 
- **Archiviazione Blob azure**, inclusi i seguenti formati di documento: PDF, Microsoft Office (DOCX/DOC, XLS/XSLX, PPTX o PPT, MSG), HTML, XML, ZIP e testo normale file (inclusi JSON). Per una panoramica di destinazione, vedere [questo articolo](search-howto-indexing-azure-blob-storage.md).
- **Archivio tabelle azure**. Per una panoramica di destinazione, vedere [questo articolo](search-howto-indexing-azure-tables.md).
     
Abbiamo stiamo prendere in considerazione l'aggiunta del supporto per altre origini dati in futuro. Per definire la priorità di queste decisioni, fornire i commenti nel [forum di commenti e suggerimenti di ricerca di Azure](http://feedback.azure.com/forums/263029-azure-search)contribuire.

Vedere [Servizio limiti](search-limits-quotas-capacity.md) per limiti massimi relativi alle risorse di origine dati e dell'indicizzatore.

## <a name="typical-usage-flow"></a>Utilizzo tipico flusso ##

È possibile creare e gestire indicizzatori e le origini dati tramite richieste HTTP semplici (POST, GET, inserire, eliminare) rispetto a un determinato `data source` o `indexer` risorse.

Configurazione di indicizzazione automatica è in genere un processo in quattro passaggi:

1. Identificare l'origine dati che contiene i dati che deve essere indicizzate. Tenere presente che ricerca Azure non supportino tutti i tipi di dati presenti nell'origine dati. Vedere [i tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx) per l'elenco.

2. Creare un indice di ricerca di Azure il cui schema è compatibile con l'origine dati.
  
3. Creare un'origine dati di Azure ricerca come descritto in [Crea origine dati](#CreateDataSource).
  
4. Creare un indicizzatore Azure ricerca come descritto in [Creare indicizzatore](#CreateIndexer).

È consigliabile creare un indicizzatore per tutte le combinazioni di origine dati e indice di destinazione. È possibile creare più indicizzatori scrittura nella stessa posizione di indice e riutilizzare la stessa origine dati per più indicizzatori. Tuttavia, un indicizzatore può utilizzare solo un'origine dati in un momento e può scrivere solo in un unico indice. 

Dopo aver creato un indicizzatore, è possibile recuperare lo stato di esecuzione mediante l'operazione di [Ottenere lo stato dell'indicizzatore](#GetIndexerStatus) . È anche possibile eseguire un indicizzatore in qualsiasi momento (anziché o oltre eseguirlo periodicamente alla programmazione di un) utilizzando l'operazione [Dell'indicizzatore eseguire](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Creare l'origine dati ##

Risultati di ricerca di Azure, un'origine dati viene utilizzata con indicizzatori, fornire le informazioni di connessione per l'aggiornamento dei dati ad hoc o programmata di un indice di destinazione. È possibile creare una nuova origine dati all'interno di un servizio di ricerca di Azure mediante una richiesta HTTP POST.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile utilizzare inserire e specificare il nome dell'origine dati sull'URI. Se l'origine dati non esiste, verrà creato.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Nota**: il numero massimo di origini dati consentito varia a seconda del livello di prezzi. Il servizio gratuito consente tutte le origini dati 3. Servizio standard consente 50 origini dati. Per informazioni dettagliate, vedere [Limiti di servizio](search-limits-quotas-capacity.md) .

**Richiesta**

HTTPS è necessario per tutte le richieste di assistenza. Utilizzo del metodo di un POST o inserire, è possibile creare la richiesta **Crea origine dati** . Quando si usa POST, è necessario specificare un nome di origine dati nel corpo della richiesta e la definizione di origine dati. Con caricamento, il nome fa parte dell'URL. Se l'origine dati non esiste, viene creata. Se esiste già, viene aggiornato alla nuova definizione. 

Nome dell'origine dati deve essere minuscola, iniziare con una lettera o un numero, ha senza barre o punti e inferiore a 128 caratteri. Dopo il nome dell'origine dati a partire da una lettera o un numero, il resto del nome è possibile includere qualsiasi lettera, numero e trattini, purché i trattini non sono consecutivi. Per informazioni dettagliate, vedere [regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

Il `api-version` è necessario. La versione corrente non è `2015-02-28`.

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi. 

- `Content-Type`: Obbligatorio. Impostare questa opzione`application/json`
- `api-key`: Obbligatorio. Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta **Crea origine dati** deve includere un `api-key` intestazione impostare la chiave di amministratore (anziché una chiave di query). 
 
Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere sia il nome di servizio e `api-key` dal dashboard del servizio nel [Portale di Azure](https://portal.azure.com/). Vedere [creare un servizio di ricerca nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

<a name="CreateDataSourceRequestSyntax"></a>
**Nella sintassi corpo della convocazione**

Il corpo della convocazione contiene una definizione di origine dati, che include le credenziali per leggere i dati, come un dati facoltativi il rilevamento delle modifiche e criteri di rilevamento di eliminazione dei dati vengono utilizzati per identificare in modo efficiente modificati o eliminati dati nell'origine dati quando si utilizza un indicizzatore programmato periodicamente tipo di origine dati. 

La sintassi per strutturare payload richiesta è come indicato di seguito. Una richiesta di esempio è disponibili ulteriori in questo argomento.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Richiesta contiene le proprietà seguenti: 

- `name`: Obbligatorio. Il nome dell'origine dati. Un nome di origine dati deve solo contengono le lettere minuscole, cifre o trattini, non è possibile iniziare o terminare con trattini ed è limitata a 128 caratteri.
- `description`: Descrizione facoltativa. 
- `type`: Obbligatorio. Deve essere uno dei tipi di origine dati supportati:
    - `azuresql`-Database SQL di azure o SQL Server in macchine virtuali di Azure
    - `documentdb`-DocumentDB azure
    - `azureblob`-Archiviazione Blob azure
    - `azuretable`-Archiviazione tabelle azure
- `credentials`:
    - Necessari `connectionString` proprietà specifica la stringa di connessione per l'origine dati. Il formato della stringa di connessione dipende dal tipo di origine dati: 
        - Per SQL Azure, questa è la stringa di connessione di SQL Server comune. Se si usa il portale di Azure per recuperare la stringa di connessione, utilizzare la `ADO.NET connection string` opzione.
        - Per DocumentDB, la stringa di connessione deve essere nel formato seguente: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Tutti i valori necessari. È possibile trovarle nel [portale di Azure](https://portal.azure.com/).  
        - Per Blob Azure e archiviazione tabella, questa è la stringa di connessione account lo spazio di archiviazione. Il formato è descritto [di seguito](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). È necessario HTTPS endpoint protocollo.  
- `container`, richiesta: specifica i dati da un indice utilizzando il `name` e `query` proprietà: 
    - `name`Obbligatorio:
        - SQL Azure: specifica la tabella o la visualizzazione. È possibile utilizzare nomi completi di schema, ad esempio `[dbo].[mytable]`.
        - DocumentDB: specifica l'insieme. 
        - Archiviazione Blob Azure: Specifica il contenitore di spazio di archiviazione.
        - Archivio tabelle Azure: consente di specificare il nome della tabella. 
    - `query`, facoltativo:
        - DocumentDB: consente di specificare una query che semplifica il layout del documento un JSON non autorizzato in uno schema flat Azure ricerca è possibile indicizzare.  
        - Archiviazione Blob Azure: consente di specificare una cartella virtuale all'interno del contenitore di blob. Ad esempio, per percorso blob `mycontainer/documents/blob.pdf`, `documents` possono essere utilizzati come cartella virtuale.
        - Archivio tabelle Azure: consente di specificare una query che filtra il set di righe da importare.
        - SQL Azure: query non è supportata. Se occorre questa funzionalità, votare per [il suggerimento](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- Facoltativa `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` di seguito sono descritte le proprietà.

<a name="DataChangeDetectionPolicies"></a>
**Criteri di rilevamento di modifica dei dati**

Lo scopo di un criterio di rilevamento modifiche di dati è in modo efficiente identificare gli elementi di dati modificati. Criteri supportati variano in base al tipo di origine dati. Nelle sezioni seguenti descrivono ogni criterio. 

***Criteri di rilevamento delle modifiche limite massimo*** 

Utilizzare questo criterio quando l'origine dati contiene una colonna o una proprietà che soddisfa i criteri seguenti:
 
- Tutti gli inserimenti specificare un valore per la colonna. 
- Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna. 
- Il valore della colonna aumenta con ogni modifica apportata.
- Query che utilizzano una clausola di filtro simile al seguente `WHERE [High Water Mark Column] > [Current High Water Mark Value]` possono essere eseguite in modo efficiente.

Ad esempio, quando si usa origini dati di SQL Azure, un indicizzate `rowversion` la colonna contiene candidato ideale per l'utilizzo con con i criteri di limite massimo. 

Questo criterio può essere specificato come indicato di seguito:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Quando si usano le origini dati DocumentDB, è necessario utilizzare la `_ts` proprietà forniti da DocumentDB. 

> [AZURE.NOTE] Quando si usa automaticamente le origini dati Blob Azure, ricerca Azure viene utilizzato un limite massimo Modifica criteri di rilevamento in base al timestamp dell'ultima modifica del blob. non è necessario specificare criteri di manualmente.   

***SQL integrata criteri di rilevamento delle modifiche***

Se il database SQL supporta [il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb933875.aspx), è consigliabile usare SQL integrato modificare verifica dei criteri. Questo criterio consente il rilevamento delle modifiche più efficiente e consente di ricerca di Azure identificare le righe eliminate senza che sia necessario avere una colonna espliciti "Elimina contorni" nello schema.

Il rilevamento delle modifiche integrata è supportato a partire con le versioni di database SQL Server seguenti: 
- SQL Server 2008 R2, se si usa SQL Server in macchine virtuali di Azure.
- Azure SQL Database V12, se si usa il Database di SQL Azure.  

Quando tramite criterio SQL integrato il rilevamento delle modifiche, non si specifica un criterio di rilevamento l'eliminazione di dati distinto: questo criterio è incorporato il supporto per l'identificazione di eliminare righe. 

Questo criterio può essere utilizzato solo con tabelle. non può essere utilizzato con le viste. È necessario attivare rilevamento delle modifiche per la tabella in uso prima di poter usare questo criterio. Per istruzioni, vedere [attivare e disattivare il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb964713.aspx) .    
 
Quando strutturare la richiesta **Crea origine dati** , i criteri di rilevamento delle modifiche SQL integrato può essere specificato come indicato di seguito:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Criteri di rilevamento l'eliminazione dei dati**

Lo scopo di un criterio di rilevamento l'eliminazione di dati è in modo efficiente identificare gli elementi di dati eliminati. Attualmente, gli unici criteri supportati sono i `Soft Delete` criteri, che consente di identificare gli elementi eliminati in base al valore di una `soft delete` colonna o una proprietà nell'origine dati. Questo criterio può essere specificato come indicato di seguito:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**Nota:** Sono supportate solo le colonne con stringa, integer o valori booleani. Il valore utilizzato come `softDeleteMarkerValue` deve essere una stringa, anche se la colonna corrispondente contiene numeri interi o valori booleani. Ad esempio, se il valore che compare nell'origine dati è 1, utilizzare `"1"` come la `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Esempi di richieste di corpo**

Se si prevede di usare l'origine dati con un indicizzatore che viene eseguito su una pianificazione, in questo esempio viene illustrato come specificare i criteri di rilevamento di modifica ed eliminazione: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Se si intende solo utilizzare l'origine dati per copia unica dei dati, è possono omettere i criteri:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Risposta**

Per una richiesta di esito negativo: 201 creato. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Aggiornare l'origine dati ##

È possibile aggiornare un'origine dati esistente con una richiesta HTTP inserire. Specificare il nome dell'origine dati per l'aggiornamento su richiesta URI:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. [Controllo delle versioni ricerca Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) dettagli e ulteriori informazioni sulle versioni alternative.

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Richiesta**

La sintassi di corpo della convocazione è la stessa [richieste Crea origine dati](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Alcune proprietà non possono essere aggiornati su un'origine dati esistente. Ad esempio, non è possibile modificare il tipo di un'origine dati esistente.  

> [AZURE.NOTE] Se non si desidera modificare la stringa di connessione per un'origine dati esistente, è possibile specificare il valore letterale `<unchanged>` per la stringa di connessione. Questa funzionalità è utile nelle situazioni in cui è necessario aggiornare i dati di origine ma non si ha accesso appropriato per la stringa di connessione poiché si tratta di dati di protezione.

**Risposta**

Per una richiesta di esito negativo: 201 creato se una nuova origine dati è stato creato e 204 Nessun contenuto se è stata aggiornata un'origine dati esistente.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Origini dati di elenco ##

L'operazione di **Origini dati elenco** restituisce un elenco delle origini dati nel servizio di ricerca di Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Per una richiesta di esito negativo: 200 OK.

Ecco un corpo di risposta di esempio:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Si noti che è possibile filtrare la risposta per difetto a solo le proprietà che interessa. Ad esempio, se si desidera solo un elenco di nomi di origine dati, utilizzare OData `$select` opzione di query:

    GET /datasources?api-version=205-02-28&$select=name

In questo caso, la risposta dell'esempio precedente risulta come segue: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Questa è una tecnica utile per salvare la larghezza di banda se si dispone di molte origini dati nel servizio di ricerca.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Ottenere l'origine dati ##

L'operazione di **Ottenere origine dati** Ottiene la definizione dell'origine dati da Azure ricerca.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

La risposta è analoga a esempi di [richieste di esempio Crea origine dati](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Non si imposta la `Accept` intestazione richiesta per `application/json;odata.metadata=none` quando si chiama questa API come in questo modo impedirà il `@odata.type` attributo omettere dalla risposta e si non sarà possibile distinguere tra modifica dei dati e criteri di rilevamento di eliminazione di dati dei diversi tipi. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Eliminare l'origine dati ##

L'operazione di **Eliminazione di origine dati** rimuove un'origine dati dal servizio di ricerca di Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Se l'origine dati che si sta eliminando fa riferimento a qualsiasi indicizzatori, l'operazione di eliminazione verrà completata. Tuttavia, si passerà indicizzatori in stato di errore dopo l'esecuzione successiva.  

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 204 Nessun contenuto viene restituito per ottenere una risposta corretta.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Creare indicizzatore ##

È possibile creare un nuovo indicizzatore all'interno di un servizio di ricerca di Azure mediante una richiesta HTTP POST.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile utilizzare inserire e specificare il nome dell'origine dati sull'URI. Se l'origine dati non esiste, verrà creato.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] Il numero massimo di indicizzatori consentito varia in base prezzi livello. Il servizio gratuito consente indicizzatori fino a 3. Servizio standard consente 50 indicizzatori. Per informazioni dettagliate, vedere [Limiti di servizio](search-limits-quotas-capacity.md) .

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.


<a name="CreateIndexerRequestSyntax"></a>
**Nella sintassi corpo della convocazione**

Il corpo della convocazione contiene una definizione dell'indicizzatore che specifica l'origine dati e l'indice di destinazione per l'indicizzazione, nonché facoltativo pianificare e parametri. 


La sintassi per strutturare payload richiesta è come indicato di seguito. Una richiesta di esempio è disponibili ulteriori in questo argomento.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Pianificazione dell'indicizzatore**

Un indicizzatore possibile specificare una pianificazione. Se è presenta una pianificazione, l'indicizzatore eseguiranno periodicamente in base alla pianificazione. Programmazione sono previsti gli attributi seguenti:

- `interval`: Obbligatorio. Viene eseguito un valore di durata che specifica un intervallo o un periodo di indicizzatore. Intervallo minimo consentito è 5 minuti; più lunga è un giorno. È necessario essere formattato come un valore di "dayTimeDuration" XSD (un sottoinsieme di un valore di [Durata ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) con restrizioni). Il modello per questa operazione è: `"P[nD][T[nH][nM]]"`. Esempi: `PT15M` per ogni 15 minuti, `PT2H` per ogni 2 ore. 

- `startTime`: Obbligatorio. Una valore datetime UTC quando l'indicizzatore inizio per l'esecuzione. 

**Parametri dell'indicizzatore**

Un indicizzatore possibile specificare diversi parametri che determinano il comportamento. Tutti i parametri sono facoltativi.  

- `maxFailedItems`: Il numero di elementi che non possono essere indicizzate prima un'esecuzione dell'indicizzatore viene considerata un errore. Il valore predefinito è 0. Informazioni sugli elementi viene restituite dall'operazione di [Ottenere lo stato dell'indicizzatore](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: Il numero di elementi che potrebbe non riuscire a indicizzare ogni batch prima di eseguire un indicizzatore è considerato un errore. Il valore predefinito è 0.

- `base64EncodeKeys`: Specifica o meno tasti documento saranno codificata base 64. Ricerca Azure limitazioni utilizzando caratteri che possono essere presenti in una chiave di documento. Tuttavia, i valori nei dati di origine possono contenere caratteri non validi. Se è necessario indicizzare tali valori come chiavi di documento, questo flag può essere impostato su true. Il valore predefinito è `false`.

- `batchSize`: Specifica il numero di elementi che vengono letti dall'origine dati e indicizzati come un unico batch per migliorare le prestazioni. Il valore predefinito dipende dal tipo di origine dati: è 1000 per SQL Azure e DocumentDB e 10 per l'archiviazione Blob Azure.

**Mapping dei campi**

È possibile utilizzare i mapping dei campi per associare il nome di un campo nell'origine dati a un nome di campo diverso nell'indice di destinazione. Si consideri ad esempio una tabella di origine con un campo `_id`. Ricerca Azure non consente il nome di un campo a partire da un carattere di sottolineatura, in modo che il campo deve essere rinominato. Questa operazione può essere eseguita tramite il `fieldMappings` proprietà dell'indicizzatore come descritto di seguito: 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

È possibile specificare più mapping dei campi: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

I nomi dei campi di origine e destinazione sono senza maiuscole/minuscole.

<a name="FieldMappingFunctions"></a>
***Funzioni di mapping dei campi***

Mapping dei campi può anche essere utilizzato per trasformare i valori dei campi di origine utilizzando *le funzioni di mapping*.

Un solo questo tipo di funzione è attualmente supportato: `jsonArrayToStringCollection`. Analizza un campo che contiene una stringa formattata come matrice JSON in un campo Collection(Edm.String) l'indice di destinazione. Destinato per l'utilizzo con indicizzatore SQL Azure in particolare, poiché SQL non dispone di un tipo di dati nativi insieme. Può essere utilizzato come indicato di seguito: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Ad esempio, se il campo di origine contiene la stringa `["red", "white", "blue"]`, quindi il campo di destinazione di tipo `Collection(Edm.String)` verrà popolata con i tre valori `"red"`, `"white"` e `"blue"`.

Si noti che la `targetFieldName` proprietà è facoltativa. Se omesso, il `sourceFieldName` valore viene utilizzato. 

<a name="CreateIndexerRequestExamples"></a>
**Esempi di richieste di corpo**

Nell'esempio seguente viene creato un indicizzatore che consente di copiare i dati della tabella a cui fa riferimento il `ordersds` per le origini dati per il `orders` indice in una pianificazione che avvia il 1 gennaio 2015 UTC ed esegue ogni ora. Ogni chiamata indicizzatore verrà eseguita correttamente se non più di 5 elementi non riesce a indicizzare ogni batch e non più di 10 elementi non riesce a indicizzare totale. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Risposta**

201 creato per una richiesta di esito negativo.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Aggiornamento dell'indicizzatore ##

È possibile aggiornare un indicizzatore esistente utilizzando una richiesta HTTP inserire. Specificare il nome dell'indicizzatore per l'aggiornamento su richiesta URI:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Il `api-version` è necessario. La versione corrente non è `2015-02-28`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Richiesta**

La sintassi di corpo della convocazione è la stessa [indicizzatore creare richieste](#CreateIndexerRequestSyntax).

**Risposta**

Per una richiesta di esito negativo: 201 creato se un nuovo indicizzatore è stato creato e 204 Nessun contenuto se è stato aggiornato un indicizzatore esistente.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Elenco indicizzatori ##

L'operazione **Elenco indicizzatori** restituisce l'elenco di indicizzatori nel servizio di ricerca di Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. [Controllo delle versioni ricerca Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) dettagli e ulteriori informazioni sulle versioni alternative.

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Per una richiesta di esito negativo: 200 OK.

Ecco un corpo di risposta di esempio:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Si noti che è possibile filtrare la risposta per difetto a solo le proprietà che interessa. Ad esempio, se si desidera solo un elenco di nomi di indicizzatore, utilizzare OData `$select` opzione di query:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

In questo caso, la risposta dell'esempio precedente risulta come segue: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Questa è una tecnica utile per salvare la larghezza di banda se si dispone di numerose indicizzatori nel servizio di ricerca.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Ottenere indicizzatore ##

L'operazione di **Ottenere indicizzatore** Ottiene la definizione dell'indicizzatore dalla ricerca Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

La risposta è analoga a esempi di [richieste di esempio creare indicizzatore](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Eliminare indicizzatore ##

L'operazione di **Eliminazione indicizzatore** rimuove indicizzatore dal servizio di ricerca di Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando si elimina un indicizzatore, le esecuzioni indicizzatore in corso in tale momento verranno eseguito fino al completamento, ma non verranno programmate ulteriormente esecuzioni. Tentativo di utilizzare un indicizzatore inesistente verrà generato il codice di stato HTTP 404 non trovato. 
 
Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 204 Nessun contenuto viene restituito per ottenere una risposta corretta.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Eseguire indicizzatore ##

Oltre a eseguire periodicamente in base alla pianificazione, un indicizzatore può anche essere richiamato su richiesta tramite l'operazione **Dell'indicizzatore eseguire** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 202 accettato viene restituito per ottenere una risposta corretta.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatore ##

L'operazione di **Ottenere lo stato dell'indicizzatore** recupera la cronologia di stato e l'esecuzione corrente di un indicizzatore: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 200 OK per ottenere una risposta corretta.

Corpo della risposta informazioni di stato di integrità indicizzatore generale, l'ultima chiamata indicizzatore, nonché la cronologia di recente chiamate indicizzatore (se presente). 

Un corpo di risposta di esempio è simile alla seguente: 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**Stato dell'indicizzatore**

Stato di indicizzatore può essere uno dei seguenti valori:

- `running`indica che l'indicizzatore è in esecuzione normalmente. Si noti che alcune delle esecuzioni indicizzatore non ancora funziona correttamente, pertanto è consigliabile verificare la `lastResult` anche proprietà. 

- `error`indica che l'indicizzatore ha rilevato un errore che non possa essere corretti senza intervento. Ad esempio le credenziali di origine dati sia scaduto o lo schema dell'origine dati o dell'indice di destinazione è stato modificato in un sostanziale modo. 

**Risultato dell'esecuzione dell'indicizzatore**

Un risultato dell'esecuzione dell'indicizzatore contiene informazioni sull'esecuzione di un singolo indicizzatore. Il risultato più recente è resi disponibili come la `lastResult` proprietà dello stato dell'indicizzatore. Altri risultati recenti, se presenti, vengono restituiti come la `executionHistory` proprietà dello stato dell'indicizzatore. 

Risultato dell'esecuzione dell'indicizzatore contiene le proprietà seguenti: 

- `status`: lo stato di un'esecuzione. Per informazioni dettagliate, vedere [Lo stato di esecuzione dell'indicizzatore](#IndexerExecutionStatus) sotto. 

- `errorMessage`: messaggio di errore per un ha avuto esito negativo. 

- `startTime`: l'ora UTC quando l'esecuzione avviato.

- `endTime`: l'ora UTC al termine di questa esecuzione. Questo valore non è impostato se l'esecuzione è ancora in corso.

- `errors`: una matrice di errori a livello di elemento, se presenti. Ogni voce contiene una chiave di documento (`key` proprietà) e un messaggio di errore (`errorMessage` proprietà). 

- `itemsProcessed`: il numero di origini dati elementi (ad esempio righe delle tabelle) dell'indicizzatore tentato di indicizzare durante l'esecuzione. 

- `itemsFailed`: il numero di elementi che non è riuscita durante l'esecuzione.  
 
- `initialTrackingState`: sempre `null` per la prima esecuzione di indicizzatore, o se i dati di modifica criteri di verifica non è attivato per l'origine dati utilizzata. Se questo criterio è attivato, in esecuzioni successive questo valore indica il primo (minimo) il rilevamento delle modifiche valore elaborata l'esecuzione. 

- `finalTrackingState`: sempre `null` se i dati modificano i criteri di verifica non è attivata l'origine dati utilizzata. In caso contrario, indica l'ultima (massimo) rilevamento valore elaborata correttamente per l'esecuzione. 

<a name="IndexerExecutionStatus"></a>
**Stato di esecuzione dell'indicizzatore**

Stato di esecuzione dell'indicizzatore acquisisce lo stato di esecuzione di un singolo indicizzatore. È possibile che i valori seguenti:

- `success`indica che l'esecuzione dell'indicizzatore è stata completata.

- `inProgress`indica che l'esecuzione dell'indicizzatore è in corso. 

- `transientFailure`indica che un'esecuzione dell'indicizzatore non è riuscita. Vedere `errorMessage` proprietà per informazioni dettagliate. Se il problema potrebbe o potrebbe non richiedono l'intervento per la risoluzione: ad esempio, risoluzione dei problemi di compatibilità un schema tra l'origine dati e l'indice di destinazione richiede azione dell'utente, mentre un tempo di inattività di origine dati temporanei non. Chiamate indicizzatore continueranno per pianificazione, se presenta. 

- `persistentFailure`indica che l'indicizzatore non è riuscita in modo che richiede l'intervento. Interrompere le esecuzioni indicizzatore pianificata. Dopo aver risolto il problema, utilizzare Reimposta indicizzatore API per riavviare le esecuzioni pianificate. 

- `reset`indica che l'indicizzatore è stati opportunamente reimpostato da una chiamata all'API di indicizzatore reimpostare (vedere sopra). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Reimpostazione dell'indicizzatore ##

L'operazione di **Reimpostazione dell'indicizzatore** Reimposta il rilevamento delle modifiche è associato l'indicizzatore stato. In questo modo è possibile impostare un trigger da zero la reindicizzazione (ad esempio, se lo schema di origine dati è stati modificati) o per modificare i criteri di rilevamento modifiche di dati per un'origine dati associato dell'indicizzatore.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

Il `api-version` è necessario. La versione di anteprima `2015-02-28-Preview`. 

Il `api-key` deve essere una chiave di amministratore (anziché una chiave di query). Fare riferimento alla sezione autenticazione in [API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per ulteriori informazioni sui tasti. [Creare un servizio di ricerca nel portale di](search-create-service-portal.md) viene illustrato come ottenere l'URL del servizio e proprietà chiave utilizzate per la richiesta.

**Risposta**

Codice di stato: 204 Nessun contenuto per ottenere una risposta corretta.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati SQL e i tipi di dati ricerca Azure ##

<table style="font-size:12">
<tr>
<td>Tipo di dati SQL</td>  
<td>Tipi di campo consentiti indice di destinazione</td>
<td>Note</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>margine di flessibilità real,</td>
<td>Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, denaro<br>decimale<br>numerico
</td>
<td>Edm.String</td>
<td>Ricerca Azure non supporta la conversione di tipi decimali in EDM perché questo comporterebbe la perdita di precisione
</td>
</tr>
<tr>
<td>Char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(EDM.String)</td>
<td>Vedere [Le funzioni di Mapping di campo](#FieldMappingFunctions) in questo documento per informazioni dettagliate su come trasformare una colonna di stringa in un Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, data, datetimeoffset</td>
<td>DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>area geografica</td>
<td>Edm.GeographyPoint</td>
<td>Sono supportate solo le istanze di area geografica di tipo POINT con 4326 SRID (ovvero l'impostazione predefinita)</td>
</tr>
<tr>
<td>RowVersion</td>
<td>N/D</td>
<td>Colonne di versione di riga non possono essere memorizzate nell'indice di ricerca, ma può essere usate per il rilevamento delle modifiche</td>
</tr>
<tr>
<td>ora, timespan<br>binario, varbinary, image<br>XML, geometria, tipi di CLR</td>
<td>N/D</td>
<td>Non è supportata</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati ricerca Azure ##

<table style="font-size:12">
<tr>
<td>Tipo di dati JSON</td> 
<td>Tipi di campo consentiti indice di destinazione</td>
<td>Note</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Numeri integrali</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Numeri a virgola mobile</td>
<td>Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>stringa</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>tipi di matrici di primitiva, ad esempio ["", "b", "c"]</td>
<td>Collection(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Stringhe che rappresentano date</td>
<td>DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Oggetti point GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Punti di GeoJSON sono oggetti JSON nel formato seguente: {"tipo": "Decimale", "coordinate": [lungo, lat]} </td>
</tr>
<tr>
<td>Altri oggetti JSON</td>
<td>N/D</td>
<td>Non è supportato. Ricerca Azure supporta al momento solo i tipi di base e insiemi di stringhe</td>
</tr>
</table>