<properties 
    pageTitle="Integrazione con Windows portata App universale SDK" 
    description="Come integrare portata Azure impegno per dispositivi mobili con App universale di Windows"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="windows-universal-apps-reach-sdk-integration"></a>Integrazione con Windows portata App universale SDK

È necessario seguire la procedura di integrazione descritta l' [Integrazione con Windows universale coinvolgimento SDK](mobile-engagement-windows-store-integrate-engagement.md) prima di iniziare questa Guida.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporare il progetto universale di Windows SDK di raggiungere coinvolgimento

Non è niente da aggiungere. `EngagementReach`riferimenti e risorse sono già di un progetto.

> [AZURE.TIP] È possibile personalizzare le immagini all'interno di `Resources` cartella del progetto, in particolare l'icona di marchio (che per impostazione predefinita l'icona di partecipazione attiva). In App universale è anche possibile spostare il `Resources` cartella nel progetto condiviso per condividerne il contenuto tra App, ma è necessario mantenere la `Resources\EngagementConfiguration.xml` mentre si tratta di piattaforma dipendente i file nella posizione predefinita.

## <a name="enable-the-windows-notification-service"></a>Attivare il servizio di notifica di Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8 e Windows Phone 8.1 solo

Per utilizzare il **Servizio di notifica di Windows** (denominato WNS) nel `Package.appxmanifest` file nel `Application UI` fare clic su `All Image Assets` nella casella sinistra bot. A destra della casella nella `Notifications`, modificare `toast capable` da `(not set)` a `(Yes)`.

### <a name="all-platforms"></a>Tutte le piattaforme

È necessario sincronizzare l'app per l'account Microsoft e la piattaforma di coinvolgimento. Per questa operazione è necessario creare un account o accedere a [Centro per sviluppatori di windows](https://dev.windows.com). Dopo che crea una nuova applicazione e trovare il SID e chiave privata. In front-end coinvolgimento, passare l'impostazione di app in `native push` e incollare le credenziali. In seguito, a destra fare clic sul progetto, seleziona `store` e `Associate App with the Store...`. È sufficiente selezionare l'applicazione creata prima di procedere alla sincronizzazione.

## <a name="initialize-the-engagement-reach-sdk"></a>Inizializzare le potenzialità di impegno SDK

Modificare il `App.xaml.cs`:

-   Inserire `EngagementReach.Instance.Init` subito dopo `EngagementAgent.Instance.Init` nel `InitEngagement` metodo:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    Il `EngagementReach.Instance.Init` viene eseguito in un thread dedicato. Non è necessario eseguire tale operazione manualmente.

> [AZURE.NOTE] Se si utilizza le notifiche push in un' posizione nell'applicazione si deve necessariamente [condividere del canale push](#push-channel-sharing) con coinvolgimento raggiungere.

## <a name="integration"></a>Integrazione

Coinvolgimento in due modi per aggiungere le intestazioni nell'app portata e le viste intermedi per gli annunci e sondaggi in un'applicazione: l'integrazione di sovrapposizione e l'integrazione di manuale visualizzazioni web. È consigliabile non combinare entrambi approccio nella stessa pagina.

La scelta tra l'integrazione di due Impossibile illustrata in questo modo:

-   È possibile scegliere l'integrazione di sovrapposizione se le pagine già eredita da agente di `EngagementPage`, è sufficiente sostituzione `EngagementPage` da `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` da `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` nelle pagine.
-   È possibile scegliere le visualizzazioni web integrazione manuale se si desidera posizionare l'interfaccia utente raggiunga nelle pagine o se non si desidera aggiungere un altro livello di ereditarietà per le pagine. 

### <a name="overlay-integration"></a>Integrazione di sovrapposizione

Sovrapposizione coinvolgimento aggiunge in modo dinamico gli elementi dell'interfaccia utente consente di visualizzare le campagne portata della pagina. Se la sovrapposizione non soddisfa i layout è necessario considerare le visualizzazioni web integrazione manuale alternativa.

La modifica di file XAML `EngagementPage` fanno riferimento a`EngagementPageOverlay`

-   Aggiungere le dichiarazioni di spazi dei nomi:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Sostituire `engagement:EngagementPage` con `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Con EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Nel file cs aggiungere tag pagina `EngagementPageOverlay` anziché `EngagementPage` e importare `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Sostituire `EngagementPage` con `EngagementPageOverlay`:

**Con EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Con EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Sovrapposizione coinvolgimento aggiunge un `Grid` elemento nella parte superiore della pagina è composto da un layout e i due `WebView` gli elementi uno per l'intestazione e l'altra per la visualizzazione intermedio.

È possibile personalizzare gli elementi di sovrapposizione direttamente nel `EngagementPageOverlay.cs` file.

### <a name="web-views-manual-integration"></a>Integrazione di Web visualizzazioni manuale

Portata verrà ricerca le pagine per i due `WebView` elementi responsabili per la visualizzazione del banner e la visualizzazione intermedio. L'unica differenza è necessario effettuare consiste nell'aggiungere questi due `WebView` elementi in un punto nelle pagine, di seguito è illustrato un esempio:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


In questo esempio la `WebView` elementi sono adattati allo loro contenitore automaticamente ridimensionato nuovamente loro in caso di modifica della dimensione rotazione o la finestra dello schermo.

> [AZURE.WARNING] È importante mantenere la stessa denominazione `engagement_notification_content` e `engagement_announcement_content` per il `WebView` elementi. Portata li identifica in base al nome. 

## <a name="handle-datapush-optional"></a>Gestire datapush (facoltativo)

Se si desidera all'applicazione di essere in grado di ricevere portata push di dati, è necessario implementare questi due eventi della classe EngagementReach:

In App.xaml.cs nel costruttore App() aggiungere:

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

## <a name="customize-ui-optional"></a>Personalizzazione dell'interfaccia utente (facoltativo)

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

Quindi impostare il contenuto del `EngagementReach.Instance.Handler` campo con l'oggetto personalizzato nel `App.xaml.cs` classe all'interno di `App()` metodo.

**Esempio:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Per impostazione predefinita, coinvolgimento utilizza la propria implementazione di `EngagementReachHandler`.
> Non è necessario crearne uno nuovo e in tal caso, non è necessario eseguire l'override di tutti i metodi. Il comportamento predefinito consiste nel selezionare l'oggetto di base coinvolgimento.

### <a name="web-view"></a>Visualizzazione Web

Per impostazione predefinita, portata utilizzerà le risorse incorporate della DLL per visualizzare le notifiche e le pagine.

Per fornire una personalizzazione completa possibilità serve solo visualizzazione web. Se si desidera personalizzare i layout, eseguire l'override direttamente i file di risorse `EngagementAnnouncement.html` e `EngagementNotification.html`. Coinvolgimento deve tutto il codice in `<body></body>` venga eseguita correttamente. Ma non è possibile aggiungere tag esterno `engagement_webview_area`.

Tuttavia, è possibile usare le risorse.

È possibile ignorare `EngagementReachHandler` metodi nella sottoclasse per indicare a coinvolgimento di utilizzare il layout, ma con attenzione incorporato meccanismo impegno:

**Esempio:**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Per impostazione predefinita è AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Rappresenta il file html che progettare il contenuto di un messaggio di push (annuncio di testo, anoucement Web e annuncio sondaggio). AnnouncementName è `engagement_announcement_content`. È il nome del modello di visualizzazione Web nella pagina xaml.

NotfificationHTML è `ms-appx-web:///Resources/EngagementNotification.html`. Rappresenta il file html che progettare la notifica di un messaggio di push. NotfificationName è `engagement_notification_content`. È il nome del modello di visualizzazione Web nella pagina xaml.

### <a name="customization"></a>Personalizzazione

È possibile personalizzare la notifica e annuncio di visualizzazione web è se si mantiene oggetto coinvolgimento. Prestare attenzione che la visualizzazione Web oggetto viene descritto tre volte - la prima volta in xaml, seconda volta nel file cs il metodo "setwebview()" e l'ora terza nel file html.

-   In xaml è descrivere il componente di visualizzazione Web layout grafico corrente.
-   Nel file cs è possibile definire "setwebview()" in cui si imposta la dimensione di due la visualizzazione Web (notifica, annuncio). È molto utile durante il ridimensionamento dell'applicazione.
-   Nel file html coinvolgimento è descrivere il contenuto di visualizzazione Web, struttura e le posizioni di elementi tra loro.

### <a name="launch-message"></a>Messaggio di avvio

Quando si fa clic su una notifica di sistema (un tipo di avviso popup), coinvolgimento avvia l'applicazione, caricare il contenuto dei messaggi push e visualizzare la pagina relativa alla campagna corrispondenti.

Si verifica un ritardo tra la barra di avvio dell'applicazione e la visualizzazione della pagina (a seconda della velocità della rete).

Per indicare all'utente che si sta caricando, è necessario fornire un informazioni visive, ad esempio un indicatore o un indicatore di stato. Impegno non è possibile gestire, ma fornisce alcuni gestori dell'utente.

Per implementare il callback, App.xaml.cs "{} App() pubblico" aggiungere:

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

È possibile impostare il callback nel metodo "{} App() pubblico" del `App.xaml.cs` file preferenza prima di `EngagementReach.Instance.Init()` chiamata.

> [AZURE.TIP] Ogni gestore viene chiamato dal Thread UI. Non avere a disposizione quando si utilizza un MessageBox o destinati correlate dell'interfaccia utente.

##<a id="push-channel-sharing"></a>Spingere la condivisione di canale

Se si utilizza le notifiche push per altri scopi nell'applicazione è necessario utilizzare il canale push funzionalità di SDK coinvolgimento di condivisione. Questa operazione consiste nell'evitare push senza risposta.

- È possibile fornire il proprio canale push all'inizializzazione coinvolgimento raggiungere. SDK verrà utilizzato invece che richiede di uno nuovo.

Aggiornare l'inizializzazione coinvolgimento raggiungere con il canale push nel `InitEngagement` dei metodi di `App.xaml.cs` file:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- In alternativa, se si desidera utilizzare il canale push dopo l'inizializzazione portata è possibile impostare un callback nel coinvolgimento raggiungere per ottenere una volta il canale push viene creato da SDK.

Impostare il callback in qualsiasi punto **dopo** l'inizializzazione portata:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Suggerimento per la combinazione di colori personalizzata

Sono disponibili Usa combinazione personalizzata. È possibile inviare tipo di URI diverso da coinvolgimento front-end da utilizzare nell'applicazione coinvolgimento. Combinazione predefinita come `http, ftp, ...` sono gestire da Windows, verrà finestra prompt dei comandi qualora non siano Nessuna applicazione predefinita installata nel dispositivo. È anche possibile creare il proprio combinazione personalizzata per l'applicazione.

Il modo semplice per impostare una combinazione personalizzata nell'applicazione consiste nell'aprire il `Package.appxmanifest` passare in `Declarations` pannello. Selezionare `Protocol` nelle dichiarazioni di disponibili casella di scorrimento e aggiungerlo. Modificare il `Name` campo con il nuovo protocollo il nome desiderato.

Per utilizzare questo protocollo, modificare ora il `App.xaml.cs` con la `OnActivated` metodo e non dimenticare di inizializzare anche coinvolgimento di seguito:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
