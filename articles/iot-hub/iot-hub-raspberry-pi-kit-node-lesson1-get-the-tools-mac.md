<properties
 pageTitle="Tutti gli strumenti (Mac OS 10.10) | Microsoft Azure"
 description="Scaricare e installare gli strumenti necessari e software per la prima applicazione di esempio per il pi greco in Mac OS."
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

# <a name="12-get-the-tools-macos-1010"></a>1.2 tutti gli strumenti (Mac OS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 azioni da adottare

Scaricare gli strumenti di sviluppo e il software per la prima applicazione di esempio per il Pi Raspberry 3. Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 informazioni contenute
In questa sezione verranno fornite:

- Come installare fra e Node
    - [Fra](https://git-scm.com) è un sistema di controllo Apri origine distribuito versione. L'applicazione di esempio per questa lezione è archiviata in fra.
    - [Node](https://nodejs.org/en/) è una fase di esecuzione di JavaScript con un ecosistema pacchetto RTF.
- Come utilizzare NPM per installare altri strumenti di sviluppo Node.
  - La versione minima necessaria di Node è 4,5 risultati.
  - [NPM](https://www.npmjs.com) corrisponde a uno dei responsabili pacchetto per Node.

## <a name="123-what-you-need"></a>1.2.3 cosa occorre

- Una connessione Internet per scaricare gli strumenti di sviluppo e il software
- Un computer Mac che eseguono Mac OS Yosemite (10.10) o versioni successive

## <a name="124-install-git-and-nodejs"></a>1.2.4 installare fra e Node

Per installare fra e Node, utilizzare l'utilità di gestione di pacchetto [Homebrew](http://brew.sh) procedendo come segue:

1. Installare Homebrew. Se è già stato installato Homebrew, andare al passaggio 2.
  1. Premere `Cmd + Space` e immettere `Terminal` per aprire una finestra del terminale.
  2. Eseguire il comando seguente:

    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. Installare fra e Node eseguendo il comando seguente:

    ```bash
    brew install node git
    ```

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 installare altri strumenti di sviluppo Node

Utilizzare [gulp.js](http://gulpjs.com) per automatizzare l'implementazione di applicazione di esempio per il pi greco. È anche possibile usare il [dispositivo-individuazione-cli](https://github.com/Azure/device-discovery-cli) per recuperare le informazioni di rete sui dispositivi IoT.

Installare `gulp` e `device-discovery-cli` eseguendo il seguente comando nella finestra del terminale:

```bash
sudo npm install -g device-discovery-cli gulp
```

Se si verificano problemi di installazione Node e questi strumenti di sviluppo aggiuntivi in Mac OS, vedere [risoluzione dei problemi di Guida](iot-hub-raspberry-pi-kit-node-troubleshooting.md) per le soluzioni ai problemi comuni.

## <a name="126-install-visual-studio-code"></a>1.2.6 installare codice Visual Studio

[Scaricare](https://code.visualstudio.com/docs/setup/osx) e installare codice Visual Studio. Codice di Visual Studio è un editor di codice sorgente leggera ma potente per Windows, Linux e Mac OS. Utilizzare questo editor in un secondo momento nell'esercitazione per modificare il codice di esempio.

## <a name="127-summary"></a>1.2.7 riepilogo

È stato installato un software per l'applicazione di esempio prima e gli strumenti di sviluppo necessari. Nella sezione successiva è creare, distribuire ed eseguire l'applicazione di esempio con il pi greco.

## <a name="next-steps"></a>Passaggi successivi

[1.3 creare e distribuire l'applicazione di esempio intermittenza](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
