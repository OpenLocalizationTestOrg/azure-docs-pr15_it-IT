<properties
    pageTitle="Web App clonare tramite PowerShell"
    description="Informazioni su come duplicare le app Web di nuove app Web tramite PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Applicazione di servizio di App Azure clonare tramite PowerShell#

Con la versione di Microsoft Azure PowerShell versione 1.1.0 una nuova opzione è stato aggiunto di nuovo AzureRMWebApp che l'utente la possibilità di duplicare un'App Web esistente per un'app appena creata in un'area diversa o nella stessa regione. In questo modo i clienti distribuire un numero di App nelle diverse aree in modo semplice e rapido.

La duplicazione App è attualmente supportato solo per i piani di servizio premium livello app. La nuova caratteristica utilizza le stesse limitazioni come caratteristica di Backup di applicazioni Web, vedere [eseguire il backup un'app web nel servizio App Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Per informazioni sull'uso di gestione risorse di Azure in base a PowerShell Azure cmdlet per gestire le app Web controllare [i comandi di PowerShell per Azure Web App in base a Gestione risorse di Azure](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>La duplicazione di un'App esistente ##

Scenario: Un'app web esistente nell'area Sud Central US, l'utente desidera clonare il contenuto in una nuova app web nell'area North Central US. Questo risultato può essere ottenuto utilizzando la versione di gestione di risorse Azure del cmdlet PowerShell per creare una nuova app web con l'opzione - SourceWebApp.

Si conosce il nome del gruppo risorse che contiene l'app web di origine, è possibile utilizzare il seguente comando di PowerShell per ottenere informazioni dell'applicazione web origine (denominate in questo caso Web App origine):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Per creare un piano di servizio nuova App, possiamo usare il comando nuovo AzureRmAppServicePlan come illustrato nell'esempio seguente

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Il comando nuovo AzureRmWebApp che possiamo creare la nuova app web nell'area North Central US e collegare a un livello di premium esistente App servizio pianificare, inoltre è possibile utilizzare lo stesso gruppo di risorse con l'app web di origine o definire un nuovo gruppo di risorse, che illustra le operazioni seguenti:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Da duplicare un'app web esistente, inclusi tutti i relativi distribuzione slot, sarà necessario utilizzare il parametro IncludeSourceWebAppSlots l'utente, il comando PowerShell seguente viene illustrato come utilizzare il parametro con il comando nuovo AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Per duplicare un'app web esistente all'interno dell'area stessa, l'utente sarà necessario creare un nuovo gruppo di risorse e un nuovo servizio app pianificare nella stessa regione e quindi utilizzando il seguente comando di PowerShell per duplicare l'applicazione web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>La duplicazione di un'App esistente in un ambiente di servizio App ##

Scenario: Un'app web esistente nell'area Sud Central US, l'utente desidera clonare il contenuto in una nuova app web di ambiente un servizio di App esistente (base).

Si conosce il nome del gruppo risorse che contiene l'app web di origine, è possibile utilizzare il comando PowerShell seguente per ottenere informazioni dell'applicazione web origine (denominate in questo caso Web App origine):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Si conosce il nome del ASE e il nome del gruppo di risorse di base a cui appartiene, l'utente può usare il comando nuovo AzureRmWebApp per creare la nuova app web in ASE esistente, che illustra le operazioni seguenti:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Il parametro percorso è necessario per il motivo legacy, ma in caso di creazione di un'app in un ASE verranno ignorato. 

## <a name="cloning-an-existing-app-slot"></a>La duplicazione di un intervallo aperto App esistente ##

Scenario: L'utente desidera duplicare un intervallo esistente aperto App Web di in una nuova App Web o nuovo spazio Web App. La nuova App Web può essere nella stessa regione l'originale intervallo aperto Web App o in un'area diversa.

Si conosce il nome del gruppo risorse che contiene l'app web di origine, è possibile utilizzare il seguente comando di PowerShell per ottenere informazioni dell'origine web app intervallo aperto (denominate in questo caso origine webappslot) associate all'origine Online-Web App:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Di seguito viene illustrata la creazione di un duplicato di origine web app a una nuova app web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configurazione di gestione di traffico durante la duplicazione di un'App ##

Creazione di applicazioni web più aree e configurazione di gestione di traffico Azure per indirizzare il traffico verso tutte queste applicazioni web, è uno scenario importante n per assicurare la disponibilità App dei clienti, quando la duplicazione di un'app web esistente è necessario la possibilità di connettersi entrambe le applicazioni web a un nuovo profilo di gestione di traffico o uno esistente, si noti che solo i Manager delle risorse di Azure versione di traffico Manager è supportato.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Creare un nuovo profilo di gestore del traffico durante la duplicazione di un'App ###

Scenario: L'utente desidera duplicare un'app web a un'altra area geografica, durante la configurazione di un profilo di gestione del traffico Manager delle risorse di Azure che sono entrambe le applicazioni web. Di seguito viene illustrata la creazione di un duplicato dell'origine web app per un'app web nuovo durante la configurazione di un nuovo profilo di gestore del traffico:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Aggiunta di nuovi elementi duplicati Web App a un profilo di gestore del traffico esistente ###

Scenario: L'utente dispone già di un profilo di manager il traffico di gestione risorse Azure che si desidera aggiungere entrambi App web come endpoint. A tale scopo, è innanzitutto necessario assemblare l'id di profilo di manager il traffico esistente, è necessario l'id di abbonamento, nome gruppo di risorse e il nome del profilo manager il traffico esistente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Dopo averla l'id di traffico Manager, di seguito è illustrata la creazione di un duplicato di origine web app a una nuova app web durante l'aggiunta a un profilo di gestore del traffico esistente:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrizioni corrente ##

Questa funzionalità è attualmente in anteprima, stiamo lavorando per aggiungere nuove funzionalità nel tempo, l'elenco seguente sono associate le restrizioni note nella versione corrente di clonare app:

- Impostazioni di ridimensionamento automatico non sono duplicate
- Impostazioni di backup pianificazione non sono duplicate
- Impostazioni di VNET non sono duplicate
- Informazioni dettagliate sui App non sono automaticamente impostati App web di destinazione
- Facile Auth impostazioni non sono duplicate
- Estensione kudu non sono duplicati
- Suggerimento regole non sono duplicate
- Contenuto del database non sono duplicati


### <a name="references"></a>Riferimenti ###
- [Azure Manager delle risorse in base a comandi PowerShell per Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [La duplicazione App Web nel portale di Azure](app-service-web-app-cloning-portal.md)
- [Eseguire il backup un'app web nel servizio App Azure](web-sites-backup.md)
- [Azure supporto Manager delle risorse per l'anteprima di gestore del traffico Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introduzione all'ambiente di servizio di App](app-service-app-service-environment-intro.md)
- [Utilizzo di PowerShell Azure Azure Gestione risorse](../powershell-azure-resource-manager.md)
