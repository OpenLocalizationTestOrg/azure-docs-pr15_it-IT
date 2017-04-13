<properties 
    pageTitle="Windows Phone portata Silverlight SDK integrazione" 
    description="Come integrare portata Azure impegno per dispositivi mobili con Windows Phone Silverlight App"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone portata Silverlight SDK integrazione

È necessario seguire la procedura di integrazione descritta in [Windows Phone Silverlight coinvolgimento SDK integrazione](mobile-engagement-windows-phone-integrate-engagement.md) prima di iniziare questa Guida.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporare l'impegno raggiunga SDK nel progetto Silverlight Windows Phone

Non è niente da aggiungere. `EngagementReach`riferimenti e risorse sono già di un progetto.

> [AZURE.TIP]  È possibile personalizzare le immagini all'interno di `Resources` cartella del progetto, in particolare l'icona di marchio (che per impostazione predefinita l'icona di partecipazione attiva).

##<a name="add-the-capabilities"></a>Aggiungere le funzionalità

L'impegno raggiunga SDK deve alcune funzionalità aggiuntive.

Aprire il `WMAppManifest.xml` file e assicurarsi che vengano dichiarate le funzionalità seguenti:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

Il primo viene utilizzato dal servizio MPNS per consentire la visualizzazione della notifica di tipo avviso popup. Nel secondo viene utilizzato per incorporare SDK di un'attività del browser.

Modificare il `WMAppManifest.xml` file e aggiungere all'interno di `<Capabilities />` tag:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Attivare il servizio di notifica Push di Microsoft

Per utilizzare il **Servizio di notifica Push di Microsoft** , definito MPNS, il `WMAppManifest.xml` file deve avere un `<App />` tag con una `Publisher` attributo impostato su nome del progetto.

##<a name="initialize-the-engagement-reach-sdk"></a>Inizializzare le potenzialità di impegno SDK

### <a name="engagement-configuration"></a>Configurazione di impegno

La configurazione di impegno è centralizzata nel `Resources\EngagementConfiguration.xml` file del progetto.

Modificare il file per specificare la configurazione di mano:

-   *Facoltativo*, indicare se è attivata push nativo (MPNS) o non è compreso tra `<enableNativePush>` e `</enableNativePush>` contrassegni (`true` per impostazione predefinita).
-   *Facoltativo*, indicare il nome del canale push tra `<channelName>` e `</channelName>` tag, fornire lo stesso che l'applicazione può utilizzare attualmente o lasciare vuoto il campo.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] È possibile specificare il nome del canale push MPNS dell'applicazione. Per impostazione predefinita, coinvolgimento crea un nome in base a appId. È necessario specificare il nome se stessi, tranne se si prevede di utilizzare il canale push all'esterno di impegno.

### <a name="engagement-initialization"></a>Inizializzazione coinvolgimento

Modificare il `App.xaml.cs`:

-   Aggiungere il `using` istruzioni:

        using Microsoft.Azure.Engagement;

-   Inserire `EngagementReach.Instance.Init` subito dopo `EngagementAgent.Instance.Init` in `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Inserire `EngagementReach.Instance.OnActivated` nel `Application_Activated` metodo:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] Il `EngagementReach.Instance.Init` viene eseguito in un thread dedicato. Non è necessario eseguire tale operazione manualmente.

##<a name="app-store-submission-considerations"></a>Considerazioni sull'invio di archivio di App

Quando si utilizzano le notifiche push, Microsoft impone alcune regole:

Dalla documentazione di Microsoft [Criteri di applicazione] delle sezioni 2.9:

1) Chiedere all'utente di accettare per ricevere notifiche push. Quindi, nella sezione Impostazioni, aggiungere un modo per disabilitare le notifiche push.

L'oggetto EngagementReach esistono due modi per gestire il consenso esplicito-in/esclusione, `EnableNativePush()` e `DisableNativePush()`. È possibile, ad esempio, creare un'opzione in impostazioni con un interruttore per disattivare o attivare MPNS.

È anche possibile disattivare MPNS tramite la configurazione di impegno\<windows phone-sdk-portata-configurazione\>.

> 2.9.1) dell'applicazione prima di tutto necessario descrivere le notifiche per fornire e **ottenere l'autorizzazione dell'utente express (di)**e **deve fornire un meccanismo mediante il quale l'utente è possibile rifiutare esplicitamente la ricezione delle notifiche push**. Tutte le notifiche disponibile tramite il servizio di notifica Push Microsoft deve essere conforme alla descrizione fornita agli utenti e devono essere conformi con tutti i [Criteri di applicazione]  [ Content Policies] e [Requisiti aggiuntivi per specifici tipi di applicazioni].

2) Non è necessario utilizzare le notifiche push troppi. Coinvolgimento gestirà le notifiche dell'utente.

> 2.9.2) dell'applicazione e l'utilizzo del servizio di notifica Push di Microsoft deve non eccessivamente utilizzare capacità di rete o della larghezza di banda del servizio di notifica Push di Microsoft, o in caso contrario eccessivamente sovraccarica un Windows Phone o altro dispositivo Microsoft o servizio con le notifiche push eccessiva definito da Microsoft a sua discrezione accettabile e deve essere non danneggiare o interferire con le reti Microsoft Server o qualsiasi server di terze parti o reti connesse al servizio di notifica Push di Microsoft.

3) Non basarsi su MPNS per inviare informazioni criticals. Coinvolgimento utilizza MPNS, in modo che la regola si applica anche per le campagne create all'interno di impegno front-end.

> 2.9.3) il servizio di notifica Push Microsoft potrebbe non essere utilizzato per inviare notifiche cruciali per critico o in caso contrario potrebbero influire sulla materia della priorità, inclusi senza notifiche critiche limitazione correlati a un dispositivo medico o condizione. MICROSOFT DECLINA ESPRESSAMENTE QUALSIASI GARANZIA CHE L'USO DI MICROSOFT PUSH NOTIFICATION SERVICE O RECAPITO DELLE NOTIFICHE DI SERVIZIO DI NOTIFICA PUSH DI MICROSOFT SARÀ SENZA INTERRUZIONE, SENZA ERRORI O IN CASO CONTRARIO SICURAMENTE ESEGUITA IN TEMPO REALE.

**È possibile garantire che l'applicazione passerà il processo di convalida se non si rispettano questi suggerimenti.**

##<a name="handle-data-push-optional"></a>Gestire i dati push (facoltativi)

Se si desidera all'applicazione di essere in grado di ricevere portata push di dati, è necessario implementare questi due eventi della classe EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };
    
    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

È possibile vedere che il callback di ogni metodo restituisce un valore boolean. Dopo l'inserimento di dati di invio coinvolgimento invia un feedback relativa back-end. Se il callback restituisce FALSO, la `exit` feedback sarà Invia. In caso contrario, sarà `action`. Se il callback non è impostato per gli eventi, il `drop` commenti e suggerimenti tornerà alla partecipazione attiva.

> [AZURE.WARNING] Impegno non è in grado di ricevere feedback multipli per un push di dati. Se si intende installare gestori diversi in un evento, tenere presente che il feedback corrisponderà fino all'ultima uno inviati. In questo caso, si consiglia di restituisce sempre lo stesso valore per evitare confusione commenti e suggerimenti su front-end.

##<a name="customize-ui-optional"></a>Personalizzazione dell'interfaccia utente (facoltativo)

### <a name="first-step"></a>Primo passaggio

Abbiamo consente di personalizzare le potenzialità dell'interfaccia utente.

A tale scopo, è necessario creare una sottoclasse di `EngagementReachHandler` classe.

**Esempio:**

    using Microsoft.Azure.Engagement;
    
    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Quindi impostare il contenuto del `EngagementReach.Instance.Handler` campo con l'oggetto personalizzato nel `App.xaml.cs` classe all'interno di `Application_Launching` metodo.

**Esempio:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Per impostazione predefinita, coinvolgimento utilizza la propria implementazione di `EngagementReachHandler`. Non è necessario crearne uno nuovo e in tal caso, non è necessario eseguire l'override di tutti i metodi. Il comportamento predefinito consiste nel selezionare l'oggetto di base coinvolgimento.

### <a name="layouts"></a>Layout

Per impostazione predefinita, portata utilizzerà le risorse incorporate della DLL per visualizzare le notifiche e le pagine.

Tuttavia, è possibile usare le risorse in modo da rispecchiare il marchio in questi componenti.

È possibile ignorare `EngagementReachHandler` metodi nella sottoclasse per indicare a coinvolgimento utilizzare il layout:

**Esempio:**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] Il `CreateNotification` metodo può restituire null. La notifica non viene visualizzata e campagna portata verrà rimosse.

Per semplificare l'implementazione di layout, è possibile utilizzare secondo le proprie xaml da utilizzare come base per il codice. Si trovano nell'archivio coinvolgimento SDK (/ src/portata /).

> [AZURE.WARNING] Le origini che sono disponibili sono le stesse esatta usato. Se si desidera modificarli direttamente, pertanto non dimenticare di modificare lo spazio dei nomi e il nome.

### <a name="notification-position"></a>Posizione di notifica

Per impostazione predefinita, viene visualizzata una notifica di app nella parte inferiore sinistra della finestra dell'applicazione. È possibile modificare questo comportamento eseguendo l'override di `GetNotificationPosition` metodo del `EngagementReachHandler` oggetto.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Attualmente, è possibile scegliere tra le `BOTTOM` (impostazione predefinita) e `TOP` posizioni.

### <a name="launch-message"></a>Messaggio di avvio

Quando si fa clic su una notifica di sistema (un tipo di avviso popup), coinvolgimento avvia l'app, caricare il contenuto dei messaggi push e visualizzare la pagina relativa alla campagna corrispondenti.

Si verifica un ritardo tra la barra di avvio dell'applicazione e la visualizzazione della pagina (a seconda della velocità della rete).

Per indicare all'utente che si sta caricando, è necessario fornire un informazioni visive, ad esempio un indicatore o un indicatore di stato. Impegno non è possibile gestire, ma fornisce alcuni gestori dell'utente.

Per implementare il callback, eseguire:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
    
    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
    
    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

È possibile impostare il callback nel `Application_Launching` metodo del `App.xaml.cs` file preferenza prima la `EngagementReach.Instance.Init()` chiamata.

> [AZURE.TIP] Ogni gestore viene chiamato dal Thread UI. Non avere a disposizione quando si utilizza un MessageBox o destinati correlate dell'interfaccia utente.

[Criteri di applicazioni]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisiti aggiuntivi per i tipi di applicazione specifica]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
