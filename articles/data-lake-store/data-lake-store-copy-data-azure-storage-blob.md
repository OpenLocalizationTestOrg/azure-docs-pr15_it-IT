<properties
   pageTitle="Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake | Microsoft Azure"
   description="Utilizzare lo strumento AdlCopy per copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake

> [AZURE.SELECTOR]
- [Utilizzo DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilizzo AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Archivio Lake dati di Azure fornisce uno strumento della riga di comando, [AdlCopy](http://aka.ms/downloadadlcopy), copiare i dati dalle origini seguenti:

* Da Azure archiviazione BLOB all'archivio Lake dati. È possibile usare AdlCopy per copiare i dati dall'archivio dati Lake Azure archiviazione BLOB.

* Tra due account Azure dati Lake Store. 

Inoltre, è possibile utilizzare lo strumento AdlCopy in due modi diversi:

* **Autonoma**, nel punto in cui lo strumento utilizza le risorse di archivio dati Lake per eseguire l'operazione.

* **Con un account di dati Lake Analitica**, in cui le unità assegnate al proprio account Analitica Lake dati vengono usate per eseguire l'operazione di copia. È consigliabile usare questa opzione quando si cerca per eseguire le operazioni di copia in modo prevedibile.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Contenitore di **Spazio di archiviazione BLOB** con alcuni dati.

- **Account azure dati Lake Analitica (facoltativo)** -, vedere [Guida introduttiva di Azure dati Lake Analitica](../data-lake-analytics/data-lake-analytics-get-started-portal.md) per istruzioni su come creare un account di archivio di dati Lake.

- **Strumento AdlCopy**. Installare lo strumento AdlCopy da [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintassi dello strumento AdlCopy

Utilizzare la sintassi seguente per lavorare con lo strumento AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Parametri nella sintassi sono descritti di seguito:

| Opzione    | Descrizione                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Origine    | Specifica la posizione dei dati di origine nel blob Azure dello spazio di archiviazione. L'origine può essere un contenitore di blob, un blob o un altro account di archivio di dati Lake.                                                                                                                                                                                                                                                                                                 |
| Destinazione      | Specifica la destinazione archivio Lake dati da copiare.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Specifica il tasto di scelta di spazio di archiviazione per l'origine di blob Azure dello spazio di archiviazione. Questa operazione è necessaria solo se l'origine è un contenitore di blob o un blob.                                                                                                                                                                                                                                                                                                                                                 |
| Account   | **Facoltativo**. Utilizzare questa opzione se si desidera utilizzare account Azure dati Lake Analitica per eseguire il processo di copia. Se si utilizza l'opzione /Account nella sintassi ma non si specifica un account di dati Lake Analitica, AdlCopy utilizza un account predefinito per eseguire il processo. Inoltre, se si utilizza questa opzione, è necessario aggiungere (Azure lo spazio di archiviazione Blob) di origine e destinazione (Azure dati Lake Store) come origini dati per il proprio account dati Lake Analitica.  |
| Unità di misura     |  Specifica il numero di unità di dati Lake Analitica che verrà utilizzato per il processo di copia. Questa opzione è obbligatoria se si usa l'opzione **/Account** per specificare l'account di dati Lake Analitica.
| Motivo   | Specifica un motivo regex che indica quali BLOB o file da copiare. AdlCopy utilizza corrispondenza tra maiuscole e minuscole. Il modello predefinito utilizzato quando non viene specificato alcun criterio consiste nel copiare tutti gli elementi. Non è possibile specificare più modelli di file.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilizzare AdlCopy (come autonomo) per copiare i dati da un archivio blob Azure lo spazio di archiviazione

1. Aprire un prompt dei comandi e passare alla cartella in cui AdlCopy è installato, in genere `%HOMEPATH%\Documents\adlcopy`.

2. Eseguire il seguente comando per copiare un blob specifico il contenitore di origine in un archivio di Lake dati:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Per esempio:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] La sintassi sopra riportata specifica il file da copiare in una cartella nella finestra account archivio Lake dati. Strumento AdlCopy crea una cartella se il nome della cartella non esiste.

    Verrà richiesto di immettere le credenziali per l'abbonamento Azure in cui si dispone di account archivio Lake dati. Verrà visualizzato un output simile al seguente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. È anche possibile copiare tutti i BLOB da un contenitore per l'account di archivio Lake dati utilizzando il comando seguente:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Per esempio:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Utilizzare AdlCopy (come autonomo) per copiare i dati da un altro account di archivio di dati Lake

È anche possibile utilizzare AdlCopy per copiare i dati tra due account archivio Lake dati.

1. Aprire un prompt dei comandi e passare alla cartella in cui AdlCopy è installato, in genere `%HOMEPATH%\Documents\adlcopy`.

2. Eseguire il seguente comando per copiare un file specifico da un account di archivio di dati Lake.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Per esempio:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] La sintassi sopra riportata specifica il file da copiare in una cartella di destinazione account archivio Lake dati. Strumento AdlCopy crea una cartella se il nome della cartella non esiste.

    Verrà richiesto di immettere le credenziali per l'abbonamento Azure in cui si dispone di account archivio Lake dati. Verrà visualizzato un output simile al seguente:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Il comando seguente consente di copiare tutti i file da una cartella specifica dell'account archivio Lake dati di origine in una cartella di destinazione account archivio Lake dati.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilizzare AdlCopy (con account dati Lake Analitica) per copiare i dati

È possibile usare anche l'account di dati Lake Analitica per eseguire il processo di AdlCopy per copiare i dati da BLOB Azure dello spazio di archiviazione dati Lake Store. In genere utilizzare questa opzione quando i dati da spostare nell'intervallo di gigabyte e terabyte e si desidera prestazioni migliori e prevedibili.

Per usare l'account di dati Lake Analitica con AdlCopy per copiare un Blob di spazio di archiviazione di Azure, è necessario aggiungere l'origine (Azure lo spazio di archiviazione Blob) come origine dati per il proprio account dati Lake Analitica. Per istruzioni sull'aggiunta di altre origini dati al proprio account dati Lake Analitica, vedere [gestire dati Lake Analitica account origini dati](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Se si sta copiando da un account Azure dati Lake Store come origine con un account di dati Lake Analitica, non è necessario associare l'account di archivio Lake dati con l'account di dati Lake Analitica. La necessità di associazione di archivio di origine con l'account di dati Lake Analitica è solo quando l'origine è un account di archiviazione Azure.

Eseguire il seguente comando per copiare un blob Azure lo spazio di archiviazione a un account di archivio di dati Lake con dati Lake Analitica account:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Per esempio:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


Analogamente, eseguire il seguente comando per copiare un blob Azure lo spazio di archiviazione a un account di archivio di dati Lake con dati Lake Analitica account:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Utilizzare AdlCopy per copiare i dati utilizzando i criteri di ricerca

In questa sezione, si imparerà a utilizzare AdlCopy per copiare i dati da un'origine (nell'esempio seguente serve Azure lo spazio di archiviazione Blob) a un account di dati Lake archivio di destinazione utilizzando i criteri di ricerca. Ad esempio, è possibile utilizzare la procedura seguente per copiare tutti i file con estensione CSV da blob origine alla destinazione.

1. Aprire un prompt dei comandi e passare alla cartella in cui AdlCopy è installato, in genere `%HOMEPATH%\Documents\adlcopy`.

2. Eseguire il seguente comando per copiare tutti i file con estensione csv un blob specifico dal contenitore di origine in un archivio di Lake dati:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Per esempio:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Fatturazione

* Se si usa lo strumento AdlCopy come autonomo verrà fatturato per i costi di uscita per spostare i dati, se l'account di archiviazione di Azure di origine non è nella stessa regione Lake archivio.

* Se si usa lo strumento AdlCopy con l'account di dati Lake Analitica, verranno applicate [le tariffe di fatturazione dati Lake Analitica](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Considerazioni per l'utilizzo AdlCopy

* AdlCopy (per la versione 1.0.5), supporta la copia di dati da origini con collettivamente più di migliaia di file e cartelle. Tuttavia, se si verificano problemi con la copia di un set di dati di grandi dimensioni, è possibile distribuire il file o cartelle in sottocartelle diverse e utilizzare invece il percorso per tali sottocartelle come origine.

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
