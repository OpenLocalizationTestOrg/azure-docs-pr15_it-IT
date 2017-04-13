<properties
   pageTitle="Guida introduttiva a archivio dati Lake | Azure"
   description="Usare PowerShell Azure per creare un account di archivio di dati Lake ed eseguire operazioni di base"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Guida introduttiva di Azure dati Lake archivio tramite PowerShell Azure

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Informazioni su come usare PowerShell Azure per creare un account Azure dati Lake Store ed eseguire le operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni sui dati Lake Store, vedere [Panoramica dei dati Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

* **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 o versione successiva**. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Autenticazione

Questo articolo viene utilizzato un approccio più semplice l'autenticazione con archivio Lake dati in cui viene chiesto di immettere le credenziali dell'account Azure. Il livello di accesso a Lake archivio account e il file system quindi dalle livello di accesso dell'utente in impostazioni. Tuttavia, esistono altri approcci anche per eseguire l'autenticazione con archivio Lake dati sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account Azure dati Lake archivio

1. Dal desktop, aprire una nuova finestra di Windows PowerShell e immettere il frammento di codice seguente per accedere al proprio account Azure, impostare la sottoscrizione e registrare il provider di dati Lake Store. Quando viene richiesto di effettuare l'accesso, assicurarsi che l'accesso un tipo di abbonamento admininistrators/proprietario:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Un account Azure dati Lake Store è associato a un gruppo di risorse Azure. Avviare la creazione di un gruppo di risorse Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creare un gruppo di risorse Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Creare un gruppo di risorse Azure")

2. Creare un account Azure dati Lake Store. Il nome specificato deve contenere solo numeri e lettere minuscole.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creare un account Azure dati Lake archivio] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creare un account Azure dati Lake archivio")

3. Verificare che l'account è stato creato correttamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    L'output per questa operazione deve essere **vera**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Creare strutture di directory nell'archivio Lake dati Azure

È possibile creare directory con il proprio account Azure dati Lake Store per gestire e archiviare i dati.

1. Specificare una directory radice.

        $myrootdir = "/"

2. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Verificare che la nuova directory è stata creata correttamente.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Viene un risultato simile al seguente:

    ![Verificare Directory] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificare Directory")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Caricare dati all'archivio Lake dati Azure

È possibile caricare i dati all'archivio di dati Lake direttamente al livello radice o in una directory che è stato creato all'interno dell'account. I frammenti di codice seguente viene illustrato come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Rinominare, scaricare ed eliminare dati dall'archivio dati Lake

Per rinominare un file, usare il comando seguente:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Per scaricare un file, usare il comando seguente:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Per eliminare un file, usare il comando seguente:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando richiesto, immettere **Y** per eliminare l'elemento. Se si dispone di più di un file da eliminare, è possibile specificare tutti i percorsi separati da virgola.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminare il proprio account Azure dati Lake archivio

Utilizzare il comando seguente per eliminare l'account di archivio di dati Lake.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando richiesto, immettere **Y** per eliminare l'account.


## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
