<properties 
   pageTitle="Gestire i dispositivi con StorSimple Snapshot Manager | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare snap-in MMC Gestione Snapshot StorSimple a connettersi e gestire i dispositivi StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Gestione delle istantanea StorSimple consente di connettersi e gestire i dispositivi StorSimple

## <a name="overview"></a>Panoramica

È possibile utilizzare i nodi nel riquadro di StorSimple Snapshot gestione **dell'ambito** per verificare i dati importati dispositivo StorSimple e aggiornare i dispositivi di memorizzazione connessi. Inoltre, quando si fa clic su nodo **dispositivi** , è possibile visualizzare un elenco dei dispositivi collegati e informazioni sullo stato corrispondente nel riquadro **risultati** .

![Dispositivi collegati](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: StorSimple Snapshot Gestione dispositivi** 

A seconda delle selezioni, **visualizzare** il riquadro **risultati** Mostra le seguenti informazioni su ciascun dispositivo. (Per ulteriori informazioni sulla configurazione di una visualizzazione, andare [dal menu Visualizza](storsimple-use-snapshot-manager.md#view-menu).


| Colonna dei risultati  |Descrizione          |
|:----------------|:--------------------| 
| Nome            | Il nome del dispositivo come configurato nel portale di classica Azure|
| Modello           | Il numero di modello del dispositivo|
| Versione         | La versione del software installato nel dispositivo |
| Stato          | Se il dispositivo è disponibile |
| Ultima sincronizzazione     | Data e l'ora dell'ultima sincronizzazione il dispositivo |
| Numero seriale      | Il numero di serie per il dispositivo |
 
Se si rapida nodo **dispositivi** nel riquadro di **ambito** , è possibile scegliere le azioni seguenti:

- Aggiunta o sostituzione di un dispositivo 
- Collegare un dispositivo e verificare che le importazioni 
- Aggiornare i dispositivi connessi 

Se si sceglie il nodo **dispositivi** e fare clic su un nome di dispositivo nel riquadro **dei risultati** , è possibile scegliere le azioni seguenti:

- Eseguire l'autenticazione di un dispositivo 
- Visualizzare i dettagli dispositivo 
- Aggiornare un dispositivo 
- Eliminare una configurazione di dispositivi 
- Modificare una password per il dispositivo

>[AZURE.NOTE] Tutte le azioni eseguite sono disponibili anche nel riquadro **Azioni** .
 
In questa esercitazione viene illustrato come utilizzare StorSimple Snapshot Manager a connettersi e gestire i dispositivi e le seguenti operazioni:

- Aggiunta o sostituzione di un dispositivo 
- Collegare un dispositivo e verificare che le importazioni 
- Aggiornare i dispositivi connessi 
- Eseguire l'autenticazione di un dispositivo 
- Visualizzare i dettagli dispositivo 
- Aggiornare un singolo dispositivo 
- Eliminare una configurazione di dispositivi 
- Modificare la password di un dispositivo scaduto
- Sostituire un dispositivo non riuscito

>[AZURE.NOTE] Per informazioni generali sull'utilizzo dell'interfaccia StorSimple Snapshot Manager, passare [all'interfaccia utente StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Aggiunta o sostituzione di un dispositivo

Utilizzare la procedura seguente per aggiungere o sostituire un dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Per aggiungere o sostituire un dispositivo

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** destro nodo **dispositivi** e quindi fare clic su **Configura un dispositivo**. Verrà visualizzata la finestra di dialogo **Configura un dispositivo** .

    ![Configurare un dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Nella casella di riepilogo a discesa **dispositivo** , selezionare l'indirizzo IP del dispositivo o periferica virtuale. 

4. Nella casella di testo **Password** digitare la password StorSimple Snapshot Manager creato per il dispositivo nel portale di classica Azure. Fare clic su **OK**. Gestione di Snapshot StorSimple Cerca il dispositivo che sono stati identificati. 

    - Se il dispositivo è disponibile, StorSimple Snapshot Manager aggiunge una connessione. 

    - Se il dispositivo non è disponibile per qualsiasi motivo, StorSimple Snapshot Manager restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore e quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configura un dispositivo** .

## <a name="connect-a-device-and-verify-imports"></a>Collegare un dispositivo e verificare che le importazioni

Utilizzare la procedura seguente per collegare un dispositivo StorSimple e verificare che tutti i gruppi volume esistente che dispone di backup associati vengono importati.

#### <a name="to-connect-a-device-and-verify-imports"></a>Per collegare un dispositivo e verificare Importa

1. Per collegare un dispositivo a StorSimple Snapshot Manager, seguire le istruzioni di aggiungere o sostituire un dispositivo. Quando si connette a un dispositivo, StorSimple Snapshot Manager risponde come indicato di seguito:

    - Se il dispositivo non è disponibile per qualsiasi motivo, StorSimple Snapshot Manager restituisce un messaggio di errore. 

   - Se il dispositivo è disponibile, StorSimple Snapshot Manager aggiunge una connessione. Quando si seleziona il dispositivo, viene visualizzato nel riquadro dei **risultati** e il campo stato indica che il dispositivo è **disponibile**. StorSimple Snapshot Manager Importa i gruppi di volume configurati per il dispositivo, purché i gruppi di volume sono associati backup. Criteri di backup non vengono importati. Gruppi di volume non dispone di backup associato non vengono importati.

2. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

3. Livello di nodo principale nel **riquadro** destro e quindi fare clic su **Mostra/Nascondi importazioni visualizzazione**.

    ![Attivare/disattivare selezionare Importa visualizzazione](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. Verrà visualizzata la finestra di dialogo **Attiva/Disattiva importazioni visualizzato** lo stato del volume importato gruppi e backup. Fare clic su **OK**. 

Dopo aver importati correttamente i gruppi di volume e l'esecuzione di backup, è possibile utilizzare StorSimple Snapshot Manager gestirli, proprio come si preferisce gestire gruppi di volume e l'esecuzione di backup che si è creato e configurato con StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Aggiornare i dispositivi connessi

Utilizzare la procedura seguente per sincronizzare i dispositivi di StorSimple collegati con StorSimple Snapshot Manager.

####<a name="to-refresh-connected-devices"></a>Per aggiornare i dispositivi connessi

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** destro **dispositivi**e quindi fare clic su **Aggiorna dispositivi**. Questo consente di sincronizzare i dispositivi connessi con StorSimple Snapshot Manager in modo che è possibile visualizzare i gruppi di volume e l'esecuzione di backup, inclusi eventuali aggiunte recenti. 

    ![Aggiornare i dispositivi StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
L'azione **Aggiorna dispositivi** recupera eventuali nuovi gruppi di volume e i backup associati da dispositivi connessi. A differenza dell'azione **analisi volumi** per il nodo **volumi** , **Aggiornare i dispositivi** non consentono di ripristinare il backup del Registro di sistema.

## <a name="authenticate-a-device"></a>Eseguire l'autenticazione di un dispositivo

Utilizzare la procedura seguente per eseguire l'autenticazione di un dispositivo StorSimple con StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Per eseguire l'autenticazione di un dispositivo

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , fare clic su **dispositivi**.

3. Nel riquadro **dei risultati** , pulsante destro del mouse sul nome del dispositivo e quindi fare clic su **autentica**.

4. Verrà visualizzata la finestra di dialogo **autentica** . Digitare la password per il dispositivo e quindi fare clic su **OK**.

    ![Eseguire l'autenticazione nella finestra di dialogo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Visualizzare i dettagli dispositivo

Utilizzare la procedura seguente per visualizzare i dettagli di un dispositivo StorSimple e, se necessario, ripetere la sincronizzazione con StorSimple Snapshot gestione.

#### <a name="to-view-and-resynchronize-device-details"></a>Per visualizzare e sincronizzare nuovamente i dettagli dispositivo

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , fare clic su **dispositivi**.

3. Nel riquadro **dei risultati** , pulsante destro del mouse sul nome del dispositivo e quindi fare clic su **Dettagli**. 

4 **Dispositivi dettagli** nella finestra di dialogo visualizzata. Questa casella Mostra nome, modello, versione, il numero di serie, stato, destinazione iSCSI nome completo (IQN) e ultima sincronizzazione data e l'ora. 

   - Fare clic su **Sincronizza nuovamente** per sincronizzare il dispositivo.

   - Fare clic su **OK** o **Annulla** per chiudere la finestra di dialogo.

    ![Dettagli dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Aggiornare un singolo dispositivo

Utilizzare la procedura seguente per sincronizzare di nuovo un singolo dispositivo StorSimple con StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Per aggiornare un dispositivo

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , fare clic su **dispositivi**. 

3. Nel riquadro **dei risultati** , pulsante destro del mouse sul nome del dispositivo e quindi fare clic su **Aggiorna dispositivo**. Il dispositivo viene sincronizzato con StorSimple Snapshot Manager. 

## <a name="delete-a-device-configuration"></a>Eliminare una configurazione di dispositivi

Utilizzare la procedura seguente per eliminare una singola configurazione dispositivo StorSimple da StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Per eliminare una configurazione di dispositivi

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , fare clic su **dispositivi**. 

3. Nel riquadro **dei risultati** , pulsante destro del mouse sul nome del dispositivo e quindi fare clic su **Elimina**. 

4. Viene visualizzato il messaggio seguente. Fare clic su **Sì** per eliminare la configurazione oppure fare clic su **No** per annullare l'eliminazione.

    ![Eliminazione di configurazione del dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Modificare la password di un dispositivo scaduto

È necessario immettere una password per l'autenticazione di un dispositivo StorSimple con StorSimple Snapshot Manager. Configurare la password quando si utilizza l'interfaccia di Windows PowerShell per configurare il dispositivo. Tuttavia, può scadere la password. In questo caso, è possibile usare il portale classico Azure per cambiare la password. Quindi, perché il dispositivo è stato configurato in StorSimple Snapshot Manager prima che la password è scaduto, è necessario autenticare nuovamente il dispositivo di StorSimple Snapshot Manager. 

#### <a name="to-change-the-expired-password"></a>Per modificare la password scaduta

1. Nel portale di classica Azure, avviare il servizio StorSimple Manager.

2. Fare clic su **dispositivi** > **Configura** per il dispositivo.

3. Scorrere fino alla sezione StorSimple Snapshot Manager. Immettere una password con caratteri 14-15. Assicurarsi che la password contiene una combinazione di caratteri maiuscoli, minuscoli, numerici e speciali.

4. Immettere nuovamente la password per confermarla.

5. Fare clic su **Salva** nella parte inferiore della pagina.

#### <a name="to-re-authenticate-the-device"></a>Eseguire nuovamente l'autenticazione il dispositivo

1. Avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , fare clic su **dispositivi**. Nel riquadro **risultati** viene visualizzato un elenco dei dispositivi configurati. 

3. Selezionare il dispositivo, pulsante destro del mouse e quindi fare clic su **autentica**.

4. Nella finestra di **autenticazione** , immettere la nuova password. 

5. Selezionare il dispositivo, pulsante destro del mouse e selezionare **il dispositivo di aggiornamento**. Il dispositivo viene sincronizzato con StorSimple Snapshot Manager. 

## <a name="replace-a-failed-device"></a>Sostituire un dispositivo non riuscito

Se un dispositivo StorSimple ha esito negativo e viene sostituito da un dispositivo standby (failover), utilizzare la procedura seguente per connettersi a nuovi dispositivi e visualizzare i backup associati.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Per connettersi a un nuovo dispositivo dopo il failover

1. Riconfigurare la connessione iSCSI sul nuovo dispositivo. Per istruzioni, passare a "passaggio 7: montaggio, inizializzazione e formattare un volume" in [Distribuisci dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Se il dispositivo StorSimple nuovo ha lo stesso indirizzo IP di quella precedente, potrebbe essere possibile connettersi configurazione precedente. 

2. Arrestare il servizio di gestione StorSimple Microsoft:

    1. Avviare Server Manager.

    2. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** . 

    3. Nella finestra **servizi** selezionare il **Servizio di gestione StorSimple Microsoft**. 

    4. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **arrestare il servizio**. 

3. Rimuovere le informazioni di configurazione relative al dispositivo precedente: 

    1. In Esplora File passare alla C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Eliminare i file nella cartella BACatalog. 

4. Riavviare il servizio di gestione StorSimple Microsoft: 

    1. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** . 

    2. Nella finestra **servizi** selezionare il **Servizio di gestione StorSimple Microsoft**. 

    3. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **riavviare il servizio**. 

5. Avviare Gestione Snapshot StorSimple. 

6. Per configurare il dispositivo StorSimple nuovo, completare i passaggi nel passaggio 2: connettere un dispositivo StorSimple in [Distribuire StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md). 

7. Destro il nodo principale nel riquadro di **ambito** (Manager StorSimple Snapshot nell'esempio) e quindi fare clic su **Mostra/Nascondi importazioni visualizzazione**. 

8. Quando i gruppi di volume importato e backup sono visibili in StorSimple Snapshot Manager, viene visualizzato un messaggio. Fare clic su **OK**. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [utilizzare Gestione Snapshot StorSimple per visualizzare e gestire i volumi](storsimple-snapshot-manager-manage-volumes.md).

