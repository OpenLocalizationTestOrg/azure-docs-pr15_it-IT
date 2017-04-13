<properties 
   pageTitle="Sostituire un PCM sul dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come rimuovere e sostituire la potenza e raffreddamento modulo (PCM) in un dispositivo StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Sostituire una potenza e raffreddamento modulo in un dispositivo StorSimple

## <a name="overview"></a>Panoramica

La potenza e raffreddamento modulo (PCM) in un dispositivo Microsoft Azure StorSimple è costituito da un gruppo di continuità e raffreddamento ventole vengono controllate mediante i principali e allegati EBOD. Esiste solo un modello di PCM certificata per ogni simbolo. Simbolo principale è certificato per un 764 PCM W e il simbolo EBOD è certificato per un 580 PCM W. Sebbene PCMs per il simbolo principale e il simbolo EBOD sono diversi, la procedura di sostituzione è identica.

In questa esercitazione viene illustrato come:

- Rimuovere un PCM
- Installare sostituzione PCM

>[AZURE.IMPORTANT] Prima di rimozione e la sostituzione di un PCM, rivedere le informazioni di sicurezza in [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Prima di sostituire un PCM

Prima di sostituire il PCM, tenere presente gli aspetti importanti seguenti:

- Se l'alimentazione di PCM non riesce, lasciare installato il modulo difettoso, ma rimuovere il cavo di alimentazione. La ventola continueranno a ricevere il simbolo di power e continuare a fornire raffreddamento appropriato. Se la ventola non riesce, PCM deve essere sostituito immediatamente.

- Prima di rimuovere la PCM, scollegare l'alimentazione da PCM disattivando il principale (se presente) o la rimozione fisica il cavo di alimentazione. In questo modo un avviso per il sistema che la chiusura di alimentazione sia imminente.

- Assicurarsi che gli altri PCM sia funzionale "Continua" funzionamento del sistema prima di sostituire PCM difettoso. Un PCM difettoso deve essere sostituito da un PCM pienamente operativo più presto possibile.

- Sostituzione di modulo PCM richiede solo pochi minuti per completare, ma devono essere completata entro 10 minuti di rimozione PCM non riuscito per impedire il surriscaldamento.

- Si noti che i moduli di PCM W 764 sostituzione fabbrica non contengono il modulo di riserva. È necessario rimuovere la pila il PCM difettoso e quindi inserirla nel modulo di sostituzione prima di eseguire la sostituzione. Per ulteriori informazioni, vedere come [rimuovere e inserire un modulo di riserva](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Rimuovere un PCM

Seguire queste istruzioni quando si è pronti rimuovere un Power e raffreddamento modulo (PCM) dal dispositivo Microsoft Azure StorSimple.

>[AZURE.NOTE] Prima di rimuovere il PCM, verificare di avere una sostituzione corretta (764 W per il simbolo principale) o 580 W per il simbolo EBOD.

#### <a name="to-remove-a-pcm"></a>Per rimuovere un PCM

1. Nel portale di classica Azure, fare clic su **dispositivi** > **manutenzione** > **Stato Hardware**. Controllare lo stato dei componenti PCM in **Componenti condivisi** per identificare i cui ha avuto esito negativo PCM:

     - Se un gruppo di continuità in PCM 0 non è riuscita, lo stato di **Alimentazione in PCM 0** sarà in rosso.

     - Se un alimentatore PCM 1 non è riuscita, lo stato di **Alimentazione PCM 1** sarà in rosso.

     - Se non è riuscita ventola PCM 1, lo stato di **raffreddamento 0 per PCM 0** o **1 raffreddamento per PCM 0** sarà in rosso.

2. Individuare PCM non riuscito nella parte posteriore della simbolo principale. Se si esegue un modello 8600, identificare il simbolo principale esaminando il numero di identificazione di unità di sistema visualizzate sul pannello anteriore display LED. Il valore predefinito che ID unità visualizzata sul simbolo principale è **00**, mentre il valore predefinito che ID unità visualizzata sul simbolo EBOD è **01**. Nella tabella e diagramma di seguito illustrano il pannello anteriore dello schermo LED.

    ![ID di sistema nel Pannello di operazioni anteriore](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figura 1** Pannello anteriore del dispositivo  

  	|Etichetta|Descrizione|
  	|:---|:-----------|
  	|1|Pulsante Disattiva|
  	|2|Alimentazione del sistema|
  	|3|Errore di modulo|
  	|4|Errore logico|
  	|5|Visualizzazione di ID unità|

3. Il monitoraggio LED nella parte posteriore simbolo principale può anche essere utilizzato per identificare PCM difettoso. Vedere le seguenti diagramma tabella per imparare a usare i LED per individuare PCM difettoso. Ad esempio, se è acceso LED corrispondente **Ventola esito negativo** , la ventola non è riuscito. Analogamente, se è acceso LED corrispondente a **AC Fail** , l'alimentazione non è riuscita. 

    ![Base del dispositivo PCM monitoraggio LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figura 2** Che quello posteriore PCM con indicatori LED

  	|Etichetta|Descrizione|
  	|:---|:-----------|
  	|1|Alimentazione AC|
  	|2|Errore ventola|
  	|3|Guasto batteria|
  	|4|OK PCM|
  	|5|Alimentazione controller di dominio|
  	|6|Batteria integro|

4. Fare riferimento a nel diagramma seguente del retro di dispositivo StorSimple di individuare il modulo PCM non riuscito. PCM 0 è sul lato sinistro e 1 PCM è posizionato a destra. La tabella che segue vengono illustrati i moduli.

     ![Base dei moduli di simbolo principale dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figura 3** Che quello posteriore dispositivo con moduli plug-in 

  	|Etichetta|Descrizione|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|1 PCM|
  	|3|Controller 0|
  	|4|Controller 1|

5. Disattivare PCM difettoso e disconnettere il cavo di alimentazione alimentazione. È ora possibile rimuovere il PCM.

6. Afferrare il fissa e il lato del quadratino di PCM tra la casella di scorrimento e il dito indice e fare in modo da aprire il quadratino.

    ![Quadratino di apertura PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figura 4** Aprire il quadratino di PCM

7. Afferrano il punto e rimuovere il PCM.

    ![Rimozione di dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figura 5** Rimozione di PCM

## <a name="install-a-replacement-pcm"></a>Installare sostituzione PCM

Seguire queste istruzioni per installare un PCM nel dispositivo StorSimple. Assicurarsi di avere inserito il modulo di riserva prima di installare la sostituzione PCM (si applica a 764 solo PCMs W). Per ulteriori informazioni, vedere come [rimuovere e inserire un modulo di riserva](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Per installare un PCM

1. Verificare di avere la sostituzione PCM corretta per il simbolo. Simbolo primaria deve un 764 PCM W e il simbolo EBOD deve un 580 PCM W. Non tentare di utilizzare il PCM W 580 in simbolo principale o PCM W 764 in simbolo EBOD. Nella figura seguente mostra la posizione in cui identificare le informazioni sull'etichetta apposta su PCM.

    ![Dispositivo PCM etichetta](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figura 6** Etichetta PCM

2. Verifica dell'integrità per il simbolo, prestando particolare attenzione ai connettori. 
                                        
    >[AZURE.NOTE] **Non installare il modulo se sono piegato qualsiasi pin dei connettori.**

3. Con il quadratino di PCM in posizione aperta, spostare il modulo il simbolo.

    ![L'installazione di dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figura 7** L'installazione di PCM

4. Chiudere manualmente il quadratino di PCM. Si deve ascoltare un clic come fissa quadratino coinvolge. 
                                        
    >[AZURE.NOTE] Per assicurarsi che il pin dei connettori hanno viva l'attenzione, è possibile leggermente tug il punto senza rilasciare il fissa. Se il PCM estratta, significa che è stato chiuso il fissa prima che i connettori viva l'attenzione.

5. Collegare i cavi di alimentazione per la fonte di alimentazione e PCM.

6. Proteggere l'affaticamento visivo Balle rilievo. 

7. Attivare il PCM.

8. Verificare che la sostituzione è stata stabilita: nel portale classico Azure del servizio StorSimple Manager, passare a **dispositivi** > **manutenzione** > **Stato Hardware**. Nella casella **Componenti condivisi**, lo stato di PCM dovrebbe essere verde. 
                                        
    >[AZURE.NOTE] Possono richiedere alcuni minuti per la sostituzione PCM inizializzare completamente.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).
