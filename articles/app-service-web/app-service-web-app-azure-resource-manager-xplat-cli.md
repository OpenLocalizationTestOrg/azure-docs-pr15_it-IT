<properties
    pageTitle="Strumenti di Azure della riga di comando multipiattaforma basate su Manager delle risorse per Azure Web App | Microsoft Azure"
    description="Informazioni su come usare i nuovi strumenti di riga di comando multipiattaforma basate su Azure risorsa Manager per gestire le app Web di Azure."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Usa Azure risorse basate su Manager XPlat CLI per Azure Web App#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Con la versione della versione di Microsoft Azure multipiattaforma della riga di comando strumenti 0.10.5, sono stati aggiunti nuovi comandi. Questi comandi consentono all'utente di utilizzare comandi basate su Gestione risorse di Azure PowerShell per gestire applicazioni Web.

Per informazioni sulla gestione dei gruppi di risorse, vedere [utilizzare CLI Azure per gestire le risorse Azure e gruppi di risorse](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Gestione App servizio plan di messaggistica unificata ##

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio di App ###
Per creare un piano di servizio app, usare il comando **appserviceplan azure create** .

Ecco le descrizioni dei parametri diversi:

-   **-gruppo di risorse**: gruppo di risorse che include il piano di servizio app appena creato.
-   **-nome**: nome del piano di servizio di app.
-   **-percorso**: percorso del piano di servizio app.
-   **-livello**: lo sku prezzo desiderato (le opzioni disponibili sono: F1 (Free). D1 (condiviso). B1 (piccola base), B2 (base medio) e B3 (Basic di grandi dimensioni). S1 (piccola Standard), S2 (Standard medio) e S3 (Standard di grandi dimensioni). P1 (piccola Premium), P2 (Premium Media) e P3 (Premium grande).)
-   **-istanze**: il numero di dipendenti nell'applicazione di servizio piano (valore predefinito è 1).

Esempio di utilizzare questo cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Elenco esistente App servizio plan di messaggistica unificata ###

Per elencare i piani di servizio app esistente, usare il comando **elenco appserviceplan azure** .

Per elencare tutti i piani di servizio di app in un determinato gruppo di risorse, usare:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Per ottenere un piano di servizio app specifica, utilizzare comando **Mostra appserviceplan azure** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurare un piano di servizio App esistente ###

Per modificare le impostazioni per un piano di servizio app esistente, usare il comando **config appserviceplan azure** . È possibile modificare lo sku e il numero di dipendenti 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Ridimensionamento di un piano di servizio di App ####

Per ridimensionare un piano di servizio App esistente, usare:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Modificare lo SKU di un piano di servizio di App ####

Per modificare lo sku di un piano di servizio App esistente, usare:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminare un piano di servizio App esistente ###

Per eliminare un piano di servizio app esistente, tutte le app web assegnate necessario essere spostato o eliminato prima di tutto. L'utilizzo del comando **Elimina azure Web App** che è possibile eliminare il piano di servizio app.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gestire le App servizio Web App ##

### <a name="create-a-web-app"></a>Creare un'App Web ###

Per creare un'app web, utilizzare il comando **Crea azure Web App** .

Ecco le descrizioni dei parametri diversi:

- **-nome**: nome per l'applicazione web.
- **-piano**: nome per il piano di servizio utilizzato per ospitare il web app.
- **-gruppo di risorse**: gruppo di risorse contenente il piano di servizio App.
- **-percorso**: il percorso di app web.

Esempio di utilizzare questo cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Eliminare un'App Web esistente ###

Per eliminare un'app web esistente è possibile utilizzare il comando **Elimina azure Web App** , è necessario specificare il nome dell'applicazione web e il nome del gruppo di risorse.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Elenco esistente Web Apps ###

Per elencare le app web esistente, usare il comando **elenco azure Web App** .

Per visualizzare tutte le applicazioni web in un determinato gruppo di risorse, usare:

    azure webapp list --resource-group ContosoAzureResourceGroup

Per ottenere un'app web specifico, usare il comando **Mostra azure Web App** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurare un'applicazione Web esistente ###

Per modificare le impostazioni e configurazioni per un'app web esistente, utilizzare il comando **set config azure Web App** .

Esempio (1): modificare la versione di php di un'app web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Esempio (2): aggiungere o modificare le impostazioni dell'app

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Sapere a cosa altri configurazione può essere modificato, utilizzare il comando **config Web App azure set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Modificare lo stato di un'App Web esistente ###

#### <a name="restart-a-web-app"></a>Riavviare un'app web ####

Per riavviare un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Interrompere un'app web ####

Per interrompere un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Avviare un'app web ####

Per avviare un'app web, è necessario specificare il gruppo nome e risorse della finestra dell'app web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gestire i profili di pubblicazione Web App ###

Ogni app web dispone di un profilo di pubblicazione che può essere utilizzato per la pubblicazione delle applicazioni.

#### <a name="get-publishing-profile"></a>Ottenere la pubblicazione del profilo ####

Per ottenere il profilo di pubblicazione per un'app web, usare:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Questo comando eco il nome utente profilo pubblicazione e la password alla riga di comando.

### <a name="manage-web-app-hostnames"></a>Gestire i nomi host Web App ###

Per gestire le associazioni hostname per un'app web, usare il comando di **nomi di configurazione host azure Web App**  

#### <a name="list-hostname-bindings"></a>Elenco hostname associazioni ####

Per ottenere le associazioni hostname corrente per un'app web, usare:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Aggiungere le associazioni di nome host ####

Per aggiungere un'app web hostname associazioni, usare:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminare le associazioni di nome host ####

Per eliminare le associazioni hostname, usare:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Passaggi successivi ###
- Per ulteriori informazioni sul supporto di Azure Manager delle risorse CLI, vedere [consente di gestire i gruppi di risorse e risorse Azure CLI Azure.](../xplat-cli-azure-resource-manager.md)
- Per informazioni sulla gestione servizio App tramite PowerShell, vedere [Using Azure Resource Manager-Based PowerShell per gestire Azure Web Apps.](app-service-web-app-azure-resource-manager-powershell.md)
