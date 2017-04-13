<properties
 pageTitle="Informazioni i metadati della soluzione di monitoraggio remoto | Microsoft Azure"
 description="Descrizione IoT Azure preconfigurato soluzione remoto monitoraggio e la relativa architettura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Informazioni i metadati della soluzione preconfigurato monitoraggio remoto

La famiglia di IoT Azure remoto preconfigurato soluzione di monitoraggio illustra un approccio per la gestione dei metadati di dispositivo. Questo articolo illustra l'approccio che questa soluzione consente di accedere in modo da comprendere:

- I metadati di dispositivo la soluzione archivia.
- La soluzione come vengono gestiti i metadati di dispositivo.

## <a name="context"></a>Scelta rapida

La soluzione preconfigurata monitoraggio remota utilizza [Azure IoT Hub] [ lnk-iot-hub] per attivare i dispositivi inviare dati a nel cloud. IoT Hub include un [Registro di sistema di dispositivo identità] [ lnk-identity-registry] per controllare l'accesso a IoT Hub. Il Registro di sistema IoT Hub identità dispositivo è distinto dal remota monitoraggio specifici *Registro di sistema dispositivo* in cui sono memorizzati i metadati informazioni. Soluzione di monitoraggio remoto utilizza un [DocumentDB] [ lnk-docdb] database per implementare il Registro di sistema di dispositivo per l'archiviazione dei metadati informazioni dispositivo. [Architettura di riferimento di Microsoft Azure IoT] [ lnk-ref-arch] viene descritto il ruolo del Registro di sistema dispositivo in una soluzione IoT tipica.

> [AZURE.NOTE] Soluzione di preconfigurato monitoraggio remoto mantiene aggiornata del Registro di sistema di identità dispositivo sincronizzato con il Registro di sistema del dispositivo. Entrambi utilizzano lo stesso id di dispositivo per identificare in modo univoco ogni dispositivo connesso a un centro di raccolta IoT.

[Anteprima di gestione di dispositivi IoT Hub] [ lnk-dm-preview] aggiunge caratteristiche a IoT Hub che sono simili alle caratteristiche di gestione delle informazioni di dispositivo descritte in questo articolo. Al momento della soluzione di monitoraggio remota utilizza solo caratteristiche (GA) generalmente disponibile nell'IoT Hub.

## <a name="device-information-metadata"></a>Dispositivo informazioni metadati

Un dispositivo basato su metadati JSON informativa archiviato nel dispositivo del Registro di sistema DocumentDB database presenta la struttura seguente:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: il dispositivo scrive queste proprietà e il dispositivo è l'autorità per questi dati. Altre proprietà del dispositivo esempio includono produttore, il numero di modello e il numero di serie. 
- **DeviceID**: l'id univoco del dispositivo. Questo valore è lo stesso del Registro di sistema IoT Hub identità dispositivo.
- **HubEnabledState**: lo stato del dispositivo IoT hub. Questo valore inizialmente è impostato su **null** fino a quando il dispositivo prima connessione. Nel portale soluzione un valore **null** viene rappresentato come il dispositivo è "registrati ma non è presente."
- **CreatedTime**: l'ora in cui è stato creato il dispositivo.
- **DeviceState**: lo stato indicato dal dispositivo.
- **UpdatedTime**: ora sul dispositivo dell'ultimo aggiornamento tramite il portale di soluzione.
- **SystemProperties**: portale soluzione scrive le proprietà di sistema e il dispositivo non ha una conoscenza nessuna di queste proprietà. Una proprietà di sistema di esempio è **ICCID** se la soluzione è autorizzata a e connesso a un servizio di gestione di dispositivi abilitati Image.
- **Comandi**: supporta il dispositivo di un elenco dei comandi. Il dispositivo fornisce le informazioni per la soluzione.
- **CommandHistory**: un elenco di comandi inviati da della soluzione di monitoraggio remota per il dispositivo e lo stato di tali comandi.
- **IsSimulatedDevice**: un contrassegno che identifica un dispositivo come dispositivo simulato.
- **ID**: l'identificatore univoco DocumentDB per questo documento dispositivo.

> [AZURE.NOTE] Informazioni sul dispositivo può includere anche metadati per descrivere telemetria che il dispositivo invia a IoT Hub. Soluzione di monitoraggio remoto Usa metadati telemetria per personalizzare la modalità di visualizzazione di [telemetria dinamico]nel dashboard[lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo di vita

Quando si crea un dispositivo nel portale di soluzione, la soluzione crea una voce nel Registro di sistema dispositivo come illustrato in precedenza. La maggior parte delle informazioni inizialmente viene generata automaticamente e **HubEnabledState** è impostato su **null**. A questo punto, la soluzione crea anche una voce per il dispositivo nel dispositivo identità Registro di sistema, genera le chiavi che il dispositivo utilizzato per eseguire l'autenticazione con IoT Hub.

Quando un dispositivo si connette prima di tutto alla soluzione, viene inviato un messaggio di informazioni dispositivo. Questo messaggio informativo dispositivo include le proprietà di dispositivo, ad esempio il produttore del dispositivo, il numero del modello e il numero di serie. Un messaggio di informazioni dispositivo include anche un elenco di comandi che il dispositivo supporta incluse informazioni sui parametri di comando. Quando la soluzione riceve il messaggio seguente, vengono aggiornati i metadati di informazioni dispositivo del Registro di sistema del dispositivo.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visualizzare e modificare le informazioni di dispositivo nel portale di soluzione

Elenco dei dispositivi di nel portale di soluzione verranno visualizzate le opzioni dispositivo seguenti come colonne: **stato** **DeviceId**, **produttore**, **Il numero del modello**, **numero seriale**, **Firmware**, **piattaforma**, **processore**e **RAM installato**. Le proprietà dei dispositivi **latitudine** e **longitudine** unità la posizione nella mappa di Bing nel dashboard. 

![Elenco dei dispositivi][img-device-list]

Se si fa clic su **Modifica** nel riquadro dei **Dettagli dispositivo** nel portale di soluzione, è possibile modificare tutte queste proprietà. Modifica di queste proprietà aggiorna il record per il dispositivo nel database DocumentDB. Tuttavia, se un dispositivo invia un messaggio informativo di dispositivo aggiornati, sovrascrive le modifiche apportate nel portale di soluzione. Impossibile modificare il **DeviceId** **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**e proprietà **UpdatedTime** nel portale di soluzione perché solo dal dispositivo avrà autorità su queste proprietà.

![Modifica di dispositivo][img-device-edit]

È possibile utilizzare il portale di soluzione per rimuovere un dispositivo dalla soluzione. Quando si rimuove un dispositivo, la soluzione rimuove i metadati di informazioni dispositivo dal Registro di sistema di dispositivo soluzione e la voce di dispositivo del Registro di sistema IoT Hub identità dispositivo. Prima di rimuovere un dispositivo, è necessario disattivarlo.

![Rimuovi dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Elaborazione dei messaggi di informazioni dispositivo

I messaggi di informazioni dispositivo inviati da un dispositivo sono distinti dai messaggi di telemetria. Dispositivo informazioni messaggi includono informazioni, ad esempio le proprietà del dispositivo, i comandi di a che un dispositivo può rispondere e la cronologia di comando. IoT Hub stesso dispone di informazioni sui metadati contenuti in un messaggio di informazioni dispositivo ed elabora il messaggio nello stesso modo che elabora qualsiasi messaggio dispositivo nel cloud. In soluzione di monitoraggio remoto, un [Azure flusso Analitica] [ lnk-stream-analytics] processo (ASA) legge i messaggi dall'IoT Hub. Analitica di flusso **DeviceInfo** processo filtri per i messaggi contenenti **"Tipooggetto": "DeviceInfo"** e li inoltra all'host **EventProcessorHost** che viene eseguita in un processo web. Logica dell'istanza **EventProcessorHost** utilizza l'id di dispositivo per individuare il record DocumentDB per il dispositivo specifico e aggiornare il record. I record del Registro di sistema dispositivo ora include informazioni, ad esempio le proprietà del dispositivo, i comandi e cronologia dei comandi.

> [AZURE.NOTE] Un messaggio di informazioni dispositivo è un dispositivo nel cloud standard. La soluzione distingue tra dispositivo informazioni messaggi e di telemetria tramite ASA query.

## <a name="example-device-information-records"></a>Record di informazioni sul dispositivo di esempio

Soluzione di preconfigurato monitoraggio remoto utilizza due tipi di record di informazioni sul dispositivo: i record per i dispositivi simulati distribuiti con la soluzione e i record per i dispositivi personalizzati si connette alla soluzione.

### <a name="simulated-device"></a>Dispositivo simulato

Nell'esempio seguente mostra il record di informazioni JSON dispositivo per un dispositivo simulato. Questo record è un valore impostato per **UpdatedTime**, che indica che il dispositivo ha inviato un messaggio **DeviceInfo** a IoT Hub. Il record include alcune proprietà dei dispositivi comuni, definisce i sei comandi simulati dispositivi supportano e con il flag **IsSimulatedDevice** impostato su **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Dispositivo personalizzato

Nell'esempio seguente mostra il record di informazioni JSON dispositivo per un dispositivo personalizzato e il contrassegno **IsSimulatedDevice** è impostata su **0**. È possibile visualizzare che il dispositivo personalizzato supporta due comandi e che il portale soluzione ha inviato un comando **SetTemperature** al dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

Di seguito viene illustrato il messaggio JSON **DeviceInfo** dispositivo inviato per aggiornare i metadati di informazioni dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato formazione come è possibile personalizzare le soluzioni preconfigurate, è possibile esplorare alcune delle altre caratteristiche e le soluzioni di famiglia IoT preconfigurato:

- [Panoramica della soluzione di manutenzione stima preconfigurata][lnk-predictive-overview]
- [Domande frequenti su IoT famiglia di prodotti][lnk-faq]
- [Protezione IoT sin][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
