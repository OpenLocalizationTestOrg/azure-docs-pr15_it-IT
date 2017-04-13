<properties
    pageTitle="App Authenticator Microsoft per telefoni cellulari | Microsoft Azure"
    description="Informazioni su come eseguire l'aggiornamento alla versione più recente di Azure autenticatore."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Microsoft autenticatore

L'app Microsoft Authenticator fornisce un ulteriore livello di protezione nell'account Azure (, ad esempio, bsimon@contoso.onmicrosoft.com), le locale account aziendale (ad esempio, bsimon@contoso.com), o l'account Microsoft (ad esempio bsimon@outlook.com).

L'app funziona in uno dei due modi:

- **Notifica**. L'app consente di impedire l'accesso non autorizzato ad account e arrestare le transazioni fraudolente premendo una notifica per smartphone e tablet. È sufficiente visualizzare la notifica e se è legittimo, selezionare **verifica**. In caso contrario, è possibile selezionare **Nega**. Per informazioni su negare le notifiche, informazioni su come usare la caratteristica di frode Report Nega per l'autenticazione a più fattori.

- **Password con codice di verifica**. L'app può essere utilizzato come un token di software per generare il codice di verifica OAuth. Immettere il codice fornito dall'applicazione nella schermata di accesso, oltre al nome utente e password, quando richiesto. Il codice di verifica fornisce un secondo metodo di autenticazione.

La vecchia app autenticatore Azure viene sostituita con la versione dell'app Authenticator Microsoft.  L'app di Azure autenticatore continueranno a funzionare, ma se si decide di passare alla nuova applicazione di Microsoft Authenticator, in questo articolo può aiutare a.  

## <a name="install-the-app"></a>Installare l'app

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Aggiungere account all'app

Per ogni account che si desidera aggiungere l'app Microsoft Authenticator, usare una delle procedure seguenti.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Aggiungere un account all'app tramite scanner codice scansione

1. Passare alla schermata delle impostazioni di verifica protezione.  Per informazioni su come visualizzare la schermata, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

2. Selezionare **Configura**.

    ![Pulsante Configura nella schermata Impostazioni di sicurezza verifica](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Verrà visualizzata una schermata con una scansione del codice su di esso.

    ![Schermata che fornisce la scansione del codice](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Aprire l'app Microsoft autenticatore. Nella schermata **account** selezionare **+**e quindi specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione.

    ![Schermata di account con segno di addizione](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Schermata di specifica di un account aziendale o dell'istituto di istruzione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Usare la videocamera per eseguire la scansione del codice e quindi selezionare **fatto** per chiudere la schermata di codice scansione.

    ![Schermata per l'analisi una scansione del codice](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Se la videocamera non funziona correttamente, è possibile immettere manualmente la scansione del codice e l'URL. Per ulteriori informazioni, vedere [aggiungere manualmente un account all'app](#add-an-account-to-the-app-manually).

5. Attendere che l'account è attivato. Al termine del processo di attivazione, selezionare **Contatto**.  Invia una notifica o un codice di verifica al telefono.  Selezionare **verifica**.

    ![Schermata in cui è selezionare verifica effettuare l'accesso](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Se è necessario un PIN per l'approvazione di verifica di accesso, è necessario immetterla.

    ![Casella per l'immissione di un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Al termine del PIN immesso, selezionare **Chiudi**. A questo punto, la verifica dovrebbe essere completata correttamente.
8. È consigliabile immettere il numero di telefono cellulare nel caso in cui si perdere l'accesso all'app. Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al nome del paese. Selezionare **Avanti**.
9. A questo punto dispone di configurare il metodo di contatto. A questo punto è possibile configurare la password dell'app per le applicazioni non browser, ad esempio Outlook 2010 o versioni precedenti. Se non si usa queste App, selezionare **completato**. In caso contrario, continuare con il passaggio successivo.

    ![Schermata per la creazione di una password per l'app](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Se si usano App diverso dal browser, copiare la password forniti app e incollare la password delle applicazioni. Per istruzioni sulle singole applicazioni, ad esempio Outlook e Lync, vedere come cambiare la password nella posta elettronica con la password di app e su come cambiare la password nell'applicazione per la password di app.
11. Selezionare **completato**.

Viene visualizzato il nuovo account nella schermata **account** .

![Schermata di account](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Aggiungere manualmente un account all'app

1. Passare alla schermata delle impostazioni di verifica protezione.  Per informazioni su come visualizzare la schermata, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

2. Selezionare **Configura**.

    ![Pulsante Configura nella schermata Impostazioni di sicurezza verifica](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Verrà visualizzata una schermata con una scansione del codice su di esso.  Nota il codice e l'URL.

    ![Schermata che fornisce la scansione del codice e l'URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Aprire l'app Microsoft autenticatore. Nella schermata **account** selezionare **+**e quindi specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione.

    ![Schermata di account con segno di addizione](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Schermata di specifica di un account aziendale o dell'istituto di istruzione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. In uno scanner, selezionare **Immettere il codice manualmente**.

    ![Schermata per l'analisi una scansione del codice](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Immettere il codice e l'URL nelle caselle appropriate nell'app.

    ![Schermata per l'immissione del codice e URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Schermata per l'immissione del codice e URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Attendere che l'account è attivato. Al termine del processo di attivazione, selezionare **Contatto**. Invia una notifica o un codice di verifica al telefono. Selezionare **verifica**.

Viene visualizzato il nuovo account nella schermata **account** .

![Schermata di account](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Aggiungere un account all'app tramite tocco ID

L'app Microsoft Authenticator in iOS supporta ID tocco.  Autenticazione a più fattori Azure consente alle organizzazioni di richiedere un PIN per i dispositivi. Con ID tocco, gli utenti di iOS non necessario inserire un PIN. Se, tuttavia, possono digitalizzare loro ID digitale e selezionare **Approva**.

Impostazione ID tocco con Microsoft Authenticator è semplice. Eseguire un'operazione impegnativa verifica normale con un PIN. Se il dispositivo supporta ID tocco, Microsoft Authenticator verrà configurarlo automaticamente per l'account.

![Verifica del programma di installazione di ID tocco](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Da che punto in poi, quando viene richiesto di verificare l'accesso in, selezionare la notifica push ricevuto ed esaminare l'ID digitale anziché immettere il PIN.

![Invio di notifiche](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Disinstallare la vecchia app l'autenticazione di Azure

Dopo aver aggiunto tutti gli account per la nuova app, è possibile disinstallare la vecchia app dal telefono.

## <a name="delete-an-account"></a>Eliminare un account

Per rimuovere un account dall'app Authenticator Microsoft, selezionare l'account e quindi scegliere **Elimina**.

![Pulsante Elimina](./media/multi-factor-authentication-azure-authenticator/remove.png)
