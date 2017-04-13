<properties
    pageTitle="Azure B2C Directory attiva: Chiamare dell'API web da un'applicazione di iOS l'utilizzo di librerie di terze parti | Microsoft Azure"
    description="In questo articolo viene illustrato come creare un'app 'elenco' iOS chiama web Node API utilizzando i token del titolare OAuth 2.0 utilizzo di una raccolta di terze parti"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< tag ms.service= "b2c di directory active" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic= "eroe articolo"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure Active Directory B2C: Chiamare dell'API web da un'applicazione di iOS utilizzo di una raccolta di terze parti

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La piattaforma identità Microsoft utilizza standard aperti, ad esempio OAuth2 e OpenID connettersi. In questo modo agli sviluppatori di sfruttare qualsiasi raccolta in cui l'integrazione con i servizi. Per facilitare gli sviluppatori utilizzando la piattaforma con altre librerie abbiamo scritto alcune procedure dettagliate analogo al seguente vengono utilizzate per illustrare come configurare librerie di terze parti per connettersi alla piattaforma Microsoft identità. La maggior parte delle raccolte in cui implementano [specifiche RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) saranno in grado di connettersi la piattaforma Microsoft Identity.


Se si conosce OAuth2 o OpenID connettersi gran parte di questa configurazione di esempio potrebbe non essere senso all'utente. È consigliabile che cercare una breve [Panoramica del protocollo che sono state descritte di seguito](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Alcune caratteristiche della piattaforma che dispongono di un'espressione in questi standard, ad esempio gestione dei criteri di accesso condizionato e Intune, è necessario usare il nostro Apri origine librerie di identità di Microsoft Azure. 
   
Non tutti gli scenari di Azure Active Directory e funzionalità supportate da piattaforma B2C.  Per determinare se è necessario utilizzare la piattaforma B2C, informazioni sulle [limitazioni B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant. Una directory è un contenitore per tutti gli utenti, App, gruppi e altro. Se non CE già, [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. L'app e web API sono rappresentati da un singolo **ID applicazione** in questo caso, poiché comprendono un'app logica. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un **dispositivo mobile** dell'applicazione.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. È anche necessario seguente in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). Questa app contiene un'esperienza di identità: un segno combinato in e per l'abbonamento. È necessario creare questo criterio di ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando si crea il criterio, assicurarsi di:

- Scegliere il **nome visualizzato** e attributi iscrizione nei criteri.
- Scegliere le richieste di applicazione **nome visualizzato** e **ID** in ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. Deve avere il prefisso `b2c_1_`.  È necessario il nome del criterio in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo avere creato i criteri, si è pronti per creare l'applicazione.


## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Per seguire, è possibile [scaricare l'app come un file ZIP](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) o clonare:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

O scaricare codice completato e iniziare immediatamente: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Scaricare il nxoauth2 di una raccolta di terze parti e avviare un'area di lavoro

Per questa procedura dettagliata verrà utilizzato OAuth2Client da GitHub, una raccolta di OAuth2 per Mac OS X e iOS (cacao & cacao tocco). Questa raccolta si basa sulla bozza 10 della specifica OAuth2. Implementa il profilo di applicazione nativa e supporta l'endpoint di autorizzazione degli utenti finali. Si tratta di tutto che occorre in ordine alla integrat con piattaforma identità Microsoft.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Aggiunta della raccolta per il progetto con CocoaPods

CocoaPods è un responsabile di relazione per i progetti Xcode. Gestisce automaticamente la procedura di installazione precedente.

```
$ vi Podfile
```
Aggiungere questo podfile quanto segue:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

A questo punto caricare podfile utilizzando cocoapods. Verrà creata una nuova area di lavoro di XCode verrà caricato.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>La struttura del progetto

Abbiamo impostato per il progetto nella definizione di base della struttura seguente:

* **Visualizzazione schema** con un riquadro attività
* Una **Visualizzazione delle attività aggiungere** per i dati relativi all'attività selezionata
* **Visualizzazione di accesso** che consente di accedere all'app.

Si passerà a vari file di progetto per aggiungere l'autenticazione. Altre parti del codice, ad esempio il codice visual non è pertinenti identità e vengono fornite automaticamente.

## <a name="create-the-settingsplist-file-for-your-application"></a>Creare il `settings.plist` file dell'applicazione

È più semplice configurare l'applicazione se si dispone di una posizione centralizzata per inserire i valori di configurazione. Consente inoltre di comprendere che cosa significa ogni impostazione dell'applicazione. L' *Elenco delle proprietà* verranno utilizzate come un modo per fornire questi valori all'applicazione.

* Crea/Apri il `settings.plist` file in `Supporting Files` nell'area di lavoro dell'applicazione

* Immettere i valori seguenti (è necessario esaminarli in modo dettagliato breve)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Passiamo a questi elementi in dettaglio.


Per `authURL`, `loginURL`, `bhh`, `tokenURL` si noterà che è necessario immettere il nome del tenant. Questo è il nome tenant del tenant B2C che è stato assegnato all'utente. Ad esempio, `kidventusb2c.onmicrosoft.com`. Se si utilizza il nostro Apri origine librerie di identità di Microsoft Azure si preferisce abbassare questi dati mediante l'endpoint metadati. I professionisti IT il lavoro dell'estrazione questi valori dell'utente.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Il `keychain` valore è il contenitore raccolta NXOAuth2Client verrà utilizzato per creare un portachiavi per archiviare i token. Se si desidera trasmettere Single Sign-on numerosi App è possibile specificare la stessa portachiavi in ognuna delle applicazioni nonché richiede l'uso di tale portachiavi nel entitements XCode. Questo argomento viene descritto nella documentazione di Apple.

Il `<policy name>` alla fine di ogni URL sono le posizioni in cui vuole inserire il criterio appena creato. L'app eseguiranno una chiamata questi criteri a seconda del flusso.

Il `taskAPI` l'Endpoint resto applicherà con il token B2C a uno aggiungere attività o attività di query esistenti. Questo è stato impostato in modo specifico per questo esempio. Non è necessario modificarlo per il corretto funzionamento di esempio.

Il resto di questi valori sono necessari per usare la raccolta, quindi creare posizioni per poter eseguire i valori per il contesto.

Dopo aver creato il `settings.plist` file creato, è necessario codice per la lettura.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configurare una classe AppData per leggere le impostazioni

Questo punto, eseguire un semplice file appena analizza il nostro `settngs.plist` file abbiamo creato in precedenza e apportare in futuro tali disponibile impostazioni a tutte le classi. Dal momento che non si desidera creare una nuova copia dei dati di ogni volta che una classe chiede, verrà utilizzare un motivo Singleton e restituire soltanto la stessa istanza creata ogni volta che viene effettuata una richiesta per le impostazioni

* Creare un `AppData.h` file:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Creare un `AppData.m` file:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

A questo punto è possibile accedere agevolmente i dati chiamando semplicemente `  AppData *data = [AppData getInstance];` tutte le classi come si verrà visualizzata sotto.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurare la raccolta di NXOAuth2Client nel AppDelegate

Raccolta NXOAuthClient richiede alcuni valori di configurazione. Una volta completato è possibile utilizzare il token acquisite chiamare API REST. Poiché è noto che la `AppDelegate` verrà chiamato ogni volta che viene caricato l'applicazione senso è posizionare i valori di configurazione a tale file.
* Apri `AppDelegate.m` file

* Importare alcuni file di intestazione che verrà usata in un secondo momento.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Aggiungere il `setupOAuth2AccountStore` metodo del AppDelegate

È necessario creare un AccountStore e feed quindi i dati vengono letti solo dalla `settings.plist` file.

Esistono alcuni aspetti da tenere presenti relative al B2C servizio a questo punto che renderà più comprensibile questo codice:


1. Azure Active Directory B2C utilizza il *criterio* come previsto per i parametri di query per la richiesta di servizio. In questo modo Azure Active Directory come un servizio indipendente solo per l'applicazione. Per fornire queste query aggiuntivi parametri necessarie per fornire il `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` metodo con i parametri di criteri personalizzati. 

2. Azure Active Directory B2C utilizza ambiti nello stesso modo come altri server OAuth2. Tuttavia poiché l'uso di B2C quanto a autenticazione di un utente come accedere alle risorse alcuni ambiti sono assolutamente necessarie per il flusso per il corretto funzionamento. Si tratta di `openid` ambito. L'identità di Microsoft SDK forniscono automaticamente il `openid` ambito dell'utente in modo che non è possibile che nella configurazione SDK. Poiché si sta utilizzando una raccolta di terze parti, tuttavia, è necessario specificare l'ambito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Successivamente, verificare che si chiama in AppDelegate in `didFinishLaunchingWithOptions:` metodo. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Creare un `LoginViewController` classe che verrà utilizzato per gestire le richieste di autenticazione

Serve una visualizzazione Web per l'accesso account. In questo modo richiedere all'utente di altri fattori come messaggio SMS (se configurati) o fornire messaggi di errore all'utente. Questa opzione è necessario impostare la visualizzazione Web di configurazione e quindi, in un secondo momento, scrivere il codice per gestire i callback che verranno eseguita in visualizzazione Web dal servizio di identità di Microsoft.

* Creare un `LoginViewController.h` classe

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Verrà creato ciascuno di questi metodi riportata di seguito.

> [AZURE.NOTE] 
    Verificare che si associa il `loginView` per la visualizzazione Web effettiva all'interno lo storyboard. In caso contrario non è possibile avere una visualizzazione Web che possono appare quando si è pronti per eseguire l'autenticazione.

* Creare un `LoginViewController.m` classe

* Aggiungere alcuni variabili per eseguire lo stato come abbiamo autenticazione

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Ignorare i metodi di visualizzazione Web per gestire l'autenticazione

Occorre fornire la visualizzazione Web il comportamento che vogliamo quando un utente deve eseguire l'accesso come descritto sopra. È sufficiente, è possibile tagliare e incollare il codice riportato di seguito.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Scrivere codice per gestire il risultato della richiesta OAuth2

È necessario codice che gestirà il redirectURL torno dalla visualizzazione Web. Se non è stato completato, si ripeterà l'operazione. Nel frattempo la raccolta costituiranno l'errore che è possibile vedere nella console o gestire maniera. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configurare le factory di notifica.

Creiamo sullo stato svolto nel `AppDelegate` sopra, ma questa volta abbiamo aggiungerà alcune `NSNotification`s per inviare commenti e suggerimenti che cosa accade nel servizio. È necessario impostare un osservatore che segnala quando nulla assume l'aspetto del token. Una volta è ottenere il token viene restituito l'utente al `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Aggiungere codice che gestisce l'utente ogni volta che viene avviata una richiesta di accesso nativo

Creazione di un metodo che verrà chiamato ogni volta che si dispone di una richiesta di autenticazione. Questo sarà il metodo che in realtà consente di creare una visualizzazione Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Infine, si chiama tutti questi metodi abbiamo scritto sopra ogni volta i `LoginViewController` carica. Facciamo mediante l'aggiunta di questi metodi per il nostro `viewDidLoad` metodo Apple offre

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Ora vengono eseguite con la creazione di quella principale che è possibile interagire con l'applicazione per l'accesso. Dopo che è stato effettuato l'accesso, è necessario utilizzare i token che abbiamo ricevuto. Per cui è necessario creare codice di supporto che esegue la chiamata API REST per tramite questa raccolta.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Creare un `GraphAPICaller` classe per gestire le richieste di un API REST

Esiste una configurazione caricata ogni volta che viene caricato l'app. A questo punto è necessario eseguire inserirvi una volta che un token. 

* Creare un `GraphAPICaller.h` file

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Vedere da questo codice che si creano due metodi: uno per ottenere le attività da un'API e un altro per aggiungere attività all'API.

Ora che è stato creato l'interfaccia, è possibile aggiungere l'implementazione effettiva:

* Creare un`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, creare ed eseguire l'app in Xcode. Iscrizione o accedere all'app e creare attività per un utente effettuato l'accesso. Disconnettersi e accedere come un altro utente e creare attività di tale utente.

Si noti che le attività sono archiviate per utente sull'API, poiché l'API estrae l'identità dell'utente da token di accesso che riceve.


## <a name="next-steps"></a>Passaggi successivi

È possibile procedere in argomenti più avanzati B2C. È possibile:

[Chiamare un web Node API da un'app web Node]()

[Personalizzazione dell'esperienza utente per un'app B2C]()
