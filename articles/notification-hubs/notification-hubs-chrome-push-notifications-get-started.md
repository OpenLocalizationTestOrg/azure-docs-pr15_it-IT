<properties
    pageTitle="Inviare le notifiche push alle App Chrome con gli hub di notifica di Azure | Microsoft Azure"
    description="Informazioni su come usare gli hub di notifica di Azure per inviare le notifiche push a un'App di Chrome."
    services="notification-hubs"
    keywords="notifiche push per dispositivi mobili, le notifiche push, invio di notifiche, chrome le notifiche push"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Inviare le notifiche push alle App Chrome con gli hub di notifica di Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In questo argomento viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push per un'App di Chrome, verrà visualizzato all'interno del contesto del browser Google Chrome. In questa esercitazione si creerà una app Chrome che riceve le notifiche push utilizzando [Google Cloud messaggistica (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

L'esercitazione viene illustrato come segue per attivare le notifiche push:

* [Abilitare Google Cloud messaggistica](#register)
* [Configurare l'hub di notifica](#configure-hub)
* [Connettere l'App Chrome all'hub di notifica](#connect-app)
* [Invia una notifica push per l'App di Chrome](#send)
* [Funzionalità e funzionalità aggiuntive](#next-steps)

>[AZURE.NOTE] Chrome app le notifiche push non sono generiche notifiche nel browser, sono specifici per estensibilità browser modello (per informazioni dettagliate, vedere [Panoramica App Chrome] ). Oltre al browser desktop Chrome App eseguire in un dispositivo mobile (Android e iOS) tramite Apache Cordova. Vedere [Chrome App in un dispositivo Mobile] per altre informazioni.

Configurazione GCM e Azure notifica hub è identica alla configurazione per Android, dal momento che [Google Cloud messaggistica per Chrome] è deprecata e GCM stesso supporta ora dispositivi Android e le istanze di Chrome.

##<a id="register"></a>Abilitare Google Cloud messaggistica

1. Passare al sito Web [Google Cloud Console] , accedere con le credenziali dell'account di Google e quindi fare clic sul pulsante **Crea progetto** . Immettere il **Nome del progetto**appropriato e quindi fare clic sul pulsante **Crea** .

    ![Google Cloud Console - creazione di progetti][1]

2. Prendere nota del **Numero di progetto** nella pagina **progetti** per il progetto appena creata. Utilizzare questo come **ID mittente GCM** nell'App Chrome per eseguire la registrazione con GCM.

    ![Console di Google Cloud - numero di progetto][2]

3. Nel riquadro sinistro fare clic su **API & auth**e quindi scorrere verso il basso e fare clic sull'interruttore per attivare **Google Cloud messaggistica per Android**. Non è necessario abilitare **Google Cloud messaggistica per Chrome**.

    ![Console di Google Cloud - chiave Server][3]

4. Nel riquadro sinistro fare clic su **credenziali** > **Crea nuova chiave** > **Chiave Server** > **Crea**.

    ![Console di Google Cloud - credenziali][4]

5. Prendere nota del server **Chiave dell'API**. Sarà necessario configurare questo l'hub di notifica, per abilitare l'invio di notifiche push GCM.

    ![Console di Google Cloud - chiave dell'API][5]

##<a id="configure-hub"></a>Configurare l'hub di notifica

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. e **l'Impostazioni** , selezionare nella **Notifica di servizi** e quindi su **Google (GCM)**. Immettere la chiave dell'API e salvare.

&emsp;&emsp;![Notifica Azure hub - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Connettere l'App Chrome all'hub di notifica

L'hub di notifica è ora configurato per l'utilizzo con GCM e disporre le stringhe di connessione per registrare l'app per ricevere e inviare le notifiche push. LK

###<a name="create-a-new-chrome-app"></a>Creare una nuova App Chrome

Nell'esempio seguente si basa [Chrome App GCM campione] e utilizza il modo consigliato per creare un'App di Chrome. Verranno evidenziati i passaggi specificamente correlati a un hub di notifica di Azure. 

>[AZURE.NOTE] È consigliabile scaricare l'origine di questa App Chrome [Chrome App notifica Hub campione].

È possibile utilizzare qualsiasi editor la preferenza per la sua creazione App Chrome viene creato tramite JavaScript. Di seguito è risulterà questa App Chrome.

![App Google Chrome][15]

1. Creare una cartella e denominarla `ChromePushApp`. Naturalmente, il nome non autorizzato: se si denominarlo qualcosa di diverso, verificare che si sostituisce il percorso tra i segmenti di codice necessari.

2. Scaricare la [raccolta crypto js] nella cartella creata nel secondo passaggio. Questa cartella libreria conterrà due sottocartelle: `components` e `rollups`.

3. Creare un `manifest.json` file. Tutte le app Chrome sono supportate da un file manifesto che contiene i metadati di app e più importante, tutte le autorizzazioni concesse all'app quando l'utente lo installa.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Avviso di `permissions` elemento, che specifica che questa App Chrome sarà in grado di ricevere notifiche push da GCM. È necessario specificare anche l'URI hub di notifica Azure in cui l'App Chrome effettua una chiamata resto per registrare.
    L'applicazione di esempio utilizza anche un file icona `gcm_128.png`, che troverà nell'origine che viene riutilizzata dall'esempio GCM originale. È possibile sostituirlo per le immagini che soddisfa i [criteri icona](https://developer.chrome.com/apps/manifest/icons).

4. Creare un file denominato `background.js` con il codice seguente:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Si tratta del file che appare la finestra di Chrome App HTML (**register.html**) e anche definisce il gestore **messageReceived** per gestire la notifica push in arrivo.

5. Creare un file denominato `register.html` -definisce l'interfaccia utente della finestra dell'App Chrome. 

   >[AZURE.NOTE] In questo esempio Usa **CryptoJS v3.1.2**. Se è stata scaricata un'altra versione della raccolta, verificare che si sostituisce correttamente la versione nel `src` percorso.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Creare un file denominato `register.js` con il codice riportato di seguito. Questo file specifica lo script dietro `register.html`. Chrome App non consentire l'esecuzione all'interno del testo, è necessario creare uno script di backup separati per l'interfaccia utente.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    Lo script indicato è i seguenti parametri chiavi:
    - **Window. OnLoad** definisce gli eventi clic sul pulsante dei due pulsanti dell'interfaccia utente. Uno registra con GCM e l'altro utilizza l'ID di registrazione che viene restituito dopo la registrazione con GCM per registrare con gli hub di notifica di Azure.
    - **Registro aggiornamento** è la funzione che consente di gestire funzionalità di registrazione semplice.
    - **registerWithGCM** è il primo gestore clic sul pulsante, semplificando così il `chrome.gcm.register` chiamata GCM per registrare l'istanza corrente di Chrome App.
    - **registerCallback** è la funzione di callback che viene chiamata quando la chiamata di registrazione GCM restituisce.
    - **registerWithNH** è il secondo gestore clic sul pulsante, che registra con gli hub di notifica. Ottiene `hubName` e `connectionString` (che l'utente ha specificato) e presenta la chiamata all'API REST di registrazione hub di notifica.
    - **splitConnectionString** e **generateSaSToken** sono helper che rappresentano l'implementazione JavaScript di un processo di creazione token SA, che deve essere utilizzato in tutte le chiamate API REST. Per ulteriori informazioni, vedere [Concetti comuni](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** è la funzione che effettua un resto HTTP chiamata a un hub di notifica di Azure.
    - **registrationPayload** definisce alle informazioni di registrazione XML. Per ulteriori informazioni, vedere [Creare API REST registrazione NH]. Si aggiorna l'ID di registrazione al suo interno con abbiamo ricevuto da GCM.
    - **client** è un'istanza di **XMLHttpRequest** usato per effettuare la richiesta HTTP POST. Si noti che viene aggiornata la `Authorization` intestazione con `sasToken`. Completamento di questa chiamata verrà registrato questa istanza App Chrome con gli hub di notifica di Azure.


La struttura delle cartelle complessivo per il progetto dovrebbe essere simile a questo:     ![Google Chrome App: struttura delle cartelle][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurare e testare l'applicazione di Chrome

1. Aprire il browser Chrome. Aprire **le estensioni Chrome** e abilitare la **modalità di sviluppo**.

    ![Google Chrome - Abilita modalità di sviluppo][16]

2. Fare clic su **Carica estensione decompressi** e passare alla cartella in cui è stato creato i file. È possibile usare anche **Chrome app & estensioni sviluppo strumento**. Questo strumento è un'App di Chrome sé (installato dall'archivio Web Chrome) e offre funzionalità avanzate di debug per lo sviluppo di App Chrome.

    ![Google Chrome - caricare l'estensione decompressi][17]

3. Se l'App Chrome viene creata senza errori, quindi verrà visualizzata l'App Chrome visualizzate.

    ![Google Chrome - Chrome App visualizzazione][18]

4. Immettere il **Numero di progetto** che è stato creato in precedenza da **Google Cloud Console** come l'ID del mittente e fare clic su **Registra con GCM**. Visualizzazione del messaggio **registrazione con GCM ha avuto esito positivo.**

    ![Google Chrome - Chrome App personalizzazione][19]

5. Immettere il **Nome di Hub di notifica** e **DefaultListenSharedAccessSignature** ottenuto dal portale di versioni precedenti e fare clic su **Registra con Azure notifica Hub**. Visualizzazione del messaggio **notifica Hub registrazione completata!** e i dettagli di risposta di registrazione che contiene la registrazione di Azure notifica hub ID.

    ![Google Chrome - specificare i dettagli di Hub di notifica][20]  

##<a name="send"></a>Invia una notifica all'App Chrome

Scopo di verifica, viene inviato le notifiche push Chrome utilizzando un .NET applicazione console. 

>[AZURE.NOTE] È possibile inviare le notifiche push con gli hub di notifica da qualsiasi back-end tramite l' <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>pubblico. Estrarre il [portale documentazione](https://azure.microsoft.com/documentation/services/notification-hubs/) per altri esempi multipiattaforma.

1. In Visual Studio, selezionare **Nuovo** e quindi su **progetto**dal menu **File** . In **Visual c#**, fare clic su **Windows** e **Applicazione Console**e quindi fare clic su **OK**.  In questo modo si crea un nuovo progetto applicazione console.

2. Dal menu **Strumenti** fare clic su **Gestione pacchetti libreria** e quindi **Console di gestione pacchetti**. Verrà visualizzata la Console di gestione pacchetti.

3. Nella finestra della console, eseguire il comando seguente:

        Install-Package Microsoft.Azure.NotificationHubs

    Aggiungere un riferimento a Azure Service Bus SDK con il <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacchetto WindowsAzure.ServiceBus NuGet</a>.

4. Apri `Program.cs` e aggiungere le seguenti `using` istruzione:

        using Microsoft.Azure.NotificationHubs;

5. Nel `Program` per la classe, aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Assicurarsi di sostituire il `<hub name>` segnaposto con il nome dell'hub notifica che viene visualizzata nel [portale](https://portal.azure.com) in blade l'Hub di notifica. Inoltre, sostituire il segnaposto di stringa di connessione con la stringa di connessione denominata `DefaultFullSharedAccessSignature` ottenuto nella sezione configurazione hub notifica.

    >[AZURE.NOTE] Assicurarsi di usare la stringa di connessione con accesso **completo** , access non **ascolto** . La stringa di connessione di accesso **ascoltare** non concedere autorizzazioni per l'invio di notifiche push.

5. Aggiungere le chiamate in seguito il `Main` metodo:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Assicurarsi che sia in esecuzione Chrome ed eseguire l'applicazione console.

7. Verrà visualizzata la seguente notifica popup sul desktop.

    ![Google Chrome - notifica][13]

8. È inoltre possibile visualizzare tutte le notifiche tramite la finestra notifiche Chrome sulla barra delle applicazioni (in Windows) durante l'esecuzione Chrome.

    ![Google Chrome - elenco di notifiche][14]

>[AZURE.NOTE] Non è necessario disporre l'esecuzione di Chrome App o aprire nel browser (anche se il browser Chrome stesso deve essere in esecuzione). È inoltre possibile ottenere una visualizzazione consolidata di tutte le notifiche nella finestra notifiche Chrome.

## <a name="next-steps"> </a>Passaggi successivi

Altre informazioni sull'hub di notifica nella [Notifica hub Panoramica].

Per utenti specifici di destinazione, fare riferimento all'esercitazione [Azure notifica hub notifica utenti] . 

Se si vuole segmento gli utenti dai gruppi di interesse, è possibile eseguire l'esercitazione [Hub di notifica di Azure le ultime notizie] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Esempio di Chrome App notifica Hub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Console di Google Cloud]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Panoramica di hub di notifica]: notification-hubs-push-notification-overview.md
[Panoramica di App Chrome]: https://developer.chrome.com/apps/about_apps
[Esempio di App GCM Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome App in un dispositivo Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Creare API REST NH di registrazione]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[raccolta di Crypto js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud messaggistica per Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notifica Azure hub informare gli utenti]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure notifica hub ultime notizie]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
