<properties
    pageTitle="Come usare archiviazione Blob da Node | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione oggetto)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Come usare archiviazione Blob da Node. js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

In questo articolo viene illustrato come eseguire gli scenari comuni utilizzando archiviazione Blob. Gli esempi sono scritti tramite l'API Node. Gli scenari coperti includono come caricare, elenco, scaricare ed eliminare BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione di Node. js

Per istruzioni su come creare un'applicazione Node, vedere [creare un'app web Node nel servizio App Azure], [compilare e distribuire un'applicazione di Node a un servizio Cloud di Azure] -- usando Windows PowerShell o [compilare e distribuire un'app web Node in Azure tramite Web matrice].

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per accedere all'archiviazione

Per utilizzare lo spazio di archiviazione Azure, è necessario Azure archiviazione SDK per Node, che include un set di raccolte semplicità di utilizzo che comunicare con i servizi di resto dello spazio di archiviazione.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Consente di ottenere il pacchetto di nodo pacchetto Manager (NPM)

1.  Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Unix) per passare alla cartella in cui è stato creato l'applicazione di esempio.

2.  Nella finestra di comando, digitare **npm installare archiviazione azure** . Output del comando è simile all'esempio seguente.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  È possibile eseguire manualmente il comando **ls** per verificare che un **nodo\_moduli** cartella è stata creata. All'interno di tale cartella, individuare il pacchetto di **archiviazione di azure** , che contiene le librerie che è necessario accedere all'archiviazione.

### <a name="import-the-package"></a>Importare il pacchetto

Usa il blocco note o un altro editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione in cui si prevede di utilizzare lo spazio di archiviazione:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione Azure

Modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`, o `AZURE_STORAGE_CONNECTION_STRING`, le informazioni necessarie per connettersi all'account di archiviazione Azure. Se non sono tali variabili, è necessario specificare le informazioni sull'account quando si chiama **createBlobService**.

Per un esempio di impostazione le variabili di ambiente nel [Portale di Azure](https://portal.azure.com) per un'app web Azure, vedere [utilizzo del servizio di tabella Azure Node web app].

## <a name="create-a-container"></a>Creare un contenitore

L'oggetto **BlobService** consente di lavorare con i contenitori e BLOB. Il codice seguente viene creato un oggetto **BlobService** . Aggiungere nella parte superiore **server.js**quanto segue:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] È possibile accedere in modalità anonima blob utilizzando **createBlobServiceAnonymous** e fornire l'indirizzo dell'host. Ad esempio, utilizzare `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Per creare un nuovo contenitore, utilizzare **createContainerIfNotExists**. Nell'esempio seguente crea un nuovo contenitore denominato 'mycontainer':

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Se il contenitore appena creato, `result.created` è vera. Se il contenitore esiste già, `result.created` è false. `response`informazioni sull'operazione, incluse informazioni ETag per il contenitore.

### <a name="container-security"></a>Protezione contenitore

Per impostazione predefinita, nuovi contenitori sono privati e non sono consentito l'accesso anonimo. Per rendere pubblico il contenitore in modo che è possibile accedervi in modalità anonima, è possibile impostare il livello di accesso del contenitore **blob** o **contenitore**.

* **blob** - consente l'accesso anonimo di lettura a contenuto blob e i metadati in questo contenitore, ma non a contenitore metadati, ad esempio l'elenco di tutti i BLOB in un contenitore

* **contenitore** - consente l'accesso anonimo di lettura a contenuto blob e dei metadati, nonché i metadati contenitore

Nell'esempio seguente viene illustrata l'impostazione il livello di accesso a **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

In alternativa, è possibile modificare il livello di accesso di un contenitore utilizzando **setContainerAcl** per specificare il livello di accesso. Nell'esempio seguente viene modificato il livello di accesso al contenitore:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

Il risultato contiene le informazioni relative all'operazione, inclusi corrente **ETag** per il contenitore.

### <a name="filters"></a>Filtri

È possibile applicare le operazioni di filtraggio facoltative alle operazioni eseguite mediante **BlobService**. Operazioni di filtro, è possono includere registrazione automaticamente la ripetizione, e così via. Filtri sono oggetti che implementano un metodo con la firma:

    function handle (requestOptions, next)

Dopo la pre-elaborazione opzioni richiesta, il metodo deve chiamare "Avanti" passando callback con la seguente firma:

    function (returnObject, finalCallback, next)

In questo callback e dopo l'elaborazione returnObject (la risposta della richiesta al server), il callback richiede di richiamare Avanti esistenza per continuare l'elaborazione di altri filtri o semplicemente richiamare finalCallback per terminare la chiamata al servizio.

Due filtri che implementano ritentare sono inclusi in Azure SDK per Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Di seguito crea un oggetto **BlobService** utilizzato **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Esistono tre tipi di BLOB: bloccare BLOB, BLOB della pagina e aggiungere BLOB. BLOB blocco consente di caricare in modo efficiente grandi quantità di dati. Accodare BLOB sono ottimizzati per le operazioni di Accodamento. Pagina BLOB sono ottimizzati per la lettura/scrittura. Per ulteriori informazioni, vedere [Understanding blocco BLOB, BLOB di accodamento e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>BLOB di blocco

Per caricare dati in un blob di blocco, utilizzare le operazioni seguenti:

* **createBlockBlobFromLocalFile** - crea un nuovo blob di blocco e carica il contenuto di un file

* **createBlockBlobFromStream** - crea un nuovo blob di blocco e carica il contenuto di un flusso

* **createBlockBlobFromText** - crea un nuovo blob di blocco e carica il contenuto di una stringa

* **createWriteStreamToBlockBlob** - fornisce un flusso di scrittura per un blob di blocco

Nell'esempio seguente consente di caricare il contenuto del file di **test. txt** in **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Il `result` restituito da questi metodi contiene le informazioni sul funzionamento, ad esempio **ETag** del blob.

### <a name="append-blobs"></a>Accodare BLOB

Per caricare dati in un nuovo blob di accodamento, utilizzare le operazioni seguenti:

* **createAppendBlobFromLocalFile** - crea un nuovo blob di accodamento e carica il contenuto di un file

* **createAppendBlobFromStream** - crea un nuovo blob di accodamento e carica il contenuto di un flusso

* **createAppendBlobFromText** - crea un nuovo blob di accodamento e carica il contenuto di una stringa

* **createWriteStreamToNewAppendBlob** - crea un nuovo blob di accodamento e quindi fornisce un flusso di scrittura

Nell'esempio seguente consente di caricare il contenuto del file di **test. txt** in **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Per aggiungere un blocco di un blob Accodamento esistente, utilizzare le operazioni seguenti:

* **appendFromLocalFile** - accodare il contenuto di un file a un blob Accodamento esistente

* **appendFromStream** - accodare il contenuto di un flusso a un blob Accodamento esistente

* **appendFromText** - accodare il contenuto di una stringa a un blob Accodamento esistente

* **appendBlockFromStream** - accodare il contenuto di un flusso a un blob Accodamento esistente

* **appendBlockFromText** - accodare il contenuto di una stringa a un blob Accodamento esistente

> [AZURE.NOTE] appendFromXXX API produrrà la convalida dei lato client per non essere rapidamente evitando unncessary server chiamate. non è possibile appendBlockFromXXX.

Nell'esempio seguente consente di caricare il contenuto del file di **test. txt** in **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>BLOB di pagine

Per caricare dati in un blob di pagine, utilizzare le operazioni seguenti:

* **createPageBlob** - crea un nuovo blob di pagina di una lunghezza specifica

* **createPageBlobFromLocalFile** - crea un nuovo blob di pagina e carica il contenuto di un file

* **createPageBlobFromStream** - crea un nuovo blob di pagina e carica il contenuto di un flusso

* **createWriteStreamToExistingPageBlob** - fornisce un flusso di scrittura per un blob di pagina esistente

* **createWriteStreamToNewPageBlob** - crea un nuovo blob di pagina e quindi fornisce un flusso di scrittura

Nell'esempio seguente consente di caricare il contenuto del file di **test. txt** in **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Pagina BLOB è costituito da 512 byte "pagine". Si riceverà un messaggio di errore durante il caricamento di dati con dimensioni che non sono un multiplo di 512.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, utilizzare il metodo **listBlobsSegmented** . Se si desidera tornare BLOB con un prefisso specifico, usare **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

Il `result` contiene un `entries` insieme, ovvero una matrice di oggetti che descrivono ogni blob. Se tutti i BLOB non possono essere restituiti, il `result` vengono forniti anche un `continuationToken`, che è possibile utilizzare come secondo parametro per recuperare voci aggiuntive.

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare i dati da un blob, utilizzare le operazioni seguenti:

* **getBlobToLocalFile** - scrive il contenuto di blob al file

* **getBlobToStream** - scrive il contenuto di blob in un flusso

* **getBlobToText** - scrive il contenuto di blob di una stringa

* **createReadStream** - fornisce un flusso di leggere il blob

Nell'esempio seguente viene illustrato come utilizzare **getBlobToStream** per scaricare il contenuto del blob **myblob** e archiviare il file di **output. txt** utilizzando un flusso:

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

Il `result` contiene le informazioni relative blob, incluse informazioni **ETag** .

## <a name="delete-a-blob"></a>Eliminare un blob

Infine, per eliminare un blob, chiamare **deleteBlob**. Nell'esempio seguente elimina blob denominato **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Accesso simultaneo

Per supportare l'accesso simultaneo a un blob da più client o più istanze di processo, è possibile utilizzare **ETag** o **leasing**.

* **Etag** - consente di rilevare che il blob o contenitore è stato modificato da un altro processo

* **Leasing** - consente di ottenere rinnovabili a vicenda, scrivere o eliminare l'accesso a un blob per un periodo di tempo

### <a name="etag"></a>ETag

Utilizzare ETag se è necessario consentire più client o le istanze in cui scrivere il blocco di una pagina o Blob Blob contemporaneamente. ETag consente di determinare se il contenitore o blob è stato modificato dopo la lettura di inizialmente o creata, che consente di evitare di sovrascrivere modifiche approvate da un altro client o processo.

È possibile impostare condizioni ETag tramite facoltativa `options.accessConditions` parametro. Nell'esempio seguente carica i file di **test. txt** solo se il blob esiste già ed è il valore ETag contenuti `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Quando si usa eTag, il modello generale è:

1. Ottenere l'ETag come risultato di un crea, un elenco o una operazione Leggi.

2. Eseguire un'azione, controllare che il valore ETag non è stato modificato.

Se il valore è stato modificato, questa indica che un altro client o istanza blob o sul contenitore dal momento che è stato acquistato il valore ETag.

### <a name="lease"></a>Leasing

È possibile acquistare un nuovo leasing utilizzando il metodo **acquireLease** , che specifica il blob o il contenitore che si desidera ottenere un leasing su. Ad esempio, il codice seguente acquisisce leasing su **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Operazioni successive su **myblob** devono fornire il `options.leaseId` parametro. Leasing ID viene restituito come `result.id` da **acquireLease**.

> [AZURE.NOTE] Per impostazione predefinita, la durata è infinita. È possibile specificare una durata non infinito (tra 15 e 60 secondi) fornendo la `options.leaseDuration` parametro.

Per rimuovere un leasing, utilizzare **releaseLease**. Per interrompere un leasing, ma impedire ad altri utenti di ottenere un nuovo leasing finché la durata originale è scaduto, utilizzare **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Lavorare con le firme di accesso condiviso

Accesso condiviso firme (SA) sono un modo protetto per consentire l'accesso granulare BLOB e contenitori senza fornire il nome dell'account di archiviazione o i codici. Accesso condiviso firme spesso vengono utilizzate per garantire un accesso limitato ai dati, ad esempio consentendo un'app per dispositivi mobili accedere a BLOB.

> [AZURE.NOTE] Mentre è inoltre possibile consentire l'accesso anonimo a BLOB, accesso condiviso firme consentono di accedere più controllata, mentre è necessario generare le associazioni di protezione.

Un'applicazione attendibile, ad esempio un servizio basato su cloud genera firme accesso condiviso mediante **generateSharedAccessSignature** di **BlobService**e fornisce a un'applicazione non attendibile o semi-trusted, ad esempio un'app per dispositivi mobili. Accesso firme generate mediante un criterio, che descrive l'inizio e fine durante il quale sono valide le firme di accesso condiviso condiviso, nonché il livello di accesso concesso per il titolare di firme accesso condiviso.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consente il titolare di firme accesso condiviso eseguire operazioni di blob **myblob** lettura e scadenza 100 minuti dopo la creazione.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Si noti che le informazioni sull'host deve essere fornite, inoltre, come richiesto quando il titolare di firme accesso condiviso tenta di accedere al contenitore.

L'applicazione client utilizza firme accesso condiviso con **BlobServiceWithSAS** per eseguire operazioni su blob. Di seguito ottiene informazioni **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Poiché le firme di accesso condiviso generate con accesso in sola lettura, se si tenta di modificare il blob, verrà restituito un errore.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

È anche possibile usare un elenco di controllo di accesso (ACL) per impostare il criterio di accesso per SA. Ciò è utile se si desidera consentire a più client accedere a un contenitore, ma fornisce criteri di accesso diversi per ogni cliente.

Un ACL è implementato utilizzando una matrice di criteri di accesso con un ID associato a ogni criterio. Nell'esempio seguente viene definito due criteri, uno per utente '1' e uno per 'Utente2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

Nell'esempio seguente ottiene gli ACL per **mycontainer**e quindi aggiunge nuovi criteri utilizzando **setBlobAcl**. In questo modo:

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Dopo aver impostato la ACL, è possibile creare firme di accesso condiviso in base all'ID per un criterio. Nell'esempio seguente viene creato nuove firme accesso condiviso per 'Utente2':

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti.

-   [Spazio di archiviazione di Azure SDK per riferimento all'API di nodo][]
-   [Blog del Team di archiviazione Azure][]
-   Archivio di [Azure SDK lo spazio di archiviazione per nodo][] in GitHub
-   [Centro per sviluppatori Node](/develop/nodejs/)
-   [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

[Spazio di archiviazione di Azure SDK per nodo]: https://github.com/Azure/azure-storage-node

[Creare un'app web Node nel servizio di App Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[App web Node mediante il servizio di tabella Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Compilare e distribuire un'app web Node Azure tramite Web matrice]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Compilare e distribuire un'applicazione di Node a un servizio Cloud di Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Spazio di archiviazione di Azure SDK per riferimento all'API di nodo]: http://dl.windowsazure.com/nodestoragedocs/index.html
