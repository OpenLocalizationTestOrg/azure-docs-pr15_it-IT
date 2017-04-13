<properties 
   pageTitle="Sostituire un controller di dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come rimuovere e sostituire uno o entrambi i moduli controller sul dispositivo StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Sostituire un modulo controller sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come rimuovere e sostituire uno o entrambi i moduli controller in un dispositivo StorSimple. Viene inoltre la logica sottostante per gli scenari di sostituzione controller singola e doppia.

>[AZURE.NOTE] Prima di eseguire una sostituzione controller, è consigliabile sempre di aggiornamento del firmware controller per la versione più recente.
>
>Per impedire danni al dispositivo StorSimple, non rimuovere il controller fino a quando non vengono visualizzati i LED come una delle operazioni seguenti:
>
>- Si illumina tutti è OFF.
>- 3 LED, ![sull'icona di segno di spunta verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![croce rossa sull'icona](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) lampeggiante, e LED 0 e 7 LED sono **via**.

Nella tabella seguente illustra gli scenari di sostituzione controller supportato.

|Maiuscole/minuscole|Scenario di sostituzione|Procedura applicabile|
|:---|:-------------------|:-------------------|
|1|Un controller di stato di errore, l'altro controller corretti e attiva.|[Sostituzione singolo controller](#replace-a-single-controller), che descrive la [logica alla base sostituzione singolo controller](#single-controller-replacement-logic), nonché la [procedura di sostituzione](#single-controller-replacement-steps).|
|2|Entrambi i controller non sono riuscita e richiedono la sostituzione. Chassis, dischi, simbolo di and.disk sono corretti.|[Sostituzione controller doppio](#replace-both-controllers), che descrive la [logica alla base sostituzione controller doppio](#dual-controller-replacement-logic), come la [procedura di sostituzione](#dual-controller-replacement-steps). |
|3|Vengono scambiate controller dal dispositivo stesso o da diversi dispositivi. Allegati disco, chassis e dischi sono integri.|Verrà visualizzato un messaggio di avviso di mancata corrispondenza intervallo aperto.|
|4|Non è presente un controller e l'altro controller non riesce.|[Sostituzione controller doppio](#replace-both-controllers), che descrive la [logica alla base sostituzione controller doppio](#dual-controller-replacement-logic), come la [procedura di sostituzione](#dual-controller-replacement-steps).|
|5|Uno o entrambi controller non è riuscita. Il dispositivo non è possibile accedere tramite la console seriale o remoti di Windows PowerShell.|[Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per una procedura di sostituzione controller manuale.|
|6|Controller di avere una versione di compilazione diverse, potrebbe essere dovuto a:<ul><li>Controller installata una versione diversa di software.</li><li>Controller installata una versione del firmware diverse.</li></ul>|Se le versioni di software controller sono diverse, la logica di sostituzione rileva che e aggiorna la versione del software sul controller di sostituzione.<br><br>Se le versioni del firmware controller sono diverse e la versione precedente di firmware **non** automaticamente aggiornabile un messaggio di avviso verrà visualizzato nel portale di classica Azure. È necessario la disponibilità di aggiornamenti e installare gli aggiornamenti firmware.</br></br>Se la versione precedente del firmware è aggiornabile automaticamente le versioni del firmware controller sono diverse, la logica di sostituzione controller rileva questa e dopo il controller viene avviato, il firmware verrà aggiornato automaticamente.|

È necessario rimuovere un modulo controller se non è riuscita. Uno o entrambi i moduli controller possono avere esito negativo, che può causare un sostitutivo controller singolo o sostituzione controller doppio. Per procedure di sostituzione e la logica alla base loro, vedere le operazioni seguenti:

- [Sostituire un unico controller](#replace-a-single-controller)
- [Sostituire entrambi i controller](#replace-both-controllers)
- [Rimuovere un controller](#remove-a-controller)
- [Inserire un controller](#insert-a-controller)
- [Identificare il controller attivo nel dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Prima di rimozione e la sostituzione di un controller, rivedere le informazioni di sicurezza in [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Sostituire un unico controller

Se uno dei due controller sul dispositivo Microsoft Azure StorSimple ha avuto esito negativo, non funziona correttamente o non è, è necessario sostituire un unico controller. 

### <a name="single-controller-replacement-logic"></a>Logica di sostituzione del controller singolo

In sostituzione singolo controller, è necessario rimuovere controller non riuscito. (I rimanenti controller nel dispositivo è l'active.) Quando si inserisce controller sostitutivo eseguite le operazioni seguenti:

1. Controller sostitutivo verrà avviata immediatamente la comunicazione con il dispositivo StorSimple.

2. Uno snapshot del disco rigido virtuale (disco rigido virtuale) per il controller attivo viene copiato sul controller di sostituzione.

3. Snapshot viene modificato in modo che quando controller sostitutivo viene avviato da questo disco rigido virtuale, verrà riconosciuto come controller di standby.

4. Dopo aver complete le modifiche, verrà avviato controller sostitutivo come controller standby.

5. Durante l'eseguono di entrambi i controller, il cluster include online.

### <a name="single-controller-replacement-steps"></a>Procedura di sostituzione controller singolo

Completare la procedura seguente se uno dei controller di dispositivo Microsoft Azure StorSimple ha esito negativo. (Altri controller deve essere attivi e in esecuzione. Se entrambi i controller di non riuscire o malfunzionante, passare alla [procedura di sostituzione controller doppio](#dual-controller-replacement-steps).)

>[AZURE.NOTE] È possibile richiedere 30-45 minuti per il controller a riavviare e recuperare completamente la procedura di sostituzione controller singola. Il tempo totale per l'intera procedura, tra cui associare i cavi è circa 2 ore.

#### <a name="to-remove-a-single-failed-controller-module"></a>Per rimuovere un modulo unico controller non riuscito

1. Nel portale di classica Azure, accedere al servizio di gestione StorSimple, fare clic sulla scheda **dispositivi** e quindi fare clic sul nome del dispositivo che si desidera eseguire il monitoraggio.

2. Passare a **manutenzione > stato Hardware**. Lo stato del Controller 0 o 1 Controller dovrebbe essere rosso, che indica un errore.

    >[AZURE.NOTE] Il non riuscito in sostituzione controller singolo è sempre un standby controller.

3. Figura 1 e nella tabella seguente consente di individuare il modulo controller non riuscito.  

    ![Base dei moduli di simbolo principale dispositivo](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1** Che quello posteriore StorSimple dispositivo

  	|Etichetta|Descrizione|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|1 PCM|
  	|3|Controller 0|
  	|4|Controller 1|

4. Rimuovere tutti i cavi di rete connessa sul controller non riuscito le porte di dati. Se si utilizza un modello 8600, rimuovere anche i cavi di SA in grado di connettersi al controller controller EBOD.

5. Seguire i passaggi descritti in [rimuovere un controller di](#remove-a-controller) rimuovere il controller non riuscito. 

6. Installare la sostituzione factory nello stesso slot da cui è stato rimosso il controller non riuscito. In questo modo viene attivata la logica di sostituzione controller singola. Per ulteriori informazioni, vedere [singolo controller sostitutivo logica](#single-controller-replacement-logic).

7. Durante la logica di sostituzione singolo controller di avanzamento in background, riconnettere i cavi. Prestare attenzione per connettere tutti i cavi allo stesso modo che sono state connesso prima la sostituzione.

8. Dopo il riavvio del controller di, verificare lo **stato del Controller** e il **Cluster stato** nel portale di classica Azure per verificare che il controller di uno stato integro e non in modalità standby.

>[AZURE.NOTE] Se si sta controllando il dispositivo tramite la console seriale, potrebbe apparire più riavvio mentre il controller è il ripristino dalla procedura di sostituzione. Quando viene visualizzato il menu console seriale, si saprà che la sostituzione è stata completata. Se non è visualizzato il menu all'interno di due ore di inizio sostituzione controller, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Sostituire entrambi i controller

Quando entrambi i controller sul dispositivo Microsoft Azure StorSimple non sono riuscita, sono malfunzionamento o mancano, è necessario sostituire entrambi i controller. 

### <a name="dual-controller-replacement-logic"></a>Logica di sostituzione controller doppio

In sostituzione controller doppio, rimuovere prima entrambi i controller non riusciti e quindi inserire sostituzioni. Quando vengono inseriti i controller di due sostituzione, si verificano le seguenti operazioni:

1. Controller sostitutivo in alloggiamento 0 controlla le operazioni seguenti:
 
   1. Utilizza versioni correnti dei software e firmware?

   2. Si tratta di una parte del cluster?

   3. Sono controller di peer e funzione raggruppate?
                            
    Se nessuna di queste condizioni sono vera, il controller consente di cercare il backup giornaliero più recente (disponibile in **nonDOMstorage** sull'unità S). Controller di copia dell'ultimo snapshot del file dal backup.

2. Controller di alloggiamento 0 utilizza snapshot per sull'immagine stessa.

3. Nel frattempo, controller di alloggiamento 1 attendere controller 0 per completare la creazione di immagini e avviare.

4. Dopo l'avvio controller 0, 1 rileva cluster creati dal controller 0, che attiva la logica di sostituzione controller singola. Per ulteriori informazioni, vedere [singolo controller sostitutivo logica](#single-controller-replacement-logic).

5. Successivamente, entrambi i controller verranno eseguito e il cluster venga recapitata in linea.

>[AZURE.IMPORTANT] In seguito sostituzione controller doppio, dopo aver configurato il dispositivo StorSimple, è essenziale eseguire un manuale di backup del dispositivo. Giornaliera backup della configurazione di dispositivi non vengono attivate finché dopo 24 ore di. Lavorare con il [Supporto tecnico clienti Microsoft](storsimple-contact-microsoft-support.md) per effettuare un manuale di backup del dispositivo.

### <a name="dual-controller-replacement-steps"></a>Procedura di sostituzione controller doppio

Questo flusso di lavoro è necessario per entrambi i controller di dispositivo Microsoft Azure StorSimple non è riuscita. Questo problema può verificarsi in un Data Center in cui il sistema di raffreddamento si interrompe e di conseguenza, entrambi i controller non all'interno di un breve periodo di tempo. A seconda che il dispositivo StorSimple è attivato o disattivato e se si utilizza un 8600 o un modello 8100, è necessario un diverso set di passaggi.

>[AZURE.IMPORTANT] Può essere necessaria 45 minuti a 1 ora per il controller a riavviare e ripristinare completamente i dati da una routine di sostituzione controller doppio. Il tempo totale per l'intera procedura, tra cui associare i cavi è di circa 2,5 ore.

#### <a name="to-replace-both-controller-modules"></a>Per sostituire entrambi i moduli controller

1. Se il dispositivo è disattivato, ignorare questo passaggio e procedere con il passaggio successivo. Se il dispositivo è attivato, disattivare il dispositivo.
                                        
    1. Se si utilizza un modello 8600, disattivare il simbolo principale e quindi disattivare il simbolo EBOD.

    2. Attendere che il dispositivo chiusa completamente. Tutti i LED nella parte posteriore il dispositivo sarà disattivato.

2. Rimuovere tutti i cavi di rete siano collegati alle porte dati. Se si utilizza un modello 8600, è necessario rimuovere anche i cavi di sa che si connettono simbolo principale per il simbolo EBOD.

3. Rimuovere entrambi i controller dal dispositivo StorSimple. Per ulteriori informazioni, vedere [rimuovere un controller](#remove-a-controller).

4. Inserire la sostituzione factory per Controller 0 prima di tutto e quindi inserire Controller 1. Per ulteriori informazioni, vedere [inserimento di un controller](#insert-a-controller). In questo modo viene attivata la logica di sostituzione controller doppio. Per ulteriori informazioni, vedere [controller doppio sostituzione logica](#dual-controller-replacement-logic).

5. Durante la logica di sostituzione controller avanza in background, riconnettere i cavi. Prestare attenzione per connettere tutti i cavi allo stesso modo che sono state connesso prima la sostituzione. Vedere le istruzioni dettagliate per il modello nella sezione del dispositivo il cavo di [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

6. Accendere il dispositivo StorSimple. Se si usa un modello 8600:

    1. Assicurarsi che sia attivato il simbolo EBOD prima.

    2. Attendere che il simbolo EBOD è in esecuzione.

    3. Attivare il simbolo principale.

    4. Dopo il primo controller riavviato e sarà funzionante, il sistema verrà eseguito.

    >[AZURE.NOTE] Se si sta controllando il dispositivo tramite la console seriale, potrebbe apparire più riavvio mentre il controller è il ripristino dalla procedura di sostituzione. Quando viene visualizzato il menu console seriale, si saprà che la sostituzione è stata completata. Se non viene visualizzato il menu all'interno di 2,5 ore dell'avvio di sostituzione controller, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Rimuovere un controller

Utilizzare la procedura seguente per rimuovere un modulo controller difettoso dal dispositivo StorSimple.

>[AZURE.NOTE] Nell'illustrazione seguente vengono è per controller 0. Per controller 1, questi da annullare.

#### <a name="to-remove-a-controller-module"></a>Per rimuovere un modulo controller

1. Afferrare fissa modulo tra la casella di scorrimento e il dito indice.

2. Fare leggermente il dito indice insieme per rilasciare fissa controller e la casella di scorrimento.

    ![Rilasciare fissa controller](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2** Rilasciare fissa controller

2. Consente di fissa come gestire una diapositiva controller fuori chassis.

    ![Scorrimento controller fuori chassis](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3** Scorrimento controller fuori chassis

## <a name="insert-a-controller"></a>Inserire un controller

Utilizzare la procedura seguente per installare un modulo fornito factory controller dopo la rimozione di un modulo difettoso dal dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Per installare un modulo controller

1. Verificare che i danni ai connettori interfaccia. Se uno qualsiasi dei pin connettore è danneggiato o piegato, non è installare il modulo.

2. Spostare il modulo controller chassis mentre la fissa è rilasciato. 

    ![Scorrimento controller chassis](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4** Scorrimento controller chassis

3. Con il modulo controller inserito, iniziare la chiusura di fissa pur continuando a push modulo controller chassis. Il fissa verrà esercitare guidare il controller nella posizione desiderata.

    ![Chiusura fissa controller](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5** Chiusura fissa controller

4. Termine quando si posiziona il fissa correttamente. LED **OK** dovrebbe ora essere su.  

    >[AZURE.NOTE] È possibile richiedere fino a 5 minuti per il controller e LED per attivare.

5. Per verificare che la sostituzione ha esito positivo, nel portale di classica Azure, passare a **dispositivi**per > **manutenzione** > **Stato Hardware**e assicurarsi che siano integri controller 0 e 1 controller (stato è verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identificare il controller attivo nel dispositivo

Esistono diverse situazioni, ad esempio la sostituzione di registrazione o controller primo dispositivo, che è necessario individuare il controller attivo in un dispositivo StorSimple. Attivo controller elabora tutte le operazioni del disco firmware e reti. Per identificare il controller attivo, è possibile utilizzare uno dei metodi seguenti:

- [Utilizzare il portale classico Azure per identificare il controller attivo](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Utilizzare Windows PowerShell per StorSimple per identificare il controller attivo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Controllare il dispositivo fisico per identificare il controller attivo](#check-the-physical-device-to-identify-the-active-controller)

Ognuna di queste procedure viene descritto di seguito.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Utilizzare il portale classico Azure per identificare il controller attivo

Nel portale di classica Azure, passare a **dispositivi** > **manutenzione**e scorrere fino alla sezione **controller** . Qui è possibile verificare quali controller è attivo.

![Identificare controller attivo nel portale classica Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Portale classica Azure con controller attivo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilizzare Windows PowerShell per StorSimple per identificare il controller attivo

Quando si accede a un dispositivo tramite la console di sequenza, viene visualizzato un messaggio di intestazione. Il messaggio di intestazione contiene informazioni di base dispositivo, ad esempio il modello, nome, versione del software installato e lo stato del controller di cui che si accede. Nella figura seguente mostra un esempio di un messaggio di intestazione:

![Messaggio di intestazione seriale](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Striscione controller con messaggio 0 come attivo

È possibile utilizzare il messaggio di intestazione per determinare se il controller a che si è connessi è attivo o passivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Controllare il dispositivo fisico per identificare il controller attivo

Per identificare controller attivo nel dispositivo, individuare il LED blu sopra la porta dati 5 nella parte posteriore della simbolo principale.

Se questo LED è a intermittenza, il controller è attivo e degli altri controller è in modalità standby. Usare nella tabella e diagramma di seguito come aiuto.

![Dispositivo principale simbolo base con dataport](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Che quello posteriore simbolo principale con dati porte e protocolli LED monitoraggio

|Etichetta|Descrizione|
|:----|:----------|
|1-6|DATI 0-5 porte di rete|
|7|LED blu|


## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).
