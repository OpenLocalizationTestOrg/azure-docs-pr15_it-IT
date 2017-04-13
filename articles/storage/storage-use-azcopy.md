<properties
    pageTitle="Copiare o spostare i dati allo spazio di archiviazione con AzCopy | Microsoft Azure"
    description="Utilizzare l'utilità AzCopy per spostare o copiare dati in o da blob, tabella e il contenuto del file. Copiare i dati allo spazio di archiviazione di Azure dai file locali o copiare dati all'interno o tra gli account di archiviazione. Migrare facilmente i dati allo spazio di archiviazione Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Trasferire i dati con l'utilità della riga di comando AzCopy

## <a name="overview"></a>Panoramica

AzCopy è un'utilità della riga di comando di Windows progettata per la copia di dati da e verso archiviazione Blob di Microsoft Azure, File e una tabella usando i comandi semplici con prestazioni ottimali. È possibile copiare dati da un oggetto a un altro all'interno di account di archiviazione o tra gli account di archiviazione.

> [AZURE.NOTE] Questa guida presuppone che si ha già familiarità con [Lo spazio di archiviazione di Azure](https://azure.microsoft.com/services/storage/). In caso contrario, leggere la documentazione di [Introduzione all'archiviazione Azure](storage-introduction.md) possono risultare utili. Soprattutto, sarà necessario [creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per iniziare a usare AzCopy.

## <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy

### <a name="windows"></a>Windows

Scaricare la [versione più recente di AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac o Linux

AzCopy non è disponibile per i sistemi operativi Mac o Linux. Azure CLI invece un'alternativa adatta per la copia di dati da e verso lo spazio di archiviazione di Azure. Letti [utilizzando CLI Azure con lo spazio di archiviazione di Azure](storage-azure-cli.md) per altre informazioni.

## <a name="writing-your-first-azcopy-command"></a>Scrivere il primo comando AzCopy

La sintassi di base per i comandi AzCopy è:

    AzCopy /Source:<source> /Dest:<destination> [Options]

Aprire una finestra di comando e passare alla directory di installazione AzCopy nel computer in uso, in cui il `AzCopy.exe` eseguibile trova. Se lo si desidera, è possibile aggiungere il percorso di installazione AzCopy al percorso di sistema. Per impostazione predefinita, AzCopy viene installato in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` o `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Negli esempi seguenti sono illustrati i vari scenari per copiare i dati per e da Microsoft Azure BLOB, file e le tabelle. Fare riferimento alla sezione [AzCopy parametri](#azcopy-parameters) per una descrizione dettagliata dei parametri utilizzati in ogni campione.

## <a name="blob-download"></a>BLOB: Download

### <a name="download-single-blob"></a>Scaricare blob singolo

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Si noti che, se la cartella `C:\myfolder` non esiste, verrà creato e scaricare AzCopy `abc.txt ` nella nuova cartella.

### <a name="download-single-blob-from-secondary-region"></a>Scaricare blob singolo dall'area secondario

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si noti che è necessario disporre geografico ridondanti dello spazio di archiviazione di accesso in lettura abilitato.

### <a name="download-all-blobs"></a>Scaricare tutti i BLOB

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Si supponga che BLOB seguenti si trovano nel contenitore specificato:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di download, nella directory `C:\myfolder` includerà i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se non si specifica opzione `/S`, non verranno scaricati BLOB.

### <a name="download-blobs-with-specified-prefix"></a>Scaricare BLOB con prefisso specificato

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Si supponga che BLOB seguenti si trovano nel contenitore specificato. Tutti i blob che iniziano con il prefisso `a` , verranno scaricati:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di download, la cartella `C:\myfolder` includerà i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Il prefisso si applica alla directory virtuale che costituisce la prima parte del nome del blob. Nell'esempio riportato sopra, la directory non corrisponde il prefisso specificato non viene scaricato. Inoltre, se l'opzione `\S` non viene specificato, AzCopy non viene scaricato un BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Impostare l'ora dell'ultima modifica di file esportati da essere uguale a quello BLOB origine

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

È inoltre possibile escludere BLOB dall'operazione di download in base a loro volta dell'ultima modifica. Ad esempio, se si desidera escludere BLOB la cui ultima volta è la stessa o più recente del file di destinazione, aggiungere il `/XN` opzione:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

O se si desidera escludere BLOB la cui ultima volta le stesse più recente del file di destinazione, aggiungere il `/XO` opzione:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>BLOB: caricare

### <a name="upload-single-file"></a>Caricare file unico

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Se il contenitore di destinazione specificata non esiste, AzCopy verrà creato e caricare il file al suo interno.

### <a name="upload-single-file-to-virtual-directory"></a>Caricare file unico alla directory virtuale

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Se la directory virtuale specificata non esiste, AzCopy verrà caricare il file in modo da includere la directory virtuale nel nome (*ad esempio*, `vd/abc.txt` nell'esempio precedente).

### <a name="upload-all-files"></a>Caricare tutti i file

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Opzione `/S` consente di caricare in modo ricorsivo lo spazio di archiviazione Blob, vale a dire che tutte le sottocartelle e i propri file verranno caricati anche il contenuto della directory specificata. Ad esempio, si supponga che si trovano i file seguenti nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se non si specifica opzione `/S`, AzCopy non verrà caricato in modo ricorsivo. Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Caricare i file corrispondenti criterio specificato

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Si supponga che si trovano i file seguenti nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore includerà i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se non si specifica opzione `/S`, AzCopy solo caricare BLOB che non si trovano in una directory:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Specificare il tipo di contenuto MIME di un blob di destinazione

Per impostazione predefinita, AzCopy imposta il tipo di contenuto di un blob di destinazione per `application/octet-stream`. A partire da versione 3.1.0, è possibile specificare in modo esplicito il tipo di contenuto tramite l'opzione `/SetContentType:[content-type]`. La sintassi seguente imposta il tipo di contenuto per tutti i BLOB in un'operazione di caricamento.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se si specifica `/SetContentType` senza un valore, quindi AzCopy consente di impostare ogni blob o tipo di contenuto del file in base all'estensione del file.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>BLOB: copia

### <a name="copy-single-blob-within-storage-account"></a>Copiare blob singolo all'interno di account di archiviazione

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Quando si copia un blob all'interno di un account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-across-storage-accounts"></a>Copiare blob singolo tra gli account di archiviazione

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Quando si copia un blob tra gli account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiare blob singolo dall'area secondaria all'area principale

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Nota è necessario che geografico ridondanti dello spazio di archiviazione di accesso in lettura abilitata.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiare gli account di archiviazione blob singolo e relativi snapshot

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Dopo l'operazione copia il contenitore di destinazione includerà il blob e relativi snapshot. Supponendo che blob nell'esempio precedente sono disponibili due istantanee, il contenitore includerà blob e istantanee seguenti:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copiare in modo sincrono BLOB tra gli account di archiviazione

AzCopy per impostazione predefinita i dati copiati tra due endpoint di spazio di archiviazione in modo asincrono. Di conseguenza, l'operazione di copia verrà eseguito in background utilizzando la capacità della larghezza di banda riserva contenente alcun contratto di servizio in termini di come rapidamente un blob verrà copiato e AzCopy verificherà periodicamente lo stato di copia fino a quando la copia è stata completata o non.

Il `/SyncCopy` opzione garantisce che l'operazione di copia otterrà velocità coerenti. AzCopy esegue la copia icona del scaricando BLOB per copiare dall'origine specificata nella memoria locale e caricando quindi alla destinazione di archiviazione Blob.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`potrebbe generare costi aggiuntivi uscita rispetto alla copia asincrono, si consiglia consiste nell'usare questa opzione in una macchina virtuale Azure nella stessa regione l'account di archiviazione di origine per evitare i costi di uscita.

## <a name="file-download"></a>File: Download

### <a name="download-single-file"></a>Scaricare file unico

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se l'origine specificato è una condivisione file Azure, quindi è necessario specificare il nome esatto, (*ad esempio* `abc.txt`) consente di scaricare un singolo file o specificare opzione `/S` per scaricare tutti i file in modo ricorsivo Condividi. Se si tenta di specificare un modello di file e l'opzione `/S` insieme verrà generato un errore.

### <a name="download-all-files"></a>Scaricare tutti i file

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Si noti che eventuali cartelle vuote non verranno scaricate.

## <a name="file-upload"></a>File: caricare

### <a name="upload-single-file"></a>Caricare file unico

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Caricare tutti i file

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Si noti che le cartelle vuote non verrà caricate.

### <a name="upload-files-matching-specified-pattern"></a>Caricare i file corrispondenti criterio specificato

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>File: copia

### <a name="copy-across-file-shares"></a>Copiare in condivisioni file

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copiare in una condivisione di blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Si noti che la copia asincrono dallo spazio di archiviazione File a pagina Blob non è supportata.

### <a name="copy-from-blob-to-file-share"></a>Copiare blob in una condivisione file

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Copiare i file in modo sincrono

È possibile specificare il `/SyncCopy` opzione per copiare i dati dallo spazio di archiviazione File allo spazio di archiviazione di File e dallo spazio di archiviazione File allo spazio di archiviazione Blob dallo spazio di archiviazione Blob allo spazio di archiviazione File in modo sincrono, AzCopy a tale scopo scaricare i dati di origine di memoria locale e ricaricarlo alla destinazione.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Quando si copiano dallo spazio di archiviazione File allo spazio di archiviazione Blob, il tipo di blob predefinito è blob blocco, l'utente può specificare l'opzione `/BlobType:page` per modificare il tipo di blob di destinazione.

Si noti che `/SyncCopy` potrebbe generare uscita altro costi confronto alla copia asincrono, si consiglia consiste nell'usare questa opzione in macchine Virtuali di Azure che si trova nella stessa regione l'account di archiviazione di origine per evitare i costi di uscita.

## <a name="table-export"></a>Tabella: esportare

### <a name="export-table"></a>Tabella di esportazione

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy scrive un file manifesto alla cartella di destinazione specificata. Il file manifesto viene utilizzato nel processo di importazione per individuare i file di dati necessari ed eseguire la convalida dei dati. Per impostazione predefinita, il file manifesto utilizza la convenzione di denominazione seguente:

    <account name>_<table name>_<timestamp>.manifest

È inoltre possibile impostare l'opzione `/Manifest:<manifest file name>` per impostare il nome di file manifesto.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Dividere in più file di esportazione

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utilizza un *indice di volume* nei nomi di file di dati divisa per distinguere più file. L'indice di volume è costituito da due parti, un *indice chiave intervallo partizione* e un *file indice di divisione*. Entrambi gli indici sono in base zero.

L'indice di intervallo chiave partizione sarà 0 se l'utente non specifica l'opzione `/PKRS`.

Si supponga ad esempio AzCopy genera due file di dati dopo l'utente specifica opzione `/SplitSize`. Nomi di file di dati risultante sarà:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Si noti che il minimo possibile valore per l'opzione `/SplitSize` 32 MB. Se la destinazione specificata archiviazione Blob, AzCopy verranno dividere il file di dati quando le dimensioni raggiunge la limitazione delle dimensioni della blob (200GB), indipendentemente dall'opzione se `/SplitSize` è stata specificata dall'utente.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Esporta tabella in formato di file di dati JSON o CSV

AzCopy per impostazione predefinita consente di esportare tabelle ai file di dati JSON. È possibile specificare l'opzione `/PayloadFormat:JSON|CSV` per esportare le tabelle come JSON o CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Quando si specifica il formato del carico utile CSV, AzCopy anche verrà generato un file di schema con estensione `.schema.csv` per ogni file di dati.

### <a name="export-table-entities-concurrently"></a>Esporta tabella entità contemporaneamente

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy inizierà operazioni simultanee per esportare entità quando l'utente specifica opzione `/PKRS`. Ogni azione consente di esportare un intervallo di chiave partizione.

Si noti che il numero di operazioni simultanee viene anche controllato dall'opzione `/NC`. AzCopy utilizza il numero di processori core il valore predefinito di `/NC` quando si copiano entità della tabella, anche se `/NC` non è stato specificato. Quando l'utente specifica opzione `/PKRS`, AzCopy viene utilizzato il valore minore dei due valori - intervalli di chiavi partizione oppure in modo implicito o esplicito specificato operazioni simultanee - per determinare il numero di operazioni simultanee per iniziare. Per ulteriori informazioni, digitare `AzCopy /?:NC` nella riga di comando.

### <a name="export-table-to-blob"></a>Esportazione della tabella blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy verrà generato un file di dati JSON nel contenitore blob con seguendo una convenzione di denominazione:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Il file di dati JSON generato segue il formato del carico utile per i metadati minimo. Per informazioni dettagliate su questo formato del carico utile, vedere [Formato del carico utile per operazioni di servizio di tabella](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Si noti che quando si esportano tabelle a BLOB, AzCopy verrà scaricare l'entità della tabella nel file di dati temporanei locali e quindi caricare tali entità il blob. Questi file di dati temporanei vengono inseriti nella cartella di file del diario con il percorso predefinito "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", è possibile specificare opzione/z: [cartella di file del diario] per modificare le registrazioni percorso della cartella di file e quindi cambiare il percorso di file di dati temporanei. Scelto di dimensioni dei file di dati temporanei, dimensioni dei entità di tabella e la dimensione specificata con /SplitSize opzione, anche se il file di dati temporanei nel disco locale verrà eliminato immediatamente dopo che è stato caricato a quello, assicurarsi che si dispone di spazio sufficiente sul disco locale per archiviare i file di dati temporanei prima dell'eliminazione.

## <a name="table-import"></a>Tabella: importazione

### <a name="import-table"></a>Tabella di importazione

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

L'opzione `/EntityOperation` indica la modalità di inserimento di entità nella tabella. Valori possibili sono:

- `InsertOrSkip`: Ignora un'entità esistente o inserire una nuova entità, se non esiste nella tabella.
- `InsertOrMerge`: Unisce un'entità esistente o inserire una nuova entità se non esiste nella tabella.
- `InsertOrReplace`: Sostituisce un'entità esistente o inserire una nuova entità se non esiste nella tabella.

Si noti che non è possibile specificare opzione `/PKRS` in uno scenario di importazione. Diversamente da quanto succede lo scenario di esportazione, in cui è necessario specificare l'opzione `/PKRS` per avviare operazioni simultanee, AzCopy per impostazione predefinita inizierà operazioni simultanee quando si importa una tabella. Il numero predefinito di operazioni simultanee avviato è uguale al numero di processori core; Tuttavia, è possibile specificare un numero differente di simultanee con l'opzione `/NC`. Per ulteriori informazioni, digitare `AzCopy /?:NC` nella riga di comando.

Si noti che AzCopy supporta solo l'importazione per JSON, non CSV. AzCopy non supporta importazioni di tabella da JSON creati dall'utente e i file manifesto. Entrambi i file deve provenire da un'esportazione della tabella AzCopy. Per evitare errori, non modificare JSON esportato o file manifesto.

### <a name="import-entities-to-table-using-blobs"></a>Importazione di entità alla tabella utilizzando BLOB di

Si supponga un contenitore di Blob contiene le operazioni seguenti: file JSON oggetto che rappresenta una tabella di Azure e il relativo file manifesto.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

È possibile eseguire il seguente comando per importare entità in una tabella utilizzando il file manifesto in tale contenitore blob:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Altre caratteristiche AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiare solo i dati che non esistono nella destinazione

Il `/XO` e `/XN` parametri consentono di escludere le risorse di origine precedente o successiva venga copiato, rispettivamente. Se si desidera copiare le risorse di origine che non esistono nella destinazione, è possibile specificare entrambi i parametri del comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Nota: Questo non è supportato quando si trova una tabella di origine o di destinazione.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Usare un file di risposta per specificare i parametri della riga di comando

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

È possibile includere tutti i parametri della riga di comando AzCopy in un file di risposta. AzCopy elabora i parametri del file come se fosse stato specificati nella riga di comando, eseguire una sostituzione diretta con il contenuto del file.

Si supponga di un file di risposta denominato `copyoperation.txt`, che contiene le righe seguenti. È possibile specificare ogni parametro AzCopy su una sola riga

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

o in righe distinte:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy non riuscirà se si divide il parametro su due righe, come illustrato di seguito per il `/sourcekey` parametro:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilizzare più file di risposta per specificare i parametri della riga di comando

Si supponga di un file di risposta denominato `source.txt` che specifica un contenitore di origine:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

File di risposta denominato `dest.txt` che specifica una cartella di destinazione nel file system:

    /Dest:C:\myfolder

File di risposta denominato `options.txt` che specifica le opzioni per AzCopy:

    /S /Y

Per chiamare AzCopy con questi file di risposta, tutte attività che si trovano in una directory `C:\responsefiles`, utilizzare questo comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy elabora questo comando esattamente come accadrebbe se sono inclusi tutti i parametri singoli nella riga di comando:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Specificare una firma di accesso condiviso (SA)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

È inoltre possibile specificare un SA sul contenitore URI:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Cartella di file diario

Ogni volta che si esegue un comando per AzCopy, viene verificato se un file journal presente nella cartella predefinita o se esiste in una cartella specificata tramite questa opzione. Se il file del diario non esiste in entrambe le posizioni, AzCopy considera l'operazione nuova e genera un nuovo file di diario.

Se il file del diario esiste, AzCopy controllerà se la riga di comando che input corrisponde a riga di comando nel file del diario. Se corrispondono a due righe di comando, AzCopy riprende l'incompleto. Se non corrispondono, verrà richiesto di essere sovrascrivere il file del diario per avviare una nuova operazione o annullare l'operazione corrente.

Se si desidera utilizzare il percorso predefinito per il file del diario:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se si omette opzione `/Z`, oppure specificare l'opzione `/Z` senza il percorso della cartella, come illustrato sopra, AzCopy crea il file journal nella posizione predefinita, ossia `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file del diario esiste già, AzCopy riprende l'operazione in base al file del diario.

Se si desidera specificare un percorso personalizzato per il file del diario:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In questo esempio viene creato il file journal se non esiste già. Se esiste, AzCopy riprende operazione in base al file del diario.

Se si desidera riprendere un'operazione AzCopy:

    AzCopy /Z:C:\journalfolder\

In questo esempio riprende l'ultima operazione che potrebbe avere non è stata completata.

### <a name="generate-a-log-file"></a>Generare un file di log

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se si specifica opzione `/V` senza specificare il percorso file registro dettagliato, quindi AzCopy crea file di log nella posizione predefinita, ossia `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

In caso contrario, è possibile creare un file di log in un percorso personalizzato:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Si noti che se si specifica un percorso relativo dopo opzione `/V`, ad esempio `/V:test/azcopy1.log`, registro dettagliato viene creato nella cartella di lavoro corrente all'interno di una sottocartella denominata `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Specificare il numero di operazioni simultanee per iniziare

Opzione `/NC` specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità di trasferimento di dati. Per le operazioni di tabella, il numero di operazioni simultanee è uguale al numero di processori che disponibili. Per le operazioni Blob e File, il numero di operazioni simultanee è uguale a 8 volte il numero di processori che disponibili. Se si esegue AzCopy in una rete di larghezza di banda ridotta, è possibile specificare un numero inferiore per /NC evitare errori causati da concorrenza delle risorse.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Eseguire l'archiviazione Azure emulatore AzCopy

È possibile eseguire AzCopy su [Azure emulatore di spazio di archiviazione](storage-use-emulator.md) BLOB:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

e tabelle:

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Parametri AzCopy

Parametri per AzCopy sono descritti di seguito. È possibile digitare uno dei comandi seguenti dalla riga di comando per assistenza con AzCopy:

- Per informazioni dettagliate della riga di comando per AzCopy:`AzCopy /?`
- Per informazioni dettagliate con qualsiasi parametro AzCopy:`AzCopy /?:SourceKey`
- Per esempi della riga di comando:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Origine: "origine"

Specifica l'origine dati da cui si desidera copiare. L'origine può essere una directory di sistema di file, un contenitore di blob, una directory virtuale blob, una condivisione file di spazio di archiviazione, una directory di archiviazione file o una tabella di Azure.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="destdestination"></a>/ Destinazione: "destinazione"

Specifica la destinazione in cui copiare. La destinazione può essere una directory di sistema di file, un contenitore di blob, una directory virtuale blob, una condivisione file di spazio di archiviazione, una directory di archiviazione file o una tabella di Azure.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="patternfile-pattern"></a>/ Modello: "modello di file"

Specifica un modello di file che indica i file da copiare. Il comportamento del parametro /Pattern dipende dalla posizione dei dati di origine e la presenza dell'opzione modalità ricorsiva. La modalità ricorsiva specificata tramite l'opzione /S.

Se l'origine specificata una directory nel file system, quindi i caratteri jolly standard sono valide e corrispondente al modello di file disponibile per i file all'interno della directory. Se l'opzione che /s viene specificato, quindi AzCopy anche corrisponde al modello specificato per tutti i file in una sottocartella della directory.

Se l'origine specificata è un contenitore di blob o directory virtuale, i caratteri jolly non vengono applicate. Se l'opzione che /s viene specificato, quindi AzCopy interpreta il modello di file specificato come un prefisso blob. Se l'opzione che /s non viene specificato, quindi AzCopy corrisponde al modello di file con nomi blob esatta.

Se l'origine specificato è una condivisione file Azure, quindi è necessario specificare il nome file esatto (ad esempio ABC) per copiare un singolo file o specificare opzione /S per copiare tutti i file in modo ricorsivo Condividi. Tentativo di specificare un modello di file e l'opzione /S insieme verrà generato un errore.

AzCopy utilizza la corrispondenza tra maiuscole e minuscole quando il /origine è un contenitore di blob o directory virtuale blob e utilizza corrispondenza di maiuscole e in tutti gli altri casi.

Il modello di file predefinito quando non viene specificato alcun criterio per il file è *.* per un percorso del file system o un prefisso vuoto per una posizione di archiviazione Azure. Non è possibile specificare più modelli di file.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="destkeystorage-key"></a>/ DestKey: "chiave di archiviazione"

Specifica la chiave account lo spazio di archiviazione per la risorsa di destinazione.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="destsassas-token"></a>/ DestSAS: "token di SA"

Specifica una firma di Access condiviso (SA) con autorizzazioni di lettura e scrittura per la destinazione (se applicabile). Racchiudere le SA con le virgolette doppie, in quanto potrebbero contiene caratteri speciali della riga di comando.

Se la risorsa di destinazione è un contenitore di blob, condivisione file o una tabella, è possibile specificare questa opzione seguita dal token di SA oppure è possibile specificare le SA come parte del contenitore di blob di destinazione, condivisione file o URI della tabella, senza questa opzione.

Se l'origine e destinazione sono entrambi BLOB, il blob di destinazione deve trovarsi nello stesso account di archiviazione blob origine.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="sourcekeystorage-key"></a>/ SourceKey: "chiave di archiviazione"

Specifica la chiave account lo spazio di archiviazione per la risorsa di origine.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="sourcesassas-token"></a>/ SourceSAS: "token di SA"

Specifica una firma di Access condiviso con autorizzazioni di lettura e l'elenco per l'origine (se applicabile). Racchiudere le SA con le virgolette doppie, in quanto potrebbero contiene caratteri speciali della riga di comando.

Se la risorsa di origine è un contenitore di blob e viene specificato un tasto né un SA, quindi il contenitore blob leggerà tramite accesso anonimo.

Se l'origine è una condivisione file o una tabella, è necessario specificare una chiave o una SA.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="s"></a>/S

Specifica la modalità ricorsiva per le operazioni di copia. In modalità ricorsiva, AzCopy verrà copiato tutti i file che corrispondono al modello di file specificato, compresi quelli in sottocartelle o BLOB.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="blobtypeblock--page--append"></a>/ BlobType: "blocchi" | "pagina" | "aggiungere"

Specifica se il blob di destinazione è un blob di blocco, un blob di pagine o blob Accodamento. Questa opzione è applicabile solo quando si carica un blob. In caso contrario, viene generato un errore. Se la destinazione è un blob e questa opzione non viene specificata, per impostazione predefinita, AzCopy crea un blob di blocco.

**Applicabili a:** BLOB

### <a name="checkmd5"></a>/ CheckMD5

Calcola un hash MD5 per dati scaricati e verifica che il MD5 memorizzati nel blob o la proprietà del file Content MD5 corrisponde all'hash calcolato. Controllo MD5 è disattivato per impostazione predefinita, è necessario specificare questa opzione per eseguire il controllo MD5 durante il download dei dati.

Si noti che lo spazio di archiviazione di Azure non garantisce che il MD5 archiviato per il blob o il file sia aggiornato. È responsabilità del cliente per aggiornare il MD5 ogni volta che viene modificato il blob o il file.

AzCopy imposta sempre la proprietà MD5 contenuto per un archivio blob Azure o il file dopo il caricamento al servizio.  

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="snapshot"></a>/ Snapshot

Indica se per il trasferimento snapshot. Questa opzione è valida solo quando l'origine è un blob.

Snapshot blob trasferiti vengono rinominati in questo formato: .extension nome blob (snapshot orario)

Per impostazione predefinita, snapshot non vengono copiati.

**Aggiuntivi applicabili ad:** BLOB

### <a name="vverbose-log-file"></a>/ V: [dettagliato-file di log]

Messaggi di stato dettagliati output in un file di log.

Per impostazione predefinita, il file di registro dettagliato è denominato AzCopyVerbose.log in `%LocalAppData%\Microsoft\Azure\AzCopy`. Se si specifica un percorso del file esistente per questa opzione, il registro dettagliato verrà aggiunto al file.  

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="zjournal-file-folder"></a>/ Z: [cartella di file del diario]

Specifica una cartella di file del diario per riprendere a un'operazione.

AzCopy supporta sempre ripresa se un'operazione è stata interrotta.

Se questa opzione non è specificata o viene specificato senza il percorso della cartella, AzCopy verrà creato il file del diario nel percorso predefinito, ovvero % LocalAppData%\Microsoft\Azure\AzCopy.

Ogni volta che si esegue un comando per AzCopy, viene verificato se un file journal presente nella cartella predefinita o se esiste in una cartella specificata tramite questa opzione. Se il file del diario non esiste in entrambe le posizioni, AzCopy considera l'operazione nuova e genera un nuovo file di diario.

Se il file del diario esiste, AzCopy controllerà se la riga di comando che input corrisponde a riga di comando nel file del diario. Se corrispondono a due righe di comando, AzCopy riprende l'incompleto. Se non corrispondono, verrà richiesto di essere sovrascrivere il file del diario per avviare una nuova operazione o annullare l'operazione corrente.

Il file del diario viene eliminato dopo il completamento dell'operazione.

Si noti che ripresa un'operazione da un file di diario creato da una versione precedente di AzCopy non è supportata.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="parameter-file"></a>/@:"parameter-file"

Specifica un file che contiene i parametri. AzCopy elabora i parametri del file come se fosse stato specificati nella riga di comando.

In un file di risposta, è possibile specificare più parametri in una singola riga o specificare ogni parametro su una riga. Tenere presente che un singolo parametro non è possibile occupare più righe.

File di risposta possono includere le righe di commenti che iniziano con il simbolo #.

È possibile specificare più file di risposta. Tuttavia, si noti che AzCopy non supporta i file di risposta annidati.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="y"></a>/Y

Elimina tutte le richieste di conferma AzCopy.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="l"></a>/L

Specifica l'operazione su una voce di sola lettura. dati non viene copiati.

AzCopy verrà interpretato l'uso di questa opzione come simulazione per l'esecuzione di riga di comando senza questa opzione /L e contare il numero di oggetti verrà copiato, è possibile specificare opzione /V nello stesso momento per verificare quali oggetti verranno copiate nel registro dettagliato.

Il comportamento di questa opzione anche dipende dalla posizione dei dati di origine e la presenza di ricorsiva opzione /S e file del modello opzione modalità /Pattern.

AzCopy richiede l'autorizzazione di elenco e lettura di questo percorso di origine quando si utilizza questa opzione.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="mt"></a>/MT

Imposta ora ultima modifica del file scaricato in modo che corrisponda come blob di origine o del file.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="xn"></a>/XO

Esclude una risorsa di origine più recente. La risorsa non verranno copiata se l'ora dell'ultima modifica dell'origine è uguale o più recente di destinazione.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="xo"></a>/XO

Esclude una risorsa di origine precedenti. La risorsa non verranno copiata se l'ora dell'ultima modifica dell'origine è uguale o meno di destinazione.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="a"></a>/A

Carica solo i file che sono impostato l'attributo archivio.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Carica solo i file che dispongono del set di attributi specificati.

Attributi disponibili sono:

- R = file di sola lettura
- Un = file pronti per l'archiviazione
- S = file di sistema
- H = file nascosti
- C = file compressa
- N = file normale
- E = file crittografati
- T = file temporanei
- O = file non in linea
- I = file Non indicizzati

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="xarashcnetoi"></a>/XA: [RASHCNETOI]

Esclude i file che dispongono del set di attributi specificati.

Attributi disponibili sono:

- R = file di sola lettura
- Un = file pronti per l'archiviazione
- S = file di sistema
- H = file nascosti
- C = file compressi
- N = file normale
- E = file crittografati
- T = file temporanei
- O = file non in linea
- I = file Non indicizzati

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="delimiterdelimiter"></a>/ Delimitatore: "delimitatore"

Indica il carattere delimitatore utilizzato per delimitare virtuali in un nome blob.

Per impostazione predefinita, viene utilizzato AzCopy / come carattere delimitatore. Tuttavia, AzCopy supporta l'utilizzo di qualsiasi carattere comuni (ad esempio @, # o %) come delimitatore. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file con le virgolette doppie.

Questa opzione è disponibile solo per il download BLOB.

**Aggiuntivi applicabili ad:** BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "numero-of-simultanee-operations"

Specifica il numero di operazioni simultanee.

AzCopy per impostazione predefinita verrà avviata un determinato numero di operazioni simultanee per aumentare la velocità di trasferimento di dati. Si noti che un numero elevato di operazioni simultanee in un ambiente di larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete ed evitare che le operazioni da completamente il completamento. Limitare operazioni simultanee in base a larghezza di banda di rete disponibili effettivo.

Il limite superiore per le operazioni simultanee è 512.

**Aggiuntivi applicabili ad:** Tabelle BLOB, file

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabella"

Specifica che il `source` risorsa è disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di spazio di archiviazione blob.

**Aggiuntivi applicabili ad:** BLOB, tabelle

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabella"

Specifica che il `destination` risorsa è disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di spazio di archiviazione blob.

**Aggiuntivi applicabili ad:** BLOB, tabelle

### <a name="pkrskey1key2key3"></a>/ PKRS: "chiave1 chiave2 # #key3 #..."

Divide l'intervallo di chiave partizione per consentire l'esportazione di dati di una tabella in parallelo, il che aumenta la velocità dell'operazione di esportazione.

Se questa opzione non viene specificata, AzCopy utilizzato un singolo thread per esportare entità della tabella. Ad esempio, se l'utente specifica /PKRS: "aa #bb", quindi AzCopy inizia tre operazioni simultanee.

Ogni operazione Esporta uno dei tre intervalli di chiavi di partizione, come illustrato di seguito:

  [chiave di partizione prima, aa)

  [aa, bb)

  [bb, chiave di partizione Cognome]

**Aggiuntivi applicabili ad:** Tabelle

### <a name="splitsizefile-size"></a>/ SplitSize: "dimensioni del file"

Specifica il file esportato dividere dimensione in MB, il valore minimo consentito è 32.

Se questa opzione non viene specificata, AzCopy verranno esportati i dati della tabella in file unico.

Se i dati della tabella viene esportati in un blob e le dimensioni del file esportato raggiungono il limite di 200 GB per dimensioni blob, AzCopy verranno suddivisi il file esportato, anche se questa opzione non è specificata.

**Aggiuntivi applicabili ad:** Tabelle

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Specifica il comportamento di importazione di dati di tabella.

- InsertOrSkip - Ignora un'entità esistente o inserire una nuova entità se non esiste nella tabella.

- InsertOrMerge - unisce un'entità esistente o inserire una nuova entità se non esiste nella tabella.

- InsertOrReplace - sostituisce un'entità esistente o inserire una nuova entità se non esiste nella tabella.

**Aggiuntivi applicabili ad:** Tabelle

### <a name="manifestmanifest-file"></a>/ Manifesto: "manifesto file"

Specifica il file manifesto per la tabella esportazione e importazione operazione.

Questa opzione è facoltativa durante l'operazione di esportazione, AzCopy verrà generato un file manifesto con il nome predefinito se questa opzione non è specificata.

Questa opzione è necessaria durante l'operazione di importazione per individuare i file di dati.

**Aggiuntivi applicabili ad:** Tabelle

### <a name="synccopy"></a>/ SyncCopy

Indica se si desidera copiare in modo sincrono BLOB o i file tra due punti finali di archiviazione Azure.

AzCopy per impostazione predefinita Usa copia asincrona sul lato server. Specificare questa opzione per eseguire una copia icona del, che consente di scaricare file o BLOB memoria locale e quindi li carica allo spazio di archiviazione Azure.

È possibile usare questa opzione quando si copiano file in archiviazione Blob, all'interno di spazio di archiviazione File o dallo spazio di archiviazione Blob allo spazio di archiviazione File o viceversa.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "tipo di contenuto"

Specifica il tipo di contenuto MIME per file o BLOB di destinazione.

AzCopy imposta il tipo di contenuto per un file o blob su application/octet-stream per impostazione predefinita. È possibile impostare il tipo di contenuto per tutti i BLOB o file, in modo esplicito specificando un valore per questa opzione.

Se si specifica questa opzione senza un valore, AzCopy verrà impostato ogni blob o tipo di contenuto del file in base all'estensione del file.

**Aggiuntivi applicabili ad:** BLOB, i file

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Specifica il formato del file di dati esportati tabella.

Se questa opzione non viene specificata, per impostazione predefinita AzCopy Esporta file di dati di tabella in formato JSON.

**Aggiuntivi applicabili ad:** Tabelle

## <a name="known-issues-and-best-practices"></a>Problemi noti e le procedure consigliate

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite di scrittura simultanee durante la copia di dati

Quando si copiano BLOB o i file con AzCopy, tenere presente che un'altra applicazione può modificare i dati mentre si sta copiando. Se possibile, assicurarsi che i dati che si sta copiando non viene modificati durante la copia. Ad esempio, quando si copia un disco rigido virtuale associato a una macchina virtuale Azure, assicurarsi che nessun altre applicazioni sono attualmente la scrittura nel disco rigido virtuale. Un buon modo per eseguire questa operazione è leasing la risorsa da copiare. In alternativa, è possibile creare uno snapshot del file prima di tutto e quindi copiare lo snapshot.

Se non è possibile impedire la scrittura di BLOB o file mentre vengono copiate di altre applicazioni, quindi tenere presente che una volta completato il processo, le risorse copiate non abbia uniformità completa con le risorse di origine.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Eseguire un'istanza di AzCopy in un computer.
AzCopy è progettato per ottimizzare l'utilizzo della risorsa del computer per accelerare il trasferimento dei dati, è consigliabile eseguire solo un'istanza di AzCopy in un computer e specificare l'opzione `/NC` se è necessario simultanea di più operazioni. Per ulteriori informazioni, digitare `AzCopy /?:NC` nella riga di comando.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Abilitare algoritmi MD5 FIPS compatibili per AzCopy quando si "utilizzo FIPS algoritmi compatibili per crittografia, hashing e l'accesso".
AzCopy per impostazione predefinita utilizza implementazione MD5 .NET per calcolare il MD5 la copia di oggetti, ma sono disponibili alcuni requisiti di sicurezza che devono AzCopy per attivare l'impostazione MD5 compatibile FIPS.

È possibile creare un file app `AzCopy.exe.config` con proprietà `AzureStorageUseV1MD5` e pubblicarla riposo con AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Per • proprietà "AzureStorageUseV1MD5" True - il valore predefinito, AzCopy utilizzerà .NET MD5 implementazione.
• False – AzCopy verranno utilizzate algoritmo MD5 conforme FIPS.

Si noti che algoritmi FIPS compatibili è disattivata per impostazione predefinita nei computer Windows, è possibile digitare secpol. msc nella finestra Esegui e selezionare l'opzione impostazioni di sicurezza -> Criteri locali -> sicurezza Opzioni -> crittografia di sistema: algoritmi usare FIPS compatibili per crittografia, l'hashing e l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'archiviazione Azure e AzCopy, consultare le risorse seguenti.

### <a name="azure-storage-documentation"></a>Documentazione di spazio di archiviazione Azure:

- [Introduzione all'archiviazione Azure](storage-introduction.md)
- [Come usare archiviazione Blob da .NET](storage-dotnet-how-to-use-blobs.md)
- [Come utilizzare lo spazio di archiviazione di File da .NET](storage-dotnet-how-to-use-files.md)
- [Come utilizzare lo spazio di archiviazione tabella da .NET](storage-dotnet-how-to-use-tables.md)
- [Come creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Post di blog di Azure lo spazio di archiviazione:
- [Introduzione di archiviazione Azure dati movimento raccolta Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Introduzione a icona del copia e tipo di contenuto personalizzato](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Annuncio disponibilità generale di AzCopy 3.0 plus Preview di AzCopy 4.0 con il supporto di File e delle tabelle](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Ottimizzato per gli scenari di copia su larga scala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Supporto per lo spazio di archiviazione ridondanti geografico di accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Trasferire i dati con la modalità nuovamente avviabile e SA token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Utilizzo Blob copia tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Durante il caricamento o download di file per i BLOB Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
