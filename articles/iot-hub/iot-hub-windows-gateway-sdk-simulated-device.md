<properties
    pageTitle="Consente di simulare un dispositivo con SDK Gateway IoT | Microsoft Azure"
    description="Procedura dettagliata IoT Gateway SDK Azure utilizzo di Windows per illustrare telemetria invio da un dispositivo simulato con Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>Azure IoT Gateway SDK (beta): inviare messaggi di dispositivo nel cloud con un dispositivo simulato con Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Creare ed eseguire l'esempio

Prima di iniziare, è necessario:

- [Configurare l'ambiente di sviluppo] [ lnk-setupdevbox] per l'utilizzo con SDK in Windows.
- [Creare un hub IoT] [ lnk-create-hub] nell'abbonamento Azure, è necessario il nome del computer di controllo per completare questa procedura dettagliata. Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.
- Aggiungere due dispositivi per il tuo fulcro IoT e prendere nota del loro ID e i tasti di dispositivo. È possibile usare [Esplora dispositivi o iothub explorer] [ lnk-explorer-tools] strumento per aggiungere i dispositivi a hub IoT creato nel passaggio precedente e recuperare le chiavi.

Per generare l'esempio:

1. Aprire un prompt dei comandi di **sviluppatore Prompt dei comandi per VS2015** .
2. Passare alla cartella radice nella copia locale del repository **iot di azure-sdk di gateway** .
3. Eseguire il **strumenti\\build.cmd** script. Questo script crea un file di soluzione Visual Studio, compila la soluzione e consente di eseguire il test. È possibile trovare la soluzione Visual Studio nella cartella **creare** nella copia locale del repository **iot di azure-sdk di gateway** .

Per eseguire l'esempio:

In un editor di testo, aprire il file **esempi\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** nella copia locale del repository **iot di azure-sdk di gateway** . Questo file configura i moduli nel gateway di esempio:

- Il modulo **IoTHub** si connette a un centro di raccolta IoT. È necessario configurarlo per inviare dati a un centro di raccolta IoT. In particolare, impostare il valore di **IoTHubName** il nome del computer IoT di controllo e impostare il valore di **IoTHubSuffix** **devices.net azure**. Impostare il valore di **trasporto** a uno dei: "HTTP", "AMQP" o "MQTT". Si noti che al momento solo "HTTP" condividerà una connessione TCP per tutti i messaggi di dispositivo. Se si imposta il valore di "AMQP" o "MQTT", il gateway manterrà una connessione TCP separata a IoT Hub per ogni dispositivo.
- Il modulo **mapping** esegue il mapping di indirizzi MAC dei dispositivi simulati l'ID di dispositivo IoT Hub. Verificare che i valori **deviceId** corrispondono gli ID di due dispositivi che sono stati aggiunti al tuo fulcro IoT e che i valori **deviceKey** contengono le chiavi di due dispositivi.
- I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi MAC corrispondono a quelli del modulo di **mapping** .
- Il modulo **Logger** registra l'attività di gateway in un file.
- I valori di **Percorso modulo** riportati di seguito presuppongono duplicati repository IoT Gateway SDK nella radice dell'unità **c** . Se si è scaricato in un'altra posizione, è necessario modificare i valori dei **percorsi di modulo** di conseguenza.
- Matrice di **collegamenti** nella parte inferiore del file JSON connette moduli **BLE1** e **BLE2** il modulo di **mapping** e il modulo **mapping** al modulo **IoTHub** . Garantisce inoltre che tutti i messaggi vengono registrati dal modulo di **Logger** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

Salvare le modifiche apportate al file di configurazione.

Per eseguire l'esempio:

1. Al prompt dei comandi, passare alla cartella radice della propria copia locale del repository **iot di azure-sdk di gateway** .
2. Eseguire il comando seguente:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. È possibile usare [Esplora dispositivi o iothub explorer] [ lnk-explorer-tools] strumento per monitorare i messaggi che IoT hub riceve dal gateway.


## <a name="next-steps"></a>Passaggi successivi

Se si desidera comprendere più avanzate di SDK Gateway IoT e sperimentare alcuni esempi di codice, visitare le seguenti esercitazioni di sviluppo e risorse:

- [Inviare messaggi di dispositivo nel cloud da un dispositivo reale con IoT Gateway SDK][lnk-physical-device]
- [Gateway IoT Azure SDK][lnk-gateway-sdk]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 