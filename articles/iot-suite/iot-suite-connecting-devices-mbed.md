<properties
   pageTitle="Connettere un dispositivo che utilizza C su mbed | Microsoft Azure"
   description="Descrive come collegare un dispositivo alla soluzione monitoraggio remoto Azure IoT Suite preconfigurato usando un'applicazione scritta in esecuzione in un dispositivo mbed C."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connettere il dispositivo per la soluzione preconfigurata monitoraggio remota (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Creare ed eseguire la soluzione di esempio C

Le istruzioni seguenti descrivono i passaggi per la connessione di un [FRDM-K64F abilitato mbed Freescale] [ lnk-mbed-home] dispositivo per la soluzione di monitoraggio remoto.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connettere il dispositivo mbed al computer di rete e desktop

1. Connettere il dispositivo mbed alla rete utilizzando un cavo Ethernet. Questa operazione è necessaria in quanto l'applicazione di esempio richiede l'accesso a internet.

2. Vedere [Guida introduttiva di mbed] [ lnk-mbed-getstarted] per collegare il dispositivo mbed al PC.

3. Se il PC è in esecuzione Windows, vedere [Configurazione PC] [ lnk-mbed-pcconnect] per configurare l'accesso porta seriale al dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Creare un progetto mbed e importare il codice di esempio

1. Nel web browser, passare al [sito per sviluppatori](https://developer.mbed.org/)di mbed.org. Se è stato ancora effettuato l'iscrizione, è presente un'opzione per creare un account (disponibile gratuitamente). In caso contrario, accedere con le credenziali dell'account. Fare clic su **compilatore** nell'angolo superiore destro della pagina. Questa azione consente di visualizzare l'interfaccia di *area di lavoro* .

2. Assicurarsi che la piattaforma hardware in uso viene visualizzata nell'angolo superiore destro della finestra o fare clic sull'icona nell'angolo superiore destro selezionare la piattaforma hardware.

3. Fare clic su **Importa** nel menu principale. Fare clic su **fare clic qui** per importare da collegamento URL accanto al logo del globo mbed.

    ![][6]

4. Nella finestra popup, immettere il collegamento per il https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ codice campione, quindi fare clic su **Importa**.

    ![][7]

5. È possibile visualizzare nella finestra del compilatore mbed che importazione del progetto Importa diverse librerie. Alcuni vengono forniti e gestiti dal team di Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), mentre altre sono disponibili nel catalogo raccolte mbed librerie di terze parti.

    ![][8]

6. Aprire il file remote_monitoring\remote_monitoring.c e individuare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Sostituisci [Id dispositivo] e [dispositivo chiave], con i dati del dispositivo per attivare il programma di esempio per connettersi a un centro di raccolta IoT. Usare il nome host Hub IoT per sostituire il [nome IoTHub] e [suffisso IoTHub, vale a dire devices.net di azure] segnaposto. Ad esempio, se il nome host Hub IoT **contoso.azure devices.net**, **contoso** è **hubName** e tutti gli elementi dopo averlo **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Scorrere il codice

Se si è interessati in funzionamento del programma, in questa sezione descrive alcuni elementi chiave del codice di esempio. Se si desidera eseguire il codice, passare a [creare ed eseguire il programma](#buildandrun).

#### <a name="defining-the-model"></a>La definizione del modello

In questo esempio si utilizza il [serializzatore] [ lnk-serializer] raccolta per definire un modello che specifica i messaggi di inviare a IoT Hub e ricevere dall'IoT Hub il dispositivo. In questo esempio, lo spazio dei nomi **Contoso** definisce un modello di **termostato** che specifica i dati di telemetria **temperatura**, **ExternalTemperature**e **umidità** insieme dei metadati, ad esempio l'id del dispositivo, proprietà dei dispositivi e i comandi che risponde a:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Correlati alla definizione del modello sono le definizioni per i comandi **SetTemperature** e **SetHumidity** che risponde a:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Connette il modello nella raccolta

Le funzioni **sendMessage** e **IoTHubMessage** sono codice standard per l'invio di telemetria dal dispositivo e la connessione di messaggi dall'IoT Hub ai gestori dei comandi.

#### <a name="the-remotemonitoringrun-function"></a>La funzione remote_monitoring_run

Funzione **principale** del programma richiama la funzione **remote_monitoring_run** all'avvio dell'applicazione eseguire il comportamento del dispositivo come un client di dispositivo IoT Hub. Questa funzione **remote_monitoring_run** costituito prevalentemente annidate coppie di funzioni:

- **piattaforma\_inizializzazione** e **piattaforma\_deinit** operazioni specifiche della piattaforma inizializzazione e chiusura.
- **serializzatore\_inizializzazione** e **serializzatore\_deinit** inizializzare e non inizializzare la raccolta di serializzatore.
- **IoTHubClient\_crea** e **IoTHubClient\_Destroy** creare un punto di client, **iotHubClientHandle**, utilizzando le credenziali di dispositivo per la connessione a un centro di raccolta IoT.

Nella sezione principale della funzione **remote_monitoring_run** , il programma esegue le operazioni seguenti tramite il quadratino di **iotHubClientHandle** :

- Crea un'istanza del modello termostato Contoso e configura automaticamente i callback messaggio per i comandi.
- Invia informazioni sul dispositivo, inclusi i comandi che supporta, per il tuo fulcro IoT utilizzando la libreria di serializzatore. Quando l'hub riceve il messaggio, lo stato dispositivo nel dashboard verrà modificato da **in sospeso** in **esecuzione**.
- Avvia un ciclo **mentre** che invia temperatura, temperatura esterna e i valori di umidità a IoT Hub ogni secondo.

Per riferimento, ecco un esempio **DeviceInfo** messaggio IoT Hub all'avvio:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Per riferimento, ecco un esempio **telemetria** messaggio IoT Hub:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Per riferimento, ecco un esempio ricevuto dall'IoT Hub **comando** :

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Creare ed eseguire il programma

1. Fare clic su **Compila** per generare il programma. È possibile in modo sicuro ignorare tutti gli avvisi, ma se la genera un errore, correggerli prima di continuare.

2. Se la compilazione ha esito positivo, il sito Web del compilatore mbed genera un file di bin con il nome del progetto e download nel computer locale. Copiare il file bin al dispositivo. Salvataggio del file bin il dispositivo provoca il dispositivo per riavviare ed eseguire il programma contenuto nel file bin. È possibile riavviare il programma manualmente in qualsiasi momento facendo clic sul pulsante Reimposta sul dispositivo mbed.

3. Connettere il dispositivo usato da un'applicazione client SSH, ad esempio PuTTY. È possibile determinare la porta seriale che il dispositivo viene utilizzata tramite Gestione dispositivi Windows.

    ![][11]

4. Selezionare il tipo di connessione **seriale** PuTTY. Il dispositivo in genere si connette alla trasmissione 9600, quindi immettere 9600 nella casella **velocità** . Fare clic su **Apri**.

5. Verrà avviato il programma in esecuzione. Potrebbe essere necessario ripristinare il discussioni (premere CTRL + interruzione o premere il pulsante di reimpostazione della scheda) se il programma non viene avviato automaticamente quando ci si connette.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
