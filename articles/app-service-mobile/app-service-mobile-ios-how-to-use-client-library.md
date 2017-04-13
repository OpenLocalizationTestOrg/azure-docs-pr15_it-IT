<properties
    pageTitle="Come usare iOS SDK per App Mobile Azure"
    description="Come usare iOS SDK per App Mobile Azure"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Come usare iOS libreria Client per le App per dispositivi mobili Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida viene descritto come eseguire gli scenari comuni utilizzando l' ultima [iOS App Mobile Azure SDK][1]. Se ha familiarità con App Mobile Azure, prima completo [Avvio rapido di Azure Mobile App] per creare un back-end, creare una tabella e scaricare un progetto di Xcode iOS predefinite. In questa guida l'attenzione su iOS il lato client SDK. Per ulteriori informazioni sul SDK sul lato server di back-end, vedere oggetti di SDK Server.

## <a name="reference-documentation"></a>Documentazione di riferimento

La documentazione di riferimento per il client di iOS SDK si trova in: [App Mobile Azure iOS riferimento Client][2].

## <a name="supported-platforms"></a>Piattaforme supportate

IOS SDK supporta progetti obiettivo C, progetti Swift 2.2 e 2.3 Swift progetti per iOS versioni 8.0 o versione successive.

L'autenticazione "server del flusso di cassa" utilizza una visualizzazione Web per l'interfaccia utente presentato.  Se il dispositivo non è in grado di presentare un UI WebView, sarà necessario un altro metodo di autenticazione che non è compreso l'ambito del prodotto.  
Questo SDK che quindi non è adatto per tipo di controllo o dispositivi con restrizioni.

##<a name="Setup"></a>Il programma di installazione e i prerequisiti

Questa guida presuppone che sia stato creato un back-end con una tabella. Questa guida presuppone che la tabella con lo stesso schema le tabelle in queste esercitazioni. Sul presupposto che nel codice, si fa riferimento `MicrosoftAzureMobile.framework` e importare `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Procedura: creare Client

Per accedere a un back-end App Mobile Azure nel progetto, creare un `MSClient`. Sostituire `AppUrl` con l'URL di app. È possibile lasciare `gatewayURLString` e `applicationKey` vuoto. Se è stato configurato un gateway per l'autenticazione, popolare `gatewayURLString` con l'URL di gateway.

**Obiettivo-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Procedura: creare il riferimento di tabella

Accesso o aggiornare i dati, creare un riferimento alla tabella back-end. Sostituire `TodoItem` con il nome della tabella

**Obiettivo-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Procedura: eseguire Query sui dati

Per creare una query di database, eseguire una query di `MSTable` oggetto. La query seguente recupera tutti gli elementi `TodoItem` e registra il testo di ogni elemento.

**Obiettivo-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Procedura: filtrare i dati restituiti.

Per filtrare i risultati, sono disponibili numerose opzioni disponibili.

Per filtrare utilizzando un predicato, utilizzare un `NSPredicate` e `readWithPredicate`. I filtri seguenti restituito dati per trovare solo gli elementi Todo incompleti.

**Obiettivo-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Procedura: utilizzare MSQuery

Per eseguire una query complessa (incluso l'ordinamento e suddivisione in pagine), creare un `MSQuery` un oggetto, direttamente o utilizzando un predicato:

**Obiettivo-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`Consente di controllare diverse comportamenti per le query.

* Specificare l'ordine dei risultati
* Limitare i campi da restituire
* Limitare il numero di record per restituire
* Specificare il numero totale di risposta
* Specificare i parametri di stringa di query personalizzati nella convocazione
* Applicare altre funzioni

Eseguire un `MSQuery` query chiamando `readWithCompletion` sull'oggetto.

## <a name="sorting"></a>Procedura: ordinare i dati con MSQuery

Per ordinare i risultati, esaminiamo un esempio. Per ordinare, "testo" campo crescente quindi decrescente 'completa', richiamare `MSQuery` come illustrato di seguito:

**Obiettivo-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Procedura: limitare campi ed espandere i parametri di stringa di Query con MSQuery

Per limitare i campi da recuperare in una query, specificare i nomi dei campi nella proprietà **selectFields** . In questo esempio restituisce solo il testo e i campi completati:

**Obiettivo-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Per includere i parametri di stringa di query aggiuntivi nella richiesta di server (ad esempio perché li usa uno script sul lato server personalizzato), popolare `query.parameters` come illustrato di seguito:

**Obiettivo-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Procedura: configurare le dimensioni della pagina

Con App Mobile Azure, le dimensioni della pagina controlla il numero di record che vengono inseriti in un momento dalle tabelle back-end. Una chiamata a `pull` dati vengono quindi batch dei dati, in base a questo formato, fino a quando non sono presenti più record in modo da estrarre.

È possibile configurare un formato di pagina utilizzando **MSPullSettings** come illustrato di seguito. Le dimensioni della pagina predefinita sono 50 e nell'esempio seguente viene modificato su 3.

È possibile configurare un formato di pagina diversi per migliorare le prestazioni. Se si dispone di un numero elevato di record di dati di piccole dimensioni, una dimensione pagina elevata riduce il numero di andata e ritorno server. 

Questa impostazione controlla solo le dimensioni della pagina sul lato client. Se il client richiede una dimensione maggiore per pagina superiore a quello supportato back-end App Mobile, le dimensioni della pagina sono impostata il numero massimo che di back-end è configurato per supportare. 

Questa impostazione è il _numero_ di record di dati, non la _dimensione in byte_.

Se si aumenta dimensione della pagina client, [è anche necessario aumentare le dimensioni di pagina nel server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Obiettivo-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Procedura: inserire i dati

Per inserire una nuova riga alla tabella, creare un `NSDictionary` e richiamare `table insert`. Se è abilitata [Dinamiche dello Schema] , back-end per dispositivi mobili servizio App Azure genera automaticamente le nuove colonne in base alla `NSDictionary`.

Se `id` non viene fornito, back-end genera automaticamente un nuovo ID univoco. Specificare il proprio `id` per usare la posta elettronica indirizzi, nomi utente, o i valori personalizzati come ID. Fornire il proprio ID possa agevolare la logica di database aziendali e di join.

Il `result` contiene la nuova voce che è stata inserita. A seconda della logica server che abbiano dati aggiuntivi o modificati rispetto ai quali è stato passato al server.

**Obiettivo-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Procedura: modifica dei dati

Per aggiornare una riga esistente, modificare un elemento e chiamare `update`:

**Obiettivo-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

In alternativa, fornire l'ID di riga e il campo aggiornato:

**Obiettivo-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Almeno, il `id` attributo deve essere impostato durante la creazione di aggiornamenti.

##<a name="deleting"></a>Procedura: eliminare i dati

Per eliminare un elemento, richiamare `delete` con l'elemento:

**Obiettivo-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

In alternativa, eliminare, fornendo un ID riga:

**Obiettivo-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Almeno, il `id` attributo deve essere impostata quando rendere Elimina.

##<a name="customapi"></a>Procedura: chiamare API personalizzato

Con una API personalizzata, è possibile esporre tutte le funzionalità di back-end. Non è necessario eseguire il mapping a un'operazione di tabella. Non solo è ottenere un maggiore controllo messaggistica, è anche possibile leggere/set di intestazioni e modificare il formato del corpo risposta. Per informazioni su come creare una API personalizzata nel back-end, leggere [API personalizzato](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Per chiamare una API personalizzata, chiamare `MSClient.invokeAPI`. La richiesta e risposta contenuto sono considerati JSON. Usare gli altri tipi di elementi multimediali, [utilizzare l'altro overload di `invokeAPI` ] [ 5].  Per rendere un `GET` richiedere al posto di un `POST` richiedere, parametro set `HTTPMethod` a `"GET"` e parametro `body` a `nil` (poiché le richieste GET non è il corpo dei messaggi.) Se l'API personalizzato supporta altri verbi HTTP, modificare `HTTPMethod` in modo appropriato.

**Obiettivo-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Procedura: modelli push Register per inviare notifiche multipiattaforma

Per registrare i modelli, passare modelli con il metodo di **client.push registerDeviceToken** nelle applicazioni client.

**Obiettivo-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

I modelli di tipo NSDictionary che possono contenere più modelli nel formato seguente:

**Obiettivo-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Tutti i contrassegni vengono rimossi dalla richiesta di sicurezza.  Per aggiungere tag a installazioni o modelli all'interno di installazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure][4].  Per inviare le notifiche tramite questi modelli registrati, collabora [Notifica hub API][3].

##<a name="errors"></a>Procedura: gestione degli errori

Quando si chiama un servizio di Azure App per dispositivi mobili la copia nel server, il blocco di completamento contiene un `NSError` parametro. Quando si verifica un errore, questo parametro è non null. Nel codice, controllare questo parametro e gestire l'errore in base alle esigenze, come illustrato in frammenti di codice precedenti.

Il file [`<WindowsAzureMobileServices/MSError.h>`] [6] definisce le costanti `MSErrorResponseKey`, `MSErrorRequestKey`, e `MSErrorServerItemKey`. Per ottenere altri dati correlati all'errore:

**Obiettivo-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Inoltre, il file definisce costanti per ogni codice di errore:

**Obiettivo-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Procedura: autenticazione degli utenti con Active Directory Authentication Library

È possibile utilizzare la libreria di autenticazione Active Directory (ADAL) per eseguire l'accesso agli utenti all'interno dell'applicazione con Azure Active Directory. Autenticazione di flusso del client con un provider di identità SDK è preferibile all'utilizzo di `loginWithProvider:completion:` metodo.  Autenticazione flusso client fornisce mostrato un esempio dell'esperienza utente più nativo e consente di un'ulteriore personalizzazione.

1. Configurare il back-end di app per dispositivi mobili per l'accesso AAD seguendo [come configurare il servizio di App per le credenziali Active Directory] [ 7] esercitazione. Assicurarsi che completare il passaggio facoltativo di registrazione di un'applicazione client nativi. Per iOS, è consigliabile che il reindirizzamento URI è la forma `<app-scheme>://<bundle-id>`. Per ulteriori informazioni, vedere [Guida introduttiva di iOS ADAL][8].

2. Installare ADAL utilizzando Cocoapods. Modificare il Podfile per includere la definizione seguente, sostituendo **Il progetto** con il nome del progetto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   e le unità:

        pod 'ADALiOS'

3. Utilizzando il terminale eseguire `pod install` dalla directory contenente il progetto e quindi aprire l'area di lavoro di Xcode generato (non il progetto).

4. Aggiungere il codice riportato di seguito per l'applicazione, in base alla lingua in uso. In ognuna, apportare queste sostituzioni:

    * Sostituire **Qui di autorità di inserimento** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. Questo valore può essere copiato dalla scheda dominio di Azure Active Directory in [portal Azure classica].
    * Sostituire **Inserisci-risorsa-ID-qui** con l'ID client per il back-end app per dispositivi mobili. È possibile ottenere l'ID client dalla scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.
    * Sostituire **Inserisci-CLIENT-ID-qui** con l'ID client sono stati copiati dall'applicazione client nativi.
    * Sostituire **Inserisci-reindirizzamento-URI qui** con endpoint _/.auth/login/done_ del sito, utilizzando lo schema HTTPS. Questo valore deve essere simile a _https://contoso.azurewebsites.net/.auth/login/done_.

**Obiettivo-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Procedura: autenticazione degli utenti con SDK Facebook per iOS

È possibile utilizzare SDK Facebook per iOS per eseguire l'accesso agli utenti all'interno dell'applicazione con Facebook.  Mediante l'autenticazione flusso un client è preferibile all'utilizzo di `loginWithProvider:completion:` metodo.  Autenticazione di flusso del client fornisce mostrato un esempio dell'esperienza utente più nativo e consente di un'ulteriore personalizzazione.

1. Configurare il back-end di app per dispositivi mobili per l'accesso Facebook seguendo la [configurazione del servizio di App per l'accesso a Facebook] [ 9] esercitazione.

2. Installare SDK Facebook per iOS seguendo [SDK di Facebook per iOS - Guida introduttiva] [ 10] documentazione. Invece di creare un'app, è possibile aggiungere la piattaforma iOS per la registrazione esistente. 

3. Documentazione di Facebook include codice obiettivo C nel delegato dell'App. Se si utilizza **Swift**, è possibile utilizzare le conversioni seguenti per AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Oltre ad aggiungere `FBSDKCoreKit.framework` al progetto, anche aggiungere un riferimento a `FBSDKLoginKit.framework` nello stesso modo. 

4. Aggiungere il codice riportato di seguito per l'applicazione, in base alla lingua in uso. 

**Obiettivo-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Procedura: autenticazione degli utenti con tessuti Twitter per iOS

È possibile utilizzare tessuti per iOS per gli utenti ad accedere a un'applicazione tramite Twitter. Flusso di autenticazione è preferibile all'utilizzo di `loginWithProvider:completion:` metodo, che consente di mostrato un esempio dell'esperienza utente più nativo e per un'ulteriore personalizzazione.

1. Configurare il back-end di app per dispositivi mobili per l'accesso Twitter seguendo l'esercitazione [come configurare il servizio di App per l'accesso a Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Aggiungere tessuti seguendo la documentazione [tessuti per iOS - Guida introduttiva] e la configurazione TwitterKit al progetto.

    > [AZURE.NOTE] Per impostazione predefinita, tessuti crea un'applicazione di Twitter dell'utente. È possibile evitare di creare un'applicazione registrando la chiave Consumer e segreto Consumer è stato creato in precedenza mediante frammenti di codice seguenti.  In alternativa, è possibile sostituire i valori di chiave Consumer e segreto Consumer che forniscono al servizio App con i valori visualizzati nei [Dashboard tessuti]. Se si sceglie questa opzione, assicurarsi di impostare l'URL di callback a un valore di segnaposto, ad esempio `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Se si sceglie di usare le informazioni riservate creata in precedenza, aggiungere il codice seguente al delegato App:
    
    **Obiettivo-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Aggiungere il codice riportato di seguito per l'applicazione, in base alla lingua in uso. 

**Obiettivo-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Procedura: autenticazione degli utenti con il SDK di accesso di Google per iOS

È possibile utilizzare il SDK di accesso di Google per iOS effettuare l'accesso agli utenti all'interno dell'applicazione con un account di Google.  Google annunciato recente le modifiche apportate ai criteri di sicurezza relativi OAuth.  Modifiche apportate ai criteri richiede l'uso di Google SDK in futuro.

1. Configurare il back-end di app per dispositivi mobili per l'accesso Google seguendo l'esercitazione [come configurare il servizio di App per l'accesso di Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Installare SDK Google per iOS, seguendo la documentazione [Google di accesso di per iOS - avviare l'integrazione](https://developers.google.com/identity/sign-in/ios/start-integrating) . È possibile ignorare la sezione "Eseguire l'autenticazione con un Server back-end".

3. Aggiungere quanto segue il delegato `signIn:didSignInForUser:withError:` metodo, in base alla lingua in uso.

**Obiettivo-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Assicurarsi di aggiungere anche le operazioni seguenti per `application:didFinishLaunchingWithOptions:` nel proprio delegato app, sostituendo "SERVER_CLIENT_ID" con lo stesso ID che è stato utilizzato per configurare il servizio App nel passaggio 1.

**Obiettivo-C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Aggiungere il codice seguente all'applicazione di un UIViewController implementa il `GIDSignInUIDelegate` protocollo, in base alla lingua in uso.  Si è disconnessi prima viene effettuato nuovamente e anche se non è necessario immettere nuovamente le credenziali, viene visualizzato una finestra di conferma.  Metodo solo quando il token di sessione è scaduto.
 
 **Obiettivo-C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile App introduttiva]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Dashboard di tessuti]: https://www.fabric.io/home
[Tessuti per iOS - Guida introduttiva]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
