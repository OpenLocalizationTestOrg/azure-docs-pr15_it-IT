In questa sezione, codice viene aggiornato nell'App Mobile back-end progetto esistente per inviare una notifica push ogni volta che viene aggiunto un nuovo elemento. Questa è una tecnologia caratteristica [modello](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) gli hub di notifica, attivazione push multipiattaforma. I vari client registrati per le notifiche push utilizzare i modelli e un singolo push universale accessibile per tutte le piattaforme client.

Scegliere la procedura descritta di seguito corrispondente al tipo di progetto back-end&mdash; [.NET back-end](#dotnet) o [back-end Node](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET
1. In Visual Studio, fare clic sul progetto server e fare clic su **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs`, quindi fare clic su **Installa**. Consente di installare la raccolta di hub di notifica per l'invio di notifiche dal back-end.

3. In project server aprire **controller** > **TodoItemController.cs**e aggiungere le seguenti istruzioni using:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. Metodo **PostTodoItem** , aggiungere il codice seguente dopo la chiamata a **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Invia una notifica di modello che contiene l'elemento. Testo quando si inserisce un nuovo elemento.

4. Pubblicare di nuovo il progetto server. 

### <a name="nodejs"></a>Progetto di back-end Node

1. Se è ancora stato già fatto, [scaricare il progetto di back-end Guida introduttiva](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilizzare l' [editor online nel portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Sostituire il codice esistente in todoitem.js con le operazioni seguenti:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
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

    Si invia una notifica di modello che contiene il item.text quando si inserisce un nuovo elemento.

2. Quando si modifica il file nel computer locale, ripubblicare project server.
