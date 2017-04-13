<properties
   pageTitle="Distribuire le risorse con PowerShell e modello | Microsoft Azure"
   description="Utilizzare Gestione risorse di Azure e Azure PowerShell per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Manager delle risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuire le risorse con i modelli di Manager delle risorse e Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portale](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

In questo argomento viene illustrato come usare PowerShell Azure con i modelli di Manager delle risorse per distribuire le risorse in Azure.  

> [AZURE.TIP] Per assistenza con il debug di un messaggio di errore durante l'installazione, vedere:
>
> - [Visualizza le operazioni di distribuzione con PowerShell Azure](resource-manager-troubleshoot-deployments-powershell.md) per informazioni su come ottenere informazioni che consente di risolvere l'errore
> - [Risoluzione dei problemi di errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md) per informazioni su come risolvere gli errori comuni di distribuzione

Il modello può essere un file locale o un file esterno è disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firme (SA) accesso condiviso durante la distribuzione.

## <a name="quick-steps-to-deployment"></a>Azioni rapide per la distribuzione

In questo articolo vengono illustrati tutte le diverse opzioni disponibili per l'utente durante la distribuzione. Tuttavia, spesso è necessario solo due semplici comandi. Per iniziare rapidamente con la distribuzione, utilizzare i comandi seguenti:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Per ulteriori informazioni sulle opzioni disponibili per la distribuzione che potrebbe essere più adatto al proprio scenario, continuare a leggere questo articolo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

1. Accedere al proprio account Azure.

        Add-AzureRmAccount

     Viene restituito un riepilogo dell'account.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Se si hanno più abbonamenti, fornire l'ID di abbonamento che si desidera utilizzare per la distribuzione con il comando **Set AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. In genere, quando si distribuisce un nuovo modello, si desidera creare un gruppo di risorse per contenere le risorse. Se si dispone di un gruppo di risorse esistente che si desidera distribuire, è possibile ignorare questo passaggio e utilizzare tale gruppo di risorse. 

     Per creare un gruppo di risorse, specificare un nome e un percorso per il gruppo di risorse. È necessario specificare un percorso per il gruppo di risorse perché il gruppo di risorse vengono archiviati i metadati le risorse. Per motivi di conformità, può essere necessario specificare in cui sono archiviati i metadati. In generale, è consigliabile specificare un percorso in cui verrà salvato la maggior parte delle risorse. Nella stessa posizione consente di semplificare il modello.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Viene restituito un riepilogo di nuovo gruppo di risorse.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Prima di eseguire la distribuzione, è possibile convalidare le impostazioni di distribuzione. Il cmdlet **Test AzureRmResourceGroupDeployment** consente di trovare i problemi prima di creare risorse effettive. Nell'esempio seguente viene illustrato come convalidare una distribuzione.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Per distribuire risorse al gruppo di risorse, eseguire il comando **Nuovo AzureRmResourceGroupDeployment** e fornire i parametri necessari. I parametri includono un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello che è stato creato e altri parametri necessari per lo scenario. Se il parametro **Mode** non viene specificato, viene utilizzato il valore predefinito **incrementale** . Per eseguire una distribuzione completa, impostare **la modalità** su **completata**. Prestare attenzione quando si usa la modalità di completamento come è possibile eliminare inavvertitamente risorse che non sono presenti nel modello scelto.

     Per distribuire un modello locale, utilizzare il parametro **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Per distribuire un modello di esterno, utilizzare il parametro **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Sono disponibili le opzioni seguenti per fornire i valori dei parametri: 
   
     1. Utilizzare parametri all'interno del testo.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utilizzare un oggetto parametro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Utilizzare un file di parametro locale. Per informazioni sul file di modello, vedere [file dei parametri](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Utilizzare un file di parametro esterni. Per informazioni sul file di modello, vedere [file dei parametri](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Quando si utilizza un file di parametro esterni, non è possibile passare altri valori inline o da un file locale. Per ulteriori informazioni, vedere [la precedenza parametro](#parameter-precendence).

     Dopo aver distribuite le risorse, verrà visualizzato un riepilogo della distribuzione.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando PowerShell, viene chiesto di specificare un valore per il parametro. Il parametro in base al modello includerà suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** i conflitti di modello con il parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Viene chiesto di specificare un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione assegnando non parametri con lo stesso nome parametri utilizzati per le operazioni di distribuzione.

6. Se si desidera registrare ulteriori informazioni sulla distribuzione che consigliabile di risolvere gli eventuali errori di distribuzione, utilizzare il parametro **DeploymentDebugLogLevel** . È possibile specificare che richiesta contenuto, del contenuto delle risposte o entrambe aver effettuato con l'operazione di distribuzione.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Per ulteriori informazioni sull'utilizzo di contenuto debug per risolvere i problemi di distribuzioni, vedere [risoluzione dei problemi delle risorse gruppo con PowerShell Azure](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Distribuire modello dallo spazio di archiviazione del token SA

È possibile aggiungere i modelli per un account di archiviazione e collegarlo a tali durante la distribuzione con un token di SA.

> [AZURE.IMPORTANT] Seguendo la procedura seguente, blob contenente il modello è accessibile a solo il proprietario dell'account. Tuttavia, quando si crea un token di associazioni di protezione per il blob, il blob è accessibile a tutti gli utenti con tale URI. Se un altro utente intercetta URI, tale utente è in grado di accedere al modello. Utilizzando un token sa è un'ottima soluzione di limitazione dell'accesso ai modelli, ma non includere dati sensibili come password direttamente nel modello.

### <a name="add-private-template-to-storage-account"></a>Aggiungere modello privato all'account di archiviazione

Configurare un account di archiviazione per i modelli nel modo seguente:

1. Creare un gruppo di risorse.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Creare un account di archiviazione. Il nome dell'account di archiviazione deve essere univoco in Azure, pertanto fornire il proprio nome per l'account.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Impostare l'account di archiviazione corrente.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Creare un contenitore. L'autorizzazione è impostato su **disattivato** che indica che il contenitore, accessibile solo al proprietario.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Aggiungere il modello al contenitore.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornire token SA durante la distribuzione

Per distribuire un modello privato in un account di archiviazione, recuperare un token SA e includere nell'URI per il modello.

1. Se è stata modificata l'account di archiviazione corrente, impostare l'account di archiviazione corrente a quella che contiene i modelli.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Creare un token di sa con autorizzazioni di lettura e un'ora di scadenza per limitare l'accesso. Recuperare l'URI completo del modello di compresi token SA.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Distribuire il modello fornendo URI che include il token SA.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Per un esempio dell'uso di un token sa con i modelli collegati, vedere [uso dei modelli collegati Gestione risorse Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Ordine di precedenza parametro

È possibile utilizzare parametri all'interno del testo e un file di parametri locale per la stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori all'interno del testo durante la distribuzione. Se fornire i valori per un parametro nel file di parametro locale sia all'interno del testo, il valore all'interno del testo avrà la precedenza.

Tuttavia, è possibile utilizzare i parametri in linea con un file di parametro esterni. Quando si specifica un file di parametri nel parametro **TemplateParameterUri** , vengono ignorati tutti i parametri all'interno del testo. È necessario specificare tutti i valori di parametro nel file esterno. Se il modello include un valore riservato che non è possibile includere nel file di parametri, aggiungere tale valore in un archivio chiave e fanno riferimento a archivio chiave nel file di parametro esterni o fornire in modo dinamico tutti in linea di valori di parametro.

Per informazioni dettagliate sull'utilizzo di un riferimento KeyVault per passare valori sicuri, vedere [passare protetti valori durante la distribuzione](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Passaggi successivi
- Per un esempio di distribuzione delle risorse tramite la raccolta di client .NET, vedere [risorse di distribuzione mediante un modello e raccolte di .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Per definire i parametri nel modello, vedere [creazione di modelli](resource-group-authoring-templates.md#parameters).
- Per indicazioni su come distribuire una soluzione in ambienti diversi, vedere [ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).

