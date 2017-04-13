<properties
 pageTitle="Creare il tuo fulcro IoT e registrare i 3 di pi greco Raspberry | Microsoft Azure"
 description="Creare un gruppo di risorse, creare un hub IoT Azure e registrare il Pi nell'hub Azure IoT usa CLI Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 creare il tuo fulcro IoT e registrare i 3 di pi greco Raspberry

## <a name="221-what-you-will-do"></a>2.2.1 azioni da adottare

- Creare un gruppo di risorse.
- Creare l'hub di Azure IoT nel gruppo di risorse.
- Aggiungere i 3 di pi greco Raspberry hub Azure IoT usa CLI Azure.

Quando si usa CLI Azure per aggiungere il tuo fulcro IoT il Pi, il servizio genera una chiave per il Pi eseguire l'autenticazione con il servizio. Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 informazioni contenute

- Informazioni su come usare CLI Azure per creare un IoT Hub Azure.
- Come creare un'identità di dispositivo per il Pi l'hub IoT Azure.

## <a name="223-what-you-need"></a>2.2.3 cosa occorre

- Un account Azure
- Un computer Mac o un computer Windows Azure CLI installato

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 creare il tuo fulcro IoT Azure

IoT Hub Azure consente di connettersi, monitorare e gestire milioni di IoT risorse. Per creare il tuo fulcro IoT Azure, procedere come segue:

1. Accedere al proprio account Azure eseguendo il comando seguente:

    ```bash
    az login
    ```

    Tutte le sottoscrizioni di Azure disponibili sono elencate volta eseguito l'accesso.

2. Impostare il valore predefinito Azure abbonamento che si desidera utilizzare eseguendo il comando seguente:

    ```bash
    az account set -n {subscription id or name}
    ```

    Il nome o l'ID abbonamento sono disponibili nell'output di `az login`.

3. Registrare il provider eseguendo il comando seguente:

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Prima di distribuire la risorsa Azure che fornisce il provider di servizi, è necessario registrare il provider.

    > [AZURE.NOTE] La maggior parte dei provider sono registrate automaticamente tramite il portale di Azure o CLI Azure in uso, ma non tutti. Per ulteriori informazioni relative al provider, vedere [risoluzione dei problemi di distribuzione Azure comuni Gestione risorse Azure](../resource-manager-common-deployment-errors.md)

4. Creare un gruppo di risorse denominato iot campioni nell'area Ovest US eseguendo il comando seguente:

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Creare un hub IoT nel gruppo di risorse iot campioni eseguendo il comando seguente:

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    L'edizione predefinita dell'hub IoT create è **F1**, che corrisponde **gratuito**. Per ulteriori informazioni, vedere [Azure IoT Hub prezzi](https://azure.microsoft.com/pricing/details/iot-hub/).

> [AZURE.NOTE] Il nome di un centro di raccolta IoT deve essere univoco globale.
>
> È possibile creare solo un'edizione di **F1** di Azure IoT Hub in abbonamento Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 registrare il Pi l'hub IoT

Ogni dispositivo che invia o riceve messaggi da/verso l'hub di Azure IoT deve essere registrato con un ID univoco.

Registrare il Pi l'hub di Azure IoT dal comando seguente in esecuzione:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 riepilogo

Aver creato un hub IoT Azure e registrato il pi greco con un'identità dispositivo l'hub IoT Azure. Si è pronti per passare alla lezione successiva per imparare a inviare messaggi dal Pi per il tuo fulcro IoT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti avviare lezione 3 che inizia con [creare un'app di funzione Azure e un account di archiviazione Azure per elaborare e archiviare i messaggi hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).