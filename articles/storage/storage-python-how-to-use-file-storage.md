<properties
    pageTitle="Come utilizzare lo spazio di archiviazione File Azure da Python | Microsoft Azure"
    description="Informazioni su come utilizzare l'archiviazione dei File di Azure da Python per caricare, elenco, download ed eliminare i file."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Come utilizzare lo spazio di archiviazione File Azure da Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Panoramica

In questo articolo illustra come eseguire gli scenari comuni utilizzando l'archiviazione dei File. Gli esempi scritto in Python e utilizzano [Microsoft Azure archiviazione SDK per Python]. Gli scenari coperti includono durante il caricamento, voce, download ed eliminazione di file.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Creare una condivisione

L'oggetto **FileService** consente di lavorare con file, directory e condivisioni. Il codice seguente viene creato un oggetto **FileService** . Aggiungere quanto segue nella parte superiore di qualsiasi file Python in cui si desidera accedere a livello di programmazione archiviazione Azure.

    from azure.storage.file import FileService

Il codice seguente crea un oggetto **FileService** utilizzando la chiave account e nome account lo spazio di archiviazione.  Sostituire "account personale" e 'MiaChiave' con il nome dell'account e la chiave.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

Nell'esempio seguente, è possibile utilizzare un oggetto **FileService** per creare la condivisione, se non esiste.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Caricare un file in una condivisione

Una condivisione di spazio di archiviazione File Azure contiene almeno, una directory radice in cui è possano risiedono file. In questa sezione verranno come caricare un file dall'archivio locale nella cartella radice di una condivisione.

Per creare un file e caricare i dati, utilizzare la **creare\_file\_da\_percorso**, **creare\_file\_da\_flusso**, **creare\_file\_da\_byte** o **creare\_file\_da\_testo** metodi. Sono metodi di alto livello che eseguono le necessarie la suddivisione in blocchi quando le dimensioni dei dati superano 64 MB.

**creare\_file\_da\_percorso** il contenuto di un file dal percorso specificato, i caricamenti e **creare\_file\_da\_flusso** carica il contenuto dal flusso file già aperto. **creare\_file\_da\_byte** carica una matrice di byte, e **creare\_file\_da\_testo** carica il valore di testo specificato utilizzando la codifica specificata (per impostazione predefinita UTF-8).

Nell'esempio seguente consente di caricare il contenuto del file **sunset.png** nel file **myfile** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Procedura: creare una Directory

È anche possibile organizzare lo spazio di archiviazione inserendo i file all'interno di sottodirectory anziché affidare a tutti gli elementi nella cartella principale. Il servizio di archiviazione file Azure consente di creare tanti directory come consentirà l'account. Il codice riportato di seguito verrà creata una directory secondaria denominata **sampledir** nella directory radice.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Procedura: elenco di file e directory in una condivisione

Per elencare i file e directory in una condivisione, utilizzare la **elenco\_directory\_e\_file** metodo. Questo metodo restituisce un generatore. Il codice seguente restituisce il **nome** di ogni file e directory in una condivisione alla console.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Scaricare i file

Per scaricare i dati da un file, usare **ottenere\_file\_a\_percorso**, **ottenere\_file\_a\_flusso**, **ottenere\_file\_a\_byte**, o **ottenere\_file\_a\_testo**. Sono metodi di alto livello che eseguono le necessarie la suddivisione in blocchi quando le dimensioni dei dati superano 64 MB.

Nell'esempio seguente viene illustrato come utilizzare **ottenere\_file\_a\_percorso** per scaricare il contenuto del file **myfile** e archiviare il file **sunset.png in uscita** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Eliminare un file

Infine, per eliminare un file, chiamare **delete_file**.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base archiviazione dei File, seguire questi collegamenti per altre informazioni.

- [Centro per sviluppatori Python](/develop/python/)
- [Servizi di archiviazione Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del Team di archiviazione Azure]
- [Spazio di archiviazione di Microsoft Azure SDK per Python]

[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Spazio di archiviazione di Microsoft Azure SDK per Python]: https://github.com/Azure/azure-storage-python
