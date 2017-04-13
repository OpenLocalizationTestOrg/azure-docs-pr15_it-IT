<properties
 pageTitle="Eseguire l'applicazione di esempio per inviare messaggi di dispositivo nel cloud | Microsoft Azure"
 description="Distribuire ed eseguire un'applicazione di esempio per i 3 di pi greco Raspberry che invia messaggi a IoT hub e lampeggiante il LED."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="32-run-sample-application-to-send-device-to-cloud-messages"></a>3.2 eseguire l'applicazione di esempio per inviare messaggi di dispositivo nel cloud

## <a name="321-what-you-will-do"></a>3.2.1 azioni da adottare

Distribuire ed eseguire un'applicazione di esempio i 3 di pi greco Raspberry che invia messaggi per il tuo fulcro IoT. Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="322-what-you-will-learn"></a>3.2.2 informazioni contenute

- Come usare lo strumento confessano per distribuire ed eseguire l'applicazione di esempio Node il pi greco.

## <a name="323-what-you-need"></a>3.2.3 cosa occorre

- È necessario che sia completata la sezione precedente in questa lezione: [creare un'app di funzione Azure e un account di archiviazione Azure per elaborare e archiviare i messaggi hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="324-get-your-iot-hub-and-device-connection-strings"></a>3.2.4 ottenere le stringhe di connessione IoT hub e dispositivo

La stringa di connessione dispositivo usata per connettere il tuo fulcro IoT di pi greco. Stringa di connessione hub IoT viene utilizzato per connettere il tuo fulcro IoT all'identità del dispositivo che rappresenta il Pi nell'hub IoT.

- Visualizzare la stringa di connessione hub IoT eseguendo il seguente comando CLI Azure:

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}`è il nome specificato nella lezione 2. Usare `iot-sample` come valore di `{resource group name}` se non è stato modificato il valore della lezione 2.

- Visualizzare la stringa di connessione dispositivo eseguendo il comando seguente:

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}`ha lo stesso valore di quella utilizzata con il comando precedente. Utilizzare `iot-sample` come valore di `{resource group name}` e usare `myraspberrypi` come valore di `{device id}` se non è stato modificato il valore della lezione 2.

## <a name="325-configure-the-device-connection"></a>3.2.5 configurare la connessione al dispositivo

1. Inizializzare il file di configurazione eseguendo i comandi seguenti:

    ```bash
    npm install
    gulp init
    ```

2. Aprire il file di configurazione del dispositivo `config-raspberrypi.json` in Visual Studio codice eseguendo il comando seguente:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

    ![config.JSON](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)

3. Effettuare le seguenti sostituzioni di `config-raspberrypi.json` file:

  - Sostituire **[hostname dispositivo o l'indirizzo IP]** con l'indirizzo IP del dispositivo o nome host ottenuto dal `device-discovery-cli` o con il valore ereditata dallo configurato nella lezione 1.
  - Sostituire **[stringa di connessione dispositivo IoT]** con il `device connection string` è ottenuto.
  - Sostituire **[stringa di connessione hub IoT]** con il `iot hub connection string` è ottenuto.

Si aggiorna il `config-raspberrypi.json` file in modo che è possibile distribuire l'applicazione di esempio dal proprio computer.

## <a name="326-deploy-and-run-the-sample-application"></a>3.2.6 distribuire ed eseguire l'applicazione di esempio

Distribuire ed eseguire l'applicazione di esempio il Pi eseguendo il comando seguente:

```bash
gulp
```

> [AZURE.NOTE] Viene eseguita l'operazione di confessano predefinito `install-tools`, `deploy`, e `run` le attività in sequenza. [Lezione 1](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md), è stato eseguito queste operazioni una dopo l'altra separatamente.

## <a name="327-verify-the-sample-application-works"></a>3.2.7 verificare il corretto funzionamento campione

Verrà visualizzato il LED che sia connesso al Pi a intermittenza ogni due secondi. Ogni volta che il LED lampeggiante, l'applicazione di esempio invia un messaggio per il tuo fulcro IoT e verifica che il messaggio è stato inviato a un centro di raccolta IoT. Inoltre, per ogni messaggio ricevuto dall'hub IoT, il messaggio viene visualizzato nella finestra della console. L'applicazione di esempio viene chiusa automaticamente dopo l'invio di 20 messaggi.

![](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="328-summary"></a>3.2.8 riepilogo

È stato distribuito ed eseguire la nuova applicazione di esempio intermittenza nel Pi per inviare messaggi di dispositivo nel cloud per il tuo fulcro IoT. È possibile spostare alla sezione successiva per monitorare i messaggi quando vengono scritti nell'account di archiviazione Azure.

## <a name="next-steps"></a>Passaggi successivi

[3.3 messaggi letti persistente in archiviazione Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)