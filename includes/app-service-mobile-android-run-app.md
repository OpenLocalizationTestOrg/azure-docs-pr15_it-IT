
1. Visitare il [portale di Azure]. Fare clic su **Esplora tutto** > **App Mobile** > back-end appena creata. Le impostazioni di app per dispositivi mobili, fare clic su **Guida introduttiva** > **Android)**. In **Configura l'applicazione client**, fare clic su **Scarica**. Consente di scaricare il progetto Android per un'app preconfigurato per connettere il back-end. 

2. Aprire il progetto di **Studio Android**, mediante **Importa progetto (ADT Eclisse, Gradle ecc.)**. Assicurarsi che effettuare questa selezione Importa per evitare errori JDK.

3. Fare clic sul pulsante **Esegui 'app'** per generare il progetto e avviare l'app in simulatore Android.

4. Nell'app digitare testo significativo, ad esempio _completata l'esercitazione_ e quindi fare clic sul pulsante 'Aggiungi'. Si invia una richiesta POST nel back-end Azure distribuito in precedenza. Inserisce i back-end i dati dalla richiesta sono nella tabella TodoItem SQL e restituisce informazioni sugli elementi appena stored tornare all'app per dispositivi mobili. App per dispositivi mobili consente di visualizzare i dati nell'elenco. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portale di Azure]: https://portal.azure.com/
