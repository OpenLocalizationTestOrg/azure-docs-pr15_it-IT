<properties
    pageTitle="Come utilizzare lo spazio di archiviazione tabella da Java | Microsoft Azure"
    description="Archiviare i dati strutturati nel cloud mediante archiviazione tabella Azure, un archivio di dati NoSQL."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>Come utilizzare lo spazio di archiviazione tabella da Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Questa guida illustra come eseguire gli scenari comuni utilizzando il servizio di archiviazione tabella Azure. Gli esempi scritto in linguaggio e usare [Azure archiviazione SDK per Java][]. Scenari coperti includono tabelle di **creazione**, **elenco**ed **eliminazione** , nonché **l'inserimento**, **query**, **la modifica**ed **eliminazione** entità in una tabella. Per ulteriori informazioni sulle tabelle, vedere la sezione [passaggi successivi](#Next-Steps) .

Nota: Un SDK è disponibile per gli sviluppatori che utilizzano lo spazio di archiviazione di Azure nei dispositivi Android. Per ulteriori informazioni, vedere [Azure SDK lo spazio di archiviazione per Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java

In questa Guida, si utilizzeranno le funzionalità di archiviazione che possono essere eseguite all'interno di un'applicazione Java locale o nel codice in esecuzione all'interno di un ruolo web o un ruolo di lavoro in Azure.

A tale scopo, è necessario installare Java Development Kit (JDK) e creare un account di archiviazione Azure nell'abbonamento Azure. Dopo avere fatto, è necessario verificare che il sistema di sviluppo soddisfi i requisiti minimi e le dipendenze elencate nell'archivio di [Azure archiviazione SDK per Java][] su GitHub. Se il sistema soddisfi questi requisiti, è possibile seguire le istruzioni per scaricare e installare le librerie di spazio di archiviazione di Azure per Java nel sistema da tale archivio. Dopo aver completato le attività, sarà possibile creare un'applicazione Java che utilizza gli esempi riportati in questo articolo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurare l'applicazione per accedere all'archiviazione tabella

Aggiungere le seguenti istruzioni di importazione nella parte superiore del file di linguaggio in cui si desidera utilizzare l'archiviazione di Microsoft Azure API per accedere a tabelle:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configurazione di una stringa di connessione di archiviazione Azure

Un client di archiviazione Azure utilizza una stringa di connessione di spazio di archiviazione per archiviare i punti finali e le credenziali per accedere ai servizi di gestione dati. Durante l'esecuzione di un'applicazione client, è necessario specificare la stringa di connessione di spazio di archiviazione nel formato seguente, utilizzando il nome del proprio account di archiviazione e la chiave primaria accesso per l'account di archiviazione riportata nel [Portale di Azure](https://portal.azure.com) per i valori di *nome account* e *AccountKey* . In questo esempio viene illustrato come dichiarare un campo statico per contenere la stringa di connessione:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

In un'applicazione in esecuzione all'interno di un ruolo di Microsoft Azure, questa stringa può essere archiviata nel file di configurazione del servizio, *ServiceConfiguration. cscfg*ed è possibile accedervi con una chiamata al metodo **RoleEnvironment.getConfigurationSettings** . Ecco un esempio di recupero la stringa di connessione da un elemento di **impostazione** denominato *StorageConnectionString* nel file di configurazione del servizio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Negli esempi seguenti presuppongono che sia stata utilizzata uno di questi due metodi per ottenere la stringa di connessione di spazio di archiviazione.

## <a name="how-to-create-a-table"></a>Procedura: creare una tabella

Un oggetto **CloudTableClient** consente di ottenere gli oggetti di riferimento per le tabelle ed entità. Il codice seguente viene creato un oggetto **CloudTableClient** e viene utilizzata per creare un nuovo oggetto **CloudTable** che rappresenta una tabella denominata "utenti". (Nota: esistono altri modi per creare gli oggetti **CloudStorageAccount** ; per ulteriori informazioni, vedere **CloudStorageAccount** nel [Riferimento SDK Client lo spazio di archiviazione di Azure].)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>Procedura: elencare le tabelle

Per ottenere un elenco di tabelle, chiamare il metodo **CloudTableClient.listTables()** per recuperare un elenco iterable di nomi di tabella.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>Procedura: aggiungere un'entità a una tabella

Eseguire il mapping entità agli oggetti Java utilizzando una classe personalizzata implementazione **TableEntity**. Per semplicità di utilizzo, la classe **TableServiceEntity** implementa **TableEntity** e utilizza riflesso per eseguire il mapping a metodi get e set denominati per le proprietà. Per aggiungere un'entità a una tabella, è necessario creare una classe che definisce le proprietà dell'entità. Il codice seguente definisce una classe di entità che utilizza nome del cliente come chiave di riga e il cognome come chiave di partizione. Insieme, partizione un'entità e chiave riga identificano l'entità nella tabella. È possibile effettuarvi entità con la stessa chiave di partizione più velocemente rispetto a quelle ottenute con i tasti partizione diversa.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Operazioni di tabella che comportano entità richiedono un oggetto **TableOperation** . Questo oggetto definisce l'operazione da eseguire su un'entità, può essere eseguita con un oggetto **CloudTable** . Il codice seguente crea una nuova istanza della classe **CustomerEntity** con alcuni archiviare i dati dei clienti. Il codice successivo chiama **TableOperation.insertOrReplace** per creare un oggetto **TableOperation** per inserire un'entità in una tabella e associa il nuovo **CustomerEntity** . Infine, il codice chiama il metodo **execute** sull'oggetto **CloudTable** che specifica la tabella "utenti" e il nuovo **TableOperation**, quindi inviare una richiesta al servizio di archiviazione per inserire la nuova entità cliente nella tabella "utenti" o sostituire l'entità se esiste già.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>Procedura: inserire un batch di entità

È possibile inserire un batch di entità al servizio di tabella in un'operazione di scrittura. Il codice seguente viene creato un oggetto **TableBatchOperation** , quindi aggiunge che tre inserire operazioni a tale. Ogni operazione di inserimento viene aggiunta la creazione di un nuovo oggetto entità, impostarne i valori e quindi chiamando il metodo **insert** sull'oggetto **TableBatchOperation** per associare un'operazione di inserimento di nuovo l'entità. Il codice chiama **eseguire** sull'oggetto **CloudTable** che specifica la tabella "utenti" e l'oggetto **TableBatchOperation** , che invia il batch di operazioni con tabelle per il servizio di archiviazione in una singola richiesta.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Alcuni aspetti da tenere presente nel batch operazioni:

- È possibile eseguire Inserisci fino a 100, eliminare, unire, sostituire, inserire o unire e inserire o sostituire le operazioni di qualsiasi combinazione in un unico batch.
- Un'operazione batch può avere un'operazione di recupero, se è l'unica operazione nel batch.
- Tutte le entità in un'unica operazione devono avere la stessa chiave di partizione.
- Un'operazione batch è limitata a un payload di dati di 4MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Procedura: recuperare tutte le entità di una partizione

Per eseguire una query per entità in una partizione di una tabella, è possibile utilizzare una **TableQuery**. Chiamare **TableQuery.from** per creare una query in una determinata tabella che restituisce un tipo di risultato specificato. Il codice seguente viene specificato un filtro per entità dove "Rossi" sono il tasto partizione. **TableQuery.generateFilterCondition** è un metodo di supporto per creare filtri per le query. Chiamare **nel punto in cui** il riferimento restituito dal metodo **TableQuery.from** per applicare il filtro per la query. Quando viene eseguita la query con una chiamata da **eseguire** sull'oggetto **CloudTable** , viene restituito un **iteratore** con il tipo di risultato **CustomerEntity** specificato. È quindi possibile utilizzare l' **iteratore** restituiti in una per ogni ciclo di utilizzare i risultati. Il codice visualizza i campi di ciascuna entità nei risultati della query alla console.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Procedura: recuperare un intervallo di entità in una partizione

Se non si desidera eseguire query su tutte le entità di una partizione, è possibile specificare un intervallo usando gli operatori di confronto in un filtro. Il codice seguente combina due filtri per ottenere tutte le entità partizione "Rossi" in cui la chiave di riga (nome) inizia con una lettera per lettera "E" dell'alfabeto. Vengono quindi i risultati della query. Se si utilizza l'entità aggiunta alla tabella nel batch di inserire sezione di questa Guida, solo due entità vengono restituite momento (Ben e Denise Smith); Jeff Smith non è incluso.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>Procedura: recuperare una singola entità

È possibile scrivere una query per recuperare un'entità singola, specifica. Il codice seguente chiama **TableOperation.retrieve** con riga e della chiave di partizione parametri chiave per specificare il cliente "Jeff Smith" anziché la creazione di un **TableQuery** e utilizzo di filtri per eseguire la stessa operazione. Quando viene eseguito, l'operazione recupera restituisce una sola entità invece di una raccolta. Il metodo **getResultAsType** viene eseguito il cast sul risultato per il tipo di destinazione di assegnazione, un oggetto **CustomerEntity** . Se questo tipo non è compatibile con la categoria specificata per la query, si avrà un'eccezione. Se non entità ha una partizione esatta e chiave riga corrispondono, viene restituito un valore null. Impostazione di tasti partizione e riga in una query è il modo più rapido per recuperare una singola entità dal servizio di tabella.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>Procedura: modificare un'entità

Per modificare un'entità, recuperare dal servizio di tabella, apportare le modifiche apportate all'oggetto entità e salvare le modifiche al servizio tabella con un'operazione di sostituzione o unione. Il codice seguente viene modificato il numero di telefono del cliente esistente. Invece di chiamare **TableOperation.insert** come abbiamo fatto per inserire, questo codice chiama **TableOperation.replace**. Il metodo **CloudTable.execute** chiama il servizio di tabella e viene sostituito l'entità, a meno che non un'altra applicazione sono stati modificati da dell'ora è stato recuperato dell'applicazione. In questo caso, viene generata un'eccezione e l'entità deve essere recuperato, modificato e salvato di nuovo. Questo modello di Riprova ottimistica è comune in un sistema di archiviazione distribuita.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>Procedura: un sottoinsieme di entità proprietà della Query

Una query in una tabella è possibile recuperare solo alcune proprietà di un'entità. Questa tecnica, denominata proiezione, riduce la larghezza di banda e migliorare le prestazioni di query, in particolare per entità di grandi dimensioni. La query nel codice seguente utilizza il metodo **select** per restituire solo gli indirizzi di posta elettronica di entità nella tabella. I risultati sono previste in una raccolta di **stringa** con l'aiuto di un **EntityResolver**, che effettua la conversione di tipo di entità restituite dal server. Altre informazioni sulle proiezione in [tabelle Azure: Introduzione a Upsert e proiezione di Query][]. Nota proiezione non è supportato emulatore archivio locale in modo che il codice viene eseguita solo quando si utilizza un account del servizio di tabella.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>Procedura: inserire o sostituire un'entità

Frequenza si desidera aggiungere un'entità a una tabella senza sapere se esiste già nella tabella. Un'operazione di inserimento o sostituzione consente di effettuare una singola richiesta che verrà inserito l'entità se non esiste o sostituire quello esistente, in caso affermativo. Basato su esempi precedenti, il codice seguente consente di inserire o sostituisce l'entità per "Walter arpa". Dopo aver creato una nuova entità, questo codice chiama il metodo **TableOperation.insertOrReplace** . Questo codice quindi chiama **eseguire** sull'oggetto **CloudTable** con la tabella e l'inserimento o sostituzione tabella come parametri. Per aggiornare solo una parte di un'entità, il metodo **TableOperation.insertOrMerge** può essere utilizzato invece. Nota che tale Inserisci-oppure-Sostituisci non è supportato in emulatore archivio locale, in modo che il codice viene eseguita solo quando si utilizza un account del servizio di tabella. Per informazioni sull'inserimento o sostituzione e Inserisci o unione in questo [tabelle Azure: Introduzione a Upsert e proiezione di Query][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>Procedura: eliminare un'entità

Dopo avere recuperato, è possibile eliminarlo facilmente un'entità. Una volta recuperato l'entità, chiamare **TableOperation.delete** con l'entità da eliminare. Chiamare quindi **eseguire** sull'oggetto **CloudTable** . Il codice seguente recupera ed elimina un'entità cliente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>Procedura: eliminare una tabella

Infine, il codice seguente elimina una tabella da un account di archiviazione. Una tabella che è stata eliminata non sarà possibile ricrearle per un periodo di tempo dopo l'eliminazione, in genere inferiore a 40 secondi.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione tabella, seguire questi collegamenti per informazioni su come eseguire attività di archiviazione più complesse.

- [Spazio di archiviazione di Azure SDK per Java][]
- [Spazio di archiviazione Azure Client SDK riferimento][]
- [API REST di archiviazione Azure][]
- [Blog del Team di archiviazione Azure][]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di linguaggio](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Spazio di archiviazione di Azure SDK per Java]: https://github.com/azure/azure-storage-java
[Spazio di archiviazione di Azure SDK per Android]: https://github.com/azure/azure-storage-android
[Spazio di archiviazione Azure Client SDK riferimento]: http://dl.windowsazure.com/storage/javadoc/
[API REST di archiviazione Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Tabelle Azure: Introduzione a Upsert e proiezione di Query]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
