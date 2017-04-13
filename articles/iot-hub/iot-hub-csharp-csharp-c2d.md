<properties
    pageTitle="Inviare messaggi cloud per dispositivo con IoT Hub | Microsoft Azure"
    description="Seguire questa esercitazione per imparare a inviare messaggi cloud per dispositivo utilizzando Azure IoT Hub con c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Esercitazione: Come inviare messaggi cloud per dispositivo con IoT Hub e .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito che consente di attivare comunicazioni bidirezionale sicura e affidabile tra milioni di dispositivi IoT e la fine di un'applicazione di nuovo. Esercitazioni [introduttive IoT Hub] viene illustrato come creare un hub IoT, effettuare il provisioning di identità di un dispositivo al suo interno e codice un dispositivo simulato che invia messaggi dispositivo nel cloud.

In questa esercitazione si basa su [Introduzione IoT Hub]. Verrà illustrata la procedura per:

- Dall'applicazione cloud back-end, inviare messaggi cloud per dispositivo a una singola periferica tramite IoT Hub.
- Ricevere messaggi cloud al dispositivo in un dispositivo.
- Dal cloud applicazione back-end, richiedere la conferma di recapito (*commenti e suggerimenti*) per i messaggi inviati a un dispositivo dall'IoT Hub.

Sono disponibili ulteriori informazioni sui messaggi cloud al dispositivo la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione, eseguire due applicazioni console di Windows:

* **SimulatedDevice**, una versione modificata dell'app create in [Guida introduttiva a IoT Hub], che si connette a un centro di raccolta IoT e riceve messaggi cloud al dispositivo.
* **SendCloudToDevice**, viene inviato un messaggio cloud al dispositivo al dispositivo simulato tramite IoT Hub e quindi riceve la conferma di recapito.

> [AZURE.NOTE] IoT Hub supporta SDK molte piattaforme di dispositivi e lingue (inclusi C, Java e Javascript) tramite Azure IoT dispositivo SDK. Per istruzioni dettagliate su come connettere il dispositivo di codice dell'esercitazione e in genere di Azure IoT Hub, vedere il [Centro per sviluppatori di Azure IoT].

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

## <a name="receive-messages-on-the-simulated-device"></a>Ricevere messaggi sul dispositivo simulato

In questa sezione, è necessario modificare l'applicazione dispositivo simulato è stato creato in [Guida introduttiva a IoT Hub] a ricevere messaggi cloud per dispositivo dall'hub IoT.

1. Nel progetto **SimulatedDevice** Visual Studio, aggiungere il metodo seguente alla classe **Program** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Il `ReceiveAsync` metodo asincrono restituisce il messaggio ricevuto nel momento in cui viene ricevuto dal dispositivo. Restituisce il *valore null* dopo un periodo di timeout specificabile (in questo caso, viene utilizzato il valore predefinito di un minuto). In questo caso, il codice deve continuare in attesa per i nuovi messaggi. Questo è il motivo per la `if (receivedMessage == null) continue` riga.

    La chiamata a `CompleteAsync()` notifica IoT Hub che il messaggio è stato elaborato. Il messaggio può essere rimossi dalla coda dispositivo. Se è verificato un problema che impedisce che l'app dispositivo il completamento di elaborazione del messaggio, IoT Hub recapitato nuovamente. È quindi importante che logica di elaborazione messaggio nell'app dispositivo essere *idempotente*, in modo che riceve il messaggio stesso più volte lo stesso risultato. Un'applicazione possa abbandonare temporaneamente anche un messaggio, creando così hub IoT mantenendo il messaggio nella coda consumo futuro. In alternativa, l'applicazione possibile rifiutare un messaggio, che consente di rimuovere definitivamente il messaggio dalla coda. Per ulteriori informazioni sul ciclo di vita messaggio cloud per dispositivo, vedere la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Quando si utilizza HTTP invece che tramite MQTT o AMQP come trasporto, il `ReceiveAsync` metodo restituisce immediatamente. Motivo supportato per i messaggi cloud per dispositivo con HTTP è connessi occasionalmente in dispositivi di controllo per i messaggi di frequente (inferiore a 25 minuti). Emissione HTTP più riceve risultati hub IoT le richieste di limitazione. Per ulteriori informazioni sulle differenze tra il supporto MQTT, AMQP e HTTP e Hub IoT limitazione, vedere la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

2. Aggiungere il metodo seguente nel metodo **Main** appena prima di `Console.ReadLine()` riga:

        ReceiveC2dAsync();

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio cloud al dispositivo

In questa sezione, è necessario scrivere un'app di console di Windows che invia messaggi cloud al dispositivo all'app dispositivo simulato.

1. Soluzione Visual Studio corrente, creare un nuovo progetto di Visual c# Desktop App utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **SendCloudToDevice**.

    ![Nuovo progetto di Visual Studio][20]

2. In Esplora soluzioni fare doppio clic la soluzione e quindi fare clic su **Gestisci pacchetti NuGet per soluzione...**. 

    Verrà aperta la finestra **Gestisci pacchetti NuGet** .

3. Cercare `Microsoft Azure Devices`, fare clic su **installazione**e accettare le condizioni di utilizzo. 

    Questo download installa e aggiunge un riferimento a [Azure IoT - pacchetto NuGet SDK servizio].

4. Aggiungere quanto segue `using` istruzione nella parte superiore del file **Program.cs** :

        using Microsoft.Azure.Devices;

5. Aggiungere i campi seguenti per la classe di **programma** . Sostituire il valore di segnaposto con la stringa di connessione IoT hub da [iniziare a utilizzare IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Aggiungere il metodo seguente alla classe **Program** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Questo metodo invia un nuovo messaggio cloud al dispositivo al dispositivo con l'ID `myFirstDevice`. Modificare il parametro di conseguenza, nel caso in cui è modificato da quelli utilizzati in [Guida introduttiva a IoT Hub].

7. Infine, aggiungere le seguenti righe al metodo **principali** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. In Visual Studio, fare doppio clic la soluzione e selezionare **progetti di avvio Set...**. Selezionare **più progetti di avvio**, quindi selezionare l'azione **Avvia** per **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice**.

9.  Premere **F5**. Tutte le tre applicazioni dovrebbero iniziare. Selezionare le finestre di **SendCloudToDevice** e premere **INVIO**. Verrà visualizzato il messaggio viene inviato app simulata.

    ![Messaggio di ricezione di App][21]

## <a name="receive-delivery-feedback"></a>Ricevere commenti e suggerimenti recapito
È possibile conferme di recapito (o scadenza) richiesta dall'IoT Hub per ogni messaggio cloud al dispositivo. In questo modo back-end cloud informare facilmente logica Riprova o indennizzo. Per ulteriori informazioni sui commenti e suggerimenti cloud per dispositivi, vedere la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

In questa sezione verrà modificato l'app **SendCloudToDevice** per richiedere commenti e suggerimenti e ricevere dall'IoT Hub.

1. Nel progetto **SendCloudToDevice** Visual Studio, aggiungere il metodo seguente alla classe **Program** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Si noti che il modello di ricezione è identico a quello utilizzato per ricevere messaggi cloud per dispositivo dall'app per dispositivi.

2. Aggiungere il metodo seguente nel metodo **Main** subito dopo il `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` riga:

        ReceiveFeedbackAsync();

3. Per richiedere commenti e suggerimenti per il recapito del messaggio cloud per dispositivo, è necessario specificare una proprietà nel metodo **SendCloudToDeviceMessageAsync** . Aggiungere la riga seguente, subito dopo il `var commandMessage = new Message(...);` riga:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Eseguire l'App premendo **F5**. Verranno visualizzate tutte le tre applicazioni iniziare. Selezionare le finestre di **SendCloudToDevice** e premere **INVIO**. Verrà visualizzato il messaggio viene ricevuto dall'app simulata e dopo alcuni secondi, il messaggio di commenti e suggerimenti ricevuto **SendCloudToDevice** dall'applicazione in uso.

    ![Messaggio di ricezione di App][22]

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso inviare e ricevere messaggi cloud al dispositivo. 

Per esempi di soluzioni-to-end complete che utilizzano IoT Hub, vedere [Azure IoT famiglia di prodotti].

Per ulteriori informazioni sullo sviluppo di soluzioni con IoT Hub, vedere la [Guida per gli sviluppatori di IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - pacchetto NuGet SDK servizio]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guida per gli sviluppatori IoT Hub]: iot-hub-devguide.md
[Guida introduttiva a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori di Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Famiglia di prodotti IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/