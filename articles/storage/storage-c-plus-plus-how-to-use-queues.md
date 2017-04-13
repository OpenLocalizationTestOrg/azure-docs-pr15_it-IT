<properties
    pageTitle="Come utilizzare lo spazio di archiviazione coda (C++) | Microsoft Azure"
    description="Informazioni su come usare il servizio di archiviazione coda in Azure. Esempi sono scritte in C++."
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

# <a name="how-to-use-queue-storage-from-c"></a>Come usare coda lo spazio di archiviazione da C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
Questa guida illustra come eseguire gli scenari comuni utilizzando il servizio di archiviazione di Azure coda. Gli esempi sono scritte in C++ e utilizzano la [Libreria di Client di spazio di archiviazione di Azure per C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Gli scenari coperti includono **l'inserimento**, **la visualizzazione**, **ottenere**e **l'eliminazione** coda messaggi, nonché **Creazione ed eliminazione di code**.

>[AZURE.NOTE] Questa guida è destinato alla libreria di Client lo spazio di archiviazione di Azure per C++ versione 1.0.0 e versioni successive. La versione consigliata è lo spazio di archiviazione Client raccolta 2.2.0, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Creare un'applicazione C++  
In questa Guida, si utilizzeranno le funzionalità di archiviazione che possono essere eseguite all'interno di un'applicazione C++.

A tale scopo, è necessario installare la libreria di Client lo spazio di archiviazione di Azure per C++ e creare un account di archiviazione Azure nell'abbonamento Azure.

Per installare la libreria di Client lo spazio di archiviazione di Azure per C++, è possibile utilizzare i metodi seguenti:

-   **Linux:** Seguire le istruzioni visualizzate nella pagina della [Raccolta di Client Azure lo spazio di archiviazione per il file Leggimi C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows:** In Visual Studio, fare clic su **Strumenti > Gestione di pacchetti NuGet > Console di gestione pacchetti**. Digitare il comando seguente nella [console di gestione di pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere **INVIO**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurare l'applicazione per accedere all'archiviazione coda
Aggiungere che le seguenti istruzioni nella parte superiore del file C++ nel punto in cui si desidera utilizzare l'API dello spazio di archiviazione di Azure per accedere alle code include:  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione Azure

Un client di archiviazione Azure utilizza una stringa di connessione di spazio di archiviazione per archiviare i punti finali e le credenziali per accedere ai servizi di gestione dati. Durante l'esecuzione di un'applicazione client, è necessario specificare la stringa di connessione di spazio di archiviazione nel formato seguente, utilizzando il nome del proprio account di archiviazione e il tasto di scelta di spazio di archiviazione per l'account di archiviazione riportata nel [Portale di Azure](https://portal.azure.com) per i valori di *nome account* e *AccountKey* . Per informazioni sull'account di archiviazione e i tasti di scelta, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md). In questo esempio viene illustrato come dichiarare un campo statico per contenere la stringa di connessione:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Per testare l'applicazione in un computer Windows locale, è possibile utilizzare Microsoft Azure [emulatore lo spazio di archiviazione](storage-use-emulator.md) che viene installato con [Azure SDK](https://azure.microsoft.com/downloads/). Emulatore lo spazio di archiviazione è un'utilità che simula i servizi di Blob, coda e una tabella disponibili in Azure nel computer di sviluppo locale. Nell'esempio seguente viene illustrato come dichiarare un campo statico per contenere la stringa di connessione per l'archiviazione locale emulatore in:  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Per avviare l'emulatore archiviazione Azure, selezionare il pulsante **Start** o premere il tasto **Windows** . Iniziare a digitare **Emulatore lo spazio di archiviazione di Azure**e selezionare **Microsoft Azure archiviazione Emulator** dall'elenco di applicazioni.

Negli esempi seguenti presuppongono che sia stata utilizzata uno di questi due metodi per ottenere la stringa di connessione di spazio di archiviazione.

## <a name="retrieve-your-connection-string"></a>Recuperare la stringa di connessione
È possibile utilizzare la classe **cloud_storage_account** per rappresentare le informazioni dell'Account di archiviazione. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione dello spazio di archiviazione, è possibile utilizzare il metodo di **analisi** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda
Un oggetto **cloud_queue_client** consente di ottenere gli oggetti di riferimento per le code. Il codice seguente viene creato un oggetto **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Utilizzare l'oggetto **cloud_queue_client** per ottenere un riferimento alla coda che si desidera utilizzare. È possibile creare coda se non esiste.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: inserire un messaggio in una coda
Per inserire un messaggio in una coda esistente, è necessario creare un nuovo **cloud_queue_message**. Successivamente, chiamare il metodo **add_message** . Un **cloud_queue_message** possono essere create da una stringa o una matrice di **byte** . Ecco codice che crea una coda (se non esiste) e viene inserito il messaggio "Hello, World":

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Procedura: leggere il messaggio successivo
È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda utilizzando il metodo **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: modificare il contenuto di un messaggio in coda
È possibile modificare il contenuto di un messaggio sul posto nella coda. Se il messaggio rappresenta un'operazione di lavoro, è possibile utilizzare questa caratteristica per aggiornare lo stato dell'attività di lavoro. Il codice seguente aggiorna il messaggio coda con nuovo contenuto e imposta il timeout di visibilità per estendere un'altra 60 secondi. Salva lo stato del lavoro associato al messaggio e offre un'altra minuti per continuare a lavorare sul messaggio sul quale il client. È possibile utilizzare questa tecnica per tenere traccia di più passaggi flussi di lavoro nella coda messaggi senza dover ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware e software. In genere si preferisce mantenere anche un numero di tentativi e se il messaggio è ripetuto più n volte, è necessario eliminarlo. Ciò protegge da un messaggio che genera un errore di applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Procedura: annullare l'accodamento il messaggio successivo
Il codice coda un messaggio da una coda in due passaggi. Quando si chiama **get_message**, viene visualizzato il messaggio successivo in una coda. Un messaggio restituito da **get_message** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **delete_message**. Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice chiama **delete_message** destra dopo l'elaborazione di messaggio.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Procedura: sfruttare le opzioni aggiuntive per deselezionare Accodamento messaggi
Esistono due modi per personalizzare il recupero dei messaggi da una coda. Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente. Nell'esempio seguente viene utilizzato il metodo **get_messages** per ottenere 20 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo **for** . Imposta inoltre il timeout invisibility a cinque minuti per ogni messaggio. Si noti che l'avvio di 5 minuti per tutti i messaggi contemporaneamente, dopo avere 5 minuti trascorsi la chiamata a **get_messages**, tutti i messaggi che non sono stati eliminati verrà visualizzata nuovamente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Procedura: ottenere la lunghezza della coda
È possibile ottenere una stima del numero di messaggi in una coda. Il metodo **download_attributes** richiede il servizio di coda di recuperare gli attributi di coda, tra cui il numero di messaggi. Il metodo **approximate_message_count** Ottiene il numero approssimativo dei messaggi nella coda.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda
Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **delete_queue_if_exists** sull'oggetto coda.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione coda, seguire questi collegamenti per altre informazioni sull'archiviazione Azure.

-   [Come usare archiviazione Blob da C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Come utilizzare lo spazio di archiviazione tabella da C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Elenco risorse di archiviazione Azure in C++](storage-c-plus-plus-enumeration.md)
-   [Raccolta di Client lo spazio di archiviazione per riferimento C++](http://azure.github.io/azure-storage-cpp)
-   [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)

