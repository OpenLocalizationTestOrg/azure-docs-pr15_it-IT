<properties
 pageTitle="Guida per sviluppatori - glossario | Microsoft Azure"
 description="Un glossario dei termini comuni relativi a IoT Hub"
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

# <a name="glossary-of-iot-hub-terms"></a>Glossario di termini IoT Hub

In questo articolo sono elencati alcuni dei termini comuni associati IoT Hub.

## <a name="advanced-message-queueing-protocol-amqp"></a>Protocollo Accodamento avanzate (AMQP)

[AMQP](https://www.amqp.org/) corrisponde a uno dei protocolli di messaggistica che supporta IoT Hub per la comunicazione con i dispositivi. Per ulteriori informazioni sui protocolli di messaggistica che supporta l'IoT Hub, vedere [inviare e ricevere messaggi con IoT Hub](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Cloud-dispositivo (C2D)

In genere utilizzato per fare riferimento ai messaggi inviati dall'IoT Hub a un dispositivo connesso. Spesso, questi messaggi sono i comandi che indicano il dispositivo per eseguire un'azione. Per ulteriori informazioni, vedere [inviare e ricevere messaggi con IoT Hub](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condizione

Fa riferimento a informazioni sullo stato dispositivo, ad esempio il metodo di integrazione applicativa attualmente in uso, come riportato da un'app per dispositivi. Dispositivi possono inoltre inviare una segnalazione le relative funzionalità. È possibile eseguire una query condizione e funzionalità tramite doppi dispositivo.

## <a name="data-point-message"></a>Messaggio punto dati

Un messaggio di punto dati è un cloud per dispositivo che contiene i dati di telemetria, ad esempio vento o la temperatura.

## <a name="desired-properties"></a>Proprietà desiderate

Nel contesto di due facce dispositivo, proprietà desiderate vengono utilizzate in combinazione con *segnalati proprietà* per la sincronizzazione di configurazione del dispositivo o condizione. Proprietà desiderate possono essere impostate solo dall'applicazione indietro fine e osservano dall'app di dispositivo. 

## <a name="device-to-cloud-d2c"></a>Dispositivo nel cloud (D2C)

In genere utilizzato per fare riferimento ai messaggi inviati da un dispositivo connesso a IoT Hub. Questi messaggi potrebbero essere messaggi [del punto dati](#data-point-message) o [interattivi](#interactive-message) . Per ulteriori informazioni, vedere [inviare e ricevere messaggi con IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registro di sistema di dispositivo identità

Il componente incorporato di un hub IoT contenente informazioni su singoli dispositivi consentito connettersi a un hub è il [Registro di sistema di dispositivo identità](iot-hub-devguide-identity-registry.md) .

## <a name="device"></a>Dispositivo

Nel contesto di IoT, un dispositivo è in genere un dispositivo di elaborazione autonoma piccole dimensioni, che potrebbe raccogliere dati o controlli altri dispositivi. Ad esempio un dispositivo potrebbe essere, un dispositivo di monitoraggio ambientale o un controller per sistemi di nutrirli e ventilazione una serra.

## <a name="device-twin"></a>Doppio dispositivo

Un [doppi dispositivo](iot-hub-devguide-device-twins.md) è una copia in IoT Hub le impostazioni di condizioni e la configurazione di un dispositivo fisico. È possibile utilizzare una coppia di dispositivo per gestire la configurazione del dispositivo fisico.

## <a name="direct-method"></a>Metodo diretto

Un [metodo diretto](iot-hub-devguide-direct-methods.md) è un modo per attivare un metodo per l'esecuzione in un dispositivo chiamando un'API l'hub IoT.

## <a name="event-hub-compatible-endpoint"></a>Endpoint compatibile con Hub di evento

Per leggere dispositivo nel cloud i messaggi inviati a un centro di raccolta IoT, è possibile connettersi a un endpoint l'hub e utilizzare qualsiasi metodo Hub evento compatibile con per leggere i messaggi. Evento compatibile con Hub metodi includono utilizzando l'evento hub SDK e Azure flusso Analitica.

## <a name="field-gateway"></a>Gateway di campo

Un gateway campo consente la connettività per dispositivi che non riesce a connettersi direttamente all'IoT Hub e viene in genere distribuito localmente con i dispositivi. Per ulteriori informazioni, vedere [Novità Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Processo

Back-end la soluzione è possibile utilizzare i processi di pianificare e tenere traccia delle attività in un set di dispositivi registrato con il tuo fulcro IoT. Le attività includono aggiornamento proprietà doppi bene accolta dei dispositivi, l'aggiornamento tag doppi del dispositivo e il richiamo di metodi di accesso diretti.

## <a name="protocol-gateway"></a>Gateway protocollo

Gateway di protocollo viene in genere distribuito nel cloud e offre servizi di traduzione per dispositivi che si connettono a IoT Hub protocollo. Per ulteriori informazioni, vedere [Novità Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Messaggio interattivo

Un messaggio interattivo è un messaggio cloud al dispositivo che attiva un'azione immediata nel applicazione back-end. Ad esempio un dispositivo potrebbe inviare un avviso relativi a un errore che deve essere registrato automaticamente in un sistema CRM.

## <a name="iot-hub"></a>Hub IoT

IoT Hub è un servizio di Azure completamente gestito che consente la comunicazione sicura e attendibile bidirezionale tra milioni di dispositivi IoT e una soluzione back-end. Per ulteriori informazioni, vedere [Novità Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>Famiglia di prodotti IoT

Azure IoT Suite pacchetti insieme più servizi Azure con soluzioni preconfigurate. Queste soluzioni preconfigurate consentono di iniziare rapidamente implementazioni-to-end scenari IoT comuni. Per ulteriori informazioni, vedere [Novità Azure IoT famiglia?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Processo

Un [processo](iot-hub-devguide-jobs.md) IoT hub consente di eseguire operazioni, ad esempio un firmware aggiornamento su più dispositivi connessi a un centro di raccolta.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) corrisponde a uno dei protocolli di messaggistica che supporta IoT Hub per la comunicazione con i dispositivi. Per ulteriori informazioni sui protocolli di messaggistica che supporta l'IoT Hub, vedere [inviare e ricevere messaggi con IoT Hub](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Proprietà segnalate

Nel contesto di due facce dispositivo, proprietà segnalate vengono utilizzate in combinazione con *le proprietà desiderate* per la sincronizzazione di configurazione del dispositivo o condizione. Proprietà segnalate può essere impostata solo dall'app di dispositivo e possano leggere ed eseguire una query mediante applicazione back-end.

## <a name="tags"></a>Tag

Nel contesto di devcie due facce, tag sono dispositivo metadati archiviati e recuperati da applicazione back-end in forma di un documento JSON. Tag non sono visibili alle App in un dispositivo.

## <a name="system-properties"></a>Proprietà-sistema

Nel contesto di due facce dispositivo, proprietà-sistema sono di sola lettura e includere informazioni relativi all'utilizzo di dispositivo, ad esempio lo stato di tempi e connessione di ultima attività.