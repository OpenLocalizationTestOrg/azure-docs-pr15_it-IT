<properties
    pageTitle="Distribuire un'app web usando MSDeploy con certificato ssl e nome host"
    description="Utilizzare un modello di gestione di risorse Azure per distribuire un'app web usando MSDeploy e sulla configurazione hostname personalizzato e un certificato SSL"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Distribuire un'app web con MSDeploy, hostname personalizzato e il certificato SSL

Questa guida è illustrata la creazione di una distribuzione-to-end per un'App Web di Azure, sfruttando MSDeploy nonché di aggiunta di un nome host personalizzato e un certificato SSL al modello ARM.

Per ulteriori informazioni sulla creazione di modelli, vedere [Creazione di modelli di Manager delle risorse Azure](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Creare l'applicazione di esempio

Si distribuendo un'applicazione web ASP.NET. Il primo passaggio consiste nel creare un'applicazione web semplice (o è possibile scegliere di utilizzarne una esistente, nel qual caso è possibile ignorare questo passaggio).

Aprire Visual Studio 2015 e scegliere File > Nuovo progetto. Nella finestra di dialogo visualizzata scegliere Web > applicazione Web ASP.NET. In modelli scegliere Web e scegliere il modello MVC. Selezionare _Cambia tipo di autenticazione_ da _Nessuna autenticazione_. Questo è solo per semplificare l'applicazione di esempio.

A questo punto si avrà un'app di web ASP.Net base pronta da utilizzare come parte del processo di distribuzione.

###<a name="create-msdeploy-package"></a>Crea pacchetto MSDeploy

Passaggio successivo consiste nel creare il pacchetto per distribuire il web app in Azure. Per eseguire questa operazione, salvare il progetto e quindi eseguire le operazioni seguenti dalla riga di comando:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Per creare un pacchetto zip nella cartella PackageLocation. L'applicazione è ora pronto per essere distribuito, che a questo punto è possibile creare un modello di gestione di risorse Azure farlo.

###<a name="create-arm-template"></a>Creare il modello di ARM
Prima di tutto, iniziamo con un modello di ARM base che consente di creare un'applicazione web e un piano di hosting (si noti che non sono visualizzate i parametri e variabili per brevità).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Successivamente, sarà necessario modificare la risorsa app web per scrivere una risorsa MSDeploy annidata. In questo modo sarà possibile riferimento il pacchetto creato in precedenza e indica a Gestione risorse di Azure usare MSDeploy per distribuire il pacchetto per la Web App Azure. Di seguito viene illustrata la risorsa Microsoft.Web/sites con la risorsa MSDeploy annidata:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

A questo punto si noterà che la risorsa MSDeploy richiede una proprietà **packageUri** definita nel seguente modo:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Questo **packageUri** accetta uri account lo spazio di archiviazione che punta all'account di archiviazione in cui sarà caricato il pacchetto zip a. Gestione risorse Azure verranno utilizzati [Condiviso firme di accesso](../storage/storage-dotnet-shared-access-signature-part-1.md) per estrarre il pacchetto verso il basso in locale dall'account di archiviazione quando si distribuisce il modello. Questo processo risulterà automatico tramite un PowerShell script che carica il pacchetto di chiamare l'API di gestione di Azure per creare le chiavi necessari e quelli nel modello passare come parametri (*_artifactsLocation* e *_artifactsLocationSasToken*). È necessario definire i parametri per la cartella e nome file del pacchetto viene caricato all'interno del contenitore di spazio di archiviazione.

Successivamente è necessario aggiungere in un'altra risorsa annidata per impostare le associazioni hostname per utilizzare un dominio personalizzato. È necessario innanzitutto garantire che si possiede il nome host e impostarlo in modo da essere verificato da Azure che si è proprietari, vedere [configurare un nome di dominio personalizzato in Azure App servizio](web-sites-custom-domain-name.md). Al termine dell'operazione è possibile aggiungere le operazioni seguenti per il modello nella sezione delle risorse Microsoft.Web/sites:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Infine, è necessario aggiungere un altro alto livello delle risorse, Microsoft.Web/certificates. Questa risorsa contiene il certificato SSL e sarà presenti allo stesso livello del web app e piano di hosting.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

È necessario disporre di un certificato SSL valido per impostare questa risorsa. Dopo avere inserito tale certificato valido è necessario estrarre byte pfx come stringa base 64. Un'opzione per estrarre ciò consiste nell'usare il comando PowerShell seguente:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

È quindi possibile passare questo come parametro per il modello di distribuzione ARM.

A questo punto, il modello ARM è pronto.

###<a name="deploy-template"></a>Distribuire modello

Il passaggio finale consiste nel mettere questo tutte insieme in una distribuzione full-to-end. Per semplificare la distribuzione che è possibile sfruttare le lo script di PowerShell **Distribuisci AzureResourceGroup.ps1** che viene aggiunto quando si crea un progetto di gruppo di risorse Azure in Visual Studio per agevolare la durante il caricamento di tutti gli elementi necessari nel modello. Richiede l'esecuzione di aver creato un account di archiviazione che si desidera utilizzare anticipo. In questo esempio creato un account di archiviazione condivisa per package.zip da caricare. Lo script verrà utilizzati AzCopy per caricare il pacchetto per l'account di archiviazione. Passare al percorso della cartella l'elemento e lo script verrà caricare automaticamente tutti i file all'interno di tale directory al contenitore di memorizzazione denominata. Dopo la chiamata Distribuisci AzureResourceGroup.ps1 è necessario aggiornare le associazioni SSL per mappare il nome host personalizzato con il certificato SSL.

PowerShell seguente mostra la distribuzione di completa la chiamata di implementazione-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

A questo punto l'applicazione è necessario aver distribuito e dovrebbe essere possibile spostarsi su di esso tramite https://www.yourcustomdomain.com
