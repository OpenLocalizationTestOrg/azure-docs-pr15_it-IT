<properties 
    pageTitle="Azure PowerShell Gestione risorse | Microsoft Azure" 
    description="Introduzione all'utilizzo di Azure PowerShell per distribuire più risorse come un gruppo di risorse in Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>Utilizzo di PowerShell Azure Azure Gestione risorse

> [AZURE.SELECTOR]
- [Portale](azure-portal/resource-group-portal.md) 
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


Gestione risorse di Azure implementato un approccio moderno al controllo del ciclo di vita di Azure risorse. Invece di creare e gestire singole risorse, è necessario innanzitutto possibile immaginare un'intera soluzione, ad esempio un blog, una raccolta foto, un portale di SharePoint o una pagina wiki. Utilizzare un modello, ossia una rappresentazione dichiarativo della soluzione, ossia per definire un gruppo di risorse contenente tutte le risorse che necessarie per supportare la soluzione. Quindi, distribuire e gestire il gruppo di risorse come unità logica. 

In questa esercitazione si imparerà a usare PowerShell Azure Gestione risorse Azure. Illustra il processo di distribuzione di una soluzione e uso di tale soluzione. Utilizzare PowerShell Azure e un modello di Manager delle risorse per la distribuzione:

- SQL server - per ospitare il database
- Database SQL - di memorizzazione dei dati
- Regole firewall - per consentire l'applicazione web per la connessione al database
- Piano di servizio App - per definire la funzionalità e il costo di web app
- Sito Web - per eseguire l'applicazione web
- Configurazione di Web - per archiviare la stringa di connessione al database 
- Regole di avviso - per il monitoraggio delle prestazioni ed errori
- Informazioni dettagliate sui App - per le impostazioni di ridimensionamento automatico

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Un account Azure
  + È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): viene visualizzato crediti è possibile utilizzare per provare a pagamento servizi Azure e anche se vengono utilizzati massimo è possibile mantenere l'account e usare gratuiti servizi Azure, ad esempio siti Web. Carta di credito non verranno mai addebitata, a meno che non in modo esplicito modificare le impostazioni e chiedere di essere addebitati.
  
  + È possibile [attivare vantaggi sottoscrittori MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): abbonamento MSDN Your offre crediti ogni mese che è possibile utilizzare per i servizi di Azure a pagamento.
- Azure PowerShell 1.0. Per informazioni su questa versione e come installarlo, vedere [come installare e configurare Azure PowerShell](powershell-install-configure.md).

In questa esercitazione è progettata per gli utenti inesperti PowerShell, ma si presuppone che comprendere i concetti di base, ad esempio moduli, i cmdlet e sessioni.

## <a name="get-help-for-cmdlets"></a>È possibile ottenere assistenza per i cmdlet

Per ottenere informazioni dettagliate per tutti i cmdlet che viene visualizzato in questa esercitazione, utilizzare il cmdlet Get-Help. 

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per ottenere assistenza per il cmdlet Get-AzureRmResource, digitare:

    Get-Help Get-AzureRmResource -Detailed

Per ottenere un elenco dei cmdlet del modulo di risorse con un riepilogo della Guida, digitare: 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

L'output sarà simile a porzione riportata di seguito:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Per visualizzare la Guida completa per un cmdlet, digitare un comando con il formato:

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Accedere al proprio account Azure

Per eseguire la soluzione, è necessario accedere al proprio account.

Per accedere al proprio account Azure, utilizzare il cmdlet **AzureRmAccount Aggiungi** .

    Add-AzureRmAccount

Il cmdlet richiede le credenziali di accesso per il proprio account Azure. Dopo avere effettuato l'accesso, il download delle impostazioni dell'account in modo che siano disponibili per Azure PowerShell. 

Le impostazioni dell'account scadono, pertanto è necessario aggiornarli occasionalmente. Per aggiornare le impostazioni dell'account, eseguire di nuovo **Aggiungi AzureRmAccount** . 

>[AZURE.NOTE] I moduli di gestione risorse è necessario aggiungere AzureRmAccount. Non è sufficiente un file di Impostazioni pubblicazione.     

Se si dispone di più di una sottoscrizione, fornire l'id di abbonamento che si desidera utilizzare per la distribuzione con il cmdlet **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di distribuire le risorse per l'abbonamento, è necessario creare un gruppo di risorse contenenti le risorse. 

Per creare un gruppo di risorse, utilizzare il cmdlet **New-AzureRmResourceGroup** .

Il comando il parametro **nome** per specificare un nome per il gruppo di risorse e il parametro di **posizione** specificare la posizione. In base a abbiamo scoperto nella sezione precedente, verrà utilizzato "US Ovest" relativa al percorso.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
L'output sarà simile a:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Il gruppo di risorse è stato creato correttamente.

## <a name="deploy-your-solution"></a>Distribuire la soluzione

In questo argomento non viene illustrato come creare un modello o discutere la struttura del modello. Per ulteriori informazioni, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md) e [Procedura dettagliata modello Manager delle risorse](resource-manager-template-walkthrough.md). Distribuire il modello di [provisioning un'App Web con un Database SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) predefinito dai [Modelli Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/).

Si dispone del gruppo di risorse e disporre il modello in modo che ora si è pronti distribuire l'infrastruttura definito nel modello scelto per il gruppo di risorse. Distribuire le risorse con i cmdlet **New-AzureRmResourceGroupDeployment** . Il modello specifica molti valori predefiniti che verrà usata in modo che non è necessario fornire i valori per i parametri. La sintassi di base è simile alla:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Specificare il gruppo di risorse e il percorso del modello. Se il modello è un file locale, si utilizza il parametro **- TemplateFile** e specifica il percorso del modello. È possibile impostare il **-modalità** parametro **incrementale** o **completo**. Per impostazione predefinita, Gestione risorse esegue un aggiornamento incrementale durante la distribuzione. non è pertanto essenziale impostare **-modalità** quando si desidera **incrementale**. Per comprendere le differenze tra le modalità di distribuzione, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Parametri di modello dinamico

Se si ha familiarità con PowerShell, è possibile sapere che è possibile scorrere i parametri disponibili per un cmdlet digitando un segno meno (-) e quindi premendo il tasto TAB. La stessa funzionalità funziona anche con i parametri definiti nel modello scelto. Non appena si digita il nome del modello, il cmdlet recupera il modello, analizza e i parametri del modello viene aggiunta al comando in modo dinamico. In questo modo molto semplice specificare i valori dei parametri di modello.

Quando si immette il comando, viene richiesto per il parametro obbligatorio manca, **administratorLoginPassword**. E, quando si digita la password, il valore di stringa protetta è nascosto. Questa strategia Elimina il rischio di fornire una password in formato testo normale.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se il modello include un parametro con un nome che corrisponde a uno dei parametri di comando per distribuire il modello (ad esempio, incluso un parametro denominato **ResourceGroupName** nel modello scelto che corrisponde al parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) ), viene chiesto di specificare un valore per un parametro con il suffisso **FromTemplate** (ad esempio **ResourceGroupNameFromTemplate**). In generale, è consigliabile evitare questa confusione assegnando non parametri con lo stesso nome parametri utilizzati per le operazioni di distribuzione.

Il comando viene eseguito e restituisce i messaggi come le risorse vengono create. Infine, verrà visualizzato il risultato della distribuzione.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

In pochi passaggi, abbiamo creato e distribuito le risorse necessarie per un sito Web complesse. 

### <a name="log-debug-information"></a>Informazioni di debug log

Quando si distribuisce un modello, è possibile registrare informazioni aggiuntive sulla richiesta e risposta specificando il parametro **- DeploymentDebugLogLevel** durante l'esecuzione di **AzureRmResourceGroupDeployment di nuovo**. Queste informazioni consentono di risolvere gli errori di distribuzione. Il valore predefinito è **Nessuno** ovvero alcuna richiesta o contenuto della risposta ha eseguito l'accesso. È possibile specificare l'accesso al contenuto da richiesta, risposta o entrambe.  Per ulteriori informazioni sulla risoluzione dei problemi distribuzioni e registrare le informazioni di debug, vedere [risoluzione dei problemi delle risorse gruppo con PowerShell Azure](resource-manager-troubleshoot-deployments-powershell.md). Nell'esempio seguente registra il contenuto di richiesta e il contenuto delle risposte per la distribuzione.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Quando si imposta il parametro DeploymentDebugLogLevel, valutare attentamente il tipo di informazioni che si sta passando durante la distribuzione. Per la registrazione di informazioni sulla richiesta o risposta è possibile esporre potenzialmente riservate i dati recuperati tramite le operazioni di distribuzione. 


## <a name="get-information-about-your-resource-groups"></a>Ottenere informazioni sui gruppi di risorse

Dopo aver creato un gruppo di risorse, è possibile utilizzare i cmdlet nel modulo Manager delle risorse per la gestione dei gruppi di risorse.

- Per ottenere un gruppo di risorse nell'abbonamento, utilizzare il cmdlet **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Che restituisce le informazioni seguenti:

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Se non si specifica il nome di un gruppo di risorse, il cmdlet restituisce tutti i gruppi di risorse nell'abbonamento.

- Per ottenere le risorse nel gruppo di risorse, utilizzare il cmdlet **Trova AzureRmResource** e il parametro **ResourceGroupNameContains** . Senza parametri, trova AzureRmResource Ottiene tutte le risorse nell'abbonamento Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Che restituisce un elenco di risorse formattato come:
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- È possibile utilizzare i contrassegni per organizzare le risorse nell'abbonamento in modo logico e recuperare le risorse con i cmdlet **AzureRmResource trova** e **Trova AzureRmResourceGroup** .

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Aggiungere a un gruppo di risorse

Per aggiungere una risorsa al gruppo di risorse, è possibile utilizzare il cmdlet **New-AzureRmResource** . Tuttavia, aggiunge una risorsa in questo modo potrebbe causare confusione futura perché la nuova risorsa non esiste nel modello scelto. Se è stata distribuita nuovamente il modello precedente, distribuire una soluzione incompleta. Se si distribuisce spesso, si troverà più semplice e affidabile per aggiungere la nuova risorsa al modello e distribuirla nuovamente.

## <a name="move-a-resource"></a>Spostare una risorsa

È possibile spostare risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Le risorse spostare nuovo gruppo di risorse o l'abbonamento](resource-group-move-resources.md).

## <a name="export-template"></a>Modello di esportazione

Per un gruppo di risorse esistente (distribuito tramite PowerShell o uno degli altri metodi, ad esempio il portale), è possibile visualizzare il modello di Manager delle risorse per il gruppo di risorse. Esportazione del modello offre due vantaggi:

1. È possibile automatizzare facilmente distribuzioni future della soluzione perché tutti dell'infrastruttura è definita nel modello.

2. È possibile acquisire familiarità con la sintassi dei modelli riconoscibili in JavaScript Object Notation (JSON) che rappresenta la soluzione.

Tramite PowerShell, è possibile generare un modello che rappresenta lo stato corrente del gruppo di risorse o recuperare il modello è stato usato per una distribuzione particolare.

Esportazione del modello per un gruppo di risorse è utile quando si sono apportate modifiche a un gruppo di risorse ed è necessario recuperare la rappresentazione JSON dello stato corrente. Tuttavia, il modello generato contiene solo un numero minimo di parametri e non le variabili. La maggior parte dei valori nel modello siano hardcoded. Prima di distribuire il modello generato, si desidera convertire più valori nei parametri in modo che è possibile personalizzare la distribuzione per ambienti diversi.

Esportazione del modello per una distribuzione particolare è utile quando si desidera visualizzare il modello effettivo utilizzato per la distribuzione di risorse. Il modello includerà tutti i parametri e variabili definite per la distribuzione originale. Tuttavia, se un utente dell'organizzazione ha apportato modifiche al gruppo di risorse all'esterno di definizione del modello, questo modello non rappresenta lo stato corrente del gruppo di risorse.

> [AZURE.NOTE] La caratteristica di modello di esportazione è in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si tenta di esportare un modello, venga visualizzato un messaggio di errore indicante che alcune risorse non sono stati esportati. Se necessario, è possibile definire manualmente queste risorse nel modello scelto dopo il download di esso.

###<a name="export-template-from-resource-group"></a>Esporta il modello dal gruppo di risorse

Per visualizzare il modello per un gruppo di risorse, eseguire il cmdlet **AzureRmResourceGroup di esportazione** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Scaricare modello da una distribuzione

Per scaricare il modello usato per una distribuzione specifica, eseguire il cmdlet **AzureRmResourceGroupDeploymentTemplate Salva** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Eliminare le risorse o un gruppo di risorse

- Per eliminare una risorsa dal gruppo di risorse, utilizzare il cmdlet **AzureRmResource Rimuovi** . Questo cmdlet consente di eliminare la risorsa, ma non eliminare il gruppo di risorse.

    Questo comando consente di rimuovere il sito Web TestSite dal gruppo di risorse TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Per eliminare un gruppo di risorse, utilizzare il cmdlet **AzureRmResourceGroup Rimuovi** . Questo cmdlet consente di eliminare il gruppo di risorse e le relative risorse.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    Viene chiesto di confermare l'eliminazione.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script di distribuzione

Esempi di distribuzione precedenti in questo argomento a cui sono visualizzati i singoli cmdlet necessarie per distribuire le risorse in Azure. Nell'esempio seguente mostra uno script di distribuzione che consente di creare il gruppo di risorse e lo distribuisce le risorse.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di modelli di Manager delle risorse, vedere [Creazione di modelli di Manager delle risorse Azure](./resource-group-authoring-templates.md).
- Per ulteriori informazioni sulla distribuzione di modelli, vedere [distribuire un'applicazione con il modello di gestione risorse Azure](./resource-group-template-deploy.md).
- Per un esempio dettagliato della distribuzione di un progetto, vedere [microservices Distribuisci prevedibili in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Per informazioni sulla risoluzione dei problemi di una distribuzione che non è riuscita, vedere [risoluzione dei problemi delle risorse gruppo in Azure](./resource-manager-troubleshoot-deployments-powershell.md).

