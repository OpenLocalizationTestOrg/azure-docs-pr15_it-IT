<properties
    pageTitle="Spostare i dati in o da archiviazione Blob Azure con connettori SSIS | Microsoft Azure"
    description="Spostare i dati in o da archiviazione Blob Azure con connettori SSIS."
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

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Spostare i dati in o da archiviazione Blob Azure con connettori SSIS

[SQL Server Integration Services Feature Pack per Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornisce componenti a cui connettersi Azure, dati di trasferimento tra Azure e origini dati locali e dati di processo archiviati in Azure.

Indicazioni sulle tecnologie utilizzati per spostare i dati a e/o dallo spazio di archiviazione Blob Azure sono collegati qui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Una volta clienti sono state spostate dati locali nel cloud, possibilità di accedervi da qualsiasi servizio Azure per sfruttare al meglio del gruppo di tecnologie Azure. Può essere impiegato, ad esempio, in apprendimento Azure o in un cluster di HDInsight.

Si tratta in genere essere il primo passaggio per le procedure dettagliate [SQL](machine-learning-data-science-process-sql-walkthrough.md) e [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) .

Per informazioni sulle canonici scenari che utilizzano SSIS per ottenere le esigenze aziendali comuni in scenari di integrazione ibrido ai dati, vedere [eseguire ulteriori con SQL Server Integration Services Feature Pack per Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [AZURE.NOTE] Per un'introduzione completa a archiviazione blob Azure, fare riferimento alle [Attività di base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) e al [Servizio Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le attività descritte in questo articolo, è necessario disporre di un abbonamento a Azure e configurare un account di archiviazione Azure. È necessario conoscere l'archiviazione Azure account e nome chiave account per il caricamento o il download dei dati.

- Per configurare un **abbonamento Azure**, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- Per istruzioni sulla creazione di un **account di archiviazione** e per ottenere informazioni chiave e account, vedere [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).


Per utilizzare i **connettori SSIS**, è necessario scaricare:

- **SQL Server 2014 o 2016 Standard (o versione successiva)**: installa include SQL Server Integration Services.
- **Microsoft SQL Server 2014 o 2016 Integration Services Feature Pack per Azure**: questi possono essere scaricati, rispettivamente, dalle pagine di [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [AZURE.NOTE] SSIS viene installato con SQL Server, ma non è incluso nella versione Express. Per informazioni su quali applicazioni sono incluse nelle varie edizioni di SQL Server, vedere [Edizioni di SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

Per materiale per la formazione su SSIS, vedere [Mani in risorse di formazione per SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Per informazioni su come ottenere accesso in esecuzione con SISS per creare semplici estrazione, trasformazione e pacchetti di caricamento (ETL), vedere [SSIS esercitazione: creare un pacchetto ETL semplice](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Scaricare Roma Taxi set di dati  
Nell'esempio descritto qui utilizzato un set di dati disponibile al pubblico, ossia il set di dati di [Roma Taxi viaggi](http://www.andresmh.com/nyctaxitrips/) . Il set di dati è costituito da si basa taxi 173 milioni di Roma nell'anno 2013. Esistono due tipi di dati: dati e tariffa i dati dei dettagli di viaggio. Come c'è un file per ogni mese, è necessario 24 file in tutti, ognuno dei quali è di circa 2GB non compressi.


## <a name="upload-data-to-azure-blob-storage"></a>Caricare dati in archiviazione blob Azure
Per spostare feature pack locali a archiviazione blob Azure di dati tramite il SSIS, viene utilizzata un'istanza dell' [**Attività di caricare Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), illustrato di seguito:

![configurare-dati-scienza-macchine virtuali](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


I parametri che utilizza l'attività sono descritti di seguito:


Campo|Descrizione|
----------------------|----------------|
**AzureStorageConnection**|Specifica un gestore di connessione di spazio di archiviazione Azure esistente o crea una nuova che fa riferimento a un account di archiviazione Azure in cui sono ospitati i file di archivio blob di puntamento.|
**BlobContainer**|Specifica il nome del contenitore di blob contenenti i file caricati come BLOB.|
**BlobDirectory**|Specifica la directory blob in cui è archiviato il file caricato come blob blocco. La directory blob è una struttura gerarchica virtuale. Se il blob esiste già, ia sostituito.|
**LocalDirectory**|Specifica la directory locale contenente i file da caricare.|
**FileName**|Specifica un nome di filtro per selezionare i file con il criterio specificato. Ad esempio, esempio alla\*. xls\* include tutti i file, ad esempio MySheet001.xls e MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Specifica un filtro di intervallo di tempo. I file modificati dopo *TimeRangeFrom* e prima di *TimeRangeTo* sono inclusi.|


> [AZURE.NOTE] Le credenziali **AzureStorageConnection** devono essere corrette e **BlobContainer** , occorre prima si tenta di eseguire il trasferimento.

## <a name="download-data-from-azure-blob-storage"></a>Scaricare dati da archiviazione blob Azure

Per scaricare i dati da archiviazione blob Azure lo spazio di archiviazione con SSIS locale, utilizzare un'istanza dell' [Attività di caricare Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##<a name="more-advanced-ssis-azure-scenarios"></a>Scenari di Azure SSIS più avanzati
Qui è possibile notare che consenta di SSIS feature pack per i flussi di più complessi da gestire con attività confezione di un insieme. Ad esempio, i dati blob Impossibile feed direttamente in un cluster di HDInsight, il cui output potrà essere scaricato al blob e quindi in un archivio locale. SSIS eseguire processi Hive e maialino in un cluster di HDInsight uso dei connettori SSIS aggiuntivi:

- Per eseguire uno script Hive in un cluster di Azure HDInsight con SSIS, utilizzare [Azure HDInsight Hive attività](https://msdn.microsoft.com/library/mt146771.aspx).
- Per eseguire uno script maialino in un cluster di Azure HDInsight con SSIS, utilizzare [Azure HDInsight le attività](https://msdn.microsoft.com/library/mt146781.aspx).
