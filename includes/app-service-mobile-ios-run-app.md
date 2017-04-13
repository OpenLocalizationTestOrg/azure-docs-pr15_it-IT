
1. Nel Mac, visitare il [Portale di Azure]. Fare clic su **Esplora tutto** > **App Mobile** > back-end appena creata. Le impostazioni di app per dispositivi mobili, fare clic su **Guida introduttiva** > **iOS (obiettivo-C)**. Se si preferisce Swift, fare clic su **Guida introduttiva** > **iOS (Swift)** invece. In **Download ed eseguire il progetto iOS**, fare clic su **Scarica**. Consente di scaricare il progetto Xcode per un'app preconfigurato per connettere il back-end. Aprire il progetto utilizzando Xcode.

2. Fare clic sul pulsante **Esegui** per generare il progetto e avviare l'app in simulatore di iOS.

3. Nell'app digitare testo significativo, ad esempio _completata l'esercitazione_ e quindi fare clic sul segno più (**+**) sull'icona. Si invia una richiesta POST nel back-end Azure distribuito in precedenza. Inserisce i back-end i dati dalla richiesta sono nella tabella TodoItem SQL e restituisce informazioni sugli elementi appena stored tornare all'app per dispositivi mobili. App per dispositivi mobili consente di visualizzare i dati nell'elenco. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portale di Azure]: https://portal.azure.com/
