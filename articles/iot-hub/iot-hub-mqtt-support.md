<properties
 pageTitle="Supporto IoT Hub MQTT | Microsoft Azure"
 description="Descrizione del MQTT supporto hub a livello di IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Supporto IoT Hub MQTT

IoT Hub consente ai dispositivi di comunicare con i punti finali dispositivo IoT Hub utilizzando [MQTT v3.1.1] [ lnk-mqtt-org] protocollo sulla porta 8883 o v3.1.1 MQTT il protocollo WebSocket sulla porta 443. IoT Hub richiede tutte le comunicazioni di dispositivo deve essere protetto tramite SSL/TLS (quindi IoT Hub non supporta connessioni non protette sulla porta 1883).

## <a name="connecting-to-iot-hub"></a>Connessione a IoT Hub

Un dispositivo è possibile usare il protocollo MQTT per connettersi a un hub IoT utilizzando le relative raccolte in [Microsoft Azure IoT SDK] [ lnk-device-sdks] o usando il MQTT protocollo direttamente.

## <a name="using-the-device-client-sdks"></a>Tramite il dispositivo client SDK

[Client per dispositivi SDK] [ lnk-device-sdks] che supportano il protocollo MQTT sono disponibili per Java, Node, C, c# e Python. Il client di dispositivo SDK consente di stabilire una connessione a un hub IoT la stringa di connessione IoT Hub standard. Per utilizzare il protocollo MQTT, è necessario impostare il parametro del protocollo client per **MQTT**. Per impostazione predefinita, il client di dispositivo SDK connettersi a un IoT Hub con **CleanSession** impostato su **0** e utilizzare **QoS 1** per lo scambio di messaggi con l'hub IoT.

Quando un dispositivo è collegato a un hub IoT, il client di dispositivo SDK forniscono metodi per abilitare il dispositivo per inviare e ricevere messaggi da un hub IoT.

Nella tabella seguente contiene collegamenti a esempi di codice per ogni lingua supportata e specifica il parametro da utilizzare per stabilire una connessione a IoT Hub mediante il protocollo MQTT.

| Lingua                   | Parametro di protocollo        |
| -------------------------- | ------------------------- |
| [Node][lnk-sample-node] | Azure-iot-dispositivo-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>La migrazione di un'app di dispositivo da AMQP a MQTT
Se si utilizza il [client per dispositivi SDK][lnk-device-sdks], il passaggio da tramite AMQP a MQTT richiede la modifica il parametro del protocollo in inizializzazione del client come descritto in precedenza.

In tal caso, assicurarsi di selezionare gli elementi seguenti:

* AMQP restituisce degli errori di molte condizioni, mentre MQTT interrompe la connessione. Di conseguenza le eccezioni logica potrebbero richiedere alcune modifiche.
* MQTT non supporta le operazioni *rifiutare* quando si ricevono [messaggi C2D][lnk-messaging]. Se il back-end deve ricevere una risposta dall'app per dispositivi, è consigliabile utilizzare [metodi diretti][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Mediante il protocollo MQTT direttamente

Se un dispositivo non è possibile utilizzare il client di dispositivo SDK, comunque possibile connettersi a endpoint dispositivo pubblico mediante il protocollo MQTT. Nel pacchetto **CONNETTERE** il dispositivo deve utilizzare i valori seguenti:

- Per il campo **ClientId** utilizzare **deviceId**. 
- Per il campo **nome utente** , utilizzare `{iothubhostname}/{device_id}`, dove {iothubhostname} è il record CName completo di hub IoT.

    Ad esempio, se il nome di un centro di raccolta IoT **contoso.azure devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere `contoso.azure-devices.net/MyDevice01`.

- Per il campo **Password** , utilizzare un token di SA. Il formato del token sa è uguale a quella per i protocolli HTTP sia AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Per ulteriori informazioni su come generare i token SA, vedere la sezione dispositivo [utilizzando IoT Hub token]di sicurezza[lnk-sas-tokens].
    
    Durante la verifica, è anche possibile usare [Esplora dispositivi] [ lnk-device-explorer] strumento per generare rapidamente un token di sa che è possibile copiare e incollare il codice personalizzato:
    
    1. Passare alla scheda **Gestione** in Esplora dispositivi.
    2. Fare clic su **Sa Token** (in alto a destra).
    3. In **SASTokenForm**, selezionare il dispositivo in **DeviceID** elenco a discesa. Impostare il **valore TTL**.
    4. Fare clic su **Genera** per creare il token.
    
    Il token SA generato ha la struttura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La parte del token da utilizzare come campo **Password** per connettersi tramite MQTT:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Per MQTT connettere e disconnettere pacchetti, IoT Hub visualizzerà un evento sul canale **Monitoraggio delle operazioni** con informazioni aggiuntive che consentono di risolvere i problemi di connettività.

### <a name="sending-messages-to-iot-hub"></a>Invio di messaggi da IoT Hub

Dopo aver apportato una connessione, un dispositivo può inviare messaggi a IoT Hub utilizzando `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come **Nome dell'argomento**. Il `{property_bag}` elemento consente l'invio di messaggi con proprietà aggiuntive in un formato con codifica url. Per esempio:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Questo `{property_bag}` elemento utilizza la stessa codifica per le stringhe di query nel protocollo HTTP.

Inoltre è possibile utilizzare l'applicazione client dispositivo `devices/{device_id}/messages/events/{property_bag}` come la **verrà nome dell'argomento** per definire *i messaggi* da inoltrare come messaggio di telemetria.

IoT Hub non supporta i messaggi QoS 2. Se un client dispositivo pubblica un messaggio con **QoS 2**, IoT Hub chiude la connessione di rete.
IoT Hub non viene mantenuto Mantieni messaggi. Se un dispositivo invia un messaggio con il flag **MANTIENI** impostato su 1, IoT Hub aggiunge la **x-acconsentire a mantenere la** proprietà dell'applicazione al messaggio. In questo caso, anziché vengono mantenute messaggio Mantieni, IoT Hub passa all'applicazione back-end.

### <a name="receiving-messages"></a>Ricezione di messaggi

Per ricevere messaggi dall'IoT Hub, un dispositivo deve eseguire la sottoscrizione tramite `devices/{device_id}/messages/devicebound/#` come **Argomento filtro**. Il carattere jolly a più livelli **#** nel filtro argomento viene utilizzato esclusivamente per consentire il dispositivo di ricevere altre proprietà del nome dell'argomento. IoT Hub non consente l'uso della **#** o **?** caratteri jolly per il filtro degli argomenti secondari. Poiché IoT Hub non è un gestore di messaggistica pub sub generale, supporta solo i nomi di argomento documentazione e i filtri di argomento.

Vedere nota, che il dispositivo non riceverà i messaggi dall'IoT Hub, prima ha correttamente sottoscritto all'endpoint specifico dispositivo rappresentato dal `devices/{device_id}/messages/devicebound/#` filtro argomento. Quando è stata stabilita sottoscrizione ha esito positivo, verrà avviato il dispositivo messaggi solo cloud al dispositivo che sono stati inviati a tale dopo l'ora della sottoscrizione. Se il dispositivo si connette con il flag **CleanSession** impostato su **0**, l'abbonamento verrà mantenuta tra le sessioni diverse. In questo caso, la volta successiva che si connette con **CleanSession 0** il dispositivo si riceverà messaggi in sospeso che sono stati inviati a tale mentre era disconnesso. Se il dispositivo è installato flag **CleanSession** impostato su **1** se che non si riceverà messaggi dall'IoT Hub fino a quando non sottoscrive endpoint dispositivo.

IoT Hub invia messaggi con il **Nome dell'argomento** `devices/{device_id}/messages/devicebound/`, o `devices/{device_id}/messages/devicebound/{property_bag}` se sono presenti le proprietà del messaggio. `{property_bag}`contiene coppie di parole chiave/valore con codifica url di proprietà dei messaggi. Solo le proprietà delle applicazioni e proprietà-sistema impostata utente (ad esempio **ID messaggio** o **correlationId**) sono inclusi nell'elenco proprietà. I nomi delle proprietà di sistema hanno il prefisso **$**, le proprietà delle applicazioni usare il nome della proprietà originale senza prefisso.

Quando un client di dispositivo si iscrive a un argomento con **QoS 2**, IoT Hub concede massimo QoS livello 1 nel pacchetto **SUBACK** . Successivamente, IoT Hub invierà i messaggi per il dispositivo usato da QoS 1.

### <a name="additional-considerations"></a>Altre considerazioni

Per garantire la finale, se è necessario personalizzare il comportamento di protocollo MQTT sul lato cloud, è consigliabile esaminare il [gateway protocollo Azure IoT] [ lnk-azure-protocol-gateway] che consente di distribuire un gateway prestazioni elevate protocollo personalizzato che si interfaccia direttamente con IoT Hub. Il gateway di protocollo IoT Azure consente di personalizzare il protocollo di dispositivo per distribuzioni MQTT brownfield o altri protocolli personalizzati. Questo approccio, tuttavia, è necessario eseguire e operare un gateway protocollo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [le note sul MQTT supporto] [ lnk-mqtt-devguide] nella Guida di sviluppo Azure IoT Hub.

Per ulteriori informazioni sul protocollo MQTT, vedere la [documentazione MQTT][lnk-mqtt-docs].

Per ulteriori informazioni sulla pianificazione della distribuzione IoT Hub, vedere:

- [Azure certificata per il catalogo dispositivo IoT][lnk-devices]
- [Supporto per protocolli aggiuntivi][lnk-protocols]
- [Confrontare con gli hub di evento][lnk-compare]
- [Ridimensionamento, HA e DR][lnk-scaling]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
