<properties
 pageTitle="Monitoraggio delle operazioni IoT Hub"
 description="Una panoramica delle operazioni di Azure IoT Hub monitoraggio, che consente di controllare lo stato di operazioni sull'hub IoT in tempo reale"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introduzione alle operazioni di monitoraggio

Monitoraggio delle operazioni IoT Hub consente di controllare lo stato di operazioni sull'hub IoT in tempo reale. IoT Hub tiene traccia degli eventi tra diverse categorie di operazioni e, è possibile farlo in invio eventi da una o più categorie a un punto finale di un centro di raccolta IoT per l'elaborazione. È possibile monitorare i dati relativi a errori o impostare l'elaborazione più complessa in base a modelli di dati.

IoT Hub esegue il monitoraggio cinque categorie di eventi:

- Operazioni di identità dispositivo
- Telemetria dispositivo
- Comandi cloud al dispositivo
- Connessioni
- Carica file

## <a name="how-to-enable-operations-monitoring"></a>Come abilitare monitoraggio delle operazioni

1. Creare un hub IoT. È possibile trovare istruzioni su come creare un hub IoT nella [Guida introduttiva] [ lnk-get-started] Guida.

2. Aprire e il dell'hub IoT. A questo punto, fare clic su **monitoraggio delle operazioni**.

    ![][1]

3. Selezionare le categorie monitoraggio che si desidera eseguire il monitoraggio e quindi fare clic su **Salva**. Gli eventi disponibili per la lettura dal punto finale Hub evento compatibile con elencato nelle **impostazioni di monitoraggio**. Endpoint IoT Hub è denominata `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Categorie di eventi e sul loro utilizzo

Ogni categoria tracce di monitoraggio delle operazioni un altro tipo di interazione con IoT Hub e ogni categoria monitoraggio presenta uno schema che definisce la struttura eventi della categoria.

### <a name="device-identity-operations"></a>Operazioni di identità dispositivo

La categoria di operazioni identità dispositivo rileva errori che si verificano quando si tenta di creare, aggiornare o eliminare una voce del Registro di sistema dell'hub IoT identità. Verifica questa categoria è utile per il provisioning di scenari.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetria dispositivo

La categoria di telemetria dispositivo rileva errori che si verificano a intervalli hub IoT e riguardano la pipeline di telemetria. Questa categoria include errori che si verificano durante l'invio di eventi di telemetria (ad esempio la limitazione) e la ricezione degli eventi di telemetria (ad esempio non autorizzato reader). Si noti che questa categoria non è possibile rilevare gli errori di esecuzione di codice nel dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandi cloud al dispositivo

La categoria comandi cloud per dispositivo tiene traccia degli errori che si verificano a intervalli hub IoT e riguardano la pipeline dei comandi di dispositivo. Questa categoria include errori che si verificano durante l'invio di comandi (ad esempio il mittente non autorizzato), che ricevono i comandi (ad esempio superamento del numero di recapito) e la ricezione di commenti e suggerimenti comando (ad esempio commenti e suggerimenti scaduto). Questa categoria non rileva errori da un dispositivo che consente di gestire in modo errato un comando se il comando è stato recapitato.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Connessioni

La categoria di connessioni consente di tenere traccia degli errori che si verificano quando dispositivi connettere o disconnettere da un hub IoT. Verifica questa categoria è utile per l'identificazione tentativi di connessione non autorizzato e per la registrazione quando una connessione viene persa per i dispositivi nelle aree di scarsa connettività.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Carica file

La categoria di caricamento di file consente di tenere traccia degli errori che si verificano a intervalli hub IoT e relative alle funzionalità di caricamento di file. Questa categoria include errori che si verificano con URI SAS (ad esempio quando scade prima di un dispositivo notifica hub della completamente caricato), caricamento non riuscito segnalato dal dispositivo e quando un file non viene trovato in archiviazione durante la creazione del messaggio di notifica IoT Hub. Si noti che questa categoria non è possibile rilevare che direttamente si verificano durante il dispositivo viene caricato un file allo spazio di archiviazione.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md