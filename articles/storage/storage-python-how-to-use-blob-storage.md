<properties
    pageTitle="Come utilizzare l'archiviazione Blob Azure (archiviazione degli oggetti) da Python | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione di oggetto)."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-azure-blob-storage-from-python"></a>Come utilizzare lo spazio di archiviazione Blob Azure da Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Archiviazione Blob Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archiviazione BLOB consentono di memorizzare qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.

In questo articolo illustra come eseguire gli scenari comuni utilizzando archiviazione Blob. Gli esempi scritto in Python e utilizzano [Microsoft Azure archiviazione SDK per Python]. Gli scenari coperti includono durante il caricamento, l'elenco, il download e l'eliminazione BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-container"></a>Creare un contenitore

In base al tipo di blob che si desidera utilizzare, creare un oggetto **BlockBlobService**, **AppendBlobService**o **PageBlobService** . Il codice seguente viene utilizzato un oggetto **BlockBlobService** . Aggiungere quanto segue nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione archivio Blob Azure blocco.

    from azure.storage.blob import BlockBlobService

Il codice seguente crea un oggetto **BlockBlobService** utilizzando la chiave account e nome account lo spazio di archiviazione.  Sostituire "account personale" e 'MiaChiave' con il nome dell'account e la chiave.

    block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Nell'esempio seguente, è possibile utilizzare un oggetto **BlockBlobService** per creare il contenitore, se non esiste.

    block_blob_service.create_container('mycontainer')

Per impostazione predefinita, il nuovo contenitore è privato, è necessario specificare il tasto di scelta di spazio di archiviazione (come descritto in precedenza) per scaricare BLOB da questo contenitore. Se si desidera rendere disponibili i BLOB all'interno del contenitore a tutti gli utenti, è possibile creare il contenitore e passare il livello di accesso pubblico utilizzando il codice riportato di seguito.

    from azure.storage.blob import PublicAccess
    block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

In alternativa, è possibile modificare un contenitore dopo averla creata utilizzando il codice seguente.

    block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Dopo questa modifica, tutti gli utenti su Internet può vedere BLOB in un contenitore di pubblico, ma solo è possibile modificare o eliminare.

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Per creare un blob di blocco e caricare i dati, utilizzare la **creare\_blob\_da\_percorso**, **creare\_blob\_da\_flusso**, **creare\_blob\_da\_byte** o **creare\_blob\_da\_testo** metodi. Sono metodi di alto livello che eseguono le necessarie la suddivisione in blocchi quando le dimensioni dei dati superano 64 MB.

**creare\_blob\_da\_percorso** il contenuto di un file dal percorso specificato, i caricamenti e **creare\_blob\_da\_flusso** carica il contenuto dal flusso file già aperto. **creare\_blob\_da\_byte** carica una matrice di byte, e **creare\_blob\_da\_testo** carica il valore di testo specificato utilizzando la codifica specificata (per impostazione predefinita UTF-8).

Nell'esempio seguente consente di caricare il contenuto del file **sunset.png** nel blob **myblob** .

    from azure.storage.blob import ContentSettings
    block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
                )

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, utilizzare la **elenco\_BLOB** metodo. Questo metodo restituisce un generatore. Il codice seguente restituisce il **nome** di ogni blob in un contenitore alla console.

    generator = block_blob_service.list_blobs('mycontainer')
    for blob in generator:
        print(blob.name)

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare i dati da un blob, utilizzare **ottenere\_blob\_a\_percorso**, **ottenere\_blob\_a\_flusso**, **ottenere\_blob\_a\_byte**, o **ottenere\_blob\_a\_testo**. Sono metodi di alto livello che eseguono le necessarie la suddivisione in blocchi quando le dimensioni dei dati superano 64 MB.

Nell'esempio seguente viene illustrato come utilizzare **ottenere\_blob\_a\_percorso** per scaricare il contenuto del blob **myblob** e archiviare il file **sunset.png in uscita** .

    block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## <a name="delete-a-blob"></a>Eliminare un blob

Infine, per eliminare un blob, chiamare **delete_blob**.

    block_blob_service.delete_blob('mycontainer', 'myblockblob')

## <a name="writing-to-an-append-blob"></a>Scrivere un blob di Accodamento

Blob Accodamento ottimizzato per le operazioni di accodamento, ad esempio la registrazione. Ad esempio un blob blocco blob accodamento è costituito da blocchi, ma quando si aggiunge un nuovo blocco a un blob di accodamento, è sempre aggiunti alla fine del blob. Non è possibile aggiornare o eliminare un blocco esistente in un blob di Accodamento. Il blocco di ID per un blob di accodamento non viene esposto come lo sono per un blob di blocco.

Ogni blocco in un blob accodamento può essere una dimensione diversa, fino a un massimo di 4 MB e blob accodamento possono includere un massimo di 50.000 blocchi. La dimensione massima di un blob accodamento è leggermente più 195 GB (4 MB X 50.000 blocchi).

Nell'esempio seguente viene creato un nuovo blob di accodamento e aggiunge alcuni dati, simulazione un'operazione semplice registrazione.

    from azure.storage.blob import AppendBlobService
    append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

    # The same containers can hold all types of blobs
    append_blob_service.create_container('mycontainer')

    # Append blobs must be created before they are appended to
    append_blob_service.create_blob('mycontainer', 'myappendblob')
    append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

    append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione Blob, seguire questi collegamenti per altre informazioni.

- [Centro per sviluppatori Python](/develop/python/)
- [Servizi di archiviazione Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del Team di archiviazione Azure]
- [Spazio di archiviazione di Microsoft Azure SDK per Python]

[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Spazio di archiviazione di Microsoft Azure SDK per Python]: https://github.com/Azure/azure-storage-python
