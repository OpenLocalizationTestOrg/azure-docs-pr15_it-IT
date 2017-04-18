> [AZURE.SELECTOR]
- [Node](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introduzione

Due facce dispositivo sono documenti JSON che memorizzano le informazioni sullo stato dispositivo (metadati, configurazioni e condizioni). IoT Hub persiste doppi un dispositivo per ogni dispositivo che si connette a IoT Hub.

Usare due facce di dispositivo per:

* Archiviare metadati dispositivo dal back-end.
* Report informazioni sullo stato corrente, ad esempio le funzionalità disponibili e condizioni (ad esempio, il metodo di integrazione applicativa utilizzato) dell'App dispositivo.
* Sincronizzare lo stato dei flussi di lavoro lunga (ad esempio gli aggiornamenti di firmware e configurazione) tra app dispositivo e back-end.
* Il dispositivo metadati, configurazione o stato della query.

> [AZURE.NOTE] Due facce dispositivo sono progettate per la sincronizzazione e per la ricerca di condizioni e configurazioni dei dispositivi. Utilizzare [i messaggi di dispositivo nel cloud] [ lnk-d2c] per le sequenze di eventi timestamp (ad esempio flussi di telemetria di dati basate sul tempo sensore) e [i metodi di cloud per dispositivo] [ lnk-methods] per un controllo interattivo dei dispositivi, ad esempio attivare una ventola da un'app controllata dall'utente.

Due facce dispositivo vengono memorizzati in un hub IoT e contengono:

* *tag*dispositivo metadati accessibili solo tramite back-end;
* *proprietà desiderate*, oggetti JSON modificabili dal back-end e visibili dall'app dispositivo; e
* *proprietà segnalate*oggetti JSON modificabili dall'app di dispositivo e leggibili da parte posteriore terminare. Tag e proprietà non possono contenere matrici, ma possono essere nidificati oggetti.

![][img-twin]

Inoltre, il back-end app eseguire una query due facce di dispositivo in base a tutti i dati indicati.
Fare riferimento a [due facce dispositivo comprendere] [ lnk-twins] per ulteriori informazioni sulle due facce dispositivo e al [linguaggio di query IoT Hub] [ lnk-query] Guida di riferimento per la ricerca.

> [AZURE.NOTE] In questa fase, due facce dispositivo sono accessibili solo da dispositivi che eseguono la connessione a IoT Hub mediante il protocollo MQTT. Consultare per [supportare MQTT] [ lnk-devguide-mqtt] articolo per istruzioni su come convertire app dispositivo esistente da utilizzare MQTT.

In questa esercitazione verrà illustrato a:

- Creare un'app di back-end che consente di aggiungere *tag* a un doppi dispositivo e un dispositivo simulato che segnala il canale di integrazione applicativa come una *proprietà segnalato* per doppi dispositivo.
- Query dispositivi dell'App back-end utilizzando i filtri del tag e proprietà creata in precedenza.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md