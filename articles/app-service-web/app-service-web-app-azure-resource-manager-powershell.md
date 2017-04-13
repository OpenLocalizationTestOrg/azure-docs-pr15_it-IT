<properties
    pageTitle="Comandi di PowerShell Azure basate su Manager delle risorse per Azure Web App | Microsoft Azure"
    description="Informazioni su come utilizzare i comandi di PowerShell basate su Gestione risorse di Azure nuovi per gestire le app Web di Azure."
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
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Uso risorse Azure basate su gestione PowerShell per gestire applicazioni Web Azure#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Con Microsoft Azure PowerShell versione 1.0.0 nuovi comandi sono state aggiunte, fornire all'utente la possibilità di usare i comandi di PowerShell basate su Azure risorsa Manager per gestire applicazioni Web.

Per ulteriori informazioni sulla gestione dei gruppi di risorse, vedere [Uso Azure PowerShell Gestione risorse Azure](../powershell-azure-resource-manager.md). 

Per conoscere l'elenco completo dei parametri e opzioni per i cmdlet di PowerShell, vedere [Completo di riferimento sui Cmdlet di cmdlet di PowerShell basate su Gestione risorse di Azure App Web](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Gestione App servizio plan di messaggistica unificata ##

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio di App ###
Per creare un piano di servizio app, utilizzare il cmdlet **New-AzureRmAppServicePlan** .

Ecco le descrizioni dei parametri diversi:

-   **Nome**: nome del piano di servizio di app.
-   **Posizione**: percorso del piano di servizio.
-   **ResourceGroupName**: gruppo di risorse che include il piano di servizio app appena creato.
-   **Livello**: il livello di prezzo desiderato (il valore predefinito è gratuito, altre opzioni sono condivisi, Basic, Standard e Premium)
-   **WorkerSize**: le dimensioni del worker (impostazione predefinita è piccolo se il parametro di livello è stato specificato come Basic, Standard o Premium. Altre opzioni sono Medium e Large.)
-   **NumberofWorkers**: il numero di dipendenti nell'applicazione di servizio piano (valore predefinito è 1). 

Esempio di utilizzare questo cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Creare un piano di servizio App in un ambiente App ###
Per creare un piano di servizio app in un ambiente app, utilizzare lo stesso comando **Nuovo AzureRmAppServicePlan** comando con parametri aggiuntivi per specificare il nome del ASE e un nome gruppo di risorse del ASE.

Esempio di utilizzare questo cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Per ulteriori informazioni sull'ambiente servizio app, selezionare [Introduzione all'ambiente di servizio App](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Elenco esistente App servizio plan di messaggistica unificata ###

Per elencare i piani di servizio app esistente, utilizzare il cmdlet **Get-AzureRmAppServicePlan** .

Per elencare tutti i piani di servizio di app in abbonamento, usare: 

    Get-AzureRmAppServicePlan

Per elencare tutti i piani di servizio di app in un determinato gruppo di risorse, usare:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Per ottenere un piano di servizio app specifica, usare:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurare un piano di servizio App esistente ###

Per modificare le impostazioni per un piano di servizio app esistente, utilizzare il cmdlet **Set-AzureRmAppServicePlan** . È possibile modificare il livello, dimensioni di lavoro e il numero di dipendenti 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Ridimensionamento di un piano di servizio di App ####

Per ridimensionare un piano di servizio App esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Modificare la dimensione di lavoro di un piano di servizio App ####

Per modificare le dimensioni dei colleghi in un piano di servizio App esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Modificare il livello di un piano di servizio di App ####

Per modificare il livello di un piano di servizio App esistente, usare:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Eliminare un piano di servizio App esistente ###

Per eliminare un piano di servizio app esistente, tutte le app web assegnate necessario essere spostato o eliminato prima di tutto. Quindi utilizzare il cmdlet **Rimuovi AzureRmAppServicePlan** è possibile eliminare il piano di servizio app.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gestire le App servizio Web App ##

### <a name="create-a-web-app"></a>Creare un'App Web ###

Per creare un'app web, utilizzare il cmdlet **New-AzureRmWebApp** .

Ecco le descrizioni dei parametri diversi:

- **Nome**: nome per l'applicazione web.
- **AppServicePlan**: nome per il piano di servizio utilizzato per ospitare il web app.
- **ResourceGroupName**: gruppo di risorse contenente il piano di servizio App.
- **Posizione**: il percorso di app web.

Esempio di utilizzare questo cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Creare un'App Web in un ambiente App ###

Per creare un'app web in un ambiente di servizio App (base). Usare lo stesso comando **Nuovo AzureRmWebApp** con i parametri aggiuntivi per specificare il nome di base e il nome del gruppo di risorse di base a cui appartiene.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Per ulteriori informazioni sull'ambiente servizio app, selezionare [Introduzione all'ambiente di servizio App](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Eliminare un'App Web esistente ###

Per eliminare un'app web esistente è possibile utilizzare il cmdlet **Rimuovi AzureRmWebApp** , è necessario specificare il nome dell'applicazione web e il nome del gruppo di risorse.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Elenco esistente Web Apps ###

Per elencare le app web esistente, utilizzare il cmdlet **Get-AzureRmWebApp** .

Per visualizzare tutte le applicazioni web in abbonamento, usare:

    Get-AzureRmWebApp

Per visualizzare tutte le applicazioni web in un determinato gruppo di risorse, usare:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Per ottenere un'app web specifico, usare:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurare un'applicazione Web esistente ###

Per modificare le impostazioni e configurazioni per un'app web esistente, utilizzare il cmdlet **Set-AzureRmWebApp** . Per un elenco completo dei parametri, selezionare il [collegamento di riferimento Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Esempio (1): utilizzare questo cmdlet per modificare le stringhe di connessione

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Esempio (2): aggiungere o modificare le impostazioni dell'app

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Esempio (3): impostare l'applicazione web per l'esecuzione in modalità a 64 bit

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Modificare lo stato di un'App Web esistente ###

#### <a name="restart-a-web-app"></a>Riavviare un'app web ####

Per riavviare un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Interrompere un'app web ####

Per interrompere un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Avviare un'app web ####

Per avviare un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gestire i profili di pubblicazione Web App ###

Ogni app web dispone di un profilo di pubblicazione che può essere utilizzato per pubblicare le app, è possono eseguire diverse operazioni sulla profili di pubblicazione.

#### <a name="get-publishing-profile"></a>Ottenere la pubblicazione del profilo ####

Per ottenere il profilo di pubblicazione per un'app web, usare:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Questo comando eco sul profilo di pubblicazione per la riga di comando come output anche il profilo di pubblicazione da un file di testo.

#### <a name="reset-publishing-profile"></a>Reimpostazione del profilo di pubblicazione ####

Per reimpostare sia la password di pubblicazione per FTP e web distribuire per un'app web, usare:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gestire i certificati di Web App ###

Per informazioni su come gestire i certificati app web, vedere [associazione di certificati SSL tramite PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Passaggi successivi ###
- Per informazioni su Azure PowerShell Gestione risorse di supporto, vedere [tramite PowerShell Azure Gestione risorse Azure.](../powershell-azure-resource-manager.md)
- Per informazioni sulle App servizio ambienti, vedere [Introduzione all'ambiente di servizio App.](app-service-app-service-environment-intro.md)
- Per informazioni sulla gestione dei certificati SSL servizio App tramite PowerShell, vedere [associazione certificati SSL tramite PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Per conoscere l'elenco completo dei cmdlet di PowerShell basate su Gestione risorse di Azure per Azure Web Apps, vedere [Azure di riferimento sui Cmdlet di Web App Azure Manager delle risorse PowerShell Cmdlets.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Per informazioni sulla gestione servizio App usa CLI, vedere [Using Azure Resource Manager-Based XPlat CLI per Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md)
