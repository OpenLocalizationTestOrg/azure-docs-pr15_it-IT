<properties
    pageTitle="Usa CLI Azure con lo spazio di archiviazione Azure | Microsoft Azure"
    description="Informazioni su come utilizzare l'interfaccia della riga di comando di Azure (Azure CLI) con Azure lo spazio di archiviazione per creare e gestire gli account di archiviazione e lavorare con BLOB Azure e file. CLI Azure è uno strumento multipiattaforma "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Usa CLI Azure con lo spazio di archiviazione Azure

## <a name="overview"></a>Panoramica

CLI Azure offre un set di Apri origine multipiattaforma comandi per l'utilizzo con la piattaforma Azure. Offre la maggior parte delle stesse funzionalità disponibili nel [portale di Azure](https://portal.azure.com) e le funzionalità di accesso ai dati complessi.

In questa Guida verrà illustrato come utilizzare [Azure della riga di comando CLI (interfaccia Azure)](../xplat-cli-install.md) per eseguire una serie di attività di sviluppo e amministrazione con lo spazio di archiviazione di Azure. È consigliabile scaricare e installare o aggiornare a CLI Azure più recente prima di usare questa Guida.

Questa guida presuppone comprendere i concetti di base di spazio di archiviazione Azure. La Guida fornisce una serie di script per illustrare l'utilizzo della CLI Azure con lo spazio di archiviazione di Azure. Assicurarsi di aggiornare le variabili di script in base alla configurazione prima di eseguire ogni script.

> [AZURE.NOTE] La Guida fornisce esempi di comando e script CLI Azure per gli account di archiviazione classica. Vedere [Usa CLI Azure per Mac, Linux e Windows Azure gestione delle risorse](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) per Azure CLI comandi per gli account di archiviazione di Manager delle risorse.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introduzione all'archiviazione Azure e CLI Azure 5 minuti

Questa guida utilizza Ubuntu per esempi, ma altre piattaforme OS devono eseguire in modo analogo.

**i nuovi utenti di Azure:** È possibile ottenere una sottoscrizione di Microsoft Azure e un account Microsoft associato a tale abbonamento. Per informazioni sulle opzioni di acquisto Azure, vedere [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/)e [Offre membro](https://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network e BizSpark e altri programmi Microsoft).

Per ulteriori informazioni sulle sottoscrizioni Azure, vedere [assegnazione di ruoli di amministratore di Azure Active Directory (Azure Active Directory)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Dopo la creazione di un abbonamento a Microsoft Azure e account:**

1. Scaricare e installare CLI Azure seguendo le istruzioni riportate in [installare CLI Azure](../xplat-cli-install.md).
2. Dopo aver installata CLI Azure, sarà possibile usare il comando azure dall'interfaccia della riga di comando (Bash, terminale, il prompt dei comandi) per accedere ai comandi CLI Azure. Tipo di `azure` comando per verificare l'output seguente.

    ![Comando Azure Output][Image1]

3. Nell'interfaccia della riga di comando, digitare `azure storage` per elencare tutti i comandi di archiviazione azure e ottenere una visione prima delle funzionalità CLI Azure fornisce. È possibile digitare il nome del comando con il parametro **-h** (ad esempio `azure storage share create -h`) per visualizzare i dettagli di una sintassi di comando.
4. A questo punto, ci sono un semplice script che mostra i comandi di Azure CLI di base per accedere all'archiviazione Azure. Lo script prima verrà chiesto di impostare due variabili per l'account di archiviazione e la chiave. Sarà quindi creare un contenitore di nuovo il nuovo account di archiviazione lo script caricare un file di immagine esistente (blob) in tale contenitore. Dopo lo script Elenca tutti i BLOB in tale contenitore, esso verrà scaricato il file di immagine nella directory di destinazione presente nel computer locale.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. Nel computer locale, aprire l'editor di testo preferito (vim ad esempio). Digitare lo script precedente nell'editor di testo.

6. A questo punto, è necessario aggiornare le variabili di script in base alle impostazioni di configurazione.

    - **< storage_account_name >** Usare il nome specificato nello script oppure immettere un nuovo nome per il proprio account di archiviazione. **Importanti:** Il nome dell'account di archiviazione deve essere univoco in Azure. Deve essere minuscola, troppo!

    - **< storage_account_key >** Tasto di scelta del proprio account di archiviazione.

    - **< container_name >** Usare il nome specificato nello script oppure immettere un nuovo nome per il contenitore.

    - **< image_to_upload >** Immettere il percorso di un'immagine nel computer locale, ad esempio: "~ / images/HelloWorld.png".

    - **< destination_folder >** Immettere il percorso di una directory locale per archiviare i file scaricati dallo spazio di archiviazione di Azure, ad esempio: "~/downloadImages".

7. Dopo aver aggiornato variabili necessarie in vim, premere combinazioni di tasti "Esc,:, wq!" Per salvare lo script.

8. Per eseguire questo script, è sufficiente digitarne il nome del file di script nella console di bash. Dopo l'esecuzione di script, è necessario disporre di una cartella di destinazione locale che include il file di immagine scaricata. Schermata seguente è illustrato un esempio dell'output:

Dopo l'esecuzione di script, è necessario disporre di una cartella di destinazione locale che include il file di immagine scaricata.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gestire gli account di archiviazione con CLI Azure

### <a name="connect-to-your-azure-subscription"></a>Connettersi al proprio abbonamento Azure

Durante la maggior parte dei comandi di spazio di archiviazione funzionerà senza un abbonamento a Azure, è consigliabile connettersi al proprio abbonamento da CLI Azure. Per configurare CLI Azure per funzionare con l'abbonamento, seguire i passaggi descritti in [connettersi a un abbonamento Azure da CLI Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione

Per utilizzare lo spazio di archiviazione Azure, è necessario un account di archiviazione. È possibile creare un nuovo account di archiviazione Azure dopo aver configurato il computer per connettersi al proprio abbonamento.

        azure storage account create <account_name>

Il nome del proprio account di archiviazione deve essere compreso tra 3 e 24 caratteri e utilizzare numeri e lettere minuscole solo.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Impostare un account di archiviazione Azure predefinito in variabili di ambiente

È possibile impostare più account di archiviazione nell'abbonamento. È possibile scegliere una di esse e impostarlo nelle variabili di ambiente per tutti i comandi di spazio di archiviazione nella stessa sessione. In questo modo è possibile eseguire i comandi di spazio di archiviazione di Azure CLI senza specificare l'account di archiviazione e la chiave in modo esplicito.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Un altro modo per configurare un account di archiviazione predefinito utilizza stringa di connessione. Ottenere innanzitutto la stringa di connessione dal comando:

        azure storage account connectionstring show <account_name>

Quindi copiare la stringa di connessione di output e impostarla su variabile di ambiente:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Creare e gestire BLOB

Archiviazione Blob Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio testo o dati binari, che è possibile accedervi da in un punto qualsiasi nel mondo tramite HTTP o HTTPS. In questa sezione presuppone che si ha già familiarità con i concetti di spazio di archiviazione Blob Azure. Per informazioni dettagliate, vedere [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Creare un contenitore

Ogni blob in archiviazione Azure deve essere in un contenitore. È possibile creare un contenitore private utilizzando il `azure storage container create` comando:

        azure storage container create mycontainer

> [AZURE.NOTE] Esistono tre livelli di accesso in lettura anonimo: **disattivare** **Blob**e **contenitore**. Per impedire l'accesso anonimo BLOB, impostare il parametro di autorizzazione per **disattivare**. Per impostazione predefinita, il nuovo contenitore è privato e possa accedere solo il proprietario dell'account. Per consentire pubblico lettura accesso anonimo alle risorse blob, ma non per i metadati contenitore o all'elenco di BLOB nel contenitore, impostare il parametro di autorizzazione a **Blob**. Per consentire l'accesso in lettura pubblica completa blob risorse, contenitore metadati e l'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione al **contenitore**. Per ulteriori informazioni, vedere [gestire anonimo accesso in lettura contenitori e BLOB](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Archiviazione Blob Azure supporta blocco BLOB e BLOB di pagine. Per ulteriori informazioni, vedere [Understanding blocco BLOB, accodare BLOB e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Per caricare BLOB in un contenitore di sezioni, è possibile utilizzare il `azure storage blob upload`. Per impostazione predefinita, questo comando consente di caricare i file locali un blob di blocco. Per specificare il tipo per il blob, è possibile utilizzare il `--blobtype` parametro.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Scaricare BLOB da un contenitore

Nell'esempio seguente viene illustrato come scaricare BLOB da un contenitore.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copiare BLOB

È possibile copiare in modo asincrono BLOB all'interno o più aree geografiche e gli account di archiviazione.

Nell'esempio seguente viene illustrato come copiare BLOB da un account di archiviazione. In questo esempio abbiamo creare un contenitore in cui BLOB sono pubblicamente, accessibile in modalità anonima.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

In questo esempio esegue una copia asincrona. È possibile monitorare lo stato di ogni operazione copia eseguendo la `azure storage blob copy show` operazione.

Si noti che l'URL di origine specificato per l'operazione di copia devono essere accessibile al pubblico o includere un token SA (firma accesso condiviso).

### <a name="delete-a-blob"></a>Eliminare un blob

Per eliminare un blob, usare il seguente comando:

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Creare e gestire condivisioni file

Spazio di archiviazione File Azure offre condivisa dello spazio di archiviazione per le applicazioni che usano il protocollo piccole e medie imprese standard. Macchine virtuali di Microsoft Azure e servizi cloud, nonché le applicazioni in locale, è possono condividere dati di file tramite condivisioni collegate. È possibile gestire dati di file tramite CLI Azure e nelle condivisioni file. Per ulteriori informazioni sull'archiviazione dei File di Azure, vedere [Guida introduttiva a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md) e [sull'archiviazione dei File di Azure con Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Creare una condivisione file

Condivisione di File di Azure è una condivisione file di piccole e medie imprese in Azure. Tutti i file e directory devono essere creati in una condivisione file. Un account può contenere un numero illimitato di quote e una condivisione consentono di memorizzare un numero illimitato di file, entro i limiti delle capacità dell'account di archiviazione. Nell'esempio seguente viene creata una condivisione file denominata **condivisione**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Creare una directory

Una directory fornisce una struttura gerarchica facoltativa per una condivisione file Azure. Nell'esempio seguente viene creata una directory denominata **myDir** in condivisione file.

        azure storage directory create myshare myDir

Nota il percorso della directory può includere più livelli, *ad esempio*, **un / b**. Tuttavia, è necessario verificare l'esistenza di tutte le directory padre. Ad esempio, per percorso **un / b**, è necessario creare directory **una** prima di tutto, quindi creare directory **b**.

### <a name="upload-a-local-file-to-directory"></a>Caricare un file locale alla directory

Nell'esempio seguente consente di caricare un file da **~/temp/samplefile.txt** alla directory **myDir** . Modificare il percorso del file in modo che faccia riferimento a un file valido sul computer locale:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Si noti che un file nella condivisione può arrivare fino a 1 TB dimensioni.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Elencare i file nella radice di condivisione o directory

È possibile elencare i file e sottodirectory in una radice di condivisione o una directory utilizzando il comando seguente:

        azure storage file list myshare myDir

Si noti che il nome della directory è facoltativo per l'operazione di elenco. Se omesso, il comando Visualizza il contenuto della directory radice della condivisione.

### <a name="copy-files"></a>Copiare i file

A partire da versione 0.9.8 di Azure CLI, è possibile copiare un file a un altro file, un file a un blob o un blob in un file. Di seguito viene illustrato come eseguire queste operazioni di copia usando i comandi CLI. Per copiare un file per la nuova directory:

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Copiare un blob di una directory di file:

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Passaggi successivi

Ecco alcuni articoli correlati e risorse utili per ottenere ulteriori informazioni sull'archiviazione di Azure.

- [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Riferimento all'API REST di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
