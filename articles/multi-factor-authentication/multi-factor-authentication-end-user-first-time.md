<properties
    pageTitle="Configurare la verifica in due passaggi per l'account aziendale o dell'istituto di istruzione"
    description="Quando la propria azienda Configura autenticazione a più fattori Azure, verrà richiesto di iscriversi per la verifica in due passaggi. Informazioni su come configurare l'account. "
    services="multi-factor-authentication"
    keywords="come usare azure active directory nel cloud, esercitazione directory active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurare un account personale per la verifica in due passaggi

La verifica in due passaggi è un passaggio di rafforzare la sicurezza che consente di proteggere il proprio account rendendo più difficile per altri utenti suddividere. Se si sta leggendo in questo articolo, probabile che sia ottenuto un messaggio di posta elettronica all'amministratore aziendale o dell'istituto di istruzione sull'autenticazione a più fattori. Forse si tentato di accedere e ha ricevuto un messaggio in cui viene chiesto di configurare la verifica rafforzare la sicurezza. Se questo è il caso **che non è possibile accedere fino al completamento del processo di registrazione automatica**.

In questo articolo consente di configurare l' **aziendale o dell'istituto di istruzione account**. Se si desidera attivare la verifica in due passaggi per il proprio, account Microsoft personale, vedere [informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Determinare l'utilizzo di autenticazione a più fattori

Verifica in due passaggi funziona viene chiesto di due elementi di identificazione quando si accede. Prima di tutto che chiede il nome utente e la password come di consueto. Quindi è contatto un telefono che è possibile sapere appartiene, e si verificare che il tentativo di accesso non autorizzato.  

Per iniziare il processo di installazione, provare ad accedere al proprio account in modo analogo in genere. Se l'amministratore ha configurato l'account per la verifica in due passaggi, verrà richiesto di avviare il processo di registrazione automatica. Iniziare il processo, fare clic su **configurare ora.**

![Programma di installazione](./media/multi-factor-authentication-end-user-first-time/first.png)

La prima domanda il processo di registrazione è la modalità di contattare l'utente. Esaminare le opzioni descritti nella tabella e utilizzare i collegamenti per passare alla procedura di configurazione per ogni metodo.

| Metodo di contatto | Descrizione |
| --- | --- |
[App per dispositivi mobili](#use-a-mobile-app-as-the-contact-method) | - **Ricevere notifiche per la verifica.** Questa opzione inserisce una notifica all'app autenticatore smartphone e tablet. Visualizzare la notifica e, se è legittimo, selezionare **autentica** nell'app. Il lavoro o dell'istituto di istruzione può richiedere immettere un PIN prima eseguire l'autenticazione.<br>- **Usare il codice di verifica.** In questa modalità app autenticatore genera un codice di verifica che gli aggiornamenti ogni 30 secondi. Immettere il codice di verifica aggiornato nell'interfaccia di accesso.<br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Chiama cellulare o il testo](#use-your-mobile-phone-as-the-contact-method) | - **Telefonata** inserisce una chiamata vocale automatico per il numero di telefono specificato. Rispondere alla chiamata e fare clic su # il tastierino del telefono per l'autenticazione.<br>- **Messaggio di testo** termina un messaggio di testo che contiene un codice di verifica. Dopo la richiesta nel testo, rispondere al messaggio di testo o immettere il codice di verifica disponibili nell'interfaccia di accesso. |  
[Office chiamata telefonica](#use-your-office-phone-as-the-contact-method) | Inserisce una chiamata vocale automatico per il numero di telefono specificato. Rispondere alla chiamata e preme # nel tastierino del telefono per l'autenticazione. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utilizzare un'app per dispositivi mobili come il metodo di contatto

Con questo metodo è necessario installare un'app autenticatore nel proprio telefono o tablet. I passaggi descritti in questo articolo dipendono dall'applicazione di Microsoft Authenticator, è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selezionare **app per dispositivi mobili** nell'elenco a discesa.
2. Selezionare **notifiche per la verifica** o **il codice di verifica**, quindi selezionare **l'impostazione**.

    ![Schermata di verifica rafforzare la sicurezza](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. In un telefono o tablet, aprire l'app e selezionare **+** per aggiungere un account. (Nei dispositivi Android, selezionare i puntini di sospensione, quindi **Aggiungi account**).
4. Specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Verrà visualizzata la finestra di uno scanner di codice scansione sul telefono. Se la videocamera non funziona correttamente, è possibile scegliere di immettere le informazioni sulla società manualmente. Per ulteriori informazioni, vedere [aggiungere manualmente un account](#add-an-account-manually).  
5. Eseguire la scansione Scansione codice che erano presenti con lo schermo per la configurazione di app per dispositivi mobili.  Selezionare **fatto** per chiudere la schermata di codice scansione.  

    ![Schermata di codice scansione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Al termine dell'attivazione sul telefono, selezionare **Contatto**.  Questo passaggio invia una notifica o un codice di verifica al telefono. Selezionare **verifica**.  
7. Se è necessario un PIN per l'approvazione di verifica di accesso, è necessario immetterla.

    ![Casella per l'immissione di un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Al termine del PIN immesso, selezionare **Chiudi**. A questo punto, la verifica dovrebbe essere completata correttamente.
9. È consigliabile immettere il numero di telefono cellulare nel caso in cui si perdere l'accesso all'app per dispositivi mobili. Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al nome del paese. Selezionare **Avanti**.
10. A questo punto viene richiesto di configurare la password dell'app per le applicazioni non browser, ad esempio Outlook 2010 o versioni precedenti o app di posta elettronica nativo sul dispositivo Apple. Ciò avviene perché alcune App non supporta la verifica in due passaggi. Se non si utilizza queste App, fare clic su **Chiudi** e ignorare il resto dei passaggi.
11. Se si utilizza queste App, copiare la password di app disponibili e incollarlo nell'applicazione anziché la password regolare. È possibile utilizzare la stessa password app per più applicazioni. Per ulteriori informazioni, [agevolare la password dell'app].
12. Fare clic su **Chiudi**.


### <a name="add-an-account-manually"></a>Aggiungere manualmente un account
Se si desidera aggiungere un account a app per dispositivi mobili manualmente, invece di usare il Visualizzatore di scansione, seguire questa procedura.

1. Selezionare il pulsante **immettere manualmente l'account** .  
2. Immettere il codice e l'URL specificato nella stessa pagina che mostra il codice a barre. Queste informazioni sono visualizzate nelle caselle **codice** e **l'URL** in app per dispositivi mobili.

    ![Programma di installazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Al termine dell'attivazione, selezionare **Contatto**. Questo passaggio invia una notifica o un codice di verifica al telefono. Selezionare **verifica**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utilizzare il telefono cellulare come il metodo di contatto

1. Selezionare **L'autenticazione telefono** dall'elenco a discesa.  

    ![Programma di installazione](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Scegliere il proprio paese dall'elenco a discesa e immettere il numero di telefono cellulare.
3. Selezionare il metodo che si preferisce utilizzare con il proprio telefono cellulare - chiamata o testo.
4. Selezionare **Contact me** per verificare il numero di telefono. A seconda della modalità che è selezionata, è una forma o chiamare. Seguire le istruzioni visualizzate sullo schermo, quindi selezionare **verifica**.
5. A questo punto viene richiesto di configurare la password dell'app per le applicazioni non browser, ad esempio Outlook 2010 o versioni precedenti o app di posta elettronica nativo sul dispositivo Apple. Ciò avviene perché alcune App non supporta la verifica in due passaggi. Se non si utilizza queste App, fare clic su **Chiudi** e ignorare il resto dei passaggi.
6. Se si utilizza queste App, copiare la password di app disponibili e incollarlo nell'applicazione anziché la password regolare. È possibile utilizzare la stessa password app per più applicazioni. Per ulteriori informazioni, [agevolare la password dell'app].
7. Fare clic su **Chiudi**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Usare il telefono dell'ufficio come il metodo di contatto

1. Selezionare **Telefono dell'ufficio** dall'elenco a discesa  

    ![Programma di installazione](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. Casella numero di telefono viene compilata automaticamente con le informazioni di contatto della società. Se il numero è errato o mancante, chiedere all'amministratore per apportare modifiche.
4. **Contatti personali** per verificare il numero di telefono, e si chiama il numero. Seguire le istruzioni visualizzate sullo schermo, quindi selezionare **verifica**.
5. A questo punto viene richiesto di configurare la password dell'app per le applicazioni non browser, ad esempio Outlook 2010 o versioni precedenti o app di posta elettronica nativo sul dispositivo Apple. Ciò avviene perché alcune App non supporta la verifica in due passaggi. Se non si utilizza queste App, fare clic su **Chiudi** e ignorare il resto dei passaggi.
6. Se si utilizza queste App, copiare la password di app disponibili e incollarlo nell'applicazione anziché la password regolare. È possibile utilizzare la stessa password app per più applicazioni. Per ulteriori informazioni, vedere [quali sono le password di App](multi-factor-authentication-end-user-app-passwords.md).
7. Fare clic su **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- Cambiare le opzioni preferite e [gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)
- Impostare la [password dell'app](multi-factor-authentication-end-user-app-passwords.md) dell'App per dispositivi nativi che non supportano la verifica in due passaggi.
- Estrarre l' [app Microsoft autenticatore](multi-factor-authentication-microsoft-authenticator.md) per l'autenticazione veloce, protetta anche quando non si dispone del servizio di cella.
