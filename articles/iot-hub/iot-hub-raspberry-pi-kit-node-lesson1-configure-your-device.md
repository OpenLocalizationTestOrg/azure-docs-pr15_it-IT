<properties
 pageTitle="Configurare il dispositivo | Microsoft Azure"
 description="Configurare i 3 di pi greco Raspberry per il primo utilizzo e installare il sistema operativo Raspbian, un sistema operativo gratuito ottimizzate per l'hardware Raspberry pi greco."
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

# <a name="11-configure-your-device"></a>1.1 configurare il dispositivo

## <a name="111-what-you-will-do"></a>1.1.1 azioni da adottare

Configurare il Pi per il primo utilizzo e installare il sistema operativo Raspbian, un sistema operativo gratuito ottimizzate per l'hardware Raspberry pi greco. Se è necessario soddisfare gli eventuali problemi, tentare le soluzioni di [risoluzione dei problemi di pagina](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="112-what-you-will-learn"></a>1.1.2 informazioni contenute

In questa sezione verranno fornite:

- Come installare Raspbian nel pi greco
- Come accendere il Pi tramite un cavo USB
- Come connettere il Pi alla rete tramite un cavo Ethernet o Wi-Fi
- Come aggiungere un LED per la breadboard e connetterlo al pi greco

## <a name="113-what-you-need"></a>1.1.3 cosa occorre

Per completare questa sezione, è necessario dalle parti seguenti da Starter Kit il Raspberry Pi 3:

- Discussioni Raspberry Pi 3
- Scheda MicroSD 16GB
- Il 5 v 2A alimentazione con il cavo USB micro 6 ft
- Il breadboard
- Cavi connettore
- Una resistenza Ohm 560
- Diffusi 10mm LED
- Il cavo Ethernet

![Aspetti dallo Starter Kit](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

È anche necessario:

- Una connessione cablata o wireless per il Pi a cui connettersi
- Una SD USB scheda mini SD scheda o per masterizzare l'immagine del sistema operativo alla scheda MicroSD.
- Un computer che esegue Windows, Mac o Linux. Il computer viene utilizzato per installare Raspbian nella scheda MicroSD.
- Una connessione a Internet per scaricare il software e gli strumenti necessari

## <a name="114-install-raspbian-on-the-microsd-card"></a>1.1.4 installare Raspbian nella scheda MicroSD

Preparare la scheda MicroSD per scrivere la foto Raspbian a.

1. Scaricare Raspbian.
  1. [Scaricare](https://www.raspberrypi.org/downloads/raspbian/) il file zip per Raspbian Jessie con Pixel.
  2. Estrarre l'immagine Raspbian in una cartella nel computer in uso.
2. Installare Raspbian alla scheda MicroSD.
  1. [Scaricare](https://www.etcher.io) e installare l'utilità di masterizzatore scheda SD Etcher.
  2. Eseguire Etcher e selezionare l'immagine Raspbian che è stato estratto nel passaggio 1.
  3. Selezionare l'unità di carta MicroSD.
    Nota: Etcher probabilmente è già selezionato l'unità corretta.
  4. Fare clic su Flash per installare Raspbian alla scheda MicroSD.
  5. Rimuovere la scheda MicroSD dal proprio computer al termine.
    Nota: È consigliabile rimuovere dalla scheda MicroSD direttamente perché Etcher automaticamente rimuove o Smonta scheda MicroSD dopo il completamento.
  6. Inserire la scheda MicroSD il pi greco.

![Inserire la scheda SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="115-power-on-your-pi"></a>1.1.5 accendere il pi greco

Accendere il Pi utilizzando il cavo USB micro e l'alimentazione.

![Accendere](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [AZURE.NOTE] È importante utilizzare l'alimentazione kit di almeno 2A per assicurarsi che il Raspberry alimentazione con capacità sufficiente a funzionare correttamente.

## <a name="116-connect-your-raspberry-pi-3-to-the-network"></a>1.1.6 connettere i 3 di pi greco Raspberry alla rete

È possibile collegare i Pi a una rete cablata o a una rete wireless. Assicurarsi che il Pi sia connesso alla stessa rete del computer in uso. Ad esempio, è possibile connettersi il Pi la stessa opzione che il computer sia connesso a.

### <a name="1161-connect-to-a-wired-network"></a>1.1.6.1 connettersi a una rete cablata

Utilizzare il cavo Ethernet per connettere il Pi alla rete cablata. Attivare i due LED sul Pi se la connessione.

![Connettersi tramite cavo Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="1162-connect-to-a-wireless-network"></a>1.1.6.2 connettersi a una rete wireless

Seguire le [istruzioni](https://www.raspberrypi.org/learning/software-guide/wifi/) della Fondazione di pi greco Raspberry a cui connettersi alla rete wireless il pi greco. Queste istruzioni è necessario connettersi innanzitutto un monitor e tastiera il pi greco.

## <a name="117-connect-the-led-to-your-pi"></a>1.1.7 il LED connettersi il pi greco

Per completare questa operazione, utilizzare [breadboard](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), i cavi di connettore, il LED e la resistenza. Si connette al porte [generiche interdipendenze](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) del pi greco. 

![Breadboard, LED e resistenza](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Connettere il segmento più breve dei LED **GPIO GND (6 Pin)**.
2. Connettere un segmento della resistenza offrendo più tempo del LED.
3. Connettere altri offrendo della resistenza **GPIO 4 (Pin 7)**.

Si noti che la polarità LED importante. Questa impostazione polarità è comunemente come basso attivo.

![Configurazione del](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Operazione riuscita! È stata configurata correttamente il pi greco.

## <a name="118-summary"></a>1.1.8 riepilogo

In questa sezione, aver appreso come configurare i Pi installando Raspbian, il Pi la connessione a una rete e connettersi un LED del pi greco. Si noti che il LED non ancora accende. Nella sezione successiva, installare il software in preparazione per l'esecuzione di un'applicazione di esempio con il pi greco e gli strumenti necessari.

![Hardware è pronto](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Passaggi successivi

[1.2 tutti gli strumenti](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
