<properties
    pageTitle="Come utilizzare lo spazio di archiviazione tabella (C++) | Microsoft Azure"
    description="Archiviare i dati strutturati nel cloud mediante archiviazione tabella Azure, un archivio di dati NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>Come utilizzare lo spazio di archiviazione tabella da C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica  
Questa guida è illustrano come eseguire scenari comuni tramite il servizio di archiviazione tabella Azure. Gli esempi sono scritte in C++ e utilizzano la [Libreria di Client di spazio di archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Gli scenari coperti includono **Creazione ed eliminazione di una tabella** e **usare l'entità della tabella**.

>[AZURE.NOTE] Questa guida è destinato alla libreria di Client lo spazio di archiviazione di Azure per C++ versione 1.0.0 e versioni successive. La versione consigliata è lo spazio di archiviazione Client raccolta 2.2.0, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Creare un'applicazione C++  
In questa Guida, si utilizzeranno le funzionalità di archiviazione che possono essere eseguite all'interno di un'applicazione C++. A tale scopo, è necessario installare la libreria di Client lo spazio di archiviazione di Azure per C++ e creare un account di archiviazione Azure nell'abbonamento Azure.  

Per installare la libreria di Client lo spazio di archiviazione di Azure per C++, è possibile utilizzare i metodi seguenti:

-   **Linux:** Seguire le istruzioni visualizzate nella pagina della [Raccolta di Client Azure lo spazio di archiviazione per il file Leggimi C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** In Visual Studio, fare clic su **Strumenti > Gestione di pacchetti NuGet > Console di gestione pacchetti**. Digitare il comando seguente nella [console di gestione di pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere INVIO.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurare l'applicazione per accedere all'archiviazione tabella  
Aggiungere che le seguenti istruzioni nella parte superiore del file C++ nel punto in cui si desidera utilizzare l'API dello spazio di archiviazione di Azure per accedere a tabelle include:  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione Azure  
Un client di archiviazione Azure utilizza una stringa di connessione di spazio di archiviazione per archiviare i punti finali e le credenziali per accedere ai servizi di gestione dati. Durante l'esecuzione di un'applicazione client, è necessario specificare la stringa di connessione di spazio di archiviazione nel formato seguente. Usare il nome del proprio account di archiviazione e il tasto di scelta di spazio di archiviazione per l'account di archiviazione riportata nel [Portale di Azure](https://portal.azure.com) per i valori di *nome account* e *AccountKey* . Per informazioni sull'account di archiviazione e i tasti di scelta, vedere [gli account di archiviazione su Azure](storage-create-storage-account.md). In questo esempio viene illustrato come dichiarare un campo statico per contenere la stringa di connessione:  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Per testare l'applicazione in un computer basato su Windows locale, è possibile utilizzare l' Azure [emulatore lo spazio di archiviazione](storage-use-emulator.md) che viene installato con [Azure SDK](https://azure.microsoft.com/downloads/). Emulatore lo spazio di archiviazione è un'utilità che simula i servizi di Blob Azure, coda e una tabella disponibili nei computer di sviluppo locale. Nell'esempio seguente viene illustrato come dichiarare un campo statico per contenere la stringa di connessione per l'archiviazione locale emulatore in:  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Per avviare l'emulatore archiviazione Azure, fare clic sul pulsante **Start** o premere il tasto Windows. Iniziare a digitare **Emulatore lo spazio di archiviazione di Azure**e quindi selezionare **Microsoft Azure archiviazione Emulator** dall'elenco di applicazioni.  

Negli esempi seguenti presuppongono che sia stata utilizzata uno di questi due metodi per ottenere la stringa di connessione di spazio di archiviazione.  

## <a name="retrieve-your-connection-string"></a>Recuperare la stringa di connessione  
È possibile utilizzare la classe **cloud_storage_account** per rappresentare le informazioni sull'account di archiviazione. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione dello spazio di archiviazione, è possibile utilizzare il metodo di analisi.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

È possibile ottenere un riferimento a una classe **cloud_table_client** , successivamente, come è possibile ottenere gli oggetti di riferimento per le tabelle ed entità memorizzate all'interno del servizio di archiviazione tabella. Il codice seguente crea un oggetto **cloud_table_client** utilizzando l'oggetto account lo spazio di archiviazione che è recuperati in precedenza:  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Creare una tabella
Un oggetto **cloud_table_client** consente di ottenere gli oggetti di riferimento per le tabelle ed entità. Il codice seguente viene creato un oggetto **cloud_table_client** e viene utilizzata per creare una nuova tabella.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità a una tabella, creare un nuovo oggetto **table_entity** e passare a **table_operation::insert_entity**. Il codice seguente utilizza nome del cliente come la chiave di riga e il cognome come chiave di partizione. Insieme, partizione un'entità e chiave riga identificano l'entità nella tabella. È possibile effettuarvi entità con la stessa chiave di partizione più velocemente rispetto a quelle ottenute con i tasti partizione diversa, ma tasti partizione diversi consente una maggiore scalabilità operazione in parallelo. Per ulteriori informazioni, vedere [Microsoft Azure le prestazioni e scalabilità elenco di controllo](storage-performance-checklist.md).

Il codice seguente crea una nuova istanza di **table_entity** con alcuni archiviare i dati dei clienti. Il codice successivo chiama **table_operation::insert_entity** per creare un oggetto **table_operation** per inserire un'entità in una tabella e associa la nuova entità di tabella. Infine, il codice chiama il metodo execute sull'oggetto **cloud_table** . E la nuova **table_operation** invia una richiesta al servizio di tabella per inserire la nuova entità cliente nella tabella "utenti".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità
È possibile inserire un batch di entità al servizio di tabella in un'operazione di scrittura. Il codice seguente viene creato un oggetto **table_batch_operation** e quindi aggiunge che tre inserire operazioni a tale. Ogni operazione di inserimento viene aggiunto creando un nuovo oggetto entità, impostarne i valori e quindi il metodo Inserisci oggetto **table_batch_operation** da associare un'operazione di inserimento di nuovo l'entità. Quindi, **cloud_table.execute** è chiamato per eseguire l'operazione.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Alcuni aspetti da tenere presente nel batch operazioni:  

-   È possibile eseguire fino a 100 Inserisci, Elimina, stampa unione, Sostituisci, Inserisci o unione e Inserisci o Sostituisci operazioni in qualsiasi combinazione in un unico batch.  
-   Un'operazione batch può avere un'operazione di recupero, se è l'unica operazione nel batch.  
-   Tutte le entità in un'unica operazione devono avere la stessa chiave di partizione.  
-   Un'operazione batch è limitata ai payload 4 MB di dati.  

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione
Per eseguire una query per tutte le entità in una partizione di una tabella, utilizzare un oggetto **table_query** . Nell'esempio seguente specifica un filtro per entità dove "Rossi" sono il tasto partizione. Questo esempio stampa i campi di ciascuna entità nei risultati della query alla console.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

La query in questo esempio porta tutte le entità che soddisfano i criteri di filtro. Se si dispone di tabelle di grandi dimensioni e base per scaricare l'entità della tabella spesso, è consigliabile che i dati vengono memorizzati in BLOB Azure dello spazio di archiviazione alternativa.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperare un intervallo di entità in una partizione
Se non si desidera eseguire query su tutte le entità di una partizione, è possibile specificare un intervallo combinando i filtri chiave partizione con un filtro di chiave di riga. Nell'esempio seguente usa due filtri per ottenere tutte le entità partizione "Rossi" in cui la chiave di riga (nome) inizia con una lettera precedente a "E" dell'alfabeto e quindi stampa i risultati della query.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità
È possibile scrivere una query per recuperare un'entità singola, specifica. Il codice seguente usa **table_operation::retrieve_entity** per specificare il cliente "Jeff Smith". Questo metodo restituisce una sola entità invece di una raccolta e il valore restituito è in **table_result**. Impostazione di tasti partizione e riga in una query è il modo più rapido per recuperare una singola entità dal servizio di tabella.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Sostituire un'entità
Per sostituire un'entità, recuperare dal servizio di tabella, modificare l'oggetto entità e quindi salvare le modifiche al servizio tabella. Il codice seguente viene modificato l'indirizzo di posta elettronica e numero di telefono del cliente esistente. Invece di chiamare **table_operation::insert_entity**, questo codice utilizza **table_operation::replace_entity**. In questo modo l'entità da sostituire completamente sul server, a meno che l'entità nel server è stato modificato dopo che è stata recuperata, nel qual caso l'operazione non riesce. Questo errore è impedire l'applicazione di sovrascrivere inavvertitamente una modifica apportata tra il recupero e l'aggiornamento da un altro componente dell'applicazione. Il corretto svolgimento di questo errore consiste nel recuperare nuovamente l'entità, apportare le modifiche (se ancora valido) e quindi eseguire un'operazione su un'altra **table_operation::replace_entity** . La sezione seguente illustra come evitare questo comportamento.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Inserimento o sostituire un'entità
operazioni **table_operation::replace_entity** avrà esito negativo se l'entità è stata modificata dal recuperarlo dal server. Inoltre, è necessario recuperare l'entità dal server prima di affinché **table_operation::replace_entity** venga eseguita correttamente. In alcuni casi, tuttavia, non si conosce l'entità esiste nel server e i valori correnti archiviati al suo interno vengono irrilevanti, ovvero l'aggiornamento devono essere sovrascritti tutti. A tale scopo, utilizzare un'operazione di **table_operation::insert_or_replace_entity** . Questa operazione inserisce l'entità se non esiste o sostituisce in caso affermativo, indipendentemente da quando è stata effettuata l'ultimo aggiornamento. Nell'esempio seguente, l'entità cliente per Jeff Smith viene ancora recuperato, ma viene quindi salvata sul server tramite **table_operation::insert_or_replace_entity**. Verranno sovrascritti gli aggiornamenti eseguiti per l'entità tra il recupero e l'operazione di aggiornamento.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Un sottoinsieme di entità proprietà della query  
Una query in una tabella è possibile recuperare solo alcune proprietà di un'entità. La query nel codice seguente utilizza il metodo **table_query::set_select_columns** per restituire solo gli indirizzi di posta elettronica di entità nella tabella.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Query su alcune proprietà di un'entità è un'operazione più efficiente del recupero di tutte le proprietà.

## <a name="delete-an-entity"></a>Eliminare un'entità
Dopo avere recuperato, è possibile eliminarlo facilmente un'entità. Una volta recuperato l'entità, chiamare **table_operation::delete_entity** con l'entità da eliminare. Chiamare il metodo **cloud_table.execute** . Il codice seguente recupera ed elimina un'entità con una chiave di partizione di "Rossi" e "Luca" una chiave di riga.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Eliminare una tabella
Infine, nell'esempio seguente elimina una tabella da un account di archiviazione. Una tabella in cui è stata eliminata non saranno disponibile per creare di nuovo per un periodo di tempo dopo l'eliminazione.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base di spazio di archiviazione tabella, seguire questi collegamenti per altre informazioni sull'archiviazione Azure:  

-   [Come usare archiviazione Blob da C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Come utilizzare lo spazio di archiviazione coda da C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Elenco risorse di archiviazione Azure in C++](storage-c-plus-plus-enumeration.md)
-   [Raccolta di Client lo spazio di archiviazione per riferimento C++](http://azure.github.io/azure-storage-cpp)
-   [Documentazione di spazio di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
