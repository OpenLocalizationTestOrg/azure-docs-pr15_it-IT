<properties 
   pageTitle="Configurare MPIO sull'host matrice virtuale StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come configurare i/o percorsi multipli (MPIO) per il StorSimple matrice virtuale connesso a un host che eseguono Windows Server 2012 R2."
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
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurare i/o percorsi multipli nell'host di Windows Server per la matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come installare funzionalità i/o percorsi multipli (MPIO) sull'host Windows Server, applicare le impostazioni di configurazione specifiche per indicare i volumi solo StorSimple e quindi verificare MPIO per indicare i volumi StorSimple. La procedura si presuppone che la matrice virtuale 1200 StorSimple con due interfacce di rete sia connesso a un host di Windows Server con due interfacce di rete. Le informazioni contenute in questo articolo si applicano solo a matrice virtuale. Per informazioni sui dispositivi serie 8000 StorSimple, vedere [Configurare MPIO per StorSimple host](storsimple-configure-mpio-windows-server.md). 

La funzionalità MPIO in Windows Server consente di configurazioni di disponibilità, tolleranza dello spazio di archiviazione. MPIO utilizza componenti percorsi fisici ridondanti, schede, cavi e parametri, per creare percorsi logici tra il server e il dispositivo di archiviazione. Se si verifica un errore componente causa un percorso logico a buon fine logica dei percorsi multipli utilizza un percorso alternativo per i/o, in modo che le applicazioni possano comunque accedere ai dati. Inoltre a seconda della configurazione MPIO possa inoltre migliorare le prestazioni nuovamente bilanciamento del carico tutti questi percorsi. Per ulteriori informazioni, vedere [Panoramica di MPIO](https://technet.microsoft.com/library/cc725907.aspx "caratteristiche e panoramica di MPIO").  

Per la disponibilità della soluzione StorSimple, configurare MPIO negli host Windows Server connessi alla matrice virtuale StorSimple 1200 (noto anche come il locale virtuale dispositivo). I server host tollerabile quindi un collegamento, reti o errore dell'interfaccia. 

È necessario seguire questi passaggi per configurare MPIO: 

- Prerequisiti di configurazione

- Passaggio 1: Installare MPIO nell'host Windows Server

- Passaggio 2: Configurare MPIO per indicare i volumi StorSimple

- Passaggio 3: Volumi montaggio StorSimple nell'host

Ognuno dei passaggi precedenti viene descritto nelle sezioni seguenti.


## <a name="prerequisites"></a>Prerequisiti di

Questa sezione descrive i prerequisiti di configurazione per l'host di Windows Server e la matrice virtuale.

### <a name="on-windows-server-host"></a>Nell'host di Windows Server

-  Assicurarsi che l'host di Windows Server abbiano 2 interfacce di rete abilitate.


### <a name="on-storsimple-virtual-array"></a>Nella matrice virtuale StorSimple

- Matrice virtuale deve essere configurata come server iSCSI. Per ulteriori informazioni, vedere [configurare la matrice virtuale come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md). In matrice, è necessario attivare una o più interfacce di rete.   

- Le interfacce di rete la matrice virtuale devono essere raggiungibili nell'host dei Server di Windows.

- Creare uno o più volumi per la matrice virtuale StorSimple. Per ulteriori informazioni, vedere [aggiungere un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) nella matrice virtuale 1200 StorSimple. In questa procedura, è creata 3 volumi (2 localmente bloccata e 1 a più livelli volume come mostrato sotto) nella matrice virtuale.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configurazione hardware per matrice virtuale StorSimple

La figura seguente mostra la configurazione hardware per disponibilità e migliorando così il bilanciamento del carico per l'host di Windows Server e matrice virtuale StorSimple utilizzati in questa procedura.  

![configurazione hardware MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Come mostrato nella figura precedente:

- La matrice virtuale StorSimple viene completato il provisioning su Hyper-V è un dispositivo attivo singolo nodo configurato come server iSCSI.

- Due interfacce virtuali sono attivate la matrice. Nel web locale dell'interfaccia utente della matrice virtuale 1200, verificare che due interfacce di rete sono attivate, passare a **Impostazioni di rete** , come illustrato di seguito:

    ![Interfacce di rete attivate 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Nota gli indirizzi IPv4 delle interfacce di rete abilitato (Ethernet, 2 Ethernet per impostazione predefinita) e aver salvato per successivi utilizzi nell'host.

- Due interfacce di rete sono attivate l'host di Windows Server. Se le interfacce connesse per host e in forma di matrice sono nella stessa subnet, quindi verrà 4 percorsi disponibili. Si tratta di maiuscole/minuscole in questa procedura. Tuttavia, se ogni interfaccia di rete sull'interfaccia in forma di matrice e host in una diversa subnet IP (e non routing), quindi solo 2 percorsi sarà disponibili.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passaggio 1: Installare MPIO nell'host Windows Server

MPIO è una funzionalità facoltativa in Windows Server e non è installato per impostazione predefinita. Deve essere installato come una caratteristica tramite Server Manager. Per installare questa caratteristica sull'host Windows Server, completare la procedura seguente.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passaggio 2: Configurare MPIO per indicare i volumi StorSimple

MPIO deve essere configurato per identificare i volumi StorSimple. Per configurare MPIO per riconoscere StorSimple volumi, procedere come segue.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passaggio 3: Volumi montaggio StorSimple nell'host

Dopo aver configurato MPIO in Windows Server, volumi creato nella matrice StorSimple possono essere installato e quindi sfruttarne MPIO per la ridondanza. Per creare un volume, eseguire i passaggi seguenti.

#### <a name="to-mount-volumes-on-the-host"></a>Per collegare volumi nell'host

1. Aprire la finestra **proprietà iniziatore iSCSI** nell'host dei Server di Windows. Fare clic su **Server Manager > Dashboard > Strumenti > iniziatore iSCSI**.
2. Nella finestra di dialogo **proprietà iniziatore iSCSI** , fare clic sulla scheda individuazione e quindi fare clic su **Scopri portale di destinazione**.
3. Nella finestra di dialogo **Scopri portale di destinazione** eseguire le operazioni seguenti:
    
    - Immettere l'indirizzo IP dell'interfaccia di rete abilitato prima la matrice virtuale StorSimple. Per impostazione predefinita, questo potrebbe essere **Ethernet**. 
    - Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** .

    >[AZURE.IMPORTANT] **Se si utilizza una rete privata per le connessioni iSCSI, immettere l'indirizzo IP della porta dati connessi alla rete privata.**

4. Ripetere i passaggi 2 e 3 per una seconda interfaccia di rete (ad esempio Ethernet 2) sulla matrice. 

5. Selezionare la scheda **destinatari** nella finestra di dialogo **proprietà iniziatore iSCSI** . Per la matrice virtuale, verrà visualizzata ogni superficie volume come destinazione in **Destinazioni individuate**. In questo caso, sarebbero scoperto tre destinazioni (corrispondente a tre volumi).

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Fare clic su **Connetti** per stabilire una sessione iSCSI con la matrice StorSimple. Verrà visualizzata una finestra di dialogo **Connetti a destinazione** . Selezionare la casella di controllo **Consenti percorsi multipli** . Fare clic su **Avanzate**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Nella finestra di dialogo **Impostazioni avanzate** eseguire le operazioni seguenti:                                       
    -    Nell'elenco a discesa **Scheda locale** selezionare **iniziatore iSCSI Microsoft**.
    -    Nella casella **IP iniziatore** selezionare l'indirizzo IP dell'host.
    -    Nell'elenco a discesa di indirizzi IP **Portale destinazione** , selezionare l'indirizzo IP dell'interfaccia di matrice.
    -    Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Fare clic su **proprietà**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Nella finestra di dialogo **proprietà** fare clic su **Aggiungi sessione**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Nella finestra di dialogo **Connetti a destinazione** , selezionare la casella di controllo **Consenti percorsi multipli** . Fare clic su **Avanzate**.
11. Nella finestra di dialogo **Impostazioni avanzate** :                                        
    -  Nell'elenco a discesa **scheda locale** selezionare iniziatore iSCSI Microsoft.
    -  Nella casella **IP iniziatore** selezionare l'indirizzo IP corrispondente all'host. In questo caso, si connette due interfacce di rete nella matrice a una singola interfaccia di rete nell'host. Di conseguenza, questa interfaccia è uguale a quello specificato per la prima sessione.
    -  Nella casella **IP portale destinazione** selezionare l'indirizzo IP per la seconda interfaccia dati attivata per la matrice.
    -  Fare clic su **OK** per tornare alla finestra di dialogo Proprietà iniziatore iSCSI. È stata aggiunta una seconda sessione di destinazione.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Dopo avere aggiunto le sessioni desiderate (i percorsi), nella finestra di dialogo **proprietà iniziatore iSCSI** , selezionare la destinazione e scegliere **proprietà**. Nella scheda sessioni della finestra di dialogo **proprietà** nota sessione quattro identificatori che corrispondono ai permutazioni possibili percorso. Per annullare una sessione di, selezionare la casella di controllo accanto a un identificatore di sessione e quindi fare clic su **Disconnetti**.
 
    - Per visualizzare i dispositivi presentati entro le sessioni, selezionare la scheda **dispositivi** . Per configurare i criteri MPIO per un dispositivo selezionato, fare clic su **MPIO**. Il * *
    -  Dettagli** verrà visualizzata la finestra di dialogo. Nel **MPIO** scheda, è possibile selezionare appropriato **criterio di bilanciamento del carico** impostazioni. È inoltre possibile visualizzare il **attivo** o **il tipo di percorso Standby * *.

10. Ripetere i passaggi da 8 a 11 per aggiungere ulteriori sessioni (i percorsi) alla destinazione. Con due interfacce nell'host e due nella matrice virtuale, è possibile aggiungere un totale di quattro sessioni per ogni sito di destinazione. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Sarà necessario ripetere questi passaggi per ogni volume (superfici come destinazione).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Aprire **Gestione Computer** , passare a **Server Manager > Dashboard > Gestione Computer**. Nel riquadro sinistro fare clic su **lo spazio di archiviazione > Gestione disco**. I volumi creati sulla matrice virtuale StorSimple visibili nell'host verranno visualizzati in **Gestione disco** come dischi nuovi.

13. Creare un nuovo volume inizializzare il disco. Durante il processo di formattazione, selezionare una dimensione di unità di assegnazione (Australia) di 64 KB. Ripetere la procedura per tutti i volumi disponibili.

    ![Gestione disco](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. In **Gestione disco**, pulsante destro del mouse **su disco** e scegliere **proprietà**.

15. Nella finestra di dialogo **Proprietà dei dispositivi disco percorsi multipli** , fare clic sulla scheda **MPIO** .

    ![Proprietà del disco](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Nella sezione **Nome DSM** fare clic su **Dettagli** e verificare che i parametri siano impostati i parametri predefiniti. I parametri predefiniti sono:

    - Percorso verificare periodo = 30
    - Conteggio tentativi = 3
    - Periodo di rimozione PDO = 20
    - Intervallo tentativi = 1
    - Verificare il percorso abilitato = deselezionata.

    >[AZURE.NOTE] **Non modificare i parametri predefiniti.**


## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare la matrice virtuale StorSimple](storsimple-ova-manager-service-administration.md).
 
