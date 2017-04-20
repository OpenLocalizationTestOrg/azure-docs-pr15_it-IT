> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

In questa procedura dettagliata del [Cloud dispositivo simulato caricare esempio] viene illustrato l'utilizzo di [Azure IoT Gateway SDK] [ lnk-sdk] per inviare telemetria dispositivo al cloud a IoT Hub dai dispositivi simulati.

Vengono trattati in questa procedura dettagliata:

1. **Architettura**: importanti informazioni dell'architettura esempio simulare caricare Cloud dispositivo.

2. **Compila ed Esegui**: i passaggi necessari per creare ed eseguire il codice di esempio.

## <a name="architecture"></a>Architettura

Nell'esempio di caricare simulato dispositivo Cloud viene illustrato come utilizzare il SDK per creare un gateway che invia telemetria dai dispositivi simulati a un hub IoT. I dispositivi simulati Impossibile connettersi direttamente a IoT Hub.

- I dispositivi di non utilizzano un protocollo di comunicazione interpretato da IoT Hub.
- I dispositivi non sono sufficientemente smart ricordare l'identità assegnata loro dal IoT Hub.

Il gateway per risolvere questo problema per i dispositivi simulati nei modi seguenti:

- Il gateway che riconosca il protocollo utilizzato dai dispositivi simulati riceve dispositivo al cloud telemetria tra i dispositivi e inoltra i messaggi a IoT Hub utilizza un protocollo interpretato dall'hub.
- Il gateway archivia le identità IoT Hub per i dispositivi simulati conto e funge da proxy quando i dispositivi simulati inviare messaggi a IoT Hub.

Il diagramma seguente illustra i componenti principali di esempio, inclusi i moduli gateway:

![][1]


> [AZURE.NOTE] I moduli non passano direttamente a altro dei messaggi. I moduli di pubblicano messaggi su un broker interni che invia i messaggi ai moduli utilizzando un meccanismo di sottoscrizione, come illustrato nella figura riportata di seguito. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di SDK di Gateway IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>Modulo di inserimento del protocollo

In questo modulo è il punto di partenza per la visualizzazione dei dati in dispositivi, attraverso il gateway e nel cloud. Nel modello di modulo esegue le quattro attività:

1.  Crea dati temperatura simulato.
    
    Nota: se si utilizzano dispositivi reali, il modulo verrà leggere dati da tali dispositivi fisici.

2.  Inserisce i dati temperatura simulato al contenuto di un messaggio.

3.  Aggiunge una proprietà con un indirizzo MAC fittizio al messaggio che contiene i dati temperatura simulato.

4.  Rende il messaggio disponibili al modulo successivo nella catena.

> [AZURE.NOTE] Il modulo denominato **X protocollo inserimento** nel diagramma precedente viene chiamato **simulare dispositivo** nel codice sorgente.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; modulo IoT Hub ID

In questo modulo analizza i messaggi che includono una proprietà che contiene l'indirizzo MAC, aggiunto dal modulo di inserimento di protocollo, del dispositivo simulato. Se il modulo trova tale proprietà, che consente di aggiungere un'altra proprietà con una chiave di dispositivo IoT Hub al messaggio e quindi trasforma il messaggio disponibili per il modulo successivo nella catena. Si tratta come nell'esempio di associa le identità dispositivo IoT Hub con i dispositivi simulati. Lo sviluppatore imposta il mapping tra gli indirizzi MAC e le identità IoT Hub manualmente come parte della configurazione del modulo. 

> [AZURE.NOTE]  In questo esempio viene utilizzato un indirizzo MAC come identificatore univoco di dispositivi e correla con un'identità di dispositivo IoT Hub. Tuttavia, è possibile scrivere un modulo personalizzato che utilizza un identificatore univoco. Ad esempio, potrebbe essere dispositivi con dati di telemetria con un nome univoco dispositivo incorporato che è possibile utilizzare per determinare l'identità del dispositivo IoT Hub o numeri di serie univoci.

### <a name="iot-hub-communication-module"></a>Modulo di comunicazione IoT Hub

In questo modulo utilizza i messaggi con un IoT Hub l'identità del dispositivo assegnato dal modulo precedente e invia il contenuto del messaggio IoT Hub tramite HTTP. HTTP è uno dei tre protocolli interpretati da IoT Hub.

Invece di aprire una connessione a IoT Hub per ogni dispositivo simulato, in questo modulo viene aperta una singola connessione HTTP dal gateway per l'hub IoT e multiplexes connessioni da tutti i dispositivi simulati in tale connessione. In questo modo un solo gateway connettere molti più dispositivi, simulati o di altro tipo, che è possibile se aperta una connessione univoca per ogni dispositivo.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Esempio di caricare Cloud dispositivo simulato]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md