<properties
   pageTitle="Guida introduttiva a archivio Lake dati mediante l'interfaccia di riga di comando multipiattaforma | Microsoft Azure"
   description="Riga di comando multipiattaforma e su Azure consente di creare un account di archivio di dati Lake ed eseguire operazioni di base"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Guida introduttiva di Azure dati Lake archivio tramite riga di comando di Azure

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Informazioni su come usare Azure interfaccia della riga di comando per creare un account Azure dati Lake Store ed eseguire operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni sui dati Lake Store, vedere [Panoramica dei dati Lake Store](data-lake-store-overview.md).

CLI Azure è implementata in Node. Può essere utilizzato in qualsiasi piattaforma che supporta Node, ad esempio Windows, Mac e Linux. CLI Azure è Apri origine. Il codice sorgente viene gestito in GitHub in <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. In questo articolo viene illustrato l'utilizzo solo CLI Azure con dati Lake archivio. Per una Guida generale sull'uso di Azure CLI, vedere [come utilizzare CLI Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI** - vedere [installare e configurare CLI Azure](../xplat-cli-install.md) per informazioni sull'installazione e configurazione. Verificare che il riavvio del computer dopo l'installazione di CLI.

## <a name="authentication"></a>Autenticazione

Questo articolo viene utilizzato un approccio più semplice l'autenticazione con archivio Lake dati in cui l'accesso come utente finale. Il livello di accesso a Lake archivio account e il file system quindi dalle livello di accesso dell'utente in impostazioni. Tuttavia, esistono altri approcci anche per eseguire l'autenticazione con archivio Lake dati sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Accesso al proprio abbonamento Azure

1. Seguire la procedura descritta in [connessione a un abbonamento Azure dall'interfaccia della riga di comando Azure Azure CLI ()](../xplat-cli-connect.md) e connettersi al abbonamento mediante la `azure login` metodo.

2. Elencare le sottoscrizioni sono associate al proprio account utilizzando il `azure account list` comando.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    Dall'output, **Azure-sub-1** è attiva e altri abbonamenti sono **Azure-sub-2**. 

3. Selezionare l'abbonamento che si desidera utilizzare in. Se si desidera utilizzare in abbonamento Azure-sub-2, utilizzare la `azure account set` comando.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Creare un account Azure dati Lake archivio

Aprire un prompt dei comandi, shell o una sessione terminal ed eseguire i comandi seguenti.

2. Passare alla modalità di gestione di risorse Azure utilizzando il comando seguente:

        azure config mode arm


5. Creare un nuovo gruppo di risorse. Il comando seguente, fornire i valori di parametro che si desidera utilizzare.

        azure group create <resourceGroup> <location>

    Se il nome del percorso contiene spazi, inserirlo tra virgolette. Ad esempio "orientale US 2".

5. Creare l'account di archivio di dati Lake.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Creare cartelle nell'archivio dati Lake

È possibile creare cartelle con il proprio account Azure dati Lake Store per gestire e archiviare i dati. Utilizzare il comando seguente per creare una cartella denominata "mynewfolder" nella radice dell'archivio Lake dati.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Per esempio:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Caricare dati all'archivio dati Lake

È possibile caricare i dati all'archivio di dati Lake direttamente al livello radice o in una cartella che è stato creato all'interno dell'account. I frammenti di codice seguente viene illustrato come caricare alcuni dati di esempio nella cartella (**mynewfolder**) creata nella sezione precedente.

Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Per esempio:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Elencare i file di archivio di dati Lake

Utilizzare il comando seguente per elencare i file in un account di archivio di dati Lake.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Per esempio:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

L'output di questo dovrebbe essere simile al seguente:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Rinominare, scaricare ed eliminare dati dall'archivio dati Lake

* **Per rinominare un file**, usare il comando seguente:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Per esempio:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Per scaricare un file**, usare il comando seguente. Assicurarsi che il percorso di destinazione specificata già esistente.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Per esempio:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Per eliminare un file**, usare il comando seguente:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Per esempio:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Quando richiesto, immettere **Y** per eliminare l'elemento.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Visualizzare l'elenco di controllo di accesso per una cartella nell'archivio dati Lake

Utilizzare il comando seguente per visualizzare gli elenchi di controllo per una cartella archivio Lake dati. Nella versione corrente, è possono impostare ACL solo nella cartella principale dell'archivio Lake dati. Pertanto, il parametro path riportata di seguito viene mantenuto radice (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Per esempio:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminare l'account di archivio di dati Lake

Usare il comando seguente per eliminare un account di archivio di dati Lake.

    azure datalake store account delete <dataLakeStoreAccountName>

Per esempio:

    azure datalake store account delete mynewdatalakestore

Quando richiesto, immettere **Y** per eliminare l'account.


## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
