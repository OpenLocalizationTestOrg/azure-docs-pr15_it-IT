<properties 
   pageTitle="Attivare o disattivare le dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come attivare un nuovo dispositivo StorSimple, attivare un dispositivo che è stato chiuso o alimentazione e disattivare un dispositivo in esecuzione."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Attivare o disattivare le dispositivo StorSimple 

## <a name="overview"></a>Panoramica

Chiusura di un dispositivo Microsoft Azure StorSimple non sono necessaria come parte del funzionamento del sistema. Tuttavia, potrebbe essere necessario attivare un nuovo dispositivo o un dispositivo che deve essere chiuso. In genere, è necessario un arresto nei casi in cui è necessario sostituire hardware non riuscito, fisica spostare un'unità o scrivere un dispositivo fuori servizio. In questa esercitazione viene procedura necessarie per attivare e arrestare il dispositivo StorSimple in scenari diversi.

Nella tabella seguente elenca diversi scenari per l'attivazione e arrestare il dispositivo StorSimple e vengono forniti collegamenti alle procedure appropriate.

|Scenario:|Argomenti della Guida di riferimento|
|:-------|:---------------|
|Attivare un nuovo dispositivo|[Attivare un nuovo dispositivo](#turn-on-a-new-device)<ul><li>[Nuovi dispositivi con solo simbolo principale](#new-device-with-primary-enclosure-only)</li><li>[Nuovi dispositivi con simbolo EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Attivare un dispositivo dopo la chiusura|[Attivare un dispositivo dopo la chiusura](#turn-on-a-device-after-shutdown)<ul><li>[Dispositivo con solo simbolo principale](#device-with-primary-enclosure-only)</li><li>[Dispositivo con il simbolo EBOD](#device-with-ebod-enclosure)</li></ul>|
|Attivare un dispositivo dopo un'interruzione|[Attivare un dispositivo dopo un'interruzione](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo con solo simbolo principale](#8100)</li><li>[Dispositivo con il simbolo EBOD](#8600)</li></ul>|
|Accendere un dispositivo dopo il simbolo principale ed EBOD connessione è stata persa|[Accendere un dispositivo dopo primario ed EBOD simbolo connessione viene persa](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Chiudere un dispositivo in esecuzione|[Disattivare un dispositivo in esecuzione](#turn-off-a-running-device)<ul><li>[Dispositivo con solo simbolo principale](#8100a)</li><li>[Dispositivo con il simbolo EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Attivare un nuovo dispositivo

La procedura per l'attivazione di un dispositivo StorSimple per la prima volta varia a seconda che il dispositivo sia un 8100 o un modello 8600. Il modello 8100 ha un singolo simbolo principale, mentre il 8600 è un dispositivo con due simbolo con un simbolo principale e un simbolo EBOD. Procedura dettagliata per entrambi i modelli sono disponibili nelle sezioni seguenti.

- [Nuovi dispositivi con solo simbolo principale](#new-device-with-primary-enclosure-only)

- [Nuovi dispositivi con simbolo EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nuovi dispositivi con solo simbolo principale

Il modello 8100 StorSimple è un dispositivo simbolo singola. Il dispositivo include ridondanti Power e moduli raffreddamento (PCMs). Sia PCMs deve essere installata e connessa a origini diverse power per assicurare la disponibilità elevata.

Eseguire la procedura seguente per il dispositivo per la potenza di cablaggio.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Per la configurazione di dispositivi completo e difettosi istruzioni, passare a [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Assicurarsi di seguire le istruzioni esattamente.

### <a name="new-device-with-ebod-enclosure"></a>Nuovi dispositivi con simbolo EBOD

Il modello StorSimple 8600 dispone di un simbolo primario e un simbolo EBOD. È necessario essere cavi insieme per power e connettività seriale collegato SCSI (SA) le unità di misura.

Quando si configura il dispositivo per la prima volta, eseguire i passaggi per SA difettosi prima di tutto e quindi completare i passaggi di power cablaggio.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Per la configurazione di dispositivi completo e difettosi istruzioni, passare a [installare il dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md). Assicurarsi di seguire le istruzioni esattamente.

## <a name="turn-on-a-device-after-shutdown"></a>Attivare un dispositivo dopo la chiusura

La procedura per l'attivazione di un dispositivo StorSimple dopo che è stato chiuso è diversa a seconda che il dispositivo sia un 8100 o un modello 8600. Il modello 8100 ha un singolo simbolo principale, mentre il 8600 è un dispositivo con due simbolo con un simbolo principale e un simbolo EBOD.

- [Dispositivo con solo simbolo principale](#device-with-primary-enclosure-only)

- [Dispositivo con il simbolo EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo con solo simbolo principale

Dopo la chiusura, utilizzare la procedura seguente per attivare un dispositivo StorSimple con un simbolo principale e non simbolo EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Per attivare un dispositivo con solo un simbolo principale

1. Assicurarsi che la potenza passa entrambi elettrica e moduli raffreddamento (PCMs) sono in posizione disattivato. Se le opzioni non sono in posizione disattivato, capovolgere le in posizione disattivato e attendere che gli indicatori di disattivazione.

2. Accendere il dispositivo capovolgendolo parametri power in entrambi PCMs sulla posizione. Il dispositivo deve attivare.

3. Controllare le operazioni seguenti per verificare che il dispositivo sia completamente attivata:

    1. LED OK su entrambi i moduli PCM sono verde.

    2. LED di stato su entrambi i controller sono verde a tinta unita.

    3. È lampeggiare LED blu su uno dei controller, che indica che il controller sia attivo.

    Se nessuna di queste condizioni non vengono soddisfatti, il dispositivo non è integro. Informazioni [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo con il simbolo EBOD

Dopo la chiusura, utilizzare la procedura seguente per attivare un dispositivo StorSimple con un simbolo primario e un simbolo EBOD. Eseguire ogni passaggio esattamente come descritto in sequenza. In caso contrario potrebbe causare la perdita di dati.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Per attivare un dispositivo con un primario e un simbolo EBOD

1. Assicurarsi che il simbolo EBOD sia connesso a simbolo principale. Per ulteriori informazioni, vedere [installare il dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

2. Assicurarsi che la potenza e raffreddamento moduli (PCMs) EBOD e allegati primarie siano in posizione disattivato. Se le opzioni non sono in posizione disattivato, capovolgere le in posizione disattivato e attendere che gli indicatori di disattivazione.

3. Attivare il simbolo EBOD prima capovolgendolo parametri power in entrambi PCMs sulla posizione. LED PCM dovrebbe essere verde. Un controller di EBOD LED verde su questo apparecchio indica che il simbolo EBOD sia attivata.

4. Attivare il simbolo principale capovolgendolo parametri power in entrambi PCMs sulla posizione. L'intero sistema dovrebbe ora essere su.

5. Verificare che i LED SA verdi, che assicura che la connessione tra il simbolo EBOD e simbolo principale è consigliabile.

## <a name="turn-on-a-device-after-a-power-loss"></a>Attivare un dispositivo dopo un'interruzione

Un'interruzione dell'alimentazione o interruzione possibile chiudere un dispositivo StorSimple. Energia elettrica può verificarsi in uno dei forniture power o entrambi alimentazione. La procedura di ripristino è diversa a seconda che il dispositivo è un modello 8100 o un 8600. Il modello 8100 ha un singolo simbolo principale, mentre il 8600 è un dispositivo con due simbolo con un simbolo principale e un simbolo EBOD. In questa sezione descrive la procedura di ripristino per ogni scenario.

- [Dispositivo con solo simbolo principale](#8100)

- [Dispositivo con il simbolo EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo con solo simbolo principale<a name="8100">

Il sistema è possibile continuare il normale funzionamento nel caso di perdita power a uno dei relativi alimentazione. Tuttavia, per garantire disponibilità del dispositivo, ripristinare power alimentazione più presto possibile.

Se c'è un'interruzione dell'alimentazione o interruzione in entrambi alimentazione, il sistema verrà chiuso in modo ordinato e controllato. Quando l'alimentazione viene ripristinata, il sistema verrà attivata automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo con il simbolo EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Fornire un'interruzione uno power

Il sistema è possibile continuare il normale funzionamento nel caso di perdita power a uno dei relativi alimentazione simbolo principale o il simbolo EBOD. Tuttavia, per garantire disponibilità del dispositivo, ripristinare power all'alimentatore più presto possibile.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Un'interruzione entrambi alimentazione principale e allegati EBOD

Se c'è un'interruzione dell'alimentazione o interruzione in entrambi alimentazione, simbolo EBOD verrà chiuso immediatamente e simbolo principale verrà chiuso in modo ordinato e controllato. Quando viene ripristinata power, verrà avviato automaticamente il dispositivo.

Se la potenza viene disattivata manualmente, quindi eseguire le operazioni seguenti per ripristinare il sistema di power.

1. Attivare il simbolo EBOD.

2. Dopo il simbolo EBOD è attivato, attivare il simbolo principale.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Un'interruzione entrambi alimentazione sul simbolo EBOD

Quando si configura i cavi, è necessario assicurarsi che la EBOD mai è connesso solo a una PDU separata. Se la EBOD e simbolo principale non contemporaneamente, verrà ripristinato il sistema.

Se solo il simbolo EBOD ha esito negativo su entrambi alimentazione, il sistema non verrà ripristinato automaticamente. Per attivare il sistema e ripristinare integro, procedere come segue:

1. Se il simbolo principale è attivato, disattivare Power e moduli raffreddamento (PCMs).

2. Attendere alcuni minuti per arrestare il sistema.

3. Attivare il simbolo EBOD.

4. Dopo il simbolo EBOD è attivato, attivare il simbolo principale.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Accendere un dispositivo dopo primario ed EBOD simbolo connessione viene persa

Se la connessione viene persa tra controller standby e il corrispondente EBOD, il dispositivo continua a funzionare. Se la connessione tra il controller active sistema e il corrispondente EBOD viene persa, failover dovrebbe essere eseguita e il dispositivo deve continuare a lavorare nel modo consueto.

Quando vengono rimossi sia cavi seriale collegato SCSI (SA) o è interrotta la connessione tra il simbolo EBOD e simbolo primaria, il dispositivo si arresta. A questo punto, procedere come segue.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Per attivare il dispositivo dopo la connessione viene persa

1. Accesso posteriore del dispositivo.

2. Se la connessione di cavo SA tra il simbolo EBOD e simbolo principale viene interrotta, tutti corsia di SA LED sul simbolo EBOD sarà disattivato.

3. Chiudere Power e moduli raffreddamento (PCMs) nel simbolo EBOD e simbolo principale.

4. Attendere che tutti gli indicatori nella parte posteriore di entrambe le allegati disattivare.

5. Reinserire i cavi di SA e verificare che sia presente una buona connessione tra il simbolo EBOD e simbolo principale.

6. Attivare il simbolo EBOD prima capovolgendolo entrambe le opzioni PCM sulla posizione.

7. Assicurarsi che il simbolo EBOD su controllando che il LED verde sia attivato.

8. Attivare il simbolo principale.

9. Assicurarsi che il simbolo principale in controllando che LED controller verde è attivato.

10. Verificare che la connessione simbolo EBOD simbolo principale è consigliabile controllando che la corsia SA LED (quattro per controller EBOD) siano via tutti.

>[AZURE.IMPORTANT] Se i cavi di SA sono difettosi o la connessione tra il simbolo EBOD e simbolo principale è il risultato non è, quando si attiva il sistema, verrà visualizzata in modalità di ripristino. Informazioni [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) se si verifica questo evento.

## <a name="turn-off-a-running-device"></a>Disattivare un dispositivo in esecuzione

Un dispositivo StorSimple in esecuzione potrebbe essere necessario arrestare se è stato spostato, sospensione del servizio, o che dispone di un componente malfunzionante che deve essere sostituito. La procedura è diversa a seconda che il dispositivo StorSimple sia un 8100 o un modello 8600. Il modello 8100 ha un singolo simbolo principale, mentre il 8600 è un dispositivo con due simbolo con un simbolo principale e un simbolo EBOD. In questa sezione vengono descritti i passaggi per chiudere un dispositivo in esecuzione.

- [Dispositivo con il simbolo principale](#8100a)

- [Dispositivo con il simbolo EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo con il simbolo principale<a name="8100a"> 

Per arrestare il dispositivo in modo ordinato e controllato, è possibile farlo tramite il portale classico Azure o tramite Windows PowerShell per StorSimple. 

>[AZURE.IMPORTANT] Non arrestare un dispositivo in esecuzione tramite il pulsante di alimentazione sul retro del dispositivo.
>
>Prima di arrestare il dispositivo, assicurarsi che tutti i componenti di dispositivo siano corretti. Nel portale di classica Azure, passare a **dispositivi** > **manutenzione** > **Stato Hardware**e verificare che lo stato di tutti i componenti sia verde. Questo è valido solo per un sistema integro. Se il sistema viene chiusa verso il basso per sostituire un componente che non funziona correttamente, verrà visualizzato un'errore (rosso) o ridotto ai rispettivi componenti **Hardware stato**campo status (schema giallo).

Dopo l'accesso di Windows PowerShell per StorSimple o il portale classico Azure, seguire i passaggi [arrestare un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo con il simbolo EBOD<a name="8600a">

>[AZURE.IMPORTANT] Prima di arrestare il simbolo principale e il simbolo EBOD, assicurarsi che tutti i componenti di dispositivo siano corretti. Nel portale di classica Azure, passare a **dispositivi** > **manutenzione** > **Stato Hardware**e verificare che tutti i componenti siano corretti.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Per arrestare un dispositivo in esecuzione con simbolo EBOD

1. Eseguire tutti i passaggi elencati in [arrestare un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) per il simbolo principale.

2. Dopo il simbolo principale viene chiuso, arrestare il EBOD capovolgendolo disattivare i parametri di alimentazione e raffreddamento modulo (PCM).

3. Per verificare che il EBOD è stato chiuso, verificare che tutti indicatori sulla parte posteriore della simbolo EBOD siano disattivate.

>[AZURE.NOTE] I cavi di sa che vengono utilizzati per connettere il simbolo EBOD simbolo principale non devono essere rimosso fino a dopo il sistema di chiusura.

## <a name="next-steps"></a>Passaggi successivi

[Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) se si verificano problemi durante l'attivazione o arrestare un dispositivo StorSimple.

