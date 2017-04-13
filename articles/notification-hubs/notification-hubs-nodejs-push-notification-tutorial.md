<properties
    pageTitle="Invio di notifiche push con gli hub di notifica di Azure e Node"
    description="Informazioni su come usare gli hub di notifica per l'invio di notifiche push da un'applicazione di Node."
    keywords="invio di notifiche push notifications,node.js push, push ios"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Invio di notifiche push con gli hub di notifica di Azure e Node
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Panoramica

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Questa guida illustra come inviare le notifiche push con l'aiuto di Azure notifica hub direttamente da un'applicazione di Node. 

Gli scenari coperti includono l'invio di notifiche push alle applicazioni piattaforme seguenti:

* Android
* iOS
* Windows Phone
* Piattaforma Windows universale 

Per ulteriori informazioni sull'hub di notifica, vedere la sezione [Passaggi successivi](#next) .

##<a name="what-are-notification-hubs"></a>Quali sono gli hub di notifica?

Hub di notifica Azure forniscono un'infrastruttura di semplice utilizzo, multipiattaforma e scalabilità per l'invio di notifiche push per dispositivi mobili. Per informazioni dettagliate su dell'infrastruttura del servizio, vedere la pagina di [Azure notifica hub](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Creare un'applicazione Node

Il primo passaggio in questa esercitazione viene creata una nuova applicazione Node vuota. Per istruzioni sulla creazione di un'applicazione Node, vedere [creare e distribuire un'applicazione di Node al sito Web di Microsoft Azure][nodejswebsite], [Servizio Cloud Node] [ Node.js Cloud Service] usando Windows PowerShell, o il [sito Web con WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurare l'applicazione di utilizzare gli hub di notifica

Per utilizzare gli hub di notifica di Azure, è necessario scaricare e usare il Node [pacchetto azure](https://www.npmjs.com/package/azure), che include un set predefinito di librerie di supporto in grado di comunicare con i servizi di resto di notifica push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Consente di ottenere il pacchetto di nodo pacchetto Manager (NPM)

1.  Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **terminale** (Mac) o **Bash** (Linux) e passare alla cartella in cui è stata creata l'applicazione vuota.

2.  Nella finestra di comando, digitare **npm installare azure sb** .

3.  È possibile eseguire manualmente il comando **ls** o **dir** per verificare che un **nodo\_moduli** cartella è stata creata. All'interno di tale cartella, individuare il pacchetto di **azure** , che contiene le librerie che è necessario accedere Hub notifica.

>[AZURE.NOTE] È possibile ottenere ulteriori informazioni sul NPM ufficiale [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 

### <a name="import-the-module"></a>Importare il modulo

Con un editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configura una connessione a Hub di notifica di Azure

L'oggetto **NotificationHubService** consente di lavorare con gli hub di notifica. Il codice seguente crea un oggetto **NotificationHubService** per hub nofication denominato **hubname**. Aggiungere tale record nella parte superiore del file **server.js** dopo l'istruzione per importare il modulo di azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Il valore di **connectionstring** connessione può essere ottenuto dal [Portale di Azure] eseguendo la procedura seguente:

1. Nel riquadro di spostamento sinistra, fare clic su **Sfoglia**.

2. Selezionare **Gli hub di notifica**e quindi cercare hub che si desidera utilizzare per il campione. È possibile fare riferimento all' [esercitazione di Windows Store introduzione](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se occorre assistenza per creare un nuovo Hub di notifica.

3. Selezionare **Impostazioni**.

4. Fare clic su **criteri di accesso**. Verrà visualizzata entrambe le stringhe di connessione di accesso completo e condivise.

![Portale di Azure - hub di notifica](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] È inoltre possibile recuperare la stringa di connessione utilizzando il cmdlet **Get-AzureSbNamespace** fornito da [Azure PowerShell](../powershell-install-configure.md) o il comando **Mostra spazio dei nomi sb azure** con l' [interfaccia di riga di comando Azure Azure CLI ()](../xplat-cli-install.md).

##<a name="general-architecture"></a>Architettura generale

L'oggetto **NotificationHubService** esposti istanze dell'oggetto seguenti per l'invio di notifiche push per applicazioni e dispositivi specifici:

* **Android** - utilizzare l'oggetto **GcmService** , disponibile al **notificationHubService.gcm**
* **iOS** - utilizzare l'oggetto **ApnsService** , è possibile accedere al **notificationHubService.apns**
* **Windows Phone** - utilizzare l'oggetto **MpnsService** , disponibile al **notificationHubService.mpns**
* **Piattaforma Windows universale** - utilizzare l'oggetto **WnsService** , disponibile al **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Procedura: le notifiche push invia alle applicazioni di Android

L'oggetto **GcmService** fornisce un metodo **inviare** che può essere utilizzato per inviare le notifiche push alle applicazioni di Android. Il metodo **send** accetta i parametri seguenti:

* **Tag** - identificatore tag. Se non viene specificato alcun tag, la notifica verrà inviata al client.
* **Payload** - JSON o payload stringa non elaborato il messaggio.
* **Callback** - la funzione di callback.

Per ulteriori informazioni sul formato del carico utile, vedere la sezione **Payload** del documento nel [Server GCM l'implementazione](http://developer.android.com/google/gcm/server.html#payload) .

Il codice seguente utilizza l'istanza di **GcmService** esposto dall' **NotificationHubService** per inviare una notifica push per tutti i client registrati.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Procedura: le notifiche push invia alle applicazioni di iOS

Stesso come per le applicazioni Android descritte in precedenza, l'oggetto **ApnsService** fornisce un metodo **inviare** che può essere utilizzato per inviare le notifiche push alle applicazioni di iOS. Il metodo **send** accetta i parametri seguenti:

* **Tag** - identificatore tag. Se non viene specificato alcun tag, verrà inviata la notifica a tutti i client.
* **Payload** - JSON del messaggio o payload stringa.
* **Callback** - la funzione di callback.

Per ulteriori informazioni il formato del carico utile, vedere la sezione **Notifica Payload** del documento [locale e Guida di programmazione di notifica Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Il codice seguente utilizza l'istanza di **ApnsService** esposto dall' **NotificationHubService** per inviare un messaggio di avviso a tutti i client:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Procedura: le notifiche push invia alle applicazioni di Windows Phone

L'oggetto **MpnsService** fornisce un metodo di **inviare** messaggi che può essere utilizzato per inviare le notifiche push alle applicazioni di Windows Phone. Il metodo **send** accetta i parametri seguenti:

* **Tag** - identificatore tag. Se non viene specificato alcun tag, verrà inviata la notifica a tutti i client.
* **Payload** - payload XML del messaggio.
* **TargetName**  -  `toast` per le notifiche di tipo avviso popup. `token`per le notifiche animate.
* **Classe di notifica** - la priorità del messaggio di notifica. Vedere la sezione di **Elementi di intestazione HTTP** del documento [da un server notifiche Push](http://msdn.microsoft.com/library/hh221551.aspx) per i valori validi.
* **Opzioni** - intestazioni di richiesta facoltativo.
* **Callback** - la funzione di callback.

Per un elenco di opzioni valide **TargetName**, **classe di notifica** e di intestazione, visitare la pagina [da un server notifiche Push](http://msdn.microsoft.com/library/hh221551.aspx) .

Il codice di esempio seguente utilizza l'istanza di **MpnsService** esposto dall' **NotificationHubService** per inviare una notifica push di tipo avviso popup:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Procedura: le notifiche push invia alle applicazioni universale Windows piattaforma (UWP)

L'oggetto **WnsService** fornisce un metodo **inviare** che può essere utilizzato per inviare le notifiche push alle applicazioni di piattaforma Windows universale.  Il metodo **send** accetta i parametri seguenti:

* **Tag** - identificatore tag. Se non viene specificato alcun tag, verrà inviata la notifica a tutti i client registrati.
* **Payload** - il payload del messaggio XML.
* **Tipo** - il tipo di notifica.
* **Opzioni** - intestazioni di richiesta facoltativo.
* **Callback** - la funzione di callback.

Per un elenco dei tipi validi e intestazioni di richiesta, vedere [le intestazioni richiesta e risposta del servizio di notifica Push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Il codice seguente utilizza l'istanza di **WnsService** esposto dall' **NotificationHubService** per inviare una notifica push di tipo avviso popup per un'app UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Passaggi successivi

I frammenti di esempio precedenti consentono di creare facilmente infrastruttura del servizio per la ricezione delle notifiche push una vasta gamma di dispositivi. Dopo aver appreso le nozioni di base dell'uso di notifica hub con Node, seguire questi collegamenti per altre informazioni su come è possibile estendere ulteriormente queste funzionalità.

-   Per [gli hub di notifica Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), vedere il riferimento MSDN.
-   Visitare l'archivio di [Azure SDK per nodo] su GitHub per altri esempi e i dettagli di implementazione.

  [Azure SDK per nodo]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sito Web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portale di Azure]: https://portal.azure.com
