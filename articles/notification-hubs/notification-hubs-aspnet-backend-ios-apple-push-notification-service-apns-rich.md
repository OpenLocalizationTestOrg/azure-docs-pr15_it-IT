<properties
    pageTitle="Notifica Azure hub RTF Push"
    description="Informazioni su come inviare le notifiche push RTF a un'app iOS da Azure. Esempi di codice scritti in obiettivo C e c#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notifica Azure hub RTF Push


##<a name="overview"></a>Panoramica

Per poter coinvolgimento degli utenti con immediato contenuto RTF, un'applicazione potrebbe essere necessario push oltre a testo normale. Queste notifiche alzare di livello interazioni dell'utente e presentare contenuto, ad esempio gli URL, suoni, immagini o tagliandi e altro. In questa esercitazione si basa sull'argomento [Informare gli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e viene illustrato come inviare le notifiche push che includono payload (ad esempio immagine).


In questa esercitazione è compatibile con iOS 7 e 8.

  ![][IOS1]

Alto livello:

1. Back-end app:
    - Archivia payload RTF (in questo caso, immagine) nell'archivio locale/database back-end
    - Invia ID questa notifica RTF al dispositivo
2. App nel dispositivo:
    - Back-end richiede payload RTF con l'ID riceve dei contatti
    - Invio di notifiche di utenti nel dispositivo quando il recupero dei dati è stata completata e Mostra il payload immediatamente quando gli utenti tocca per altre informazioni


## <a name="webapi-project"></a>Progetto WebAPI

1. In Visual Studio, aprire il progetto **AppBackend** creata in esercitazione [Informare gli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Ottenere un'immagine che si desidera informare gli utenti che dispongono e inserirlo in una cartella **img** nella directory del progetto.
3. Fare clic su **Mostra tutti i file** in Esplora soluzioni e fare clic sulla cartella per **Includere nel progetto**.
4. Con l'immagine selezionata, cambiare Build Action nella finestra proprietà su **Risorsa incorporata**.

    ![][IOS2]

5. In **Notifications.cs**, aggiungere la seguente istruzione:

        using System.Reflection;

6. Aggiornare l'intera classe di **notifiche** con il codice riportato di seguito. Assicurarsi di sostituire i segnaposto con le credenziali di hub di notifica e nome del file immagine.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (facoltativo) Per ulteriori informazioni su come aggiungere e ottenere le risorse del progetto, consultare [come incorporare e accedere alle risorse utilizzando Visual c#](http://support.microsoft.com/kb/319292) .

7. In **NotificationsController.cs**ridefinire **NotificationsController** con i frammenti di seguito. Questo invia un id notifica RTF invisibile iniziale al dispositivo e consente di recuperare lato client l'immagine:

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. A questo punto verrà installato nuovamente l'app in un sito Web Azure per renderla accessibile da tutti i dispositivi. Pulsante destro del mouse sul progetto **AppBackend** e selezionare **pubblica**.

9. Selezionare il sito Web Azure come la destinazione di pubblicazione. Accedere con l'account Azure e selezionare un sito Web esistente o nuovo e prendere nota della proprietà **URL di destinazione** nella scheda **connessione** . Si farà riferimento a questo URL come il *punto finale di back-end* più avanti in questa esercitazione. Fare clic su **pubblica**.

## <a name="modify-the-ios-project"></a>Modificare il progetto di iOS

Ora che è stato modificato il back-end di app per inviare solo l' *id* di una notifica, modificare l'app iOS per gestire l'id di recuperare il messaggio RTF dal back-end.

1. Aprire il progetto iOS e attivare le notifiche remote passando alla destinazione principale app nella sezione **target** .

2. Fare clic su **funzionalità**, attivare la **Modalità di sfondo**e selezionare la casella di controllo **Remoto notifiche** .

    ![][IOS3]

3. Passare a **Main.storyboard**e verificare che si dispone di un Controller di visualizzazione (a cui fa come Home Controller di visualizzazione in questa esercitazione) da [Una notifica utente](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) esercitazione.

4. Aggiungere un **Controller di spostamento** per lo storyboard e CTRL + trascinamento a casa Controller di visualizzazione per rendere la **visualizzazione radice** della struttura di spostamento. Verificare che sia selezionata **È iniziale View Controller** di controllo attributi solo del controller di spostamento.

5. Aggiungere un **Controller di visualizzazione** per storyboard e aggiungere una **Visualizzazione immagine**. Questa è la pagina verrà visualizzato agli utenti una volta scelgono di altre informazioni, scegliere il notifiication. Lo storyboard avrà un aspetto come indicato di seguito:

    ![][IOS4]

6. Fare clic su **Home View Controller** di storyboard e assicurarsi che abbia **homeViewController** come **Classe personalizzata** e **ID Storyboard** sotto il controllo di identità.

7. Eseguire la stessa operazione per immagine View Controller come **imageViewController**.

8. Creare una nuova classe View Controller intitolato **imageViewController** per gestire l'interfaccia utente appena creato.

9. In **imageViewController.h**, aggiungere quanto segue a dichiarazioni di interfaccia del controller. Assicurarsi di trascinamento del controllo dalla visualizzazione immagine storyboard a queste proprietà collegare due:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. In **imageViewController.m**, aggiungere quanto segue alla fine di **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. Importare controller immagine che è stato creato in **AppDelegate.m**:

        #import "imageViewController.h"

12. Aggiungere una sezione interfaccia con la dichiarazione seguente:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. In **AppDelegate**, assicurarsi che l'app Registra per le notifiche non interattiva in **applicazione: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Sostituire nell'implementazione seguente per **applicazione: didRegisterForRemoteNotificationsWithDeviceToken** per rendere lo storyboard dell'interfaccia utente viene modificato in considerazione:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Aggiungere quindi i metodi seguenti per **AppDelegate.m** per recuperare l'immagine dall'endpoint e inviare una notifica locale una volta completato il recupero. Assicurarsi di sostituire il segnaposto `{backend endpoint}` con l'endpoint di back-end:

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Gestire la notifica locale sopra aprendo il controller di visualizzazione immagine **AppDelegate.m** con i metodi seguenti:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Eseguire l'applicazione

1. In XCode, eseguire l'app in un dispositivo iOS fisica (simulatore non funziona con le notifiche push).

2. Nell'applicazione di iOS dell'interfaccia utente, immettere un nome utente e password dello stesso valore per l'autenticazione e fare clic su **Accedi**.

3. Fare clic su **Invia push** e verrà visualizzato un avviso all'interno dell'applicazione. Se si fa clic su **altro**, verranno inseriti l'immagine che si è scelto di includere il back-end app.

4. È possibile anche fare clic su **Invia push** e immediatamente fare clic sul pulsante iniziale del dispositivo. In alcuni secondi, si riceverà una notifica push. Se si è toccare o fare clic su altro, verranno inseriti l'app e il contenuto dell'immagine RTF.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
