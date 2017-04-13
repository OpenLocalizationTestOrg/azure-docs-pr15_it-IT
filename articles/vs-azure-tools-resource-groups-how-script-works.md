<properties
    pageTitle="Panoramica dello script di distribuzione progetto di gruppo di risorse Azure | Microsoft Azure"
    description="Descrive come funziona lo script di PowerShell nel progetto di distribuzione di Azure gruppo di risorse."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Panoramica dello script di distribuzione progetto di gruppo di risorse Azure

Informazioni sul passaggio e distribuire file e altri elementi in Azure progetti di distribuzione di Azure gruppo di risorse. Quando si crea un progetto di distribuzione di gestione di risorse Azure in Visual Studio, uno script di PowerShell denominato **Distribuisci AzureResourceGroup.ps1** viene aggiunta al progetto. In questo argomento vengono fornite informazioni dettagliate su che cosa significa questo script e su come eseguirlo all'interno e all'esterno di Visual Studio.

## <a name="what-does-the-script-do"></a>Qual è lo script?

Lo script AzureResourceGroup.ps1 Distribuisci esegue due operazioni che è importante il flusso di lavoro di distribuzione.

- Caricare i file o gli elementi necessari per la distribuzione dei modelli
- Distribuire il modello

La prima parte dello script carica i file e gli elementi per la distribuzione e il cmdlet ultimo nello script distribuire il modello. Ad esempio, se una macchina virtuale deve essere configurato con uno script, lo script di distribuzione prima di tutto in modo sicuro consente di caricare lo script di configurazione a un account di archiviazione Azure. Per renderlo disponibile per gestione di risorse di Azure per configurare la macchina virtuale durante il provisioning.

Poiché non tutte le distribuzioni di modello è necessitano elementi aggiuntivi che devono essere caricate, viene valutato il parametro parametro *uploadArtifacts* . Se tutti gli elementi devono essere caricate, impostare l'opzione *uploadArtifacts* quando si chiama lo script. Tenere presente che il file di modello principale parametri non è necessario essere caricati. Solo gli altri file, ad esempio gli script di configurazione, nidificati modelli di distribuzione ed è necessario caricare i file di applicazione.

## <a name="detailed-script-description"></a>Descrizione dettagliata script

Di seguito è una descrizione delle quali selezionare sezioni si script Distribuisci AzureResourceGroup.ps1 Azure PowerShell.

>[AZURE.NOTE] Descrive versione 1.0 dello script AzureResourceGroup.ps1 Distribuisci.

1.  Dichiarare parametri necessari per il progetto di distribuzione di Manager delle risorse di Azure. Alcuni parametri includono valori predefiniti che sono stati impostati quando è stato creato il progetto. È possibile modificare i valori predefiniti nello script o aggiungere i valori dei parametri diversi prima di eseguire lo script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Parametro|Descrizione|
  	|---|---|
  	|$ResourceGroupLocation|L'area geografica o centro dati percorso per il gruppo di risorse, ad esempio **Occidentale degli Stati Uniti** o **Asia orientale**.|
  	|$ResourceGroupName|Il nome del gruppo di risorse Azure.|
  	|$UploadArtifacts|Valore binario che indica se gli elementi devono essere caricate in Azure dal sistema.|
  	|$StorageAccountName|Il nome del proprio account di archiviazione Azure in cui vengono caricati gli elementi.|
  	|$StorageAccountResourceGroupName|Il nome del gruppo di risorse Azure che contiene l'account di archiviazione.|
  	|$StorageContainerName|Il nome del contenitore di spazio di archiviazione utilizzato per il caricamento degli elementi.|
  	|$TemplateFile|Il percorso del file di distribuzione (`<app name>.json`) di un progetto di gruppo di risorse Azure.|
  	|$TemplateParametersFile|Il percorso del file di parametri (`<app name>.parameters.json`) di un progetto di gruppo di risorse Azure.|
  	|$ArtifactStagingDirectory|Il percorso nel sistema di elementi in cui sono localmente caricati, inclusi cartella radice dello script di PowerShell. Questo percorso può essere assoluti o relativi al percorso script.|
  	|$AzCopyPath|Il percorso in cui lo strumento AzCopy.exe copia i file con estensione zip, compresa la cartella radice script di PowerShell. Questo percorso può essere assoluti o relativi al percorso script.|
  	|$DSCSourceFolder|Il percorso della cartella di origine DSC (bene accolta stato configurazione), compresa la cartella radice script di PowerShell. Questo percorso può essere assoluti o relativi al percorso script. Vedere [Introduzione all'estensione di Azure PowerShell DSC (bene accolta stato configurazione)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), se applicabile, per altre informazioni.|

1.  Verificare se gli elementi devono essere caricate in Azure. In caso contrario, andare al passaggio 11. In caso contrario, procedere come segue.

1.  Convertire tutte le variabili con percorsi relativi in percorsi assoluti. Ad esempio, cambiare un percorso ad esempio `..\Tools\AzCopy.exe` a `C:\YourFolder\Tools\AzCopy.exe`. Inoltre, inizializzare le variabili *ArtifactsLocationName* e *ArtifactsLocationSasTokenName* su null. *ArtifactsLocation* e *SaSToken* possono essere parametri per il modello. Se i valori null dopo la lettura del file di parametri, lo script genera valori per loro.

    Gli strumenti di Azure utilizzare il parametro valori *_artifactsLocation* e *_artifactsLocationSasToken* nel modello per gestire gli elementi. Se lo script di PowerShell trova parametri con i nomi, ma non vengono forniti i valori dei parametri, lo script carica gli elementi e restituisce i valori appropriati per i parametri. Passa quindi loro cmdlet tramite `@OptionsParameters`.

  	|Variabile|Descrizione|
  	|---|---|
  	|ArtifactsLocationName|Il percorso in cui si trovano gli elementi di Azure.|
  	|ArtifactsLocationSasTokenName|Nome token SA (condiviso accesso firma) utilizzato per lo script per autenticazione Bus di servizio. Per ulteriori informazioni, vedere [Autenticazione tramite firma accesso condiviso con Bus di servizio](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Questa sezione che controlla il <app name>. parameters.json file (denominato "file di parametri") è un nodo padre **parametri** denominato (nel `else` blocco). In caso contrario, non ha alcun nodo padre. Entrambi i formati sono accettabili.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Scorrere l'insieme di parametri JSON. Se un valore di parametro è stato assegnato a *_artifactsLocation* o *_artifactsLocationSasToken*, quindi impostare la variabile *$OptionalParameters* con tali valori. Impedisce dello script di sovrascrivere accidentalmente i valori dei parametri specificati.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  È possibile ottenere la chiave account lo spazio di archiviazione e il contesto per la risorsa di account di archiviazione utilizzata per contenere gli elementi per la distribuzione.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Se si usa DSC PowerShell per configurare una macchina virtuale, l'estensione DSC richiede gli elementi in un singolo file zip. Pertanto, creare un file di archivio ZIP per la configurazione di DSC. A tale scopo, verificare se è presente $DSCSourceFolder. Se è presente una configurazione DSC, rimuoverlo e quindi si crea un nuovo file compresso denominato dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Se il file di parametri non previsto alcun percorso per gli elementi di Azure, impostare un percorso per lo script di PowerShell da utilizzare durante il caricamento di elementi. A tale scopo, creare un percorso utilizzando una combinazione di percorso endpoint dell'account di archiviazione e il nome del contenitore di spazio di archiviazione. Aggiornare il file di parametri con il nuovo percorso.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Utilizzare l'utilità di **AzCopy** (incluso nella cartella **Strumenti** del progetto di distribuzione gruppo di risorse Azure) per copiare tutti i file dal percorso di rilascio di spazio di archiviazione locale nel proprio account Azure lo spazio di archiviazione online. Se questo passaggio non riesce, chiudere lo script in quanto la distribuzione è possibile che non venga eseguita correttamente senza gli elementi necessari.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Se non è specificato un token di SA per la posizione degli elementi nel file di parametri, crearne uno per fornire accesso in sola lettura temporaneo al contenitore di spazio di archiviazione online. Quindi, passare tale token SA alla riga di comando come "optionalParameter". Si noti che tutti i parametri passati nella riga di comando avranno la precedenza sui valori forniti nel file di parametri.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Creare il gruppo di risorse se non è già disponibile e archiviare il file di modello e parametri per gli errori di convalida che impedisce la distribuzione dai successivi.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Infine, distribuire il modello. Questo codice crea un nome univoco per la distribuzione utilizzando un timestamp.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Distribuire il gruppo di risorse

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Distribuire il gruppo di risorse in Visual Studio

1. Dal menu di scelta rapida del progetto di gruppo di risorse di Azure, scegliere **Distribuisci** > **Nuova distribuzione**.

    ![][0]

1. Nella finestra di dialogo **Distribuisci al gruppo di risorse** di scegliere un gruppo di risorse esistente nella casella di riepilogo a discesa per distribuire oppure scegliere ** &lt;creare una nuova... &gt;** Per creare un nuovo gruppo di risorse.

    ![][1]

1. Se richiesto, immettere un nome gruppo di risorse e un percorso nella finestra di dialogo **Crea gruppo di risorse** e quindi fare clic sul pulsante **Crea** .

    ![][2]

1. Fare clic sul pulsante **Modifica parametri** per visualizzare la finestra di dialogo **Modifica parametri** e quindi immettere i valori dei parametri mancante.

    ![][3]

    >[AZURE.NOTE] Se tutti i parametri richiesti occorrono valori, questa finestra di dialogo viene visualizzata automaticamente quando si distribuisce.

    ![][4]

1. Dopo aver completato immettere i valori dei parametri, fare clic sul pulsante **Salva** e quindi fare clic su **Distribuisci** .

    Viene eseguito lo script di distribuzione (Distribuisci AzureResourceGroup.ps1) e lo distribuisce il modello, insieme a tutti gli elementi in Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Distribuire il gruppo di risorse tramite PowerShell

Se si desidera eseguire lo script senza utilizzare il comando Pubblica Visual Studio e l'interfaccia utente, nel menu di scelta rapida per lo script, scegliere **Apri con PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Esempi di distribuzione di comando

### <a name="deploy-using-default-values"></a>Distribuire utilizzando i valori predefiniti

In questo esempio viene illustrato come eseguire script utilizzando i valori di parametro predefiniti. (Perché il parametro di posizione non dispone di un valore predefinito, è necessario specificare una.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Distribuire override i valori predefiniti

In questo esempio viene illustrato come eseguire lo script per distribuire i file di modello e parametri diversi da quelli predefiniti.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Distribuire mediante UploadArtifacts per la gestione temporanea

In questo esempio viene illustrato come eseguire lo script per caricare gli elementi dalla cartella release e distribuire i modelli non predefinito.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

In questo esempio viene illustrato come eseguire lo script in un'attività di Azure PowerShell in Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla gestione di risorse di Azure leggendo [Panoramica di gestione di risorse Azure](azure-resource-manager/resource-group-overview.md).

Per altri esempi di utilizzo dei progetti di gruppo di risorse di Azure, vedere [Distribuisci e gestire le risorse Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) da [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connetti [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Per altre guide rapide dal demo HealthClinic.biz, vedere [Guide rapide strumenti di sviluppo di Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
