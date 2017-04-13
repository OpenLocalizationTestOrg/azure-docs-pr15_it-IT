<properties
    pageTitle="Aggiungere le notifiche push per l'app universale Windows piattaforma (UWP) | Azure App per dispositivi mobili"
    description="Informazioni su come utilizzare App Mobile servizio App di Azure e Azure notifica hub per inviare le notifiche push all'app universale Windows piattaforma (UWP)."
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>Aggiungere le notifiche push per l'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione, aggiungere le notifiche push per il progetto di [avvio veloce di Windows](app-service-mobile-windows-store-dotnet-get-started.md) in modo che una notifica push viene inviata al dispositivo ogni volta che viene inserito un record.

Se non si utilizza il progetto di server introduttive scaricato, sarà necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="configure-hub"></a>Configurare un Hub di notifica

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push

È necessario inviare l'app di Windows Store, quindi configurare il progetto server per l'integrazione con Windows notifica servizi (WNS) per inviare push.

1. In Esplora soluzioni Visual Studio, fare clic sul progetto app UWP, fare clic su **Store** > **Associare App con l'archivio...**. 

    ![Associare app di Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. Nella procedura guidata, fare clic su **Avanti**, accedere con l'account Microsoft, digitare un nome per l'app riserva **un nuovo nome app**, quindi fare clic su **riserva**.

3. Dopo la registrazione di app è stata creata, selezionare il nuovo nome app, fare clic su **Avanti**e quindi fare clic su **Associa**. Le informazioni di registrazione di Windows Store richieste verrà aggiunto al manifesto dell'applicazione.  

7. Passare al [Centro per sviluppatori di Windows](https://dev.windows.com/en-us/overview), effettuare l'accesso con l'account Microsoft, fare clic su nuova registrazione di app **nell'App**, quindi espandere **servizi** > **notifiche Push**. 

8. Nella pagina delle **notifiche Push** , fare clic su **sito Live Services** in **Microsoft Azure Mobile Services**.

9. Nella pagina di registrazione, prendere nota del valore in **informazioni riservate applicazione** e il **Pacchetto SID**, che verrà utilizzato Avanti per configurare il back-end app per dispositivi mobili. 

    ![Associare app di Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] Il segreto del client e SID sono le credenziali di sicurezza importanti. Non condividere questi valori con chiunque o distribuirle con l'app. **Id applicazione** viene utilizzata con il segreto per configurare l'autenticazione dell'Account Microsoft.

##<a name="configure-the-backend-to-send-push-notifications"></a>Configurare back-end per inviare le notifiche push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>Aggiornare il server per l'invio di notifiche push

Utilizzare la procedura corrispondente al tipo di progetto back-end&mdash; [.NET back-end](#dotnet) o [back-end Node](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET

1. In Visual Studio, fare clic sul progetto server e fare clic su **Gestisci pacchetti NuGet**, cercare Microsoft.Azure.NotificationHubs, quindi fare clic su **Installa**. Consente di installare la libreria di client hub di notifica.

2. Espandere **controller**, aprire TodoItemController.cs e aggiungere le seguenti istruzioni using:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. Metodo **PostTodoItem** , aggiungere il codice seguente dopo la chiamata a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Questo codice indica l'hub di notifica per inviare una notifica push dopo un nuovo elemento di inserimento.

4. Ripubblicare project server.

### <a name="nodejs"></a>Progetto di back-end Node

1. Se è ancora stato già fatto, [scaricare la Guida introduttiva project](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilizzare l' [editor online nel portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Sostituire il codice esistente nel file todoitem.js con le operazioni seguenti:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Invia una notifica di tipo avviso popup WNS che contiene il item.text quando si inserisce un nuovo elemento todo.

2. Quando si modifica il file nel computer locale, ripubblicare project server.

##<a id="update-app"></a>Aggiungere le notifiche push applicazione in uso.

Successivamente, l'app deve eseguire la registrazione per le notifiche push all'avvio. Quando è già stata attivata l'autenticazione, assicurarsi che l'utente segni aggiuntivo prima di provare a eseguire la registrazione per le notifiche push.

1. Aprire il file di progetto **App.xaml.cs** e aggiungere quanto segue `using` istruzioni:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. Nello stesso file, aggiungere la definizione del metodo **InitNotificationsAsync** seguente per la classe **App** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Il codice recupera ChannelURI per l'app da WNS e quindi registra tale ChannelURI con il servizio di App Mobile App.

3. Nella parte superiore del gestore eventi **OnLaunched** in **App.xaml.cs**, aggiungere il modificatore **asincrono** alla definizione del metodo e aggiungere la seguente chiamata per il nuovo metodo di **InitNotificationsAsync** , come illustrato nell'esempio seguente:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    In questo modo che ChannelURI breve registrato ogni volta che viene avviata l'applicazione.

4. Rigenerare il progetto di app UWP. L'app è ora possibile ricevere notifiche di tipo avviso popup.

##<a id="test"></a>Notifiche push di test nell'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Passaggi successivi

Ulteriori informazioni sulle notifiche push:

* [Come utilizzare il client gestito per App Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
Modelli offrono la possibilità di inviare push multipiattaforma e inserisce localizzati. Informazioni su come registrare modelli.

* [Diagnosticare i problemi di notifica push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Esistono diversi motivi perché notifiche scada o non finiscano nei dispositivi. In questo argomento viene illustrato come analizzare e individuare la causa degli errori di notifica push. 

Valutare la possibilità di proseguire con uno dei seguenti esercitazioni:

+ [Aggiungere l'autenticazione applicazione in uso.](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Informazioni su come eseguire l'autenticazione degli utenti dell'app con un provider di identità.

+ [Attivare la sincronizzazione offline per l'app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Informazioni su come aggiungere l'app utilizzando un back-end App Mobile supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

