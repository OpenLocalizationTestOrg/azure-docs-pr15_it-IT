<properties
   pageTitle="Guida introduttiva di Azure Batch PowerShell | Microsoft Azure"
   description="Una rapida introduzione ai cmdlet di PowerShell Azure che è possibile utilizzare per gestire il servizio di Azure Batch"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Iniziare a utilizzare i cmdlet di PowerShell Batch Azure
Con i cmdlet di PowerShell Batch Azure, è possibile eseguire e script molte attività che svolgere con le API Batch, il portale di Azure e Azure interfaccia riga di comando (CLI). Si tratta di una breve introduzione ai cmdlet che è possibile utilizzare per gestire gli account Batch e lavorare con le risorse sono Batch, ad esempio pool, processi e attività.

Per un elenco completo dei cmdlet di Batch e sintassi cmdlet dettagliate, vedere il [riferimento sui cmdlet di Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

In questo articolo si basa su cmdlet di PowerShell Azure versione 3.0.0. È consigliabile aggiornare il PowerShell Azure frequentemente per usufruire di miglioramenti e aggiornamenti del servizio.

## <a name="prerequisites"></a>Prerequisiti

Eseguire le operazioni seguenti per l'utilizzo di Azure PowerShell per gestire le risorse Batch.

* [Installare e configurare PowerShell Azure](../powershell-install-configure.md)

* Eseguire il cmdlet **AzureRmAccount di accesso** per connettersi al proprio abbonamento (spedizione cmdlet il Batch di Azure del modulo di gestione delle risorse Azure):

    `Login-AzureRmAccount`

* **Eseguire la registrazione con lo spazio dei nomi provider Batch**. Questa operazione deve avere eseguito **una volta all'abbonamento**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gestire gli account Batch e chiavi

### <a name="create-a-batch-account"></a>Creare un account di Batch

**Nuovo AzureRmBatchAccount** crea un account di Batch in un gruppo di risorse specificato. Se si dispone già di un gruppo di risorse, crearne uno eseguendo il cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Specificare una delle regioni Azure nel parametro **posizione** , ad esempio "Centrale USA". Per esempio:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Nel gruppo di risorse, specificare un nome per l'account in <*account_name*> e il percorso e il nome del gruppo risorse, creare un account di Batch. Creazione di account Batch può richiedere del tempo per completare. Per esempio:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] Il nome dell'account Batch deve essere univoco all'area di Azure per il gruppo di risorse, contenga da 3 a 24 caratteri e utilizzare solo numeri e lettere minuscole.

### <a name="get-account-access-keys"></a>Ottenere i tasti di scelta account
**Get-AzureRmBatchAccountKeys** Mostra i tasti di scelta associati a un account Azure Batch. Ad esempio, eseguire le operazioni seguenti per ottenere le chiavi primarie e secondarie dell'account che è stato creato.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generare una nuova chiave di accesso
**Nuovo AzureRmBatchAccountKey** genera una nuova chiave primaria o secondaria account per un account Azure Batch. Ad esempio per generare una nuova chiave primaria per il proprio account Batch, digitare:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Per generare una nuova chiave secondaria, specificare "Secondario" per il parametro **KeyType** . È necessario rigenerare le chiavi primarie e secondarie separatamente.

### <a name="delete-a-batch-account"></a>Eliminare un account di Batch
**Rimuovi AzureRmBatchAccount** consente di eliminare un account di Batch. Per esempio:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando richiesto, confermare che si desidera rimuovere l'account. Rimozione di account può richiedere del tempo per completare.

## <a name="create-a-batchaccountcontext-object"></a>Creare un oggetto BatchAccountContext

Per l'autenticazione tramite i cmdlet di PowerShell Batch quando si crea e Gestisci pool Batch, processi, attività e altre risorse, creare innanzitutto un oggetto BatchAccountContext per archiviare il nome dell'account e chiavi:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Passare all'oggetto BatchAccountContext in cmdlet per l'utilizzo del parametro **BatchContext** .

> [AZURE.NOTE] Per impostazione predefinita, chiave primaria dell'account viene usata per l'autenticazione, ma è possibile selezionare la chiave da utilizzare per la modifica proprietà **KeyInUse** dell'oggetto BatchAccountContext: `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Creare e modificare le risorse Batch
Utilizzare i cmdlet, ad esempio **AzureBatchPool di nuovo**, **Nuovo AzureBatchJob**e **AzureBatchTask di nuovo** per creare le risorse con un account di Batch. Sono corrispondenti **Get -** e i cmdlet **Set -** per aggiornare le proprietà delle risorse esistenti e **Rimuovi -** cmdlet per rimuovere le risorse con un account di Batch.

Quando si utilizza molti di questi cmdlet, oltre a passare un oggetto BatchContext, è necessario creare o passare oggetti che contengono le impostazioni delle risorse dettagliata, come illustrato nell'esempio seguente. Visualizzare informazioni dettagliate per ogni cmdlet per altri esempi.

### <a name="create-a-batch-pool"></a>Creare un pool di Batch

Durante la creazione o aggiornamento di un pool di Batch, si seleziona una configurazione del servizio cloud o una configurazione macchina virtuale per il sistema operativo sui nodi di calcolo (vedere [Panoramica delle funzionalità Batch](batch-api-basics.md#pool)). La scelta determina se i nodi di calcolo siano mostrati con uno dei [sistemi operativi Guest Azure rilascia](../cloud-services/cloud-services-guestos-update-matrix.md#releases) o con uno supportati immagini Linux o macchine Virtuali di Windows Azure Marketplace.

Quando si esegue di **Nuovo AzureBatchPool**, passare le impostazioni di sistema operativo in un oggetto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Ad esempio, il cmdlet seguente crea un nuovo pool di Batch con nodi di calcolo di piccole dimensioni nella configurazione del servizio cloud, dell'immagine con l'ultima versione del sistema operativo della famiglia 3 (Windows Server 2012). In questo caso, il parametro **CloudServiceConfiguration** specifica la variabile *$configuration* come oggetto PSCloudServiceConfiguration. Il parametro **BatchContext** specifica una variabile definita in precedenza *$context* dell'oggetto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Il numero dei nodi di calcolo nel pool di nuova destinazione dipende dalla formula in forma di adattamento automatico. In questo caso, la formula è sufficiente **$TargetDedicated = 4**, che indica il numero dei nodi di calcolo nel pool di è "4" al massimo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Query per i pool, processi, attività e altri dettagli

Utilizzare i cmdlet, ad esempio **Get-AzureBatchPool**, **Get-AzureBatchJob**e **Get-AzureBatchTask** a query per entità create con un account di Batch.

### <a name="query-for-data"></a>Query per i dati

Ad esempio, utilizzare **Get-AzureBatchPools** per trovare i pool. Per impostazione predefinita questa query per tutti i pool con il proprio account, presupponendo che è già archiviato l'oggetto BatchAccountContext in *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utilizzare un filtro OData

È possibile fornire un filtro di OData utilizzando il parametro di **filtro** per trovare solo gli oggetti che ti interessano. Ad esempio, è possibile trovare tutti i pool con ID partire da "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Questo metodo non è quanto più flessibile tramite "Where-Object" nella pipeline di locale. Tuttavia, la query viene inviata al servizio Batch direttamente in modo che tutti i filtri accade sul lato server, il salvataggio della larghezza di banda Internet.

### <a name="use-the-id-parameter"></a>Utilizzare il parametro Id

In alternativa a un filtro di OData è possibile utilizzare il parametro **Id** . Eseguire una query per uno specifico gruppo con id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Il parametro **Id** supporta solo ricerca id completa, non i caratteri jolly o lo stile OData filtri.

### <a name="use-the-maxcount-parameter"></a>Utilizzare il parametro MaxCount

Per impostazione predefinita, ogni cmdlet restituisce un numero massimo di 1000 oggetti. Se si raggiunge questo limite, perfezionare il filtro per visualizzare nuovamente meno oggetti o impostati in modo esplicito un massimo di utilizzo del parametro **MaxCount** . Per esempio:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Per rimuovere il limite superiore, impostare **MaxCount** a 0 o meno.

### <a name="use-the-powershell-pipeline"></a>Utilizzare la pipeline di PowerShell

Cmdlet di batch possibile sfruttare la pipeline di PowerShell per inviare dati tra i cmdlet. Ha lo stesso effetto dell'impostazione di un parametro, ma semplifica l'utilizzo di più entità.

Ad esempio, trovare e visualizzare tutte le attività con l'account:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Riavvio (riavvio) ogni nodo di calcolo in un pool:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestione dei pacchetti di applicazione

Pacchetti di applicazioni offrono un modo semplificato per distribuire le applicazioni dei nodi di elaborazione nei pool. Con i cmdlet di PowerShell Batch, è possibile caricare e gestire i pacchetti di applicazioni nell'account Batch e distribuire le versioni del pacchetto per nodi di calcolo.

**Creare** un'applicazione:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Aggiungere** un pacchetto dell'applicazione:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Impostare la **versione predefinita** per l'applicazione:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Elenco** pacchetti di un'applicazione

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Eliminare** un pacchetto dell'applicazione

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Eliminare** un'applicazione

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Prima di eliminare l'applicazione, è necessario eliminare tutte le versioni di pacchetto dell'applicazione di un'applicazione. Se si tenta di eliminare un'applicazione che ha attualmente pacchetti di applicazioni, si riceverà un errore 'Conflitto'.

### <a name="deploy-an-application-package"></a>Distribuire un pacchetto dell'applicazione

È possibile specificare uno o più pacchetti di applicazione per la distribuzione quando si crea un pool. Quando si specifica un pacchetto al momento della creazione del pool, viene distribuito a ciascun nodo come il pool di join nodo. Pacchetti vengono distribuiti anche quando un nodo viene riavviato o ricostruire l'immagine.

Specificare la `-ApplicationPackageReference` opzione durante la creazione di un pool per distribuire un pacchetto dell'applicazione in nodi del pool che si connettono del pool. Prima di tutto, creare un oggetto **PSApplicationPackageReference** e configurarlo con la versione di pacchetto e Id dell'applicazione che si desidera distribuire nodi di elaborazione del pool:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

A questo punto creare del pool e specificare l'oggetto di riferimento pacchetto come argomento per la `ApplicationPackageReferences` opzione:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Disponibili ulteriori informazioni sui pacchetti di applicazioni nella [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md).

>[AZURE.IMPORTANT] È necessario [un account di archiviazione Azure collegamento](#linked-storage-account-autostorage) al proprio account Batch per utilizzare i pacchetti di applicazione.

### <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti di applicazioni del pool

Per aggiornare le applicazioni assegnate a un pool esistente, creare innanzitutto un oggetto PSApplicationPackageReference con le proprietà desiderate (versione dell'applicazione Id e pacchetto):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Successivamente, è possibile ottenere il pool dal Batch, cancellare gli eventuali pacchetti esistenti, aggiungere il nuovo pacchetto di riferimento e aggiornare il servizio di Batch con le nuove impostazioni del pool:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

A questo punto è stato aggiornato le proprietà del pool nel servizio Batch. Per distribuire il nuovo pacchetto di applicazione per nodi nel pool di calcolo, tuttavia, è necessario riavviare o creare una nuova immagine i nodi. È possibile riavviare tutti i nodi in un pool con questo comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] È possibile distribuire più pacchetti di applicazione dei nodi di elaborazione in un pool. Se si desidera *aggiungere* un pacchetto dell'applicazione anziché sostituendo i pacchetti attualmente installati, omettere il `$pool.ApplicationPackageReferences.Clear()` riga precedente.

## <a name="next-steps"></a>Passaggi successivi

* Per sintassi cmdlet dettagliate ed esempi, vedere [informazioni di riferimento cmdlet Batch Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Per ulteriori informazioni sulle applicazioni e di pacchetti di applicazioni in Batch, vedere [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md).
