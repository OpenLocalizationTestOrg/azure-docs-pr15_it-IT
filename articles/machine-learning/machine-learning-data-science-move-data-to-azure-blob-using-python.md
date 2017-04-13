<properties
    pageTitle="Spostare i dati da e verso archiviazione Blob Azure con Python | Microsoft Azure"
    description="Spostare i dati da e verso archiviazione Blob Azure con Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Spostare i dati da e verso archiviazione Blob Azure con Python

In questo argomento viene descritto come elenco, caricare e scaricare BLOB tramite l'API Python. Con l'API Python forniti SDK Azure, è possibile:

- Creare un contenitore
- Caricare un blob in un contenitore
- Scaricare BLOB
- Elencare i BLOB in un contenitore
- Eliminare un blob

Per ulteriori informazioni sull'uso dell'API Python, vedere [come utilizzare il servizio di archiviazione Blob da Python](../storage/storage-python-how-to-use-blob-storage.md).

Indicazioni sulle tecnologie utilizzati per spostare i dati a e/o dallo spazio di archiviazione Blob Azure sono collegati qui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se si utilizza macchine Virtuali che è stata impostata con gli script di [macchine virtuali di scienze di dati di Azure](machine-learning-data-science-virtual-machines.md), AzCopy è già installato nella macchina virtuale.

> [AZURE.NOTE] Per un'introduzione completa a archiviazione blob Azure, fare riferimento alle [Attività di base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) e al [Servizio Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Prerequisiti

In questo documento si presuppone che si dispone di un abbonamento a Azure, un account di archiviazione e la chiave di spazio di archiviazione corrispondente per l'account. Prima di caricamento/download dei dati, è necessario conoscere la chiave account e nome account Azure dello spazio di archiviazione.

- Per configurare un abbonamento a Azure, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni chiave e account, vedere [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Caricare dati Blob

Aggiungere il frammento di codice seguente nella parte superiore del codice Python in cui si desidera accedere a livello di programmazione archiviazione Azure:

    from azure.storage.blob import BlobService

L'oggetto **BlobService** consente di lavorare con i contenitori e BLOB. Il codice seguente crea un oggetto BlobService utilizzando la chiave account e nome account lo spazio di archiviazione. Sostituire chiave account e nome dell'account con l'account reale e la chiave.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Per caricare dati in un blob, utilizzare i metodi seguenti:

1. inserire\_blocco\_blob\_da\_percorso (carica il contenuto di un file dal percorso specificato)
2. inserire\_block_blob\_da\_file (file di caricamento del contenuto dal flusso file già aperto)
3. inserire\_blocco\_blob\_da\_byte (il caricamento di una matrice di byte)
4. inserire\_blocco\_blob\_da\_testo (carica il valore di testo specificato utilizzando la codifica specificata)

Il codice di esempio seguente carica un file locale in un contenitore:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Il codice di esempio seguente carica tutti i file (escluse le directory) in una directory locale a archiviazione blob:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Scaricare dati da Blob

Utilizzare i metodi seguenti per scaricare i dati da un blob:
1. ottenere\_blob\_a\_percorso
2. ottenere\_blob\_a\_file
3. ottenere\_blob\_a\_byte
4. ottenere\_blob\_a\_testo

Questi metodi che eseguono le necessarie la suddivisione in blocchi quando le dimensioni dei dati superano 64 MB.

Il codice di esempio seguente consente di scaricare il contenuto di un blob in un contenitore in un file locale:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Nell'esempio Scarica tutti i BLOB da un contenitore. Utilizza elenco\_BLOB per ottenere l'elenco di BLOB disponibili nel contenitore e li scarica in una directory locale.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
