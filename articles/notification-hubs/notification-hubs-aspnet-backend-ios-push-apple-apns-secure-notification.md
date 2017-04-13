<properties
    pageTitle="Hub di notifica Azure sicuro Push"
    description="Informazioni su come inviare le notifiche push sicura a un'app iOS da Azure. Esempi di codice scritti in obiettivo C e c#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
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

In questa esercitazione sicura Push viene illustrato come inviare una notifica push in modo sicuro. L'esercitazione si basa sui esercitazione [Informare gli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) in modo che è necessario completare la procedura in tale esercitazione prima di tutto.

> [AZURE.NOTE] In questa esercitazione si presuppone che è stato creato e configurato l'hub di notifica, come descritto nella [Guida introduttiva di notifica hub (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificare il progetto di iOS

Ora che è stato modificato l'app back-end per inviare solo l' *id* di una notifica, è necessario modificare l'app iOS per gestire la notifica di richiamare il back-end per recuperare il messaggio protetto da visualizzare.

A tale scopo, è necessario scrivere la logica per recuperare il contenuto protetto da app back-end.

1. In **AppDelegate.m**, verificare che i registri di app per le notifiche automatica in modo che elabora l'id di notifica inviata dal back-end. Aggiungere l'opzione **UIRemoteNotificationTypeNewsstandContentAvailability** in didFinishLaunchingWithOptions:

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. Nel **AppDelegate.m** aggiungere una sezione di implementazione nella parte superiore con la seguente dichiarazione:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Aggiungere quindi nella sezione implementazione il codice seguente, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint per il back-end ottenuto in precedenza:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. A questo punto è necessario gestire la notifica in arrivo e utilizzare il metodo precedente per recuperare il contenuto da visualizzare. Prima di tutto, è necessario abilitare l'app iOS per l'esecuzione in background quando si riceve una notifica push. In **XCode**, selezionare il progetto app nel riquadro sinistro, quindi scegliere la destinazione principale app nella sezione **target** dal riquadro centrale.

5. Scegliere la scheda **funzionalità** nella parte superiore del riquadro centrale e selezionare la casella di controllo **Remoto notifiche** .

    ![][IOS1]


6. Aggiungere il metodo seguente per gestire le notifiche push in **AppDelegate.m** :

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Si noti che è preferibile gestire casi di proprietà di intestazione mancanti autenticazione o il rifiuto il back-end. Gestione specifica di questi casi dipendono prevalentemente l'esperienza utente di destinazione. È possibile visualizzare una notifica con la richiesta generica per gli utenti autenticati per recuperare la notifica effettiva.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1. In XCode, eseguire l'app in un dispositivo iOS fisica (simulatore non funziona con le notifiche push).

2. Nell'applicazione di iOS dell'interfaccia utente, immettere un nome utente e password. Può trattarsi di qualsiasi stringa, ma devono essere lo stesso valore.

3. Nell'applicazione di iOS dell'interfaccia utente, fare clic su **accesso**. Fare clic su **Invia push**. Verrà visualizzata la notifica sicura vengano visualizzata nell'interfaccia di notifica.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
