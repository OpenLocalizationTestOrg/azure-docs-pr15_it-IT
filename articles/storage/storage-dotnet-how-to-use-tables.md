<properties
    pageTitle="Guida introduttiva a archiviazione tabella Azure tramite .NET | Microsoft Azure"
    description="Archiviare dati strutturati nel cloud tramite archivio tabelle Azure, un archivio di dati NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>Guida introduttiva a archiviazione tabella Azure con .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Archivio tabelle Azure è un servizio che archivia dati strutturati NoSQL nel cloud. Archiviazione tabelle è un archivio di chiave/attributo con uno schema schemaless. Perché l'archiviazione tabella è schemaless, è facile adattare i dati come le esigenze dei evolve applicazione. Accesso ai dati è veloce e conveniente per tutti i tipi di applicazioni. Archiviazione tabelle è in genere notevolmente inferiori a costo tradizionale SQL per indicare i volumi simili dei dati.

È possibile utilizzare lo spazio di archiviazione tabella per memorizzare flessibile set di dati, ad esempio dati utente per le applicazioni web, rubriche, informazioni sul dispositivo e qualsiasi altro tipo di metadati richiesti dal servizio. È possibile archiviare qualsiasi numero di entità in una tabella e un account di archiviazione può contenere qualsiasi numero di tabelle, fino al limite di capacità dell'account di archiviazione.

### <a name="about-this-tutorial"></a>Su questa esercitazione

In questa esercitazione viene illustrato come scrivere codice .NET per alcuni scenari comuni di utilizzo dell'archiviazione tabella Azure, tra cui la creazione ed eliminazione di una tabella e l'inserimento, aggiornamento, eliminazione e query sui dati di tabella.

**Durata stimata per completare:** 45 minuti

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Raccolta di Client di archiviazione Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [account di archiviazione Azure](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Ulteriori esempi

Per ulteriori esempi sull'utilizzo di archiviazione, vedere [Guida introduttiva di archivio tabelle Azure in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). È possibile scaricare l'applicazione di esempio ed eseguirlo o esplorare il codice nella GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Aggiungere le dichiarazioni di nomi

Aggiungere quanto segue `using` le istruzioni nella parte superiore di `program.cs` file:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Creare il client di servizi di tabella

La classe **CloudTableClient** consente di recuperare entità archiviate in archiviazione tabella e tabelle. Ecco un modo per creare il client di servizi:

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

A questo punto si è pronti scrivere codice che legge i dati da e scrive dati in archiviazione tabella.

## <a name="create-a-table"></a>Creare una tabella

In questo esempio viene illustrato come creare una tabella se non esiste già:

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Eseguire il mapping a C entità\# oggetti utilizzando una classe personalizzata derivano dalla **TableEntity**. Per aggiungere un'entità a una tabella, creare una classe che definisce le proprietà dell'entità. Il codice seguente definisce una classe di entità che utilizza nome del cliente come la chiave di riga e il cognome come chiave di partizione. Insieme, partizione un'entità e chiave riga identificano l'entità nella tabella. È possibile effettuarvi entità con la stessa chiave partizione modo più rapido rispetto a quelle ottenute con i tasti partizione diversa, ma usando i tasti di diversi partizione consente una maggiore scalabilità di operazioni parallele.  Per qualsiasi proprietà che dovrà essere archiviato nel servizio di tabella, la proprietà deve essere una proprietà pubblica di un tipo supportato esposti entrambe `get` e `set`.
Inoltre, il tipo di entità *necessario* esporre un costruttore senza parametri.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Operazioni di tabella che includono entità vengono eseguite tramite l'oggetto **CloudTable** creata in precedenza nella sezione "Creare una tabella". Operazione da eseguire è rappresentato da un oggetto **TableOperation** .  Nell'esempio seguente viene illustrata la creazione dell'oggetto **CloudTable** e quindi un oggetto **CustomerEntity** .  Per preparare l'operazione, viene creato un oggetto **TableOperation** per inserire l'entità cliente nella tabella.  Infine, l'operazione viene eseguita chiamando **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità

È possibile inserire un batch di entità in una tabella in un'operazione di scrittura. Alcune altre note sulle operazioni batch:

-  È possibile eseguire aggiornamenti, Elimina e inserisce nella stessa operazione unico batch.
-  Un'unica operazione possibile includere fino a 100 entità.
-  Tutte le entità in un'unica operazione devono avere la stessa chiave di partizione.
-  Sebbene sia possibile eseguire una query come un'operazione batch, deve essere l'unica operazione nel batch.

<!-- -->
Nell'esempio seguente crea due oggetti entità e aggiunti al **TableBatchOperation** utilizzando il metodo di **inserimento** . Quindi, **CloudTable.Execute** è chiamato per eseguire l'operazione.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione

Per eseguire una query per tutte le entità in una partizione di una tabella, utilizzare un oggetto **TableQuery** .
Nell'esempio seguente specifica un filtro per entità dove "Rossi" sono il tasto partizione. Questo esempio stampa i campi di ciascuna entità nei risultati della query alla console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperare un intervallo di entità in una partizione

Se non si desidera eseguire query su tutte le entità di una partizione, è possibile specificare un intervallo combinando i filtri chiave partizione con un filtro di chiave di riga. Nell'esempio seguente usa due filtri per ottenere tutte le entità partizione 'Rossi' in cui inizia con una lettera precedente a "E" dell'alfabeto la chiave di riga (nome) e quindi stampa i risultati della query.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità

È possibile scrivere una query per recuperare un'entità singola, specifica. Il codice seguente usa **TableOperation** per specificare il cliente "Ben Smith".
Questo metodo restituisce una sola entità invece di una raccolta e il valore restituito in **TableResult.Result** è un oggetto **CustomerEntity** .
Impostazione di tasti partizione e riga in una query è il modo più rapido per recuperare una singola entità dal servizio di tabella.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Sostituire un'entità

Per aggiornare un'entità, recuperare dal servizio di tabella, modificare l'oggetto entità e quindi salvare le modifiche al servizio tabella. Il codice seguente viene modificato il numero di telefono del cliente esistente. Invece di chiamare **inserire**, questo codice utilizza **sostituire**. In questo modo l'entità da sostituire completamente sul server, a meno che l'entità nel server è stato modificato dopo che è stata recuperata, nel qual caso l'operazione non riesce.  Questo errore è impedire all'applicazione di sovrascrivere accidentalmente una modifica apportata tra l'aggiornamento e il recupero da un altro componente di un'applicazione.  Il corretto svolgimento di questo errore consiste nel recuperare nuovamente l'entità, apportare le modifiche (se ancora valido) e quindi eseguire un'altra operazione di **sostituzione** .  La sezione seguente illustra come evitare questo comportamento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Inserimento o sostituire un'entità

**Sostituire** operazioni avrà esito negativo se l'entità è stata modificata dal recuperarlo dal server.  Inoltre, è necessario recuperare l'entità dal server prima di ordine per l'operazione di **sostituzione** venga eseguita correttamente.
In alcuni casi, tuttavia, non si conosce l'entità esiste nel server e i valori correnti archiviati al suo interno vengono irrilevanti. L'aggiornamento devono essere sovrascritti tutti.  A tale scopo, utilizzare un'operazione **InsertOrReplace** .  Questa operazione inserisce l'entità se non esiste o sostituisce in caso affermativo, indipendentemente da quando è stata effettuata l'ultimo aggiornamento.  Nell'esempio seguente, l'entità cliente per Ben Smith viene ancora recuperato, ma viene quindi salvata sul server tramite **InsertOrReplace**.  Verranno sovrascritti gli aggiornamenti eseguiti per l'entità tra le operazioni di aggiornamento e recupero.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Un sottoinsieme di entità proprietà della query

Una query di tabella è possibile recuperare solo alcune proprietà di un'entità anziché tutte le proprietà di entità. Questa tecnica, denominata proiezione, riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. La query nel codice seguente restituisce solo gli indirizzi di posta elettronica di entità nella tabella. In tal caso, utilizzando una query di **DynamicTableEntity** e anche a **EntityResolver**. È possibile ottenere ulteriori informazioni proiezione nel [blog di proiezione di Query e Introduzione a Upsert post][]. Si noti che proiezione non è supportata emulatore archivio locale, in modo che il codice viene eseguita solo quando si usa un account del servizio di tabella.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Eliminare un'entità

È possibile eliminare un'entità facilmente dopo avere recuperato, usando lo stesso modello visualizzato per l'aggiornamento di un'entità.  Il codice seguente recupera ed elimina un'entità cliente.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Eliminare una tabella

Infine, nell'esempio seguente elimina una tabella da un account di archiviazione. Una tabella in cui è stata eliminata non saranno disponibile per creare di nuovo per un periodo di tempo dopo l'eliminazione.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperare entità nelle pagine in modo asincrono

Se si sta leggendo un numero elevato di entità e si desidera processo/Visualizza entità come vengono recuperate anziché in attesa per tutti i restituire, è possibile recuperare entità utilizzando una query segmentata. In questo esempio viene illustrato come restituire i risultati in pagine utilizzando il modello asincrono attesa in modo che non l'esecuzione viene bloccata mentre si sta attendendo per un elevato numero di risultati da restituire. Per altre informazioni sull'utilizzo del modello asincrono Await in .NET, vedere [programmazione asincrona con asincrono e Await (c# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione tabella, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse:

- Vedere altri esempi di spazio di archiviazione tabella nella [Guida introduttiva di archivio tabelle Azure in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Visualizzare la documentazione di riferimento servizio tabella per informazioni dettagliate sulle API disponibili:
    - [Raccolta di Client lo spazio di archiviazione per riferimento .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Guida di riferimento all'API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Informazioni su come semplificare il codice che scritto per gestire lo spazio di archiviazione di Azure mediante [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Consente di visualizzare altre funzionalità Guide per informazioni sulle altre opzioni per l'archiviazione dei dati di Azure.
    - [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md) per archiviare i dati non strutturati.
    - [Connetti a Database SQL di tramite .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) per memorizzare dati relazionali.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Post di blog Upsert introduzione e proiezione di Query]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
