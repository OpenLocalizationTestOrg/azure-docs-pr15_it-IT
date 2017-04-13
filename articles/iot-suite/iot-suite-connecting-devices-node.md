<properties
   pageTitle="Connettere un dispositivo che utilizza Node | Microsoft Azure"
   description="Descrive come collegare un dispositivo alla soluzione monitoraggio remoto Azure IoT Suite preconfigurato utilizzando un'applicazione scritta in Node."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connettere il dispositivo per la soluzione preconfigurata monitoraggio remota (Node)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Creare ed eseguire la soluzione di esempio Node

1. Per duplicare l'archivio di *Microsoft Azure IoT SDK* GitHub e installare il *dispositivo di Microsoft Azure IoT SDK per Node* nel proprio ambiente desktop, seguire [preparare l'ambiente di sviluppo] [ lnk-github-prepare] istruzioni.

2. La copia locale del [SDK di iot azure] [ lnk-github-repo] repository, copiare le due file dalla cartella nodo / / esempi relativi ai dispositivi in una cartella nel dispositivo:

  - Packages.JSON
  - remote_monitoring.js

3. Aprire il file remote_monitoring.js e cercare la variabile seguente:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Sostituire **[stringa di connessione dispositivo IoT Hub]** con la stringa di connessione di dispositivi. È possibile trovare i valori per il tuo fulcro IoT hostname, id dispositivo e la chiave dispositivo nel dashboard soluzione monitoraggio remoto. Una stringa di connessione di dispositivi è nel formato seguente:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se il nome host IoT Hub è **contoso** e l'id di dispositivo è **mydevice**, la stringa di connessione ha l'aspetto:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Salvare il file. Al prompt dei comandi nella cartella contenente i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio, eseguire i comandi seguenti:

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md