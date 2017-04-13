<properties
    pageTitle="Registrare l'utente corrente per le notifiche push tramite l'API Web | Microsoft Azure"
    description="Informazioni su come richiedere la registrazione di notifica push in un'app iOS con gli hub di notifica di Azure quando registeration viene eseguita tramite API Web ASP.NET."
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
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrare l'utente corrente per le notifiche push utilizzando ASP.NET

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come richiedere la registrazione di notifica push con gli hub di notifica di Azure quando viene eseguita la registrazione tramite API Web ASP.NET. In questo argomento estende l'esercitazione [notifica agli utenti con gli hub di notifica]. È necessario avere già completato i passaggi necessari in tale esercitazione per creare il servizio di dispositivi mobili autenticato. Per ulteriori informazioni sullo scenario di notifica agli utenti, vedere [notifica agli utenti con gli hub di notifica].

##<a name="update-your-app"></a>Aggiornare l'app  

1. Nel MainStoryboard_iPhone.storyboard, aggiungere i seguenti componenti di dalla libreria di oggetti:

    + **Etichetta**: "Push per gli utenti che hanno hub di notifica"
    + **Etichetta**: "ID di installazione"
    + **Etichetta**: "Utente"
    + **Campo di testo**: "Utente"
    + **Etichetta**: "Password"
    + **Campo di testo**: "Password"
    + **Pulsante**: "Accesso"

    A questo punto, lo storyboard è simile alla seguente:

    ![][0]

2. Nell'editor di Assistente creare prese per tutti i controlli switched e chiamata metta View Controller (delegato) i campi di testo e creare un' **azione** per il pulsante di **accesso** .

    ![][1]

    Il file BreakingNewsViewController.h ora deve contenere il codice seguente:

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Creare una classe denominata **DeviceInfo**e copiare il codice seguente nella sezione interfaccia del file DeviceInfo.h:

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Copiare il codice seguente nella sezione dell'implementazione del file DeviceInfo.m:

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. In PushToUserAppDelegate.h, aggiungere il singleton proprietà seguenti:

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. Nel metodo **didFinishLaunchingWithOptions** PushToUserAppDelegate.m, aggiungere il codice seguente:

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    La prima riga consente di inizializzare singleton **DeviceInfo** . Nella seconda riga verrà avviata la registrazione per che è già stata completata l'esercitazione [Introduzione a hub di notifica] push le notifiche, è già presentano.

9. In PushToUserAppDelegate.m, implementare il metodo **didRegisterForRemoteNotificationsWithDeviceToken** nel AppDelegate e aggiungere il codice seguente:

        self.deviceInfo.deviceToken = deviceToken;

    Consente di impostare il token di dispositivo per la richiesta.

    > [AZURE.NOTE] A questo punto, non occorre qualsiasi altro codice in questo metodo. Se si dispone già di una chiamata al metodo **registerNativeWithDeviceToken** aggiunti al termine dell'esercitazione [Introduzione a hub di notifica](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , è necessario commento esterna o rimuovere la chiamata.

10. Nel file PushToUserAppDelegate.m, aggiungere il metodo di gestore seguenti:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Questo metodo viene visualizzato un avviso nell'interfaccia utente quando l'app riceve le notifiche mentre è in esecuzione.

9. Aprire il file PushToUserViewController.m e restituire la tastiera di implementazione seguente:

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. Il metodo di **viewDidLoad** nel file PushToUserViewController.m, inizializzare l'etichetta ID di installazione come indicato di seguito:

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Aggiungere le proprietà seguenti nell'interfaccia di PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. Aggiungere quindi l'implementazione seguente:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Copiare il codice seguente il metodo di gestore **login** creato da XCode:

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Questo metodo ottiene un ID di installazione e di canale per le notifiche push e invia, insieme al tipo di dispositivo, il metodo API Web autenticato che crea una registrazione nell'hub di notifica. Questa API Web è stata definita nella [notifica agli utenti con gli hub di notifica].

Ora che è stato aggiornato l'applicazione client, tornare alla [notifica agli utenti con gli hub di notifica] e aggiornare il servizio per dispositivi mobili per l'invio di notifiche tramite hub di notifica.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Informare gli utenti con gli hub di notifica]: /manage/services/notification-hubs/notify-users-aspnet

[Guida introduttiva a hub di notifica]: /manage/services/notification-hubs/get-started-notification-hubs-ios
