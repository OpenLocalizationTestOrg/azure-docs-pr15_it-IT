<properties
 pageTitle="Guida per sviluppatori - processi | Microsoft Azure"
 description="Guida di sviluppo di Azure IoT Hub - pianificazione processi da eseguire in più dispositivi connessi a un centro di raccolta"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Pianificare processi in più dispositivi (preview)

## <a name="overview"></a>Panoramica

Come descritto in articoli precedenti, Azure IoT Hub consente a un numero di blocchi predefiniti ([tag e proprietà dei dispositivi doppi] [ lnk-twin-devguide] e [i metodi di cloud per dispositivo][lnk-dev-methods]).  In genere le applicazioni di back-end IoT consentono agli amministratori di dispositivo e operatori di aggiornamento e interagire con i dispositivi IoT in blocco e un'ora pianificata.  Processi includono l'esecuzione del dispositivo doppi aggiornamenti e i metodi di C2D rispetto a un set di dispositivi in fase di pianificazione.  Ad esempio, un operatore da utilizzare un'applicazione di back-end che avviare e tenere traccia di un processo per riavviare una serie di dispositivi creazione 43 e planimetrie 3 in un momento che non sarebbe interrompa le operazioni dell'edificio.

### <a name="when-to-use"></a>Quando utilizzare

Valutare la possibilità di utilizzo processi al: un back-soluzione end esigenze aziendali per pianificare e tenere traccia dello stato le operazioni seguenti su un set di dispositivo:

- Aggiornare le proprietà doppi bene accolta del dispositivo
- Aggiornare i tag doppi del dispositivo
- Richiamare metodi C2D

## <a name="job-lifecycle"></a>Ciclo di vita del processo

Processi avviati da Esplora back-end e gestiti dall'IoT Hub.  È possibile avviare un processo tramite un URI servizio esposto (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) e query per lo stato di avanzamento in un processo in esecuzione tramite un URI servizio esposto (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Una volta che viene avviato un processo, invio di query per i processi consentiranno all'applicazione di back-end aggiornare lo stato dei processi in esecuzione.

> [AZURE.NOTE] Quando si avvia un processo, nomi di proprietà e i valori possono contenere US ASCII stampabile caratteri alfanumerici, ad eccezione di quelli le seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sull'utilizzo dei processi.

## <a name="jobs-to-execute-direct-methods"></a>Processi per eseguire i metodi di accesso diretti

Di seguito è riportato i dettagli della richiesta HTTP 1.1 per l'esecuzione di un [metodo diretto] [ lnk-dev-methods] su un set di dispositivi tramite un processo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Processi di aggiornamento proprietà doppi dei dispositivi

Di seguito è i dettagli della richiesta HTTP 1.1 per l'aggiornamento delle proprietà di un doppio dispositivo utilizzando un processo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Invio di query per lo stato di avanzamento per i processi

Di seguito è riportato i dettagli della richiesta HTTP 1.1 per [eseguire la query per i processi][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
Il continuationToken viene fornito dalla risposta.  

## <a name="jobs-properties"></a>Proprietà dei processi

Di seguito è un elenco di proprietà e le descrizioni corrispondenti, che possono essere utilizzate durante la ricerca per processi o risultati dei processi.

| Proprietà | Descrizione |
| -------------- | -----------------|
| **ID processo** | Applicazione fornito ID per il processo. |
| **ora di inizio** | Applicazione fornito ora di inizio (ISO8601) per il processo. |
| **Ora fine** | IoT Hub fornito data (ISO8601) per il completamento del processo. Valido solo dopo che il processo raggiunge lo stato 'completato. | 
| **tipo** | Tipi di processi: |
| | **scheduledUpdateTwin**: un processo utilizzato per aggiornare un insieme di proprietà doppi bene accolta o contrassegni. |
| | **scheduledDeviceMethod**: un processo utilizzato per richiamare un metodo di dispositivo in un set di doppi. |
| **stato** | Stato corrente del processo. Valori possibili per stato: |
| | **in sospeso** : pianificato in attesa di recuperare tramite il servizio processo. |
| | **programmata** : pianificate per un'ora in futuro. |
| | **in esecuzione** : processo attualmente attivo. |
| | **annullato** : processo è stato annullato. |
| | **non è riuscito** : processo non è riuscito. |
| | **completamento** : completamento del processo. |
| **deviceJobStatistics** | Statistiche sull'esecuzione del processo. |

Durante l'anteprima, l'oggetto deviceJobStatistics è disponibile solo dopo il completamento del processo.

| Proprietà | Descrizione |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Numero dei dispositivi nel processo. |
| **deviceJobStatistics.failedCount** | Numero dei dispositivi in cui il processo non è riuscito. |
| **deviceJobStatistics.succeededCount** | Numero dei dispositivi in cui è stata eseguita correttamente il processo. |
| **deviceJobStatistics.runningCount** | Numero di dispositivi attualmente in esecuzione del processo. |
| **deviceJobStatistics.pendingCount** | Numero dei dispositivi in sospeso per eseguire il processo. |


### <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare nell'esercitazione IoT Hub che segue:

- [Pianificazione e la trasmissione di processi][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
