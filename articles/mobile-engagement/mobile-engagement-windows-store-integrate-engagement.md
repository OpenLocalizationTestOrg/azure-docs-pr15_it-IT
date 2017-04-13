<properties 
    pageTitle="Integrazione con Windows App universale coinvolgimento SDK" 
    description="Come integrare App universale di Windows Azure impegno per dispositivi mobili"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integrazione con Windows App universale coinvolgimento SDK

> [AZURE.SELECTOR] 
- [Windows universale](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Questa procedura descrive il modo più semplice attivare del coinvolgimento Analitica e il monitoraggio delle funzioni nell'applicazione universale di Windows.

La procedura seguente è sufficienti per attivare il report del log necessari per calcolare tutte le statistiche relative agli utenti, sessioni, attività, anomalo e Technicals. Report sui registri necessari per calcolare altre statistiche come eventi, gli errori e processi devono essere eseguiti manualmente tramite l'API di impegno (vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app Windows universale](mobile-engagement-windows-store-use-engagement-api.md) poiché queste statistiche sono dipende dall'applicazione.

## <a name="supported-versions"></a>Versioni supportate

Il coinvolgimento SDK per Windows universale App Mobile possono essere integrate solo in Windows Runtime e nelle applicazioni di piattaforma Windows universale destinazione:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (famiglie di desktop e portatili)

> [AZURE.NOTE] Se assegnazione viene effettuata in Windows Phone Silverlight, consultare la [procedura di integrazione di Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installare SDK App universale di impegno per dispositivi mobili

### <a name="all-platforms"></a>Tutte le piattaforme

L'impegno SDK per Windows universale App Mobile è disponibile come pacchetto Nuget chiamato *MicrosoftAzure.MobileEngagement*. È possibile installare Office da Visual Studio Nuget pacchetto Manager.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8 e Windows Phone 8.1

NuGet distribuisce automaticamente le risorse SDK il `Resources` cartella nella radice del progetto dell'applicazione.

### <a name="windows-10-universal-windows-platform-applications"></a>Applicazioni di piattaforma Windows universale di Windows 10

NuGet non distribuisce automaticamente le risorse SDK nell'applicazione UWP ancora. È necessario farlo manualmente fino a quando non viene reintrodotto distribuzione di risorse in NuGet:

1.  Aprire l'Esplora File.
2.  Passare a uno dei seguenti percorsi (**x.x.x** è la versione di impegno si sta installando): *% profiloutente %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Trascinare e rilasciare la cartella di **risorse** da Esplora file nella radice del progetto in Visual Studio.
4.  In Visual Studio selezionare il progetto e attivare l'icona **Mostra tutti i file** nella parte superiore di **Esplora soluzioni**.
5.  Alcuni file non sono inclusi nel progetto. Per importare loro contemporaneamente destra fare clic su della cartella di **risorse** , **escludere dal progetto** quindi un'altra scelta rapida della cartella di **risorse** , **Includi nel progetto** per includere l'intera cartella di nuovo. Tutti i file dalla cartella **risorse** sono ora inclusi nel progetto.

## <a name="add-the-capabilities"></a>Aggiungere le funzionalità

SDK coinvolgimento deve alcune funzionalità di Windows SDK per il corretto funzionamento.

Aprire il `Package.appxmanifest` file e assicurarsi che vengano dichiarate le funzionalità seguenti:

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inizializzare coinvolgimento SDK

### <a name="engagement-configuration"></a>Configurazione di impegno

La configurazione di impegno è centralizzata nel `Resources\EngagementConfiguration.xml` file del progetto.

Modificare il file per specificare:

-   Stringa di connessione dell'applicazione tra tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale classica di Azure.

### <a name="engagement-initialization"></a>Inizializzazione coinvolgimento

Quando si crea un nuovo progetto, un `App.xaml.cs` file viene generato. Questa classe eredita `Application` e sono disponibili numerosi metodi importanti. Verrà inoltre utilizzato per inizializzare SDK coinvolgimento.

Modificare il `App.xaml.cs`:

-   Aggiungere il `using` istruzioni:

        using Microsoft.Azure.Engagement;

-   Definire un metodo per condividere l'inizializzazione coinvolgimento una volta per tutte le chiamate:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Chiamare `InitEngagement` nel `OnLaunched` metodo:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   All'avvio dell'applicazione utilizzando una combinazione personalizzata, un'altra applicazione o riga di comando il `OnActivated` metodo. Inoltre, è necessario avviare SDK coinvolgimento quando si attiva l'app. A tale scopo, ignorare `OnActivated` metodo:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Abbiamo invitare è possibile aggiungere inizializzazione impegno in un'altra posizione dell'applicazione.

## <a name="basic-reporting"></a>Report di base

### <a name="recommended-method-overload-your-page-classes"></a>Metodo consigliato: overload il `Page` classi

Per attivare il report di tutti i registri necessari affinché impegno per calcolare gli utenti, sessioni, attività, anomalo e le statistiche tecniche, è possibile semplicemente apportare tutte le `Page` classi secondari ereditano il `EngagementPage` classi.

Ecco un esempio di come eseguire questa operazione per una pagina dell'applicazione. È possibile eseguire la stessa operazione per tutte le pagine dell'applicazione.

#### <a name="c-source-file"></a>File di origine c#

Modificare la pagina `.xaml.cs` file:

-   Aggiungere il `using` istruzioni:

        using Microsoft.Azure.Engagement;

-   Sostituire `Page` con `EngagementPage`:

**Senza impegno:**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Con coinvolgimento:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Se la pagina esegue l'override di `OnNavigatedTo` metodo, assicurarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verranno segnalato (il `EngagementPage` chiamate `StartActivity` all'interno il `OnNavigatedTo` metodo).

#### <a name="xaml-file"></a>File XAML

Modificare la pagina `.xaml` file:

-   Aggiungere le dichiarazioni di spazi dei nomi:

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Sostituire `Page` con `engagement:EngagementPage`:

**Senza impegno:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Con coinvolgimento:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Ignorare il comportamento predefinito

Per impostazione predefinita, il nome della classe della pagina viene indicato che il nome dell'attività, con senza aggiuntivi. Se la classe utilizza il suffisso "Pagina", impegno verrà rimossa anche.

Se si vuole ignorare il comportamento predefinito per il nome, è sufficiente aggiungere questo al codice:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Se si desidera segnalare alcune informazioni aggiuntive con le attività, è possibile aggiungere il codice:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Questi metodi vengono chiamati dall'interno di `OnNavigatedTo` metodo della pagina.

### <a name="alternate-method-call-startactivity-manually"></a>Alternare metodo: chiamare `StartActivity()` manualmente

Se non è possibile o non si desidera overload il `Page` classi, è possibile iniziare l'attività invece chiamando `EngagementAgent` metodi direttamente.

È consigliabile chiamare `StartActivity` all'interno del `OnNavigatedTo` metodo della pagina.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Assicurarsi che terminare la sessione correttamente.
> 
> In Windows SDK universale chiama automaticamente il `EndActivity` metodo alla chiusura dell'applicazione. In questo modo, è **altamente** consigliati per chiamare la `StartActivity` metodo ogni volta che l'attività dell'utente modificare e su **mai** chiamare il `EndActivity` metodo, questo metodo invia al server di impegno che utente corrente è lasciare l'applicazione, ciò influisce su tutti i registri di applicazione.

## <a name="advanced-reporting"></a>Segnalazione avanzate

Se si desidera, è consigliabile eventi specifici dell'applicazione di report, gli errori e processi, a tale scopo, utilizzare gli altri metodi disponibili nella `EngagementAgent` classe. L'API coinvolgimento consente di utilizzare tutte le funzionalità avanzate di impegno.

Per ulteriori informazioni, vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configurazione avanzata

### <a name="disable-automatic-crash-reporting"></a>Disattivare la segnalazione di arresto anomalo automatico

È possibile disattivare l'arresto anomalo automatica delle funzionalità di impegno report. Quindi, quando si verifica un'eccezione non gestita, impegno non esegue alcuna operazione.

> [AZURE.WARNING] Se si prevede di disabilitare questa caratteristica, tenere presente che quando si verifica un arresto anomalo non gestito nell'app, impegno non invierà che un arresto anomalo **e** non verrà chiusa la sessione e processi.

Per disattivare la segnalazione di arresto anomalo automatico, personalizzare solo la configurazione in base il modo in cui che è stato dichiarato:

#### <a name="from-engagementconfigurationxml-file"></a>Da `EngagementConfiguration.xml` file

Impostare un arresto anomalo report `false` tra `<reportCrash>` e `</reportCrash>` tag.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Da `EngagementConfiguration` oggetto in fase di esecuzione

Impostare un arresto anomalo report su false usando l'oggetto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modalità burst

Per impostazione predefinita, i report del servizio coinvolgimento registra in tempo reale. Se l'applicazione dei registri molto spesso, è consigliabile per i registri di buffer e per inviare una segnalazione tutte contemporaneamente su un orario base (questo comando si chiama "modalità burst").

A tale scopo, chiamare il metodo:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, solo se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza parametri o con il valore 0.

La modalità burst leggermente prolungare l'autonomia ma ha un impatto sul Monitor coinvolgimento: tutte le sessioni e processi la durata verrà arrotondato alla soglia burst (in questo modo, sessioni e processi inferiore soglia burst potrebbe non essere visibile). È consigliabile usare una soglia burst non è più di 30000 (30s). È necessario tenere presente che registri salvati sono limitate a 300 elementi. Se l'invio è troppo lungo è possibile perdere alcuni log.

> [AZURE.WARNING] Non è possibile configurare la soglia burst per un periodo inferiore alla 1s. Se si tenta di eseguire questa operazione, SDK visualizzerà una traccia con l'errore e di reimpostazione automatica per il valore predefinito, ad esempio, 0. Questo attiverà SDK per segnalare i log in tempo reale.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
