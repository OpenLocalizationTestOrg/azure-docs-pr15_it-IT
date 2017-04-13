<properties
    pageTitle="Utilizzo di PowerShell Azure con lo spazio di archiviazione Azure | Microsoft Azure"
    description="Informazioni su come utilizzare i cmdlet di PowerShell Azure per lo spazio di archiviazione di Azure per creare e gestire gli account di archiviazione; lavorare con BLOB, tabelle, code e file; configurare e query analitica lo spazio di archiviazione e creare firme di accesso condiviso."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Utilizzo di PowerShell Azure con lo spazio di archiviazione Azure

## <a name="overview"></a>Panoramica

PowerShell Azure è un modulo che fornisce i cmdlet per la gestione di Azure tramite Windows PowerShell. È una shell della riga di comando basato su attività e linguaggio di script progettato per amministrazione del sistema. Con PowerShell, è possibile controllare facilmente e automatizzare l'amministrazione dei servizi Azure e le applicazioni. Ad esempio, è possibile utilizzare i cmdlet per eseguire le stesse operazioni che è possibile eseguire tramite il [Portale di Azure](https://portal.azure.com).

In questa Guida verrà illustrato come utilizzare i [Cmdlet di spazio di archiviazione di Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) per eseguire una serie di attività di sviluppo e amministrazione con lo spazio di archiviazione di Azure.

Questa guida presuppone che abbia già esperienza nell'uso [Dello spazio di archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) e [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). La Guida fornisce una serie di script per illustrare l'utilizzo di PowerShell con lo spazio di archiviazione di Azure. È necessario aggiornare le variabili di script in base alla configurazione prima di eseguire ogni script.

La prima sezione in questa guida offre una rapida occhiata archiviazione Azure e PowerShell. Per informazioni dettagliate e istruzioni, iniziare dai [Prerequisiti per l'utilizzo di PowerShell Azure con lo spazio di archiviazione di Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Guida introduttiva di archiviazione Azure e PowerShell in 5 minuti

In questa sezione viene illustrato come accedere all'archiviazione Azure tramite PowerShell 5 minuti.

**i nuovi utenti di Azure:** È possibile ottenere una sottoscrizione di Microsoft Azure e un account Microsoft associato a tale abbonamento. Per informazioni sulle opzioni di acquisto Azure, vedere [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/)e [Offre membro](https://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network e BizSpark e altri programmi Microsoft).

Per ulteriori informazioni sulle sottoscrizioni Azure, vedere [assegnazione di ruoli di amministratore di Azure Active Directory (Azure Active Directory)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Dopo la creazione di un abbonamento a Microsoft Azure e account:**

1.  Scaricare e installare [PowerShell Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Avvia Windows PowerShell scrittura di script ambiente integrato (ISE): Del computer locale, passare al menu **Start** . Digitare **Strumenti di amministrazione** e fare clic per eseguirlo. Nella finestra **Strumenti di amministrazione** rapida **Windows PowerShell ISE**, fare clic su **Esegui come amministratore**.
3.  In **Windows PowerShell ISE**, fare clic su **File** > **Nuovo** per creare un nuovo file di script.
4.  A questo punto, ci sono un semplice script che mostra i comandi di PowerShell di base per accedere all'archiviazione Azure. Lo script chiederà prima di tutto il Azure le credenziali dell'account per aggiungere il Azure account all'ambiente di PowerShell locale. Lo script verrà quindi impostare il valore predefinito abbonamento Azure e creare un nuovo account di archiviazione in Azure. Lo script verrà successivamente, creare un contenitore di nuovo il nuovo account di archiviazione e caricare un file di immagine esistente (blob) in tale contenitore. Dopo lo script Elenca tutti i BLOB in tale contenitore, verrà creare una nuova cartella di destinazione nel computer locale e scaricare il file di immagine.
5.  Nella sezione seguente di codice, selezionare lo script tra le note **#begin** e **#end**. Premere CTRL + C per copiarlo negli Appunti.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  In **Windows PowerShell ISE**, premere CTRL + V per copiare lo script. Fare clic su **File** > **salvare**. Nella finestra di dialogo **Salva con nome** digitare il nome del file di script, ad esempio "mystoragescript". Fare clic su **Salva**.

6.  A questo punto, è necessario aggiornare le variabili di script in base alle impostazioni di configurazione. È necessario aggiornare la variabile **$SubscriptionName** con il proprio abbonamento. È possibile mantenere le altre variabili come specificato nello script o aggiornarli nel modo desiderato.

    - **$SubscriptionName:** Con il proprio nome di sottoscrizione, è necessario aggiornare la variabile. Eseguire una delle seguenti tre modi per individuare il nome del proprio abbonamento:

        un. In **Windows PowerShell ISE**, fare clic su **File** > **Nuovo** per creare un nuovo file di script. Copiare il seguente script per il nuovo file di script e fare clic su **Debug** > **eseguire**. Il seguente script chiederà prima di tutto il Azure le credenziali dell'account per aggiungere il Azure account all'ambiente di PowerShell locale e quindi Mostra tutte le sottoscrizioni collegato alla sessione di PowerShell locale. Prendere nota del nome dell'abbonamento a cui si desidera utilizzare durante la seguendo l'esercitazione:

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Per individuare e copiare il nome dell'abbonamento nel [Portale di Azure](https://portal.azure.com), nel menu Hub a sinistra, fare clic su **abbonamenti**. Copia il nome dell'abbonamento a cui si desidera utilizzare durante l'esecuzione di script in questa Guida.

        ![Portale di Azure][Image2]

        c. Per individuare e copiare il nome dell'abbonamento nel [Portale classica di Azure](https://manage.windowsazure.com/), scorrere verso il basso e fare clic su **Impostazioni** sul lato sinistro del portale. Fare clic su **sottoscrizioni** per visualizzare un elenco delle sottoscrizioni. Copia il nome dell'abbonamento a cui si desidera utilizzare durante l'esecuzione di script specificato in questa Guida.

        ![Portale classica Azure][Image1]

    - **$StorageAccountName:** Usare il nome specificato nello script oppure immettere un nuovo nome per il proprio account di archiviazione. **Importanti:** Il nome dell'account di archiviazione deve essere univoco in Azure. Deve essere minuscola, troppo!

    - **$Location:** Usare specificato "Ovest negli Stati Uniti" nello script o scegliere altri percorsi Azure, ad esempio Stati Uniti orientali, Nord Europa e così via.

    - **$ContainerName:** Usare il nome specificato nello script oppure immettere un nuovo nome per il contenitore.

    - **$ImageToUpload:** Immettere il percorso di un'immagine nel computer locale, ad esempio: "C:\Images\HelloWorld.png".

    - **$DestinationFolder:** Immettere il percorso di una directory locale per archiviare i file scaricati dallo spazio di archiviazione di Azure, ad esempio: "C:\DownloadImages".

7.  Dopo aver aggiornato le variabili di script nel file "mystoragescript.ps1", fare clic su **File** > **salvare**. Quindi fare clic su **Debug** > **eseguire** oppure premere **F5** per eseguire lo script.  

Dopo l'esecuzione di script, è necessario disporre di una cartella di destinazione locale che include il file di immagine scaricata. Schermata seguente è illustrato un esempio dell'output:

![Scaricare BLOB][Image3]


> [AZURE.NOTE] La sezione "Guida introduttiva a archiviazione Azure e PowerShell 5 minuti" fornito una breve introduzione su come usare PowerShell Azure con lo spazio di archiviazione di Azure. Per informazioni dettagliate e istruzioni, è consigliabile leggere le sezioni seguenti.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Prerequisiti per l'utilizzo di PowerShell Azure con lo spazio di archiviazione di Azure
È necessario un abbonamento Azure e account per eseguire i cmdlet di PowerShell indicati in questa Guida, come descritto in precedenza.

PowerShell Azure è un modulo che fornisce i cmdlet per la gestione di Azure tramite Windows PowerShell. Per informazioni sull'installazione e configurazione di Azure PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). È consigliabile scaricare e installare o aggiornare al modulo di Azure PowerShell più recente prima di utilizzare questa Guida.

È possibile eseguire i cmdlet di Windows PowerShell Scripting ambiente integrato (ISE) o standard console di Windows PowerShell. Ad esempio, per aprire **Windows PowerShell ISE**, passare al menu Start, digitare strumenti di amministrazione e fare clic per eseguirlo. Nella finestra Strumenti di amministrazione rapida Windows PowerShell ISE, fare clic su Esegui come amministratore.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Come gestire gli account di archiviazione in Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Come configurare una sottoscrizione Azure predefinite
Per gestire l'archiviazione di Azure tramite PowerShell di Azure, è necessario autenticare l'ambiente di client con Azure tramite l'autenticazione di Azure Active Directory o l'autenticazione basata su certificato. Per informazioni dettagliate, vedere Esercitazione su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Questa guida utilizza l'autenticazione di Azure Active Directory.

1.  In Windows PowerShell ISE, digitare il comando seguente per aggiungere il Azure account all'ambiente di PowerShell locale:

    `Add-AzureAccount`

2.  Nella finestra "Sign in a Microsoft Azure" digitare l'indirizzo di posta elettronica e la password associati all'account. Azure autentica e consente di salvare le informazioni sulle credenziali e quindi si chiude la finestra.

3.  Successivamente, eseguire il comando seguente per visualizzare gli account Azure nel proprio ambiente di PowerShell locale e verificare che l'account sia elencato:

    `Get-AzureAccount`

4.  Eseguire il cmdlet seguente per visualizzare tutte le sottoscrizioni collegato alla sessione di PowerShell locale, quindi verificare che sia presente l'abbonamento:

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Per impostare una sottoscrizione Azure predefinita, eseguire il cmdlet AzureSubscription selezionare:

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Verificare il nome della sottoscrizione predefinito eseguendo il cmdlet Get-AzureSubscription:

    `Get-AzureSubscription -Default`

7.  Per visualizzare tutti i cmdlet di PowerShell disponibili per l'archiviazione di Azure, eseguire:

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Come creare un nuovo account di archiviazione Azure
Per utilizzare lo spazio di archiviazione Azure, è necessario un account di archiviazione. È possibile creare un nuovo account di archiviazione Azure dopo aver configurato il computer per connettersi al proprio abbonamento.

1.  Eseguire il cmdlet Get-AzureLocation per trovare tutti i percorsi di Data Center disponibili:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Successivamente, eseguire il cmdlet New-AzureStorageAccount per creare un nuovo account di archiviazione. Nell'esempio seguente viene creato un nuovo account di archiviazione in Data Center del "Ovest degli Stati Uniti".

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] Il nome del proprio account di archiviazione deve essere univoco all'interno di Azure e deve essere minuscolo. Per le convenzioni di denominazione e limitazioni, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md) e [denominazione e contenitori che fa riferimento, BLOB e i metadati](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Come configurare un account di archiviazione Azure predefinito
È possibile impostare più account di archiviazione nell'abbonamento. È possibile scegliere una di esse e impostarla come l'account di archiviazione predefinito per tutti i comandi di spazio di archiviazione nella stessa sessione di PowerShell. In questo modo è possibile eseguire i comandi di spazio di archiviazione di Azure PowerShell senza specificare il contesto di archiviazione in modo esplicito.

1.  Per impostare un account di archiviazione predefinito per l'abbonamento, è possibile eseguire il cmdlet Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Successivamente, eseguire il cmdlet Get-AzureSubscription per verificare che l'account di archiviazione è associata al tuo account di sottoscrizione predefinito. Questo comando restituisce le proprietà di sottoscrizione dell'abbonamento corrente, tra cui l'account di archiviazione corrente.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Come visualizzare un elenco di tutti gli account Azure dello spazio di archiviazione di un abbonamento
Ogni abbonamento Azure può contenere fino a 100 account di archiviazione. Per informazioni più aggiornate sui limiti, vedere [abbonamento Azure e limiti del servizio, le quote e vincoli](../azure-subscription-service-limits.md).

Eseguire il cmdlet seguente per individuare il nome e lo stato degli account lo spazio di archiviazione in abbonamento corrente:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Come creare un contesto di archiviazione Azure
Contesto di archiviazione Azure è un oggetto di PowerShell per racchiudere le credenziali di spazio di archiviazione. Utilizzando il contesto di spazio di archiviazione durante l'esecuzione di tutti i cmdlet successive consente di eseguire l'autenticazione la richiesta senza specificare l'account di archiviazione e la relativa chiave di accesso in modo esplicito. È possibile creare un contesto dello spazio di archiviazione in molti modi, ad esempio utilizzando chiave nome e l'accesso account lo spazio di archiviazione, token di accesso condiviso firme (SA), la stringa di connessione o anonimi. Per ulteriori informazioni, vedere [AzureStorageContext di nuovo](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilizzare uno dei seguenti tre modi per creare un contesto di archiviazione:

- Eseguire il cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) per individuare il tasto di scelta principale dello spazio di archiviazione per l'account di archiviazione Azure. Successivamente, chiamare il cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) per creare un contesto dello spazio di archiviazione:

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generare un token di firma accesso condiviso per un contenitore di archiviazione Azure e usarlo per creare un contesto dello spazio di archiviazione:

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Per ulteriori informazioni, vedere [Nuovo AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) e [Utilizzando le firme di Access condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md).

- In alcuni casi può essere necessario specificare gli endpoint del servizio quando si crea un nuovo contesto di spazio di archiviazione. Potrebbe essere necessario dopo aver registrato un nome di dominio personalizzato per l'account di archiviazione con il servizio Blob o si desidera utilizzare una firma di accesso condiviso per accedere alle risorse di archiviazione. Impostare gli endpoint del servizio in una stringa di connessione e usarlo per creare un nuovo contesto di spazio di archiviazione, come illustrato di seguito:

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Per ulteriori informazioni su come configurare una stringa di connessione di spazio di archiviazione, vedere [Configurazione di stringhe di connessione](storage-configure-connection-string.md).

Configurare il computer e appreso come gestire le sottoscrizioni e gli account di archiviazione tramite PowerShell di Azure, passare alla sezione successiva per imparare a gestire BLOB Azure e blob istantanee.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Come recuperare e rigenerare chiavi per l'archiviazione Azure

Un account di archiviazione Azure viene fornito con due chiavi account. È possibile utilizzare il cmdlet seguente per recuperare le chiavi.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Utilizzare il cmdlet seguente per recuperare una chiave specifica. Valori validi sono primario e secondario.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Se si desidera rigenerare le chiavi, utilizzare il cmdlet seguente. Valori validi per - KeyType sono "Principale" e "Secondarie"

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Come gestire BLOB Azure
Archiviazione Blob Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio testo o dati binari, che è possibile accedervi da in un punto qualsiasi nel mondo tramite HTTP o HTTPS. In questa sezione presuppone che si ha già familiarità con i concetti di servizio di archiviazione Blob Azure. Per informazioni dettagliate, vedere [Guida introduttiva a archiviazione Blob con .NET](storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Come creare un contenitore
Ogni blob in archiviazione Azure deve essere in un contenitore. È possibile creare un contenitore privato utilizzando il cmdlet New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Esistono tre livelli di accesso in lettura anonimo: **disattivare** **Blob**e **contenitore**. Per impedire l'accesso anonimo BLOB, impostare il parametro di autorizzazione per **disattivare**. Per impostazione predefinita, il nuovo contenitore è privato e possa accedere solo il proprietario dell'account. Per consentire pubblico lettura accesso anonimo alle risorse blob, ma non per i metadati contenitore o all'elenco di BLOB nel contenitore, impostare il parametro di autorizzazione a **Blob**. Per consentire l'accesso in lettura pubblica completa blob risorse, contenitore metadati e l'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione al **contenitore**. Per ulteriori informazioni, vedere [gestire anonimo accesso in lettura contenitori e BLOB](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Come caricare un blob in un contenitore
Archiviazione Blob Azure supporta BLOB blocco e BLOB di pagine. Per ulteriori informazioni, vedere [Understanding blocco BLOB, BLOB di accodamento e BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Per caricare BLOB in un contenitore di sezioni, è possibile utilizzare il cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . Per impostazione predefinita, questo comando consente di caricare i file locali un blob di blocco. Per specificare il tipo per il blob, è possibile utilizzare il parametro - BlobType.

Nell'esempio seguente viene eseguito il cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) per visualizzare tutti i file nella cartella specificata e quindi li passa al cmdlet successivo usando l'operatore pipeline. Il cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) consente di caricare i file locali il contenitore:

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Come scaricare BLOB da un contenitore
Nell'esempio seguente viene illustrato come scaricare BLOB da un contenitore. Nell'esempio innanzitutto stabilisce una connessione allo spazio di archiviazione di Azure utilizzando il contesto di account di archiviazione, che include il nome dell'account di archiviazione e la chiave primaria di access. Quindi, nell'esempio recupera un riferimento blob utilizzando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . Quindi, nell'esempio utilizza il cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) per scaricare BLOB nella cartella locale di destinazione.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Come copiare BLOB da un contenitore di spazio di archiviazione a un'altra
È possibile copiare in modo asincrono BLOB in più aree geografiche e gli account di archiviazione. Nell'esempio seguente viene illustrato come copiare BLOB da un contenitore di spazio di archiviazione due account di archiviazione diversa. Nell'esempio innanzitutto imposta le variabili per gli account di archiviazione di origine e di destinazione e quindi crea un contesto dello spazio di archiviazione per ogni account. Quindi, nell'esempio copiato BLOB dal contenitore di origine al contenitore di destinazione mediante il cmdlet [Start AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) . Si presuppone che l'account di spazio di archiviazione di origine e destinazione e contenitori esistano già.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Si noti che in questo esempio esegue una copia asincrona. È possibile monitorare lo stato di ogni copia eseguendo il cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Come copiare BLOB da una posizione secondaria
È possibile copiare BLOB dalla posizione secondaria di un account RA GRS abilitato.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Come eliminare un blob
Per eliminare un blob, prima di tutto ottenere un riferimento blob e quindi chiamare il cmdlet Rimuovi AzureStorageBlob su di esso. Nell'esempio seguente elimina tutti i BLOB in un dato contenitore. Nell'esempio innanzitutto imposta le variabili per un account di archiviazione e quindi crea un contesto dello spazio di archiviazione. Quindi, nell'esempio recupera un riferimento blob utilizzando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e viene eseguito il cmdlet [AzureStorageBlob Rimuovi](http://msdn.microsoft.com/library/azure/dn806399.aspx) per rimuovere un contenitore di Azure archiviazione BLOB.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Come gestire istantanee blob Azure
Azure consente di creare uno snapshot dei blob. Uno snapshot è una versione di sola lettura di un blob che viene eseguita in un punto nel tempo. Dopo aver creato uno snapshot, è è possibile leggere, copiata, o eliminato ma non modificarli. Snapshot consentono di eseguire il backup blob come appare in un momento. Per ulteriori informazioni, vedere [creazione di uno Snapshot dei Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Come creare uno snapshot blob
Per creare dello stile di vita di un blob, prima di tutto ottenere un riferimento blob, quindi chiamare il `ICloudBlob.CreateSnapshot` metodo su di esso. Nell'esempio seguente prima di tutto imposta le variabili per un account di archiviazione e quindi crea un contesto dello spazio di archiviazione. Quindi, nell'esempio recupera un riferimento blob utilizzando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e viene eseguito il metodo [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) per creare uno snapshot.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Come snapshot del blob di elenco
È possibile creare tanti snapshot desiderate per un blob. È possibile elencare le istantanee associate il blob per tenere traccia degli snapshot correnti. Nell'esempio seguente viene utilizzato un blob predefinito e chiama il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) per elencare gli snapshot di tale blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Come copiare uno snapshot di un blob
È possibile copiare uno snapshot del blob per ripristinare lo snapshot. Per informazioni dettagliate e limitazioni, vedere [creazione di uno Snapshot dei Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). Nell'esempio seguente prima di tutto imposta le variabili per un account di archiviazione e quindi crea un contesto dello spazio di archiviazione. Quindi, nell'esempio definisce le variabili di nome contenitore e blob. Nell'esempio recupera un riferimento blob tramite il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) ed esegue il metodo [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) per creare uno snapshot. Nell'esempio eseguirà il cmdlet [Start AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) per copiare lo snapshot di un blob usando l'oggetto ICloudBlob per il blob di origine. Assicurarsi di aggiornare le variabili in base alla configurazione prima di eseguire l'esempio. Si noti che nell'esempio seguente presuppone che l'origine e destinazione contenitori e blob origine esista già.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Dopo avere appreso come gestire BLOB Azure e blob istantanee con PowerShell di Azure, passare alla sezione successiva per imparare a gestire le tabelle, code e file.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Come gestire tabelle Azure ed entità della tabella
Servizio di archiviazione tabella Azure è un archivio di dati NoSQL che è possibile utilizzare per archiviare ed eseguire query di grandi dimensioni set di dati strutturati e non relazionali. Componenti principali del servizio sono tabelle, entità e proprietà. Una tabella è un insieme di entità. Un'entità è un insieme di proprietà. Ogni entità può avere fino a 252 proprietà, ovvero tutte coppie nome-valore. In questa sezione presuppone che si ha già familiarità con i concetti del servizio di archiviazione tabelle Azure. Per informazioni dettagliate, vedere [informazioni sul modello di dati di tabella servizio](http://msdn.microsoft.com/library/azure/dd179338.aspx) e [iniziare a utilizzare archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md).

Nelle sottosezioni seguenti sarà imparare a gestire il servizio di archiviazione tabella Azure tramite PowerShell Azure. Gli scenari coperti includono **creazione**, **eliminazione**e **recupero** di **tabelle**, nonché **aggiunta**, **la ricerca**ed **eliminazione entità della tabella**.

### <a name="how-to-create-a-table"></a>Come creare una tabella
In ogni tabella deve trovarsi in un account di archiviazione Azure. Nell'esempio seguente viene illustrato come creare una tabella in archiviazione Azure. Nell'esempio innanzitutto stabilisce una connessione allo spazio di archiviazione di Azure utilizzando il contesto di account di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Il cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) viene quindi utilizzato per creare una tabella in archiviazione Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Come recuperare una tabella
È possibile richiedere e recuperare una o tutte le tabelle in un account di archiviazione. Nell'esempio seguente viene illustrato come recuperare una determinata tabella utilizzando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Se si chiama il cmdlet Get-AzureStorageTable senza parametri, ottiene tutte le tabelle di spazio di archiviazione per un account di archiviazione.

### <a name="how-to-delete-a-table"></a>Come eliminare una tabella
È possibile eliminare una tabella da un account di archiviazione tramite il cmdlet [Rimuovi AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Come gestire entità della tabella
Attualmente, PowerShell Azure non sono disponibili i cmdlet per gestire direttamente le entità della tabella. Per eseguire operazioni su entità della tabella, è possibile utilizzare le classi indicate in [Una raccolta di Client lo spazio di archiviazione di Azure per .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Come aggiungere entità della tabella
Per aggiungere un'entità a una tabella, creare innanzitutto un oggetto che consente di definire le proprietà di entità. Un'entità può avere fino a 255 proprietà, tra cui 3 Proprietà-sistema: **PartitionKey**, **RowKey**e **Timestamp**. Sei responsabile per l'inserimento e aggiornamento dei valori di **PartitionKey** e **RowKey**. Il server gestisce il valore di **Timestamp**, che non possono essere modificate. Insieme di **PartitionKey** e **RowKey** identificano ogni entità all'interno di una tabella.

-   **PartitionKey**: determina la partizione entità archiviata in.
-   **RowKey**: identifica l'entità all'interno della partizione.

È possibile definire fino a 252 proprietà personalizzate per un'entità. Per ulteriori informazioni, vedere [informazioni sul modello di dati del servizio di tabella](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Nell'esempio seguente viene illustrato come aggiungere entità a una tabella. Nell'esempio viene illustrato come recuperare la tabella dei dipendenti e aggiungere diversi entità al suo interno. Prima di tutto una connessione allo spazio di archiviazione di Azure utilizzando il contesto di account di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, recupera la tabella specificata utilizzando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se la tabella non esiste, il cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) viene utilizzato per creare una tabella in archiviazione Azure. Successivamente, viene definita una funzione personalizzata entità Aggiungi per aggiungere entità alla tabella specificando partizione ogni entità e dei tasti di riga. La funzione di Aggiungi entità chiama il cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) in classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) per creare un oggetto entità. In un secondo momento, l'esempio chiama il metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) sull'oggetto entità per aggiungerla a una tabella.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Come eseguire query su entità della tabella
Per eseguire una query una tabella, utilizzare la classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . Nell'esempio seguente presuppone che è già stato eseguito lo script indicato in come aggiungere sezione entità di questa Guida. Nell'esempio innanzitutto stabilisce una connessione allo spazio di archiviazione di Azure utilizzando il contesto di spazio di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, tenterà di recuperare la tabella "Dipendenti" creata in precedenza utilizzando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Chiamare il cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) della classe Microsoft.WindowsAzure.Storage.Table.TableQuery crea un nuovo oggetto di query. Nell'esempio consente di cercare le entità che dispone di una colonna 'ID' il cui valore è 1 come specificato in un filtro stringa. Per informazioni dettagliate, vedere [tabelle di query ed entità](http://msdn.microsoft.com/library/azure/dd894031.aspx). Quando si esegue la query, restituisce tutte le entità che soddisfano i criteri di filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Come eliminare entità della tabella
È possibile eliminare un'entità utilizzando i tasti partizione e riga. Nell'esempio seguente presuppone che è già stato eseguito lo script indicato in come aggiungere sezione entità di questa Guida. Nell'esempio innanzitutto stabilisce una connessione allo spazio di archiviazione di Azure utilizzando il contesto di spazio di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, tenterà di recuperare la tabella "Dipendenti" creata in precedenza utilizzando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se la tabella esistente, nell'esempio chiama il metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) per recuperare un'entità in base ai relativi valori chiavi partizione e riga. Quindi, passare l'entità il metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) da eliminare.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Come gestire code Azure e i messaggi
Lo spazio di archiviazione di Azure coda è un servizio per l'archiviazione di un numero elevato di messaggi accessibili da qualsiasi punto del mondo tramite chiamate autenticate con HTTP o HTTPS. In questa sezione presuppone che si ha già familiarità con i concetti il servizio di archiviazione di Azure coda. Per informazioni dettagliate, vedere [Guida introduttiva di archiviazione di Azure coda mediante .NET](storage-dotnet-how-to-use-queues.md).

In questa sezione illustra come gestire il servizio di archiviazione di Azure coda tramite PowerShell Azure. Gli scenari coperti includono **l'inserimento** ed **eliminazione** coda messaggi, nonché **creazione**, **eliminazione**e **recupero di code**.

### <a name="how-to-create-a-queue"></a>Come creare una coda
Nell'esempio seguente prima di tutto stabilisce una connessione allo spazio di archiviazione di Azure utilizzando il contesto di account di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, chiama cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) per creare una coda denominata 'coda'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Per informazioni sulle convenzioni di denominazione per il servizio di coda Azure, vedere [denominazione code e i metadati](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Come recuperare una coda
È possibile richiedere e recuperare una specifica coda o un elenco di tutte le code di un account di archiviazione. Nell'esempio seguente viene illustrato come recuperare una coda specificata utilizzando il cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Se si chiama il cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) senza parametri, riceve un elenco di tutte le code.

### <a name="how-to-delete-a-queue"></a>Come eliminare una coda
Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il cmdlet AzureStorageQueue Rimuovi. Nell'esempio seguente viene illustrato come eliminare una coda specificata utilizzando il cmdlet AzureStorageQueue Rimuovi.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Come inserire un messaggio in una coda
Per inserire un messaggio in una coda esistente, prima di tutto creare una nuova istanza della classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Successivamente, chiamare il metodo [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Un CloudQueueMessage può essere create da una stringa (in formato UTF-8) o una matrice di byte.

Nell'esempio seguente viene illustrato come aggiungere messaggio a una coda. Nell'esempio innanzitutto stabilisce una connessione allo spazio di archiviazione di Azure usando il contesto di account di archiviazione, che include il nome dell'account di archiviazione e la relativa chiave di accesso. Quindi recupera coda specificata utilizzando il cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Se coda è presente, il cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) viene utilizzato per creare un'istanza della classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . In un secondo momento, l'esempio chiama il metodo [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) sull'oggetto messaggio per aggiungerla a una coda. Ecco il codice che recupera una coda e viene inserito il messaggio 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Come annullare l'accodamento il messaggio successivo
Il codice coda un messaggio da una coda in due passaggi. Quando si chiama il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , viene visualizzato il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per completare la rimozione del messaggio dalla coda, è anche necessario chiamare il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice chiama **DeleteMessage** destra dopo l'elaborazione di messaggio.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Come gestire i file e nelle condivisioni file Azure
Spazio di archiviazione File Azure offre condivisa dello spazio di archiviazione per le applicazioni che usano il protocollo piccole e medie imprese standard. Macchine virtuali di Microsoft Azure e servizi cloud possono condividere dati di file tra i componenti dell'applicazione tramite condivisioni collegate e le applicazioni in locale possono accedere dati dei file in una condivisione tramite l'API di spazio di archiviazione File o Azure PowerShell.

Per ulteriori informazioni sull'archiviazione dei File di Azure, vedere [Introduzione a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md) e [API REST di servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Come installare e query analitica lo spazio di archiviazione
È possibile utilizzare [Azure archiviazione Analitica](storage-analytics.md) per raccogliere metrica per l'account di archiviazione Azure e registra i dati di richieste inviati all'account di archiviazione. È possibile utilizzare metriche di archiviazione per monitorare lo stato di un account di archiviazione e la registrazione dello spazio di archiviazione per la diagnosi e risoluzione dei problemi con l'account di archiviazione.
Per impostazione predefinita, metriche di archiviazione non sono abilitata per i servizi di archiviazione. È possibile attivare il monitoraggio tramite il portale di Azure e Windows PowerShell di o a livello di programmazione tramite la libreria di client lo spazio di archiviazione. Registrazione di spazio di archiviazione accade sul lato server e consente di registrare i dettagli per le richieste di riusciti e non nel proprio account di archiviazione. I registri consentono di visualizzare i dettagli di leggere, scrivere ed eliminare le operazioni con le tabelle, il BLOB e code, nonché i motivi per cui le richieste non riuscite.

Per informazioni su come attivare e visualizzare dati metriche di archiviazione tramite PowerShell, vedere [come abilitare metriche di archiviazione tramite PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Per informazioni su come abilitare e recuperare dati lo spazio di archiviazione registrazione tramite PowerShell, vedere [come abilitare la registrazione dello spazio di archiviazione tramite PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) e [trovare i dati di registro registrazione lo spazio di archiviazione](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Per informazioni dettagliate sull'uso di metriche di archiviazione e la registrazione di spazio di archiviazione per la risoluzione dei problemi di archiviazione, vedere [monitoraggio, diagnosi dei e risoluzione dei problemi di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Come gestire condiviso Access firma (SA) e criteri di accesso archiviato
Accesso condiviso firme sono una parte importante del modello di sicurezza per le applicazioni che utilizzano lo spazio di archiviazione di Azure. Sono utili per fornire autorizzazioni limitate al proprio account di archiviazione per i clienti che non devono avere la chiave account. Per impostazione predefinita, può accedere solo il proprietario dell'account di archiviazione BLOB, tabelle e code all'interno di tale account. Se il servizio o l'applicazione deve rendere queste risorse disponibili agli altri client, senza condividere il tasto di scelta, sono disponibili tre opzioni:

- Impostare le autorizzazioni del contenitore per consentire l'accesso in lettura anonimo il contenitore e il relativo BLOB. Non è consentito per tabelle o le code.
- Utilizzare una firma di accesso condiviso concede limitarla diritti di accesso per i contenitori, BLOB, code e tabelle per un intervallo di tempo specifico.
- Utilizzare un criterio di accesso archiviate per ottenere un livello aggiuntivo di controllo per le firme di accesso condiviso per un contenitore o il relativo BLOB, per una coda o per una tabella. Il criterio di accesso archiviate consente di modificare le autorizzazioni per una firma, ora di inizio o ora di scadenza, o per revocare dopo tale voce è stato rilasciato.

Una firma di accesso condiviso può trovarsi in uno dei due modi:

- **Ad hoc SA**: quando si crea un SA ad hoc, l'ora di inizio, ora di scadenza e le autorizzazioni per le associazioni di sicurezza vengono specificate in URI SAS. Questo tipo di SA potrebbe essere creato in un contenitore di blob, tabella o coda ed è non revokable.
- **Associazioni di protezione con criterio di accesso archiviate**: viene definito un criterio di accesso archiviate in un contenitore di risorse un contenitore di blob, una tabella o una coda - ed è possibile utilizzare per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa un SA a un criterio di accesso archiviate, le associazioni di protezione eredita i vincoli - autorizzazioni - definite per il criterio di accesso archiviate, ora di inizio e ora di scadenza. Questo tipo di SA è revokable.

Per ulteriori informazioni, vedere [Usare firme di Access condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md) e [Gestisci anonimo accesso in lettura contenitori e BLOB](storage-manage-access-to-resources.md).

Nelle sezioni successive, si imparerà creare un criterio di accesso token e archiviati firma accesso condiviso per le tabelle Azure. Azure PowerShell fornisce simili cmdlet per i contenitori, BLOB e anche code. Per eseguire gli script in questa sezione, scaricare [PowerShell Azure versione 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) o versione successiva.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Come creare un token di firme accesso condiviso basate sui criteri
Utilizzare il cmdlet New-AzureStorageTableStoredAccessPolicy per creare un nuovo criterio di accesso archiviate. Quindi, chiamare il cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) per creare un nuovo token di firma in base a criteri di accesso condiviso per una tabella di archiviazione Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Come creare un token di firma di Access condiviso (non revokable) ad hoc
Utilizzare il cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) per creare un nuovo token del condiviso firma di Access (non revokable) ad hoc, per una tabella di archiviazione Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Come creare un criterio di accesso archiviate
Utilizzare il cmdlet New-AzureStorageTableStoredAccessPolicy per creare un nuovo criterio di accesso archiviato per una tabella di archiviazione Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Come aggiornare un criterio di accesso archiviate
Utilizzare il cmdlet Set-AzureStorageTableStoredAccessPolicy per aggiornare un criterio di accesso archiviato esistente per una tabella di archiviazione Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Come eliminare un criterio di accesso archiviate
Utilizzare il cmdlet AzureStorageTableStoredAccessPolicy Rimuovi per eliminare un criterio di accesso archiviate in una tabella di archiviazione Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Come usare lo spazio di archiviazione di Azure per governo degli Stati Uniti e Cina Azure
Un ambiente Azure è una distribuzione indipendente di Microsoft Azure, ad esempio [Per la pubblica amministrazione Azure per governo degli Stati Uniti](https://azure.microsoft.com/features/gov/), [AzureCloud per Azure globale](https://portal.azure.com)e [AzureChinaCloud per Azure gestito da 21Vianet in Cina](http://www.windowsazure.cn/). È possibile distribuire nuovi ambienti Azure per statunitense per enti pubblici e Azure Cina.

Per utilizzare lo spazio di archiviazione di Azure con AzureChinaCloud, è necessario creare un contesto di archiviazione associato AzureChinaCloud. Seguire questi passaggi per iniziare:

1.  Eseguire il cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) per visualizzare gli ambienti di Azure disponibili:

    `Get-AzureEnvironment`

2.  Aggiungere un account Azure Cina a Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Creare un contesto di archiviazione per un account AzureChinaCloud:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Per utilizzare lo spazio di archiviazione di Azure con [Governo degli Stati Uniti Azure](https://azure.microsoft.com/features/gov/), si deve definire un nuovo ambiente e quindi si crea un nuovo contesto di spazio di archiviazione con questo ambiente:

1.  Eseguire il cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) per visualizzare gli ambienti di Azure disponibili:

    `Get-AzureEnvironment`

2.  Aggiungere un account Azure IT per la pubblica amministrazione a Windows PowerShell:

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Creare un contesto di archiviazione per un account AzureUSGovernment:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Per ulteriori informazioni, vedere:

- [Guida per gli sviluppatori di Microsoft Azure per enti pubblici](../azure-government-developer-guide.md).
- [Panoramica delle differenze durante la creazione di un'applicazione del servizio di Cina](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Passaggi successivi
In questa guida aver appreso come gestire l'archiviazione di Azure con PowerShell Azure. Ecco alcuni articoli correlati e risorse utili per ottenere ulteriori informazioni sulle loro.

- [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Cmdlet di PowerShell archiviazione Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Riferimento di Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
