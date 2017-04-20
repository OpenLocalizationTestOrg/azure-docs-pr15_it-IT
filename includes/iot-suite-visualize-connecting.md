## <a name="view-device-telemetry-in-the-dashboard"></a>Visualizzazione dispositivo telemetria nel dashboard

Il dashboard nella soluzione di monitoraggio remota consente di visualizzare la telemetria che inviano i dispositivi a IoT Hub.

1. Nel browser, tornare al dashboard di monitoraggio soluzione remota, fare clic su **dispositivi** nel riquadro sinistro passare all' **elenco dei dispositivi**.

2. Nell' **elenco dei dispositivi**, verrà visualizzato che lo stato del dispositivo è **in esecuzione**.

    ![][18]

3. Selezionare **Dashboard** per tornare al dashboard, selezionare il dispositivo del **dispositivo di visualizzazione** elenco a discesa per visualizzare il relativo telemetria. Telemetria dall'applicazione di esempio è 50 unità per la temperatura interna, 55 unità di misura di temperature esterna e 50 unità per umidità. Si noti che per impostazione predefinita il dashboard Visualizza solo i valori di temperatura e umidità.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Inviare un comando al dispositivo

Il dashboard nella soluzione di monitoraggio remota consente di inviare i comandi per i dispositivi tramite IoT Hub. Nella soluzione di monitoraggio remota, ad esempio, è possibile inviare un comando di impostare la temperatura interna di un dispositivo.

1. In remoto dashboard di monitoraggio soluzione, fare clic su **dispositivi** nel riquadro sinistro passare all' **elenco dei dispositivi**.

2. Fare clic su **ID del dispositivo** per il dispositivo nell' **elenco dei dispositivi**.

3. Nel riquadro **Dettagli dispositivo** fare clic su **comandi**.

    ![][13]

4. Nell'elenco a discesa **comando** selezionare **SetTemperature**e quindi immettere un nuovo valore temperatura **temperatura** . Fare clic su **Invia comandi** per inviare il comando al dispositivo.

    ![][14]

    > [AZURE.NOTE] La cronologia dei comandi inizialmente risulta lo stato del comando **in sospeso**. Quando il dispositivo riconosce il comando, lo stato cambia per **esito positivo**.

5. Nel dashboard, verificare che il dispositivo è ora inviando 75 come nuovo valore temperature.

## <a name="next-steps"></a>Passaggi successivi

L'articolo [personalizzazione preconfigurato soluzioni] [ lnk-customize] vengono illustrati alcuni modi per estendere in questo esempio. Estensioni possibili includono l'utilizzo sensori reali e l'implementazione di comandi aggiuntivi.

Per ulteriori informazioni sulle [autorizzazioni per il sito azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
