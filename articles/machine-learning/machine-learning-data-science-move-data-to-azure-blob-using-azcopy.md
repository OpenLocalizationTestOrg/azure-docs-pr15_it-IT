<properties
    pageTitle="Spostare i dati da e verso archiviazione Blob Azure con AzCopy | Microsoft Azure"
    description="Spostare i dati da e verso archiviazione Blob Azure con AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Spostare i dati da e verso archiviazione Blob Azure con AzCopy

AzCopy è un'utilità della riga di comando progettata per il caricamento, download e copia di dati da e verso blob Microsoft Azure, file e lo spazio di archiviazione tabella.

Per istruzioni sull'installazione AzCopy e altre informazioni sull'utilizzo e la piattaforma Azure, vedere [Guida introduttiva con l'utilità della riga di comando AzCopy](../storage/storage-use-azcopy.md).

Indicazioni sulle tecnologie utilizzati per spostare i dati a e/o dallo spazio di archiviazione Blob Azure sono collegati qui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se si utilizza macchine Virtuali che è stata impostata con gli script di [macchine virtuali di scienze di dati di Azure](machine-learning-data-science-virtual-machines.md), AzCopy è già installato nella macchina virtuale.

> [AZURE.NOTE] Per un'introduzione completa a archiviazione blob Azure, fare riferimento alle [Attività di base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) e al [Servizio Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Prerequisiti

In questo documento presuppone che si disponga di un abbonamento a Azure, un account di archiviazione e la chiave di spazio di archiviazione corrispondente per tale account. Prima di caricamento/download dei dati, è necessario conoscere la chiave account e nome account Azure dello spazio di archiviazione.

- Per configurare un abbonamento a Azure, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni chiave e account, vedere [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Eseguire i comandi di AzCopy

Per eseguire comandi AzCopy, aprire una finestra di comando e passare alla directory di installazione AzCopy nel computer in cui si trova AzCopy.exe eseguibile. 

La sintassi di base per i comandi AzCopy è:

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] È possibile aggiungere il percorso di installazione AzCopy al percorso di sistema e quindi eseguire i comandi da qualsiasi directory. Per impostazione predefinita, AzCopy viene installato *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* o *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Caricare file in un archivio blob Azure

Per caricare un file, usare il comando seguente:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Scaricare i file da un archivio blob Azure

Per scaricare un file da un archivio blob Azure, usare il comando seguente:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Trasferire BLOB tra i contenitori di Azure

Per trasferire BLOB tra i contenitori di Azure, usare il comando seguente:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Suggerimenti per l'uso AzCopy

> [AZURE.TIP]   
> 1. Quando **il caricamento di** file, */S* carica i file in modo ricorsivo. Senza questo parametro, file nelle sottodirectory non vengono caricati.  
> 2. Durante **il download di** file, */S* ricerca in modo ricorsivo contenitore fino a tutti i file nella directory specificata e le relative sottodirectory o tutti i file che corrispondono al modello specificato nella directory specificata e le relative sottodirectory, vengono scaricati.  
> 3.  Non è possibile specificare un **file specifico blob** da scaricare tramite il parametro */origine* . Per scaricare un file specifico, specificare il nome del file blob scaricare utilizzando il parametro */Pattern* . **/S** parametro può essere utilizzato per cercare un nome di file modello in modo ricorsivo AzCopy. Senza il parametro motivo AzCopy Scarica tutti i file nella directory.
