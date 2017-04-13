## <a name="create-an-iot-hub"></a>Creare un hub IoT

Creare un hub IoT del dispositivo simulato a cui connettersi. La procedura seguente viene illustrato come completare questa attività tramite il portale di Azure.

1. Accedere al [portale di Azure][lnk-portal].

2. In Jumpbar, fare clic su **Nuovo** > **Internet di elementi** > **Azure IoT Hub**.

    ![Portale Azure Jumpbar][1]

3. Scegliere la configurazione per il tuo fulcro IoT e il **IoT hub** .

    ![Blade hub IoT][2]

    * Nella casella **nome** immettere un nome per il tuo fulcro IoT. Se il **nome** è valide e disponibili, nella casella **nome** viene visualizzato un segno di spunta verde.
    * Selezionare un [livello di prezzi e scala][lnk-pricing]. In questa esercitazione non è necessario un livello specifico. Per questa esercitazione, utilizzare il livello di F1 gratuito.
    * Nel **gruppo di risorse**, creare un nuovo gruppo di risorse oppure selezionarne uno esistente. Per ulteriori informazioni, vedere [utilizzo di gruppi di risorse per gestire le risorse Azure][lnk-resource-groups].
    * In **posizione**, selezionare il percorso per ospitare il tuo fulcro IoT. Per questa esercitazione, scegliere la posizione più vicina.

4. Quando si sono scelto il tuo fulcro IoT opzioni di configurazione, fare clic su **Crea**.  È possibile richiedere alcuni minuti per Azure creare il tuo fulcro IoT. Per controllare lo stato, è possibile monitorare l'avanzamento di Startboard o nel Pannello di notifiche.

    ![Nuovo stato hub IoT][3]

5. Quando l'hub IoT è stato creato correttamente, fare clic sul riquadro nuovo per il tuo fulcro IoT nel portale di Azure per aprire e il per il nuovo hub IoT. Prendere nota del **nome host**e quindi fare clic su **criteri di accesso condivisi**.

    ![Nuova pala hub IoT][4]

6. In e il **criteri di accesso condiviso** , fare clic su criteri **iothubowner** e quindi copiare e prendere nota della stringa di connessione e il **iothubowner** . Per ulteriori informazioni, vedere [il controllo dell'accesso] [ lnk-access-control] in "Azure IoT Hub sviluppo guide."

    ![Blade criteri di accesso condiviso][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
