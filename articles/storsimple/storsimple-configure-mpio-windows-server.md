<properties 
   pageTitle="Configurare MPIO del dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come configurare i/o percorsi multipli (MPIO) per il dispositivo StorSimple connesso a un host che eseguono Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurare i/o percorsi multipli per il dispositivo StorSimple

Microsoft ha creato il supporto per la funzionalità dei / o percorsi multipli (MPIO) in Windows Server per assistenza disponibilità, tolleranza SAN configurazioni della build. MPIO utilizza componenti percorsi fisici ridondanti, schede, cavi e parametri, per creare percorsi logici tra il server e il dispositivo di archiviazione. Se si verifica un errore componente causa un percorso logico a buon fine logica dei percorsi multipli utilizza un percorso alternativo per i/o, in modo che le applicazioni possano comunque accedere ai dati. Inoltre a seconda della configurazione MPIO possa inoltre migliorare le prestazioni nuovamente bilanciamento del carico tutti questi percorsi. Per ulteriori informazioni, vedere [Panoramica di MPIO](https://technet.microsoft.com/library/cc725907.aspx "caratteristiche e panoramica di MPIO").  

Per la disponibilità della soluzione StorSimple, è necessario configurare MPIO nel dispositivo StorSimple. Quando MPIO è installato nel server host che eseguono Windows Server 2012 R2, il server tollerabile quindi un collegamento, reti o errore dell'interfaccia. 

MPIO è una funzionalità facoltativa in Windows Server e non è installato per impostazione predefinita. Deve essere installato come una caratteristica tramite Server Manager. In questo argomento illustra i passaggi da seguire per installare e usare la caratteristica MPIO su un host che eseguono Windows Server 2012 R2 e connesso a un dispositivo fisico StorSimple.

>[AZURE.NOTE] **Questa procedura è applicabile 8000 StorSimple solo per serie. MPIO non è attualmente supportata in un dispositivo virtuale StorSimple.**

È necessario seguire questi passaggi per configurare MPIO nel dispositivo StorSimple:

- Passaggio 1: Installare MPIO nell'host Windows Server

- Passaggio 2: Configurare MPIO per indicare i volumi StorSimple

- Passaggio 3: Volumi montaggio StorSimple nell'host

- Passaggio 4: Configurare MPIO per elevata disponibilità e il bilanciamento del carico

Ognuno dei passaggi precedenti viene descritto nelle sezioni seguenti.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passaggio 1: Installare MPIO nell'host Windows Server

Per installare questa caratteristica sull'host Windows Server, completare la procedura seguente.

#### <a name="to-install-mpio-on-the-host"></a>Per installare MPIO nell'host

1. Aprire Gestione Server sull'host Windows Server. Per impostazione predefinita, Server Manager viene avviato quando un membro del gruppo Administrators accede a un computer che esegue Windows Server 2012 R2 o Windows Server 2012. Se il Server Manager non è già aperto, fare clic su **Start > Server Manager**.
![Gestione server](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Fare clic su **Server Manager > Dashboard > aggiungere ruoli e le funzionalità**. Verrà avviata la procedura guidata **Aggiungi ruoli e funzionalità** .
![Aggiungere ruoli e guidata funzionalità 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Nella procedura guidata **Aggiungi ruoli e funzionalità** , eseguire le operazioni seguenti:

    - Nella pagina **prima di iniziare** , fare clic su **Avanti**.
    - Nella pagina **Seleziona tipo di installazione** accettare l'impostazione di installazione **basato sui ruoli o funzionalità di base** . Fare clic su **Avanti**. ![Aggiungere ruoli e guidata funzionalità 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Nella pagina **Selezionare il server di destinazione** , scegliere **Selezionare un server del pool di server**. Il server host deve rilevato automaticamente. Fare clic su **Avanti**.
    - Nella pagina **Selezione ruoli server** , fare clic su **Avanti**.
    - Nella pagina **selezionare caratteristiche** selezionare **I/o percorsi multipli**e fare clic su **Avanti**. ![Aggiungere ruoli e guidata funzionalità 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - Nella pagina **Conferma selezioni per l'installazione** , confermare la selezione e quindi selezionare **automaticamente se richiesto, riavviare il server di destinazione**, come illustrato di seguito. Fare clic su **Installa**. ![Aggiungere ruoli e guidata funzionalità 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Verrà visualizzato durante l'installazione è stata completata. Fare clic su **Chiudi** per chiudere la procedura guidata. ![Aggiungere ruoli e guidata funzionalità 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passaggio 2: Configurare MPIO per indicare i volumi StorSimple

MPIO deve essere configurato per identificare i volumi StorSimple. Per configurare MPIO per riconoscere StorSimple volumi, procedere come segue.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Per configurare MPIO per indicare i volumi StorSimple

1. Aprire la **configurazione MPIO**. Fare clic su **Server Manager > Dashboard > Strumenti > MPIO**.

2. Nella finestra di dialogo **Proprietà MPIO** , selezionare la scheda **Individua percorsi multipli** .

3. Aggiungi **supporto per i dispositivi iSCSI**e quindi fare clic su **Aggiungi**.  
![Alla scoperta di proprietà MPIO con più percorsi](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Riavviare il server quando richiesto.
5. Nella finestra di dialogo **Proprietà MPIO** , fare clic sulla scheda **Dispositivi MPIO** . Fare clic su **Aggiungi**.
    </br>![MPIO proprietà MPIO dispositivi](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. In **ID Hardware dispositivo**, finestra di dialogo **Aggiungi supporto MPIO** immettere il numero seriale del dispositivo. Per ottenere il numero seriale di dispositivo, è possibile accedere al servizio di gestione StorSimple e passare a **dispositivi > Dashboard**. Il numero seriale di dispositivo viene visualizzato nella parte destra riquadro **Riepilogo rapido** del dashboard dispositivo.
    </br>![Aggiungere il supporto MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Riavviare il server quando richiesto.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passaggio 3: Volumi montaggio StorSimple nell'host

Dopo aver configurato MPIO in Windows Server, volumi creati sul dispositivo StorSimple possono essere installato e quindi sfruttarne MPIO per la ridondanza. Per creare un volume, eseguire i passaggi seguenti.

#### <a name="to-mount-volumes-on-the-host"></a>Per collegare volumi nell'host

1. Aprire la finestra **proprietà iniziatore iSCSI** nell'host dei Server di Windows. Fare clic su **Server Manager > Dashboard > Strumenti > iniziatore iSCSI**.
2. Nella finestra di dialogo **proprietà iniziatore iSCSI** , fare clic sulla scheda individuazione e quindi fare clic su **Scopri portale di destinazione**.
3. Nella finestra di dialogo **Scopri portale di destinazione** eseguire le operazioni seguenti:
    
    - Immettere l'indirizzo IP del punto dati di un dispositivo StorSimple (ad esempio, immettere dati 0).
    - Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** .

    >[AZURE.IMPORTANT] **Se si utilizza una rete privata per le connessioni iSCSI, immettere l'indirizzo IP della porta dati connessi alla rete privata.**

4. Ripetere i passaggi 2 e 3 per una seconda interfaccia di rete (ad esempio 1 di dati) nel dispositivo. Tenere presente che queste interfacce devono essere abilitate per iSCSI. Per ulteriori informazioni, vedere [le interfacce di rete di modifica](storsimple-modify-device-config.md#modify-network-interfaces).
5. Selezionare la scheda **destinatari** nella finestra di dialogo **proprietà iniziatore iSCSI** . Verrà visualizzata la destinazione di dispositivo StorSimple IQN in **Destinazioni individuate**.
 ![Scheda destinazioni proprietà iniziatore iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Fare clic su **Connetti** per stabilire una sessione iSCSI con il dispositivo StorSimple. Verrà visualizzata una finestra di dialogo **Connetti a destinazione** .

7. Nella finestra di dialogo **Connetti a destinazione** , selezionare la casella di controllo **Consenti percorsi multipli** . Fare clic su **Avanzate**.

8. Nella finestra di dialogo **Impostazioni avanzate** eseguire le operazioni seguenti:                                       
    -    Nell'elenco a discesa **Scheda locale** selezionare **iniziatore iSCSI Microsoft**.
    -    Nella casella **IP iniziatore** selezionare l'indirizzo IP dell'host.
    -    Nell'elenco a discesa di indirizzi IP **Portale destinazione** , selezionare l'indirizzo IP dell'interfaccia di dispositivo.
    -    Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** .

9. Fare clic su **proprietà**. Nella finestra di dialogo **proprietà** fare clic su **Aggiungi sessione**.
10. Nella finestra di dialogo **Connetti a destinazione** , selezionare la casella di controllo **Consenti percorsi multipli** . Fare clic su **Avanzate**.
11. Nella finestra di dialogo **Impostazioni avanzate** :                                        
    -  Nell'elenco a discesa **scheda locale** selezionare iniziatore iSCSI Microsoft.
    -  Nella casella **IP iniziatore** selezionare l'indirizzo IP corrispondente all'host. In questo caso, si connette due interfacce di rete sul dispositivo a una singola interfaccia di rete nell'host. Di conseguenza, questa interfaccia è uguale a quello specificato per la prima sessione.
    -  Nella casella **IP portale destinazione** selezionare l'indirizzo IP per la seconda interfaccia dati attivata nel dispositivo.
    -  Fare clic su **OK** per tornare alla finestra di dialogo Proprietà iniziatore iSCSI. È stata aggiunta una seconda sessione di destinazione.

12. Aprire **Gestione Computer** , passare a **Server Manager > Dashboard > Gestione Computer**. Nel riquadro sinistro fare clic su **lo spazio di archiviazione > Gestione disco**. I volumi creati sul dispositivo StorSimple visibili nell'host verranno visualizzati in **Gestione disco** come dischi nuovi.

13. Creare un nuovo volume inizializzare il disco. Durante il processo di formattazione, selezionare una dimensione di blocco di 64 KB.
![Gestione disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. In **Gestione disco**, pulsante destro del mouse **su disco** e scegliere **proprietà**.
15. Nel modello di StorSimple # # # finestra di dialogo **Proprietà di più percorsi disco dispositivo** fare clic sulla scheda **MPIO** .
![StorSimple 8100 disco più percorsi DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Nella sezione **Nome DSM** fare clic su **Dettagli** e verificare che i parametri siano impostati i parametri predefiniti. I parametri predefiniti sono:

    - Percorso verificare periodo = 30
    - Conteggio tentativi = 3
    - Periodo di rimozione PDO = 20
    - Intervallo tentativi = 1
    - Verificare il percorso abilitato = deselezionata.


>[AZURE.NOTE] **Non modificare i parametri predefiniti.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passaggio 4: Configurare MPIO per elevata disponibilità e il bilanciamento del carico

Per più percorsi basati su disponibilità e bilanciamento del carico, più sessioni devono essere aggiunta manualmente per dichiarare diversi percorsi disponibili. Ad esempio, se l'host ha due interfacce connessi alla rete SAN e il dispositivo ha due interfacce connessione alla rete SAN, quindi è necessario quattro sessioni configurate con permutazioni percorso corretto (solo due sessioni devono avvenire se ogni interfaccia di dati e host è in una diversa subnet IP e non è).

>[AZURE.IMPORTANT] **È consigliabile non combinare 1 GbE 10 GbE interfacce di rete e. Se si usano due interfacce di rete, entrambe le interfacce devono essere di tipo identici.**

La procedura seguente viene descritto come aggiungere sessioni quando un dispositivo StorSimple con due interfacce di rete è connesso a un host con due interfacce di rete.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Per configurare MPIO per elevata disponibilità e il bilanciamento del carico

1. Eseguire un rilevamento della destinazione: nella scheda **individuazione** della finestra di dialogo **proprietà iniziatore iSCSI** fare clic su **Scopri Portal**.
2. Nella finestra di dialogo **Connetti a destinazione** immettere l'indirizzo IP di uno delle interfacce di rete del dispositivo.
3. Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** .

4. Nella finestra di dialogo **proprietà iniziatore iSCSI** selezionare la scheda **destinazioni** , evidenziare la destinazione della scoperta di recente e quindi fare clic su **Connetti**. Viene visualizzata la finestra di dialogo **Connetti a destinazione** .

5. Nella finestra di dialogo **Connetti a destinazione** :
    
    - Lasciare la destinazione selezionato predefinita impostazione per **aggiungere la connessione** all'elenco di destinazioni preferite. In questo modo il dispositivo è sarà automaticamente il tentativo di riavviare la connessione ogni volta che si riavvia il computer.
    - Selezionare la casella di controllo **Consenti percorsi multipli** .
    - Fare clic su **Avanzate**.

6. Nella finestra di dialogo **Impostazioni avanzate** :
    - Nell'elenco a discesa **Scheda locale** selezionare **iniziatore iSCSI Microsoft**.
    - Nella casella **IP iniziatore** selezionare l'indirizzo IP dell'host.
    - Nella casella **IP portale destinazione** selezionare l'indirizzo IP dell'interfaccia dati attivato nel dispositivo.
    - Fare clic su **OK** per tornare alla finestra di dialogo Proprietà iniziatore iSCSI.

7. Fare clic su **proprietà**e nella finestra di dialogo **proprietà** fare clic su **Aggiungi sessione**.

8. Nella finestra di dialogo **Connetti a destinazione** , selezionare la casella di controllo **Consenti percorsi multipli** e quindi fare clic su **Avanzate**.

9. Nella finestra di dialogo **Impostazioni avanzate** :
    1. Nell'elenco a discesa **scheda locale** selezionare **iniziatore iSCSI Microsoft**.
    2. Nella casella **IP iniziatore** selezionare l'indirizzo IP corrispondente alla seconda interfaccia nell'host.
    3. Nella casella **IP portale destinazione** selezionare l'indirizzo IP per la seconda interfaccia dati attivata nel dispositivo.
    4. Fare clic su **OK** per tornare alla finestra di dialogo **proprietà iniziatore iSCSI** . È stato aggiunto una seconda sessione di destinazione.

10. Ripetere i passaggi 8-10 per aggiungere ulteriori sessioni (i percorsi) di destinazione. Con due interfacce nell'host e due sul dispositivo, è possibile aggiungere un totale di quattro sessioni.

11. Dopo avere aggiunto le sessioni desiderate (i percorsi), nella finestra di dialogo **proprietà iniziatore iSCSI** , selezionare la destinazione e scegliere **proprietà**. Nella scheda sessioni della finestra di dialogo **proprietà** nota sessione quattro identificatori che corrispondono ai permutazioni possibili percorso. Per annullare una sessione di, selezionare la casella di controllo accanto a un identificatore di sessione e quindi fare clic su **Disconnetti**.

12. Per visualizzare i dispositivi presentati entro le sessioni, selezionare la scheda **dispositivi** . Per configurare i criteri MPIO per un dispositivo selezionato, fare clic su **MPIO**. Verrà visualizzata la finestra di dialogo **Dettagli dispositivo** . Nella scheda **MPIO** , è possibile selezionare le impostazioni del **Criterio di bilanciamento del carico** appropriate. È anche possibile visualizzare il tipo di percorso **attivo** o **Standby** .

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per modificare la configurazione del dispositivo StorSimple](storsimple-modify-device-config.md).
 
