<properties
    pageTitle="Come utilizzare lo spazio di archiviazione Blob Azure da iOS | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione oggetto)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Come usare archiviazione Blob di iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

In questo articolo illustra come eseguire gli scenari comuni utilizzando archiviazione Blob di Microsoft Azure. Gli esempi scritto in obiettivo C e utilizzano la [Libreria di Client di spazio di archiviazione di Azure per iOS](https://github.com/Azure/azure-storage-ios). Gli scenari coperti includono **durante il caricamento**, **voce**, **download**e BLOB di **eliminazione** . Per ulteriori informazioni sui blob, vedere la sezione [Passaggi successivi](#next-steps) . È anche possibile scaricare l' [applicazione di esempio](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) per visualizzare rapidamente l'utilizzo di spazio di archiviazione di Azure in un'applicazione di iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importare la raccolta di iOS archiviazione Azure in un'applicazione

È possibile importare la raccolta di iOS archiviazione Azure nell'applicazione utilizzando [CocoaPod lo spazio di archiviazione di Azure](https://cocoapods.org/pods/AZSClient) o importazione del file **Framework** .

## <a name="cocoapod"></a>CocoaPod

1. Se già fatto, [Installare CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) nel computer in uso, aprire una finestra del terminale e il seguente comando

        sudo gem install cocoapods

2. Successivo nella directory del progetto (la directory contenente il `.xcodeproj` file), creare un nuovo file denominato `Podfile`(senza estensione di file). Aggiungere le operazioni seguenti per `Podfile` e salvare

        pod 'AZSClient'

3. Nella finestra del terminale, passare alla directory del progetto ed eseguire il comando seguente

        pod install

4. Se il `.xcodeproj` è aperta in Xcode, chiuderla. Nella directory del progetto aprire il file di progetto appena creato che avrà il `.xcworkspace` estensione. Questo è il file che si sarà sta lavorando da per ora.

## <a name="framework"></a>Framework
Per utilizzare la libreria di iOS lo spazio di archiviazione di Azure, è necessario innanzitutto compilare il file framework.

1. Prima di tutto, scaricare o duplicare [repo ios di spazio di archiviazione azure](https://github.com/azure/azure-storage-ios).

2. Passare a *ios di spazio di archiviazione azure* -> *raccolta* -> *Libreria Client lo spazio di archiviazione di Azure*e aprire `AZSClient.xcodeproj` in Xcode.

3. In alto a sinistra del Xcode, modificare lo schema attivo da "Libreria di Client lo spazio di archiviazione di Azure" a "Framework".

4. Compilare il progetto (⌘ + B). Verrà creata una `AZSClient.framework` file sul Desktop.

È quindi possibile importare il file framework nell'applicazione eseguendo le operazioni seguenti:

1. Creare un nuovo progetto o aprire un progetto esistente in Xcode.

2. Fare clic sul progetto nel riquadro di spostamento sinistro e fare clic sulla scheda *Generale* nella parte superiore dell'editor del progetto.

3. Nella sezione *Framework collegato e le raccolte* fare clic sul pulsante Aggiungi (+).

4. Fare clic su *Aggiungi altre...*. Individuare e aggiungere la `AZSClient.framework` file appena creato.

5. Nella sezione *Framework collegate e raccolte* , fare di nuovo clic sul pulsante Aggiungi (+).

6. Nell'elenco di raccolte già specificato, cercare `libxml2.2.dylib` e aggiungerlo al progetto.

7. Fare clic sulla scheda *Impostazioni di compilazione* nella parte superiore dell'editor del progetto.

8. Nella sezione *Percorsi di ricerca* , fare doppio clic su *Percorsi di ricerca Framework* e aggiungere il percorso per il `AZSClient.framework` file.

## <a name="import-statement"></a>Istruzione di importazione
È necessario includere l'istruzione import seguente nel file in cui si vuole richiamare l'API di spazio di archiviazione Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operazioni asincrone
> [AZURE.NOTE] Tutti i metodi che l'esecuzione della richiesta in base al servizio sono operazioni asincrone. In alcuni esempi di codice si trovano questi metodi dispongono di un gestore di completamento. Codice all'interno del gestore di completamento verrà eseguito **dopo** che la richiesta viene completata. In corso il codice dopo il gestore di completamento verrà eseguito **durante** la richiesta.

## <a name="create-a-container"></a>Creare un contenitore
Ogni blob in archiviazione Azure deve trovarsi in un contenitore. Nell'esempio seguente viene illustrato come creare un contenitore, chiamato *newcontainer*nell'account di archiviazione se non esiste già. Quando si sceglie un nome per il contenitore, fare attenzione alle regole di denominazione indicate in precedenza.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

È possibile confermare che funziona esamina [Esplora archivi di Microsoft Azure](http://storageexplorer.com) e verificare che *newcontainer* è presente nell'elenco dei contenitori per l'account di archiviazione.

## <a name="set-container-permissions"></a>Impostare le autorizzazioni contenitore
Autorizzazioni del contenitore sono configurate per l'accesso **privato** per impostazione predefinita. Tuttavia, contenitori forniscono esistono diverse opzioni per l'accesso contenitore:

- **Privato**: contenitore e blob dati possono essere letti da solo il proprietario dell'account.

- **BLOB**: dati Blob in questo contenitore possono essere letti tramite richiesta anonima, ma contenitore dati non sono disponibili. Client non è possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima.

- **Contenitore**: contenitore e blob dati possono essere letti tramite richiesta anonima. Client possibile enumerare BLOB all'interno del contenitore tramite richiesta anonima, ma non è possibile enumerare contenitori in account di archiviazione.

Nell'esempio seguente viene illustrato come creare un contenitore con autorizzazioni di accesso **contenitore** che consentono l'accesso pubblico e di sola lettura per tutti gli utenti su Internet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore
Come indicato nella sezione [concetti relativi al servizio Blob](#blob-service-concepts) , archiviazione Blob offre tre tipi diversi di BLOB: bloccare BLOB, accodare BLOB e BLOB della pagina. In questo momento, la raccolta di iOS archiviazione Azure supporta solo BLOB di blocco. Nella maggior parte dei casi, blob blocco è il tipo consigliato da utilizzare.

Nell'esempio seguente viene illustrato come caricare un blob di blocco da un NSString. Se esiste già un blob con lo stesso nome in questo contenitore, il contenuto di questo blob sovrascritti.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

È possibile confermare che funziona esamina [Esplora archivi di Microsoft Azure](http://storageexplorer.com) e verificare che il contenitore, *containerpublic*contiene il blob *sampleblob*. In questo esempio è stato usato un contenitore pubblico in modo è inoltre possibile verificare che questo sia stata eseguita facendo clic su BLOB URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Oltre al caricamento di un blob di blocco da un NSString, sono disponibili metodi simili per NSData, NSInputStream o un file locale.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Nell'esempio seguente viene illustrato come elencare tutti i BLOB in un contenitore. Quando si esegue questa operazione, è necessario attenzione dei parametri seguenti:     

- **continuationToken** - token di continuazione rappresenta da cui deve iniziare l'operazione di elenco. Se viene specificato alcun token, è elencato BLOB dall'inizio. Qualsiasi numero di BLOB può essere elencata da zero fino a un massimo di set. Anche se questo metodo restituisce zero risultati ottimali, se `results.continuationToken` non sia nullo, potrebbero esserci altre BLOB il servizio non elencati.
- **prefisso** - è possibile specificare il prefisso da utilizzare per l'elenco di blob. Verranno elencati solo i blob che iniziano con il prefisso.
- **useFlatBlobListing** - come indicato nella sezione [denominazione e contenitori di riferimento e BLOB](#naming-and-referencing-containers-and-blobs) , anche se il servizio Blob è una combinazione di spazio di archiviazione flat, è possibile creare una gerarchia virtuale assegnando BLOB con informazioni sul percorso. Tuttavia, non uniformi elenco attualmente non è supportato. Questo è disponibile a breve. Per ora, questo valore deve essere`YES`
- **blobListingDetails** - è possibile specificare gli elementi da includere nell'elenco BLOB
    - `AZSBlobListingDetailsNone`: Elenca solo approvato BLOB e non vengono restituiti i metadati blob.
    - `AZSBlobListingDetailsSnapshots`: Elenco approvata BLOB e istantanee blob.
    - `AZSBlobListingDetailsMetadata`: Recupera blob metadati per ogni blob restituito nell'elenco.
    - `AZSBlobListingDetailsUncommittedBlobs`: Elenco BLOB approvato e non salvate.
    - `AZSBlobListingDetailsCopy`: Includere copia proprietà nell'elenco.
    - `AZSBlobListingDetailsAll`: Elencare tutti disponibili BLOB approvato BLOB non salvate e istantanee e restituire tutti i metadati e copia stato per tali BLOB.
- **maxResults** - il numero massimo di risultati da restituire per l'operazione. Utilizzare -1 per non impostato un limite.
- **completionHandler** - il blocco di codice da eseguire con i risultati dell'operazione di elenco.

In questo esempio viene utilizzato per un metodo di supporto chiamata in modo ricorsivo l'elenco BLOB metodo ogni volta che viene restituito un token di continuazione.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Scaricare un blob

Nell'esempio seguente viene illustrato come scaricare un blob a un oggetto NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Eliminare un blob

Nell'esempio seguente viene illustrato come eliminare un blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Eliminare un contenitore di blob

Nell'esempio seguente viene illustrato come eliminare un contenitore.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come utilizzare archiviazione Blob da iOS, seguire questi collegamenti per ulteriori informazioni sulla raccolta iOS e il servizio di archiviazione.

- [Azure libreria Client lo spazio di archiviazione per iOS](https://github.com/azure/azure-storage-ios)
- [Azure archiviazione iOS documentazione di riferimento](http://azure.github.io/azure-storage-ios/)
- [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage)

Se si dispone di domande relative a questa raccolta è possibile effettuare al nostro [forum MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o [Overflow dello Stack](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se si dispone di suggerimenti relativi alle funzionalità per lo spazio di archiviazione di Azure, inviare [Commenti e suggerimenti dello spazio di archiviazione di Azure](https://feedback.azure.com/forums/217298-storage/).
