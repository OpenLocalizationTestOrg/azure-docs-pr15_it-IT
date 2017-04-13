<properties 
   pageTitle="Sostituire un controller di StorSimple EBOD | Microsoft Azure"
   description="In questo articolo viene spiegato come rimuovere e sostituire uno o entrambi controller EBOD in un dispositivo 8600 StorSimple."
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Sostituire un controller EBOD nel dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come sostituire un modulo di controller EBOD difettoso nel dispositivo Microsoft Azure StorSimple. Per sostituire un modulo di controller EBOD, è necessario:

- Rimuovere il controller EBOD difettoso
- Installare un nuovo controller EBOD

Prima di iniziare, considerare le seguenti informazioni:

- Moduli EBOD vuoti devono essere inseriti in tutte le bande orarie inutilizzati. L'allegato non verrà raffreddare correttamente se spazio viene lasciata aperta.

- Controller EBOD è a caldo e possono essere rimossi o sostituiti. Rimuovere un modulo non riuscito finché non si dispone di sostituzione. Quando si avvia il processo di sostituzione, è necessario terminare all'interno di 10 minuti.

>[AZURE.IMPORTANT] Prima di rimuovere o sostituire qualsiasi componente StorSimple, verificare che le [convenzioni sull'icona di sicurezza](storsimple-safety.md#safety-icon-conventions) e altre [precauzioni di sicurezza](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Rimuovere un controller di EBOD

Prima di sostituire il modulo controller EBOD non riuscito nel dispositivo StorSimple, assicurarsi che l'altro modulo controller EBOD sia attivo e in esecuzione. La procedura e nella tabella seguente viene illustrato come rimuovere il modulo controller EBOD.

#### <a name="to-remove-an-ebod-module"></a>Per rimuovere un modulo EBOD

1. Aprire il portale classico Azure.

2. Passare a **dispositivi** > **manutenzione** > **Stato Hardware**e verificare che lo stato del LED del modulo di controller EBOD active verde e il LED del modulo di controller EBOD non riuscito rosso.

3. Individuare il modulo controller EBOD non riuscito in background il dispositivo.

4. Rimuovere i cavi che collegano il modulo controller EBOD al controller prima di eseguire il modulo EBOD dal sistema.

5. Prendere nota della porta SA esatta del modulo controller EBOD connessa al controller. Sarà necessario ripristinare il sistema di questa configurazione dopo aver si sostituisce il modulo EBOD. 

    >[AZURE.NOTE] In genere, si sarà A porte, che viene segnalato come **Host** nel diagramma seguente.

    ![Controller di base di EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Che quello posteriore modulo EBOD

  	|Etichetta|Descrizione|
  	|:----|:----------|
  	|1|Errore LED|
  	|2|LED alimentazione|
  	|3|Connettori SA|
  	|4|LED SA|
  	|5|Porte seriali factory solo per uso|
  	|6|Porta (Host in)|
  	|7|Porta B (Host fuori)|
  	|8|Porta C (solo per uso Factory)|

## <a name="install-a-new-ebod-controller"></a>Installare un nuovo controller EBOD

La procedura e nella tabella seguente viene illustrato come installare un modulo di controller EBOD nel dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Per installare un controller EBOD

1. Controllare il dispositivo EBOD dei danni, in particolare al connettore interfaccia. Non installare il nuovo EBOD controller se sono piegato qualsiasi PIN.

2. Con i dispositivi di blocco nella posizione aperta, spostare il modulo l'allegato fino a esercitare i dispositivi di blocco.

    ![L'installazione di controller EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2**  Installare il modulo controller EBOD

3. Chiudere la fissa. Si deve ascoltare un clic come coinvolge la fissa.

    ![Rilasciare fissa EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3**  Chiusura fissa di modulo EBOD

4. Riconnettere i cavi. Usare la configurazione esatta che era presente prima la sostituzione. Vedere le seguenti diagramma per informazioni dettagliate su come collegare i cavi di tabella.

    ![Cavo dispositivo 4U per power](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4**. Riconnessione cavi

  	|Etichetta|Descrizione|
  	|:----|:----------|
  	|1|Simbolo principale|
  	|2|PCM 0|
  	|3|1 PCM|
  	|4|Controller 0|
  	|5|Controller 1|
  	|6|Controller EBOD 0|
  	|7|Controller EBOD 1|
  	|8|Simbolo EBOD|
  	|9|Unità di distribuzione di Power|

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).
