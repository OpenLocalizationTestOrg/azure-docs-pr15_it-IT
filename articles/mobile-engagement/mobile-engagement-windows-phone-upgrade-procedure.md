<properties 
    pageTitle="Windows Phone Silverlight SDK procedure di aggiornamento" 
    description="Windows Phone Silverlight SDK procedure di aggiornamento per Azure impegno per dispositivi mobili"                  
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

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK procedure di aggiornamento

Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

È necessario eseguire alcune procedure notato diverse versioni di SDK. Se, ad esempio la migrazione da 0.10.1 a 0.11.0 che è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1" quindi la procedura "da 0.10.1 a 0.11.0".

##<a name="from-200-to-330"></a>Da 2.0.0 a 3.3.0

### <a name="test-logs"></a>Log di test

Log degli console prodotti da SDK può essere attivato/disattivato/filtrati. Per personalizzare questo, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` un valore disponibile la `EngagementTestLogLevel` enumerazione, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>Da 1.1.1 a 2.0.0

Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio di Capptain fornito dal Capptain SAS in un'app con tecnologia Azure Mobile coinvolgimento. 

> [Azure.IMPORTANT] Capptain e coinvolgimento Mobile non sono gli stessi servizi e la procedura indicata di seguito viene evidenziato solo come eseguire la migrazione dell'applicazione client. Migrazione SDK nell'app verrà non eseguire la migrazione dei dati da server Capptain ai server coinvolgimento Mobile

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire la migrazione a 1.1.1 prima di tutto, quindi applicare la procedura seguente

### <a name="nuget-package"></a>Pacchetto NuGet

Sostituire **Capptain.WindowsPhone** pacchetto Nuget **MicrosoftAzure.MobileEngagement** .

### <a name="applying-mobile-engagement"></a>L'applicazione di impegno per dispositivi mobili

il SDK viene utilizzato il termine `Engagement`. È necessario aggiornare il progetto in base a questa modifica.

È necessario disinstallare il pacchetto nuget Capptain corrente. Tenere presente che verranno rimosse tutte le modifiche nella cartella Capptain risorse. Se si desidera mantenere i file, quindi eseguire una copia di esse.

Successivamente, installare il nuovo pacchetto nuget coinvolgimento di Microsoft Azure sul progetto. È possibile trovare direttamente nel [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Questa azione sostituisce tutti i file di risorse utilizzati in coinvolgimento e aggiunge la nuova DLL coinvolgimento ai riferimenti del progetto.

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

4. Per altre risorse come Capptain le immagini, tenere presente che anche essere stati rinominati per utilizzare "Impegno".

### <a name="application-id--sdk-key"></a>ID applicazione / chiave SDK

Coinvolgimento utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e un tasto SDK con coinvolgimento di dispositivi mobili, è sufficiente specificare una stringa di connessione. È possibile impostare sul file EngagementConfiguration.

La configurazione di impegno può essere impostata nel `Resources\EngagementConfiguration.xml` file del progetto.

Modificare il file per specificare:

-   Stringa di connessione dell'applicazione tra tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima l'inizializzazione agente impegno:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

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



 
