<properties
   pageTitle="Installare il dispositivo 8100 StorSimple | Microsoft Azure"
   description="Descrive come decomprimere, rack montaggio e cavo dispositivo 8100 StorSimple prima di distribuire e configurare il software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Decomprimere, montaggio su rack e cavo dispositivo 8100 StorSimple

## <a name="overview"></a>Panoramica

Il 8100 StorSimple di Microsoft Azure è un simbolo singola installati su rack dispositivo. In questa esercitazione viene illustrato come decomprimere, installazione a rack e cavo 8100 StorSimple dispositivo hardware prima di configurare e distribuire il dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Decomprimere il dispositivo 8100 StorSimple

La procedura seguente istruzioni chiara e dettagliata su come decomprimere il dispositivo di archiviazione StorSimple 8100. Il dispositivo viene fornito in una singola casella.

### <a name="prepare-to-unpack-your-device"></a>Prepararsi a decomprimere il dispositivo

Prima decomprimere il dispositivo, leggere le informazioni seguenti.

![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona approfondita](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **avviso!**

1. Assicurarsi di avere due persone disponibili per gestire lo spessore del locale se si sta gestendo manualmente. Un simbolo completamente configurato di valutare fino a 32 kg (70 kg.).
1. Posizionare la casella su una superficie piatta e Livella.

Successivamente, eseguire la procedura seguente per decomprimere il dispositivo.

#### <a name="to-unpack-your-device"></a>Per decomprimere il dispositivo

1. Esaminare la casella e il materiale espanso imballaggio per crushes, tagli, danni acqua o altri danni più evidenti. Se la casella o sull'imballaggio è gravemente danneggiato, non è possibile aprire la casella. Informazioni [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per informazioni su come verificare se il dispositivo è funzioni correttamente.

2. Decomprimere la casella. Nella figura seguente mostra la visualizzazione del dispositivo StorSimple decompressa.

     ![Decomprimere il dispositivo di archiviazione](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Visualizzazione decompressi del dispositivo di archiviazione**

     Etichetta | Descrizione
     ----- | -------------
     1     | Casella documento
     2     | Espanso inferiore
     3     | Dispositivo
     4     | Superiore espanso
     5     | Casella accessorio


3. Al termine della decompressione finestra, assicurarsi di avere:

   - 1 dispositivo simbolo singola
   - 2 i cavi di alimentazione
   - 1 incrociato cavo Ethernet
   - 2 cavi console seriale
   - 1 convertitore seriale USB per l'accesso seriale
   - 1 cacciavite di T10 alterazione prova
   - QSFP 4-a-funzione + schede per l'utilizzo con 10 interfacce di rete GbE
   - 1 rack kit di montaggio (2 Guide lato con hardware per montaggio)
   - Guida introduttiva documentazione

    Se non si dispone uno qualsiasi degli elementi elencati sopra, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

Il passaggio successivo consiste nel dispositivo di installazione a rack.

## <a name="rack-mount-your-storsimple-8100-device"></a>Il dispositivo 8100 StorSimple di installazione a rack

Seguire la procedura seguente per installare il dispositivo di archiviazione 8100 StorSimple in un rack da 19 standard con anteriore e posteriore post. Il dispositivo 8100 StorSimple dispone di un singolo simbolo principale.

L'installazione è costituita da più passaggi, ognuno dei quali viene descritto nelle procedure seguenti.

> [AZURE.IMPORTANT]
Dispositivi StorSimple devono essere installato su per il corretto funzionamento.

### <a name="prepare-the-site"></a>Preparare il sito

Il dispositivo deve essere installato in un rack da 19 standard contenente anteriore e posteriore post. Utilizzare la procedura seguente per preparare l'ambiente per l'installazione di rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Per preparare il sito per l'installazione di rack

1. Assicurarsi che il dispositivo viene posizionato in modo sicuro sul lavoro flat stabile e livello superficie (o simile).

2. Verificare che il sito in cui si desidera configurare disponga alimentazione standard da un'origine indipendente o un'unità di distribuzione di alimentazione (PDU) rack con un gruppo di continuità (Sportivo).

3. Assicurarsi che un intervallo di 2U aperto è disponibile nei rack in cui si intende installare il dispositivo.

![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona approfondita](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **avviso!**

Assicurarsi di avere due persone disponibili per gestire il peso se si gestisce la configurazione di dispositivi manualmente. Un simbolo completamente configurato di valutare fino a 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Prerequisiti rack

Simbolo 8100 è progettato per l'installazione in un standard rack da 19 con:

- Profondità minima di 27.84 pollici dal rack post al post.
- Peso massimo di 32 kg per il dispositivo
- La pressione massima di 5 Pascal (colonna d'acqua 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit rail montaggio su rack

Viene fornito un insieme di montaggio Guide per l'utilizzo con l'archivio rack da 19. Le guide sono stati testati per gestire il peso massimo simbolo. Queste guide consente inoltre l'installazione di più allegati senza alcuna perdita di spazio all'interno del rack.


#### <a name="to-install-the-device-on-the-rails"></a>Per installare il dispositivo nelle guide

2. Eseguire questo passaggio solo se Guide interne non è installati nel dispositivo. In genere, guide interne sono installati in fase di produzione. Se non sono installati Guide, installare le diapositive rail a sinistra e destra per i lati del telaio simbolo. Si allega con sei viti metriche su entrambi i lati. Per evitare con orientamento, le diapositive rail contrassegnate **1H-anteriore** e **relativa – Front**end apposta dietro l'allegato è estremità affusolata.<br/>

    ![Associare diapositive rail a chassis simbolo](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **le diapositive rail interna di connessione per i lati del locale**

       Etichetta | Descrizione
       ----- | -----------
       1     | M x4 3 viti pulsante testa
       2     | Diapositive chassis

3. Collegare l'esterno rail a sinistra e assembly esterno corretto ai membri del verticale CAB rack. Parentesi quadre sono contrassegnate **LH** **relativa**e **il lato alto** per guidare l'orientamento corretto.

4. Individuare il PIN rail sulla parte anteriore e posteriore di assembly rail. Estendere rail per adattare tra i post rack e inserire il PIN anteriore e posteriore rack post membro verticale holes. Assicurarsi che l'assembly rail livello.

5. Utilizzare due viti di metriche fornite per proteggere il gruppo rail il rack membri verticali. Utilizzare le viti in primo piano e uno sul retro.

6. Ripetere questi passaggi per altri assembly rail.<br/>

     ![Associare diapositive rail a rack CAB](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Associare assembly rail esterno al rack**

     Etichetta | Descrizione
     ----- | -----------
     1     | Vite di bloccaggio
     2     | Rack anteriore quadrato ingressi post vite
     3     | PIN posizione anteriore rail a sinistra
     4     | Vite di bloccaggio
     5     | PIN posizione posteriore rail a sinistra


### <a name="mounting-the-device-in-the-rack"></a>Il montaggio del dispositivo nel rack

Usa i fisse che sono state installate, eseguire la procedura seguente per installare il dispositivo nel rack.

#### <a name="to-mount-the-device"></a>Per installare il dispositivo

1. Con un Assistente sollevare il simbolo e allineare con guide rack.

2. Inserire con attenzione il dispositivo le guide e quindi inserire il dispositivo completamente rack CAB.<br/>

    ![Inserimento di dispositivo nel rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Il montaggio del dispositivo nel rack**


3. Rimuovere le terminazioni di flangia anteriore destro e sinistro il pull e BLOC MAIUSC gratuito. Maiuscole flangia semplicemente bloccata nella flange.

5. Proteggere l'allegato nel rack installando una vite cacciavite testa fornita tramite ciascuna flangia, sinistro e destro.

4. Installare le terminazioni di flangia premendo loro nella posizione desiderata e li aggancio in posizione.<br/>

     ![L'installazione di BLOC MAIUSC flangia](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **L'installazione di BLOC MAIUSC flangia**

     Etichetta | Descrizione
     ----- | -----------
     1     | Vite attacchi simbolo

Il passaggio successivo consiste nel dispositivo per power, rete e accesso seriale di cablaggio.

## <a name="cable-your-storsimple-8100-device"></a>Cavo dispositivo 8100 StorSimple

Le procedure seguenti illustrano come cavo dispositivo StorSimple 8100 per power, rete e connessioni seriali.

### <a name="prerequisites"></a>Prerequisiti di

Prima di iniziare il cablaggio del dispositivo, è necessario:

- Il dispositivo di archiviazione, completamente decompresso e sul rack.

- 2 i cavi di alimentazione fornita in dotazione con il dispositivo

- Accesso alle unità di distribuzione Power 2 (scelta consigliata).

- Cavi di rete

- Fornito cavi seriali

- Convertitore USB seriale con il driver appropriato installato sul PC (se necessario)

- Fornito QSFP 4-a-funzione + schede per l'utilizzo con 10 interfacce di rete GbE

- [Hardware supportato 10 GbE interfacce di rete in un dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Cavo di alimentazione

Il dispositivo include ridondanti Power e moduli raffreddamento (PCMs). Sia PCMs deve essere installata e connessa a origini diverse power per assicurare la disponibilità elevata.

Eseguire la procedura seguente per il dispositivo per la potenza di cablaggio.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cavi di rete

Il dispositivo è una configurazione standby attivo: in qualsiasi momento, un modulo di controller è attivo e l'elaborazione di tutte le operazioni su disco e di rete durante l'altro modulo controller in standby. Se un controller non riesce, controller standby attivato immediatamente e continua in tutto il disco e le operazioni di rete.

Per cedere il questo controller ridondanti, è necessario cavo di rete dispositivo come descritto nei passaggi seguenti.

#### <a name="to-cable-for-network-connection"></a>Cavo di connessione di rete

1. Il dispositivo ha sei interfacce di rete su ciascun controller: quattro 1 GB/s, e due 10 porte Ethernet GB/s. Identificare le varie porte dati sulla base del dispositivo.

    ![Base del dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Nuovo del dispositivo con porte dati**

     Etichetta   | Descrizione
     ------- | -----------
     0,1,4,5 |  Interfacce di rete GbE 1
     2,3     | 10 interfacce di rete GbE
     6       | Porte seriali

2. Visualizzare il diagramma seguente per cavi di rete. (La configurazione di rete minimo indicata dalle linee blu a tinta unita. Configurazione aggiuntivi necessaria per prestazioni e disponibilità è indicata dalle linee tratteggiate.)


    ![Cavo dispositivo 2U per la rete](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cavi per il dispositivo di rete**


  	|Etichetta | Descrizione |
  	|----- | ----------- |
  	| RISPOSTE    | LAN con accesso a Internet |
  	| B    | Controller 0 |
  	| C    | PCM 0 |
  	| D    | Controller 1 |
  	| E    | PCM 1 |
  	| F E G | Host |
  	| 0-5  | Interfacce di rete |



Quando il dispositivo di cablaggio, i requisiti minimi di configurazione:


- Almeno due interfacce di rete connessa in ogni controller con uno per l'accesso cloud e uno per iSCSI. I dati 0 porta automaticamente è abilitata e configurata tramite la console seriale del dispositivo. Oltre a dati 0, un'altra porta dati anche deve essere configurato tramite il portale classico Azure. In questo caso, la connessione dati 0 porta LAN principale (rete con accesso a Internet). Altre porte dati possono essere collegate al segmento SAN/iSCSI LAN (VLAN) della rete, in base al ruolo desiderato.

- Interfacce identiche in ogni controller connessi alla stessa rete per assicurare la disponibilità in caso di errore controller caso. Ad esempio, se si sceglie di connettere i dati 0 e 3 di dati per uno dei controller, è necessario connettere i dati corrispondenti 0 e 3 di dati in altro controller.

Prendere in considerazione per prestazioni e disponibilità:


- Se possibile, configurare una coppia di rete per l'accesso cloud (1 GbE) e un'altra coppia per iSCSI (10 GbE consigliato) in ogni controller.

- Se possibile, connettersi interfacce di rete da ogni controller di due diverse opzioni per assicurare la disponibilità con un errore di parametro. La figura mostra due 10 GbE interfacce di rete, dati 2 e 3 di dati, da ogni controller connesso a due opzioni diverse.

Per ulteriori informazioni, consultare le **interfacce di rete** in base ai [requisiti di disponibilità per il dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se si utilizza funzione + ricetrasmettitori con le interfacce di rete GbE 10, utilizzare QSFP fornito-funzione + schede. Per ulteriori informazioni, vedere [hardware supportati 10 GbE interfacce di rete in un dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).



### <a name="serial-port-cabling"></a>Porta seriale difettosi

Eseguire i passaggi seguenti per la porta seriale di cablaggio.

#### <a name="to-cable-for-serial-connection"></a>Cavo per connessione seriale

1. Il dispositivo ha una porta seriale su ciascun controller identificato dall'icona di chiave inglese. Fare riferimento all'illustrazione nella sezione [cavi di rete](#network-cabling) per individuare le porte seriali di base del dispositivo.

2. Identificare il controller active sulla base del dispositivo. Un LED azzurro lampeggiante indica che il controller sia attivo.

3. Utilizzare i cavi seriali forniti, se necessario, il convertitore USB seriale per il laptop e connettere la console o il computer (con emulazione terminale al dispositivo) alla porta seriale del controller di attivo.

4. Installare i driver seriale USB (forniti con il dispositivo) nel computer in uso.

5. Configurare la connessione seriale come indicato di seguito: trasmissione 115.200, 8 bit di dati, 1 bit di stop, non uniformità e controllo del flusso impostata su Nessuno.

6. Verificare che la connessione funzioni premendo INVIO sulla console. Deve essere visualizzato un menu console seriale.

>[AZURE.NOTE] **Gestione la sicurezza**: quando il dispositivo è installato in un Data Center remoto o in una chat room computer con accesso limitato, assicurarsi che le connessioni seriali a entrambi i controller sono sempre connessi alla console seriale o dispositivo simile. In questo modo telecomando fuori banda e le operazioni di supporto se sono presenti interruzioni di rete o errori imprevisti.

Il dispositivo sia collegato a questo punto per power, accesso alla rete e connettività seriale. Il passaggio successivo consiste nel configurare il software e distribuire il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e configurare il dispositivo di StorSimple locale](storsimple-deployment-walkthrough.md).
