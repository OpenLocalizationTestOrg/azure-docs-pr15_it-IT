<properties
    pageTitle="Azure Active Directory iOS introduzione | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione di iOS che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrare Azure Active Directory in un'App per iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure Active Directory è Active Directory Authentication Library o ADAL, per i client di iOS devono poter accedere alle risorse protette.  Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata un'applicazione di elenco di attività C obiettivo che:

-   Ottiene access token per richiamare l'API di Azure Active Directory grafico utilizzando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Ricerca in una directory per gli utenti con un alias specificato.

Per creare l'applicazione di lavoro completa, è necessario:

2. Registrare l'applicazione di Azure Active Directory.
3. Installare e configurare ADAL.
5. Utilizzare ADAL per ottenere i token da Azure Active Directory.

Per iniziare a, [scaricare la struttura di app](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

> [AZURE.TIP] Provare l'anteprima del nuovo [portale per sviluppatori](https://identity.microsoft.com/Docs/iOS) che consentono di iniziare a utilizzarlo con Azure Active Directory in pochi minuti!  Portale per sviluppatori di verrà descritto il processo di registrazione di un'app e integrazione del codice di Azure Active Directory.  Al termine, si avrà una semplice applicazione che può eseguire l'autenticazione di utenti al tenant e un back-end che possono accettare i token ed eseguire la convalida. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. determinare quale il URI reindirizzare sarà per iOS*

Per le applicazioni in alcuni scenari di Single Sign-on di avvio in modo sicuro è necessario creare un **URI reindirizzare** in un formato specifico. URI reindirizzare la chiamata viene utilizzato per assicurarsi che i token restituiscono all'applicazione corretta frequenti per loro.

Il formato di iOS per un URI reindirizzare è:

```
<app-scheme>://<bundle-id>
```

-   **combinazione di aap** - questo è registrato nel progetto XCode. Si tratta di come è possono chiamare altre applicazioni. È possibile trovare in Info.plist -> tipi di URL -> identificatore URL. Se si dispone già di una o più configurato, è necessario crearne uno.
-   **raggruppamento di id** - è l'identificatore di aggregazione disponibili nella sezione "identità" annullare le impostazioni del progetto in XCode.

Ad esempio per il codice di questa Guida introduttiva: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. registrare l'applicazione DirectorySearcher*
Per abilitare l'app ottenere i token, è prima necessario registrare nel tenant di Azure Active Directory e concessione dell'autorizzazione per accedere all'API di Azure Active Directory grafico:

-   Accedere al portale di gestione di Azure
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **Applicazione Client nativa**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   **Reindirizzare Uri** è una combinazione di schema e stringa utilizzate per restituire le risposte token Azure Active Directory.  Immettere un valore specifico per l'applicazione in base alle informazioni sopra.
-   Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda **Configura** .
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni".  Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione di **Directory dell'organizzazione l'accesso** con **Autorizzazioni di delega**.  In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="3-install--configure-adal"></a>*3. installare e configurare ADAL*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare ADAL e scrivere il codice di identità.  Affinché ADAL la possibilità di comunicare con Azure Active Directory, è necessario fornirgli alcune informazioni la registrazione di app.
-   Iniziare aggiungendo ADAL al progetto DirectorySearcher utilizzando Cocapods.

```
$ vi Podfile
```
Aggiungere questo podfile quanto segue:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

A questo punto caricare podfile utilizzando cocoapods. Verrà creata una nuova area di lavoro di XCode verrà caricato.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   Nel progetto di Guida introduttiva, aprire il file plist `settings.plist`.  Sostituire i valori degli elementi nella sezione per riflettere i valori di input al portale di Azure.  Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `clientId` è clientId dell'applicazione sono stati copiati dal portale.
    -   Il `redirectUri` è il reindirizzamento url registrato nel portale.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. utilizzare ADAL per ottenere i token da AAD*
È fondamentale dietro ADAL ogni volta che l'app è necessario un token di accesso, chiama semplicemente un completionBlock `+(void) getToken : `, e ADAL non tutte le altre applicazioni.  

-   Nel `QuickStart` progetto aperto `GraphAPICaller.m` e individuare il `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` commento nella parte superiore.  Si tratta in cui si passano ADAL le coordinate tramite CompletionBlock per comunicare con Azure Active Directory e indicare la modalità di memorizzazione nella cache token.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- A questo punto è necessario utilizzare questo token per cercare utenti nel grafico. Trovare il `// TODO: implement SearchUsersList` commentThis metodo esegue una richiesta GET all'API di Azure Active Directory grafico per eseguire una query per gli utenti di cui UPN inizia con il termine di ricerca specificati.  Ma eseguire query su API di grafico, è necessario includere un Access nel `Authorization` intestazione della richiesta - questa entra ADAL in.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Quando l'app richiede un token chiamando `getToken(...)`, ADAL proverà a restituire un token senza chiedere all'utente le credenziali.  Se ADAL determina che l'utente deve eseguire l'accesso ottenere un token, verrà visualizzare una finestra di dialogo di accesso, raccogliere le credenziali dell'utente e restituire un token volta eseguita l'autenticazione.  Se ADAL è in grado di restituire un token per qualsiasi motivo, genera un `AdalException`.
- Si noti che la `AuthenticationResult` oggetto contiene un `tokenCacheStoreItem` oggetto che può essere utilizzato per raccogliere informazioni potrebbero essere necessarie l'app.  In Guida introduttiva, `tokenCacheStoreItem` viene utilizzata per determinare se è già avvenuta autenticazione.


## <a name="step-5-build-and-run-the-application"></a>Passaggio 5: Creare ed eseguire l'applicazione



Congratulazioni! Ora dispone di un'applicazione di iOS di lavoro che è in grado di autenticazione degli utenti, in modo sicuro chiamare API Web usando OAuth 2.0 e ottenere informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.  Eseguire la Guida introduttiva app e accedere con uno di questi utenti.  Ricerca di altri utenti in base alle loro UPN.  Chiudere l'app e ripetere l'esecuzione.  Si noti come rimane intatta la sessione dell'utente.

ADAL rende più facile tutte queste caratteristiche comuni identità incorporare nell'applicazione.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.  Ciò che occorre sapere è una singola chiamata all'API `getToken`.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Altri scenari
È possibile procedere con per altri scenari.  È consigliabile provare:

- [Proteggere un Web Node API con Azure Active Directory](active-directory-devquickstarts-webapi-nodejs.md)
- Informazioni su [come abilitare tra app Single Sign-On in iOS utilizzando ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
