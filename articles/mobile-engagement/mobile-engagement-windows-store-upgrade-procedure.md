<properties 
    pageTitle="Procedure di aggiornamento di Windows SDK App universale" 
    description="App universale di Windows SDK aggiornare le procedure per Azure impegno per dispositivi mobili"                     
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

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedure di aggiornamento di Windows SDK App universale

Se è già stata integrata una versione precedente di impegno nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

È necessario eseguire alcune procedure notato diverse versioni di SDK. Se, ad esempio la migrazione da 0.10.1 a 0.11.0 che è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1" quindi la procedura "da 0.10.1 a 0.11.0".

##<a name="from-330-to-340"></a>Da 3.3.0 a 3.4.0

### <a name="test-logs"></a>Log di test

Log degli console prodotti da SDK può essere attivato/disattivato/filtrati. Per personalizzare questo, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` un valore disponibile la `EngagementTestLogLevel` enumerazione, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Risorse

Sovrapposizione portata è stata migliorata. Fa parte delle risorse di pacchetto NuGet SDK.

Durante l'aggiornamento alla nuova versione di SDK è possibile scegliere se si desidera mantenere i file esistenti dalla cartella sovrapposizione delle risorse o meno:

* Se è in esecuzione la sovrapposizione precedente dell'utente o si integra la `WebView` elementi manualmente quindi è possibile scegliere di mantenere i file in fase di chiusura, continuerà a funzionare. 
* Se si vuole eseguire l'aggiornamento a sovrapposizione nuova, è necessario sostituire solo all'intera `overlay` cartella dalle risorse con quello nuovo dal pacchetto SDK (app UWP: dopo l'aggiornamento, è possibile ottenere la nuova cartella di sovrapposizione da % profiloutente %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Con la nuova sovrapposizione verranno sovrascritti dalle personalizzazioni apportate nella versione precedente.

##<a name="from-320-to-330"></a>Da 3.2.0 a 3.3.0

### <a name="resources"></a>Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se si personalizzano le risorse fornite da SDK (html, immagini, sovrapposizione) è necessario eseguire il backup di loro prima dell'aggiornamento e riapplicare la personalizzazione delle risorse aggiornate.

##<a name="from-310-to-320"></a>Da 3.1.0 a 3.2.0

### <a name="resources"></a>Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se si personalizzano le risorse fornite da SDK (html, immagini, sovrapposizione) è necessario eseguire il backup di loro prima dell'aggiornamento e riapplicare la personalizzazione delle risorse aggiornate.

### <a name="webview-integration"></a>Integrazione di visualizzazione Web
In questa versione sono stati introdotti alcuni miglioramenti in modo che corrispondano fattori di forma dispositivo diverso. Verificare che l'integrazione della visualizzazione Web corrisponda a quanto segue:

La pagina XAML ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E nel file cs associati:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>Da 2.0.0 a 3.0.0

### <a name="resources"></a>Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se si personalizzano le risorse fornite da SDK (html, immagini, sovrapposizione) è necessario eseguire il backup di loro prima dell'aggiornamento e riapplicare la personalizzazione delle risorse aggiornate.

##<a name="from-111-to-200"></a>Da 1.1.1 a 2.0.0

Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio di Capptain fornito dal Capptain SAS in un'app con tecnologia Azure Mobile coinvolgimento. 

> [Azure.IMPORTANT] Capptain e coinvolgimento Mobile non sono gli stessi servizi e la procedura indicata di seguito viene evidenziato solo come eseguire la migrazione dell'applicazione client. Migrazione SDK nell'app verrà non eseguire la migrazione dei dati da server Capptain ai server coinvolgimento Mobile

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire la migrazione a 1.1.1 prima di tutto, quindi applicare la procedura seguente

### <a name="nuget-package"></a>Pacchetto NuGet

Sostituire **Capptain.WindowsPhone** pacchetto Nuget **MicrosoftAzure.MobileEngagement** .

### <a name="applying-mobile-engagement"></a>L'applicazione di impegno per dispositivi mobili

il SDK viene utilizzato il termine `Engagement`. È necessario aggiornare il progetto in base a questa modifica.

È necessario disinstallare il pacchetto nuget Capptain corrente. Tenere presente che verranno rimosse tutte le modifiche nella cartella Capptain risorse. Se si desidera mantenere i file, quindi eseguire una copia di esse.

Successivamente, installare il nuovo pacchetto nuget coinvolgimento di Microsoft Azure sul progetto. È possibile trovare direttamente in [nuget website]. o indice qui. Questa azione sostituisce tutti i file di risorse utilizzati in coinvolgimento e aggiunge la nuova DLL coinvolgimento ai riferimenti del progetto.

È necessario eliminare i riferimenti del progetto eliminando riferimenti Capptain DLL. Se non effettuare questa operazione, la versione di Capptain è in conflitto e si verificherà errori.

Se è stato personalizzato Capptain risorse, copiare il contenuto di file precedente e incollarle in nuovi file di progetto. Si noti che sono necessario aggiornare i file xaml e cs.

Quando è necessario completare questi passaggi è necessario sostituire vecchi riferimenti Capptain per i nuovi riferimenti coinvolgimento.

1. Tutti gli spazi dei nomi Capptain devono essere aggiornate.

    Prima della migrazione:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Dopo la migrazione:
    
        using Microsoft.Azure.Engagement;

2. Tutte le classi Capptain che contengono "Capptain" devono contenere "Impegno".

    Prima della migrazione:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Dopo la migrazione:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Per i file xaml attributi e spazio dei nomi Capptain modificate.

    Prima della migrazione:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Dopo la migrazione:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Sovrapposizione della pagina
    > [AZURE.IMPORTANT] Sovrapposizione anche le modifiche. Il nuovo spazio dei nomi è `Microsoft.Azure.Engagement.Overlay`. Deve essere utilizzato nei file xaml e cs. Inoltre `CapptainGrid` deve essere denominato `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` sono denominate `engagement_notification_content` e `engagement_announcement_content`.
    
    Per sovrapposizione:
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Diventa:
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Per altre risorse come Capptain immagini e file HTML, tenere presente che anche essere stati rinominati per utilizzare "Impegno".

### <a name="project-declaration"></a>Dichiarazione di progetto

In Package.appxmanifest `File Type Associations` non è stato aggiornato:

 -   capptain\_raggiungere\_contenuto coinvolgimento\_raggiungere\_contenuto
 -   capptain\_log\_il file da coinvolgimento\_log\_file

### <a name="application-id--sdk-key"></a>ID applicazione / chiave SDK

Coinvolgimento utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e un tasto SDK con coinvolgimento di dispositivi mobili, è sufficiente specificare una stringa di connessione. È possibile impostare sul file EngagementConfiguration.

La configurazione di impegno può essere impostata nel `Resources\EngagementConfiguration.xml` file del progetto.

Modificare il file per specificare:

-   Stringa di connessione dell'applicazione tra tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale classica di Azure.

### <a name="items-name-change"></a>Modifica del nome elementi

Tutti gli elementi denominati *capptain* avere nome *coinvolgimento*. Analogamente, per *Capptain* a *coinvolgimento*.

Esempi di uso comune Capptain elementi:

-   CapptainConfiguration ora denominata EngagementConfiguration
-   CapptainAgent ora denominata EngagementAgent
-   CapptainReach ora denominata EngagementReach
-   CapptainHttpConfig ora denominata EngagementHttpConfig
-   GetCapptainPageName ora denominata GetEngagementPageName

Si noti che Rinomina influisce anche sui metodi sottoposti a override.

 
