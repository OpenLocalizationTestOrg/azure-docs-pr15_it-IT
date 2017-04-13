<properties
    pageTitle="Azure coinvolgimento Mobile iOS integrazione SDK | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-on-ios"></a>Come integrare impegno in iOS

> [AZURE.SELECTOR]
- [Universale di Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Questa procedura descrive il modo più semplice attivare del coinvolgimento Analitica e il monitoraggio delle funzioni nell'applicazione iOS.

SDK coinvolgimento richiede iOS6 + e Xcode 8: la destinazione della distribuzione dell'applicazione in uso deve essere almeno iOS 6.

> [AZURE.NOTE]
> Se si dipende molto XCode 7 è possibile utilizzare [iOS coinvolgimento SDK v3.2.4](https://aka.ms/r6oouh). Esiste un errore noto sul modulo portata di questa versione precedente durante l'esecuzione in dispositivi iOS 10 possibile notare un' [integrazione di modulo portata](mobile-engagement-ios-integrate-engagement-reach.md) per altri dettagli. Se si sceglie di usare v3.2.4 SDK quindi ignorare il `UserNotifications.framework` importare nel passaggio successivo.

La procedura seguente è sufficienti per attivare il report del log necessari per calcolare tutte le statistiche relative agli utenti, sessioni, attività, anomalo e Technicals. Report sui registri necessari per calcolare altre statistiche come eventi, gli errori e processi devono essere eseguiti manualmente tramite l'API di impegno (vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app iOS](mobile-engagement-ios-use-engagement-api.md) poiché queste statistiche sono dipende dall'applicazione.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporare SDK coinvolgimento nel progetto iOS

- Scaricare il file iOS SDK da [qui](http://aka.ms/qk2rnj).

- Aggiungere al progetto di iOS SDK coinvolgimento: in Xcode, fare clic con il pulsante destro del progetto e selezionare **"Aggiungi file per..."** e scegliere il `EngagementSDK` cartella.

- Coinvolgimento richiede ulteriori Framework per l'uso: in Gestione progetti, aprire il riquadro di progetto e selezionare la destinazione corretta. Quindi aprire la scheda **"Realizzare fasi"** e **"binario con librerie di collegamento"** menu aggiungere questi Framework:

    -   `UserNotifications.framework`-impostare il collegamento come`Optional`
    -   `AdSupport.framework`-impostare il collegamento come`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] È possibile rimuovere il framework degli AdSupport. Coinvolgimento deve questo framework per raccogliere le IDFA. Tuttavia, può essere disattivata insieme IDFA \<ios-sdk-coinvolgimento-idfa\> ai nuovi criteri di Apple relativo a questo ID.

##<a name="initialize-the-engagement-sdk"></a>Inizializzare coinvolgimento SDK

È necessario modificare il delegato applicazione:

-   Nella parte superiore del file di implementazione consente di importare l'agente di impegno:

        [...]
        #import "EngagementAgent.h"

-   Inizializzare coinvolgimento all'interno del metodo '**applicationDidFinishLaunching:**'o'**applicazione: didFinishLaunchingWithOptions:**':

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Report di base

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Metodo consigliato: overload il `UIViewController` classi

Per attivare il report di tutti i registri necessari affinché impegno per calcolare gli utenti, sessioni, attività, anomalo e le statistiche tecniche, è possibile semplicemente apportare tutte le `UIViewController` classi secondari ereditano il `EngagementViewController` classi (stessa regola per `UITableViewController`  - \> `EngagementTableViewController`).

**Senza impegno:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Con coinvolgimento:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternare metodo: chiamare `startActivity()` manualmente

Se non è possibile o non si desidera overload il `UIViewController` classi, invece, è possibile iniziare l'attività chiamando `EngagementAgent`del metodi direttamente.

> [AZURE.IMPORTANT] IOS SDK chiama automaticamente il `endActivity()` metodo alla chiusura dell'applicazione. In questo modo, è *altamente* consigliati per chiamare la `startActivity` metodo ogni volta che l'attività dell'utente modificare e su *mai* chiamare il `endActivity` metodo, poiché questo metodo impone la sessione corrente per terminare.

##<a name="location-reporting"></a>Posizione dei report

Condizioni di Apple del servizio non consentano alle applicazioni di utilizzare solo a scopo di statistiche di rilevamento del percorso. In questo modo, è consigliabile attivare posizione report solo se l'applicazione utilizza anche la posizione per un altro motivo.

A partire da iOS 8, è necessario fornire una descrizione per l'utilizzo l'app servizi percorso mediante l'impostazione di una stringa per il tasto [NSLocationWhenInUseUsageDescription] o [NSLocationAlwaysUsageDescription] nel file Info.plist dell'applicazione. Se si vuole percorso di report in background con coinvolgimento, aggiungere la chiave NSLocationAlwaysUsageDescription. In tutti gli altri casi, aggiungere la chiave NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Area Prato posizione report

Area Prato percorso reporting consente di inviare una segnalazione del paese, l'area geografica e località associati ai dispositivi. Questo tipo di posizione reporting utilizza solo percorsi di rete (basati su ID di cella o WIFI). L'area del dispositivo viene segnalato una sola volta per sessione. Non è mai utilizzato il GPS e pertanto questo tipo di report di posizione è pochi (non di esprimere) relativo impatto sulla pila.

Aree segnalate vengono utilizzate per calcolare geografiche statistiche sugli utenti, sessioni, eventi e gli errori. Possono inoltre essere utilizzati come criterio in campagne di mano. Grazie all' [API di dispositivi], è possibile recuperare l'ultima area noto segnalato per un dispositivo.

Per attivare il report percorso area Prato, aggiungere la riga seguente dopo inizializzazione dell'agente di impegno:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Posizione dei report in tempo reale

In tempo reale posizione reporting consente di inviare una segnalazione latitudine e longitudine associati ai dispositivi. Per impostazione predefinita, questo tipo di posizione reporting utilizza solo percorsi di rete (basati su ID di cella o WIFI) e la segnalazione è attiva solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione).

Posizioni in tempo reale sono *non* utilizzati per calcolare statistiche. Lo scopo solo consiste nel consente l'uso di in tempo reale geografico-recinzione \<geofencing di gruppo di destinatari portata\> criterio nelle campagne di mano.

Per attivare il report posizione in tempo reale, aggiungere la riga seguente dopo l'inizializzazione dell'agente di impegno:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>Creazione di report in base a GPS

Per impostazione predefinita, in tempo reale posizione reporting utilizza solo percorsi di rete in base a. Per abilitare l'utilizzo dei percorsi di GPS in base a (che sono molto più preciso), aggiungere:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Creazione di report di sfondo

Per impostazione predefinita, in tempo reale posizione reporting è attivo solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione). Per abilitare la segnalazione anche in background, aggiungere:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Quando l'applicazione viene eseguita in background, solo in percorsi di rete vengono segnalati, anche se è abilitata la GPS.

Implementazione di questa funzione eseguiranno una chiamata [startMonitoringSignificantLocationChanges] quando l'applicazione rileva lo sfondo. Tenere presente automaticamente verrà riavviare l'applicazione in background se arriva un nuovo evento.

##<a name="advanced-reporting"></a>Segnalazione avanzate

Facoltativamente, se si desidera segnalare eventi specifici dell'applicazione, errori e processi, è necessario utilizzare l'API coinvolgimento mediante i metodi del `EngagementAgent` classe. Un oggetto di questa classe può essere recuperato chiamando il `[EngagementAgent shared]` metodo statico.

API coinvolgimento consente di utilizzare tutte le funzionalità avanzate di impegno e dettaglio nelle come usare l'API coinvolgimento nel iOS (nonché come nella documentazione tecnica del `EngagementAgent` classe).

##<a name="disable-idfa-collection"></a>Disabilitare la raccolta IDFA

Per impostazione predefinita, coinvolgimento utilizzerà [IDFA] per identificare un utente. Ma se non si usa la pubblicità in un' posizione nell'app, potrebbe rifiutato dal processo di revisione App Store. Raccolta IDFA può essere disattivata mediante l'aggiunta di macro preprocessore `ENGAGEMENT_DISABLE_IDFA` nel file pch (o il `Build Settings` dell'applicazione). In questo modo non c'è alcun riferimento a `ASIdentifierManager`, `advertisingIdentifier` o `isAdvertisingTrackingEnabled` nella build dell'applicazione.

Integrazione nel file **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

È possibile verificare che la raccolta IDFA disabilitata correttamente nell'applicazione selezionando i log di test coinvolgimento. Vedere i Test di integrazione\<ios-sdk-coinvolgimento-test-idfa\> documentazione per ulteriori informazioni.

##<a name="disable-log-reporting"></a>Disattivare la segnalazione di log

### <a name="using-a-method-call"></a>Eseguendo una chiamata di metodo

Se si vuole impegno per interrompere l'invio dei registri, è possibile chiamare:

    [[EngagementAgent shared] setEnabled:NO];

Questa chiamata è persistente: utilizzerà `NSUserDefaults` per memorizzare le informazioni.

È possibile attivare log reporting nuovamente chiamando la stessa funzione con `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integrazione del raggruppamento di impostazioni

Anziché la chiamata a questa funzione, è possibile integrare questa impostazione direttamente in esistenti `Settings.bundle` file. La stringa `engagement_agent_enabled` deve essere utilizzato come un identificatore preferenza che deve essere associati a un'opzione Attiva/Disattiva (`PSToggleSwitchSpecifier`).

Nell'esempio seguente di `Settings.bundle` viene illustrato come implementare:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Dispositivo API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
