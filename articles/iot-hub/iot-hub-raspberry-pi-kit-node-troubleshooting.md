<properties
 pageTitle="Risoluzione dei problemi | Microsoft Azure"
 description="Risoluzione dei problemi di pagina per un'esperienza utente Raspberry Pi Node"
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

# <a name="troubleshooting"></a>Risoluzione dei problemi

## <a name="hardware-issues"></a>Problemi hardware

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>L'applicazione viene eseguita correttamente ma il LED non a intermittenza

Questo problema è sempre correlato alla connettività di circuito hardware. Utilizzare la procedura seguente per identificare i problemi.

1. Verificare se si sceglie corretto **GPIO** sulla scheda. Due porte in questa lezione devono essere **GPIO GND (6 Pin)** e **04 GPIO (Pin 7)**.
2. Verificare se la polarità del LED sia corretta. Offrendo più dovrebbe essere indicato **positivo**, pin anode.
3. Usare il **Pin 3.3 v** e **Pin GND** il Pi al lampone 3. Indicazione del Pi alimentazione CC. Verificare se il LED funziona correttamente.

![LED specifiche](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Altri problemi hardware

Per informazioni sulla risoluzione dei problemi comuni in Raspberry Pi 3, fare riferimento alla [pagina di risoluzione dei problemi ufficiale](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problemi di pacchetto Node

### <a name="no-response-during-gulp-tasks"></a>Nessuna risposta durante confessano attività

Se si verificano problemi confessano attività in esecuzione, è possibile aggiungere il `--verbose` opzione per il debug. Provare a terminare le attività correnti confessano con `Ctrl + C` e quindi eseguire il comando seguente nella finestra di console per visualizzare i messaggi di debug. Verranno visualizzati messaggi di errore dettagliati stampati nell'output console. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemi di individuazione di dispositivi

Per la risoluzione dei problemi comuni di `devdisco` comando, selezionare il [file Leggimi](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="other-npm-issues"></a>Altri problemi NPM

Provare ad aggiornare il pacchetto NPM con il comando seguente:

```bash
npm install -g npm
```

Se il problema persiste, lasciare i commenti alla fine di questo articolo o creare un problema Github il nostro [Archivio di esempio](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Il debug remoto

### <a name="run-the-sample-application-in-debug-mode"></a>Eseguire l'applicazione di esempio in modalità debug

```bash
gulp run --debug
```

Quando il motore di debug è pronto, è necessario vedere ```Debugger listening on port 5858``` nell'output della console.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Configurare il codice di Visual Studio per connettersi al dispositivo remoto

Aprire il pannello **Debug** sul lato sinistro.

Fare clic sul pulsante **Avvia debug** (F5) verde. Codice di Visual Studio verrà aperto un file **launch.json** , è necessario aggiornare.

Aggiornare il file **launch.json** con il seguente contenuto. Sostituire `[device hostname or IP address]` con l'indirizzo IP del dispositivo effettivo o nome host.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configurazione di debug remoto](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>La connessione all'applicazione remota

Fare clic sul pulsante **Avvia debug** (F5) verde per avviare il debug. 

Leggere [JavaScript nel codice Visual Studio](https://code.visualstudio.com/docs/languages/javascript#_debugging) per altre informazioni su verrà.

![Debug interattivo remoto](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemi CLI Azure

Azure CLI è una versione di anteprima. È possibile fare riferimento alla [Guida all'installazione di anteprima](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) per la ricerca di soluzioni.

Nel caso di qualsiasi bug con lo strumento, file di un [problema](https://github.com/Azure/azure-cli/issues) nella sezione dei **problemi** del repo GitHub.

Per informazioni sulla risoluzione dei problemi comuni, selezionare il [file Leggimi](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemi di installazione Python

### <a name="legacy-installation-issues-macos"></a>Problemi di installazione legacy (Mac OS)

Quando si installa **pip**, viene generato un errore di autorizzazione quando sono presenti pacchetti legacy che vengono installati con le autorizzazioni **su** . Questa situazione è dovuto al fatto che installazione precedente di Python mediante forte (Mac OS) non è stato disinstallato completamente. Alcuni pacchetti **pip** di un'installazione precedente sono stati creati da radice, che causa l'errore di autorizzazione. La soluzione consiste nel rimuovere i pacchetti installati da radice. Per completare questa attività, procedere come segue:

1. Passare a /usr/local/lib/python2.7/site-packages
2. Elenco pacchetti per creare, radice:`ls -l | grep root`
3. Disinstallare pacchetti da passaggio2:`sudo rm -rf {package name}`
4. Reinstallare Python.

## <a name="azure-iot-hub-issues"></a>Problemi di hub IoT Azure

Se è stato completato il provisioning hub IoT Azure con `azure-cli`, ed è necessario uno strumento per la gestione di dispositivi che si connettono all'hub IoT, provare a strumenti seguenti:

### <a name="device-explorer"></a>Esplora dispositivi

[Esplora dispositivi](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) eseguibile sul computer locale di Windows e si connette a un centro di raccolta IoT in Azure. Comunica con gli [IoT Hub endpoint](iot-hub-devguide.md)seguenti:

- *Gestione delle identità di dispositivo* per eseguire il provisioning e gestione di dispositivi registrato con il tuo fulcro IoT.
- *Ricezione dispositivo nel cloud* che consentono di controllare i messaggi inviati dal dispositivo per il tuo fulcro IoT.
- Consentono di inviare messaggi per i dispositivi dall'hub IoT *inviare cloud al dispositivo* .

Configurare il `IoT hub connection string` all'interno di questo strumento da usare tutte le funzionalità.

### <a name="iot-hub-explorer"></a>Hub IoT Esplora risorse

[Hub IoT Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) è uno strumento CLI multipiattaforma di esempio per gestire i clienti di dispositivo. Lo strumento consente di gestire i dispositivi del Registro di sistema di identità e monitorare i messaggi di dispositivo nel cloud e inviare comandi cloud al dispositivo.

Per installare l'ultima versione di (versione non definitiva) dello strumento explorer iothub, eseguire il comando seguente nel proprio ambiente della riga di comando:

```
npm install -g iothub-explorer@latest
```

Per ottenere informazioni aggiuntive su tutti i comandi di Esplora risorse iothub e i relativi parametri, è possibile utilizzare il comando seguente:

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>Consente di gestire le risorse portale Azure

In tutte le lezioni un'esperienza completa con CLI viene fornita per creare e gestire tutte le risorse Azure. È consigliabile anche usare il [portale di Azure](../azure-portal-overview.md) Guida disposizione, gestire e le risorse Azure di debug.

## <a name="azure-storage-issues"></a>Problemi di archiviazione Azure

[Esplora archivi di Microsoft Azure (Preview)](http://storageexplorer.com) è un'applicazione autonoma di Microsoft che consente di utilizzare i dati di Azure lo spazio di archiviazione in Windows, Mac OS e Linux. Consente di connettersi alla tabella ed esaminare i dati al suo interno. È possibile utilizzare questo strumento per la risoluzione dei problemi relativi all'archiviazione Azure.
