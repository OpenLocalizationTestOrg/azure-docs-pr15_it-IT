<properties
   pageTitle="Ripristinare da un backup della matrice virtuale StorSimple"
   description="Ulteriori informazioni su come ripristinare una copia di backup della matrice virtuale StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Ripristinare da un backup della matrice virtuale StorSimple

## <a name="overview"></a>Panoramica 

Questo articolo si applica alla versione di Microsoft Azure StorSimple virtuale in forma di matrice (noto anche come dispositivo virtuale locale StorSimple o periferica virtuale StorSimple) in esecuzione marzo 2016 disponibilità generale (GA) o versioni successive. In questo articolo vengono illustrati dettagliate come ripristinare da un set di backup delle azioni o i volumi la matrice virtuale StorSimple. L'articolo dettagli anche funzionamento ripristino a livello di elemento della matrice virtuale StorSimple configurato come un file server.


## <a name="restore-shares-from-a-backup-set"></a>Ripristino delle condivisioni da un set di backup


**Prima di provare a ripristinare condivisioni, assicurarsi di disporre di spazio sufficiente sul dispositivo per completare l'operazione.** Per ripristinare una copia di backup, nel [portale classica Azure](https://manage.windowsazure.com/), procedere come segue.

#### <a name="to-restore-a-share"></a>Per ripristinare una condivisione

1.  Passare alla **copia di Backup del catalogo**. Filtrare in base al dispositivo appropriato e intervallo di tempo per cercare le copie di backup. Fare clic sull'icona di controllo ![](./media/storsimple-ova-restore/image1.png) per eseguire la query.


1.  Nell'elenco dei set di backup visualizzata, fare clic su e selezionare un backup specifico. Espandere il backup per visualizzare le varie condivisioni sotto di esso. Selezionare una condivisione che si desidera ripristinare.

2.  Nella parte inferiore della pagina, fare clic su **Ripristina come nuovo**.

3.  Verrà avviata la procedura guidata **ripristinare come nuova condivisione** . Nella pagina **Specifica nome e posizione** :


    1.  Verificare il nome del dispositivo di origine. Deve trattarsi di dispositivo che contiene la condivisione da ripristinare. La selezione del dispositivo è in grigio. Per selezionare un dispositivo di origine diversi, sarà necessario uscire e selezionare di nuovo il nuovo set di backup.

    2.  Specificare un nome di condivisione. Il nome della condivisione deve contenere i caratteri da 3 a 127.

    3.  Verificare la dimensione, tipo e le autorizzazioni associate alla condivisione che si sta tentando di ripristinare. Sarà possibile modificare le proprietà di condivisione tramite Esplora risorse al termine del ripristino.

    4.  Fare clic sull'icona di controllo ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Al termine del processo di ripristino, verrà avviato il ripristino e verrà visualizzata un'altra notifica. Per controllare lo stato di avanzamento di ripristino, fare clic su **Visualizza processo**. Verrà visualizzata la pagina **dei processi** .

2.  È possibile tenere traccia dello stato di avanzamento del processo di ripristino. Quando il ripristino è completata al 100%, passare alla pagina **condivisioni** nel dispositivo.

3.  È ora possibile visualizzare la nuova condivisione ripristinata nell'elenco di azioni nel dispositivo. Si noti che ripristina viene eseguita per lo stesso tipo della condivisione. Una condivisione a più livelli viene ripristinata come a più livelli e una condivisione locale bloccata come condivisione in locale bloccata.

Ora completato la configurazione del dispositivo e appreso come backup o ripristino di una condivisione. 


## <a name="restore-volumes-from-a-backup-set"></a>Ripristinare volumi da un set di backup


Per ripristinare una copia di backup, nel portale di classica Azure, procedere come segue. L'operazione di ripristino consente di ripristinare il backup di un nuovo volume nello stesso dispositivo virtuale; non è possibile ripristinare un dispositivo diverso.

#### <a name="to-restore-a-volume"></a>Per ripristinare un volume

1.  Passare alla **copia di Backup del catalogo**. Filtrare in base al dispositivo appropriato e intervallo di tempo per cercare le copie di backup. Fare clic sull'icona di controllo ![](./media/storsimple-ova-restore/image1.png) per eseguire la query.

2.  Nell'elenco dei set di backup visualizzata, fare clic su e selezionare un backup specifico. Espandere il backup per visualizzare i vari volumi sotto di esso. Selezionare il volume da ripristinare. 

5.  Nella parte inferiore della pagina, fare clic su **Ripristina come nuovo**. Verrà avviata la procedura guidata **ripristinare come nuovo volume** .

1.  Nella pagina **Specifica nome e posizione** :


    1.  Verificare il nome del dispositivo di origine. Deve trattarsi di dispositivo che contiene il volume che si desidera ripristinare. La selezione del dispositivo non è disponibile. Per selezionare un dispositivo di origine diversi, sarà necessario uscire e selezionare di nuovo il nuovo set di backup.

    2.  Specificare un nome di volume per il volume in corso il ripristino come nuovo. Il nome del volume deve contenere i caratteri da 3 a 127.

    3.  Fare clic sull'icona freccia.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Nella pagina **host di specificare che possono utilizzare questo volume** selezionare ACRs appropriato nell'elenco a discesa.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Fare clic sull'icona di controllo ![](./media/storsimple-ova-restore/image1.png). Verrà avviata un processo di ripristino e si vedrà la seguente notifica che il processo è in corso.

2.  Al termine del processo di ripristino, verrà avviato il ripristino e verrà visualizzata un'altra notifica. Per controllare lo stato di avanzamento di ripristino, fare clic su **Visualizza processo**. Verrà visualizzata la pagina **dei processi** .

3.  È possibile tenere traccia dello stato di avanzamento del processo di ripristino. Passare alla pagina **volumi** nel dispositivo.

4.  È ora possibile visualizzare il nuovo volume ripristinato nell'elenco dei volumi nel dispositivo. Si noti che ripristina viene eseguita per lo stesso tipo di volume. Un volume a più livelli viene ripristinato come a più livelli e un volume locale bloccato viene ripristinato come un volume locale bloccato.

5.  Quando il volume viene visualizzata in linea nell'elenco di volumi, il volume è disponibile per l'uso.  Nell'host dei iniziatore iSCSI, aggiornare l'elenco dei destinatari nella finestra Proprietà iniziatore iSCSI.  Una nuova destinazione che contiene il nome del volume ripristinato dovrebbero essere visualizzate come "inattiva" nella colonna stato.

6.  Selezionare la destinazione e fare clic su **Connetti**.   Dopo l'iniziatore è connesso alla destinazione, lo stato dovrebbe essere **connesso**. 

7.  Nella finestra **Gestione disco** volumi collegati verranno visualizzato come illustrato nella figura seguente. Rapida volume rilevato scegliendo il nome del disco e quindi fare clic su **Online**.

> [AZURE.IMPORTANT] Quando si tenta di ripristinare un volume o da una condivisione di un set di backup, se il processo di ripristino non riesce, volume di destinazione o condivisione ancora potrebbe essere creati nel portale. È importante eliminare il volume di destinazione o condividere nel portale per ridurre al minimo eventuali problemi futuri derivanti da questo elemento.

## <a name="item-level-recovery-ilr"></a>Ripristino a livello di elemento (ILR)

In questa versione introduce il ripristino di livello di elemento (ILR) in un dispositivo virtuale StorSimple configurato come un file server. La caratteristica consente di eseguire granulare ripristino dei file e cartelle da un backup cloud di tutte le quote sul dispositivo StorSimple. Gli utenti possono recuperare file eliminati da un backup recenti tramite un modello di modalità self-service.

Ogni condivisione dispone di una cartella *.backups* contenente i backup più recenti. L'utente può passare al backup desiderato, copiare pertinenti file e cartelle dal backup e ripristinarli. Per evitare chiamate per gli amministratori per il ripristino di file da un backup.

1.  Quando si esegue il ILR, è possibile visualizzare i backup tramite Esplora risorse. Fare clic su Condividi specifici che si desidera esaminare il processo di backup. Verrà visualizzata una cartella *.backups* creata in Condividi contenente tutti i backup. Espandere la cartella *.backups* per visualizzare i backup. La cartella visualizzerà esplosa dell'intera gerarchia di backup. Questa visualizzazione viene creata su richiesta e in genere richiede solo alcuni secondi per creare.

    Ultimi 5 backup vengono visualizzati in questo modo e può essere utilizzato per eseguire un ripristino a livello di elemento. I 5 backup recenti includono il valore predefinito pianificato e il backup manuale.

    
    -   **Backup pianificati** nominati &lt;nome del dispositivo&gt;DailySchedule-aaaammgg-HHMMSS-UTC.

    -   **Backup manuale** denominata come Ad-hoc-aaaammgg-HHMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identificare il backup contenente la versione più recente del file eliminato. Se il nome della cartella contiene un timestamp UTC in ciascuno dei casi precedenti, l'ora in cui è stata creata la cartella è al dispositivo effettivo avvio quando il backup. Utilizzare il timestamp cartella per individuare e identificare i backup.

2.  Individuare la cartella o il file che si desidera ripristinare il backup identificato nel passaggio precedente. Nota che è possibile visualizzare solo i file o cartelle che si dispone di autorizzazioni. Se non si riesce ad accedere a determinati file o cartelle, sarà necessario contattare l'amministratore di condivisione in grado di utilizzare Esplora risorse per modificare le autorizzazioni di condivisione e consentono di accedere ai file specifici o sulla cartella. È una procedura consigliata che l'amministratore di condivisione in un gruppo di utenti al posto di un singolo utente.

3.  Copiare il file o la cartella in condivisione appropriate nel server file StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **Video disponibile**

Guardare il video per scoprire come è possibile creare condivisioni, condivisioni il backup e ripristinare i dati in una matrice virtuale StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su come [amministrare la matrice virtuale StorSimple tramite l'interfaccia web locale](storsimple-ova-web-ui-admin.md).
