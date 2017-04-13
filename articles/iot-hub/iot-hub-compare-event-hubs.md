<properties
 pageTitle="Confronto tra Azure Hub IoT a un evento Azure hub | Microsoft Azure"
 description="Confronto dei servizi IoT Hub Azure e Azure evento hub evidenziazione differenze funzionali e use case."
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
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Confronto di Azure IoT Hub e hub evento Azure

Uno dei casi di utilizzo principale per IoT Hub è raccogliere telemetria da dispositivi. Per questo motivo, IoT Hub è spesso confrontate con quelle di [Azure evento hub][]. Ad esempio IoT Hub, hub di evento è un evento servizio che consente di eventi e telemetry ingresso nel cloud in scala enorme con latenza bassa e affidabilità di elaborazione.

Tuttavia, i servizi hanno molte differenze, sono riportate nella tabella seguente.

| Area | Hub IoT | Hub di evento |
| ---- | ------- | ---------- |
| Modelli di comunicazione | Attiva Messaggistica dispositivo nel cloud e cloud al dispositivo. | Consente solo ingresso evento (in genere considerato per gli scenari di dispositivo nel cloud). |
| Supporto del protocollo dispositivo | Supporta MQTT, AMQP, AMQP su WebSockets e HTTP. Inoltre, IoT Hub interagisce con il [gateway protocollo Azure IoT][lnk-azure-protocol-gateway], un'implementazione di gateway protocollo personalizzabile per supportare protocolli personalizzati. | Supporta AMQP, AMQP WebSockets e HTTP. |
| Sicurezza | Fornisce il controllo dell'accesso revocabile e identità per dispositivo. Vedere la [sezione sicurezza della Guida per gli sviluppatori IoT Hub]. | Fornisce a livello di evento hub [criteri di accesso condiviso][Event Hubs - security], con revoca limitato supporta tramite i [criteri di publisher][Event Hubs publisher policies]. Soluzioni IoT spesso necessario implementare una soluzione personalizzata per supportare le credenziali per dispositivo e misure anti-spoofing. |
| Monitoraggio delle operazioni | Abilita le soluzioni IoT sottoscrivere una vasta gamma di dispositivo identità gestione e la connettività gli eventi, ad esempio gli errori di autenticazione di singoli dispositivi, limitazione ed eccezioni di formato non valido. Questi eventi consentono di individuare rapidamente i problemi di connettività a livello singolo dispositivo. | Espone solo metrica di aggregazione. |
| Scala | Ottimizzata per supportare milioni di dispositivi collegati contemporaneamente. | Supporta un numero più limitato di connessioni simultanee, fino a 5.000 connessioni AMQP, in base alle [quote Bus di servizio Azure][]. Hub evento mano, consente di specificare la partizione per ogni messaggio inviato. |
| SDK per dispositivi | Fornisce [dispositivo SDK] [ Azure IoT Hub SDKs] per un'ampia varietà di piattaforme e lingue. | È supportata in .NET e C. Vengono forniti anche AMQP e HTTP inviare interfacce. |
| Caricamento di file | Abilita IoT soluzioni caricare i file da dispositivi nel cloud. Include un endpoint di notifica di file per l'integrazione del flusso di lavoro e un monitoraggio delle categorie in fase di debug operazioni. | Usa un modello di controllo Richiedi per richiedere file da dispositivi e fornire dispositivi con una chiave di spazio di archiviazione per le transazioni manualmente. |

In conclusione, anche se il caso di utilizzo solo in ingresso di telemetria di dispositivo nel cloud, IoT Hub fornisce un servizio progettato specificamente per la connettività di dispositivo IoT. Per espandere le proposte di valore per questi scenari con le caratteristiche specifiche IoT continuerà. Hub di evento è progettato per ingresso di evento in una scala enorme entrambi nel contesto di scenari Data Center tra e all'interno del centro.

Non è insolito utilizzare IoT Hub e hub di evento nella stessa soluzione - dove IoT Hub gestisce la comunicazione dispositivo nel cloud e hub evento gestisce ingresso evento fase successiva in motori di elaborazione in tempo reale.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla pianificazione della distribuzione IoT Hub, vedere [proporzioni, HA e DR][lnk-scaling].

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[Hub evento Azure]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Sezione sicurezza della Guida per gli sviluppatori IoT Hub]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quote di Azure Bus di servizio]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
