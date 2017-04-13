<properties
    pageTitle="Come utilizzare lo spazio di archiviazione di File da Java | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di Azure file da caricare, scaricare, elenco ed eliminare file. Esempi scritti in linguaggio."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Come utilizzare lo spazio di archiviazione di File da Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Panoramica

In questa guida è spiegato come eseguire operazioni di base sul servizio di archiviazione di File di Microsoft Azure. Esempi scritto in linguaggio verrà imparare a creare azioni e directory, caricare, elenco ed eliminare i file. Se ha familiarità con il servizio di archiviazione di File di Microsoft Azure, l'attraversano i concetti nelle sezioni che seguono sarà molto utile comprendere gli esempi.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java

Per creare gli esempi, sarà necessario Java Development Kit (JDK) e [[SDK Azure lo spazio di archiviazione per Java]. È necessario anche aver creato un account di archiviazione Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configurazione dell'applicazione di utilizzare l'archiviazione di File

Per usare l'API dello spazio di archiviazione di Azure, aggiungere l'istruzione nella parte superiore del file di linguaggio in cui si desidera accedere al servizio di archiviazione da.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configurazione di una stringa di connessione di archiviazione Azure

Per utilizzare l'archiviazione dei File, è necessario connettersi al proprio account di archiviazione Azure. Il primo passaggio, è possibile configurare una stringa di connessione che verrà usato per connettersi al proprio account di archiviazione. Di seguito definire una variabile statica a tale scopo.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Sostituire your_storage_account_name e your_storage_account_key con i valori effettivi per l'account di archiviazione.

## <a name="connecting-to-an-azure-storage-account"></a>Connessione a un account di archiviazione Azure

Per connettersi al proprio account di archiviazione, è necessario utilizzare l'oggetto **CloudStorageAccount** , passare il metodo di **analizzare** una stringa di connessione.

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** genera un InvalidKeyException, pertanto è necessario inserire in un blocco try/catture.

## <a name="how-to-create-a-share"></a>Procedura: creare una condivisione

Tutti i file e directory di archiviazione dei File si trovano in un contenitore denominato **Condividi**. L'account di archiviazione consentito quante condivisioni come consente la capacità di account. Per accedere a una condivisione e il relativo contenuto, è necessario usare un client di spazio di archiviazione File.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Usa il client di spazio di archiviazione File, quindi è possibile ottenere un riferimento a una condivisione.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Per creare effettivamente la condivisione, utilizzare il metodo **createIfNotExists** dell'oggetto CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

A questo punto, **condividere** contiene un riferimento a una condivisione denominata **sampleshare**.

## <a name="how-to-upload-a-file"></a>Procedura: caricare un file

Una condivisione di spazio di archiviazione File Azure contiene almeno, una directory radice in cui è possano risiedono file. In questa sezione verranno come caricare un file dall'archivio locale nella cartella radice di una condivisione.

Il primo passo per caricare un file è ottenere un riferimento alla directory in cui devono essere inclusi. Questo caso utilizzando il metodo **getRootDirectoryReference** dell'oggetto condivisione.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Dopo aver creato un riferimento alla directory radice della condivisione, è possibile caricare un file su di esso con il codice riportato di seguito.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Procedura: creare una Directory

È anche possibile organizzare lo spazio di archiviazione inserendo i file all'interno di sottodirectory anziché affidare a tutti gli elementi nella cartella principale. Il servizio di archiviazione file Azure consente di creare più directory come consentirà l'account. Il codice riportato di seguito verrà creata una directory secondaria denominata **sampledir** nella directory radice.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Procedura: elenco di file e directory in una condivisione

Ottenere un elenco di file e directory all'interno di una condivisione scopo chiamando **listFilesAndDirectories** su un riferimento CloudFileDirectory. Viene restituito un elenco di oggetti ListFileItem che è possibile scorrere in. Ad esempio, il codice seguente verrà elencati i file e directory all'interno della directory radice.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Procedura: scaricare un file

Una delle operazioni più frequenti che eseguirà contro lo spazio di archiviazione di file è per scaricare i file. Nell'esempio seguente, il codice Scarica samplefile. txt e viene visualizzato il relativo contenuto.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Procedura: eliminare un file

Un'altra operazione di archiviazione file comune è l'eliminazione dei file. Il codice seguente consente di eliminare un file denominato samplefile. txt memorizzati all'interno di una directory denominata **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Procedura: eliminare una directory

Eliminazione di una directory è un'operazione semplice, anche se si noti che non è possibile eliminare una directory che contiene ancora file o altre directory.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Procedura: eliminare una condivisione

Eliminazione di una condivisione viene eseguita tramite il metodo **deleteIfExists** su un oggetto CloudFileShare. Ecco il codice di esempio che vengono eseguite.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Passaggi successivi

Se si desidera ottenere ulteriori informazioni su altri API dello spazio di archiviazione di Azure, seguire questi collegamenti.

- [Centro per sviluppatori Java](http://azure.microsoft.com/develop/java/)
- [Spazio di archiviazione di Azure SDK per Java](https://github.com/azure/azure-storage-java)
- [Spazio di archiviazione di Azure SDK per Android](https://github.com/azure/azure-storage-android)
- [Spazio di archiviazione Azure Client SDK riferimento](http://dl.windowsazure.com/storage/javadoc/)
- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
