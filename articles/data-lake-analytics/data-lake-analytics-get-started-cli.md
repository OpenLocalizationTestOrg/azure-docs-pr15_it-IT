<properties 
   pageTitle="Guida introduttiva di Azure dati Lake Analitica tramite l'interfaccia della riga di comando Azure | Microsoft Azure" 
   description="Informazioni su come utilizzare l'interfaccia della riga di comando di Azure per creare un account di archivio Lake dati, creare un processo dati Lake Analitica utilizzando U SQL e inviare il processo. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Esercitazione: Introduzione a Azure dati Lake Analitica utilizzando Azure interfaccia riga di comando (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come usare Azure CLI per creare gli account Azure dati Lake Analitica, definire processi dati Lake Analitica in [U SQL](data-lake-analytics-u-sql-get-started.md)e inviare i processi ad account dati Lake Analitica. Per ulteriori informazioni sui dati Lake Analitica, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).

In questa esercitazione verrà sviluppato un processo che legge i file con valori (TSV) separati da una scheda e lo converte in un file CSV con valori separati da virgola. Per passare all'interno dell'esercitazione stessa usare altri strumenti supportati, selezionare le schede nella parte superiore di questa sezione.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Azure CLI**. Vedere [installare e configurare Azure CLI](../xplat-cli-install.md).
    - Scaricare e installare la **versione non definitiva** [Azure CLI strumenti](https://github.com/MicrosoftBigData/AzureDataLake/releases) per poter completare questa demo.
- **L'autenticazione**, utilizzando il comando seguente:

        azure login
    Per ulteriori informazioni sull'autenticazione tramite un account aziendale o dell'istituto di istruzione, vedere [connettersi a un abbonamento Azure da CLI Azure](../xplat-cli-connect.md).
- **Passare alla modalità di gestione risorse di Azure**, utilizzando il comando seguente:

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Creare account Analitica Lake dati

Devono avere un account Analitica Lake dati prima di poter eseguire tutti i processi. Per creare un account di dati Lake Analitica, è necessario specificare le operazioni seguenti:

- **Gruppo di risorse Azure**: account A dati Lake Analitica deve essere creato all'interno di un gruppo di risorse di Azure. [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md) consente di gestire le risorse dell'applicazione come un gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.  

    Enumerare i gruppi di risorse nell'abbonamento:
    
        azure group list 
    
    Per creare un nuovo gruppo di risorse:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome dell'account Analitica Lake dati**
- **Posizione**: una delle aree di dati di Azure che supporta dati Lake Analitica.
- **Account Lake dati predefinito**: ogni account dati Lake Analitica ha un account di Lake dati predefinito.

    Per visualizzare un elenco di account Lake dati esistente:
    
        azure datalake store account list

    Per creare un nuovo account Lake dati:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] Il nome dell'account Lake dati deve contenere solo numeri e lettere minuscole.



**Per creare un account di dati Lake Analitica**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Dati Lake Analitica mostra account](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Il nome dell'account dati Lake Analitica deve contenere solo numeri e lettere minuscole.


## <a name="upload-data-to-data-lake-store"></a>Caricare dati all'archivio dati Lake

In questa esercitazione si elaborerà alcuni log di ricerca.  Il log di ricerca può essere archiviato in archivio dati Lake o archiviazione Blob Azure. 

Il portale di Azure offre un'interfaccia utente per la copia di alcuni file di dati di esempio per l'account di Lake dati predefinito, che includono un file di log di ricerca. Vedere [preparare dati di origine](data-lake-analytics-get-started-portal.md#prepare-source-data) per caricare i dati per l'account di archivio Lake dati predefinito.

Per caricare i file usando cli, usare il comando seguente:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Dati Lake Analitica accessibile anche archiviazione Blob Azure.  Per caricare i dati a archiviazione Blob Azure, vedere [utilizzo CLI Azure con lo spazio di archiviazione di Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Inviare dati Lake Analitica processi

I processi di dati Lake Analitica scritte in linguaggio U-SQL. Per ulteriori informazioni su U-SQL, vedere [Guida introduttiva a linguaggio SQL U](data-lake-analytics-u-sql-get-started.md) e [riferimento al linguaggio SQL U](http://go.microsoft.com/fwlink/?LinkId=691348).

**Per creare uno script di processo dati Lake Analitica**

- Creare un file di testo con script U SQL seguente e salvare il file di testo workstation in uso:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Questo script SQL U legge il file di dati di origine utilizzando **Extractors.Tsv()**e quindi viene creato un file csv tramite **Outputters.Csv()**. 
    
    Non modificare due percorsi, a meno che non si copia il file di origine in una posizione diversa.  Dati Lake Analitica creerà la cartella di output se non esiste.
    
    È semplice utilizzare percorsi relativi per i file memorizzati predefiniti dati account Lake. È anche possibile utilizzare percorsi assoluti.  Per esempio 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario utilizzare percorsi assoluti per accedere ai file nell'account di archiviazione collegati.  La sintassi per i file memorizzati in account di archiviazione Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenitore di Blob Azure con BLOB pubblico o le autorizzazioni di accesso contenitori pubblico non sono attualmente supportati.      

    
**Per inviare il processo**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Per elencare processi, ottenere i dettagli dei processi e annullare processi, è possono utilizzare i comandi seguenti:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Una volta completato il processo, è possibile utilizzare i cmdlet seguenti per visualizzare un elenco di file e scaricare il file:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Vedere anche

- Per visualizzare l'esercitazione stessa usare altri strumenti, fare clic su selettori scheda nella parte superiore della pagina.
- Per una query più complessa, vedere [registri di sito Web analizza mediante Azure dati Lake Analitica](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a applicazioni U SQL, vedere [gli script di sviluppare U-SQL utilizzando dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md).
- Per le attività di gestione, vedere [Gestione dei Analitica delle Lake Azure dati nel portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per ottenere una panoramica dei dati Lake Analitica, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).

