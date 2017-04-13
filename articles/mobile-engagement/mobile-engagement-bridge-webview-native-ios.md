<properties 
    pageTitle="Bridge iOS, la visualizzazione Web con nativo coinvolgimento Mobile iOS SDK" 
    description="Viene descritto come creare un collegamento tra la visualizzazione Web in esecuzione di Javascript e nativo coinvolgimento Mobile iOS SDK"      
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Bridge iOS, la visualizzazione Web con nativo coinvolgimento Mobile iOS SDK

> [AZURE.SELECTOR]
- [Bridge di Android](mobile-engagement-bridge-webview-native-android.md)
- [Bridge di iOS](mobile-engagement-bridge-webview-native-ios.md)

Alcune App per dispositivi mobili sono progettate come app ibrido in app viene sviluppata utilizzando sviluppo nativo iOS obiettivo C ma alcune o tutte le schermate in cui vengono visualizzate all'interno di un iOS la visualizzazione Web. È comunque possibile utilizzare coinvolgimento Mobile iOS SDK all'interno di tali applicazioni e in questa esercitazione viene illustrato come passare su questa operazione. 

Esistono due modi per eseguire questa operazione anche se si verificano entrambe:

- Prima di tutto quello descritto in questo [collegamento](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) che comporta la registrazione di un `UIWebViewDelegate` la visualizzazione web e catture-e-immediatamente-Annulla una modifica posizione eseguita in Javascript. 
- Secondo uno si basa su questa [sessione WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), un approccio che è il modo più efficiente rispetto alla prima e che è necessario seguire questa Guida. Si noti che questo approccio funziona solo in iOS7 e versioni successive. 

Seguire la procedura seguente per il file iOS bridge esempio:

1. Prima di tutto, è necessario assicurarsi che hanno effettuato tramite il nostro [esercitazione Guida introduttiva](mobile-engagement-ios-get-started.md) di integrare impegno Mobile iOS più SDK nell'app ibrida. Facoltativamente, è possibile abilitare test registrazione come indicato di seguito in modo che è possibile vedere i metodi SDK sono attivati dalla visualizzazione Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Assicurarsi che l'app ibrido dispone di uno schermo con una visualizzazione Web in cui è. È possibile aggiungerla alla `Main.storyboard` dell'app. 

3. Associare la visualizzazione Web il **ViewController** facendo clic e trascinando la visualizzazione Web dalla schermata Controller di visualizzazione per il `ViewController.h` modifica dello schermo, posizionandolo sotto la `@interface` riga. 

4. Una volta eseguita questa operazione, verrà visualizzata una finestra di dialogo che richiede di un nome. Specificare il nome come **la visualizzazione Web**. Il `ViewController.h` file avrà un aspetto simile al seguente:

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Microsoft aggiornerà il `ViewController.m` file in un secondo momento ma prima di tutto si creerà il file bridge che crea una spaziale su alcune iOS Mobile impegno comune metodi SDK. Creare un nuovo file di intestazione denominato **EngagementJsExports.h** che usa il `JSExport` meccanismo descritto in precedenza [sessione](https://developer.apple.com/videos/play/wwdc2013/615) per esporre i metodi di iOS nativo. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Successivamente si creerà la seconda parte del file bridge. Creare un file denominato **EngagementJsExports.m** che contiene l'implementazione la creazione di wrapper effettivo chiamando i metodi SDK coinvolgimento Mobile iOS. Si noti inoltre che si sono analisi di `extras` passati da javascript la visualizzazione Web e inserire che in un `NSMutableDictionary` oggetto passare con il metodo chiama coinvolgimento SDK.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. A questo punto si torna a **ViewController.m** e aggiornare con il codice seguente: 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Tenere presente gli aspetti seguenti dei file **ViewController.m** :

    - Nel `loadWebView` metodo, è in corso il caricamento di un file HTML locale denominato **LocalPage.html** il cui codice esaminate successivo. 
    - Nel `webViewDidFinishLoad` metodo, si sta cattura la `JsContext` e associare la classe spaziale. In questo modo si chiama il nostro spaziale metodi SDK tramite il quadratino di **EngagementJs** dalla visualizzazione Web. 

7. Creare un file denominato **LocalPage.html** con il codice seguente:

        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
               
               <script type="text/javascript">
               
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Tenere presente gli aspetti seguenti dei file HTML precedente:

    -   Contiene una serie di caselle di input in cui è possibile specificare i dati da utilizzare come nomi per l'evento, processo, errore, AppInfo. Quando si fa clic sul pulsante corrispondente, viene effettuata una chiamata a Javascript che chiama i metodi dal file bridge per passare la chiamata per la partecipazione attiva Mobile iOS SDK. 
    -   Si sta tagging in alcune informazioni aggiuntive statica per gli eventi, processi e persino errori per dimostrare come questa operazione. Queste informazioni aggiuntive viene inviata come un JSON stringa che, se Cerca nel `EngagementJsExports.m` file, viene analizzata e passato insieme a inviare gli eventi, processi, errori. 
    -   Un processo di impegno Mobile viene persa con il nome specificato nella casella input eseguire per 10 secondi e arrestato. 
    -   Un tag o appinfo coinvolgimento Mobile viene passato con 'cliente' come chiave statica e il valore immesso nell'input come valore per il contrassegno. 
 
9. Eseguire l'app e verrà visualizzato quanto segue. Ora fornire un nome per un evento di test simile al seguente e fare clic su **Invia** corrispondente. 

    ![][1]

10. Se si passa alla scheda **monitorare** le app e aspetto in **eventi -> Dettagli**, verrà visualizzata l'evento compaiano insieme statico app-info che stiamo inviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
