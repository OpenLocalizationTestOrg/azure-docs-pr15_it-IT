<properties
    pageTitle="Notifica hub localizzate ultime notizie esercitazione"
    description="Informazioni su come utilizzare Azure notifica hub per inviare notifiche di news interruzione localizzati."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Utilizzare gli hub di notifica per inviare notizie localizzati

> [AZURE.SELECTOR]
- [C# di Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare la funzionalità di **modello** di Azure notifica hub per trasmettere le notifiche di news importanti che sono state localizzate, lingua e dispositivo. In questa esercitazione si inizierà con l'app di Windows Store creato in [Utilizzare gli hub di notifica per inviare ultime notizie]. Al termine, che sarà possibile eseguire la registrazione per le categorie che si desidera, specificare una lingua in cui ricevere le notifiche e ricevere solo le notifiche push per le categorie selezionate in tale lingua.


Esistono due parti a questo scenario:

- app di Windows Store consente ai client dispositivi per specificare una lingua e per l'iscrizione a categorie di news interruzione diverso.

- back-end trasmette le notifiche tramite la funzionalità di **tag** e un **modello** di Azure notifica hub.



##<a name="prerequisites"></a>Prerequisiti

È necessario avere già completato l'esercitazione di [Utilizzare gli hub di notifica per inviare ultime notizie] e di avere il codice disponibile, perché questa esercitazione si basa direttamente su tale codice.

Occorre inoltre Visual Studio 2012 o versione successiva.


##<a name="template-concepts"></a>Concetti relativi ai modelli

Nella [Notifica di utilizzare gli hub per inviare ultime notizie] è creata un'app utilizzati **tag** sottoscrivere le notifiche per le categorie di news diversi.
Molte applicazioni, tuttavia, più mercati di destinazione e richiedono localizzazione. Ciò significa che il contenuto delle notifiche sole necessario localizzate e recapitato per il set di dispositivi corretto.
In questo argomento verrà illustrato come utilizzare la funzionalità di **modello** di notifica hub di fornire facilmente le notifiche di news interruzione localizzati.

Nota: è possibile inviare notifiche localizzate consiste nel creare più versioni di ogni tag. Ad esempio, per supportare inglese, francese e Mandarino, sono necessari tre tag diversi per essere sempre informati world: "world_en", "world_fr" e "world_ch". È quindi necessario inviare una versione localizzata di news world a ciascun tag. In questo argomento vengono utilizzati modelli per evitare la proliferazione di tag e il requisito di invio di messaggi di più.

Un alto livello, i modelli sono un modo per specificare come un dispositivo specifico deve ricevere una notifica. Il modello specifica il formato del carico utile esatta facendo riferimento a proprietà che fanno parte del messaggio inviato tramite l'app back-end. In questo caso, si verrà inviato un messaggio di impostazioni locali indipendente contenente tutte le lingue supportate:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Quindi abbiamo garantisce che i dispositivi registrato con un modello che fa riferimento a proprietà corretta. Ad esempio un'app di Windows Store che desidera ricevere un messaggio di tipo avviso popup semplice verrà registrato per il modello con i corrispondenti tag seguente:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



I modelli sono una potente caratteristica che ottenere ulteriori informazioni nell'articolo [modelli](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>Interfaccia utente di app

A questo punto si modificherà l'app ultime notizie creata nell'argomento [Utilizzare gli hub di notifica per inviare ultime notizie] inviare notizie localizzati uso dei modelli.

Nell'app di Windows Store:

Modificare il MainPage per includere una casella combinata impostazioni locali:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Creazione dell'applicazione client di Windows Store

1. In una classe le notifiche, aggiungere un parametro di impostazioni locali per i metodi *StoreCategoriesAndSubscribe* e *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Si noti che invece il metodo *RegisterNativeAsync* è chiamato *RegisterTemplateAsync*: è in corso registrazione di un formato di notifica specifico in cui il modello dipende le impostazioni locali. È possibile utilizzare un nome per il modello ("localizedWNSTemplateExample"), poiché potrebbe essere necessario eseguire la registrazione più di un modello, ad esempio una per le notifiche di tipo avviso popup e l'altra per le sezioni ed è necessario assegnare un nome per poter aggiornare o eliminarli.

    Si noti che se un dispositivo registra più modelli con lo stesso tag, una destinazione messaggi in arrivo che generano tag più notifiche recapitato al dispositivo (uno per ogni modello). Questo comportamento risulta utile quando lo stesso messaggio logico ha al risultato in più le notifiche visive, ad esempio che mostra un badge e un tipo di avviso popup nelle applicazioni di Windows Store.

2. Aggiungere il metodo seguente per recuperare le impostazioni locali archiviate:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Nel MainPage.xaml.cs, il pulsante Aggiorna fare clic su gestore recupera il valore corrente della casella combinata impostazioni locali e fornendo a partecipare alla chiamata per la classe le notifiche, come illustrato:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Infine, nel file App.xaml.cs, assicurarsi di aggiornare il `InitNotificationsAsync` metodo per recuperare le impostazioni locali e usarlo per la sottoscrizione:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Invio di notifiche localizzate dal back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Utilizzare gli hub di notifica per inviare ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
