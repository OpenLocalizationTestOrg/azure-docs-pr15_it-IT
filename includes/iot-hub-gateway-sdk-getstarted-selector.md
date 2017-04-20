> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

In questo articolo fornisce una procedura dettagliata del [codice di esempio Hello World] [ lnk-helloworld-sample] per illustrare i componenti fondamentali di [Azure IoT Gateway SDK] [ lnk-gateway-sdk] architettura. Nell'esempio viene utilizzato il SDK di Gateway IoT Hub per creare un gateway semplice che registra un messaggio "hello world" in un file di ogni cinque secondi.

Vengono trattati in questa procedura dettagliata:

- **Concetti**: una panoramica dei componenti che costituiscono un gateway è possibile creare con IoT Gateway SDK.  
- **Architettura di esempio Hello World**: viene descritto come applicano i concetti di esempio Hello World e la modalità di interazione dei componenti.
- **Come creare il codice di esempio**: I passaggi necessari per creare il codice di esempio.
- **Come eseguire il codice di esempio**: I passaggi necessari per eseguire il codice di esempio. 
- **Output tipico**: un esempio dell'output di cui si prevede che durante l'esecuzione dell'esempio.
- **Frammenti di codice**: una raccolta di frammenti di codice per mostrare come nell'esempio di Hello World viene implementata componenti principali di gateway.

## <a name="azure-iot-gateway-sdk-concepts"></a>Concetti IoT Gateway SDK Azure

Prima di esaminare il codice di esempio o creare il proprio gateway di campo con IoT Gateway SDK, è consigliabile acquisire familiarità con i concetti chiave che supportano l'architettura del SDK.

### <a name="modules"></a>Moduli

Per compilare un gateway con Azure IoT Gateway SDK, creazione e assemblaggio di *moduli*. Moduli di utilizzano *i messaggi* per scambiare dati tra loro. Un modulo riceve un messaggio, esegue un'azione su di esso, facoltativamente trasforma in un nuovo messaggio e quindi pubblicarlo per altri moduli per l'elaborazione. Alcuni moduli potrebbe essere generato solo i nuovi messaggi e mai elaborano i messaggi in arrivo. Una serie di moduli consente di creare una pipeline di elaborazione dati di ogni modulo eseguendo una trasformazione sui dati in corrispondenza del una punto pipeline.

![Una serie di moduli di gateway create con Azure IoT Gateway SDK][1]
 
SDK include le operazioni seguenti:

- Moduli creati in precedenza per i quali eseguono le funzioni comuni gateway.
- Le interfacce di uno sviluppatore può utilizzare per scrivere i moduli personalizzati.
- L'infrastruttura necessaria per distribuire ed eseguire una serie di moduli.

SDK offre un livello di astrazione che consente di creare gateway per l'esecuzione in numerosi sistemi operativi e le piattaforme.

![Livello di astrazione Azure IoT Gateway SDK][2]

### <a name="messages"></a>Messaggi

Sebbene pensare moduli passare a altro dei messaggi è un modo comodo per definire il concetto di funzionamento di un gateway, non accuratamente riflette cosa accade. I moduli di utilizzare un broker di comunicare tra loro, che pubblicare messaggi broker (bus, pubsub o qualsiasi altro motivo messaggistica) e consentire il broker di routing del messaggio per i moduli connessi.

Una moduli viene utilizzata la funzione **Broker_Publish** per pubblicare un messaggio al broker. Service broker offre i messaggi a un modulo di chiamando una funzione di richiamata automatica. Un messaggio è costituita da un insieme di proprietà chiave/valore e il contenuto passato come un blocco di memoria.

![Il ruolo di Service Broker in Azure IoT Gateway SDK][3]

### <a name="message-routing-and-filtering"></a>Il routing dei messaggi e filtro

Esistono due modalità di indirizzamento dei messaggi per i moduli corretti. Un insieme di collegamenti può essere passato a broker in modo che broker di identificare la posizione di origine e di sink per ogni modulo o il modulo è possibile filtrare le proprietà del messaggio. Un modulo deve agire solo su un messaggio se il messaggio destinato. I collegamenti e filtro dei messaggi permette di creare in modo efficiente una pipeline di messaggi.

## <a name="hello-world-sample-architecture"></a>Architettura di esempio Hello World

Nell'esempio di Hello World che illustra i concetti descritti nella sezione precedente. Nell'esempio di Hello World implementa un gateway con una pipeline costituita da due moduli:

-   Il modulo *world hello* consente di creare un messaggio ogni cinque secondi e passa alla funzionalità logger.
-   Il modulo *logger* scrive i messaggi ricevuti in un file.

![Architettura di esempio Hello World creata con Azure IoT Gateway SDK][4]

Come descritto nella sezione precedente, il modulo Hello World non passa messaggi direttamente alla funzionalità logger ogni cinque secondi. In realtà, vengono pubblicati un messaggio al broker ogni cinque secondi.

Il modulo logger riceve il messaggio di Service broker e funge da esso, scrivere il contenuto del messaggio in un file.

Modulo logger utilizza solo i messaggi provenienti da Service broker, vengono non pubblicati mai nuovi messaggi al broker.

![Come Service broker instrada i messaggi tra i moduli di Azure IoT Gateway SDK][5]

Nella figura precedente è illustrata l'architettura di esempio Hello World e i percorsi relativi ai file di origine che implementano diverse parti del campione del [repository][lnk-gateway-sdk]. Esplorare il codice personalizzato, oppure utilizzare frammenti di codice seguente come guida.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk