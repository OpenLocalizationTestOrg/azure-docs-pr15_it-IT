> [AZURE.SELECTOR]
- [C di Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C su Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C di mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Panoramica dello scenario

In questo scenario, si crea un dispositivo che invia la telemetria seguente per il computer remoto monitoring [soluzione preconfigurata][lnk-what-are-preconfig-solutions]:

- Temperatura esterna
- Temperatura interna
- Umidità

Per semplicità, il codice sul dispositivo genera valori di esempio, ma è consigliabile estendere il codice di esempio connettendosi sensori reali sul dispositivo e invio di telemetria reale.

Per completare questa esercitazione, è necessario un account di Azure active. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per ulteriori informazioni, vedere [Versione di valutazione gratuita di Azure][lnk-free-trial].

## <a name="before-you-start"></a>Prima di iniziare

Prima di scrivere codice per il dispositivo, è necessario eseguire il provisioning della soluzione preconfigurata monitoraggio remota e quindi eseguire il provisioning di un nuovo dispositivo personalizzato all'interno della soluzione.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Eseguire il provisioning della soluzione preconfigurata monitoraggio remota

Il dispositivo si creerà in questa esercitazione invia dati a un'istanza di [monitoraggio remoto] [ lnk-remote-monitoring] preconfigurato soluzione. Se non sono già eseguito il provisioning della soluzione di preconfigurato monitoraggio remota nel tuo account Azure, attenersi alla procedura descritta di seguito:

1. Nella pagina <https://www.azureiotsuite.com/> fare clic su **+** per creare una nuova soluzione.

2. Fare clic su **Seleziona** nel Pannello di **controllo remoto** per creare la nuova soluzione.

3. Nella pagina **creare remoto soluzione di monitoraggio** , immettere un **Nome soluzione** desiderato, selezionare l' **area** che si desidera distribuire e selezionare la sottoscrizione Azure per da utilizzare. Fare clic su **Crea soluzione**.

4. Attendere il completamento del processo di provisioning.

> [AZURE.WARNING] Le soluzioni preconfigurate utilizzano servizi di Azure fatturabile. Assicurarsi di rimuovere la soluzione preconfigurata dalla sottoscrizione quando è terminata per evitare i costi non necessari. È possibile rimuovere completamente una soluzione preconfigurata dalla sottoscrizione visitando la pagina <https://www.azureiotsuite.com/> .

Al termine dell'esecuzione del processo di provisioning per la soluzione di monitoraggio remoto, fare clic su **Avvia** per aprire il dashboard di soluzione nel browser.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Eseguire il provisioning del dispositivo nella soluzione di monitoraggio remoto

> [AZURE.NOTE] Se già abbia effettuato il provisioning di un dispositivo nella soluzione, è possibile ignorare questo passaggio. È necessario conoscere le credenziali per un dispositivo, quando si crea l'applicazione client.

Per un dispositivo per la connessione alla soluzione preconfigurata, è necessario identificarsi IoT Hub utilizzando credenziali valide. È possibile recuperare le credenziali per un dispositivo nel dashboard di soluzione. Includere le credenziali per un dispositivo nell'applicazione client più avanti in questa esercitazione. 

Per aggiungere un nuovo dispositivo per la soluzione di monitoraggio remoto, completare la procedura seguente nel dashboard di soluzione:

1.  Nell'angolo inferiore sinistro del dashboard, scegliere **Aggiungi un dispositivo**.

    ![][1]

2.  Nel Pannello di **Dispositivo personalizzato** , fare clic su **Aggiungi nuovo**.

    ![][2]

3.  Selezionare **Consenti la definire i propri ID dispositivo**, immettere un ID dispositivo, ad esempio **mydevice**, quindi scegliere **ID di controllo** per verificare che il nome specificato non è già in uso e quindi fare clic su **Crea** per il provisioning del dispositivo.

    ![][3]

5. Annotare le credenziali di dispositivi (ID dispositivo, nome host Hub IoT e chiave dispositivo), è necessario che l'applicazione client loro di connettersi alla soluzione di monitoraggio remoto. Fare clic su **Fine**.

    ![][4]

6. Verificare che il dispositivo viene visualizzato nella sezione devices. Stato dispositivo è **in attesa** fino a quando il dispositivo stabilisce una connessione per la soluzione di monitoraggio remoto.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/