<properties 
    pageTitle="Gestire le ricerche di Azure con gli script di Powershell | Microsoft Azure | Servizio di ricerca cloud ospitato" 
    description="Gestire il servizio di ricerca di Azure con gli script di PowerShell. Creare o aggiornare un servizio di ricerca di Azure e la gestione delle chiavi amministrazione ricerca Azure" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Gestire il servizio di ricerca di Azure con PowerShell
> [AZURE.SELECTOR]
- [Portale](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

In questo argomento vengono descritti i comandi di PowerShell per eseguire molte delle attività di gestione per i servizi di ricerca di Azure. Esamineremo la creazione di un servizio di ricerca, modificarlo in scala e la gestione delle chiavi l'API.
Questi comandi parallela le opzioni di gestione disponibili nell' [API REST di Azure ricerca gestione](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Prerequisiti
 
- È necessario disporre Azure PowerShell 1.0 o versione successiva. Per istruzioni, vedere [installare e configurare Azure PowerShell](../powershell-install-configure.md).
- È necessario accedere al proprio abbonamento Azure PowerShell come descritto di seguito.

Prima di tutto, è necessario accedere a Azure con questo comando:

    Login-AzureRmAccount

Specificare l'indirizzo di posta elettronica del proprio account Azure e la relativa password nella finestra di dialogo di accesso di Microsoft Azure.

In alternativa, è possibile [eseguire l'accesso non interattivo con un servizio principale](../resource-group-authenticate-service-principal.md).

Se si hanno più abbonamenti Azure, è necessario impostare l'abbonamento Azure. Per visualizzare un elenco delle sottoscrizioni corrente, eseguire il comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente è il nome dell'abbonamento `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandi che consentono di iniziare

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Passaggi successivi
    
Una volta creato il servizio, è possibile eseguire i passaggi successivi: creare un [indice](search-what-is-an-index.md), [query un indice analitico](search-query-overview.md), infine creare e gestire il proprio applicazione di ricerca che usa la ricerca di Azure.

- [Creare un indice di ricerca di Azure nel portale di Azure](search-create-index-portal.md)

- [Query di un indice di ricerca Azure mediante Esplora ricerche nel portale di Azure](search-explorer.md)

- [Configurazione indicizzatore per caricare i dati da altri servizi](search-indexer-overview.md)

- [Come usare la ricerca di Azure in .NET](search-howto-dotnet-sdk.md)

- [Analizzare il traffico di ricerca di Azure](search-traffic-analytics.md)
