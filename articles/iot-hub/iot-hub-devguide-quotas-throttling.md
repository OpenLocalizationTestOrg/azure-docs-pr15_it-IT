<properties
 pageTitle="Guida per sviluppatori - quote e limitazioni | Microsoft Azure"
 description="Guida di sviluppo IoT Hub Azure - descrizione delle quote da applicare a IoT Hub e limitazione previsto"
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

# <a name="reference---quotas-and-throttling"></a>Guida di riferimento - quote e limitazioni

## <a name="quotas-and-throttling"></a>Quote e limitazioni

Ogni abbonamento Azure può includere al massimo 10 IoT hub.

Ogni hub IoT viene effettuato il provisioning con un determinato numero di unità in una specifica SKU (per ulteriori informazioni, vedere [Azure IoT Hub prezzi][lnk-pricing]). SKU e il numero di unità di determinare la quota giornaliera massima dei messaggi che è possibile inviare.

Lo SKU determina inoltre i limiti limitazioni IoT Hub vengono applicate in tutte le operazioni.

## <a name="operation-throttles"></a>Limita operazione

Operazione limita alcune limitazioni tasso che sono applicate in intervalli minuti e previsto per evitare frodi. IoT Hub tenta di evitare che venga restituito errori ogni volta che è possibile, ma viene avviato restituzione eccezioni la limitazione di caso di violazione troppo a lungo.

Di seguito è l'elenco di imposta limita. Valori si riferiscono a un hub singoli.

| Limitazione | Valore-hub |
| -------- | ------------- |
| Operazioni del Registro di sistema di identità (creazione, recuperare, elenco, aggiornare ed eliminare) | 5000/min/unità (per S3) <br/> 100/min/unità (per S1 e S2). |
| Connessioni ai dispositivi | 6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Almeno 100/sec. <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si ha almeno 100/sec attraverso le unità. Con nove S1 unità, si dispone di 108/sec (9\*12) tra le unità. |
| Invia dispositivo nel cloud | 6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Almeno 100/sec. <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si ha almeno 100/sec attraverso le unità. Con nove S1 unità, si dispone di 108/sec (9\*12) tra le unità. |
| Invia cloud al dispositivo | 5000/min/unità (per S3), 100/min/unità (per S1 e S2). |
| Cloud per dispositivo riceve | min/50000/unità (per S3), 1000/min/unità (per S1 e S2). |
| Operazioni di caricamento di file | file di 5000 caricare le notifiche/min/unità (per S3), 100 file upload notifiche/min/unità (per S1 e S2). <br/> URI SA 10000 può essere indietro per un account di archiviazione Azure contemporaneamente.<br/> URI SA 10/dispositivo può essere fuori contemporaneamente. | 

È importante chiarire che la limitazione di *connessioni ai dispositivi* determina il tasso in cui è possono stabilire connessioni ai nuovi dispositivi con un hub IoT e non il numero massimo di dispositivi connessi contemporaneamente. La limitazione dipende dal numero di unità viene effettuato il provisioning per l'hub.

Ad esempio, se si acquista un'unica S1, viene visualizzato un velocità di 100 connessioni al secondo. Questo errore indica che per connettere 100.000 dispositivi, richiede almeno 1000 secondi (circa 16 minuti). Tuttavia, è possibile impostare come molti dispositivi connessi contemporaneamente se si dispone di dispositivi registrati nel Registro di sistema identità dispositivo.

Per informazioni dettagliate sulla IoT Hub limitazione comportamento, vedere il post del blog [IoT Hub limitazione e si][lnk-throttle-blog].

>[AZURE.NOTE] In qualsiasi momento, è possibile aumentare le quote o dei limiti di velocità aumentando il numero di unità di provisioning in un hub IoT.

>[AZURE.IMPORTANT] Operazioni del Registro di sistema di identità è destinate in fase di esecuzione di gestione di dispositivi e scenari di provisioning. Leggere o aggiornare un numero elevato di identità dispositivo è supportata tramite [importare ed esportare i processi][lnk-importexport].

## <a name="next-steps"></a>Passaggi successivi

Altri argomenti in questa guida per sviluppatori IoT Hub includono:

- [Punti finali IoT Hub][lnk-devguide-endpoints]
- [Linguaggio di query per due facce, metodi e processi][lnk-devguide-query]
- [Supporto IoT Hub MQTT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md