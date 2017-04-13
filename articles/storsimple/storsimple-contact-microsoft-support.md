<properties 
   pageTitle="Contattare il supporto tecnico Microsoft | Microsoft Azure"
   description="Informazioni su come creare una richiesta di assistenza e avviare una sessione di supporto nel dispositivo StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Contattare il supporto tecnico Microsoft

Se si riscontrano problemi con la soluzione Microsoft Azure StorSimple, è possibile creare una richiesta di assistenza per il supporto tecnico. In una sessione in linea con il supporto tecnico, è necessario anche avviare una sessione di supporto nel dispositivo StorSimple. In questo articolo illustra le:

- Come creare una richiesta di assistenza.
- Informazioni su come avviare una sessione di supporto nell'interfaccia di Windows PowerShell del dispositivo StorSimple.

Esaminare le [informazioni e contratti di servizio supporto tecnico serie 8000 StorSimple](https://msdn.microsoft.com/library/mt433077.aspx) prima di creare una richiesta di assistenza.

## <a name="create-a-support-request"></a>Creare una richiesta di assistenza

Per creare una richiesta di assistenza, procedere come segue:

#### <a name="to-create-a-support-request"></a>Per creare una richiesta di assistenza

1. Nel [portale classica Azure](https://manage.windowsazure.com/), nell'angolo superiore destro, selezionare il nome dell'account e quindi fare clic su **Contattare il supporto tecnico Microsoft**.

    ![Supporto di MS contatto tramite ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Si verrà reindirizzati al portale di Azure nuovo (portal.azure.com). Fare clic sul riquadro **nuova richiesta di assistenza** .

    ![Contattare il supporto di MS tramite nuovo portale](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    Sul lato destro dello schermo, viene visualizzato il riquadro **nuova richiesta di assistenza** . 

    ![Nuovo riquadro richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Nella finestra di dialogo **Nozioni di base** , completare le operazioni seguenti:                                
    1. Dall'elenco a discesa **tipo di problema** selezionare **tecnico**.
    2. Selezionare una **sottoscrizione** dall'elenco a discesa.
    3. Nell'elenco a discesa **servizio** selezionare **StorSimple**. 
    4. Selezionare un **piano di supporto** dall'elenco a discesa. È necessario un piano di supporto a pagamento per attivare il supporto tecnico.

4. Fare clic su **Avanti**. Verrà visualizzata la finestra di dialogo **problema** .

    ![Nuovo riquadro richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Nella finestra di dialogo **problema** , eseguire le operazioni seguenti:

    1.  Selezionare un livello di **gravità** dall'elenco a discesa.
    2.  Selezionare un **tipo di problema** dall'elenco a discesa.
    3.  Selezionare una **categoria** nell'elenco a discesa. 
    4.  Nella casella **Dettagli** descrivere brevemente il problema.
    5.  Nella casella **intervallo di tempo** indicano la data, ora e fuso orario che corrisponde a quella più recente del problema.
    6.  In **caricamento File**, fare clic sull'icona della cartella per il pacchetto di supporto.
    7.  Selezionare la casella di controllo di **condivisione delle informazioni di diagnostiche** .

6. Fare clic su **Avanti**. Viene visualizzata la finestra di dialogo **informazioni di contatto** .

    ![Nuovo riquadro richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Immettere le informazioni di contatto e selezionare un metodo di contatto (telefono o messaggio di posta elettronica). 

8. Selezionare la casella di controllo **Salva contattare modifiche per le richieste di assistenza futuri** .

9. Fare clic su **Crea**.

Dopo che è stata inviata la richiesta, si verrà contattati tecnico quanto prima di procedere con la richiesta.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Avviare una sessione di supporto tecnico in Windows PowerShell per StorSimple

Per risolvere i problemi che potrebbero verificarsi con il dispositivo StorSimple, sarà necessario con il team di supporto Microsoft. Supporto Microsoft potrebbe essere necessario usare una sessione di supporto per accedere al dispositivo. 

Per avviare una sessione di supporto, procedere come segue:

#### <a name="to-start-a-support-session"></a>Per avviare una sessione di supporto

1. Accedere al dispositivo direttamente tramite la console seriale o una sessione telnet da un computer remoto. A tale scopo, seguire i passaggi [PuTTY utilizzare per connettersi alla console seriale dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Nella sessione che viene visualizzata, premere il tasto **INVIO** per ottenere un prompt dei comandi.

3. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**.

4. Al prompt dei comandi, digitare la password seguente: 

    `Password1`

5. Al prompt dei comandi, digitare il comando seguente:

    `Enable-HcsSupportAccess`

6. Verrà visualizzata una stringa crittografata all'utente. Copiare la stringa in un editor di testo, ad esempio Blocco note.

7. Salvare questa stringa e inviare un messaggio di posta elettronica al supporto Microsoft. 

> [AZURE.IMPORTANT] È possibile disattivare l'accesso di supporto eseguendo `Disable-HcsSupportAccess`. Il dispositivo StorSimple anche tenterà di disattivare l'accesso di supporto 8 ore dopo che è stata avviata la sessione. È consigliabile modificare le credenziali del dispositivo StorSimple dopo l'avvio di una sessione di supporto.
