<properties
   pageTitle="Distribuire le risorse con Azure CLI e modello | Microsoft Azure"
   description="Utilizzare Gestione risorse di Azure e Azure CLI per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Manager delle risorse."
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Manager delle risorse e CLI Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portale](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

In questo argomento viene illustrato come utilizzare CLI Azure con i modelli di Manager delle risorse per distribuire le risorse in Azure.  

> [AZURE.TIP] Per assistenza con il debug di un messaggio di errore durante l'installazione, vedere:
>
> - [Visualizza le operazioni di distribuzione con Azure CLI](resource-manager-troubleshoot-deployments-cli.md) per informazioni su come ottenere informazioni che consente di risolvere l'errore
> - [Risoluzione dei problemi di errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md) per informazioni su come risolvere gli errori comuni di distribuzione

Il modello può essere un file locale o un file esterno è disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firme (SA) accesso condiviso durante la distribuzione.

## <a name="quick-steps-to-deployment"></a>Azioni rapide per la distribuzione

In questo articolo vengono illustrati tutte le diverse opzioni disponibili per l'utente durante la distribuzione. Tuttavia, spesso è necessario solo due semplici comandi. Per iniziare rapidamente con la distribuzione, utilizzare i comandi seguenti:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Per ulteriori informazioni sulle opzioni disponibili per la distribuzione che potrebbe essere più adatto al proprio scenario, continuare a leggere questo articolo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Distribuire con CLI Azure

Se non è stato già utilizzato CLI Azure con Gestione risorse, vedere [uso CLI Azure per Mac, Linux e Windows Azure gestione delle risorse](xplat-cli-azure-resource-manager.md).

1. Accedere al proprio account Azure. Dopo avere fornito le credenziali, il comando restituisce il risultato di accesso dell'utente.

        azure login
  
        ...
        info:    login command OK

2. Se si hanno più abbonamenti, fornire l'id di abbonamento che si desidera utilizzare per la distribuzione.

        azure account set <YourSubscriptionNameOrId>

3. Passare al modulo di gestione di risorse Azure. Si riceve la conferma della nuova modalità.

        azure config mode arm
   
        info:     New mode is arm

4. Se non si dispone di un gruppo di risorse esistente, creare un gruppo di risorse. Specificare il nome del gruppo di risorse e percorso in cui è necessario per la soluzione. È necessario specificare un percorso per il gruppo di risorse perché il gruppo di risorse vengono archiviati i metadati le risorse. Per motivi di conformità, può essere necessario specificare in cui sono archiviati i metadati. In generale, è consigliabile specificare un percorso in cui verrà salvato la maggior parte delle risorse. Nella stessa posizione consente di semplificare il modello.

        azure group create -n ExampleResourceGroup -l "West US"

     Viene restituito un riepilogo di nuovo gruppo di risorse.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Convalidare la distribuzione prima di eseguire eseguendo il comando **convalida modello gruppo azure** . Durante la verifica della distribuzione, fornire parametri esattamente come quando si esegue la distribuzione (illustrata nella sezione successiva).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Per distribuire risorse al gruppo di risorse, eseguire il comando seguente e fornire i parametri necessari. I parametri includono un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello e gli altri parametri necessari per lo scenario. 
   
     Sono i seguenti tre opzioni per fornire i valori dei parametri: 

     1. Utilizzare parametri all'interno del testo e un modello locale. Ogni parametro è nel formato: `"ParameterName": { "value": "ParameterValue" }`. Nell'esempio seguente mostra i parametri con caratteri di escape.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Utilizzare parametri all'interno del testo e un collegamento a un modello.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Usare un file di parametri. Per informazioni sul file di modello, vedere [file dei parametri](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Dopo aver distribuite le risorse tramite uno dei tre metodi precedenti, verrà visualizzato un riepilogo della distribuzione.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Per eseguire una distribuzione completa, impostare **la modalità** su **completata**. Prestare attenzione quando si utilizza questa modalità come è possibile eliminare inavvertitamente risorse che non sono presenti nel modello scelto.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Se si desidera registrare ulteriori informazioni sulla distribuzione che consigliabile di risolvere gli eventuali errori di distribuzione, utilizzare il parametro di **impostazione di debug** . È possibile specificare che richiesta contenuto, del contenuto delle risposte o entrambe aver effettuato con l'operazione di distribuzione.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Distribuire modello dallo spazio di archiviazione del token SA

È possibile aggiungere i modelli per un account di archiviazione e collegarlo a tali durante la distribuzione con un token di SA.

> [AZURE.IMPORTANT] Seguendo la procedura seguente, blob contenente il modello è accessibile a solo il proprietario dell'account. Tuttavia, quando si crea un token di associazioni di protezione per il blob, il blob è accessibile a tutti gli utenti con tale URI. Se un altro utente intercetta URI, tale utente è in grado di accedere al modello. Utilizzando un token sa è un'ottima soluzione di limitazione dell'accesso ai modelli, ma non includere dati sensibili come password direttamente nel modello.

### <a name="add-private-template-to-storage-account"></a>Aggiungere modello privato all'account di archiviazione

Configurare un account di archiviazione per i modelli nel modo seguente:

1. Creare un gruppo di risorse.

        azure group create -n "ManageGroup" -l "westus"

2. Creare un account di archiviazione. Il nome dell'account di archiviazione deve essere univoco in Azure, pertanto fornire il proprio nome per l'account.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Impostare le variabili per l'account di archiviazione e la chiave.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Creare un contenitore. L'autorizzazione è impostato su **disattivato** che indica che il contenitore, accessibile solo al proprietario.

        azure storage container create --container templates -p Off 
        
4. Aggiungere il modello al contenitore.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornire token SA durante la distribuzione

Per distribuire un modello privato in un account di archiviazione, recuperare un token SA e includere nell'URI per il modello.

1. Creare un token di sa con autorizzazioni di lettura e un'ora di scadenza per limitare l'accesso. Impostare l'ora di scadenza per consentire abbastanza tempo completare la distribuzione. Recuperare l'URI completo del modello di compresi token SA.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Distribuire il modello fornendo URI che include il token SA.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Per un esempio dell'uso di un token sa con i modelli collegati, vedere [uso dei modelli collegati Gestione risorse Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Passaggi successivi
- Per un esempio di distribuzione delle risorse tramite la raccolta di client .NET, vedere [risorse di distribuzione mediante un modello e raccolte di .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Per definire i parametri nel modello, vedere [creazione di modelli](resource-group-authoring-templates.md#parameters).
- Per indicazioni su come distribuire una soluzione in ambienti diversi, vedere [ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
- Per informazioni dettagliate sull'utilizzo di un riferimento KeyVault per passare valori sicuri, vedere [passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).

