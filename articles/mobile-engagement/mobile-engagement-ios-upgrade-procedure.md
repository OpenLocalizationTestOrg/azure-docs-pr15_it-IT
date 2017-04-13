<properties
    pageTitle="Azure coinvolgimento Mobile iOS SDK aggiornare procedura | Microsoft Azure"
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

#<a name="upgrade-procedures"></a>Procedure di aggiornamento

Se è già stata integrata una versione precedente di impegno nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

Per ogni nuova versione di SDK è necessario sostituire (rimuovere e reimportare in xcode) le cartelle EngagementSDK ed EngagementReach.

##<a name="from-300-to-400"></a>Da 3.0.0 a 4.0.0

### <a name="xcode-8"></a>8 XCode
8 XCode è obbligatorio a partire dalla versione 4.0.0 di SDK.

> [AZURE.NOTE] Se si dipende molto XCode 7 è possibile utilizzare [iOS coinvolgimento SDK v3.2.4](https://aka.ms/r6oouh). Esiste un errore noto sul modulo portata di questa versione precedente mentre è in esecuzione in dispositivi iOS 10: le notifiche di sistema non sono prese in considerazione. Per risolvere il problema è necessario implementare API obsoleta `application:didReceiveRemoteNotification:` nell'app delegare come indicato di seguito:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Non è consigliabile questa soluzione alternativa** come questo comportamento può modificare qualsiasi aggiornamento della versione imminenti iOS (anche minore) perché questo iOS API è deprecata. È necessario passare a 8 XCode più presto possibile.

### <a name="usernotifications-framework"></a>Framework UserNotifications
È necessario aggiungere il `UserNotifications` framework le fasi creare.

in Gestione progetti, aprire il riquadro di progetto e selezionare la destinazione corretta. Quindi aprire la scheda **"Realizzare fasi"** e **"binario con librerie di collegamento"** menu aggiungere framework `UserNotifications.framework` -impostare il collegamento come`Optional`

### <a name="application-push-capability"></a>Funzionalità di push applicazione
8 XCode possono reimpostare l'app push funzionalità, verificare `capability` scheda dell'obiettivo selezionato.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Aggiungere il nuovo codice di registrazione di notifica iOS 10
Il frammento di codice precedente per registrare l'app alle notifiche ancora funziona ma utilizza API obsolete mentre è in esecuzione in iOS 10.

Importazione di `User Notification` framework:

        #import <UserNotifications/UserNotifications.h> 

In delegato l'applicazione `application:didFinishLaunchingWithOptions` Sostituisci metodo:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

da:

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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se si dispone già di implementazione UNUserNotificationCenterDelegate

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

##<a name="from-200-to-300"></a>Da 2.0.0 a 3.0.0
Eliminare il supporto per iOS 4. Inizio di questa versione la destinazione della distribuzione dell'applicazione in uso deve essere almeno iOS 6.

Se si usa portata nell'applicazione, è necessario aggiungere `remote-notification` valore per il `UIBackgroundModes` in forma di matrice nel file di Info.plist per ricevere notifiche remote.

Il metodo `application:didReceiveRemoteNotification:` deve essere sostituito da `application:didReceiveRemoteNotification:fetchCompletionHandler:` al delegato dell'applicazione.

"AEPushDelegate.h" è deprecata interfaccia ed è necessario rimuovere tutti i riferimenti. Rimuovendo `[[EngagementAgent shared] setPushDelegate:self]` e i metodi di delegato dal proprio delegato applicazione:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>Da 1.16.0 a 2.0.0
Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio di Capptain fornito dal Capptain SAS in un'app con tecnologia Azure Mobile coinvolgimento.
Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire la migrazione a 1.16 prima di tutto, quindi applicare la procedura seguente.

>[AZURE.IMPORTANT] Capptain e coinvolgimento Mobile non sono gli stessi servizi e la procedura indicata di seguito viene evidenziato solo come eseguire la migrazione dell'applicazione client. Migrazione SDK nell'app verrà non eseguire la migrazione dei dati da server Capptain ai server coinvolgimento Mobile

### <a name="agent"></a>Agente

Il metodo `registerApp:` è stata sostituita dal nuovo metodo `init:`. Il delegato applicazione devono essere aggiornate conseguenza e Usa stringa di connessione:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Verifica SmartAd è stata rimossa dal SDK è sufficiente rimuovere tutte le istanze di `AETrackModule` classe

### <a name="class-name-changes"></a>Modifiche ai nomi classe

Durante l'attribuzione dei marchi usando, sono disponibili due classe/nomi di file che devono essere modificate.

Tutte le classi con "CP" precedute vengono rinominate con prefisso "UA".

Esempio:

-   `CPModule.h`è stato rinominato `AEModule.h`.

Tutte le classi con "Capptain" precedute vengono rinominate con prefisso "Impegno".

Esempi:

-   La classe `CapptainAgent` è stato rinominato `EngagementAgent`.
-   La classe `CapptainTableViewController` è stato rinominato `EngagementTableViewController`.
-   La classe `CapptainUtils` è stato rinominato `EngagementUtils`.
-   La classe `CapptainViewController` è stato rinominato `EngagementViewController`.
