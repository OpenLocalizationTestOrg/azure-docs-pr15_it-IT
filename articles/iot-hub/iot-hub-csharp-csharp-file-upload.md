<properties
    pageTitle="Caricare i file da dispositivi con IoT Hub | Microsoft Azure"
    description="Seguire questa esercitazione per informazioni su come caricare file da dispositivi con Azure IoT Hub c#."
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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Esercitazione: Come caricare file da dispositivi nel cloud con IoT Hub

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito consente affidabile e protette bidirezionale tra milioni di dispositivi IoT e un'applicazione di back-end. Esercitazioni precedenti ([Guida introduttiva a IoT Hub] e [inviare messaggi Cloud per dispositivo con IoT Hub]) illustrano il base dispositivo nel cloud e funzionalità di messaggistica cloud al dispositivo di IoT Hub e l'esercitazione [processo dispositivo nel Cloud messaggi] descrive un modo per archiviare in modo affidabile messaggi dispositivo nel cloud in archiviazione blob Azure. Tuttavia, in alcuni casi non è possibile mappare facilmente i dati che dispositivi inviare i messaggi di dispositivo nel cloud relativamente IoT Hub accetta. Esempi di file di grandi dimensioni che contengono immagini, video e dati vibrazione campionati ad alta frequenza o che contengono un tipo di dati pre-elaborati. Questi file sono in genere batch elaborati nel cloud utilizzo degli strumenti, ad esempio [Azure Data Factory] o stack [Hadoop] . Dopo aver configurato il caricamento di file da un dispositivo preferito per l'invio di eventi, è ancora possibile usare la funzionalità di protezione e affidabilità IoT Hub.

In questa esercitazione si basa sul codice nell'esercitazione [inviare messaggi Cloud per dispositivo con IoT Hub] in cui viene illustrato come utilizzare le funzionalità di caricamento di file di IoT Hub. Viene illustrato come fornire in modo sicuro un dispositivo con un Azure blob URI per caricare un file e come usare le notifiche di caricamento di file IoT Hub per attivare l'elaborazione del file di back-end l'app.

Al termine di questa esercitazione si eseguire due applicazioni console di Windows:

* **SimulatedDevice**, una versione modificata dell'app creata in esercitazione [inviare messaggi Cloud per dispositivo con IoT Hub] , che consente di caricare un file di archiviazione con un SAS URI tramite il tuo fulcro IoT.
* **ReadFileUploadNotification**, che riceve le notifiche per caricamenti file dall'hub IoT.

> [AZURE.NOTE] IoT Hub supporta molte piattaforme di dispositivi e lingue (inclusi C, Java e Javascript) tramite il dispositivo di Azure IoT SDK. Fare riferimento al [Centro per sviluppatori di Azure IoT] per istruzioni dettagliate su come connettere il dispositivo per il codice riportato in questa esercitazione e in genere per Azure IoT Hub.

Per completare questa esercitazione, è necessario quanto segue:

+ Microsoft Visual Studio 2015,

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associare un account di archiviazione Azure a IoT Hub

Poiché il dispositivo simulato carica un file in un blob, devono avere un account di [Archiviazione Azure] associato a IoT Hub. Quando si associa un account di archiviazione Azure a un hub IoT, hub può generare un URI SAS utilizzate un dispositivo in modo sicuro caricare un file in un contenitore di blob. Il servizio IoT Hub e SDK dispositivo coordinare il processo che genera URI SAS e rende disponibili per un dispositivo da usare per caricare un file.

Seguire le istruzioni in [file di configurazione carica tramite il portale di Azure] [ lnk-configure-upload] per associare un account di archiviazione Azure per il tuo fulcro IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Caricare un file da un dispositivo simulato

In questa sezione è modificare l'applicazione dispositivo simulato è stato creato in [invio di messaggi Cloud per dispositivo con IoT Hub] per ricevere i messaggi cloud per dispositivo dall'hub IoT.

1. In Visual Studio, fare clic sul progetto **SimulatedDevice** , fare clic su **Aggiungi**e quindi fare clic su **Elemento esistente**. Passare a un file di immagine e includere nel progetto. In questa esercitazione si presuppone che l'immagine è denominata `image.jpg`.

2. Pulsante destro del mouse sull'immagine e quindi fare clic su **proprietà**. Assicurarsi che **copiare Directory di Output** sia impostato su **Copia sempre**.

    ![][1]

3. Nel file **Program.cs** , aggiungere le istruzioni seguenti nella parte superiore del file:

        using System.IO;

4. Aggiungere il metodo seguente alla classe **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    Il `UploadToBlobAsync` metodo accetta l'origine di flusso e nome file del file da caricare e gestisce il caricamento allo spazio di archiviazione. L'applicazione console consente di visualizzare il tempo che necessario per caricare il file.

5. Aggiungere il metodo seguente nel metodo **Main** appena prima di `Console.ReadLine()` riga:

        SendToBlobAsync();

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="receive-a-file-upload-notification"></a>Ricevere una notifica di caricamento di file

In questa sezione, scrivere un'applicazione di console di Windows che riceve messaggi di notifica di caricamento di file dall'IoT Hub.

1. Soluzione Visual Studio corrente, creare un nuovo progetto di Visual c# Windows utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **ReadFileUploadNotification**.

    ![Nuovo progetto di Visual Studio][2]

2. In Esplora soluzioni fare clic sul progetto **ReadFileUploadNotification** e quindi fare clic su **Gestisci pacchetti NuGet**.

    Verrà visualizzata la finestra Gestisci pacchetti NuGet.

2. Cercare `Microsoft.Azure.Devices`, fare clic su **installazione**e accettare le condizioni di utilizzo. 

    Questo download installa e aggiunge un riferimento a [Azure IoT - pacchetto NuGet SDK servizio] nel progetto **ReadFileUploadNotification** .

3. Nel file **Program.cs** , aggiungere le istruzioni seguenti nella parte superiore del file:

        using Microsoft.Azure.Devices;

4. Aggiungere i campi seguenti per la classe di **programma** . Sostituire il valore di segnaposto con la stringa di connessione IoT hub da [iniziare a utilizzare IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Aggiungere il metodo seguente alla classe **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Si noti che il modello di ricezione è identico a quello utilizzato per ricevere messaggi cloud per dispositivo dall'app per dispositivi.

6. Infine, aggiungere le seguenti righe al metodo **principali** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. In Visual Studio, fare doppio clic la soluzione e selezionare **progetti di avvio impostare**. Selezionare **più progetti di avvio**, quindi selezionare l'azione **Avvia** per **ReadFileUploadNotification** e **SimulatedDevice**.

2. Premere **F5**. Devono iniziare a entrambe le applicazioni. Verrà visualizzato il caricamento completato in un'applicazione console e il messaggio di notifica upload ricevuti da altre app console. È possibile utilizzare il [portale di Azure] e Visual Studio Server Explorer per verificare la presenza di file caricato nell'account di archiviazione Azure.

  ![][50]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso come sfruttare le funzionalità di caricamento di file di IoT Hub per semplificare il caricamento di file da dispositivi. È possibile continuare a esplorare IoT hub funzionalità e scenari con gli articoli seguenti:

- [Creare un hub IoT a livello di programmazione][lnk-create-hub]
- [Introduzione a C SDK][lnk-c-sdk]
- [Hub IoT SDK][lnk-sdks]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portale di Azure]: https://portal.azure.com/

[Dati di Azure Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Inviare messaggi Cloud per dispositivo con IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Elabora i messaggi di dispositivo nel Cloud]: iot-hub-csharp-csharp-process-d2c.md
[Guida introduttiva a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori di Azure IoT]: http://www.azure.com/develop/iot

[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Spazio di archiviazione Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - pacchetto NuGet SDK servizio]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


