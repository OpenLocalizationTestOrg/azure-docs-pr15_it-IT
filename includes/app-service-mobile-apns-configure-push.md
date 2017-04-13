

1. Nel Mac, avviare **Accesso Portachiavi**. Aprire **I certificati personali** nella **categoria** sulla barra di navigationn a sinistra. Trovare il certificato SSL che è stato scaricato nella sezione precedente e divulgare il relativo contenuto. Selezionare solo il certificato (non selezionare la chiave privata) ed [esportarle](https://support.apple.com/kb/PH20122?locale=en_US).

2. Nel [portale di Azure](https://portal.azure.com/), fare clic su **Esplora tutto** > **Servizi App** > il back-end App Mobile. In **Impostazioni**, fare clic su **App servizio Push**e quindi fare clic sul nome di hub di notifica. Passare a **Apple Push Notification Services** > **Carica certificato**. Caricare il file p12, selezionare la corretta **modalità** (a seconda che il client SSL certificato da versioni precedenti è produzione o Sandbox). Salvare le modifiche.

Il servizio è ora configurato per lavorare con le notifiche push su iOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
