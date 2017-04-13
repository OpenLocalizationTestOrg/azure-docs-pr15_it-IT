<properties
 pageTitle="Azure IoT Hub proporzioni dei caratteri | Microsoft Azure"
 description="Descrive come ridimensionare Azure IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Ridimensionamento IoT Hub

IoT Hub Azure supportino fino a un milioni di dispositivi connessa contemporaneamente. Per ulteriori informazioni, vedere [IoT Hub prezzi][lnk-pricing]. Ogni unità IoT Hub consente un certo numero di messaggi giornalieri.

Per ridimensionare correttamente la soluzione, è consigliabile l'utilizzo in questione di IoT Hub. In particolare, prendere in considerazione la velocità di picco necessario per le categorie di esecuzione delle operazioni seguenti:

* Messaggi di dispositivo nel cloud
* Messaggi cloud al dispositivo
* Operazioni del Registro di sistema di identità

Oltre a queste informazioni effettiva, vedere [Limita e le quote di IoT Hub][] e progettare una soluzione di conseguenza.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Produttività dispositivo nel cloud e cloud di dispositivo del messaggio

Il modo migliore per ridimensionare una soluzione IoT Hub è di valutare il traffico alla scala cronologica per unità.

I messaggi di dispositivo nel cloud seguono queste linee guida velocità.

| Livello | Velocità | Frequenza di trasmissione prolungato |
| ---- | -------------------- | ------------------- |
| S1 | Fino a KB 1111/minuto unitario<br/>(1,5 GB/giorno/unità) | Media dei messaggi al minuto 278 unitario<br/>(400.000 messaggi/giorno unitario) |
| S2 | Fino a 16 MB/minuto unitario<br/>(22,8 GB/giorno/unità) | Media dei messaggi al minuto 4167 unitario<br/>(6 milioni di messaggi al giorno per unità) |
| S3 | Fino a MB 814/minuto unitario<br/>(1144.4 GB/giorno/unità) | Media dei messaggi al minuto 208,333 unitario<br/>(300 milioni di messaggi/giorno unitario) |

## <a name="identity-registry-operation-throughput"></a>Velocità di trasmissione di identità del Registro di sistema operazione

Operazioni del Registro di sistema di identità IoT Hub non dovrebbero essere le operazioni di runtime, come sono prevalentemente correlati provisioning dispositivo.

Per i numeri delle prestazioni burst specifiche, vedere [Limita e le quote di IoT Hub][].

## <a name="sharding"></a>Sharding

Mentre un singolo hub IoT può scalare a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche non garantisce un singolo hub IoT. In questo caso, è consigliabile suddividere i dispositivi in più IoT hub. Hub IoT più uniforme il traffico burst e ottenere la velocità necessari o tariffe operazione necessari.

## <a name="next-steps"></a>Passaggi successivi

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Limita e le quote di IoT Hub]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
