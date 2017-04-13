<properties
 pageTitle="Guida per sviluppatori - Carica file | Microsoft Azure"
 description="Guida di sviluppo IoT Hub Azure - caricamento di file da un dispositivo a IoT Hub"
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
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="upload-files-from-a-device"></a>Caricare i file da un dispositivo

## <a name="overview"></a>Panoramica

Come illustrato nella [IoT Hub endpoint] [ lnk-endpoints] articolo dispositivi possono avviare il caricamento di file tramite l'invio di una notifica tramite un endpoint esposto dispositivo (**/devices/ {deviceId} / file**).  Quando un dispositivo notifica IoT Hub di completamente caricato, IoT Hub genera notifiche per caricamenti file come messaggi ricevuti tramite un endpoint servizio esposto (**/messages/servicebound/filenotifications**).

Invece di negoziazione messaggi tramite IoT Hub stesso, IoT Hub funge invece un dispatcher per un account di archiviazione Azure associato. Un dispositivo richiede un token di spazio di archiviazione dall'IoT Hub specifica per il file che sul dispositivo desidera caricare. Il dispositivo utilizza l'URI SAS per caricare il file allo spazio di archiviazione e una volta completato il caricamento il dispositivo invia una notifica di completamento per IoT Hub. IoT Hub verifica che il file è stato caricato e quindi aggiunge una notifica di caricamento di file al nuovo servizio esposto file notifica messaggistica endpoint.

Prima di caricare un file a IoT Hub da un dispositivo, è necessario configurare il tuo fulcro associando [Un archivio di Azure] [ lnk-associate-storage] a tale account.

Il dispositivo quindi possibile [inizializzare un caricamento] [ lnk-initialize] e quindi [una notifica IoT hub] [ lnk-notify] al termine del caricamento. Facoltativamente, quando un dispositivo notifica IoT Hub che è stato completato il caricamento, il servizio può generare un [messaggio di notifica][lnk-service-notification].

### <a name="when-to-use"></a>Quando utilizzare

Utilizzare questa caratteristica IoT Hub quando è necessario caricare un file da un dispositivo per il servizio di back-end invece di inviare un messaggio tramite IoT Hub.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associare un account di archiviazione Azure IoT Hub

Per utilizzare la funzionalità di caricamento di file, è prima necessario collegare un account di archiviazione Azure a IoT Hub. È possibile eseguire questa operazione tramite il [portale di Azure][lnk-management-portal], o a livello di programmazione tramite il [provider di risorse IoT Hub API REST][lnk-resource-provider-apis]. Dopo aver associato un account di archiviazione Azure con il tuo fulcro IoT, il servizio restituisce un URI SAS a un dispositivo quando il dispositivo avvia una richiesta di caricamento di file.

> [AZURE.NOTE] [Azure IoT Hub SDK] [ lnk-sdks] gestire automaticamente il recupero URI SAS, il caricamento del file e notifica IoT Hub di completamente caricato.

## <a name="initialize-a-file-upload"></a>Inizializzare il caricamento di un file

IoT Hub dispone di un estremo specificamente per dispositivi richiedere un URI SAS per lo spazio di archiviazione per caricare un file. Il dispositivo avvia il processo di caricamento di file inviando un POST per hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files` con il corpo JSON seguente:

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub restituisce le seguenti, che il dispositivo utilizzato per caricare il file:

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Obsoleto: inizializzare il caricamento di un file con un GET

> [AZURE.NOTE] In questa sezione descrive le funzionalità obsolete per la procedura ricevere un URI SAS dall'IoT Hub. Utilizzare il metodo POST descritto in precedenza.

IoT Hub include due resto i punti finali al supporto tecnico di caricamento di file, uno per ottenere l'URI SAS per lo spazio di archiviazione e l'altra per ricevere una notifica hub IoT della completamente caricato. Il dispositivo avvia il processo di caricamento di file inviando un GET a hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Hub restituisce un URI SAS specifiche per il file da caricare e un ID di correlazione da utilizzare una volta completato il caricamento.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifica IoT Hub di caricamento di un file completato

Il dispositivo è tenuto a caricare il file allo spazio di archiviazione tramite SDK di spazio di archiviazione Azure. Una volta completato il caricamento, il dispositivo invia un POST a hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` con il corpo JSON seguente:

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Il valore di `isSuccess` è un valore Boolean che rappresenta il file è stato caricato o meno. Il codice di stato per `statusCode` è stato per il caricamento di file allo spazio di archiviazione e la `statusDescription` corrisponde alla `statusCode`.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sul caricamento di file da un dispositivo.

## <a name="file-upload-notifications"></a>Notifiche di caricamento di file

Quando un dispositivo che consente di caricare un file e notifica IoT Hub di completamento di caricamento, il servizio genera facoltativamente un messaggio di notifica che contiene la posizione di archiviazione e nome del file.

Come illustrato in [endpoint][lnk-endpoints], IoT Hub recapita notifiche di caricamento di file tramite un endpoint servizio esposto (**/messages/servicebound/fileuploadnotifications**) come messaggi. La semantica di ricezione per le notifiche di caricamento di file sono gli stessi messaggi cloud al dispositivo e avere la stessa [ciclo di vita del messaggio][lnk-lifecycle]. Ciascun messaggio recuperato dall'endpoint di notifica di caricamento di file è un record JSON con le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTimeUtc | Indicazione quando è stata creata la notifica. |
| DeviceId | **DeviceId** del dispositivo in cui caricata il file. |
| BlobUri | URI del file caricato. |
| BlobName | Nome del file caricato. |
| LastUpdatedTime | Indicazione dell'ultimo aggiornamento il file. |
| BlobSizeInBytes | Dimensioni del file caricato. |

**Esempio**. Si tratta di un corpo di esempio di un messaggio di notifica di caricamento di file.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opzioni di configurazione di notifica upload file

Ogni hub IoT esposti le seguenti opzioni di configurazione per le notifiche di caricamento di file:

| Proprietà | Descrizione | Intervallo e predefinito |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controlla se le notifiche di caricamento di file vengono scritte all'endpoint notifiche del file. | Bool. Predefinito: True. |
| **fileNotifications.ttlAsIso8601** | TTL predefinito per le notifiche di caricamento di file. | ISO_8601 intervallo 48 H (minimo 1 minuto). Predefinito: 1 ora. |
| **fileNotifications.lockDuration** | Bloccare la durata della coda di notifiche Carica file. | 5-300 secondi (minimi 5 secondi). Predefinito: 60 secondi. |
| **fileNotifications.maxDeliveryCount** | Conteggio di recapito massimo per il file caricare coda di notifica. | da 1 a 100. Predefinito: 100. |

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è stato illustrato come caricare file da dispositivi con IoT Hub, potrebbero interessare gli argomenti della Guida per gli sviluppatori seguenti:

- [Gestione delle identità dispositivo IoT hub][lnk-devguide-identities]
- [Controllare l'accesso alle IoT Hub][lnk-devguide-security]
- [Utilizzare due facce di dispositivo per sincronizzare stato e configurazioni][lnk-devguide-device-twins]
- [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare nell'esercitazione IoT Hub che segue:

- [Come caricare file da dispositivi nel cloud con IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
