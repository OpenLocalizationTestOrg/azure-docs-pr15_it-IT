<properties
    pageTitle="Creare un IoT Hub usa CLI Azure | Microsoft Azure"
    description="Seguire questo articolo per creare un IoT Hub tramite l'interfaccia della riga di comando di Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Creare un IoT Hub usa CLI Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile utilizzare l'interfaccia della riga di Azure per creare e gestire gli hub di Azure IoT a livello di programmazione. In questo articolo viene illustrato come utilizzare CLI Azure per creare un IoT Hub.

Per completare questa esercitazione è necessario quanto segue:

- Un account Azure attivo. Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.
- [Azure CLI 0.10.4] [ lnk-CLI-install] o versione successiva. Se si dispone già di Azure CLI per convalidare la versione corrente al prompt dei comandi con il comando seguente:
```
    azure --version
```

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse di Azure e classica](../resource-manager-deployment-model.md). CLI Azure deve essere in modalità di gestione di risorse Azure:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Configurare l'account Azure e la sottoscrizione 

1. Al momento dell'accesso al prompt dei comandi digitando il comando seguente
```
    azure login
```
Utilizzare il browser web suggeriti e il codice per eseguire l'autenticazione.

2. Se si hanno più abbonamenti Azure, la connessione a Azure consente l'accesso a tutte le sottoscrizioni Azure associate le credenziali. È possibile visualizzare le sottoscrizioni di Azure, nonché quello che è l'impostazione predefinita, il comando
```
    azure account list 
```

Per impostare il contesto di sottoscrizione in cui si desidera eseguire il resto dell'utilizzo di comandi

```
    azure account set <subscription name>
```

3. Se non si dispone di un gruppo di risorse è possibile creare uno denominato **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] L'articolo [utilizzare CLI Azure per gestire i gruppi di risorse e risorse Azure] [ lnk-CLI-arm] vengono fornite ulteriori informazioni sull'utilizzo di Azure CLI per gestire le risorse Azure. 


## <a name="create-an-iot-hub"></a>Creare un Hub IoT

Parametri obbligatori:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Per visualizzare tutti i parametri disponibili per la creazione è possibile utilizzare il comando della Guida in prompt dei comandi
```
    azure iothub create -h 
```
Esempio veloce:

 Per creare un IoT Hub denominato **exampleIoTHubName** nel gruppo di risorse **exampleResourceGroup** è sufficiente eseguire il comando seguente
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Questo comando di Azure CLI crea un S1 Standard IoT Hub per il quale vengono addebitate. È possibile eliminare l' hub di IoT **exampleIoTHubName** il seguente comando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sullo sviluppo per IoT Hub, vedere gli articoli seguenti:
- [Hub IoT SDK][lnk-sdks]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Tramite il portale di Azure per gestire IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
