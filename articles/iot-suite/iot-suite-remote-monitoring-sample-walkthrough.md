<properties
 pageTitle="Monitoraggio remoto preconfigurato procedura dettagliata soluzione | Microsoft Azure"
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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Monitoraggio remoto preconfigurato soluzione dettagliata

## <a name="introduction"></a>Introduzione

Telecomando IoT Suite monitoraggio [preconfigurato soluzione] [ lnk-preconfigured-solutions] è un'implementazione di un-to-end monitoraggio soluzione per più computer che esegue in postazioni remote. La soluzione combina servizi Azure chiave per fornire un'implementazione generica dello scenario business e si può usare come punto di partenza per la propria implementazione. È possibile [personalizzare] [ lnk-customize] la soluzione per soddisfare esigenze specifiche.

In questo articolo illustra alcuni degli elementi principali della soluzione di monitoraggio remoto che consentono di comprendere il funzionamento. Questa conoscenza consente di:

- Risoluzione dei problemi della soluzione.
- Pianificare le modalità di personalizzazione per la soluzione per soddisfare esigenze specifiche. 
- Progettare IoT soluzione che utilizza servizi Azure.

## <a name="logical-architecture"></a>Architettura logica

Nel diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![Architettura logica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Dispositivi simulati

Soluzione preconfigurato, il dispositivo simulato rappresenta un dispositivo raffreddamento (ad esempio un edificio condizionamento o un'unità gestione air le strutture). Quando si distribuisce la soluzione preconfigurata, viene automaticamente effettuato il provisioning quattro simulati dispositivi che eseguono un [Azure WebJob][lnk-webjobs]. I dispositivi simulati semplificano esplorare il comportamento della soluzione senza la necessità di distribuire eventuali dispositivi fisici. Per distribuire un dispositivo fisico reali, vedere [connettere il dispositivo per il computer remoto monitoraggio soluzione preconfigurato] [ lnk-connect-rm] esercitazione.

Ogni dispositivo simulato può inviare i tipi di messaggio seguenti a IoT Hub:

| Messaggio  | Descrizione |
|----------|-------------|
| Avvio  | Quando si avvia il dispositivo, viene inviato un messaggio di **informazioni sul dispositivo** contenente informazioni sul proprio back-end. Questi dati includono l'id del dispositivo, i metadati di dispositivo, un elenco dei comandi supporta dispositivo e la configurazione corrente del dispositivo. |
| Presenza | Un dispositivo periodicamente invia un messaggio di **presenza** per segnalare se il dispositivo è possibile grado di rilevare la presenza di un sensore. |
| Telemetria | Un dispositivo periodicamente invia un messaggio di **telemetria** che segnala simulati valori per la temperatura e umidità acquisite dal sensori simulata del dispositivo. |


I dispositivi simulati inviare le seguenti proprietà dei dispositivi in un messaggio di **informazioni sul dispositivo** :

| Proprietà               |  Scopo |
|------------------------|--------- |
| ID dispositivo              | ID che viene fornito o assegnato al momento della creazione di un dispositivo della soluzione. |
| Produttore           | Produttore del dispositivo |
| Numero di modello           | Numero di modello del dispositivo |
| Numero seriale          | Numero seriale del dispositivo |
| Firmware               | Versione corrente del firmware sul dispositivo |
| Piattaforma               | Architettura della piattaforma del dispositivo |
| Processore              | Processore il dispositivo |
| RAM installata          | Quantità di RAM installata nel dispositivo |
| Stato di attivazione hub      | Proprietà stato IoT Hub del dispositivo |
| Ora creazione           | Ora di che creazione della soluzione il dispositivo |
| Ora aggiornata           | Ora ultima aggiornare le proprietà per il dispositivo |
| Latitudine               | Posizione di latitudine del dispositivo |
| Longitudine              | Posizione di longitudine del dispositivo |

Il simulatore semi queste proprietà in dispositivi simulati con valori di esempio.  Ogni volta che il simulatore consente di inizializzare un dispositivo simulato, il dispositivo invia i metadati predefiniti IoT Hub. Si noti come questa sovrascrive gli aggiornamenti di metadati eseguiti nel portale di dispositivo.


I dispositivi simulati possono gestire i comandi seguenti inviati dal dashboard soluzione attraverso l'hub IoT:

| Comando                | Descrizione                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Invia un _ping_ al dispositivo sia attivo   |
| StartTelemetry         | Avvia il dispositivo l'invio di telemetria                 |
| StopTelemetry          | Interrompere il dispositivo dall'invio di telemetria             |
| ChangeSetPointTemp     | Modifica il valore set intorno al quale viene generati dati casuali |
| DiagnosticTelemetry    | Attiva il simulatore di dispositivo per inviare un valore di telemetria aggiuntive (externalTemp) |
| ChangeDeviceState      | Modifica una proprietà estese sullo stato per il dispositivo e invia il messaggio di informazioni dispositivo dal dispositivo |

Il riconoscimento comando dispositivo al back-end soluzione viene fornito tramite hub IoT.

## <a name="iot-hub"></a>Hub IoT

[Hub IoT] [ lnk-iothub] Flash la Compact Flash dati inviati da dispositivi nel cloud e rende disponibili per i processi di Azure flusso Analitica (ASA). Hub IoT invia anche i comandi per i dispositivi per conto del portale di dispositivo. Ogni processo ASA flusso utilizza un gruppo di consumer IoT Hub distinto per leggere il flusso di messaggi dai dispositivi.

## <a name="azure-stream-analytics"></a>Flusso Azure Analitica

In soluzione di monitoraggio remoto, [Azure flusso Analitica] [ lnk-asa] (ASA) invia messaggi dispositivo ricevuti dall'hub IoT ad altri componenti di back-end per l'elaborazione o lo spazio di archiviazione. Processi ASA diversi funzioni specifiche in base al contenuto dei messaggi.

**Processo 1: informazioni sul dispositivo** Filtra i messaggi di informazioni dispositivo dal flusso di messaggi in arrivo e li invia a un endpoint Hub evento. Un dispositivo invia messaggi di informazioni dispositivo all'avvio e in risposta a un comando **SendDeviceInfo** . Questo processo viene utilizzata la seguente definizione di query per identificare i messaggi di **informazioni sul dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Consente di inviare l'output a un Hub di eventi per ulteriori elaborazioni.

**Processo 2: regole** restituiscono valori in arrivo di telemetria di temperatura e umidità con soglie per dispositivo. I valori soglia sono impostati nell'editor delle regole disponibili nel dashboard soluzione. Ogni coppia di dispositivo/valore archiviata in timestamp in un blob che legge Analitica flusso dei **Dati**di riferimento. Il processo Confronta un valore non vuoto con la soglia specificata per il dispositivo. Se supera il ' >' condizione, il processo genera un evento di **avviso** che indica che la soglia viene superata e fornisce il dispositivo, valore e i valori timestamp. Questo processo viene utilizzata la seguente definizione di query per identificare i messaggi di telemetria che devono attivare un avviso:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Il processo l'output viene inviato a un Hub di eventi per ulteriori elaborazioni e consente di salvare i dettagli di tutti gli avvisi a archiviazione blob nel punto in cui il dashboard soluzione possibile leggere le informazioni di avviso.

**Processo 3: telemetria** interviene il flusso di telemetria di dispositivo in ingresso in due modi. Il primo invia tutti i messaggi di telemetria dei dispositivi di a archiviazione blob persistente per lo spazio di archiviazione a lungo termine. Il secondo calcola i valori Media, minimo e massimo umidità in una finestra scorrevole di cinque minuti e invia dati a archiviazione blob. Dashboard di soluzione legge i dati di telemetria dallo spazio di archiviazione blob per popolare i tipi di grafici. Questo processo viene utilizzata la seguente definizione di query:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Hub di evento

I processi ASA **dispositivo informazioni** e **le regole** di output i dati a un hub di evento in modo affidabile inoltrare all' **Evento processore** in esecuzione nel WebJob.

## <a name="azure-storage"></a>Spazio di archiviazione Azure

La soluzione utilizza archiviazione blob Azure per tutti i dati di telemetria non elaborati e riepilogati da dispositivi della soluzione. Dashboard di legge i dati di telemetria dallo spazio di archiviazione blob per popolare i tipi di grafici. Per visualizzare gli avvisi, il dashboard legge i dati dallo spazio di archiviazione blob che registra quando i valori di telemetria supera i valori soglia configurata. La soluzione utilizza anche archiviazione blob per registrare i valori soglia impostati nel dashboard.

## <a name="webjobs"></a>WebJobs

Oltre a hosting simulatori di dispositivo, WebJobs della soluzione ospitare anche **Processore eventi** in esecuzione in un WebJob Azure che gestisce i messaggi di informazioni dispositivo e le risposte di comando. Utilizza:

- Messaggi di informazioni dispositivo per l'aggiornamento del Registro di sistema di dispositivo (archiviato nel database DocumentDB) con le informazioni sul dispositivo corrente.
- Messaggi di risposta del comando per aggiornare la cronologia dei comandi dispositivo (archiviata nel database DocumentDB).

## <a name="documentdb"></a>DocumentDB

La soluzione utilizza un database DocumentDB per memorizzare le informazioni sui dispositivi connessi alla soluzione. Queste informazioni includono i metadati e la cronologia di comandi inviato ai dispositivi nel dashboard.

## <a name="web-apps"></a>Web App

### <a name="remote-monitoring-dashboard"></a>Dashboard di monitoraggio remoto
Questa pagina nell'applicazione web utilizza i controlli di ottenere informazioni javascript (vedere [ottenere informazioni e gli elementi visivi repo](https://www.github.com/Microsoft/PowerBI-visuals)) per visualizzare i dati di telemetria dei dispositivi di. La soluzione utilizza il processo di telemetria ASA per scrivere i dati di telemetria nell'archiviazione blob.


### <a name="device-administration-portal"></a>Portale di amministrazione del dispositivo

Questa applicazione web consente di:

- Effettuare il provisioning di un nuovo dispositivo. Questa azione imposta l'id univoco del dispositivo e genera la chiave di autenticazione. Scrive informazioni sul dispositivo per il Registro di sistema di identità IoT Hub e il database di DocumentDB specifici.
- Gestire le proprietà del dispositivo. Questa azione include una visualizzazione proprietà esistenti e l'aggiornamento con nuove proprietà.
- Inviare i comandi a un dispositivo.
- Visualizzare la cronologia di comando per un dispositivo.
- Attivare e disattivare i dispositivi.

## <a name="next-steps"></a>Passaggi successivi

I post di blog TechNet seguenti forniscono informazioni più dettagliate della soluzione di preconfigurato monitoraggio remota:

- [Famiglia di prodotti - roba da smanettoni - IoT monitoraggio remoto](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT famiglia di prodotti - monitoraggio remoto - aggiunta dispositivi Live e simulati](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

È possibile continuare Guida introduttiva di famiglia di prodotti IoT, leggere gli articoli seguenti:

- [Connettere il dispositivo per la soluzione preconfigurata monitoraggio remota][lnk-connect-rm]
- [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md