<properties
    pageTitle="Notifica hub ultime notizie esercitazione - iOS"
    description="Informazioni su come usare gli hub di notifica di Azure servizio Bus inviare notifiche notizie importanti ai dispositivi iOS."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizzare gli hub di notifica per inviare ultime notizie

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare Azure notifica hub per trasmettere le notifiche di news importanti per un'app iOS. Al termine, si sarà in grado di eseguire la registrazione per l'interruzione delle categorie di news che si è interessati e ricevere solo le notifiche push per le categorie. Questo scenario è un modello comune per molte applicazioni in cui le notifiche devono essere inviate a gruppi di utenti che hanno precedentemente dichiarati interesse in essi, ad esempio lettore RSS, App per fan e così via.

Trasmissione sono abilitati facilmente individuabili perché includono uno o più _tag_ durante la creazione di una registrazione nell'hub notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno registrato per il tag riceverà la notifica. Poiché i contrassegni sono semplicemente stringhe, non è necessario eseguire il provisioning in anticipo. Per ulteriori informazioni sui tag, fare riferimento al [Routing hub di notifica e le espressioni di Tag](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Prerequisiti

In questo argomento si basa sull'app è stato creato in [Guida introduttiva a hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario avere già completato [iniziare con gli hub di notifica][get-started].

##<a name="add-category-selection-to-the-app"></a>Aggiungere una selezione di una categoria all'app

Il primo passaggio consiste nell'aggiungere gli elementi dell'interfaccia utente per lo storyboard esistente che consentono all'utente di selezionare le categorie per eseguire la registrazione. Le categorie selezionate da un utente sono archiviate nel dispositivo. Quando si avvia l'app, una registrazione di dispositivo viene creata l'hub di notifica con le categorie selezionate come tag.

1. Nel MainStoryboard_iPhone.storyboard aggiungere i seguenti componenti dalla libreria di oggetti:
    + Un'etichetta con il testo "Ultime notizie",
    + Etichette con i testi delle categorie "Tutti", "Politica", "Business", "Technology", "Scienza", "Sport"
    + Sei parametri, uno per ogni categoria, impostare ogni opzione **stato** deve essere **disattivata** per impostazione predefinita.
    + Un pulsante denominato "Sottoscrivi"

    Lo storyboard avrà un aspetto come indicato di seguito:

    ![][3]

2. Nell'editor di Assistente, creare prese per tutte le opzioni e chiamata "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Creare un'azione per il pulsante denominato "sottoscrizione". Il ViewController.h deve contenere le operazioni seguenti:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Creare una nuova **Classe tocco cacao** denominata `Notifications`. Copiare il codice seguente nella sezione interfaccia del file Notifications.h:

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Aggiungere la seguente istruzione di importazione Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copiare il codice seguente nella sezione dell'implementazione del file Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Questa classe viene utilizzata l'archiviazione locale per archiviare e recuperare le varie categorie di news che riceverà il dispositivo. Inoltre, contiene un metodo per eseguire la registrazione per queste categorie utilizzando un [modello](notification-hubs-templates-cross-platform-push-messages.md) di registrazione.

7. Nel file AppDelegate.h, aggiungere un'istruzione import per Notifications.h e aggiungere una proprietà per un'istanza della classe notifiche:

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. Nel metodo **didFinishLaunchingWithOptions** AppDelegate.m, aggiungere il codice per inizializzare l'istanza di notifiche all'inizio del metodo.  
 
    `HUBNAME`e `HUBLISTENACCESS` (definito in hubinfo.h) deve avere già il `<hub name>` e `<connection string with listen access>` segnaposto sostituito con il nome di hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuto in precedenza

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Poiché credenziali distribuiti con un'applicazione client non sono in genere sicura, è necessario distribuire solo chiave per ascoltare l'accesso con l'applicazione client. Ascoltare consente l'accesso non è possibile modificare l'app per eseguire la registrazione per le notifiche, ma le registrazioni esistente e non è possibile inviare notifiche. Accesso completo usata in un servizio di back-end protetta per l'invio di notifiche e modificare le registrazioni esistenti.


9. Nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** AppDelegate.m sostituire il codice nel metodo con il codice riportato di seguito per passare il token di dispositivo per la classe le notifiche. La classe le notifiche verrà eseguita automaticamente la registrazione per le notifiche con le categorie. Se l'utente modifica le selezioni delle categorie, si chiama la `subscribeWithCategories` metodo in risposta a pulsante **Segui** aggiornarli.

    > [AZURE.NOTE] Poiché il token di dispositivo assegnato da Apple Push Notification Service (APNS) è possibile modificare in qualsiasi momento, è necessario registrare per le notifiche frequentemente evitare errori di notifica. In questo esempio Registra per la notifica ogni volta che l'avvio dell'applicazione. Per le app che vengono eseguite di frequente, più di una volta al giorno, è possibile probabilmente ignorare la registrazione per mantenere la larghezza di banda se meno di un giorno è trascorse registrazione precedente.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Si noti che a questo punto non dovrebbe essere presente nessun altro codice nel metodo **didRegisterForRemoteNotificationsWithDeviceToken** .

10. I metodi seguenti devono essere già presenti in AppDelegate.m da completare la [Guida introduttiva a hub di notifica] [ get-started] esercitazione.  In caso contrario aggiungerli.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Questo metodo gestisce le notifiche ricevute quando l'applicazione è in esecuzione visualizzando un semplice **UIAlert**.

11. In ViewController.m, aggiungere un'istruzione di importazione per AppDelegate.h e copiare il codice seguente viene generato XCode **la sottoscrizione** . Questo codice aggiornerà la registrazione di notifica per usare i nuovi tag di categoria selezionato dall'utente nell'interfaccia utente.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Questo metodo crea un **NSMutableArray** delle categorie e viene utilizzata la classe **le notifiche** per memorizzare l'elenco nell'archivio locale e registra il tag corrispondente con l'hub di notifica. Quando si modificano le categorie, la registrazione verrà ricreata con le nuove categorie.

12. In ViewController.m, aggiungere il codice seguente nel metodo **viewDidLoad** per impostare l'interfaccia utente in base alle categorie salvate in precedenza.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



L'app è ora possibile di memorizzare un gruppo di categorie nella memoria locale del dispositivo usata per eseguire la registrazione con l'hub di notifica ogni volta che avvia l'app.  È possibile modificare la selezione delle categorie in fase di esecuzione e fare clic sul metodo **sottoscritti** per aggiornare la registrazione del dispositivo. Successivamente, sarà necessario aggiornare l'app per inviare le notifiche di news interruzione direttamente nell'app.


##<a name="optional-sending-tagged-notifications"></a>(facoltativo) Invio di notifiche con tag

Se non si dispone di accesso a Visual Studio, è possibile passare alla sezione successiva e invio di notifiche da app stesso. È anche possibile inviare la notifica di modello appropriato dal [Portale classica Azure] tramite la scheda debug per l'hub di notifica. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(facoltativo) Invio di notifiche dal dispositivo

In genere le notifiche da inviate da un servizio di back-end, ma è possibile inviare notifiche di news interruzione direttamente dall'app. Per eseguire questa operazione verrà aggiornato il `SendNotificationRESTAPI` metodo definiti in [Guida introduttiva a hub di notifica] [ get-started] esercitazione.


1. Aggiornamento di ViewController.m il `SendNotificationRESTAPI` metodo come segue in modo che accetta un parametro per il tag di categoria e invia notifica appropriato [modello](notification-hubs-templates-cross-platform-push-messages.md) .

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. In ViewController.m aggiornare l'azione **Invia notifica** come nell'esempio che segue. In modo che invia le notifiche tramite singolarmente ogni tag e inviare a più piattaforme.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Rigenerare il progetto e verificare che non siano presenti errori di compilazione.


##<a name="run-the-app-and-generate-notifications"></a>Eseguire l'app e generare le notifiche

1. Fare clic sul pulsante Esegui per compilare il progetto e avviare l'app. Selezionare alcune opzioni di news interruzione sottoscrivere e quindi fare clic sul pulsante **Sottoscrivi** . Verrà visualizzata una finestra di dialogo che indica che sono stati sottoscritti le notifiche.

    ![][1]

    Quando si sceglie **Sottoscrivi**, l'app converte categorie selezionate in tag e le richieste di una nuova registrazione di dispositivo per i tag selezionati dall'hub notifica.

2. Immettere un messaggio da inviare come ultime notizie, quindi fare clic sul pulsante **Invia notifica** . In alternativa, eseguire l'app console .NET per generare le notifiche.

    ![][2]


3. Ogni dispositivo sottoscritto le ultime notizie riceverà le notifiche di news interruzione che appena inviato.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo appreso come trasmettere aggiornate in base alla categoria. Valutare la possibilità di completare una delle seguenti esercitazioni che illustrano altri scenari avanzati hub di notifica:

+ **[Utilizzare gli hub di notifica trasmettere notizie localizzati]**

    Informazioni su come espandere l'app di news importanti per abilitare l'invio di notifiche localizzate.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizzare gli hub di notifica trasmettere notizie localizzati]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Portale classica Azure]: https://manage.windowsazure.com
