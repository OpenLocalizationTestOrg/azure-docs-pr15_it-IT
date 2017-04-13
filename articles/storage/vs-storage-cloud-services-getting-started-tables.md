<properties
    pageTitle="Introduzione all'archiviazione tabelle e Visual Studio connessi servizi (servizi cloud) | Microsoft Azure"
    description="Come iniziare a utilizzare archivio tabelle Azure in un progetto di servizio cloud in Visual Studio dopo la connessione a un account di archiviazione utilizzando Visual Studio connessi servizi"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Guida introduttiva di archiviazione tabelle Azure e Visual Studio connessi servizi (cloud services progetti)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

##<a name="overview"></a>Panoramica

In questo articolo viene descritto come iniziare a utilizzare archiviazione tabelle Azure in Visual Studio dopo aver creato o si desidera fare riferimento a un account di archiviazione Azure in un progetto di servizi cloud usando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** . L'operazione di **Aggiungere servizi connessi** installa i pacchetti NuGet appropriati per accedere all'archiviazione Azure del progetto e aggiunge la stringa di connessione per l'account di archiviazione per i file di configurazione del progetto.

Il servizio di archiviazione tabella Azure consente di memorizzare grandi quantità di dati strutturati. Il servizio è un archivio dati NoSQL che accetta chiamate autenticate da interne ed esterne cloud Azure. Tabelle Azure sono ideali per l'archiviazione dei dati strutturati e non relazionali.

Per iniziare, è innanzitutto necessario creare una tabella nel proprio account di archiviazione. Ecco come creare una tabella di Azure nel codice, nonché come eseguire operazioni di entità, ad esempio aggiunta, modifica, la lettura e lettura entità della tabella e tabella di base. Gli esempi sono scritti c\# codice e utilizzare la [libreria client lo spazio di archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Nota:** Alcune delle API che eseguono chiamate fuori allo spazio di archiviazione Azure sono asincrone. Per ulteriori informazioni, vedere [asincrono programmazione con asincrono e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Il codice seguente presuppone vengono utilizzati i metodi di programmazione asincrono.

- Per ulteriori informazioni su come modificare a livello di programmazione tabelle, vedere [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md) .
- Per informazioni generali sull'archiviazione di Azure, vedere [la documentazione di spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/) .
- Per informazioni generali sui servizi cloud Azure, vedere [la documentazione di servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) .
- Per ulteriori informazioni sulla programmazione di applicazioni ASP.NET, vedere [ASP.NET](http://www.asp.net) .

## <a name="access-tables-in-code"></a>Tabelle di Access nel codice

Per accedere a tabelle nei progetti di servizio cloud, è necessario includere gli elementi seguenti a qualsiasi file di origine c# di accedere all'archiviazione tabelle Azure.

1. Assicurarsi che le dichiarazioni di spazio dei nomi nella parte superiore del file c# includono queste istruzioni **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. È possibile ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice riportato di seguito per ottenere la stringa di connessione di spazio di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
> [AZURE.NOTE]  Utilizzare tutto il codice sopra davanti al codice negli esempi seguenti.

3. È possibile ottenere un oggetto **CloudTableClient** per fare riferimento gli oggetti della tabella nell'account di archiviazione.

         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. È possibile ottenere un oggetto di riferimento **CloudTable** per fare riferimento a una tabella specifica ed entità.

        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Creare una tabella nel codice

Per creare la tabella Azure, è sufficiente aggiungere una chiamata a **CreateIfNotExistsAsync** per il dopo aver ottenuto un oggetto **CloudTable** come descritto nella sezione "Tabelle nel codice di accesso".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità a una tabella, creare una classe che definisce le proprietà dell'entità. Il codice seguente definisce una classe di entità denominata **CustomerEntity** che utilizza nome del cliente come la chiave di riga e il cognome come chiave di partizione.

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

Vengono eseguite le operazioni di tabella che comportano entità usando l'oggetto **CloudTable** creata in precedenza in "accesso tabelle nel codice." L'oggetto **TableOperation** rappresenta l'operazione da eseguire. Nell'esempio seguente viene illustrato come creare un oggetto **CloudTable** e un oggetto **CustomerEntity** . Per preparare l'operazione, viene creata una **TableOperation** per inserire l'entità cliente nella tabella. Infine, l'operazione viene eseguita chiamando **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità

È possibile inserire più entità in una tabella in un'operazione di scrittura singola. Nell'esempio seguente crea due oggetti entità ("Jeff Smith" e "Ben Smith"), aggiunti a un oggetto **TableBatchOperation** utilizzando il metodo Inserisci e quindi viene avviata l'operazione chiamando **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Ottenere tutte le entità in una partizione

Per eseguire una query per tutte le entità in una partizione di una tabella, utilizzare un oggetto **TableQuery** . Nell'esempio seguente specifica un filtro per entità dove "Rossi" sono il tasto partizione. Questo esempio stampa i campi di ciascuna entità nei risultati della query alla console.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>Ottenere un'entità singola

È possibile scrivere una query per ottenere un'entità singola, specifica. Il codice seguente utilizza un oggetto **TableOperation** per specificare un cliente denominato "Ben Smith". Questo metodo restituisce una sola entità, anziché una raccolta e il valore restituito in **TableResult.Result** un oggetto **CustomerEntity** . Impostazione di tasti partizione e riga in una query è il modo più rapido per recuperare una singola entità dal servizio di **tabella** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminare un'entità
È possibile eliminare un'entità dopo averlo trovato. Il codice seguente per un'entità cliente "Ben Zaffaroni" e, se si trova, questo viene eliminato.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
