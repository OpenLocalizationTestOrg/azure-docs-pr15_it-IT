<properties
    pageTitle="Utilizzare gli hub di notifica per inviare ultime notizie (Windows universale)"
    description="Utilizzare gli hub di notifica di Azure con tag per la registrazione per inviare ultime notizie a un'app universale di Windows."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizzare gli hub di notifica per inviare ultime notizie


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare Azure notifica hub per trasmettere le notifiche di news interruzione a un archivio di Windows o un'app di Windows Phone 8.1 (non Silverlight). Se si utilizza Windows Phone 8.1 Silverlight, fare riferimento alla versione [Di Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Al termine, si sarà in grado di eseguire la registrazione per l'interruzione delle categorie di news che si è interessati e ricevere solo le notifiche push per le categorie. Questo scenario è un modello comune per molte applicazioni in cui le notifiche devono essere inviate a gruppi di utenti che hanno precedentemente dichiarati interesse nel loro, ad esempio lettore RSS, App per fan e così via. 

Trasmissione sono abilitati facilmente individuabili perché includono uno o più _tag_ durante la creazione di una registrazione nell'hub notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno registrato per il tag riceverà la notifica. Poiché i contrassegni sono semplicemente stringhe, non è necessario eseguire il provisioning in anticipo. Per ulteriori informazioni sui tag, fare riferimento al [Routing hub di notifica e le espressioni di Tag](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Prerequisiti

In questo argomento si basa sull'app è stato creato in [Guida introduttiva a hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario avere già completato [iniziare con gli hub di notifica][get-started].

##<a name="add-category-selection-to-the-app"></a>Aggiungere una selezione di una categoria all'app

Il primo passaggio consiste nell'aggiungere gli elementi dell'interfaccia utente alla propria pagina principale esistente che consentono all'utente di selezionare le categorie per eseguire la registrazione. Le categorie selezionate da un utente sono archiviate nel dispositivo. Quando si avvia l'app, una registrazione di dispositivo viene creata l'hub di notifica con le categorie selezionate come tag.

1. Aprire il file di progetto MainPage, quindi copiare il codice seguente nell'elemento **griglia** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Fare clic con il pulsante destro del progetto **condivisi** e aggiungere una nuova classe denominata **notifiche**, aggiungere il modificatore **public** alla definizione della classe, quindi aggiungere le seguenti istruzioni **using** per il nuovo file di codice:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copiare il codice seguente sul nuovo corso **notifiche** :

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Questa classe utilizza l'archiviazione locale per archiviare le varie categorie di news del dispositivo per la ricezione. Notare che invece il metodo *RegisterNativeAsync* è chiamato *RegisterTemplateAsync* per eseguire la registrazione per le categorie utilizzando un modello di registrazione. 
    
    È possibile utilizzare un nome per il modello ("simpleWNSTemplateExample"), poiché potrebbe essere necessario eseguire la registrazione più di un modello, ad esempio una per le notifiche di tipo avviso popup e l'altra per le sezioni ed è necessario assegnare un nome per poter aggiornare o eliminarli.

    Si noti che se un dispositivo registra più modelli con lo stesso tag, una destinazione messaggi in arrivo che generano tag più notifiche recapitato al dispositivo (uno per ogni modello). Questo comportamento risulta utile quando lo stesso messaggio logico ha al risultato in più le notifiche visive, ad esempio che mostra un badge e un tipo di avviso popup nelle applicazioni di Windows Store.

    Per ulteriori informazioni sui modelli, vedere [modelli](notification-hubs-templates-cross-platform-push-messages.md).




4. Nel file di progetto App.xaml.cs aggiungere le proprietà seguenti per la classe **App** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Questa proprietà è utilizzata per creare e accedere a un'istanza di **notifiche** .

    Nel codice precedente, sostituire il `<hub name>` e `<connection string with listen access>` segnaposto con il nome di hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuto in precedenza.

    > [AZURE.NOTE] Poiché credenziali distribuiti con un'applicazione client non sono in genere sicura, è necessario distribuire solo chiave per ascoltare l'accesso con l'applicazione client. Ascoltare consente l'accesso non è possibile modificare l'app per eseguire la registrazione per le notifiche, ma le registrazioni esistente e non è possibile inviare notifiche. Accesso completo usata in un servizio di back-end protetta per l'invio di notifiche e modificare le registrazioni esistenti.

5. Nel MainPage.xaml.cs, aggiungere la riga seguente:

        using Windows.UI.Popups;

6. Nel file di progetto MainPage.xaml.cs, aggiungere il metodo seguente:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Questo metodo consente di creare un elenco delle categorie e viene utilizzata la classe **le notifiche** per archiviare l'elenco nell'archivio locale e registrare il tag corrispondente con l'hub di notifica. Quando si modificano le categorie, la registrazione verrà ricreata con le nuove categorie.

L'app sarà in grado di archiviare un insieme di categorie in un archivio locale nel dispositivo e registrare con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie.

##<a name="register-for-notifications"></a>Eseguire la registrazione per le notifiche

Questa procedura registra con l'hub di notifica all'avvio utilizzando le categorie che sono state memorizzate nell'archivio locale.

> [AZURE.NOTE] Poiché il canale URI assegnato dal servizio di notifica di Windows (WNS) può cambiare in qualsiasi momento, è necessario registrare per le notifiche frequentemente evitare errori di notifica. In questo esempio Registra per la notifica ogni volta che l'avvio dell'applicazione. Per le app che vengono eseguite di frequente, più di una volta al giorno, è possibile probabilmente ignorare la registrazione per mantenere la larghezza di banda se meno di un giorno è trascorse registrazione precedente.

1. Aprire il file App.xaml.cs e aggiornare il metodo **InitNotificationsAsync** da utilizzare il `notifications` classe per la sottoscrizione in base alle categorie.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    In questo modo che ogni volta che avvia l'app recupera le categorie dall'archivio locale e le richieste di registeration per queste categorie. Il metodo **InitNotificationsAsync** è stato creato come parte della [Guida introduttiva a hub di notifica] [ get-started] esercitazione.

3. Aggiungere il codice seguente nel file di progetto MainPage.xaml.cs metodo *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

    Consente di aggiornare la pagina principale in base allo stato delle categorie salvate in precedenza.

L'app è stata completata e memorizzazione un gruppo di categorie nell'archivio locale dispositivo usato per eseguire la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie. Si definisce quindi un back-end che possono inviare le notifiche di categoria per questa app.

##<a name="sending-tagged-notifications"></a>Invio di notifiche con tag

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Eseguire l'app e generare le notifiche

1. In Visual Studio, premere F5 per compilare e avviare l'app.

    ![][1]

    Nota che l'app dell'interfaccia utente fornisce un set di interruttori che consente di scegliere le categorie per la sottoscrizione.

2. Attivare gli interruttori uno o più categorie, quindi fare clic su **Sottoscrivi**.

    L'app converte le categorie selezionate in tag e le richieste di una nuova registrazione di dispositivo per i tag selezionati dall'hub notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

    ![][19]

4. Inviare una nuova notifica da back-end in uno dei modi seguenti:

    + **Console app:** avviare l'applicazione console.

    + **Linguaggio/PHP:** eseguire l'app/script.

    Notifiche relative alle categorie selezionate vengono visualizzati come le notifiche di tipo avviso popup.

    ![][14]

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo appreso come trasmettere aggiornate in base alla categoria. Valutare la possibilità di completare una delle seguenti esercitazioni che illustrano altri scenari avanzati hub di notifica:

+ [Utilizzare gli hub di notifica trasmettere notizie localizzati]

    Informazioni su come espandere l'app di news importanti per abilitare l'invio di notifiche localizzate.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Utilizzare gli hub di notifica trasmettere notizie localizzati]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
