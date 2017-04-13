<properties
 pageTitle="Ottenere strumenti Azure (Mac OS 10.10) | Microsoft Azure"
 description="Installare Python e Azure della riga di comando CLI (interfaccia Azure) in Mac OS."
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

# <a name="21-get-azure-tools-macos-1010"></a>2.1 strumenti Azure (Mac OS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 azioni da adottare

Installare il Azure interfaccia della riga (comando Azure). Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 informazioni contenute

- Come installare CLI Azure.
- Come aggiungere sottogruppo IoT di Azure CLI.

## <a name="213-what-you-need"></a>2.1.3 cosa occorre

- Un computer Mac con connessione a Internet
- Un abbonamento attivo a Azure. Se non si dispone di un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## <a name="214-install-python"></a>2.1.4 installare Python

Sebbene Mac OS viene fornito con Python 2.7 all'esterno della casella, è consigliabile installare Python tramite Homebrew. Vedere [Installazione Python in Mac OS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Installare Python e pip eseguendo il comando seguente:

```bash
brew install python
```

## <a name="215-install-the-azure-cli"></a>2.1.5 installare CLI Azure

CLI Azure offre un'esperienza della riga di comando multipiattaforma per Azure, che consente di lavorare direttamente dalla riga di comando eseguire il provisioning e gestire le risorse. 

Per installare l'ultima CLI Azure, procedere come segue:

1. Eseguire i comandi seguenti in una finestra. Può richiedere cinque minuti per installare CLI Azure.

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. Verificare l'installazione eseguendo il comando seguente:

    ```bash
    az iot -h
    ```
  
Verrà visualizzato il seguente output se l'installazione viene completata.

![AZ iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="215-summary"></a>2.1.5 riepilogo

È stato installato CLI Azure. Passare alla sezione successiva per creare l'identità di Azure IoT Hub e dispositivo che usa CLI Azure.

## <a name="next-steps"></a>Passaggi successivi

[2.2 creare il tuo fulcro IoT e registrare i 3 di pi greco Raspberry](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
