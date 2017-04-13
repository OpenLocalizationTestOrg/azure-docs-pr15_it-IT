<properties
    pageTitle="Report avanzati con coinvolgimento MobileApps universale di Windows"
    description="Come integrare App universale di Windows Azure impegno per dispositivi mobili"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Report avanzati con il coinvolgimento di App universale di Windows SDK

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

In questo argomento descrive scenari di creazione di report aggiuntivi nell'applicazione universale di Windows. Gli scenari seguenti sono disponibili opzioni che è possibile scegliere di applicare all'app creata in esercitazione [Introduzione](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Prima di iniziare questa esercitazione, è prima necessario completare l'esercitazione [Introduzione](mobile-engagement-windows-store-dotnet-get-started.md) , ossia deliberatamente semplice e diretto. In questa esercitazione è descritta è possibile scegliere altre opzioni.

## <a name="specifying-engagement-configuration-at-runtime"></a>Configurazione di impegno specificando in fase di esecuzione

La configurazione di impegno è centralizzata nel `Resources\EngagementConfiguration.xml` file del progetto, in cui è indicato nell'argomento della [Guida introduttiva](mobile-engagement-windows-store-dotnet-get-started.md) .

Ma è possibile specificare anche in fase di esecuzione: è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Metodo consigliato: overload il `Page` classi

Per attivare la segnalazione di tutti i log necessari affinché impegno per calcolare gli utenti, sessioni, attività, anomalo e le statistiche tecniche, apportare tutte le `Page` classi secondari ereditano il `EngagementPage` classi.

Ecco un esempio di una pagina dell'applicazione. È possibile eseguire la stessa operazione per tutte le pagine dell'applicazione.

### <a name="c-source-file"></a>File di origine c#

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

> [AZURE.IMPORTANT] Se la pagina esegue l'override di `OnNavigatedTo` metodo, assicurarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verranno segnalati (il `EngagementPage` chiamate `StartActivity` all'interno il `OnNavigatedTo` metodo).

### <a name="xaml-file"></a>File XAML

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

### <a name="override-the-default-behaviour"></a>Ignorare il comportamento predefinito

Per impostazione predefinita, il nome della classe della pagina viene indicato che il nome dell'attività, con senza aggiuntivi. Se la classe utilizza il suffisso "Pagina", coinvolgimento rimuove.

Per ignorare il comportamento predefinito per il nome, aggiungere il codice:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Per segnalare informazioni aggiuntive con le attività, aggiungere il codice:

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
> In Windows SDK universale chiama automaticamente il `EndActivity` metodo alla chiusura dell'applicazione. In questo modo, è **altamente** consigliati per chiamare la `StartActivity` metodo ogni volta che l'attività dell'utente modificare e su **mai** chiamare il `EndActivity` metodo. Questo metodo notifica server impegno che l'utente corrente ha lasciato l'applicazione, che ha un impatto tutti i registri di applicazione.

## <a name="advanced-reporting"></a>Segnalazione avanzate

Facoltativamente, è possibile inviare una segnalazione eventi specifiche di un'applicazione, gli errori e processi, a tale scopo, utilizzano altri metodi disponibili nella `EngagementAgent` classe. L'API coinvolgimento consente uso delle funzionalità avanzate di impegno tutti.

Per ulteriori informazioni, vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API nell'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md).
