<properties
    pageTitle="Hub di notifica Azure sicuro Push"
    description="Informazioni su come inviare le notifiche push sicura in Azure. Esempi di codice scritti in c# tramite l'API di .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Hub di notifica Azure sicuro Push

> [AZURE.SELECTOR]
- [Universale di Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Panoramica

Supporto di notifica push di Microsoft Azure consente di accedere a un'infrastruttura push di semplice utilizzo, multipiattaforma e scalabilità orizzontale, semplificando notevolmente l'implementazione delle notifiche push per le applicazioni aziendali e per le piattaforme di dispositivi mobili.

A causa di normative o ai vincoli di sicurezza, a volte un'applicazione può decidere di includere nel messaggio di notifica che non è possibile trasmettere attraverso l'infrastruttura di notifica push standard. In questa esercitazione viene illustrato come ottenere la stessa esperienza mediante l'invio di informazioni riservate tramite una connessione protetta e autenticata tra il dispositivo client e back-end app.

Un alto livello, il flusso è:

1. App back-end:
    - Payload sicura archivia nel database back-end.
    - Invia l'ID di questa notifica al dispositivo (nessuna informazione sicura viene inviata).
2. L'app nel dispositivo, quando si riceve la notifica:
    - Il dispositivo dei contatti di back-end richiede payload sicuro.
    - L'app è possibile visualizzare il payload di una notifica sul dispositivo.

È importante tenere presente che nel flusso precedente e in questa esercitazione, si presuppone che il dispositivo memorizza un token di autenticazione in un archivio locale, dopo l'utente accede. In questo modo si garantisce un'esperienza completamente trasparente, mentre il dispositivo è possibile recuperare payload sicura di notifica con questo token. Se l'applicazione non memorizzare i token di autenticazione sul dispositivo o se questi token possono essere scaduti, l'app di dispositivo, dopo aver ricevuto la notifica dovrebbe essere visualizzata una notifica generica che richiede all'utente per avviare l'app. L'app quindi autentica l'utente e Mostra il payload di notifica.

In questa esercitazione sicura Push viene illustrato come inviare una notifica push in modo sicuro. L'esercitazione si basa sui esercitazione [Informare gli utenti](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) in modo che è necessario completare la procedura in tale esercitazione prima di tutto.

> [AZURE.NOTE] In questa esercitazione si presuppone che è stato creato e configurato l'hub di notifica, come descritto nella [Guida introduttiva di notifica hub (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Si noti inoltre che Windows Phone 8.1 richiede le credenziali di Windows (non Windows Phone) e che le attività in background non funzionano in Windows Phone 8.0 o 8.1 Silverlight. Per le applicazioni di Windows Store, è possibile ricevere notifiche tramite un'attività in background solo se l'app è abilitato schermata di blocco (fare clic sulla casella di controllo di Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificare il progetto di Windows Phone

1. Aggiungere il codice seguente nel progetto **NotifyUserWindowsPhone** App.xaml.cs per registrare le attività in background push. Aggiungere la riga di codice seguente alla fine della `OnLaunched()` metodo:

        RegisterBackgroundTask();

2. Sempre in App.xaml.cs, aggiungere il seguente codice immediatamente dopo la `OnLaunched()` metodo:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. Dal menu **File** in Visual Studio, fare clic su **Salva tutto**.

## <a name="create-the-push-background-component"></a>Creare il componente di sfondo Push

Il passaggio successivo consiste nel creare il componente di sfondo push.

1. In Esplora soluzioni rapida il nodo principale della soluzione (**soluzione SecurePush** in questo caso), quindi fare clic su **Aggiungi**, quindi fare clic su **Nuovo progetto**.

2. Espandere **App Store**, quindi fare clic su **App di Windows Phone**, quindi fare clic su **Componente Runtime di Windows (Windows Phone)**. Nome del progetto **PushBackgroundComponent**e quindi fare clic su **OK** per creare il progetto.

    ![][12]

3. In Esplora soluzioni fare clic sul progetto **PushBackgroundComponent (Windows Phone 8.1)** , quindi fare clic su **Aggiungi**, quindi fare clic su **classe**. Denominare la nuova classe **PushBackgroundTask.cs**. Fare clic su **Aggiungi** per generare la classe.

4. Sostituire l'intero contenuto della definizione dello spazio dei nomi di **PushBackgroundComponent** con il codice seguente, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint di back-end ottenuto quando si distribuisce il back-end:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. In Esplora soluzioni fare clic sul progetto **PushBackgroundComponent (Windows Phone 8.1)** e quindi fare clic su **Gestisci pacchetti NuGet**.

6. Sul lato sinistro fare clic su **Online**.

7. Nella casella **Cerca** digitare **Http Client**.

8. Nell'elenco dei risultati fare clic su **Librerie Client HTTP Microsoft**e quindi fare clic su **Installa**. Completare l'installazione.

9. Nella casella NuGet **ricerca** digitare **Json.net**. Installare il pacchetto di **Json.NET** , quindi chiudere la finestra di gestione di pacchetti NuGet.

10. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. In Esplora soluzioni nel progetto **NotifyUserWindowsPhone (Windows Phone 8.1)** rapida **riferimenti**e quindi fare clic su **Aggiungi riferimento**. Nella finestra di dialogo Gestione di riferimento, selezionare la casella accanto a **PushBackgroundComponent**e quindi fare clic su **OK**.

12. In Esplora soluzioni fare doppio clic su **Package.appxmanifest** nel progetto **NotifyUserWindowsPhone (Windows Phone 8.1)** . In **notifiche**, impostare **Dotati di tipo avviso popup** su **Sì**.

    ![][3]

13. Sempre in **Package.appxmanifest**, fare clic sul menu **dichiarazioni** nella parte superiore. Nell'elenco a discesa **Dichiarazioni disponibili** , fare clic su **Attività in Background**e quindi fare clic su **Aggiungi**.

14. In **Package.appxmanifest**, in **proprietà**, selezionare **invio di notifiche**.

15. In **Package.appxmanifest**, in **Impostazioni di App**, digitare **PushBackgroundComponent.PushBackgroundTask** nel campo **Punto di ingresso** .

    ![][13]

16. Dal menu **File** fare clic su **Salva tutto**.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1. In Visual Studio, eseguire l'applicazione di API Web **AppBackend** . Viene visualizzata una pagina web ASP.NET.

2. In Visual Studio, eseguire l'app di Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . Emulatore di Windows Phone esegue e carica automaticamente l'app.

3. Nell'applicazione di **NotifyUserWindowsPhone** dell'interfaccia utente, immettere un nome utente e password. Può trattarsi di qualsiasi stringa, ma devono essere lo stesso valore.

4. Nell'applicazione di **NotifyUserWindowsPhone** dell'interfaccia utente, fare clic su **accedere e registrare**. Fare clic su **Invia push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
