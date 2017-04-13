Clienti Azure sbloccare 25.000 email gratuite ogni mese. Questi 25.000 gratuite mensile messaggi di posta elettronica otterranno consente di accedere a report avanzati e analitica e [tutte le API][] (Web, SMTP, eventi, analisi e altro). Per informazioni su altri servizi forniti da SendGrid, vedere la pagina [SendGrid caratteristiche][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Per accedere a un account SendGrid

1. Accedere al [portale di gestione Azure][].

2. Nel riquadro inferiore del portale di gestione, fare clic su **Nuovo**.

    ![comando nuova barra][command-bar-new]

3. Fare clic su **Marketplace**.

    ![archivio sendgrid][sendgrid-store]

4. Nella finestra di dialogo **Scegli un'applicazione e servizio** selezionare **SendGrid** e fare clic sulla freccia a destra.

5. Nella finestra di dialogo **Personalizza applicazioni e servizi** selezionare il piano di **SendGrid** che si desidera eseguire l'accesso a.

6. Immettere un nome per identificare il servizio **SendGrid** nelle impostazioni di Azure o usare il valore predefinito di **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Nomi devono essere compreso tra 1 e 100 caratteri e contenere caratteri di sottolineatura, trattini, punti e solo caratteri alfanumerici. Il nome deve essere univoco nell'elenco degli elementi di archivio di Azure sottoscritto.

    ![archivio-schermo-2][store-screen-2]

7. Scegliere un valore per l'area geografica; ad esempio Ovest US.

8. Fare clic sulla freccia a destra.

9. Nella scheda **Revisione acquisti** , esaminare il piano e informazioni sui prezzi e le condizioni legali. Se si accettano le condizioni, fare clic sul segno di spunta. Dopo aver fatto clic sul segno di spunta, l'account SendGrid inizierà il [processo di provisioning SendGrid].

    ![archivio-schermo-3][store-screen-3]

10. Dopo aver verificato l'acquisto si viene reindirizzati al dashboard di componenti aggiuntivi e verrà visualizzato il messaggio **SendGrid acquisto**.

    ![messaggio acquisto sendgrid][sendgrid-purchasing-message]

    L'account SendGrid viene eseguito il provisioning immediatamente e verrà visualizzato il messaggio **viene eseguito correttamente al acquistato SendGrid componente aggiuntivo**. L'account e le credenziali dell'ora vengono creati. Si è pronti a inviare messaggi di posta elettronica a questo punto. 

    Per modificare il piano di abbonamento o si vede il SendGrid contattare impostazioni, fare clic sul nome del servizio SendGrid per aprire il dashboard SendGrid Marketplace. 

    ![sendgrid-aggiungere-su-dashboard][sendgrid-add-on-dashboard]

    Per inviare un messaggio di posta elettronica utilizzando SendGrid, è necessario specificare le credenziali dell'account (nome utente e password).

### <a name="to-find-your-sendgrid-credentials"></a>Per trovare le credenziali SendGrid ###

1. Fare clic su **informazioni di connessione**.

    ![pulsante informazioni di connessione sendgrid][sendgrid-connection-info-button]

2. Nella finestra di dialogo *informazioni di connessione* , copiare nome utente e **Password** per utilizzare più avanti in questa esercitazione.

    ![informazioni di connessione sendgrid][sendgrid-connection-info]

    Per configurare la posta elettronica le impostazioni di distribuzione, fare clic sul pulsante **Gestisci** . Si verrà reindirizzati al pannello di controllo SendGrid. 

    ![Pannello di controllo sendgrid][sendgrid-control-panel]

    Per ulteriori informazioni sulla Guida introduttiva di SendGrid, vedere [Guida introduttiva SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Caratteristiche SendGrid]: http://sendgrid.com/features
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Guida introduttiva SendGrid]: http://sendgrid.com/docs
[Processo di Provisioning SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[tutte le API]: https://sendgrid.com/docs/API_Reference/index.html

