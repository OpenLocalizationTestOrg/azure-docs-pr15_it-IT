<properties
    pageTitle="Utilizzare gli hub di notifica per inviare ultime notizie (Windows Phone)"
    description="Hub di notifica di Azure consente di utilizzare tag per le registrazioni per inviare ultime notizie a un'app di Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizzare gli hub di notifica per inviare ultime notizie

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare Azure notifica hub per trasmettere le notifiche di news importanti per un'app di Windows Phone 8.0/8.1 Silverlight. Se la destinazione di Windows Store o app di Windows Phone 8.1, consultare la versione [Universale di Windows](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Al termine, si sarà in grado di eseguire la registrazione per l'interruzione delle categorie di news che si è interessati e ricevere solo le notifiche push per le categorie. Questo scenario è un modello comune per molte applicazioni in cui le notifiche devono essere inviate a gruppi di utenti che hanno precedentemente dichiarati interesse essi, ad esempio lettore RSS, App per fan e così via.

Trasmissione sono abilitati facilmente individuabili perché includono uno o più _tag_ durante la creazione di una registrazione nell'hub notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno registrato per il tag riceverà la notifica. Poiché i contrassegni sono semplicemente stringhe, non è necessario eseguire il provisioning in anticipo. Per ulteriori informazioni sui tag, fare riferimento al [Routing hub di notifica e le espressioni di Tag](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Prerequisiti

In questo argomento si basa sull'app che è stato creato in [Guida introduttiva a hub di notifica]. Prima di iniziare questa esercitazione, è necessario avere già completato [iniziare con gli hub di notifica].

##<a name="add-category-selection-to-the-app"></a>Aggiungere una selezione di una categoria all'app

Il primo passaggio consiste nell'aggiungere gli elementi dell'interfaccia utente alla propria pagina principale esistente che consentono all'utente di selezionare le categorie per eseguire la registrazione. Le categorie selezionate da un utente sono archiviate nel dispositivo. Quando si avvia l'app, una registrazione di dispositivo viene creata l'hub di notifica con le categorie selezionate come tag.

1. Aprire il file di progetto MainPage e quindi sostituire gli elementi di **griglia** denominati `TitlePanel` e `ContentPanel` con il codice seguente:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Nel progetto, creare una nuova classe denominata **notifiche**, aggiungere il modificatore **public** alla definizione della classe, quindi aggiungere le seguenti istruzioni **utilizzando** il nuovo file di codice:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copiare il codice seguente sul nuovo corso **notifiche** :

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Questa classe utilizza l'archiviazione isolata per archiviare le varie categorie di news che riceverà il dispositivo. Sono inoltre disponibili metodi per eseguire la registrazione per queste categorie utilizzando una registrazione di notifica di [modello](notification-hubs-templates-cross-platform-push-messages.md) .


4. Nel file di progetto App.xaml.cs aggiungere le proprietà seguenti per la classe **App** . Sostituire il `<hub name>` e `<connection string with listen access>` segnaposto con il nome di hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuto in precedenza.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Poiché credenziali distribuiti con un'applicazione client non sono in genere sicura, è necessario distribuire solo chiave per ascoltare l'accesso con l'applicazione client. Ascoltare consente l'accesso non è possibile modificare l'app per eseguire la registrazione per le notifiche, ma le registrazioni esistente e non è possibile inviare notifiche. Accesso completo usata in un servizio di back-end protetta per l'invio di notifiche e modificare le registrazioni esistenti.

5. Nel MainPage.xaml.cs, aggiungere la riga seguente:

        using Windows.UI.Popups;

6. Nel file di progetto MainPage.xaml.cs, aggiungere il metodo seguente:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Questo metodo consente di creare un elenco delle categorie e viene utilizzata la classe **le notifiche** per archiviare l'elenco nell'archivio locale e registrare il tag corrispondente con l'hub di notifica. Quando si modificano le categorie, la registrazione verrà ricreata con le nuove categorie.

L'app sarà in grado di archiviare un insieme di categorie in un archivio locale nel dispositivo e registrare con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie.

##<a name="register-for-notifications"></a>Eseguire la registrazione per le notifiche

Questa procedura registra con l'hub di notifica all'avvio utilizzando le categorie che sono state memorizzate nell'archivio locale.

> [AZURE.NOTE] Poiché il canale URI assegnato da Microsoft Push Notification Service (MPNS) può cambiare in qualsiasi momento, è necessario registrare per le notifiche frequentemente evitare errori di notifica. In questo esempio Registra per le notifiche ogni volta che viene avviata l'app. Per le app che vengono eseguite di frequente, più di una volta al giorno, è possibile probabilmente ignorare la registrazione per mantenere la larghezza di banda se meno di un giorno è trascorse registrazione precedente.


1. Aprire il file App.xaml.cs e aggiungere il modificatore **asincrono** al metodo **Application_Launching** e sostituire il codice di registrazione hub di notifica che è stato aggiunto in [Guida introduttiva a hub di notifica] tramite il codice seguente:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    In questo modo che ogni volta che avvia l'app recupera le categorie dall'archivio locale e le richieste di registrazione per queste categorie.

2. Nel file di progetto MainPage.xaml.cs, aggiungere il codice seguente di implementazione il metodo **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }

    Consente di aggiornare la pagina principale in base allo stato delle categorie salvate in precedenza.

L'app è stata completata e memorizzazione un gruppo di categorie nell'archivio locale dispositivo usato per eseguire la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie. Si definisce quindi un back-end che possono inviare le notifiche di categoria per questa app.

##<a name="sending-tagged-notifications"></a>Invio di notifiche con tag

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Eseguire l'app e generare notifiche

1. In Visual Studio, premere F5 per compilare e avviare l'app.

    ![][1]

    Nota che l'app dell'interfaccia utente fornisce un set di interruttori che consente di scegliere le categorie per la sottoscrizione.

2. Attivare gli interruttori uno o più categorie, quindi fare clic su **Sottoscrivi**.

    L'app converte categorie selezionate in tag e le richieste di una nuova registrazione di dispositivo per i tag selezionati dall'hub notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

    ![][2]

3. Dopo aver ricevuto un messaggio di conferma che le categorie sono state completate sottoscrizione, eseguire l'app console per l'invio di notifiche per ogni categorie. Verificare che viene visualizzata solo una notifica per le categorie di che aver sottoscritto.

    ![][3]

È stata completata in questo argomento.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Guida introduttiva a hub di notifica]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

