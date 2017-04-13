<properties
    pageTitle="Come usare archiviazione Blob (archiviazione degli oggetti) da trascrizione | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione oggetto)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Come usare archiviazione Blob da trascrizione

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Archiviazione Blob Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archiviazione BLOB consentono di memorizzare qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.

Questa guida illustra come eseguire gli scenari comuni utilizzando archiviazione Blob. Gli esempi sono scritti con l'API trascrizione. Gli scenari coperti includono BLOB **durante il caricamento, elenco, il download** e **l'eliminazione** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creare un'applicazione di trascrizione

Creare un'applicazione trascrizione. Per istruzioni, vedere [trascrizione in applicazione Web guide su una macchina virtuale Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per accedere all'archiviazione

Per utilizzare lo spazio di archiviazione di Azure, è necessario scaricare e usare il pacchetto di azure trascrizione, che include un set di raccolte semplicità di utilizzo che comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix).

2. Digitare "azure installa indicatore" nella finestra di comando per installare il simbolo e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto

Usare un editor di testo, aggiungere quanto segue nella parte superiore del file trascrizione nel punto in cui si prevede di utilizzare lo spazio di archiviazione:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Configura una connessione di archiviazione Azure

Modulo di azure leggerà le variabili di ambiente **AZURE\_lo spazio di archiviazione\_ACCOUNT** e **AZURE\_lo spazio di archiviazione\_ACCESS_KEY** le informazioni necessarie per connettersi all'account di archiviazione Azure. Se non sono tali variabili, è necessario specificare le informazioni sull'account prima di utilizzare **Azure::Blob::BlobService** con il codice seguente:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Per ottenere i valori da un classico o account di archiviazione di Manager delle risorse nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Selezionare **I tasti di scelta**e l'impostazioni sulla destra.
4. In e il chiavi di accesso che viene visualizzata, verrà visualizzato il tasto 1 e 2 tasto di scelta. È possibile usare uno di questi elementi. 
5. Fare clic sull'icona di copia per copiare la chiave negli Appunti. 

Per ottenere i valori da un account di archiviazione classica nel portale di Azure classico:

1. Accedere al [portale di Azure classica](https://manage.windowsazure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Fare clic su **GESTISCI i tasti di scelta** nella parte inferiore del riquadro di spostamento.
4. Nella finestra di dialogo popup vengono visualizzati il nome dell'account di archiviazione, tasto primario e secondario tasto di scelta. Per il tasto di scelta, è possibile utilizzare quello principale o quello secondario. 
5. Fare clic sull'icona di copia per copiare la chiave negli Appunti.

## <a name="create-a-container"></a>Creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L'oggetto **Azure::Blob::BlobService** consente di lavorare con i contenitori e BLOB. Per creare un contenitore, utilizzare la **creare\_container()** metodo.

Nell'esempio seguente viene creato un contenitore o stampare l'errore eventuale.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Se si desidera rendere pubblico i file nel contenitore, è possibile impostare le autorizzazioni del contenitore.

È possibile modificare solo il <strong>creare\_container()</strong> chiamata per passare il **: pubblico\_accesso\_livello** opzione:

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Valori validi per il **: pubblico\_accesso\_livello** opzione sono:

* **blob:** Specifica l'accesso in lettura pubblica completa per i dati contenitore e blob. Client possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima, ma non è possibile enumerare contenitori in account di archiviazione.

* **contenitore:** Specifica l'accesso in lettura pubblico per BLOB. Dati BLOB in questo contenitore possono essere letti tramite richiesta anonima, ma contenitore dati non sono disponibili. Client non è possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima.

In alternativa, è possibile modificare il livello di accesso pubblico di un contenitore utilizzando **impostare\_contenitore\_acl()** metodo per specificare il livello di accesso pubblico.

Nell'esempio seguente viene modificato il livello di accesso pubblico al **contenitore**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Per caricare i dati in un blob, utilizzare la **creare\_blocco\_blob()** metodo per creare il blob, utilizzare un file o una stringa come il contenuto del blob.

Il codice seguente consente di caricare il file **test.png** come nuovo blob denominato "immagine blob" nel contenitore.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i contenitori, utilizzare il metodo **list_containers()** .
Per elencare i BLOB in un contenitore, utilizzare **elenco\_blobs()** metodo.

Questo emette gli URL di tutti i BLOB in tutti i contenitori per l'account.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare BLOB, utilizzare la **ottenere\_blob()** metodo per recuperare il contenuto.

Nell'esempio seguente viene illustrato come utilizzare **ottenere\_blob()** per scaricare il contenuto di "immagine blob" e scrivere in un file locale.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Eliminare un Blob
Infine, per eliminare un blob, utilizzare la **eliminare\_blob()** metodo. Nell'esempio seguente viene illustrato come eliminare un blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle attività di archiviazione più complesse, seguire questi collegamenti:

- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Archivio di [Azure SDK per trascrizione](https://github.com/WindowsAzure/azure-sdk-for-ruby) in GitHub
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
