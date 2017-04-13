<properties
    pageTitle="Invio di notifiche push per iOS con gli hub di notifica di Azure | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione di iOS."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="invio di notifiche, le notifiche push ios notifiche push"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Invio di notifiche push per iOS con gli hub di notifica di Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

In questa esercitazione viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push a un'applicazione di iOS. Sarà necessario creare un'app per iOS vuoto che riceve le notifiche push utilizzando il [servizio di notifica Push di Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Al termine, sarà possibile usare l'hub di notifica per trasmettere le notifiche push per tutti i dispositivi che eseguono l'app.

## <a name="before-you-begin"></a>Prima di iniziare

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione sono disponibili [in GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ [Servizi mobili iOS SDK versione 1.2.4]
+ Versione più recente di [Xcode]
+ Un iOS 8 (o versione successiva)-dispositivo dotato di connessione
+ Partecipazione al [Programma di sviluppo di Apple](https://developer.apple.com/programs/) .

   > [AZURE.NOTE] A causa di requisiti di configurazione per le notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo fisico iOS (iPhone o iPad) anziché iOS simulatore.

Esercitazione è un prerequisito per tutte le altre esercitazioni hub di notifica per le app iOS.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurare l'Hub di notifica per iOS notifiche push

In questa sezione viene descritto come creare un nuovo hub di notifica e configurare l'autenticazione con APN utilizzando il certificato di push **p12** creato. Se si desidera utilizzare un hub di notifica che è già stato creato, è possibile procedere al passaggio 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Fare clic sul pulsante <b>Notification Services</b> e <b>l'Impostazioni</b> , quindi selezionare <b>Apple (APNS)</b>. Fare clic su <b>Carica certificato</b> e selezionare il file <b>p12</b> esportati in precedenza. Assicurarsi di specificare anche la password corretta.</p>
<p>Assicurarsi di selezionare la modalità <b>Sandbox</b> poiché si tratta per lo sviluppo. Utilizzare la <b>produzione</b> solo se si desidera inviare le notifiche push agli utenti che hanno acquistato l'app dall'archivio.</p>
</li>
</ol>
&emsp;&emsp;![Configurare APN nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Configurare certificazione APN nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



L'hub di notifica è ora configurato per l'utilizzo con APN e disporre le stringhe di connessione per registrare l'app e inviare le notifiche push.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS a un hub di notifica

1. In Xcode, creare un nuovo progetto iOS e selezionare il modello **Singola applicazione di visualizzazione** .

    ![Xcode - visualizzazione singola applicazione][8]

2. Quando si impostano le opzioni per il nuovo progetto, assicurarsi di utilizzare lo stesso **Nome prodotto** e **Identificativo dell'organizzazione** che è utilizzato quando si impostata in precedenza l'ID di raggruppamento nel portale per sviluppatori di Apple.

    ![Xcode - opzioni di project][11]

3. In **i siti di destinazione**, fare clic su nome del progetto, fare clic sulla scheda **Impostazioni di compilazione** ed espandere **Identità di accesso di codice**e quindi in **Debug**, impostare l'identità di firma del codice. Attivare o disattivare **i livelli** di **base** per **tutti**e impostare **Il Provisioning di profilo** al profilo provisioning creata in precedenza.

    Se non è visualizzato il nuovo profilo di provisioning creata in Xcode, provare ad aggiornare i profili per l'identità di firma. Fare clic su **Xcode** sulla barra dei menu, fare clic su **Preferenze**, fare clic sulla scheda **Account** , fare clic sul pulsante **Visualizza dettagli** , fare clic sull'identità di firma e quindi fare clic sul pulsante Aggiorna nell'angolo in basso a destra.

    ![Xcode - profilo provisioning][9]

4. [Servizi mobili iOS SDK versione 1.2.4] di scaricare e decomprimere il file. In Xcode, fare clic sull'opzione **Aggiungi file per** aggiungere la cartella **WindowsAzureMessaging.framework** al progetto Xcode mouse sul progetto. Selezionare **copiare elementi, se necessario**e quindi fare clic su **Aggiungi**.

    >[AZURE.NOTE] Gli hub di notifica SDK non supporta attualmente bitcode in Xcode 7.  È necessario impostare **Abilitare Bitcode** su **No** nella **Build Options** per il progetto.

    ![Decomprimere SDK Azure][10]

5. Aggiungere un nuovo file di intestazione al progetto denominato `HubInfo.h`. Questo file contiene le costanti per l'hub di notifica.  Aggiungere le seguenti definizioni e sostituire i segnaposto letterale stringa con il *nome dell'hub* e *DefaultListenSharedAccessSignature* che indicato in precedenza.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Aprire il `AppDelegate.h` file aggiungere le seguenti istruzioni di importazione:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. Nel `AppDelegate.m file`, aggiungere il codice seguente nel `didFinishLaunchingWithOptions` metodo in base alla versione di iOS. Questo codice registra il quadratino di dispositivo con APN:

    Per iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Per le versioni di iOS prima di 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Nello stesso file, aggiungere i metodi seguenti. Questo codice si connette all'hub notifica utilizzando le informazioni di connessione specificate in HubInfo.h. Quindi fornisce il token di dispositivo all'hub di notifica, in modo che l'hub di notifica può inviare notifiche:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. Nello stesso file, aggiungere il metodo seguente per visualizzare un **UIAlert** se si riceve la notifica mentre è attivo l'app:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Creare ed eseguire l'app in un dispositivo per verificare che non si verificano errori.

## <a name="send-test-push-notifications"></a>Inviare le notifiche push di test


È possibile testare ricezione delle notifiche nell'app tramite l'invio di notifiche push nel [Portale di Azure] tramite la sezione di **risoluzione dei problemi** e il hub (utilizzare l'opzione *Invia testare* ).

![Portale di Azure - invio di Test][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Facoltativo) Inviare le notifiche push dall'app

>[AZURE.IMPORTANT] In questo esempio di invio di notifiche da app client disponibile per solo a scopo didattico. Dal momento che sarà necessario il `DefaultFullSharedAccessSignature` ad assistere all'applicazione client, vengono esposti l'hub di notifica per il rischio che un utente può accedere per l'invio di notifiche non autorizzate ai client.

Se si desidera inviare le notifiche push all'interno di un'app, in questa sezione fornisce un esempio di come eseguire questa operazione utilizzando l'interfaccia REST.

1. In Xcode, aprire `Main.storyboard` e aggiungere i seguenti componenti dell'interfaccia utente dalla libreria di oggetti per consentire all'utente di inviare le notifiche push nell'app:

    - Etichetta con alcun testo dell'etichetta. Da utilizzare per segnalare errori invio di notifiche. La proprietà **righe** deve essere impostata su **0** in modo che verrà automaticamente dimensioni vincolato a destra e i margini sinistro e la parte superiore della visualizzazione.
    - Un campo di testo con testo **segnaposto** è impostato su **Immetti messaggio di notifica**. Limitare il campo sotto l'etichetta come illustrato di seguito. Visualizzazione Controller come delegato presa.
    - Il pulsante Guida **Invia notifica** vincolato sotto il campo di testo e nell'interfaccia di orizzontale.

    La visualizzazione avrà un aspetto come indicato di seguito:

    ![Finestra di progettazione Xcode][32]


2. [Aggiungi prese](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) per il campo di testo ed etichetta connessi la visualizzazione e aggiornare il `interface` definizione per supportare `UITextFieldDelegate` e `NSXMLParserDelegate`. Aggiungere le dichiarazioni di tre proprietà illustrate di seguito per assistenza con la chiamata API REST e analisi della risposta.

    Il file ViewController.h avrà un aspetto come indicato di seguito:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Apri `HubInfo.h` e aggiungere le seguenti costanti che verranno utilizzate per l'invio di notifiche per il tuo fulcro. Sostituire la stringa segnaposto letterale con la stringa di connessione *DefaultFullSharedAccessSignature* effettiva.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Aggiungere quanto segue `#import` istruzioni per il `ViewController.h` file.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. In `ViewController.m` aggiungere il codice seguente per l'implementazione dell'interfaccia. Questo codice analizza la stringa di connessione *DefaultFullSharedAccessSignature* . Come indicato nella [Guida di riferimento all'API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), queste informazioni analizzate verranno utilizzate per generare un token SA per l'intestazione della richiesta di **autorizzazione** .

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. In `ViewController.m`, aggiornare la `viewDidLoad` metodo per analizzare la stringa di connessione durante il caricamento della visualizzazione. Aggiungere anche i metodi di utilità, illustrati di seguito, per l'implementazione dell'interfaccia.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. In `ViewController.m`, aggiungere il codice seguente per l'implementazione dell'interfaccia per generare il token di autorizzazione SA forniti nell'intestazione di **autorizzazione** , come indicato nel [Riferimento all'API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. CTRL + trascinamento da **Inviare notifica** pulsante per `ViewController.m` per aggiungere un'azione denominata **SendNotificationMessage** per l'evento **Tocco verso il basso** . Metodo di aggiornamento con il codice seguente per inviare la notifica tramite l'API REST.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. In `ViewController.m`, aggiungere il metodo seguente delegato per il supporto di chiudere la tastiera per il campo di testo. CTRL e trascinare il campo di testo per l'icona di Controller di visualizzazione nella finestra di progettazione dell'interfaccia per impostare il visualizzazione controller delegato presa.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. In `ViewController.m`, aggiungere i seguenti metodi di delegato per il supporto di analisi della risposta tramite `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Compilare il progetto e verificare che non siano presenti errori.


> [AZURE.NOTE] Se si verifica un errore nel Xcode7 sul supporto bitcode, è necessario modificare le **Impostazioni di compilazione** > **Abilitare Bitcode (ENABLE_BITCODE)** su **NO** nella Xcode. SDK hub di notifica attualmente non supporta bitcode. 

È possibile trovare tutti i payload notifica possibili in Apple [Push Notification Programming Guide e locale].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Verifica se l'app è possibile ricevere notifiche push

Per testare le notifiche push su iOS, è necessario distribuire l'app in un dispositivo iOS fisica. È possibile inviare le notifiche push di Apple utilizzando iOS simulatore.

1. Eseguire l'app e verificare che la registrazione ha avuto esito positivo e quindi fare **clic su OK**.

    ![iOS Test registrazione di notifica Push App][33]

2. È possibile inviare una notifica push di test dal [Portale di Azure], come descritto in precedenza. Se è stato aggiunto il codice per l'invio di notifiche push nell'app, toccare all'interno del campo di testo per immettere un messaggio di notifica. Premere il pulsante **Invia** la tastiera o il pulsante **Invia notifica** nella visualizzazione per inviare il messaggio di notifica.

    ![iOS App Push Notification Invia prova][34]

3. Notifiche push viene inviato a tutti i dispositivi registrati per ricevere le notifiche dall'Hub notifica specifico.

    ![iOS Test ricezione di notifica Push App][35]


##<a name="next-steps"></a>Passaggi successivi

In questo esempio semplice trasmessa le notifiche push per tutti i dispositivi iOS registrati. È consigliabile come un passaggio successivo in quanto appreso che si procede all'esercitazione [Azure notifica hub informare gli utenti per iOS con back-end .NET] che verrà guidano l'utente nella creazione di un back-end per inviare le notifiche push utilizzando i tag. 

Se si vuole segmento gli utenti dai gruppi di interesse, è possibile spostare inoltre in per l'esercitazione di [Utilizzare gli hub di notifica per inviare ultime notizie] . 

Per informazioni generali sull'hub di notifica, vedere [Guida hub di notifica].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Servizi mobili iOS SDK versione 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notifica hub indicazioni]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure notifica hub informare gli utenti per iOS con back-end .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Utilizzare gli hub di notifica per inviare ultime notizie]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Locale e Guida di programmazione di notifica Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portale di Azure]: https://portal.azure.com