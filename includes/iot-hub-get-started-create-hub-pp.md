## <a name="create-a-device-management-enabled-iot-hub"></a>Creare un dispositivo gestione abilitata IoT Hub

Poiché la gestione dei dispositivi IoT Hub nell'anteprima, è necessario creare un hub di IoT gestione attivata dispositivo. Quando la gestione dei dispositivi IoT Hub raggiunge disponibilità generale, in questa esercitazione verrà aggiornata. La procedura seguente viene illustrato come completare questa attività tramite il portale di Azure.

1.  Accedere al [portale di Azure].
2.  In Jumpbar, fare clic su **Nuovo**, quindi fare clic su **Internet di elementi**e quindi fare clic su **Azure IoT Hub**.

    ![][img-new-hub]

3.  Scegliere la configurazione per il tuo fulcro IoT e il **IoT Hub** .

    ![][img-configure-hub]

  -   Nella casella **nome** immettere un nome per il tuo fulcro IoT. Se il **nome** è valide e disponibili, nella casella **nome** viene visualizzato un segno di spunta verde.
  -   Selezionare un **livello di prezzo e scala**. In questa esercitazione non è necessario un livello specifico.
  -   Nel **gruppo di risorse**, creare un nuovo gruppo di risorse oppure selezionarne uno esistente. Per ulteriori informazioni, vedere [utilizzo di gruppi di risorse per gestire le risorse Azure].
  -   Selezionare la casella per **Attivare la gestione dei dispositivi - anteprima**.
  -   In **posizione**, selezionare il percorso per ospitare il tuo fulcro IoT. Gestione di dispositivi IoT Hub è disponibile solo in Stati Uniti orientali, Europa Nord America e Asia orientale durante l'anteprima pubblica.

    > [AZURE.NOTE]Se non si seleziona la casella per **Attivare la gestione dei dispositivi**, gli esempi non funzionano.<br/>Selezionando **Abilita la gestione dei dispositivi**, si crea un'anteprima IoT Hub è supportato solo in Stati Uniti orientali, Europa Nord America e Asia orientale, non può essere scenari di produzione. È possibile eseguire la migrazione di dispositivi e in uscita dall'hub gestione attivata dispositivo.

4.  Quando si sono scelto il tuo fulcro IoT opzioni di configurazione, fare clic su **Crea**. È possibile richiedere alcuni minuti per Azure creare il tuo fulcro IoT. Per controllare lo stato, è possibile monitorare l'avanzamento **Startboard** o nel Pannello di **notifiche** .

    ![][img-monitor]

5.  Quando l'IoT Hub è stato creato correttamente, e il per il tuo fulcro si aprirà automaticamente. Prendere nota del **nome host**e quindi fare clic su **criteri di accesso condivisi**.

    ![][img-keys]

6.  Fare clic sul criterio **iothubowner** , quindi copiare e prendere nota della stringa di connessione e il **iothubowner** . Copiare in un percorso che è possibile accedere in un secondo momento, poiché è necessario per completare questa esercitazione.

    > [AZURE.NOTE] In scenari di produzione, assicurarsi di evitare di utilizzare le credenziali **iothubowner** .

    ![][img-connection]

È stato creato un dispositivo gestione abilitata IoT Hub. È necessario la stringa di connessione per completare questa esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità di dispositivo

In questa sezione, si utilizza uno strumento nodo denominato [IoT Hub Explorer] [ iot-hub-explorer] per creare un'identità di dispositivo per questa esercitazione.

1. Nell'ambiente della riga di comando, eseguire le operazioni seguenti:

    Installa npm -giothub-explorer@latest

2. Eseguire il seguente comando per l'accesso a un centro di raccolta, poiché ricordarsi di sostituire `{service connection string}` con la stringa di connessione IoT Hub copiato in precedenza:

    accesso iothub explorer "{servizio stringa di connessione}"

3. Infine, creare una nuova identità dispositivo chiamata `myDeviceId` con il comando:

    iothub explorer creare myDeviceId - stringa di connessione

Prendere nota della stringa di connessione dispositivo dal risultato. Questa stringa di connessione viene utilizzata dall'app di dispositivo per connettersi a un centro di raccolta IoT come dispositivo.

![][img-identity]

Fare riferimento alla [Guida introduttiva a IoT Hub] [ lnk-getstarted] per consente di creare identità dispositivo a livello di programmazione.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Portale di Azure]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[Uso dei gruppi di risorse per gestire le risorse Azure]: ../articles/azure-portal/resource-group-portal.md
