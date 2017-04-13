<properties
    pageTitle="Come utilizzare lo spazio di archiviazione di File da C++ | Microsoft Azure"
    description="Archiviare i dati del file nel cloud con spazio di archiviazione File Azure."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Come utilizzare lo spazio di archiviazione di File da C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>Su questa esercitazione

In questa esercitazione verrà come eseguire operazioni di base sul servizio di archiviazione di File di Microsoft Azure. Esempi scritto in C++, si verrà informazioni su come creare condivisioni e directory, caricare, elenco ed eliminare i file. Se ha familiarità con il servizio di archiviazione di File di Microsoft Azure, l'attraversano i concetti nelle sezioni che seguono sarà molto utile comprendere gli esempi.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Creare un'applicazione C++

Per creare gli esempi, è necessario installare la libreria di Client di spazio di archiviazione di Azure 2.4.0 per C++. È necessario anche aver creato un account di archiviazione Azure.

Per installare il Client di spazio di archiviazione di Azure 2.4.0 per C++, è possibile utilizzare uno dei metodi seguenti:

-   **Linux:** Seguire le istruzioni visualizzate nella pagina della [Raccolta di Client Azure lo spazio di archiviazione per il file Leggimi C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows:** In Visual Studio, fare clic su **strumenti &gt; Gestione pacchetti NuGet &gt; Console di gestione pacchetti**. Digitare il comando seguente nella [console di gestione di pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere **INVIO**.

    Pacchetto di installazione wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>Impostare l'applicazione di utilizzare l'archiviazione di File

Aggiungere che le seguenti istruzioni nella parte superiore del file C++ nel punto in cui si desidera utilizzare l'API dello spazio di archiviazione di Azure per accedere ai file include:

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione Azure

Per utilizzare l'archiviazione dei File, è necessario connettersi al proprio account di archiviazione Azure. Il primo passaggio, è possibile configurare una stringa di connessione che verrà usato per connettersi al proprio account di archiviazione. Di seguito definire una variabile statica a tale scopo.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Connessione a un account di archiviazione Azure

È possibile utilizzare la classe **cloud_storage_account** per rappresentare le informazioni dell'Account di archiviazione. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione dello spazio di archiviazione, è possibile utilizzare il metodo di **analisi** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Procedura: creare una condivisione

Tutti i file e directory di archiviazione dei File si trovano in un contenitore denominato **Condividi**. L'account di archiviazione consentito tanti condivisioni come consente la capacità di account. Per accedere a una condivisione e il relativo contenuto, è necessario usare un client di spazio di archiviazione File.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Usa il client di spazio di archiviazione File, quindi è possibile ottenere un riferimento a una condivisione.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

Per creare la condivisione, utilizzare il metodo **create_if_not_exists** dell'oggetto **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

A questo punto, **condividere** contiene un riferimento a una condivisione denominata **Condividi risorse del campione**.

## <a name="how-to-upload-a-file"></a>Procedura: caricare un file

Almeno, una condivisione di spazio di archiviazione File Azure contiene una directory radice in cui è possano risiedono file. In questa sezione verranno come caricare un file dall'archivio locale nella cartella radice di una condivisione.

Il primo passo per caricare un file è ottenere un riferimento alla directory in cui devono essere inclusi. Questo caso utilizzando il metodo **get_root_directory_reference** dell'oggetto condivisione.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Dopo aver creato un riferimento alla directory radice della condivisione, è possibile caricare un file su di esso. In questo esempio carica da un file di testo e da un flusso.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Procedura: creare una Directory

È anche possibile organizzare lo spazio di archiviazione inserendo i file all'interno di sottodirectory anziché affidare a tutti gli elementi nella cartella principale. Il servizio di archiviazione file Azure consente di creare più directory come consentirà l'account. Il codice riportato di seguito verrà creata una directory denominata **directory personale esempio** sotto la directory radice, nonché una sottodirectory denominata **sottodirectory personale campione**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Procedura: elenco di file e directory in una condivisione

Ottenere un elenco di file e directory all'interno di una condivisione scopo chiamando **list_files_and_directories** su un riferimento **cloud_file_directory** . Per accedere a vasta gamma di proprietà e metodi per un restituito **list_file_and_directory_item**, è necessario chiamare il metodo **list_file_and_directory_item.as_file** per ottenere un oggetto **cloud_file** o il metodo **list_file_and_directory_item.as_directory** per ottenere un oggetto **cloud_file_directory** .

Il codice seguente viene illustrato come recuperare e generare l'output URI di ogni elemento nella cartella radice della condivisione.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Procedura: scaricare un file

Per scaricare i file, prima di tutto recuperare un riferimento al file e quindi il metodo **download_to_stream** per trasferire i contenuti del file a un oggetto di flusso che è possibile mantenere quindi in un file locale. In alternativa, è possibile utilizzare il metodo **download_to_file** per scaricare il contenuto di un file in un file locale. È possibile utilizzare il metodo **download_text** per scaricare il contenuto di un file come una stringa di testo.

Nell'esempio seguente usa i metodi **download_to_stream** e **download_text** per illustrare il download di file che sono stati creati nelle sezioni precedenti.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Procedura: eliminare un file

Un'altra operazione di archiviazione file comune è l'eliminazione dei file. Il codice seguente elimina un file denominato my-esempio-file-3 archiviato nella directory radice.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Procedura: eliminare una directory

Eliminazione di una directory è un'operazione semplice, anche se si noti che non è possibile eliminare una directory che contiene ancora file o altre directory.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Procedura: eliminare una condivisione

Eliminazione di una condivisione viene eseguita tramite il metodo **delete_if_exists** su un oggetto cloud_file_share. Ecco il codice di esempio che vengono eseguite.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Impostare le dimensioni massime per una condivisione file

È possibile impostare la quota (o dimensioni massime) per la condivisione di file, in gigabyte. È inoltre possibile controllare la quantità di dati è attualmente archiviato presenti nella condivisione.

Se si imposta la quota per una condivisione, è possibile limitare le dimensioni totali dei file archiviati nella condivisione. Se la dimensione totale del file della condivisione supera la quota di impostare la condivisione, quindi client saranno in grado di aumentare le dimensioni del file esistenti o creare nuovi file, a meno che i file sono vuoti.

Nell'esempio seguente viene illustrato come controllare l'utilizzo corrente per la condivisione e su come impostare la quota per la condivisione.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generare una firma di accesso condiviso per un file o una condivisione file

È possibile generare una firma di accesso condiviso (SA) per una condivisione file o per un singolo file. È anche possibile creare un criterio di accesso condiviso in una condivisione file per gestire le firme di accesso condiviso. Creazione di un criterio di accesso condiviso è consigliabile, in quanto consente di revocare le SA se devono essere compromessa.

Nell'esempio seguente viene creato un criterio di accesso condiviso in una condivisione e quindi utilizza tale criterio per offrire ai vincoli per SA su un file nella condivisione.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Per ulteriori informazioni sulla creazione e l'uso delle firme accesso condiviso, vedere [Utilizzo condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'archiviazione di Azure, esplorare queste risorse:

-   [Raccolta di Client di spazio di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)

-   [Esplora archivi Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
