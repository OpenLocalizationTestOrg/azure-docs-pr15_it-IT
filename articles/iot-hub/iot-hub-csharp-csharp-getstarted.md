<properties
    pageTitle="Azure IoT Hub per C#: Guida introduttiva | Microsoft Azure"
    description="IoT Hub Azure con c# Guida introduttiva esercitazione. Utilizzare Azure IoT Hub e c# con Microsoft Azure IoT SDK per implementare una soluzione Internet di elementi."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Guida introduttiva a Azure IoT Hub per .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione, sono disponibili tre applicazioni console di Windows:

* **CreateDeviceIdentity**, che consente di creare una chiave di protezione associato per connettere il dispositivo simulato e identità di dispositivo.
* **ReadDeviceToCloudMessages**, che consente di visualizzare telemetria inviato dal dispositivo simulato.
* **SimulatedDevice**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza e inviare un messaggio di telemetria ogni secondo mediante il protocollo AMQP.

> [AZURE.NOTE] Per informazioni su SDK diversi che è possibile utilizzare per creare entrambe le applicazioni per eseguire analisi sui dispositivi e la soluzione back-end, vedere [IoT Hub SDK][lnk-hub-sdks].

Per completare questa esercitazione, è necessario quanto segue:

+ Microsoft Visual Studio 2015.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

È stato creato il tuo fulcro IoT ed è la stringa di connessione e hostname che è necessario completare il resto di questa esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità di dispositivo

In questa sezione, si crea un'app di console di Windows che consente di creare un'identità di dispositivo del Registro di sistema di identità l'hub IoT. Un dispositivo non riesce a connettersi a IoT hub a meno che non dispone di una voce del Registro di sistema di identità di dispositivo. Per ulteriori informazioni, vedere la sezione "Dispositivo identità del Registro di sistema" della [Guida per gli sviluppatori Hub IoT][lnk-devguide-identity]. Quando si esegue questa applicazione console, genera un ID univoco del dispositivo e una chiave che il dispositivo è possibile utilizzare per identificarsi durante l'invio di messaggi di dispositivo nel cloud a IoT Hub.

1. In Visual Studio, aggiungere un progetto Visual c# Desktop di Windows alla soluzione corrente utilizzando il modello di progetto **Applicazione Console** . Assicurarsi che la versione di .NET Framework sia 4.5.1 o versioni successive. Nome del progetto **CreateDeviceIdentity**.

    ![Nuovo progetto di Visual c# Desktop di Windows][10]

2. In Esplora soluzioni fare clic sul progetto **CreateDeviceIdentity** e quindi fare clic su **Gestisci pacchetti Nuget**.

3. Nella finestra **Gestione pacchetti Nuget** selezionare **Sfoglia**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto di **Microsoft.Azure.Devices** e accettare le condizioni di utilizzo. Questa procedura Scarica, installa e aggiunge un riferimento a [Microsoft Azure IoT Service SDK] [ lnk-nuget-service-sdk] Nuget pacchetto e delle dipendenze.

    ![Finestra di gestione di pacchetti NuGet][11]

4. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Aggiungere i campi seguenti per la classe di **programma** . Sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT creata nella sezione precedente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Aggiungere il metodo seguente alla classe **Program** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Questo metodo crea identità di un dispositivo con ID **myFirstDevice**. (Se l'ID di dispositivo è già presente nel Registro di sistema, il codice recupera semplicemente le informazioni sul dispositivo esistente.) L'app Visualizza quindi la chiave primaria per tale identità. Utilizzare questo tasto nel dispositivo simulato per connettersi a un centro di raccolta IoT.

7. Infine, aggiungere le seguenti righe al metodo **principali** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Eseguire questa applicazione e prendere nota della chiave del dispositivo.

    ![Chiave dispositivo generata dall'applicazione][12]

> [AZURE.NOTE] Registro di sistema di identità IoT Hub archivia solo identità di dispositivo per abilitare l'accesso sicuro a hub. Memorizza ID e i tasti da utilizzare come le credenziali di sicurezza e un contrassegno di attivazione/disattivazione che è possibile utilizzare per disattivare l'accesso per un singolo dispositivo. Se l'applicazione necessita memorizzare altri metadati specifico di dispositivo, deve utilizzare un archivio specifico dell'applicazione. Per ulteriori informazioni, vedere [Guida per gli sviluppatori Hub IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Ricevere messaggi di dispositivo nel cloud

In questa sezione, si crea un'app di console di Windows che legge i messaggi di dispositivo nel cloud dall'IoT Hub. Un hub IoT espone un [Hub evento Azure][lnk-event-hubs-overview]-endpoint compatibile che consentono di leggere i messaggi di dispositivo nel cloud. Per semplicità, questa esercitazione viene creato un lettore di base che non è adatto per una distribuzione alta velocità. Per informazioni su come elaborare i messaggi di dispositivo nel cloud in scala, vedere [elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione. Per ulteriori informazioni su come per elaborare i messaggi da hub di evento, vedere la [Guida introduttiva a evento hub] [ lnk-eventhubs-tutorial] esercitazione. (Disponibile per gli endpoint Hub IoT Hub evento compatibile con questa esercitazione.)

> [AZURE.NOTE] Endpoint compatibile con evento Hub per la lettura dei messaggi di dispositivo nel cloud sempre il protocollo AMQP.

1. In Visual Studio, aggiungere un progetto Visual c# Desktop di Windows per la soluzione corrente, utilizzando il modello di progetto **Applicazione Console** . Assicurarsi che la versione di .NET Framework sia 4.5.1 o versioni successive. Nome del progetto **ReadDeviceToCloudMessages**.

    ![Nuovo progetto di Visual c# Desktop di Windows][10]

2. In Esplora soluzioni fare clic sul progetto **ReadDeviceToCloudMessages** e quindi fare clic su **Gestisci pacchetti Nuget**.

3. Nella finestra **Gestione pacchetti Nuget** cercare **WindowsAzure.ServiceBus**, selezionare **Installa**e accettare le condizioni di utilizzo. Questa procedura Scarica, installa e aggiunge un riferimento a [Bus di servizio Azure][lnk-servicebus-nuget], con tutte le relative dipendenze. Questo pacchetto consente di connettersi all'endpoint Hub evento compatibile con il tuo fulcro IoT l'applicazione.

4. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Aggiungere i campi seguenti per la classe di **programma** . Sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT creata nella sezione "Creare un hub IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Aggiungere il metodo seguente alla classe **Program** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Questo metodo utilizza un'istanza di **EventHubReceiver** per ricevere i messaggi provenienti da tutti IoT hub dispositivo-a-cloud ricevano partizioni. Si noti come si passa un `DateTime.Now` parametro quando si crea l'oggetto **EventHubReceiver** , in modo che riceva solo i messaggi inviati dopo l'avvio. Questo filtro è utile in un ambiente di testing in modo da visualizzare la serie corrente di messaggi. In un ambiente di produzione codice assicurarsi che l'elaborazione di tutti i messaggi. Per ulteriori informazioni, vedere [la modalità di elaborazione dei messaggi di dispositivo nel cloud IoT Hub] [ lnk-process-d2c-tutorial] esercitazione.

7. Infine, aggiungere le seguenti righe al metodo **principali** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app di console di Windows che simula un dispositivo che invia messaggi dispositivo nel cloud a un hub IoT.

1. In Visual Studio, aggiungere un progetto Visual c# Desktop di Windows per la soluzione corrente, utilizzando il modello di progetto **Applicazione Console** . Assicurarsi che la versione di .NET Framework sia 4.5.1 o versioni successive. Nome del progetto **SimulatedDevice**.

    ![Nuovo progetto di Visual c# Desktop di Windows][10]

2. In Esplora soluzioni fare clic sul progetto **SimulatedDevice** e quindi fare clic su **Gestisci pacchetti Nuget**.

3. Nella finestra **Gestione pacchetti Nuget** selezionare **Sfoglia**, cercare **Microsoft.Azure.Devices.Client**, selezionare **Installa** per installare il pacchetto di **Microsoft.Azure.Devices.Client** e accettare le condizioni di utilizzo. Questa procedura Scarica, installa e aggiunge un riferimento a [Azure IoT - pacchetto Nuget SDK dispositivo] [ lnk-device-nuget] e le dipendenze.

4. Aggiungere quanto segue `using` istruzione nella parte superiore del file **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Aggiungere i campi seguenti per la classe di **programma** . Sostituire i valori segnaposto con il nome host hub IoT recuperati nella sezione "Creare un hub IoT" e la chiave dispositivo recuperata nella sezione "Creare un'identità di dispositivo".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Aggiungere il metodo seguente alla classe **Program** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Questo metodo invia un nuovo messaggio di dispositivo nel cloud ogni secondo. Il messaggio contiene un oggetto serializzato JSON, con l'ID del dispositivo e un numero casuale per simulare un sensore di velocità del vento.

7. Infine, aggiungere le seguenti righe al metodo **principali** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Per impostazione predefinita, il metodo **Create** crea un'istanza di **DeviceClient** che usa il protocollo AMQP per comunicare con IoT Hub. Per utilizzare il protocollo HTTP, utilizzare l'override del metodo **Create** che consente di specificare il protocollo. Se si usa il protocollo HTTP, è necessario aggiungere anche il pacchetto Nuget **Microsoft.AspNet.WebApi.Client** al progetto da includere lo spazio dei nomi **System.Net.Http.Formatting** .

In questa esercitazione illustra i passaggi per creare un client di dispositivo IoT Hub. È anche possibile utilizzare il [Servizio di connessione per Azure IoT Hub] [ lnk-connected-service] estensione di Visual Studio per aggiungere il codice necessario all'applicazione client dispositivo.


> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  In Visual Studio, in Esplora soluzioni fare doppio clic la soluzione e quindi fare clic su **progetti di avvio impostare**. Selezionare **più progetti di avvio**e quindi selezionare **Avvia** l'azione dei progetti **ReadDeviceToCloudMessages** e **SimulatedDevice** .

    ![Proprietà del progetto di avvio][41]

2.  Premere **F5** per iniziare a entrambe le applicazioni in esecuzione. L'output dall'app **SimulatedDevice** Visualizza i messaggi che dispositivo simulato invia il tuo fulcro IoT. L'output dall'app **ReadDeviceToCloudMessages** Visualizza i messaggi che riceve il tuo fulcro IoT.

    ![Output console App][42]

3. Il riquadro **l'uso** del [portale di Azure] [ lnk-portal] Mostra il numero di messaggi inviati a hub:

    ![Azure riquadro l'uso del portale][43]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è configurato un hub IoT nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Questa identità dispositivo usato per abilitare l'app dispositivo simulato l'invio di messaggi di dispositivo nel cloud hub. È inoltre creata un'applicazione che consente di visualizzare i messaggi ricevuti dall'hub. 

Guida introduttiva a IoT Hub e per esplorare altri scenari IoT, vedere:

- [Connessione del dispositivo][lnk-connect-device]
- [Guida introduttiva di gestione di dispositivi][lnk-device-management]
- [Guida introduttiva a SDK di Gateway IoT][lnk-gateway-SDK]

Per informazioni su come estendere la soluzione IoT ed elaborare messaggi dispositivo nel cloud in scala, vedere [elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
