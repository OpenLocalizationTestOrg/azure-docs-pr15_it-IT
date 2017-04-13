<properties
   pageTitle="Gateway di protocollo IoT Azure | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare Azure IoT protocollo gateway per estendere la funzionalità e il supporto del protocollo di Azure IoT Hub."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Supporto protocolli aggiuntivi per IoT Hub

Azure IoT Hub supporta la comunicazione tramite i protocolli MQTT, AMQP e HTTP. In alcuni casi, dispositivi o campo gateway potrebbe non essere possibile usare uno di questi protocolli standard e richiederà adeguamento protocollo. In tal caso, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato abilitare adeguamento protocollo per gli endpoint IoT Hub bridging il traffico da e verso IoT Hub. È possibile utilizzare il [gateway protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) come gateway personalizzati per abilitare adeguamento protocollo IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Gateway di protocollo IoT Azure

Il gateway di protocollo IoT Azure è una struttura per l'adeguamento protocollo progettato per vasta scala, la comunicazione con IoT Hub dispositivo bidirezionale. Il gateway protocollo è un componente pass-through che accetta connessioni ai dispositivi tramite un protocollo specifico. Supera i limiti il traffico a IoT Hub rispetto a AMQP 1.0. Il gateway di protocollo IoT Azure è disponibile come un progetto software open source per fornire flessibilità per l'aggiunta di supporto per un'ampia varietà di protocolli e versioni del protocollo.

È possibile distribuire il gateway protocollo in Azure in modo scalabilità utilizzando i ruoli di lavoro servizi Cloud Windows Azure. Inoltre, il gateway protocollo distribuire negli ambienti in locale, ad esempio gateway di campo.

Il gateway di protocollo IoT Azure include un adattatore protocollo MQTT che consente di personalizzare il comportamento di protocollo MQTT se necessario. Dall'IoT Hub fornisce supporto incorporato per il protocollo v3.1.1 MQTT, è consigliabile solo utilizzare la scheda protocollo MQTT se si ha la necessità di personalizzazioni protocollo o requisiti specifici per aggiungere ulteriori funzionalità.

Scheda MQTT inoltre illustrato il modello di programmazione per la creazione di schede protocollo per altri protocolli. Inoltre, il modello di programmazione di Azure IoT protocollo gateway consente di collegare nei componenti personalizzati per l'elaborazione specializzata, ad esempio autenticazione personalizzata, trasformazioni messaggio, compressione/decompressione o crittografia/decrittografia del traffico tra i dispositivi e IoT Hub.

Per flessibilità, il gateway protocollo e implementazione MQTT sono incluse in un progetto software open source. In questo modo è possibile personalizzare l'implementazione in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su gateway protocollo IoT Azure e su come utilizzare e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio di gateway protocollo IoT Azure in GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida di Azure IoT protocollo gateway per sviluppatori](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Per ulteriori informazioni sulla pianificazione della distribuzione IoT Hub, vedere:

- [Confrontare con gli hub di evento][lnk-compare]
- [Ridimensionamento, HA e DR][lnk-scaling]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
