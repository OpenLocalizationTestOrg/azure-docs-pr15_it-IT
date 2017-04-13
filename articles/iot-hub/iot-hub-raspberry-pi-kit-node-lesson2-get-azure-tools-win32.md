<properties
 pageTitle="Ottenere strumenti Azure (Windows 7 +) | Microsoft Azure"
 description="Installare Python e Azure della riga di comando CLI (interfaccia Azure) in Windows 7 e versioni successive."
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

# <a name="21-get-azure-tools-windows-7-"></a>2.1 ottenere strumenti Azure (Windows 7 +)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 azioni da adottare

Installa Python e Azure della riga di comando interfaccia (Azure CLI). Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 informazioni contenute

- Come installare Python.
- Come installare CLI Azure.

## <a name="213-what-you-need"></a>2.1.3 cosa occorre

- Un computer Windows con connessione a Internet
- Un abbonamento attivo a Azure. Se non si dispone di un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## <a name="214-install-python"></a>2.1.4 installare Python

Installare Python in un computer Windows. È possibile installare Python 2.7, 3.4 o 3.5. In questa esercitazione si basa su 2.7 Python. Se è già stato installato Python, passare alla sezione 2.1.5.

[Ottenere Python per Windows](https://www.python.org/downloads/)

È necessario aggiungere il percorso di cartelle in cui sono installati python.exe e pip.exe al sistema `PATH` variabile di ambiente. Per impostazione predefinita, python.exe è installato `C:\Python27` e pip.exe viene installato in `C:\Python27\Scripts`.

## <a name="215-install-the-azure-cli"></a>2.1.5 installare CLI Azure

CLI Azure offre un'esperienza della riga di comando multipiattaforma per Azure, che consente di lavorare direttamente dalla riga di comando eseguire il provisioning e gestire le risorse.

Per installare CLI Azure, procedere come segue:

1. Aprire una finestra del prompt dei comandi come amministratore.
2. Eseguire i comandi seguenti:

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```
3. Verificare l'installazione eseguendo il comando seguente:

    ```bash
    az iot -h
    ```

Se l'installazione viene completata, viene visualizzato il seguente output.

![AZ iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="216-summary"></a>2.1.6 riepilogo

È stato installato CLI Azure. Passare alla sezione successiva per creare l'identità di Azure IoT Hub e dispositivo che usa CLI Azure.

## <a name="next-steps"></a>Passaggi successivi

[2.2 creare il tuo fulcro IoT e registrare i 3 di pi greco Raspberry](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
