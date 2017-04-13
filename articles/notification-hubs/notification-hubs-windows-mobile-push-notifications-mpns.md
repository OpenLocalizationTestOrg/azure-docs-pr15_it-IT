<properties
    pageTitle="Invio di notifiche push con Azure notifica hub di Windows Phone | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per le notifiche push per un'applicazione di Windows Phone 8 o Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="invio di notifiche, invio di notifiche, push di windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Invio di notifiche push con Azure notifica hub di Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

In questa esercitazione viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione di Windows Phone 8 o Windows Phone 8.1 Silverlight. Se si utilizza Windows Phone 8.1 (non Silverlight), fare riferimento alla versione [Universale di Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
In questa esercitazione si crea un'app di Windows Phone 8 vuota che riceve le notifiche push utilizzando Microsoft Push Notification Service (MPNS). Al termine, sarà possibile usare l'hub di notifica per trasmettere le notifiche push per tutti i dispositivi che eseguono l'app.

> [AZURE.NOTE] Notifica hub Windows Phone SDK non supporta l'utilizzo di Windows Push Notification Service (WNS) con le applicazioni di Windows Phone 8.1 Silverlight. Per utilizzare WNS (anziché MPNS) con le applicazioni di Windows Phone 8.1 Silverlight, seguire la [Notifica hub - esercitazione di Windows Phone Silverlight], che utilizza API REST.

L'esercitazione viene illustrato lo scenario di trasmissione semplice con gli hub di notifica.

##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ [Visual Studio 2012 Express per Windows Phone]o versione successiva.

Esercitazione è un prerequisito per tutte le altre esercitazioni hub di notifica per le applicazioni di Windows Phone 8.

##<a name="create-your-notification-hub"></a>Creare l'hub di notifica

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Fare clic sulla sezione <b>Notification Services</b> (in <i>Impostazioni</i>), fare clic su <b>Windows Phone (MPNS)</b> e quindi fare clic su casella di controllo <b>Abilita push non autenticati</b> .</p>
</li>
</ol>

&emsp;&emsp;![Portale di Azure - abilitare le notifiche push non autenticato](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Il tuo fulcro a questo punto viene creato e configurato per l'invio di notifiche non autenticati per Windows Phone.

> [AZURE.NOTE] In questa esercitazione utilizza MPNS in modalità non autenticate. Modalità non autenticate MPNS viene fornito con restrizioni sulle notifiche che è possibile inviare a ogni canale. Notifica hub supporta [MPNS autenticato modalità](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) in modo da caricare il certificato.

##<a name="connecting-your-app-to-the-notification-hub"></a>Connessione dell'applicazione all'hub di notifica

1. In Visual Studio, creare una nuova applicazione di Windows Phone 8.

    ![App di Windows Phone - nuovo progetto - Visual Studio][13]

    In Visual Studio 2013 aggiornamento 2 o versioni successive è invece creare un'applicazione di Windows Phone Silverlight.

    ![Visual Studio - nuovo progetto - vuoto App: Windows Phone Silverlight][11]

2. In Visual Studio, fare doppio clic la soluzione e quindi fare clic su **Gestisci pacchetti NuGet**.

    Verrà visualizzata la finestra di dialogo **Gestisci pacchetti NuGet** .

3. Cercare `WindowsAzure.Messaging.Managed` e fare clic su **installazione**e quindi su accetta le condizioni di utilizzo.

    ![Visual Studio - Gestione pacchetti NuGet][20]

    Questo download, si installa e si aggiunge un riferimento alla libreria di Azure messaggistica per Windows utilizzando il <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacchetto WindowsAzure.Messaging.Managed NuGet</a>.

4. Aprire il file App.xaml.cs e aggiungere quanto segue `using` istruzioni:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Aggiungere il codice seguente nella parte superiore del metodo **Application_Launching** in App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] Il valore **MyPushChannel** è un indice che viene utilizzato per cercare un canale esistente nella raccolta [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . In caso contrario non esiste, creare una nuova voce con lo stesso nome.
    
    Assicurarsi di inserire il nome di un centro di raccolta e la stringa di connessione denominata **DefaultListenSharedAccessSignature** che è stato acquistato nella sezione precedente.
    Il codice recupera l'URI del canale per l'app da MPNS e quindi registra il canale URI con l'hub di notifica. Garantisce inoltre che il canale che URI viene registrata l'hub di notifica ogni volta che viene avviata l'applicazione.

    >[AZURE.NOTE]In questa esercitazione invia una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di sezione, è necessario chiamare invece il metodo **BindToShellTile** sul canale. Per supportare entrambi tipo avviso popup e il riquadro notifiche, chiamare **BindToShellTile** e **BindToShellToast**.

6. In Esplora soluzioni espandere **proprietà**, aprire la `WMAppManifest.xml` file, fare clic sulla scheda **funzionalità** e assicurarsi che sia selezionata la funzionalità **ID_CAP_PUSH_NOTIFICATION** .

    ![Visual Studio - App funzionalità di Windows Phone][14]

    In questo modo che l'app può ricevere notifiche push. In caso contrario, qualsiasi tentativo di inviare una notifica push all'app avrà esito negativo.

7. Premere il `F5` tasto per eseguire l'app.

    Verrà visualizzato un messaggio di registrazione nell'app.

8. Chiudere l'applicazione.  

   >[AZURE.NOTE] Per ricevere una notifica push di tipo avviso popup, l'applicazione non deve essere in esecuzione in primo piano.

##<a name="send-push-notifications-from-your-backend"></a>Inviare le notifiche push dal back-end

È possibile inviare le notifiche push usando gli hub di notifica da qualsiasi back-end tramite l' <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>pubblica. In questa esercitazione, inviare le notifiche push usando un'applicazione console .NET. 

Per informazioni su come inviare le notifiche push da un back-end ASP.NET WebAPI integrata con gli hub di notifica, vedere [Azure notifica hub informare gli utenti che dispongono .NET back-end](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Per informazioni su come inviare le notifiche push tramite le [API REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), vedere [come utilizzare gli hub di notifica da Java](./notification-hubs-java-push-notification-tutorial.md) e [come usare gli hub di notifica da PHP](./notification-hubs-php-push-notification-tutorial.md).

1. Fare doppio clic la soluzione, selezionare **Aggiungi** e **Nuovo progetto**, quindi in **c#**, fare clic su **Windows** e **Applicazione Console**e fare clic su **OK**.

    ![Applicazione Console Visual Studio - nuovo progetto-][6]

    Consente di aggiungere una nuova c# applicazione console alla soluzione. È possibile eseguire questa operazione in una soluzione separata.

4. Fare clic su **Strumenti**, fare clic su **Gestione pacchetti libreria**e quindi fare clic su **Gestione pacchetti Console**.

    Verrà visualizzata la Console di gestione pacchetti.

5.  Nella finestra **Gestione pacchetti Console** set **predefinito di project** per il nuovo progetto applicazione console e quindi nella finestra della console, eseguire il comando seguente:

        Install-Package Microsoft.Azure.NotificationHubs

    Aggiungere un riferimento di Azure notifica hub SDK utilizzando il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto Microsoft.Azure.Notification hub NuGet</a>.

6. Aprire la `Program.cs` file e aggiungere le seguenti `using` istruzione:

        using Microsoft.Azure.NotificationHubs;

6. Nel `Program` per la classe, aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Assicurarsi di sostituire il `<hub name>` segnaposto con il nome dell'hub notifica che viene visualizzata nel portale. Inoltre, sostituire il segnaposto di stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** che è stato acquistato nella sezione "Configurare l'hub di notifica."

    >[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **completo** , access non **ascolto** . Stringa ascolto access non dispone delle autorizzazioni per l'invio di notifiche push.

4. Aggiungere la riga seguente nel `Main` metodo:

         SendNotificationAsync();
         Console.ReadLine();

5. Con l'emulatore di Windows Phone in esecuzione e l'app chiuso, impostare il progetto di applicazione console come progetto di avvio predefinita e quindi premere la `F5` tasto per eseguire l'app.

    Si riceverà una notifica push di tipo avviso popup. Toccare l'intestazione di tipo avviso popup consente di caricare l'app.

È possibile trovare tutti i possibili payload negli argomenti di [tipo avviso popup catalogo] e [riquadro catalogo] su MSDN.

##<a name="next-steps"></a>Passaggi successivi

In questo esempio semplice trasmessa le notifiche push per tutti i dispositivi Windows Phone 8. 

Per impostare la destinazione utenti specifici, fare riferimento all'esercitazione [Utilizzare gli hub di notifica per le notifiche push per gli utenti] . 

Se si vuole segmento gli utenti dai gruppi di interesse, è possibile leggere [Utilizzare gli hub di notifica per inviare ultime notizie]. 

Ulteriori informazioni su come usare gli hub di notifica nella [Notifica hub indicazioni].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notifica hub indicazioni]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utilizzare gli hub di notifica per le notifiche push per gli utenti]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizzare gli hub di notifica per inviare ultime notizie]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catalogo di tipo avviso popup]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[riquadro catalogo]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notifica hub - esercitazione Silverlight Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

