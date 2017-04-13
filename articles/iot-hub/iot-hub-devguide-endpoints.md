<properties
 pageTitle="Guida per sviluppatori - endpoint IoT Hub | Microsoft Azure"
 description="Guida per sviluppatori di IoT Hub Azure - informazioni di riferimento sugli endpoint IoT Hub"
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

# <a name="reference---iot-hub-endpoints"></a>Guida di riferimento - endpoint IoT Hub

## <a name="list-of-iot-hub-endpoints"></a>Elenco di endpoint IoT Hub

Azure IoT Hub è un servizio multi-tenant esposti le funzionalità ai vari operatori. Il diagramma seguente illustra i vari endpoint se IoT Hub esegue.

![Punti finali IoT Hub][img-endpoints]

Di seguito è una descrizione dei punti finali:

* **Provider di risorse**. Il provider di risorse IoT Hub presenta un [Manager delle risorse Azure] [ lnk-arm] interfaccia che consente ai proprietari di Azure abbonamento creare ed eliminare IoT hub e aggiornare le proprietà hub IoT. Proprietà IoT Hub regolano [criteri di sicurezza a livello di hub][lnk-accesscontrol], anziché il controllo dell'accesso a livello di dispositivo e funzionale opzioni per la messaggistica cloud al dispositivo e al dispositivo nel cloud. Il provider di risorse IoT Hub consente inoltre esportare [identità dispositivo][lnk-importexport].
* **Gestione delle identità dispositivo**. Ogni hub IoT esposto un insieme di endpoint HTTP resto per gestire le identità dispositivo (creare, recuperare, aggiornare ed eliminare). [Identità dispositivo] [ lnk-device-identities] vengono utilizzati per il controllo di autenticazione e accesso dispositivo.
* **Gestione di dispositivi doppi**. Ogni hub IoT esposto un insieme di servizio esposto endpoint HTTP resto della query e aggiornare [due facce dispositivo] [ lnk-twins] (aggiornamento tag e proprietà).
* **Gestione dei processi**. Ogni hub IoT esposto un insieme di endpoint HTTP resto servizio esposto per eseguire query e gestire i [processi di][lnk-jobs].
* **I punti finali dispositivo**. Per ogni dispositivo viene completato il provisioning del Registro di sistema di identità di dispositivo, IoT Hub esposto un insieme di endpoint che è possibile utilizzare un dispositivo per inviare e ricevere messaggi:
    - *Inviare messaggi di dispositivo nel cloud*. Utilizzare l'endpoint per [inviare messaggi di dispositivo nel cloud][lnk-d2c].
    - *Ricezione di messaggi cloud al dispositivo*. Un dispositivo viene usata l'endpoint per ricevere target [messaggi cloud al dispositivo][lnk-c2d].
    - *Avviare il caricamento di file*. Per ottenere un URI SA lo spazio di archiviazione di Azure dall'IoT Hub per [caricare un file di]un dispositivo utilizza l'endpoint[lnk-upload].
    - *Recuperare e le proprietà di un aggiornamento doppio*. Un dispositivo utilizza questo endpoint per accedere a relativa [doppi dispositivo][lnk-twins]della proprietà.
    - *Le richieste di metodi diretti ricezione*. Un dispositivo Usa questo endpoint per ascoltare i [metodi diretti][lnk-methods]di richieste.

    Questi endpoint vengono esposti utilizzando [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [1.0 AMQP] [ lnk-amqp] protocolli. Si noti che AMQP è disponibile anche su [WebSockets] [ lnk-websockets] sulla porta 443.
    
    Endpoins dei due facce e dei metodi sono disponibili solo con [MQTT v3.1.1][lnk-mqtt].

* **Endpoint del servizio**. Ogni hub IoT esposto un insieme di endpoint che back-end l'applicazione consente di comunicare con i dispositivi. Questi endpoint sono attualmente solo attualmente esposta tramite [AMQP] [ lnk-amqp] protocollo, fatta eccezione per l'endpoint di chiamata metodo esposto tramite HTTP 1.1.
    - *Ricezione di messaggi dispositivo nel cloud*. L'endpoint è compatibile con [Gli hub evento Azure][lnk-event-hubs]. Un servizio di back-end può essere utilizzata per leggere tutti i [messaggi di dispositivo nel cloud] [ lnk-d2c] provenienti dai dispositivi.
    - *Inviare messaggi cloud al dispositivo e ricevere conferme di recapito*. Questi endpoint attivare l'applicazione back-end invia affidabile [messaggi cloud per dispositivo][lnk-c2d]e riceverà il recapito corrispondente o riconoscimenti scadenza.
    - *Ricevere notifiche di file*. Questo endpoint messaggistica consente di ricevere notifiche quando i dispositivi correttamente caricare un file. 
    - *Chiamata metodo diretto*. In questo punto finale consente a un servizio di back-end richiamare un [metodo diretto] [ lnk-methods] in un dispositivo.

[API Hub IoT e SDK] [ lnk-sdks] articolo vengono illustrati i vari modi per accedere a questi endpoint.

Infine, è importante tenere presente che tutti gli endpoint IoT Hub utilizzano [TLS] [ lnk-tls] protocollo e nessun endpoint mai viene esposto su canali non crittografato/non protetta.

## <a name="field-gateways"></a>Gateway di campo

In una soluzione IoT un *gateway campo* si trova tra i dispositivi e gli endpoint IoT Hub. È in genere si trova vicino i dispositivi. I dispositivi comunicano direttamente con il gateway di campo utilizzando un protocollo supportato per i dispositivi. Il gateway campo si connette a un endpoint IoT Hub utilizza un protocollo supportato da IoT Hub. Un gateway di campo può essere hardware altamente specializzate o un computer power bassa l'esecuzione di software che esegue lo scenario-to-end a cui è destinato il gateway.

È possibile utilizzare [Azure IoT Gateway SDK] [ lnk-gateway-sdk] per implementare un gateway di campo. Questo SDK offre funzionalità specifiche, ad esempio la possibilità di dover riutilizzare la comunicazione da più dispositivi nella stessa connessione IoT Hub.

## <a name="next-steps"></a>Passaggi successivi

Altri argomenti in questa guida per sviluppatori IoT Hub includono:

- [Linguaggio di query per due facce, metodi e processi][lnk-devguide-query]
- [Quote e limitazioni][lnk-devguide-quotas]
- [Supporto IoT Hub MQTT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md