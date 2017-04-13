<properties
    pageTitle="Alta densità hosting del servizio di App Azure | Microsoft Azure"
    description="Alta densità hosting del servizio di App Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Alta densità hosting del servizio di App Azure

Quando si usa il servizio di App, l'applicazione è disaccoppiato dalla capacità assegnata a tale da due concetti:

- **L'applicazione:** Rappresenta l'app e la configurazione di runtime. Ad esempio, include la versione di .NET il runtime deve caricare, le impostazioni di app e così via.

- **Il piano di servizio App:** Definisce le caratteristiche della capacità, set di caratteristiche disponibili e località dell'applicazione. Caratteristiche, ad esempio, potrebbero essere grandi computer (quattro core), quattro istanze, caratteristiche Premium negli Stati Uniti orientali.

Un'app è sempre associata a un piano di servizio App, ma un piano di servizio App può fornire capacità di uno o più applicazioni.

Di conseguenza, la piattaforma flessibilità per isolare single app o altre persone più applicazioni di condividere le risorse per la condivisione di un piano di servizio di App.

Tuttavia, quando più applicazioni condividono un piano di servizio App, un'istanza di tale app viene eseguita per tutte le istanze di tale piano di servizio di App.

## <a name="per-app-scaling"></a>Per il ridimensionamento app
*Per il ridimensionamento app* è una caratteristica che può essere abilitata al livello del piano di servizio di App e quindi utilizzata per ogni applicazione.

Per app proporzioni dei caratteri scale di un'app in modo indipendente dal piano di servizio App che lo ospita. In questo modo, un piano di servizio App può essere configurato per fornire 10 istanze, ma può essere un'app su scala a solo 5 di esse.

Il modello di gestione di risorse Azure seguente crea un piano di servizio App scalabilità 10 istanze e un'app configurata in modo da usare per il ridimensionamento app e scala a solo 5 istanze.

Il piano di servizio App impostazione della proprietà **proporzioni dei caratteri per sito** vero ( `"perSiteScaling": true`). L'app è impostare il **numero di dipendenti** da utilizzare per 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configurazione consigliata per l'hosting ad alta densità

Per il ridimensionamento app è una funzionalità che è attivata in aree Azure pubbliche e App servizio ambienti. Tuttavia, la strategia consigliata consiste nell'utilizzare App servizio ambienti per sfruttare le funzionalità avanzate e i pool di dimensioni maggiori della capacità.  

Seguire questa procedura per configurare ad alta densità di hosting per le app:

1. Configurare l'ambiente di servizio App e scegliere un pool di lavoro che si impegna costantemente per lo scenario di hosting ad alta densità.

1. Creare un piano di servizio App singolo e scalare, è possibile utilizzare tutta la capacità disponibile nel pool di lavoro.

1. Impostare il contrassegno di ridimensionamento per sito su true per il piano di servizio di App.

1. Nuovi siti vengono creati e assegnati a tale piano di servizio App con la proprietà **numberOfWorkers** impostata su **1**. Utilizzando questa configurazione si la densità più alta possibili nel pool di lavoro.

1. Il numero di dipendenti può essere configurato in modo indipendente per ogni sito da concedere a risorse aggiuntive in base alle esigenze. Ad esempio, un sito di utilizzo può impostare **numberOfWorkers** a **3** per avere maggiore capacità di elaborazione per tale app siti meno utilizzate da impostare **numberOfWorkers** su **1**.
