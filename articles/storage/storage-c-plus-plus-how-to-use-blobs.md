<properties
    pageTitle="Come usare archiviazione blob (archiviazione degli oggetti) da C++ | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione oggetto)."
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

# <a name="how-to-use-blob-storage-from-c"></a>Come usare archiviazione Blob da C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Archiviazione Blob Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archiviazione BLOB consentono di memorizzare qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.

Questa Guida verrà illustrato come eseguire gli scenari comuni utilizzando il servizio di archiviazione Blob Azure. Gli esempi sono scritte in C++ e utilizzano la [Libreria di Client di spazio di archiviazione di Azure per C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Gli scenari coperti includono **durante il caricamento**, **voce**, **download**e BLOB di **eliminazione** .  

>[AZURE.NOTE] Questa guida è destinato alla libreria di Client lo spazio di archiviazione di Azure per C++ versione 1.0.0 e versioni successive. La versione consigliata è lo spazio di archiviazione Client raccolta 2.2.0, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Creare un'applicazione C++
In questa Guida, si utilizzeranno le funzionalità di archiviazione che possono essere eseguite all'interno di un'applicazione C++.  

A tale scopo, è necessario installare la libreria di Client lo spazio di archiviazione di Azure per C++ e creare un account di archiviazione Azure nell'abbonamento Azure.   

Per installare la libreria di Client lo spazio di archiviazione di Azure per C++, è possibile utilizzare i metodi seguenti:

-   **Linux:** Seguire le istruzioni visualizzate nella pagina della [Raccolta di Client Azure lo spazio di archiviazione per il file Leggimi C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** In Visual Studio, fare clic su **Strumenti > Gestione di pacchetti NuGet > Console di gestione pacchetti**. Digitare il comando seguente nella [console di gestione di pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere **INVIO**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurare l'applicazione per accedere all'archiviazione Blob  
Aggiungere che le seguenti istruzioni nella parte superiore del file C++ nel punto in cui si desidera utilizzare l'API dello spazio di archiviazione di Azure per accedere a BLOB include:  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Configurazione di una stringa di connessione di archiviazione Azure
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

È possibile ottenere un riferimento a una classe **cloud_blob_client** successivamente, come è possibile recuperare gli oggetti che rappresentano i contenitori e BLOB memorizzati all'interno del servizio di archiviazione Blob. Il codice seguente crea un oggetto **cloud_blob_client** utilizzando l'oggetto account lo spazio di archiviazione che è recuperati in precedenza:  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Procedura: creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

In questo esempio viene illustrato come creare un contenitore se non esiste già:  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

Per impostazione predefinita, il nuovo contenitore è privato e specificare il tasto di scelta di spazio di archiviazione per scaricare BLOB da questo contenitore. Se si desidera rendere disponibili i file (BLOB) all'interno del contenitore a tutti gli utenti, è possibile impostare il contenitore di tipo public utilizzando il codice seguente:  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Tutti gli utenti su Internet può vedere BLOB in un contenitore di pubblico, ma è possibile modificare o eliminare solo se si dispone della chiave di accesso appropriato.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Procedura: caricare un blob in un contenitore
Archiviazione Blob Azure supporta blocco BLOB e BLOB di pagine. Nella maggior parte dei casi, blob blocco è il tipo consigliato da utilizzare.  

Per caricare un file in un blob di blocco, ottenere un riferimento contenitore e usarlo per ottenere un riferimento di blob di blocco. Dopo avere inserito un riferimento blob, è possibile caricare qualsiasi flusso di dati tramite il metodo **upload_from_stream** . Se non si è in precedenza esiste, o salvarlo in caso affermativo, questa operazione creerà il blob. Nell'esempio seguente viene illustrato come caricare un blob in un contenitore e si presuppone che il contenitore è già stato creato.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

In alternativa, è possibile utilizzare il metodo **upload_from_file** per caricare un file in un blob di blocco.

## <a name="how-to-list-the-blobs-in-a-container"></a>Procedura: elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, ottenere innanzitutto un riferimento contenitore. È quindi possibile utilizzare il metodo del contenitore **list_blobs** per recuperare il BLOB e/o directory all'interno di essa. Per accedere a vasta gamma di proprietà e metodi per un restituito **list_blob_item**, è necessario chiamare il metodo **list_blob_item.as_blob** per ottenere un oggetto **cloud_blob** o il metodo **list_blob.as_directory** per ottenere un oggetto cloud_blob_directory. Il codice seguente viene illustrato come recuperare e generare l'output URI di ogni elemento nel contenitore di **risorse del contenitore di esempio** :

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Per ulteriori informazioni sull'elenco operazioni, vedere [Risorse di archiviazione Azure elenco c++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Procedura: scaricare BLOB
Per scaricare BLOB, prima di tutto recuperare un riferimento blob e quindi il metodo **download_to_stream** . Nell'esempio seguente viene utilizzato il metodo **download_to_stream** per trasferire il contenuto di blob a un oggetto di flusso che è possibile mantenere quindi in un file locale.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

In alternativa, è possibile utilizzare il metodo **download_to_file** per scaricare il contenuto di un blob in un file.
Inoltre, è possibile anche utilizzare il metodo **download_text** per scaricare il contenuto di un blob come stringa di testo.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Procedura: eliminare BLOB
Per eliminare un blob, ottenere innanzitutto un riferimento blob e quindi chiamare il metodo **delete_blob** .  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base di spazio di archiviazione blob, seguire questi collegamenti per altre informazioni sull'archiviazione Azure.  

-   [Come usare coda lo spazio di archiviazione da C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Come utilizzare lo spazio di archiviazione tabella da C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Elenco risorse di archiviazione Azure in C++](storage-c-plus-plus-enumeration.md)
-   [Raccolta di Client lo spazio di archiviazione per riferimento C++](http://azure.github.io/azure-storage-cpp)
-   [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
