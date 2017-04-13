
+ **Back-end .NET (c#)**:    
    1. In Visual Studio, fare clic sul progetto server e fare clic su **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs`, quindi fare clic su **Installa**. Consente di installare la raccolta di hub di notifica per l'invio di notifiche dal back-end.

    2. Nel progetto di Visual Studio di back-end, aprire **controller** > **TodoItemController.cs**. Nella parte superiore del file, aggiungere quanto segue `using` istruzione:

            using Microsoft.Azure.Mobile.Server.Config;
            using Microsoft.Azure.NotificationHubs;


    3. Sostituire il `PostTodoItem` metodo con il codice seguente:  
      
            public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
            {
                TodoItem current = await InsertAsync(item);
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
    
                // iOS payload
                var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";
    
                try
                {
                    // Send the push notification and log the results.
                    var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
    
                    // Write the success result to the logs.
                    config.Services.GetTraceWriter().Info(result.State.ToString());
                }
                catch (System.Exception ex)
                {
                    // Write the failure result to the logs.
                    config.Services.GetTraceWriter()
                        .Error(ex.Message, null, "Push.SendAsync Error");
                }
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            }

    4. Pubblicare di nuovo il progetto server.

+ **Back-end Node** : 
   
    1. Se è ancora stato già fatto, [scaricare la Guida introduttiva project](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilizzare l' [editor online nel portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor). 
    
    2. Sostituire lo script di tabella todoitem.js con il codice seguente:


            var azureMobileApps = require('azure-mobile-apps'),
                promises = require('azure-mobile-apps/src/utilities/promises'),
                logger = require('azure-mobile-apps/src/logger');
            
            var table = azureMobileApps.table();
            
            // When adding record, send a push notification via APNS
            table.insert(function (context) {
                // For details of the Notification Hubs JavaScript SDK, 
                // see http://aka.ms/nodejshubs
                logger.info('Running TodoItem.insert');
                
                // Create a payload that contains the new item Text.
                var payload = "{\"aps\":{\"alert\":\"" + context.item.text + "\"}}";
                
                // Execute the insert; Push as a post-execute action when results are returned as a Promise.
                return context.execute()
                    .then(function (results) {
                        // Only do the push if configured
                        if (context.push) {
                            context.push.apns.send(null, payload, function (error) {
                                if (error) {
                                    logger.error('Error while sending push notification: ', error);
                                } else {
                                    logger.info('Push notification sent successfully!');
                                }
                            });
                        }
                        return results;
                    })
                    .catch(function (error) {
                        logger.error('Error while running context.execute: ', error);
                    });
            });
            
            module.exports = table;

    2. Quando si modifica il file nel computer locale, ripubblicare project server.
