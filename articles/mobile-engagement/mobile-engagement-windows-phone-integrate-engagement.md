<properties 
    pageTitle="Integrazione di Windows Phone Silverlight coinvolgimento SDK" 
    description="Come integrare Azure impegno per dispositivi mobili con Windows Phone Silverlight App"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Integrazione di Windows Phone Silverlight coinvolgimento SDK

> [AZURE.SELECTOR] 
- [Universale di Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Questa procedura descrive il modo più semplice attivare Azure Mobile coinvolgimento Analitica e il monitoraggio delle funzioni nell'applicazione di Windows Phone Silverlight.

La procedura seguente è sufficienti per attivare il report del log necessari per calcolare tutte le statistiche relative agli utenti, sessioni, attività, anomalo e Technicals. Report sui registri necessari per calcolare altre statistiche come eventi, gli errori e processi devono essere eseguiti manualmente tramite l'API di impegno (vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ) perché questi statistiche sono dipendenti dall'applicazione.

##<a name="supported-versions"></a>Versioni supportate

Mobile coinvolgimento SDK per Windows Silverlight può essere integrato solo nelle applicazioni basate su:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Se si utilizza Windows Phone 8.1 (non Silverlight), fare riferimento alla [procedura integrazione universale di Windows](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Installare Silverlight SDK impegno per dispositivi mobili

Mobile coinvolgimento SDK per Windows Silverlight è disponibile come pacchetto Nuget chiamato *MicrosoftAzure.MobileEngagement*. È possibile installare Office da Visual Studio Nuget pacchetto Manager. 

##<a name="add-the-capabilities"></a>Aggiungere le funzionalità

SDK coinvolgimento deve alcune funzionalità di Windows Phone Silverlight SDK per il corretto funzionamento.

Aprire il `WMAppManifest.xml` file e assicurarsi che le seguenti funzionalità sono dichiarate nel `Capabilities` pannello:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Inizializzare coinvolgimento SDK

### <a name="engagement-configuration"></a>Configurazione di impegno

La configurazione di impegno è centralizzata nel `Resources\EngagementConfiguration.xml` file del progetto.

Modificare il file per specificare:

-   Stringa di connessione dell'applicazione tra tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale classica di Azure.

### <a name="engagement-initialization"></a>Inizializzazione coinvolgimento

Quando si crea un nuovo progetto, un `App.xaml.cs` file viene generato. Questa classe eredita `Application` e sono disponibili numerosi metodi importanti. Verrà inoltre utilizzato per inizializzare SDK coinvolgimento.

Modificare il `App.xaml.cs`:

-   Aggiungere il `using` istruzioni:

        using Microsoft.Azure.Engagement;

-   Inserire `EngagementAgent.Instance.Init` nel `Application_Launching` metodo:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Inserire `EngagementAgent.Instance.OnActivated` nel `Application_Activated` metodo:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Abbiamo invitare è possibile aggiungere inizializzazione impegno in un'altra posizione dell'applicazione. Tuttavia, tenere presente che il `EngagementAgent.Instance.Init` metodo viene eseguito un thread dedicato e non al thread dell'interfaccia utente.

##<a name="basic-reporting"></a>Report di base

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Metodo consigliato: overload il `PhoneApplicationPage` classi

Per attivare il report di tutti i registri necessari affinché impegno per calcolare gli utenti, sessioni, attività, anomalo e le statistiche tecniche, è possibile semplicemente apportare tutte le `PhoneApplicationPage` classi secondari ereditano il `EngagementPage` classi.

Ecco un esempio di come eseguire questa operazione per una pagina dell'applicazione. È possibile eseguire la stessa operazione per tutte le pagine dell'applicazione.

#### <a name="c-source-file"></a>File di origine c#

Modificare la pagina `.xaml.cs` file:

-   Aggiungere il `using` istruzioni:

        using Microsoft.Azure.Engagement;

-   Sostituire `PhoneApplicationPage` con `EngagementPage` :

**Senza impegno:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Con coinvolgimento:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Se la pagina eredita le `OnNavigatedTo` metodo, prestare attenzione affinché la `base.OnNavigatedTo(e)` chiamata. In caso contrario, l'attività non verranno segnalato. In effetti, il `EngagementPage` chiama `StartActivity` all'interno di `OnNavigatedTo` metodo.

#### <a name="xaml-file"></a>File XAML

Modificare la pagina `.xaml` file:

-   Aggiungere le dichiarazioni di spazi dei nomi:

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Sostituire `phone:PhoneApplicationPage` con `engagement:EngagementPage` :

**Senza impegno:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Con coinvolgimento:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Ignorare il comportamento predefinito

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

Se non è possibile o non si desidera overload il `PhoneApplicationPage` classi, è possibile iniziare l'attività invece chiamando `EngagementAgent` metodi direttamente.

È consigliabile chiamare `StartActivity` all'interno del `OnNavigatedTo` metodo del PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Assicurarsi che terminare la sessione correttamente.
>
> SDK chiama automaticamente il `EndActivity` metodo alla chiusura dell'applicazione. In questo modo, è **altamente** consigliati per chiamare la `StartActivity` metodo ogni volta che l'attività dell'utente modificare e su **mai** chiamare il `EndActivity` metodo. Questo metodo invia un messaggio al server di impegno che l'utente corrente ha lasciato l'applicazione e ciò influisce su tutti i registri di applicazione.

##<a name="advanced-reporting"></a>Segnalazione avanzate

Se si desidera, è consigliabile eventi specifici dell'applicazione di report, gli errori e processi, a tale scopo, utilizzare gli altri metodi disponibili nella `EngagementAgent` classe. L'API coinvolgimento consente di utilizzare tutte le funzionalità avanzate di impegno.

Per ulteriori informazioni, vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configurazione avanzata

### <a name="disable-automatic-crash-reporting"></a>Disattivare la segnalazione di arresto anomalo automatico

È possibile disattivare l'arresto anomalo automatica delle funzionalità di impegno report. Quindi, quando si verifica un'eccezione non gestita, impegno non esegue alcuna operazione.

> [AZURE.WARNING] Se si prevede di disabilitare questa caratteristica, tenere presente che quando si verifica un arresto anomalo non gestito nell'app, impegno non invierà un arresto anomalo **e** non verrà chiuso la sessione e processi.

Per disattivare la segnalazione di arresto anomalo automatico, personalizzare solo la configurazione in base il modo in cui che è stato dichiarato:

#### <a name="from-engagementconfigurationxml-file"></a>Da `EngagementConfiguration.xml` file

Impostare un arresto anomalo report `false` tra `<reportCrash>` e `</reportCrash>` tag.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Da `EngagementConfiguration` oggetto in fase di esecuzione

Impostare un arresto anomalo report su false usando l'oggetto EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modalità burst

Per impostazione predefinita, i report del servizio coinvolgimento registra in tempo reale. Se l'applicazione dei registri molto spesso, è consigliabile per i registri di buffer e per inviare una segnalazione tutte contemporaneamente su un orario base (questo comando si chiama "modalità burst").

A tale scopo, chiamare il metodo:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. In qualsiasi momento, solo se si desidera riattivare la registrazione in tempo reale, chiamare il metodo senza parametri o con il valore 0.

La modalità burst leggermente prolungare l'autonomia ma ha un impatto sul Monitor coinvolgimento: tutte le sessioni e processi la durata verrà arrotondato alla soglia burst (in questo modo, sessioni e processi inferiore soglia burst potrebbe non essere visibile). È consigliabile usare una soglia burst non è più di 30000 (30s). È necessario tenere presente che registri salvati sono limitate a 300 elementi. Se l'invio è troppo lungo è possibile perdere alcuni log.

> [AZURE.WARNING] Soglia burst non può essere configurata per un periodo minore rispetto a un secondo. Se si tenta di eseguire in modo che il SDK visualizzerà una traccia con l'errore e verrà automaticamente reimpostare il valore predefinito, vale a dire zero secondi. Questo attiverà SDK per segnalare i log in tempo reale.
 
