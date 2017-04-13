<properties
   pageTitle="Installare il dispositivo 8600 StorSimple | Microsoft Azure"
   description="Descrive come decomprimere, rack montaggio e cavo dispositivo 8600 StorSimple prima di distribuire e configurare il software."
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Decomprimere, montaggio su rack e cavo dispositivo 8600 StorSimple

## <a name="overview"></a>Panoramica
Il 8600 StorSimple di Microsoft Azure è un dispositivo simbolo doppio ed è costituito da un primario e un simbolo EBOD. In questa esercitazione viene illustrato come decomprimere, installazione a rack e cavo 8600 StorSimple dispositivo hardware prima di configurare il software StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Decomprimere il dispositivo 8600 StorSimple

La procedura seguente istruzioni chiara e dettagliata su come decomprimere il dispositivo di archiviazione 8600 StorSimple. Il dispositivo viene fornito in due caselle, uno per il simbolo primario e un altro per il simbolo EBOD. Queste due caselle vengono quindi inserite in una singola casella.

### <a name="prepare-to-unpack-your-device"></a>Prepararsi a decomprimere il dispositivo

Prima decomprimere il dispositivo, leggere le informazioni seguenti.


![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona approfondita](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **avviso!**

1. Assicurarsi di avere due persone disponibili per gestire lo spessore del dispositivo, se si sta gestendo manualmente. Un simbolo completamente configurato di valutare fino a 32 kg (70 kg.).
1. Posizionare la casella su una superficie piatta e Livella.

Successivamente, eseguire la procedura seguente per decomprimere il dispositivo.

#### <a name="to-unpack-your-device"></a>Per decomprimere il dispositivo

1. Esaminare la casella e il materiale espanso imballaggio per crushes, tagli, danni acqua o altri danni più evidenti. Se la casella o sull'imballaggio è gravemente danneggiato, non è possibile aprire la casella. Informazioni [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per informazioni su come verificare se il dispositivo è funzioni correttamente.

2. Aprire la finestra di dialogo esterno e quindi creare le due caselle corrispondenti alle principale e allegati EBOD. È ora possibile decomprimere i principali e allegati EBOD. La figura seguente mostra la visualizzazione decompressa di uno di allegati.

    ![Decomprimere il dispositivo di archiviazione](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Visualizzazione decompressi del dispositivo di archiviazione**

     Etichetta | Descrizione
     ----- | -------------
     1     | Casella documento
     2     | Cavi SA (nella barra delle applicazioni accessori e cavi)
     3     | Espanso inferiore
     4     | Dispositivo
     5     | Superiore espanso
     6     | Casella accessorio

3. Al termine della decompressione le due caselle, assicurarsi di avere:

  - 1 simbolo principale (il simbolo principale e simbolo EBOD sono disponibili in due caselle distinte)
  - 1 simbolo EBOD
  - 4 cavo di alimentazione, 2 in ciascuna casella
  - 2 cavi SA (a cui connettersi simbolo principale simbolo EBOD)
  - 1 incrociato cavo Ethernet
  - 2 cavi console seriale
  - 1 convertitore seriale USB per l'accesso seriale
  - QSFP 4-a-funzione + schede per l'utilizzo con 10 interfacce di rete GbE
  - 2 rack Kit di montaggio (4 lato guide con montaggio hardware, 2 per il simbolo principale e simbolo EBOD), 1 in ogni casella
  - Guida introduttiva documentazione

    Se non si dispone uno qualsiasi degli elementi elencati sopra, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).  

Il passaggio successivo consiste nel dispositivo di installazione a rack.

## <a name="rack-mount-your-storsimple-8600-device"></a>Il dispositivo 8600 StorSimple di installazione a rack

Seguire la procedura seguente per installare il dispositivo di archiviazione 8600 StorSimple in un rack da 19 standard con anteriore e posteriore post. Questo dispositivo dotato di due allegati: un simbolo primario e un simbolo EBOD. Entrambe le informazioni di rack-montaggio.

L'installazione è costituita da più passaggi, ognuno dei quali viene descritto nelle procedure seguenti.

> [AZURE.IMPORTANT]
Dispositivi StorSimple devono essere installato su per il corretto funzionamento.



### <a name="site-preparation"></a>Preparazione del sito

Gli allegati devono essere installati in un rack da 19 standard con anteriore e posteriore post. Utilizzare la procedura seguente per preparare l'ambiente per l'installazione di rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Per preparare il sito per l'installazione di rack

1. Assicurarsi che i principali e allegati EBOD siano in sospeso in modo sicuro in un'area di lavoro semplice, stabile e livello (o simili).

2. Verificare che il sito in cui si desidera configurare disponga alimentazione standard da un'origine indipendente o su un rack Power distribuzione PDU (Unit) con un gruppo di continuità (Sportivo).

3. Verificare che l'intervallo di (2 X 2U) aperto uno 4U è disponibile nei rack in cui si intende installare l'allegati.

![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona approfondita](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **avviso!**

 Assicurarsi di avere due persone disponibili per gestire il peso se si gestisce la configurazione di dispositivi manualmente. Un simbolo completamente configurato di valutare fino a 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Prerequisiti rack

Gli allegati sono progettate per l'installazione in un standard rack da 19 con:

- Profondità minima di 27.84 pollici dal rack post al post
- Peso massimo di 32 kg per il dispositivo
- Pressione posteriore massima di 5 Pascal (colonna d'acqua 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit rail montaggio su rack

Una serie di guide di montaggio sarà fornita per l'utilizzo con l'archivio rack da 19. Le guide sono stati testati per gestire il peso massimo simbolo. Queste guide consente inoltre l'installazione di più allegati senza perdita di spazio all'interno del rack. Installare prima il simbolo EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Per installare il simbolo EBOD nelle guide

2. Eseguire questo passaggio solo se Guide interne non è installati nel dispositivo. In genere, guide interne sono installati in fase di produzione. Se non sono installati Guide, installare le diapositive rail a sinistra e destra per i lati del telaio simbolo. Si allega con sei viti metriche su entrambi i lati. Per evitare con orientamento, le diapositive rail contrassegnate **1H-anteriore** e **relativa – Front**end apposta dietro l'allegato è estremità affusolata.

    ![Associare diapositive rail a chassis simbolo](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Associare diapositive rail ai lati del locale**

    Etichetta | Descrizione
    ----- | -----------
    1     | M x4 3 viti pulsante testa
    2     | Diapositive chassis

3. Collegare il rail a sinistra e assembly corretto ai membri del verticale CAB rack. Parentesi quadre sono contrassegnate **LH** **relativa**e **il lato la** procedura orientamento corretto.

4. Individuare il PIN rail sulla parte anteriore e posteriore di assembly rail. Estendere rail per adattare tra i post rack e inserire il PIN anteriore e holes membro verticale posteriore rack post. Assicurarsi che l'assembly rail livello.

5. Proteggere il gruppo rail il rack membri verticali utilizzando due viti metriche fornite. Utilizzare le viti in primo piano e uno sul retro.

6. Ripetere questi passaggi per altri assembly rail.

     ![Associare diapositive rail a rack CAB](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Collegamento assembly rail a rack**

     Etichetta | Descrizione
     ----- | -----------
     1     | Vite di bloccaggio
     2     | Rack anteriore quadrato ingressi post vite
     3     | PIN posizione rail anteriore a sinistra
     4     | Vite di bloccaggio
     5     | PIN posizione rail posteriore a sinistra

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montaggio simbolo EBOD nel rack

Usa i fisse che sono state installate, eseguire la procedura seguente per installare il simbolo EBOD nel rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Per installare il simbolo EBOD

1. Con un Assistente sollevare il simbolo e allineare con guide rack.

2. Le guide con attenzione inserire il simbolo e quindi inserirlo completamente rack CAB.

    ![Inserimento di dispositivo nel rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montaggio l'allegato nel rack**

3. Rimuovere le terminazioni di flangia anteriore destro e sinistro il pull e BLOC MAIUSC gratuito. Maiuscole flangia semplicemente bloccata nella flange.

4. Proteggere il simbolo rack installando una vite cacciavite testa fornita tramite ciascuna flangia, sinistro e destro.

4. Installare le terminazioni di flangia premendo loro nella posizione desiderata e li blocca nella posizione desiderata.

     ![L'installazione di BLOC MAIUSC flangia](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **L'installazione di BLOC MAIUSC flangia**

     Etichetta | Descrizione
     ----- | -----------
     1     | Vite attacchi simbolo


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montaggio simbolo principale nel rack

Dopo aver montaggio simbolo EBOD, sarà necessario installare il simbolo principale eseguendo gli stessi passaggi.

> [AZURE.NOTE]
>
> - È possibile che alcuni bande orarie vuote nel rack tra simbolo principale e il simbolo EBOD.
> - Utilizzare il cavo SA fornito 2m a cui connettersi simbolo EBOD simbolo principale.
> - Esistono vincoli nella posizione relativa dell'unità di testa all'unità EBOD. Di conseguenza, il simbolo principale può essere inserita nell'intervallo di aperto superiore e il simbolo EBOD riportata di seguito, o viceversa.

Il passaggio successivo consiste nel dispositivo per power, rete e accesso seriale di cablaggio.

## <a name="cable-your-storsimple-8600-device"></a>Cavo dispositivo 8600 StorSimple

Le procedure seguenti illustrano come cavo dispositivo StorSimple 8600 per power, rete e connessioni seriali.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a cavo del dispositivo, è necessario:

- Il simbolo principale e simbolo EBOD completamente decompresso.
- 4 cavi di alimentazione (2 ogni primario e il simbolo EBOD) fornita in dotazione con il dispositivo
- 2 cavi SA forniti con il dispositivo a cui connettersi simbolo EBOD simbolo principale
- Accesso a 2 Power unità di distribuzione (PDU) (scelta consigliata)
- Cavi di rete
- Fornito cavi seriali
- Convertitore seriale USB con il driver appropriato installato sul PC (se necessario)
- Fornito QSFP 4-a-funzione + schede per l'utilizzo con 10 interfacce di rete GbE
- [Hardware supportato 10 GbE interfacce di rete in un dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SA e Power difettosi

Il dispositivo ha un simbolo primario e un simbolo EBOD. È necessario essere cavi insieme per power e connettività seriale collegato SCSI (SA) le unità di misura.

Quando si configura il dispositivo per la prima volta, eseguire i passaggi per SA difettosi prima di tutto e quindi completare i passaggi di power cablaggio.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cavi di rete

Il dispositivo è in una configurazione standby attivo: in qualsiasi momento, un modulo di controller è attivo e l'elaborazione di tutte le operazioni su disco e di rete durante l'altro modulo controller in standby. Se si verifica un errore nel controller, controller standby immediatamente attiva e continua in tutte le operazioni su disco e di rete.

Per cedere il questo controller ridondanti, è necessario cavo di rete dispositivo come illustrato di seguito.

#### <a name="to-cable-for-network-connection"></a>Cavo di connessione di rete

1. Il dispositivo ha sei interfacce di rete su ciascun controller: quattro 1 e delle due 10 GB/s Ethernet porte. Fare riferimento alla figura riportata di seguito per identificare le porte dati sulla base del dispositivo.

     ![Base del dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Indietro di un dispositivo con le porte di dati**

     Etichetta   | Descrizione
     ------- | -----------
     0,1,4,5 |  Interfacce di rete GbE 1
     2,3     | 10 interfacce di rete GbE
     6       | Porte seriali



1. Visualizzare il diagramma seguente per cavi di rete. (La configurazione di rete minimo indicata dalle linee blu a tinta unita. Per prestazioni e disponibilità, operazioni di configurazione richieste è indicato dalle linee tratteggiate.)

![Cavo dispositivo 4U per la rete](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cavi per il dispositivo di rete**

Etichetta | Descrizione
----- | -----------
RISPOSTE    | LAN con accesso a Internet
B    | Controller 0
C    | PCM 0
D    | Controller 1
E    | 1 PCM
F    | Controller EBOD 0
G    | Controller EBOD 1
H, SI  | Host (ad esempio file server)
0-5  | Interfacce di rete
6    | Simbolo principale
7    | Simbolo EBOD

Quando il dispositivo di cablaggio, i requisiti minimi di configurazione:


- Almeno due interfacce di rete connessa in ogni controller con uno per l'accesso cloud e uno per iSCSI. I dati 0 porta automaticamente è abilitata e configurata tramite la console seriale del dispositivo. Oltre a dati 0, un'altra porta dati anche deve essere configurato tramite il portale classico Azure. In questo caso, la connessione dati 0 porta LAN principale (rete con accesso a Internet). Altre porte dati possono essere collegate al segmento SAN/iSCSI LAN (VLAN) della rete, in base al ruolo desiderato.

- Interfacce identiche in ogni controller connessi alla stessa rete per assicurare la disponibilità in caso di errore controller caso. Ad esempio, se si sceglie di connettere i dati 0 e 3 di dati per uno dei controller, è necessario connettere i dati corrispondenti 0 e 3 di dati in altro controller.

Prendere in considerazione per prestazioni e disponibilità:


- Se possibile, configurare una coppia di rete per l'accesso cloud (1 GbE) e un'altra coppia per iSCSI (10 GbE consigliato) in ogni controller.

- Se possibile, connettersi interfacce di rete da ogni controller di due diverse opzioni per assicurare la disponibilità con un errore di parametro. La figura mostra due 10 GbE interfacce di rete, dati 2 e 3 di dati, da ogni controller connesso a due opzioni diverse. Per ulteriori informazioni, consultare le **interfacce di rete** in base ai [requisiti di disponibilità per il dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se con funzione + ricetrasmettitori le interfacce di rete GbE 10, utilizzare QSFP fornito-funzione + schede. Per ulteriori informazioni, vedere [hardware supportati 10 GbE interfacce di rete in un dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Porta seriale difettosi

Eseguire i passaggi seguenti per la porta seriale di cablaggio.

#### <a name="to-cable-for-serial-connection"></a>Cavo per connessione seriale

1. Il dispositivo ha una porta seriale su ciascun controller identificato dall'icona di chiave inglese. Per individuare le porte seriali, consultare l'illustrazione che mostra i dati porte nella parte posteriore del dispositivo.

2. Identificare il controller active sulla base del dispositivo. Un LED azzurro lampeggiante indica che il controller sia attivo.

3. Utilizzare il cavo seriale fornito, se necessario, il convertitore USB seriale per il laptop e connettere la console o il computer (con emulazione terminale al dispositivo) alla porta seriale del controller di attivo.

4. Installare i driver seriale USB (forniti con il dispositivo) nel computer in uso.

5. Configurare la connessione seriale come indicato di seguito:
   - trasmissione 115.200
   - 8 bit di dati
   - 1 bit di stop
   - Non uniformità
   - Flusso di controllo è impostata su **Nessuno**

6. Verificare che la connessione funzioni premendo INVIO sulla console. Deve essere visualizzato un menu console seriale.

> [AZURE.NOTE] **Gestione la sicurezza:** Quando il dispositivo è installato in un Data Center remoto o in una chat room computer con accesso limitato, assicurarsi che le connessioni seriali a entrambi i controller sono sempre connessi alla console seriale o dispositivo simile. In questo modo telecomando fuori banda e le operazioni di supporto in caso di problemi di rete o errori imprevisti.

È stata completata difettosi il dispositivo per power, accesso alla rete e connessione seriale. Il passaggio successivo consiste nel configurare il software in un dispositivo.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti per [distribuire e configurare il dispositivo di StorSimple locale](storsimple-deployment-walkthrough-u2.md).
