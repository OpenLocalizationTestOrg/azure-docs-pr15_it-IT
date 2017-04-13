
1. Nel [portale di Azure](https://portal.azure.com/), fare clic su **Esplora tutto** > **Servizi App** > il back-end App Mobile. In **Impostazioni**, fare clic su **App servizio Push**e quindi fare clic sul nome di hub di notifica.

2. Passare a **Windows (WNS)**, immettere la **chiave di protezione** (segreto del client) e il **Pacchetto SID** ottenuto dal sito di servizi di Live, quindi fare clic su **Salva**.

    ![Impostare la chiave WNS nel portale](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Il back-end è ora configurato per l'utilizzo di WNS per inviare le notifiche push.
