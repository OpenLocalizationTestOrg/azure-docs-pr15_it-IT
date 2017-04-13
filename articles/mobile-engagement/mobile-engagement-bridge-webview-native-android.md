<properties 
    pageTitle="Visualizzazione Web Android Bridge con nativo Mobile coinvolgimento Android SDK" 
    description="Viene descritto come creare un collegamento tra la visualizzazione Web in esecuzione di Javascript e nativo Mobile coinvolgimento Android SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Visualizzazione Web Android Bridge con nativo Mobile coinvolgimento Android SDK

> [AZURE.SELECTOR]
- [Bridge di Android](mobile-engagement-bridge-webview-native-android.md)
- [Bridge di iOS](mobile-engagement-bridge-webview-native-ios.md)

Alcune App per dispositivi mobili sono progettate come app ibrido in app viene sviluppata utilizzando sviluppo Android nativo ma alcune o tutte le schermate in cui vengono visualizzate all'interno di una visualizzazione Web Android. È comunque possibile utilizzare Mobile coinvolgimento Android SDK all'interno di tali applicazioni e in questa esercitazione viene illustrato come passare su questa operazione. Il codice di esempio seguente è basato sulla documentazione Android [qui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Viene descritto come questo approccio documentazione potrebbe essere utilizzato per implementare la stessa per metodi comuni Mobile coinvolgimento Android SDK in modo che una visualizzazione Web da un'app per ibrido possono inoltre avviare richieste per tenere traccia eventi, processi, gli errori, informazioni app durante la loro tubazioni tramite Windows SDK Android. 

1. Prima di tutto, è necessario assicurarsi che hanno effettuato tramite il nostro [esercitazione Introduzione](mobile-engagement-android-get-started.md) per integrare il coinvolgimento di dispositivi mobili Android SDK nell'app ibrida. Una volta eseguita, il `OnCreate` metodo un aspetto simile al seguente.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Assicurarsi che l'app ibrido dispone di uno schermo con una visualizzazione Web in cui è. Il codice sarà simile a quanto segue nel punto in cui è corso il caricamento di un file HTML locale dei file **Sample.html** in visualizzazione Web nel `onCreate` metodo dello schermo. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. A questo punto creare un file bridge denominato **WebAppInterface** che consente di creare un spaziale su alcuni metodi Mobile coinvolgimento Android SDK comune utilizzando il `@JavascriptInterface` approccio descritto nella [documentazione Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Dopo aver creato il file bridge precedente, è necessario verificare che associato con la visualizzazione Web. A tale scopo, è necessario modificare il `SetWebview` metodo in modo che lo ha l'aspetto seguente:

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. Frammento precedente, è chiamato `addJavascriptInterface` per associare la classe bridge con la visualizzazione Web e anche creato un punto di **EngagementJs** per chiamare i metodi dal file bridge. 

6. Creare il file seguente **Sample.html** in un progetto in una cartella denominata **risorse** che viene caricata la visualizzazione Web e in cui si eseguiranno una chiamata i metodi dal file bridge.

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
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Tenere presente gli aspetti seguenti dei file HTML precedente:

    -   Contiene una serie di caselle di input in cui è possibile specificare i dati da utilizzare come nomi per l'evento, processo, errore, AppInfo. Quando si fa clic sul pulsante corrispondente, viene effettuata una chiamata a Javascript che chiama i metodi dal file bridge per passare la chiamata a SDK Android coinvolgimento Mobile. 
    -   Si sta tagging in alcune informazioni aggiuntive statica per gli eventi, processi e persino errori per dimostrare come questa operazione. Queste informazioni aggiuntive viene inviata come un JSON stringa che, se Cerca nel `WebAppInterface` file, viene analizzata e inserire in un dispositivo Android `Bundle` e passato insieme a inviare gli eventi, processi, errori. 
    -   Un processo di impegno Mobile viene persa con il nome specificato nella casella input eseguire per 10 secondi e arrestato. 
    -   Un tag o appinfo coinvolgimento Mobile viene passato con 'cliente' come chiave statica e il valore immesso nell'input come valore per il contrassegno. 
 
9. Eseguire l'app e verrà visualizzato quanto segue. Ora fornire un nome per un evento di test simile al seguente e fare clic su **Invia** sotto di esso. 

    ![][1]

10. Se si passa alla scheda **monitorare** le app e aspetto in **eventi -> Dettagli**, verrà visualizzata l'evento compaiano insieme statico app-info che stiamo inviando. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
