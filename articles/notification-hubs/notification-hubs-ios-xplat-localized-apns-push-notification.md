<properties
    pageTitle="Notifica hub localizzate ultime notizie esercitazione per iOS"
    description="Informazioni su come utilizzare Azure servizio Bus notifica hub per inviare notifiche di news interruzione localizzati (iOS)."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Utilizzare gli hub di notifica per inviare notizie localizzati per i dispositivi iOS

> [AZURE.SELECTOR]
- [C# di Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare la funzionalità di [modelli](notification-hubs-templates-cross-platform-push-messages.md) di Azure notifica hub per trasmettere le notifiche di news importanti che sono state localizzate, lingua e dispositivo. In questa esercitazione si inizierà con l'app iOS creato in [Utilizzare gli hub di notifica per inviare ultime notizie]. Al termine, che sarà possibile eseguire la registrazione per le categorie che si desidera, specificare una lingua in cui ricevere le notifiche e ricevere solo le notifiche push per le categorie selezionate in tale lingua.


Esistono due parti a questo scenario:

- app iOS consente ai client dispositivi per specificare una lingua e per l'iscrizione a categorie di news interruzione diverso.

- back-end trasmette le notifiche tramite la funzionalità di **tag** e un **modello** di Azure notifica hub.



##<a name="prerequisites"></a>Prerequisiti

È necessario avere già completato l'esercitazione di [Utilizzare gli hub di notifica per inviare ultime notizie] e di avere il codice disponibile, perché questa esercitazione si basa direttamente su tale codice.

Visual Studio 2012 o versioni successiva è facoltativo.



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

Quindi abbiamo garantisce che i dispositivi registrato con un modello che fa riferimento a proprietà corretta. Ad esempio un'app iOS desideri registrare per essere sempre informati francese verrà registrato quanto segue:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

I modelli sono una potente caratteristica che ottenere ulteriori informazioni nell'articolo [modelli](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>Interfaccia utente di app

A questo punto si modificherà l'app ultime notizie creata nell'argomento [Utilizzare gli hub di notifica per inviare ultime notizie] inviare notizie localizzati uso dei modelli.


Nel MainStoryboard_iPhone.storyboard, aggiungere un controllo segmentato con tre lingue è supporterà: inglese, francese e mandarino.

![][13]

Assicurarsi quindi aggiungere un IBOutlet il ViewController.h, come illustrato di seguito:

![][14]

##<a name="building-the-ios-app"></a>Creazione di app iOS


1. Nel Notification.h aggiungere il metodo *retrieveLocale* e modificare l'archivio e sottoscrivere metodi come illustrato di seguito:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Nel Notification.m, modificare il metodo *storeCategoriesAndSubscribe* , aggiungendo il parametro locale e archiviazione in impostazioni predefinite degli utenti:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Quindi modificare il metodo *sottoscritti* per includere le impostazioni locali:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Si noti come ora si sta utilizzando il metodo *registerTemplateWithDeviceToken*, anziché *registerNativeWithDeviceToken*. Quando si registra per il modello è necessario specificare il modello json e anche un nome per il modello (come l'app essere utile registrare diversi modelli). Assicurarsi di registrare le categorie come tag, come si desidera assicurarsi di ricevere notifciations per tali novità.

    Aggiungere un metodo per recuperare le impostazioni locali dalle impostazioni predefinite utente:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Ora che si modifica la classe le notifiche, è necessario assicurarsi che il nostro ViewController vengono utilizzate le nuove UISegmentControl. Aggiungere la riga seguente nel metodo *viewDidLoad* per assicurarsi di visualizzare le impostazioni locali attualmente selezionato:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Quindi, nel metodo di *sottoscrizione* , modificare la chiamata a *storeCategoriesAndSubscribe* nel modo seguente:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Infine, è necessario aggiornare il metodo *didRegisterForRemoteNotificationsWithDeviceToken* nel AppDelegate.m, in modo che è possibile aggiornare correttamente la registrazione all'avvio dell'applicazione. Modificare la chiamata al metodo *la sottoscrizione* delle notifiche con le operazioni seguenti:

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facoltativo) Inviare le notifiche di modello localizzata da .NET console app.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(facoltativo) Invio di notifiche modello localizzati dal dispositivo

Se si non dispone dell'accesso a Visual Studio o da testare solo se si invia le notifiche di modello localizzata direttamente dall'app sul dispositivo.  È possibile semplice aggiungere parametri modello localizzati per i `SendNotificationRESTAPI` metodo definito nell'esercitazione precedente.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo dei modelli, vedere:

- [Informare gli utenti con gli hub di notifica: ASP.NET]
- [Informare gli utenti con gli hub di notifica: servizi mobili]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizzare gli hub di notifica per inviare ultime notizie]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Informare gli utenti con gli hub di notifica: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Informare gli utenti con gli hub di notifica: servizi mobili]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
