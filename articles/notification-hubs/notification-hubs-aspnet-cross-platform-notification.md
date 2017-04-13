<properties
    pageTitle="Inviare notifiche multipiattaforma agli utenti con notifica hub (ASP.NET)"
    description="Informazioni su come usare i modelli di notifica hub per inviare, in una singola richiesta una notifica indipendente dalla piattaforma che tutte le piattaforme."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Inviare notifiche multipiattaforma agli utenti con gli hub di notifica


Nell'esercitazione precedente [notifica agli utenti con gli hub di notifica], si appreso come notifiche push per tutti i dispositivi registrati da uno specifico utente autenticato. In tale esercitazione più richieste erano necessario inviare una notifica a ogni piattaforma client supportati. Notifica hub supporta modelli, che consentono di specificare come un dispositivo specifico desidera ricevere notifiche. Semplifica l'invio di notifiche multipiattaforma. In questo argomento viene illustrato come sfruttare i modelli per inviare una sola richiesta una notifica indipendente dalla piattaforma che tutte le piattaforme. Per ulteriori informazioni sui modelli, vedere [Panoramica di Azure notifica hub][Templates].

> [AZURE.NOTE] Notifica hub consente a un dispositivo registrare più modelli con lo stesso tag. In questo caso, un messaggio di posta in arrivo destinati tale tag genera più notifiche recapitate al dispositivo, uno per ogni modello. In questo modo è possibile visualizzare lo stesso messaggio più notifiche visive, ad esempio entrambe come un badge e come una notifica di tipo avviso popup in un'app di Windows Store.

Completare la procedura seguente per l'invio di notifiche multipiattaforma utilizzando modelli:

1. In Esplora soluzioni in Visual Studio, espandere la cartella **controller** , quindi aprire il file RegisterController.cs.

2. Individuare il blocco di codice nel metodo di **Post** che crea una nuova Sostituisci di registrazione di `switch` contenuto con il codice seguente:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Questo codice chiama il metodo specifico della piattaforma per creare una registrazione modello anziché una registrazione nativa. Non è necessario modificare le registrazioni esistenti poiché le registrazioni modello derivano da registrazioni native.

3. In controller di **notifiche** , sostituire il metodo **sendNotification** con il codice seguente:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Questo codice invia una notifica a tutte le piattaforme nello stesso momento e senza dover specificare un payload nativo. Notifica hub consente di creare e recapita payload corretto a tutti i dispositivi con il valore di fornito _tag_ , come specificato nei modelli registrati.

4. Pubblicare di nuovo il progetto di back-end WebApi.

5. Rieseguire l'applicazione client e verificare che la registrazione ha avuto esito positivo.

6. (Facoltativo) Distribuire l'applicazione client in un secondo dispositivo, quindi eseguire l'app.

    Si noti che viene visualizzata una notifica ogni dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata completata questa esercitazione, è possibile trovare ulteriori informazioni sull'hub di notifica e modelli in questi argomenti:

+ **[Utilizzare gli hub di notifica per inviare ultime notizie]** <br/>Viene illustrato un altro scenario per l'uso di modelli

+  **[Panoramica di hub notifica Azure][Templates]**<br/>Panoramica sulla ha più informazioni dettagliate sui modelli.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Utilizzare gli hub di notifica per inviare ultime notizie]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Informare gli utenti con gli hub di notifica]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
