<properties 
   pageTitle="Sostituire la pila in un dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come rimuovere, sostituire e mantenere il modulo di riserva sul dispositivo StorSimple."
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

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Sostituire il modulo di riserva sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

Il simbolo principale Power e raffreddamento modulo (PCM) in un dispositivo Microsoft Azure StorSimple dispone di un pacco aggiuntive. Questo pacchetto di alimentazione in modo che il dispositivo StorSimple possibile salvare i dati nel caso di perdita di alimentazione per il simbolo principale. Questo pacco viene definito il *modulo di riserva*. Modulo di riserva esiste solo per il simbolo principale del dispositivo StorSimple (simbolo EBOD non contiene un modulo di riserva). 

In questa esercitazione viene illustrato come:

- Rimuovere il modulo di riserva 
- Installare un nuovo modulo di riserva
- Mantenere il modulo di riserva

>[AZURE.IMPORTANT] Prima di rimozione e la sostituzione di un modulo di riserva, rivedere le informazioni di sicurezza in [Introduzione alla sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Rimuovere il modulo di riserva

Il modulo di riserva del dispositivo StorSimple è un campo FRU. Prima di installare in PCM, è necessario memorizzato il modulo batteria nell'imballaggio originale. Per rimuovere la riserva, procedere come segue.

#### <a name="to-remove-the-backup-battery-module"></a>Per rimuovere il modulo di riserva

1. Nel portale di classica Azure, passare a **dispositivi**per > **manutenzione** > **Stato Hardware**. Nella casella **Componenti condivisi**, esaminare lo stato della pila.

2. Identificare PCM in cui non è riuscita la pila. Figura 1 mostra posteriore del dispositivo StorSimple.

    ![Base del dispositivo principale simbolo moduli](./media/storsimple-battery-replacement/IC740994.png)

    **Figura 1** Nuovo del dispositivo principale con moduli PCM e controller

  	|Etichetta|Descrizione|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|1 PCM|
  	|3|Controller 0|
  	|4|Controller 1|

    Come mostrato dal numero 3 nella figura 2, l'indicatore monitoraggio LED su PCM 0 corrisponde a **Batteria guasto** deve acceso.

    ![Base di LED PCM al dispositivo monitoraggio](./media/storsimple-battery-replacement/IC740992.png)

    **Figura 2** Che quello posteriore PCM con LED monitoraggio

  	|Etichetta|Descrizione|
  	|:---|:-----------|
  	|1|Alimentazione AC|
  	|2|Errore ventola|
  	|3|Guasto batteria|
  	|4|OK PCM|
  	|5|Alimentazione controller di dominio|
  	|6|Batteria integro|

3. Per rimuovere PCM con una batteria non riuscita, seguire la procedura descritta in [rimuovere un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Con PCM rimosso, sollevare e ruotare l'handle del modulo batteria verso l'alto, come mostrato nella figura riportata di seguito e recuperare fino a rimuovere la pila.

    ![Rimozione batteria dal PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figura 3** Rimuovere la pila PCM

5. Posizionare il modulo nell'unità di campo sostituibili imballaggio.

6. Fornire l'unità difettoso a Microsoft per la gestione e manutenzione corretta.

## <a name="install-a-new-backup-battery-module"></a>Installare un nuovo modulo di riserva

Eseguire la procedura seguente per installare il modulo batteria di sostituzione in PCM in simbolo principale del dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Per installare il modulo batteria

1. Posizionare il modulo di riserva l'orientamento corretto in PCM.

2. Premere verso il basso il quadratino di modulo batteria tutto per alloggiare il connettore.

3. Sostituire PCM in simbolo principale seguendo le indicazioni presenti [sostituire un Power e raffreddamento modulo in un dispositivo StorSimple](storsimple-power-cooling-module-replacement.md).

4. Una volta completata la sostituzione, passare a **dispositivi**per > **manutenzione** > **Stato Hardware** nel portale di classica Azure. Verificare lo stato della pila assicurarsi che l'installazione è stata stabilita. Stato di colore verde indica che la pila sia integro.

## <a name="maintain-the-backup-battery-module"></a>Mantenere il modulo di riserva

Nel dispositivo StorSimple il modulo di riserva fornisce power al controller di durante un evento di perdita di alimentazione. Consente il dispositivo StorSimple per salvare i dati critici prima di arresto in modo controllato. Con due AA completamente carica nel PCMs, il sistema possibile gestire questi due eventi perdita consecutivi.

Nel portale di classica Azure, lo **Stato di Hardware** nella pagina **manutenzione** indica se la pila non funziona o si avvicina il ciclo di vita. Stato della batteria è indicato **in PCM 0** o **batteria in 1 PCM** in **Componenti condivisi**meno al. Questa pagina contiene uno stato **danneggiato** per raggiungere fine del ciclo di vita e raggiunto **non riuscito** di fine del ciclo di vita. 

>[AZURE.NOTE] La pila segnalare **non riuscita** quando è necessario semplicemente addebitati.
 
Se viene visualizzato lo stato **danneggiato** , è consigliabile la seguente procedura da seguire:

- Il sistema che si sia verificata una perdita di power recenti o le pile possono essere soggetto a manutenzione periodica. Osservare il sistema per 12 ore prima di continuare.

    - Se lo stato è ancora **danneggiato** dopo 12 ore di connessione continua l'alimentazione con i controller e PCMs in esecuzione, la pila deve essere sostituito. Informazioni per un modulo di riserva sostitutivo, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) .

    - Se lo stato diventa OK dopo 12 ore, la pila è operativa e necessarie solo un costi di manutenzione.

- Se non è stata una perdita associata di alimentazione e PCM sia attivato e connesso al alimentazione, la pila deve essere sostituito. [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per ordinare un modulo di riserva di sostituzione.

>[AZURE.IMPORTANT] Eliminare la pila non riuscita in base alle normative nazionali e internazionali. 

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).
