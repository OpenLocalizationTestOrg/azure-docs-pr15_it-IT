<properties
    pageTitle="Come usare archiviazione blob (archiviazione degli oggetti) da PHP | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione di oggetto)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Come usare archiviazione blob da PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Archiviazione Blob Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archiviazione BLOB consentono di memorizzare qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando il servizio di archivio blob Azure. Gli esempi scritto in PHP e usare [Azure SDK per PHP] [download]. Gli scenari coperti includono **durante il caricamento**, **voce**, **download**e BLOB di **eliminazione** . Per ulteriori informazioni sui blob, vedere la sezione [passaggi successivi](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP

L'unico requisito per la creazione di un'applicazione PHP che accede al servizio blob Azure è il riferimento di classi di Azure SDK per PHP all'interno del codice. Per creare l'applicazione, inclusi il blocco note, è possibile utilizzare gli strumenti di sviluppo.

In questa guida è usare le funzionalità di servizio, che possono essere chiamate all'interno di un'applicazione PHP in locale o nel codice in esecuzione all'interno di un ruolo Azure web, un ruolo di lavoro o un sito Web.

## <a name="get-the-azure-client-libraries"></a>Ottenere le librerie Client Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurare l'applicazione di accedere al servizio blob

Per utilizzare le API del servizio di archivio blob Azure, è necessario:

1. Fare riferimento al file caricatore utilizzando l'istruzione [require_once] e
2. Fare riferimento a tutte le classi che è possibile utilizzare.

Nell'esempio seguente viene illustrato come includere il file caricatore e fare riferimento alla classe **ServicesBuilder** .

> [AZURE.NOTE] In questo esempio (e altri esempi in questo articolo) presuppongono che è stato installato le librerie di Client PHP per Azure tramite compositore. Se è stato installato manualmente le raccolte, è necessario fare riferimento il `WindowsAzure.php` file caricatore.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Negli esempi seguenti, il `require_once` istruzione verrà visualizzato sempre, ma solo le classi necessarie, ad esempio eseguire fanno riferimento.

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione Azure

Per creare un'istanza di un client di servizi blob Azure, è una stringa di connessione valida. Il formato della stringa di connessione di servizio blob è:

Per accedere al servizio live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Per l'accesso a emulatore lo spazio di archiviazione:

    UseDevelopmentStorage=true


Per creare un client di servizi Azure, è necessario utilizzare la classe **ServicesBuilder** . Si può:

* Passare la stringa di connessione direttamente a o
* Utilizzare **CloudConfigurationManager (CCM)** per selezionare più origini esterne per la stringa di connessione:
    * Per impostazione predefinita, è dotato di supporto per un'origine esterna - variabili di ambiente.
    * È possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource** .

Per gli esempi riportati di seguito, la stringa di connessione verrà passata direttamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Un oggetto **BlobRestProxy** consente di creare un contenitore di blob con il metodo **createContainer** . Quando si crea un contenitore, è possibile impostare opzioni per il contenitore, ma non è necessario. (Nell'esempio seguente viene illustrato come impostare l'elenco di controllo accesso contenitore (ACL) e i metadati contenitore).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Chiamare **setPublicAccess (PublicAccessType::CONTAINER\_e\_BLOB)** rende i dati contenitore e blob accessibile tramite le richieste di anonime. Per rendere accessibile tramite le richieste di anonime solo i dati blob, chiamare **setPublicAccess(PublicAccessType::BLOBS_ONLY)** . Per ulteriori informazioni sul contenitore ACL, vedere [Set contenitore ACL (API REST)][container-acl].

Per ulteriori informazioni sui codici di errore servizio Blob, vedere [Codici di errore servizio Blob][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Per caricare un file come blob, utilizzare il metodo **BlobRestProxy -> createBlockBlob** . Se non esiste o sovrascrive in caso affermativo, questa operazione crea il blob. Esempio di codice seguente presuppone che il contenitore è già stato creato e Usa [fopen] [ fopen] per aprire il file come flusso.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si noti che l'esempio precedente consente di caricare un blob come flusso. Tuttavia, un blob anche da caricare come una stringa utilizzando, ad esempio, il [file\_ottenere\_contenuto] [ file_get_contents] funzione. Per eseguire questa operazione utilizzando l'esempio precedente, cambiare `$content = fopen("c:\myfile.txt", "r");` a `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, utilizzare il metodo **BlobRestProxy -> listBlobs** con **foreach** ciclo a ciclo continuo tra il risultato. Il codice seguente viene visualizzato il nome di ogni blob come output in un contenitore e viene visualizzato il relativo URI nel browser.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Scaricare un blob

Per scaricare un blob, chiamare il metodo **BlobRestProxy -> getBlob** , quindi chiamare il metodo **getContentStream** sull'oggetto **GetBlobResult** risultante.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si noti che l'esempio precedente Ottiene un blob sotto forma di una risorsa di flusso (impostazione predefinita). Tuttavia, è possibile utilizzare il [flusso\_ottenere\_contenuto] [ stream-get-contents] la funzione per convertire il flusso restituito in una stringa.

## <a name="delete-a-blob"></a>Eliminare un blob

Per eliminare un blob, passare il nome del contenitore e blob **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Eliminare un contenitore di blob

Infine, per eliminare un contenitore di blob, passare il nome del contenitore **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base del servizio di archivio blob Azure, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse.

- Visitare il [blog del team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Vedere l' [esempio di blob blocco PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Vedere l' [esempio di blob pagina PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
 
Per ulteriori informazioni, vedere anche il [Centro per sviluppatori PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
