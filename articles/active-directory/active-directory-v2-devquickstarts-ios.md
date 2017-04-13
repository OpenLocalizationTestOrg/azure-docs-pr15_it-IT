<properties
    pageTitle="IOS v 2.0 di Azure Active Directory App | Microsoft Azure"
    description="Come creare un'app iOS accede account aziendale o dell'istituto di istruzione e gli utenti che dispongono entrambi account Microsoft personale utilizzando le raccolte di terze parti."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Aggiungere accesso a un'app iOS con una raccolta di terze parti API grafico utilizzando l'endpoint v 2.0

La piattaforma identità Microsoft utilizza standard aperti, ad esempio OAuth2 e OpenID connettersi. Gli sviluppatori possono utilizzare raccolta per l'integrazione con i servizi. Per consentire agli sviluppatori di utilizzare la piattaforma con altre raccolte, abbiamo scritto alcune procedure dettagliate analogo al seguente per dimostrare come configurare le librerie di terze parti per connettersi alla piattaforma Microsoft identità. La maggior parte delle raccolte in cui implementano [specifiche RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) possono connettersi alla piattaforma di identità di Microsoft.

Con l'applicazione creata in questa procedura dettagliata, gli utenti possono accedere alla propria organizzazione e quindi cercare ad altri utenti all'interno dell'organizzazione tramite l'API di grafico.

Se si conosce OAuth2 o OpenID connettersi, la maggior parte di questo esempio di configurazione non può avere senso all'utente. È consigliabile leggere [i protocolli v 2.0 - OAuth 2.0 autorizzazione codice flusso](active-directory-v2-protocols-oauth-code.md) per lo sfondo.


> [AZURE.NOTE]
    Alcune caratteristiche della piattaforma che dispongono di un'espressione in standard OAuth2 o OpenID connettersi, ad esempio accesso condizionato e gestione di criteri Intune, è necessario usare il nostro Apri origine librerie di identità di Microsoft Azure.

Endpoint v 2.0 non supporta tutti gli scenari di Azure Active Directory e funzionalità.

> [AZURE.NOTE]
    Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Scaricare codice da GitHub
Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o duplicare la struttura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

È anche possibile scaricare il campione e iniziare immediatamente:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app [portale di registrazione dell'applicazione](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o seguire la procedura dettagliata su [come registrare un'app all'endpoint v 2.0](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copiare l' **Id applicazione di** assegnare all'app perché sarà necessario breve.
- Aggiungere la piattaforma **Mobile** per l'app.
- Copiare il **Reindirizzamento URI** dal portale. È necessario usare il valore predefinito di `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Scaricare la raccolta di NXOAuth2 di terze parti e creare un'area di lavoro

Per questa procedura dettagliata, occorre usare OAuth2Client da GitHub, ovvero una libreria OAuth2 per Mac OS X e iOS (cacao e sue tocco). Questa raccolta si basa sulla bozza 10 della specifica OAuth2. Implementa il profilo di applicazione nativa e supporta l'endpoint di autorizzazione dell'utente. Si tratta di tutte le operazioni che necessarie per l'integrazione con la piattaforma di identità di Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Aggiungere la raccolta a un progetto utilizzando CocoaPods

CocoaPods è un responsabile di relazione per i progetti Xcode. Gestisce automaticamente la procedura di installazione precedente.

```
$ vi Podfile
```
1. Aggiungere questo podfile quanto segue:

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Caricare il podfile utilizzando CocoaPods. Verrà creata una nuova area di lavoro Xcode che verrà caricato.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Esplorare la struttura del progetto

La struttura seguente viene configurata per il progetto nella definizione di base:

- Visualizzazione schema con una ricerca UPN
- Una visualizzazione di dettaglio per i dati relative all'utente selezionato
- Una visualizzazione di accesso la nel punto in cui un utente può accedere all'app per eseguire una query nel grafico

Passiamo a vari file nella definizione di base per aggiungere l'autenticazione. Altre parti del codice, ad esempio codice visual non riguardano identità ma fornite automaticamente.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurare il file settings.plst nella raccolta

-   Nel progetto di Guida introduttiva, aprire la `settings.plist` file. Sostituire i valori degli elementi nella sezione per riflettere i valori che è stato utilizzato nel portale di Azure. Il codice farà riferimento questi valori ogni volta che viene utilizzato Active Directory Authentication Library.
    -   Il `clientId` corrisponde all'ID client dell'applicazione che sono stati copiati dal portale.
    -   Il `redirectUri` è l'URL di reindirizzamento che ha fornito il portale.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurare la raccolta di NXOAuth2Client nel LoginViewController

Raccolta NXOAuth2Client richiede alcuni valori di configurazione. Dopo aver completato l'attività, è possibile utilizzare il token acquisito per chiamare l'API di grafico. Poiché `LoginView` sarà chiamato in qualsiasi momento è necessario eseguire l'autenticazione, è opportuno posizionare i valori di configurazione a tale file.

- Aggiungere alcuni valori per il `LoginViewController.m` file per impostare il contesto per l'autenticazione e l'autorizzazione. Informazioni dettagliate sui valori eseguire il codice.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Diamo un'occhiata ulteriori informazioni sul codice.

Prima stringa riguarda `scopes`.  Il `User.Read` valore consente di leggere la base del profilo dell'utente l'accesso al.

Sono disponibili ulteriori su tutti gli ambiti disponibili in [Microsoft Graph ambiti di autorizzazione](https://graph.microsoft.io/docs/authorization/permission_scopes).

Per `authURL`, `loginURL`, `bhh`, e `tokenURL`, è necessario utilizzare i valori indicati in precedenza. Se si usa Apri origine librerie di identità di Microsoft Azure, abbiamo abbassare questi dati dell'utente utilizzando il nostro endpoint metadati. I professionisti IT il lavoro dell'estrazione questi valori dell'utente.

Il `keychain` valore è il contenitore raccolta NXOAuth2Client verrà utilizzato per creare un portachiavi per archiviare i token. Se si desidera ottenere single sign-on (SSO) tra le applicazioni numerosi, è possibile specificare la stessa portachiavi in ognuna delle applicazioni e richiede l'uso di tale portachiavi per i diritti Xcode. Questo è descritto nella documentazione di Apple.

Il resto di questi valori sono necessari per utilizzare la libreria e creare posizioni per poter eseguire i valori per il contesto.

### <a name="create-a-url-cache"></a>Creare una cache URL

All'interno di `(void)viewDidLoad()`, che viene sempre chiamato dopo la visualizzazione è stata caricata, il codice seguente primes una cache per l'uso.

Aggiungere il codice seguente:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Creare una visualizzazione Web per l'accesso

Una visualizzazione Web può richiedere all'utente di altri fattori come messaggio SMS (se configurati) o restituire messaggi di errore all'utente. Qui è possibile impostare la visualizzazione Web di configurazione e quindi, in un secondo momento, scrivere il codice per gestire i callback che verranno eseguita in visualizzazione Web dalla pagina Servizi di identità.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Ignorare i metodi di visualizzazione Web per gestire l'autenticazione

Per conoscere la visualizzazione Web cosa accade quando un utente deve effettuare l'accesso come descritto in precedenza, è possibile incollare il codice riportato di seguito.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Scrivere codice per gestire il risultato della richiesta OAuth2

Il codice seguente consente di gestire redirectURL che restituisce la visualizzazione Web. Se l'autenticazione non riuscito, il codice ripeterà l'operazione. Nel frattempo, la raccolta costituiranno l'errore che è possibile vedere nella console o gestire in modo asincrono.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurare il contesto OAuth (noto come archivio account)

Qui è possibile chiamare `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` l'archivio account condiviso per ogni servizio che si desidera che l'applicazione in grado di accedere. Il tipo di account è una stringa che viene utilizzata come un identificatore per un certo servizio. Poiché si accede all'API di grafico, il codice fa riferimento a tale come `"myGraphService"`. È quindi impostare un osservatore che indica quando nulla assume l'aspetto del token. Dopo aver ottenuto il token, si torna all'utente di eseguire il backup per la `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Impostare la visualizzazione schema per eseguire ricerche e visualizzare gli utenti di API di grafico

Un'app Controller di visualizzazione principale (MVC) che consente di visualizzare i dati restituiti nella griglia è lo scopo di questa procedura dettagliata e molte esercitazioni online illustrano come creare uno. Tutto il codice è nel file di base. Tuttavia, è necessario gestire alcuni aspetti in questa applicazione MVC:

* Intercetta quando l'utente digita qualcosa nel campo di ricerca
* Fornire un oggetto di dati al MasterView in modo che è possibile visualizzare i risultati della griglia

È necessario eseguire quelli riportata di seguito.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Aggiungere un controllo per verificare se è stato effettuato

L'applicazione non offre se l'utente non è stato eseguito l'accesso, pertanto è smart verificare se è già presente un token nella cache. In caso contrario, viene reindirizzati LoginView per l'utente ad accedere. Se si ricorderà, il modo migliore per eseguire azioni quando viene caricata una visualizzazione consiste nell'utilizzare la `viewDidLoad()` metodo Apple offre.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Aggiornare la visualizzazione tabella quando vengono ricevuti dati

Quando l'API grafico restituisce i dati, è necessario visualizzare i dati. Per semplicità, Ecco tutto il codice per aggiornare la tabella. È possibile incollare solo i valori corretti nel codice MVC standard.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Consente di chiamare l'API grafico quando un utente immette nel campo di ricerca

Quando si digita una ricerca nella casella, è necessario che shove all'API di grafico. Il `GraphAPICaller` classe, da generare il codice riportato di seguito, separa le funzionalità di ricerca dalla presentazione. Per il momento possibile scrivere il codice che feed i caratteri di ricerca per l'API di grafico. A tale scopo occorre fornire un metodo denominato `lookupInGraph`, che accetta la stringa da cercare.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Scrivere una classe di supporto per accedere all'API di grafico

Si tratta di base dell'applicazione. Mentre il resto è stato inserito codice nel modello di MVC predefinito presso Apple, di seguito è scrivere codice per eseguire query su graph durante la digitazione e quindi tornare tali dati. Ecco il codice e una spiegazione dettagliata lo segue.

### <a name="create-a-new-objective-c-header-file"></a>Creare un nuovo file di intestazione C obiettivo

Nome del file `GraphAPICaller.h`e aggiungere il codice riportato di seguito.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Di seguito viene visualizzato un metodo specificato accetta una stringa che restituisce un completionBlock. CompletionBlock, come si può immaginare, verranno aggiornati nella tabella fornendo un oggetto con dati compilata in tempo reale come ricerche utente.


### <a name="create-a-new-objective-c-file"></a>Creare un nuovo file C obiettivo

Nome del file `GraphAPICaller.m`e aggiungere il metodo seguente.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Eseguiamo questo metodo in modo dettagliato.

Il codice di è nel `NXOAuth2Request`, metodo che accetta i parametri che è già stato definito nel file settings.plist.

Il primo passaggio consiste nel creare la chiamata API grafico destra. Poiché si sta chiamando `/users`, è necessario che accodandola alla risorsa API grafico insieme alla versione. È opportuno inserire gli elementi in un file di impostazioni esterni perché questi può essere modificato con l'API evoluzione.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

È necessario specificare i parametri che fornirà anche a partecipare alla chiamata API di grafico. È *molto importante* non inserire i parametri nell'endpoint risorsa perché che annullate per tutti i caratteri conforme non URI in fase di esecuzione. Tutto il codice di query deve essere fornito nei parametri.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

È possibile notare si chiama un `convertParamsToDictionary` metodo che non ancora stato scritto. Di seguito farlo ora alla fine del file:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Successivamente, utilizzare la `NXOAuth2Request` metodo per tornare dati dall'API nel formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Infine, diamo un'occhiata come reimpostare i dati di MasterViewController. I dati viene restituito come tali ed sono necessario essere deserializzato e caricati in un oggetto in grado di utilizzare il MainViewController. A questo scopo è la struttura di una `User.m/h` file che consente di creare un oggetto utente. Inserire l'oggetto utente con le informazioni nel grafico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Eseguire l'esempio

Se è stato usato la struttura o seguito insieme procedura dettagliata che l'applicazione dovrebbe essere in esecuzione. Avviare il simulatore e fare clic su **Accedi** per usare l'applicazione.

## <a name="get-security-updates-for-our-product"></a>Ottenere gli aggiornamenti di sicurezza per il prodotto

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare il [TechCenter di sicurezza](https://technet.microsoft.com/security/dd252948) e sottoscrivono gli avvisi di avviso di sicurezza.
