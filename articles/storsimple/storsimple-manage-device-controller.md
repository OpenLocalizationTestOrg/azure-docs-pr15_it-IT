<properties
   pageTitle="Gestire i controller di dispositivo StorSimple | Microsoft Azure"
   description="Informazioni su come interrompere, riavviare, arrestare o reimpostare il controller di dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Gestire i controller di dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione descrive le diverse operazioni che è possono eseguire sui controller di dispositivo StorSimple. Controller di dispositivo StorSimple sono ridondanti (peer) controller in una configurazione attiva passiva. In un determinato momento un solo controller è attivo ed elaborazione tutte le operazioni su disco e di rete. Altri controller è in modalità passiva. Se non riesce controller attivo, passivo controller diventa attivo automaticamente.

In questa esercitazione include istruzioni dettagliate per gestire i controller di dispositivo utilizzando il:

- Sezione **controller** della pagina **manutenzione** del servizio di gestione StorSimple
- Windows PowerShell per StorSimple.

È consigliabile gestire controller dispositivo tramite il servizio di gestione StorSimple. Se un'azione può essere eseguita solo con Windows PowerShell per StorSimple, l'esercitazione apporta una nota di esso.

Dopo aver letto questa esercitazione, sarà possibile per:

- Riavviare o arrestare un controller di dispositivo StorSimple
- Chiudere un dispositivo StorSimple
- Riavviare il dispositivo StorSimple predefinite


## <a name="restart-or-shut-down-a-single-controller"></a>Riavviare o arrestare un unico controller

Riavvio controller o arresto non è necessario come parte del funzionamento del sistema. Operazioni di chiusura di un controller singolo dispositivo sono comuni solo nei casi in cui un componente hardware dispositivo non riuscito richiede sostituzione. Riavviare il computer controller potrebbe essere necessario anche in situazioni in cui le prestazioni viene influenzata dall'utilizzo di memoria eccessiva o di un controller non funziona correttamente. È inoltre potrebbe essere necessario riavviare un controller dopo la sostituzione controller ha esito positivo, se si desidera abilitare e sostituito controller di test.

Il riavvio di un dispositivo non è interrompa iniziatori connessi, presupponendo che non è disponibile controller passivo. Se un controller passivo non disponibile o spento disattivarlo del tutto, quindi riavviare il controller di active potrebbe causare problemi del servizio e il tempo di inattività.

> [AZURE.IMPORTANT]

> - **Un controller di esecuzione non dovrà mai rimuovere come in questo modo, una perdita di ridondanza e un rischio maggiore del tempo di inattività.**

> - La procedura seguente si applica solo alla periferica fisica StorSimple. Per informazioni su come iniziare, interrompere o riavviare il dispositivo virtuale, vedere [lavorare con il dispositivo virtuale](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

È possibile riavviare o arrestare un controller singolo dispositivo tramite il portale classico Azure del servizio di gestione StorSimple o Windows PowerShell per StorSimple

Per gestire i controller di dispositivo dal portale di classica Azure, procedere come segue.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Per riavviare o arrestare un controller nel portale classica

1. Passare a **dispositivi > manutenzione**.

1. Passare allo **Stato di Hardware** e verificare che lo stato dei controller nel dispositivo sia **Integro**.

    ![Verificare che siano integro controller dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766017.png)

1. In fondo alla pagina **manutenzione** , fare clic su **Gestisci controller**.

    ![Gestire i controller di dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Se **Gestisci controller**non è visibile, è necessario installare gli aggiornamenti. Per ulteriori informazioni, vedere [aggiornare il dispositivo StorSimple](storsimple-update-device.md).

1. Nella finestra di dialogo **Cambia Controller impostazioni** eseguire le operazioni seguenti:
    1. Dall'elenco a discesa **Selezionare Controller** selezionare controller che si vuole gestire. Le opzioni sono Controller 0 e 1 Controller. Questi controller vengono anche identificati come attivo o passivo.

        >[AZURE.NOTE] Non è possibile gestire un controller di se è disponibile o capovolta disattivata e non verrà visualizzato nell'elenco a discesa.

    2. Dall'elenco a discesa **Selezionare l'azione** scegliere **riavviare controller** o **arrestare controller**.

        ![Riavviare StorSimple dispositivo passivo controller](./media/storsimple-manage-device-controller/IC766020.png)
    3. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-device-controller/IC740895.png).

Verrà riavviare o arrestare il controller. Nella tabella seguente vengono riepilogati i dettagli di quello che succede in base alle selezioni effettuate nella finestra di dialogo **Cambia Controller impostazioni** .  


|Selezione #|Se si sceglie di...|Questa operazione verrà eseguita.|
|---|---|---|
|1.|Riavviare il controller passivo.|Verrà creato un processo per riavviare il controller e, si riceverà una notifica dopo il processo è stato creato correttamente. Verrà avviata il riavvio di controller. È possibile monitorare il processo di riavvio accedendo **servizio > Dashboard > visualizzare registri operazioni** e il filtraggio di dai parametri specifici del servizio.|
|2.|Riavviare il controller attivo.|Verrà visualizzato l'avviso seguente: "Se si riavvia il controller attivo, il dispositivo non riuscirà sul controller passivo. Si desidera continuare?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi sarà corrispondono a quelle utilizzate per riavviare il controller passivo (vedere la selezione di 1).|
|3.|Arrestare il controller passivo.|Verrà visualizzato il messaggio seguente: "dopo avere inserito arresto, sarà necessario premere il tasto power sul controller per attivare il componente aggiuntivo. Si è certi di voler arrestare il controller?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi sarà corrispondono a quelle utilizzate per riavviare il controller passivo (vedere la selezione di 1).|
|4.|Arrestare il controller attivo.|Verrà visualizzato il messaggio seguente: "dopo avere inserito arresto, sarà necessario premere il tasto power sul controller per attivare il componente aggiuntivo. Si è certi di voler arrestare il controller?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi sarà corrispondono a quelle utilizzate per riavviare il controller passivo (vedere la selezione di 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Per riavviare o arrestare un controller di Windows PowerShell per StorSimple
Per arrestare o riavviare un unico controller sul dispositivo StorSimple dal portale di classica Azure, procedere come segue.


1. Accedere al dispositivo tramite la console seriale o una sessione telnet da un computer remoto. Connettersi a Controller 0 o 1 Controller eseguendo la procedura descritta in [PuTTY utilizzare per connettersi alla console seriale dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**.

1. Quando il messaggio di intestazione, prendere nota del controller di cui si è connessi (Controller 0 o 1 Controller) e se è attivo o il passivo controller (standby).
    - Per chiudere un singolo controller, al prompt dei comandi digitare:

        `Stop-HcsController`

        Questa operazione verrà chiuso il controller di cui si è connessi. Se si interrompe controller attivo, quindi non riuscirà sul controller passivo prima che venga chiuso.
    - Per riavviare un controller, al prompt dei comandi digitare:

        `Restart-HcsController`

        Questa operazione verrà riavviato controller che si è connessi. Se si riavvia il controller attivo, esso non riuscirà su controller passivo prima del riavvio.


## <a name="shut-down-a-storsimple-device"></a>Chiudere un dispositivo StorSimple

In questa sezione viene illustrato come arrestare parziale o un dispositivo StorSimple non riuscito da un computer remoto. Un dispositivo è disattivato dopo l'arresto entrambi i controller di dispositivo. Un arresto dispositivo viene eseguito quando il dispositivo è stato spostato fisica o esce servizio.

> [AZURE.IMPORTANT] Prima di arrestare il dispositivo, verificare l'integrità dei componenti dispositivo. Passare a **dispositivi > manutenzione > stato Hardware** e verificare che lo stato di LED di tutti i componenti sia verde. Solo un dispositivo integro avrà uno stato verde. Se il dispositivo viene chiusa verso il basso per sostituire un componente che non funziona correttamente, verrà visualizzato un'errore (rosso) o uno stato peggiore (schema giallo) per i rispettivi componenti.

#### <a name="to-shut-down-a-storsimple-device"></a>Per arrestare un dispositivo StorSimple

1. Utilizzare la procedura [riavviare o arrestare un controller](#restart-or-shut-down-a-single-controller) per identificare e arrestare il controller passivo nel dispositivo. È possibile eseguire questa operazione nel portale di classica Azure o in Windows PowerShell per StorSimple.
2. Ripetere il passaggio precedente arrestare controller attivo.
3. È ora necessario esaminare il piano posteriore del dispositivo. Dopo due controller completamente arresta, LED di stato su entrambi i controller dovrebbe lampeggiare rosso. Se è necessario disattivare completamente il dispositivo al momento, capovolgere interruttori Power e moduli raffreddamento (PCMs) in posizione disattivato. Questa operazione deve disattivare il dispositivo.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Reimpostare il dispositivo per le impostazioni predefinite

> [AZURE.IMPORTANT] Se è necessario riavviare il dispositivo per le impostazioni predefinite, contattare il supporto Microsoft. Utilizzare la procedura descritta di seguito solo in combinazione con supporto Microsoft.

Questa procedura viene descritto come ripristinare le impostazioni predefinite usando Windows PowerShell per StorSimple il dispositivo di Microsoft Azure StorSimple.
Reimpostazione di un dispositivo rimuove tutti i dati e impostazioni per l'intero cluster per impostazione predefinita.

Per ripristinare il dispositivo di Microsoft Azure StorSimple impostazioni predefinite, procedere come segue:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Per reimpostare il dispositivo impostazioni predefinite in Windows PowerShell per StorSimple

1. Accedere al dispositivo tramite la console seriale. Selezionare il messaggio di intestazione per garantire che si è connessi a controller attivo.

1. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**.

1. Al prompt dei comandi, digitare il comando seguente per reimpostare l'intero cluster, la rimozione di tutte le impostazioni di dati, metadati e controller di:

    `Reset-HcsFactoryDefault`

    Per reimpostare invece un unico controller, utilizzare il cmdlet [Reimpostare HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) con la `-scope` parametro.)

    Il sistema verrà riavviato più volte. Verrà avvertiti il ripristino è stata completata. A seconda del modello di sistema può richiedere 45-60 minuti per i dispositivi 8100 e 90 60 minuti per un 8600 completare questa procedura.

    > [AZURE.TIP]

    > - Se si usa aggiornamento 1.2 o versioni precedenti usare il `–SkipFirmwareVersionCheck` parametro per ignorare il controllo delle versioni firmware (in caso contrario verrà visualizzato un errore di mancata corrispondenza firmware: Reimposta Factory non è possibile continuare a causa di mancata corrispondenza di versioni del firmware. ).

    > - La procedura di ripristino factory può avere esito negativo per StorSimple dispositivi che eseguono aggiornamento 1 o 1.1 nel portale per enti pubblici e avere eseguito sostituzione completato controller singolo o doppio (con controller di sostituzione fornita con un software pre-aggiornamento 1). Si verifica questo evento quando la factory Reimposta immagine viene convalidata la presenza di un file di SHA1 sul controller di che non esiste per il software di 1 pre-aggiornamento. Se viene visualizzato che questo factory reimpostare l'errore, contattare il supporto Microsoft per semplificare la dei passaggi successivi. Questo problema non si presenta con controller sostitutivi forniti dal produttore con 1 aggiornamento o versioni successivo software.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Domande e risposte su gestione dei dispositivi controller

In questa sezione sono state abbiamo illustrate alcune domande frequenti relative gestione controller di dispositivo StorSimple.

**Q.** Cosa accade se entrambi i controller sul dispositivo sono corretti e capovolta in e riavviare o arrestare il controller attivo?

**RISPOSTE.** Se entrambi i controller nel dispositivo sono corretti e capovolta, verrà richiesto di confermare l'operazione. È possibile scegliere di:

- **Riavviare il controller attivo** , si riceverà una notifica che il riavvio di un controller di active causerà il dispositivo non sopra i controller passivo. Il controller verrà riavviato.

- **Chiudere un controller di active** – si riceverà una notifica che arresto un controller di active comporterà i tempi di inattività. Sarà anche necessario premere il tasto power sul dispositivo per attivare il controller.

**Q.** Cosa accade se controller passivo sul dispositivo non è disponibile o capovolta deselezionata e riavviare o arrestare il controller active?

**RISPOSTE.** Se il controller passivo nel dispositivo è disponibile o capovolta deselezionata e si sceglie di:

- **Riavviare il controller attivo** , si riceverà una notifica che proseguire con l'operazione verrà generato in un'interruzione temporanea del servizio e verrà richiesto di confermare l'operazione.

- **Arrestare un controller di active** , si riceverà una notifica che proseguire con l'operazione comporterà il tempo di inattività e sarà necessario premere il tasto power su uno o entrambi controller per attivare il dispositivo. Verrà richiesto di confermare l'operazione.

**Q.** Quando esegue il riavvio di controller o l'arresto non riesce a avanzamento?

**RISPOSTE.** Riavviare o arrestare un controller potrebbe non riuscire se:

- Un aggiornamento per dispositivi è in corso.

- Riavviare il computer controller è già in corso.

- Un arresto controller è già in corso.

**Q.** Come potete capire se un controller è stato riavviato o arrestato?

**RISPOSTE.** È possibile verificare lo stato di controller nella pagina manutenzione. Lo stato del controller indica se un controller è stato riavviato o arrestato. Inoltre, la pagina avvisi conterrà avviso se è stata riavviare o arrestare il controller. Operazioni di riavvio e arresto controller registrate anche i registri di operazione. Per ulteriori informazioni sui registri operazione, passare a [visualizzare i registri di operazione](storsimple-service-dashboard.md#view-the-operations-logs).

**Q.** Esiste un impatto ai / o in seguito controller failover?

**RISPOSTE.** Le connessioni TCP tra iniziatori e controller di active verranno ripristinate in seguito failover controller, ma verranno ripristinate quando controller passivo assume operazione. Nel corso di questa operazione può essere una pausa temporaneo (30 secondi) in attività tra iniziatori e il dispositivo.

**Q.** Come è possibile visualizzare il controller di servizio dopo che è stato chiuso e rimosso?

**RISPOSTE.** Per restituire un controller al servizio, è necessario inserire chassis come descritto in [sostituzione di un modulo controller sul dispositivo StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si riscontrano problemi con i controller di dispositivo StorSimple che non è possibile risolvere utilizzando le procedure descritte in questa esercitazione, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

- Per ulteriori informazioni sull'utilizzo del servizio di gestione StorSimple, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
