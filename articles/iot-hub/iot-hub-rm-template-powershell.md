<properties
    pageTitle="Creare un IoT Hub utilizzando un modello di gestione di risorse Azure e PowerShell | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a utilizzare i modelli di gestione di risorse Azure per creare un IoT Hub con PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Creare un hub IoT tramite PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile utilizzare Gestione risorse di Azure per creare e gestire gli hub di Azure IoT a livello di programmazione. In questa esercitazione viene illustrato come utilizzare un modello di gestione di risorse Azure per creare un hub IoT con PowerShell.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse di Azure e classica](../resource-manager-deployment-model.md).  In questo articolo è descritta utilizzando il modello di distribuzione di Manager delle risorse di Azure.

Per completare questa esercitazione è necessario quanto segue:

- Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] o versione successiva.

> [AZURE.TIP] L'articolo [Tramite PowerShell Azure Gestione risorse Azure] [ lnk-powershell-arm] vengono fornite ulteriori informazioni su come usare gli script di PowerShell e gestione di risorse Azure modelli per creare risorse Azure. 

## <a name="connect-to-your-azure-subscription"></a>Connettersi al proprio abbonamento Azure

In un prompt dei comandi di PowerShell, immettere il seguente comando per accedere al proprio abbonamento Azure:

```
Login-AzureRmAccount
```

È possibile utilizzare i comandi seguenti per scoprire dove è possibile distribuire un hub IoT e le versioni di API attualmente supportate:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Creare un gruppo di risorse per contenere il tuo fulcro IoT utilizzando il comando seguente in una delle posizioni supportate per IoT Hub. In questo esempio viene creato un gruppo di risorse denominato **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Invio di un modello di gestione di risorse Azure per creare un hub IoT

Utilizzare un modello JSON per creare un hub IoT nel gruppo di risorse. È anche possibile usare un modello di gestione di risorse Azure per apportare modifiche a un hub IoT esistente.

1. Utilizzare un editor di testo per creare un modello di gestione risorse Azure denominato **template.json** con la definizione di risorse seguenti per creare un nuovo hub IoT standard. In questo esempio aggiunge IoT Hub nell'area **Degli Stati Uniti orientali** , Crea endpoint Hub evento compatibile con due gruppi di consumatori (**cg1** e **cg2**) e utilizza la versione **2016-02-03** API. Questo modello prevede anche passare il nome dell'hub IoT come un parametro denominato **hubName**. Per l'elenco corrente delle aree che supportano l'IoT Hub vedere [Lo stato di Azure][lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Salvare il file del modello Gestione risorse Azure sul computer locale. Si presuppone che si salva una cartella denominata **c:\templates**.

3. Eseguire il seguente comando per distribuire il nuovo hub IoT, passando il nome di un centro di raccolta IoT come parametro. In questo esempio, il nome dell'hub IoT è **abcmyiothub** (si noti che il nome deve essere univoco globale in modo che deve includere il nome o le iniziali):

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. L'output visualizza i tasti per l'hub IoT che è stato creato.

5. È possibile verificare che l'applicazione aggiunto il nuovo hub IoT visitando il [portale di Azure] [ lnk-azure-portal] e visualizzazione dell'elenco delle risorse o utilizzando il cmdlet di PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] In questo esempio di applicazione aggiunge un S1 Standard IoT Hub per il quale vengono addebitate. È possibile eliminare l'hub IoT tramite il [portale di Azure] [ lnk-azure-portal] oppure utilizzare il cmdlet di PowerShell **Rimuovi AzureRmResource** al termine.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT utilizzando un modello di gestione di risorse Azure con PowerShell, è consigliabile esaminare ulteriormente:

- Informazioni sulle funzionalità del [provider di risorse IoT Hub API REST][lnk-rest-api].
- Panoramica di [Gestione risorse di Azure] [ lnk-azure-rm-overview] per ulteriori informazioni sulle funzionalità di gestione di risorse di Azure.

Per ulteriori informazioni sullo sviluppo per IoT Hub, vedere gli articoli seguenti:

- [Introduzione a C SDK][lnk-c-sdk]
- [Hub IoT SDK][lnk-sdks]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
