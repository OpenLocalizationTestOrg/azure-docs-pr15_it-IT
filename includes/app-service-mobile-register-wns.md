
1. In Esplora soluzioni Visual Studio, fare clic sul progetto app di Windows Store, fare clic su **Store** > **Associare App con l'archivio...**.

    ![Associare app di Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. Nella procedura guidata, fare clic su **Avanti**, accedere con l'account Microsoft, digitare un nome per l'app riserva **un nuovo nome app**, quindi fare clic su **riserva**.

3. Dopo la registrazione di app è stata creata, selezionare il nuovo nome app, fare clic su **Avanti**e quindi fare clic su **Associa**. Le informazioni di registrazione di Windows Store richieste verrà aggiunto al manifesto dell'applicazione.

7. Ripetere i passaggi 1 e 3 per il progetto di app Store di Windows Phone utilizzando la stessa registrazione creata in precedenza per l'app di Windows Store.  

7. Passare al [Centro per sviluppatori di Windows](https://dev.windows.com/en-us/overview), effettuare l'accesso con l'account Microsoft, fare clic su nuova registrazione di app **nell'App**, quindi espandere **servizi** > **notifiche Push**.

8. Nella pagina delle **notifiche Push** , fare clic su **sito Live Services** in **Windows Push Notification Services (WNS) e le applicazioni di Microsoft Azure Mobile**e prendere nota dei valori del **Pacchetto SID** e il valore *corrente* in **Segreto dell'applicazione**. 

    ![Impostazione di App nell'interfaccia di sviluppo](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] Il segreto di applicazione e pacchetto SID sono le credenziali di sicurezza importanti. Non condividere questi valori con chiunque o distribuirle con l'app.
