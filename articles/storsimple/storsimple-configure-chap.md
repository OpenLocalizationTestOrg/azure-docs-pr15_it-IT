<properties 
   pageTitle="Configurare CHAP del dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come configurare la richiesta di verifica CHAP Handshake Authentication Protocol () in un dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configurare CHAP del dispositivo StorSimple

In questa esercitazione viene illustrato come configurare CHAP del dispositivo StorSimple. La procedura dettagliata in questo articolo si applica a serie 8000 StorSimple, nonché StorSimple 1200 dispositivi.

CHAP l'acronimo di verifica Handshake Authentication Protocol. Si tratta di uno schema di autenticazione utilizzato dal server per la convalida dell'identità dei client remoti. La verifica è basata su una password condivisa o segreta. CHAP può essere unidirezionale (unidirezionale) o comune (bidirezionale). CHAP unidirezionale è quando la destinazione autentica iniziatore. Mano, questo protocollo reciproco o indietro, è necessario che la destinazione autenticazione dell'iniziatore e quindi l'iniziatore eseguire l'autenticazione di destinazione. È possibile implementare l'autenticazione iniziatore senza l'autenticazione di destinazione. Tuttavia, è possibile implementare l'autenticazione di destinazione solo se l'autenticazione iniziatore viene inoltre implementata. 

Come ottimale, è consigliabile utilizzare CHAP per migliorare la sicurezza iSCSI.

>[AZURE.NOTE] Tenere presente che IPSEC non è attualmente supportato nei dispositivi StorSimple.

Il CHAP sul dispositivo StorSimple possono essere configurate nei modi seguenti:

- Autenticazione unidirezionale o unidirezionale

- Autenticazione reciproca o indietro o bidirezionale

In ciascuno di questi casi, il portale per il dispositivo e il software di iniziatore iSCSI server deve essere configurato. La procedura dettagliata per questa configurazione è descritti nell'esercitazione che segue.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticazione unidirezionale o unidirezionale

In autenticazione unidirezionale, la destinazione autentica iniziatore. Questa autenticazione è necessario configurare le impostazioni di iniziatore CHAP sul dispositivo StorSimple e iSCSI software Initiator nell'host. Successivamente sono descritte le procedure dettagliate per il dispositivo di StorSimple e host di Windows.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Per configurare il dispositivo per l'autenticazione unidirezionale

1. Nel portale di classica Azure, nella pagina **dispositivi** fare clic sulla scheda **Configura** .

    ![CHAP iniziatore](./media/storsimple-configure-chap/IC740943.png)

2. Scorrere verso il basso in questa pagina, quindi nella sezione **CHAP iniziatore** :
                                                    
    1. Specificare un nome utente per l'iniziatore CHAP.

    2. Fornire una password per l'iniziatore CHAP.

         > [AZURE.IMPORTANT] Il nome utente CHAP deve contenere meno di 233 caratteri. La password CHAP deve essere compreso tra 12 e 16 caratteri. Un nome utente o password più verrà generato un errore di autenticazione dell'host di Windows.
    
    3. Confermare la password.

4. Fare clic su **Salva**. Verrà visualizzato un messaggio di conferma. Fare clic su **OK** per salvare le modifiche.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Per configurare l'autenticazione unidirezionale Windows Server host

1. Il server di host di Windows, avviare l'iniziatore iSCSI.

2. Nella finestra **proprietà iniziatore iSCSI** , procedere come segue:
                                                    
    1. Fare clic sulla scheda **individuazione** .

        ![proprietà iniziatore iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Fare clic su **Individua Portal**.

3. Nella finestra di dialogo **Scopri portale destinazione** :
                                                    
    1. Specificare l'indirizzo IP del dispositivo.

    3. Fare clic su **Avanzate**.

        ![Alla scoperta di portale destinazione](./media/storsimple-configure-chap/IC740945.png)

4. Nella finestra di dialogo **Impostazioni avanzate** :
                                                    
    1. Selezionare la casella di controllo **Abilita CHAP Accedi** .

    2. Nel campo **nome** immettere il nome utente specificato per l'iniziatore CHAP nel portale di classica.

    3. Nel campo **segreto destinazione** specificare la password specificata per iniziatore CHAP nel portale di classica.

    4. Fare clic su **OK**.

        ![Impostazioni avanzate generale](./media/storsimple-configure-chap/IC740946.png)

5. Nella scheda **destinazioni** della finestra **delle proprietà iniziatore iSCSI** , lo stato dispositivo dovrebbero essere visualizzate come **connesso**. Se si utilizza un dispositivo StorSimple 1200, quindi ogni volume verrà essere installato come destinazione iSCSI come illustrato di seguito. Di conseguenza, saranno necessario passaggi 3 e 4 di essere ripetute per ogni volume.

    ![Volumi installati come separare le destinazioni](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Se si cambia il nome iSCSI, il nuovo nome verrà utilizzato per le nuove sessioni iSCSI. Nuove impostazioni non vengono utilizzate per esistenti sessioni fino a quando non ci si disconnette dal e accedere nuovamente.

Per ulteriori informazioni sulla configurazione CHAP Windows Server host, vedere [altre considerazioni](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Autenticazione reciproca e bidirezionale

In autenticazione bidirezionale, la destinazione autentica l'iniziatore e quindi l'iniziatore autentica la destinazione. È necessario all'utente di configurare le impostazioni di iniziatore CHAP, nonché le impostazioni di CHAP inverse del software iniziatore dispositivo e iSCSI nell'host. Le procedure seguenti descrivono i passaggi per configurare l'autenticazione reciproca nel dispositivo e dell'host di Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Per configurare il dispositivo per l'autenticazione

1. Nel portale di classica Azure, nella pagina **dispositivi** fare clic sulla scheda **Configura** .

    ![Destinazione CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Scorrere verso il basso in questa pagina, quindi nella sezione **CHAP Target** :
                                                    
    1. Specificare un **nome utente invertire CHAP** del dispositivo.

    2. Fornire una **password invertire CHAP** per il dispositivo.

    3. Confermare la password.

3. Nella sezione **CHAP iniziatore** :
                                                
    1. Specificare un **nome utente** per il dispositivo.

    1. Fornire una **password** per il dispositivo.

    3. Confermare la password.

4. Fare clic su **Salva**. Verrà visualizzato un messaggio di conferma. Fare clic su **OK** per salvare le modifiche.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Per configurare l'autenticazione bidirezionale Windows Server host

1. Il server di host di Windows, avviare l'iniziatore iSCSI.

2. Nella finestra **proprietà iniziatore iSCSI** fare clic sulla scheda **configurazione** .

3. Fare clic su **CHAP**.

4. Nella finestra di dialogo **iSCSI segreto CHAP reciproca iniziatore** :
                                                    
    1. Digitare l' **Invertire Password CHAP** configurati nel portale di classica Azure.

    2. Fare clic su **OK**.

        ![iSCSI segreto reciproco CHAP](./media/storsimple-configure-chap/IC740949.png)

5. Fare clic sulla scheda **i siti di destinazione** .

6. Fare clic sul pulsante **Connetti** . 

7. Nella finestra di dialogo **Connessione alla destinazione** , fare clic su **Avanzate**.

8. Nella finestra di dialogo **Proprietà avanzate** :
                                                    
    1. Selezionare la casella di controllo **Abilita CHAP Accedi** .

    2. Nel campo **nome** immettere il nome utente specificato per l'iniziatore CHAP nel portale di classica.

    3. Nel campo **segreto destinazione** specificare la password specificata per iniziatore CHAP nel portale di classica.

    4. Selezionare la casella di controllo **Esegui autenticazione reciproca** .

        ![Autenticazione reciproca impostazioni avanzate](./media/storsimple-configure-chap/IC740950.png)

    5. Fare clic su **OK** per completare la configurazione di CHAP
     
Per ulteriori informazioni sulla configurazione CHAP Windows Server host, vedere [altre considerazioni](#additional-considerations).

## <a name="additional-considerations"></a>Altre considerazioni

La funzionalità di **Connessione rapida** non supporta connessioni con CHAP abilitato. Quando è abilitato CHAP, assicurarsi di usare il pulsante **Connetti** è disponibile nella scheda **destinazioni** per connettersi a un valore di destinazione.

![Connettersi alla destinazione](./media/storsimple-configure-chap/IC740947.png)

Nella finestra di **connettersi a destinazione** finestra di dialogo che viene visualizzata, selezionare la casella di controllo **Aggiungi la connessione all'elenco di destinazioni preferite** . In questo modo che ogni volta che il riavvio del computer, si è tentato di ripristinare la connessione alle destinazioni preferite iSCSI.

## <a name="errors-during-configuration"></a>Errori durante la configurazione

Se la configurazione di CHAP è corretta, in genere che visualizzare un messaggio di errore **errore di autenticazione** .

## <a name="verification-of-chap-configuration"></a>Verifica della configurazione CHAP

È possibile verificare che CHAP viene utilizzato completando la procedura seguente.

#### <a name="to-verify-your-chap-configuration"></a>Per verificare la configurazione di CHAP

1. Fare clic su **destinazioni preferite**.

2. Selezionare la destinazione abilitate per l'autenticazione.

3. Fare clic su **Dettagli**.

    ![destinazioni preferite iSCSI iniziatore proprietà](./media/storsimple-configure-chap/IC740951.png)

4. Nella finestra di dialogo **Dettagli destinazione Preferiti** nota nel campo **autenticazione** . Se la configurazione è stata completata, dovrebbe essere indicato **CHAP**.

    ![Dettagli destinazione preferita](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulla [protezione StorSimple](storsimple-security.md).

- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
