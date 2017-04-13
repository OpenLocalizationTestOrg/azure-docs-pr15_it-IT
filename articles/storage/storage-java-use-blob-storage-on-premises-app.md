<properties
    pageTitle="Applicazione di archiviazione blob (Java) locale | Microsoft Azure"
    description="Informazioni su come creare un'applicazione console che consente di caricare un'immagine Azure e di visualizzarne l'immagine nel browser. Esempi di codice in linguaggio."
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

# <a name="on-premises-application-with-blob-storage"></a>Applicazione di archiviazione blob locale

## <a name="overview"></a>Panoramica

Nell'esempio seguente mostra come è possibile utilizzare lo spazio di archiviazione Azure per archiviare le immagini in Azure. Il codice in questo articolo riguarda un'applicazione console che consente di caricare un'immagine Azure e quindi viene creato un file HTML contenente l'immagine nel browser.

## <a name="prerequisites"></a>Prerequisiti

- È installato un linguaggio sviluppo Kit (JDK), versione 1.6 o versione successiva.
- Azure SDK è installato.
- VASO relative alle librerie di Azure per Java vasetti da qualsiasi dipendenza applicabile, vengono installati e sono nel percorso di compilazione utilizzato dal compilatore del linguaggio. Per informazioni sull'installazione le librerie di Azure per Java, vedere [scaricare Azure SDK per Java](java-download-azure-sdk.md).
- Configurare un account di archiviazione Azure. Il nome dell'account e la chiave di account per l'account di archiviazione da utilizzare per il codice in questo articolo. Informazioni su [come creare un Account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per informazioni sulla creazione di un account di archiviazione e [visualizzazione e copia lo spazio di archiviazione i tasti di scelta](storage-create-storage-account.md#view-and-copy-storage-access-keys) per informazioni sul recupero chiave account.

- È stato creato un file di immagine locale denominato archiviati in c: il percorso\\myimages\\Image1. In alternativa, modificare il costruttore   **FileInputStream** nell'esempio usare un'immagine diversa percorso e il nome file.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Come utilizzare archiviazione blob Azure per caricare un file

La procedura dettagliata viene presentata di seguito. Se si desidera passare, più avanti in questo articolo viene presentato l'intero codice.

Iniziare il codice includendo importazioni per le classi dello spazio di archiviazione di base Azure, le classi client blob Azure, le classi Java IO e la classe **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Dichiarare una classe denominata **StorageSample**e includere parentesi quadra aperta **{**.

    public class StorageSample {

All'interno della classe **StorageSample** dichiarare un stringa che conterrà il protocollo endpoint predefinito, il nome dell'account di archiviazione e il tasto lo spazio di archiviazione, come specificato nell'account di archiviazione Azure. Sostituire i valori segnaposto **il\_account\_nome** e **il\_account\_chiave** con il proprio account chiave account e nome, rispettivamente.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Aggiungere la dichiarazione per **principale**, incluso un blocco di **prova** e includere le parentesi aperte necessarie, **{**.

    public static void main(String[] args)
    {
        try
        {

Dichiarazione di variabili del tipo seguente (le descrizioni sono per come vengono utilizzati in questo esempio):

-   **CloudStorageAccount**: utilizzato per inizializzare l'oggetto con il nome dell'account Azure dello spazio di archiviazione e la chiave account e creare l'oggetto client blob.
-   **CloudBlobClient**: utilizzato per accedere al servizio blob.
-   **CloudBlobContainer**: consente di creare un contenitore di blob, elenco BLOB nel contenitore ed elimina il contenitore.
-   **CloudBlockBlob**: utilizzato per caricare un file di immagine locale al contenitore.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Assegnare il valore della variabile di **account** .

    account = CloudStorageAccount.parse(storageConnectionString);

Assegnare il valore della variabile **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Assegnare un valore per la variabile del **contenitore** . Si ottiene un riferimento a un contenitore denominato **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Creare il contenitore. Se non è disponibile (e restituire **vero**), questo metodo creerà il contenitore. Se il contenitore esiste, verrà restituito **false**. Un'alternativa alla **createIfNotExists** è il metodo **create** (che verrà restituito un errore se il contenitore esiste già).

    container.createIfNotExists();

Impostare l'accesso anonimo per il contenitore.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

È possibile ottenere un riferimento a blob blocco, che rappresenterà il blob Azure dello spazio di archiviazione.

    blob = container.getBlockBlobReference("image1.jpg");

Utilizzare il costruttore di **File** per ottenere un riferimento al file creato localmente che sarà caricato. Assicurarsi di che aver creato il file prima di eseguire il codice.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Caricare il file locale mediante una chiamata al metodo **CloudBlockBlob.upload** . Il primo parametro per il metodo **CloudBlockBlob.upload** è un oggetto **FileInputStream** che rappresenta il file locale caricati allo spazio di archiviazione Azure. Il secondo parametro è la dimensione, in byte del file.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Chiamare una funzione di supporto denominata **MakeHTMLPage**, per rendere una pagina HTML base contenente un ** &lt;immagine&gt; ** elemento con l'origine impostato a quello che si trova ora nell'account di archiviazione Azure. Il codice per **MakeHTMLPage** verrà descritti più avanti in questo articolo.

    MakeHTMLPage(container);

Stampare un messaggio di stato e informazioni sulla pagina HTML creata.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Chiudere blocco **try** inserendo una parentesi chiusa: **}**

Gestire le eccezioni seguenti:

-   **FileNotFoundException**: possono essere generate dal produttore **FileInputStream** o **FileOutputStream** .
-   **StorageException**: possono essere generate dalla libreria di spazio di archiviazione Azure client.
-   **URISyntaxException**: possono essere generate dal metodo **ListBlobItem.getUri** .
-   **Eccezioni**: eccezioni generico.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Chiudere **principale** inserendo una parentesi chiusa: **}**

Creare un metodo denominato **MakeHTMLPage** per creare una pagina HTML. Questo metodo ha un parametro di tipo **CloudBlobContainer**, che verrà utilizzato per scorrere l'elenco dei blob caricato. Questo metodo genererà eccezioni di tipo **FileNotFoundException**, che possono essere generate dal costruttore **FileOutputStream** e **URISyntaxException**, che possono essere generate dal metodo **ListBlobItem.getUri** . Includere la parentesi di apertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Creare un file locale denominato **index. HTML**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Scrivere file locale, aggiunta nel ** &lt;html&gt;**, ** &lt;intestazione&gt;**, e ** &lt;corpo&gt; ** elementi.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Scorrere l'elenco dei blob caricato. Per ogni blob nella pagina HTML creare un ** &lt;img&gt; ** elemento con l'attributo **src** inviata a URI del blob come presente nell'account di archiviazione Azure.
Anche se è stato aggiunto solo un'immagine in questo esempio, se sono stati aggiunti più, questo codice da scorrere tutti gli elementi.

Per semplicità, in questo esempio si presuppone ogni blob caricati un'immagine. Se è stato aggiornato BLOB ad eccezione di immagini o BLOB di pagine anziché BLOB blocco, modificare il codice in base alle esigenze.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Chiudere la ** &lt;corpo&gt; ** elemento e il ** &lt;html&gt; ** elemento.

    stream.println("</body>");
    stream.println("</html>");

Chiudere il file locale.

    stream.close();

Chiudere **MakeHTMLPage** inserendo una parentesi chiusa: **}**

Chiudere **StorageSample** inserendo una parentesi chiusa: **}**

Di seguito è il codice completo per questo esempio. È necessario ricordare di modificare i valori di segnaposto **il\_account\_nome** e **il\_account\_chiave** a usare il nome account e la chiave account, rispettivamente.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Oltre a caricare il file di immagine locale di archiviazione Azure, viene creato un namedindex.html di file locale, è possibile aprire nel browser per visualizzare l'immagine caricata.

Poiché il codice contiene il nome account e la chiave account, assicurarsi che il codice sorgente sia protetto.

## <a name="to-delete-a-container"></a>Per eliminare un contenitore

Poiché addebitate per lo spazio di archiviazione, può essere necessario eliminare il contenitore **gettingstarted** dopo aver sperimentare in questo esempio. Per eliminare un contenitore, utilizzare il metodo **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Per chiamare il metodo **CloudBlobContainer.delete** , il processo di inizializzazione oggetti **CloudStorageAccount**, **ClodBlobClient**e **CloudBlobContainer** è uguale a quello mostrato per il metodo **createIfNotExist** . Di seguito è un esempio completo che elimina il contenitore denominato **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Per una panoramica di altri metodi e le classi dello spazio di archiviazione blob, vedere [come utilizzare l'archiviazione Blob da Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Passaggi successivi

Seguire questi collegamenti per ulteriori informazioni sulle attività di archiviazione più complesse.

- [Spazio di archiviazione di Azure SDK per Java](https://github.com/azure/azure-storage-java)
- [Spazio di archiviazione Azure Client SDK riferimento](http://dl.windowsazure.com/storage/javadoc/)
- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
