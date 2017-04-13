<properties
    pageTitle="Azure coinvolgimento Mobile iOS Panoramica SDK | Microsoft Azure"
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

#<a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK per Azure Mobile coinvolgimento

Iniziare da qui per visualizzare tutti i dettagli su come integrare Azure Mobile impegno in un'App per iOS. Se si desidera sperimentare prima di tutto, assicurarsi di eseguire le [esercitazioni di 15 minuti](mobile-engagement-ios-get-started.md).

Fare clic per visualizzare [Il contenuto SDK](mobile-engagement-ios-sdk-content.md)

##<a name="integration-procedures"></a>Procedure di integrazione
1. Iniziare qui: [come integrare impegno Mobile nell'app iOS](mobile-engagement-ios-integrate-engagement.md)

2. Per le notifiche: [come integrare portata (notifiche) nell'app iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Contrassegnare l'implementazione di piano: [come usare il coinvolgimento Mobile avanzate tagging API nell'app iOS](mobile-engagement-ios-use-engagement-api.md)


##<a name="release-notes"></a>Note sulla versione

### <a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notifica fissa non prese in considerazione in dispositivi iOS 10.
-   Impostare come deprecato XCode 7.

Per la versione precedente vedere le [note sulla versione completa](mobile-engagement-ios-release-notes.md)

##<a name="upgrade-procedures"></a>Procedure di aggiornamento

Se è già stata integrata una versione precedente di impegno nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

È necessario eseguire alcune procedure notato diverse versioni di vedere SDK complete [Le procedure di aggiornamento](mobile-engagement-ios-upgrade-procedure.md).

Per ogni nuova versione di SDK è necessario sostituire (rimuovere e reimportare in xcode) le cartelle EngagementSDK ed EngagementReach.

###<a name="from-300-to-400"></a>Da 3.0.0 a 4.0.0

### <a name="xcode-8"></a>8 XCode
8 XCode è obbligatorio a partire dalla versione 4.0.0 di SDK.

> [AZURE.NOTE] Se si dipende molto XCode 7 è possibile utilizzare [iOS coinvolgimento SDK v3.2.4](https://aka.ms/r6oouh). Esiste un errore noto sul modulo portata di questa versione precedente mentre è in esecuzione in dispositivi iOS 10: le notifiche di sistema non sono prese in considerazione. Per risolvere il problema è necessario implementare API obsoleto `application:didReceiveRemoteNotification:` nell'app delegare come indicato di seguito:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Non è consigliabile questa soluzione alternativa** come questo comportamento può modificare qualsiasi aggiornamento della versione imminenti iOS (anche minore) perché questo iOS API è deprecata. È necessario passare a 8 XCode più presto possibile.

#### <a name="usernotifications-framework"></a>Framework UserNotifications
È necessario aggiungere il `UserNotifications` framework le fasi creare.

in Gestione progetti, aprire il riquadro di progetto e selezionare la destinazione corretta. Quindi aprire la scheda **"Realizzare fasi"** e **"binario con librerie di collegamento"** menu aggiungere framework `UserNotifications.framework` -impostare il collegamento come`Optional`

#### <a name="application-push-capability"></a>Funzionalità di push applicazione
8 XCode possono reimpostare l'app push funzionalità, verificare `capability` scheda dell'obiettivo selezionato.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Aggiungere il nuovo codice di registrazione di notifica iOS 10
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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se si dispone già di implementazione UNUserNotificationCenterDelegate

SDK include la propria implementazione del protocollo UNUserNotificationCenterDelegate. Viene utilizzato da SDK per monitorare il ciclo di vita delle notifiche di impegno nei dispositivi che eseguono in iOS 10 o versione successiva. Se il SDK rileva il delegato non utilizza la propria implementazione poiché può essere presente un solo delegato UNUserNotificationCenter per ogni applicazione. Questo significa che è necessario aggiungere logica di impegno per il proprio delegato.

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