<properties
    pageTitle="Azure coinvolgimento Mobile iOS SDK raggiunga integrazione | Microsoft Azure"
    description="Ultimi aggiornamenti e le procedure per iOS SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Come integrare impegno raggiungere in iOS

È necessario seguire la procedura di integrazione descritta in [come integrare impegno iOS documento](mobile-engagement-ios-integrate-engagement.md) prima di iniziare questa Guida.

Questa documentazione richiede XCode 8. Se si dipende molto XCode 7 è possibile utilizzare [iOS coinvolgimento SDK v3.2.4](https://aka.ms/r6oouh). C'è un errore noto in questa versione precedente mentre è in esecuzione in dispositivi iOS 10: le notifiche di sistema non sono prese in considerazione. Per risolvere il problema è necessario implementare API obsoleto `application:didReceiveRemoteNotification:` nell'app delegare come indicato di seguito:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Non è consigliabile questa soluzione alternativa** come questo comportamento può modificare qualsiasi aggiornamento della versione imminenti iOS (anche minore) perché questo iOS API è deprecata. È necessario passare a 8 XCode più presto possibile.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Abilitare l'app ricevere notifiche Push invisibile

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Passaggi per l'integrazione

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporare l'impegno raggiunga SDK nel progetto iOS

-   Aggiungere sdk portata nel progetto Xcode. In Xcode, passare a **progetto \> Aggiungi a progetto** e scegliere il `EngagementReach` cartella.

### <a name="modify-your-application-delegate"></a>Modificare il delegato applicazione

-   Nella parte superiore del file di implementazione, importare il modulo impegno raggiungere:

        [...]
        #import "AEReachModule.h"

-   Metodo `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:`, creare un modulo portata e passare alla linea inizializzazione impegno esistente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modificare la stringa **"icon.png"** con il nome dell'immagine che si desidera impostare come icona di notifica.
-   Se si desidera utilizzare l'opzione *valore badge di aggiornamento* in portata campagne o se si desidera utilizzare push nativo \<SaaS/portata API/campagna formato/nativo Push\> campagne, è necessario consentire le potenzialità di modulo per gestire l'icona badge stesso (verrà automaticamente deselezionare badge applicazione e reimpostare anche il valore archiviato da coinvolgimento ogni volta che l'applicazione è iniziato o foregrounded). Aggiungendo la riga seguente dopo l'inizializzazione modulo portata:

        [reach setAutoBadgeEnabled:YES];

-   Se si vuole gestire portata push di dati, è necessario consentire al delegato applicazione rispettare le `AEReachDataPushDelegate` protocollo. Aggiungere la riga seguente dopo inizializzazione del modulo di mano:

        [reach setDataPushDelegate:self];

-   È possibile implementare i metodi `onDataPushStringReceived:` e `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` nel proprio delegato applicazione:

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Categoria

Il parametro di categoria è facoltativo quando si crea una campagna Push di dati e consente di filtrare i dati inserisce. Questo è utile se si desidera inviare diversi tipi di `Base64` dati e si desidera identificare il tipo di prima dell'analisi loro.

**L'applicazione è ora possibile ricevere e visualizzare contenuto portata!**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Come ricevere gli annunci e sondaggi in qualsiasi momento

Coinvolgimento possibile inviare notifiche portata agli utenti finali in qualsiasi momento tramite il servizio di notifica Push Apple.

Per abilitare questa funzionalità, è necessario preparare l'applicazione per le notifiche push Apple e modificare i delegati di applicazione.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparare l'applicazione per le notifiche push di Apple

Seguire la Guida: [come preparare l'applicazione per le notifiche Push di Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Aggiungere il codice client necessario

*A questo punto l'applicazione deve disporre di un certificato di push Apple registrato in frontend coinvolgimento.*

Se non si è già fatto, è necessario registrare l'applicazione di ricevere notifiche push.

* Importazione di `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

* Aggiungere la riga seguente all'avvio dell'applicazione (in genere in `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

Quindi, è necessario fornire coinvolgimento token dispositivo restituito dal server di Apple. Questa operazione viene eseguita nel metodo denominato `application:didRegisterForRemoteNotificationsWithDeviceToken:` nel proprio delegato applicazione:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Infine, è necessario informare SDK coinvolgimento quando l'applicazione riceve una notifica remota. A tale scopo, chiamare il metodo `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` nel proprio delegato applicazione:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] Il metodo precedente è stato introdotto in iOS 7. Se la destinazione iOS < 7, assicurarsi di implementare metodo `application:didReceiveRemoteNotification:` nel delegato applicazione e chiamare `applicationDidReceiveRemoteNotification` in EngagementAgent passando null anziché il `handler` argomento:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Per impostazione predefinita, coinvolgimento raggiunga controlla il completionHandler. Se si vuole rispondere manualmente il `handler` blocco nel codice, è possibile passare null il `handler` argomento e controllo del completamento bloccare manualmente. Vedere la `UIBackgroundFetchResult` tipo per un elenco di valori possibili.


### <a name="full-example"></a>Esempio completo

Ecco un esempio completo dell'integrazione:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se si dispone di implementazione UNUserNotificationCenterDelegate

SDK include anche l'implementazione del protocollo UNUserNotificationCenterDelegate. Viene utilizzato da SDK per monitorare il ciclo di vita delle notifiche di impegno nei dispositivi che eseguono in iOS 10 o versione successiva. Se il SDK rileva il delegato non utilizza la propria implementazione poiché può essere presente un solo delegato UNUserNotificationCenter per ogni applicazione. Questo significa che è necessario aggiungere logica di impegno per il proprio delegato.

Esistono due modi per eseguire questa operazione.

È sufficiente inoltrando il delegato chiama SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Oppure che ereditano dalla `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] È possibile determinare se una notifica proviene da una partecipazione attiva o non passando il `userInfo` dizionario all'agente `isEngagementPushPayload:` metodo per la classe.

##<a name="how-to-customize-campaigns"></a>Come personalizzare campagne

### <a name="notifications"></a>Notifiche

Esistono due tipi di notifiche: le notifiche di sistema e all'interno dell'applicazione.

Notifiche di sistema vengono gestite dal iOS e non possono essere personalizzate.

Le notifiche di app sono costituite da una visualizzazione in modo dinamico viene aggiunto alla finestra dell'applicazione corrente. Questo comando si chiama una sovrapposizione di notifica. Sovrapposizioni di notifica sono ideali per una rapida integrazione perché è necessario non è possibile modificare qualsiasi visualizzazione dell'applicazione.

#### <a name="layout"></a>Layout

Per modificare l'aspetto delle notifiche di nell'app, è sufficiente modificare il file `AENotificationView.xib` alle proprie esigenze, purché si mantenere i valori di tag e i tipi di sottoviste esistente.

Per impostazione predefinita, in app vengono visualizzate nella parte inferiore dello schermo. Se si preferisce visualizzarli nella parte superiore dello schermo, modificare l'oggetto fornito `AENotificationView.xib` e modificare il `AutoSizing` proprietà di visualizzazione principale in modo che può trovarsi nella parte superiore della relativa superview.

#### <a name="categories"></a>Categorie

Quando si modifica il layout disponibili, modificare l'aspetto di tutte le notifiche. Categorie consentono di definire diversi aspetti target (eventualmente comportamenti) per le notifiche. Quando si crea una campagna di mano, è possibile specificare una categoria. Tenere presente che le categorie consentono inoltre di personalizzare gli annunci e sondaggi, descritto più avanti in questo documento.

Per registrare un gestore di categoria per le notifiche, è necessario aggiungere una chiamata dopo il modulo portata viene inizializzato.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`deve essere un'istanza di un oggetto che è conforme al protocollo `AENotifier`.

È possibile implementare i metodi di protocollo da soli o si può scegliere di implementare nuovamente la classe esistente `AEDefaultNotifier` che già esegue la maggior parte del lavoro.

Se si desidera ridefinire la visualizzazione di notifica per una specifica categoria, ad esempio, è possibile seguire questo esempio:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Questo semplice esempio categoria si supponga di disporre di un file denominato `MyNotificationView.xib` il bundle principale dell'applicazione. Se il metodo non riesce a trovare una corrispondenza `.xib`, non verrà visualizzata la notifica e coinvolgimento verrà visualizzato un messaggio nella console.

Il file di ufficio fornito deve rispettare le regole seguenti:

-   Deve contenere solo un'unica visualizzazione.
-   Sottoviste devono essere dello stesso tipo di quelle all'interno del file di ufficio fornito denominate`AENotificationView.xib`
-   Sottoviste devono avere gli stessi tag come quelli all'interno fornito file ufficio denominato`AENotificationView.xib`

> [AZURE.TIP] È sufficiente copiare il file di ufficio fornito, denominato `AENotificationView.xib`e iniziare a usare da tale posizione. Ma attenzione, la visualizzazione all'interno di file di ufficio è associata alla classe `AENotificationView`. Questa classe ridefinita il metodo `layoutSubViews` spostare e ridimensionare le sottoviste in base al contesto. È possibile sostituirlo con un `UIView` o una visualizzazione personalizzata classe.

Se è necessario personalizzazione più approfondita delle notifiche di (se si desidera, ad esempio per caricare la visualizzazione direttamente dal codice), è consigliabile consultare la documentazione di classe e codice di origine di `Protocol ReferencesDefaultNotifier` e `AENotifier`.

Si noti che è possibile utilizzare la stessa notifica per più categorie.

È anche possibile ridefinire notifica predefinito alla seguente:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Gestione di notifica

Quando si usa la categoria predefinita, alcuni metodi di ciclo di vita sono invitati la `AEReachContent` oggetto report statistiche e aggiornare lo stato della campagna:

-   Nell'applicazione, verrà visualizzata la notifica di `displayNotification` metodo (che vengono rilevate le statistiche) da `AEReachModule` se `handleNotification:` restituisce `YES`.
-   Se la notifica viene chiusa, il `exitNotification` chiamato, la segnalazione di statistica e ora è possibile elaborare campagne successive.
-   Se si fa clic sul messaggio di notifica, `actionNotification` è chiamato, la segnalazione di statistica e viene eseguita l'azione associata.

Se l'implementazione di `AENotifier` consente di ignorare il comportamento predefinito, è necessario chiamare questi metodi di ciclo di vita per se stessi. Negli esempi seguenti sono disponibili alcuni casi in cui viene ignorato il comportamento predefinito:

-   Non estendere `AEDefaultNotifier`, ad esempio è implementata la gestione delle categorie da zero.
-   Overrode `prepareNotificationView:forContent:`, assicurarsi di eseguire il mapping almeno `onNotificationActioned` o `onNotificationExited` a uno del U.I controlli.

> [AZURE.WARNING] Se `handleNotification:` genera un'eccezione, il contenuto viene eliminata e `drop` è chiamato, viene riportato nelle statistiche e ora è possibile elaborare campagne successive.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Includere notifica come parte di una visualizzazione esistente

Sovrapposizioni sono ideali per un'integrazione veloce ma possono non essere talvolta comoda o possono avere effetti indesiderati.

Se non si è soddisfatti il sistema di sovrapposizione in alcune delle proprie visualizzazioni, è possibile personalizzare per queste visualizzazioni.

È possibile includere il layout di notifica nelle proprie visualizzazioni esistenti. A tale scopo, non c'è due stili di implementazione:

1.  Aggiungere la visualizzazione di notifiche tramite Generatore interfaccia

    -   Aprire *l'interfaccia generatore*
    -   Inserire un 320 x 60 (o 768 x 60 se ci si trova nell'iPad) `UIView` nel punto in cui si desidera che vengono visualizzate la notifica
    -   Impostare il valore di Tag per la visualizzazione agli: **36822491**

2.  Aggiungere la visualizzazione di notifica a livello di programmazione. Quando la visualizzazione è stata inizializzata è sufficiente aggiungere il codice seguente:

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`azione di macro sono disponibili `AEDefaultNotifier.h`.

> [AZURE.NOTE] Notifica predefinita rileva automaticamente che il layout di notifica inclusi in questa visualizzazione e non aggiungerà una sovrapposizione per renderla.

### <a name="announcements-and-polls"></a>Annunci e sondaggi

#### <a name="layouts"></a>Layout

È possibile modificare i file `AEDefaultAnnouncementView.xib` e `AEDefaultPollView.xib` come mantenere i valori di tag e i tipi di sottoviste esistente.

#### <a name="categories"></a>Categorie

##### <a name="alternate-layouts"></a>Layout alternativi

Ad esempio le notifiche, categoria della campagna può essere utilizzata per layout alternativi per gli annunci e sondaggi.

Per creare una categoria per un annuncio, è necessario estendere **AEAnnouncementViewController** e la si registra una volta inizializzato il modulo di mano:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Ogni volta che un utente deve fare clic su una notifica per un annuncio con la categoria "personale\_categoria", controller visualizzazione registrati (in questo caso `MyCustomAnnouncementViewController`) verrà inizializzato utilizzando il metodo `initWithAnnouncement:` e la visualizzazione verrà aggiunti alla finestra dell'applicazione corrente.

Per l'implementazione del `AEAnnouncementViewController` classe è necessario leggere la proprietà `announcement` inizializzare le sottoviste. Valutare la possibilità di esempio seguente, in cui due etichette vengono inizializzati utilizzando `title` e `body` proprietà del `AEReachAnnouncement` classe:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Se non si desidera caricare le visualizzazioni per se stessi ma si desidera riutilizzare il layout di visualizzazione predefinito annuncio, è possibile apportare semplicemente controller visualizzazione personalizzata estende la classe `AEDefaultAnnouncementViewController`. In questo caso, duplicare il file di ufficio `AEDefaultAnnouncementView.xib` e rinominarla in modo che può essere caricato da controller visualizzazione personalizzata (per un controller denominato `CustomAnnouncementViewController`, è necessario chiamare il file di ufficio `CustomAnnouncementView.xib`).

Per sostituire la categoria predefinita di annunci, è sufficiente registrare il controller di visualizzazione personalizzata per la categoria definita `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Sondaggi possono essere personalizzati nello stesso modo:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Questa volta, l'oggetto fornito `MyCustomPollViewController` necessario estendere `AEPollViewController`. Oppure è possibile scegliere di estese dagli controller predefinito: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Non dimenticare di chiamare `action` (`submitAnswers:` per controller di visualizzazione personalizzato sondaggio) o `exit` metodo prima visualizzazione controller viene chiusa. In caso contrario, le statistiche non verrà inviate (ad esempio non analitica della campagna) e altre campagne importante successiva non si riceveranno fino a quando non si riavvia il processo di applicazione.

##### <a name="implementation-example"></a>Esempio di implementazione

In questa implementazione la visualizzazione degli annunci personalizzato viene caricata da un file XI esterni.

Ad esempio per la personalizzazione di notifiche avanzate, è consigliabile esaminare il codice sorgente dell'implementazione standard.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
