<properties 
    pageTitle="Note sulla versione per informazioni dettagliate sui applicazione per Windows" 
    description="Gli aggiornamenti più recenti per Windows Store SDK." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Note sulla versione per informazioni dettagliate sui applicazione SDK per Windows Phone e archiviare

Applicazione approfondimenti SDK invia telemetria sull'applicazione live per [Informazioni dettagliate sui applicazione](https://azure.microsoft.com/services/application-insights/), in cui è possibile analizzare il relativo utilizzo e le prestazioni.


## <a name="version-111"></a>Versione 1.1.1

### <a name="windows-sdk"></a>Windows SDK

- Risolvere un blocco durante l'arresto anomalo quando si usa Silverlight SDK di Windows Phone. Dopo questa modifica, verrà mantenuto qualsiasi arresto anomalo che succede oltre ~ 2 secondi dopo la chiamata a WindowsAppInitialier.InitializeAsync(...) a su disco e verrà inviata al successivo avvio dell'app. Se si verifica un arresto anomalo prima ~ 2 secondi dopo la chiamata, verrà ignorata.  
- Impostare dipendenze del NuGet a una versione specifica del principale e Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>SDK di base

- Principali viene gestita in github. Note sulla versione futura di Core SDK sono disponibili [in github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Versione 1.1

### <a name="core-sdk"></a>SDK di base

- SDK presenta un nuovo tipo di telemetria ```DependencyTelemetry``` che contiene informazioni sulla chiamata dipendenza dall'applicazione
- Nuovo metodo ```TelemetryClient.TrackDependency``` consente di inviare informazioni sulle chiamate dipendenza dall'applicazione

## <a name="version-100"></a>Versione 1.0.0

### <a name="windows-app-sdk"></a>App di Windows SDK

- Nuova inizializzazione per le applicazioni di Windows. Nuova `WindowsAppInitializer` con `InitializeAsync()` metodo consente l'avvio automatico di inizializzazione della raccolta SDK. Questa modifica consentire a un controllo più preciso e miglioramenti delle prestazioni inizializzazione app significativa su tecnica ApplicationInsights.config precedente.
- DeveloperMode viene impostata non è più automaticamente. Per modificare il comportamento DeveloperMode che è necessario specificare nel codice.
- Pacchetto NuGet inserisce non è più ApplicationInsights.config. È consigliabile utilizzare il nuovo WindowsAppInitializer quando si aggiungono manualmente pacchetto NuGet.
- Legge solo ApplicationInsights.config `<InstrumentationKey>`, come preferenza per accedere a impostazioni WindowsAppInitializer vengono ignorate tutte le altre impostazioni.
- Punto vendita sarà automatico raccolta da SDK.
- Molte correzioni di bug, stabilità e miglioramenti delle prestazioni.

### <a name="core-sdk"></a>SDK di base

- File ApplicationInsights.config non è più requiered. E non aggiunte dal pacchetto NuGet. Configurazione può essere completamente specificata nel codice.
- Pacchetto NuGet non aggiungerà non è più un file di destinazione alla soluzione. Consente di rimuovere l'impostazione automatica di DeveloperMode durante la compilazione di debug. DeveloperMode impostare manualmente nel codice.

## <a name="version-017"></a>Versione 0,17

### <a name="windows-app-sdk"></a>App di Windows SDK

- App di Windows SDK supporta ora universale app di Windows create con Windows 10 technical preview e con RC 2015 VS.

### <a name="core-sdk"></a>SDK di base

- TelemetryClient le impostazioni predefinite per inizializzare con la InMemoryChannel.
- Nuova API aggiunto, `TelemetryClient.Flush()`. Questo metodo svuotamento attiverà un caricamento blocco immediato di telemetria tutti ha eseguito l'accesso al client. In questo modo attivazione manuale di caricamento prima di arresto del processo.
- Pacchetto NuGet aggiunto un valore di destinazione .net 4.5. Questa destinazione senza dipendenze esterne (rimosso libreria di classi base ed EventSource dipendenze).

## <a name="version-016"></a>Versione 0,16 

Anteprima 2015-04-28

- SDK supporta ora la piattaforma di destinazione DNX per attivare il monitoraggio delle applicazioni di [base di .NET framework](http://www.dotnetfoundation.org/NETCore5) .
- Le istanze di ```TelemetryClient``` non memorizzare nella cache strumentazione tasto più. Ora, se non è stata impostata strumentazione chiave ```TelemetryClient``` in modo esplicito ```InstrumentationKey``` restituirà null. Risolvere un problema quando si impostano ```TelemetryConfiguration.Active.InstrumentationKey``` dopo alcuni telemetria già raccolti, i moduli di telemetria come Raccoglitore dipendenza, web richieste insieme e prestazioni contatori di raccolta dati utilizzerà nuova chiave strumentazione.

## <a name="version-015"></a>Versione 0,15

- Nuova proprietà ```Operation.SyntheticSource``` ora disponibile in ```TelemetryContext```. A questo punto è possibile contrassegnare gli elementi di telemetria come "non il traffico utente reale" e specificare la modalità di generazione il traffico. Ad esempio impostando la proprietà è possibile distinguere il traffico di automazione di test dal traffico di test di carico.
- Logica di canale è stata spostata NuGet separato chiamato Microsoft.ApplicationInsights.PersistenceChannel. Canale predefinito è denominata InMemoryChannel
- Nuovo metodo ```TelemetryClient.Flush``` consente di scaricare gli elementi di telemetria dal buffer sincrono

## <a name="version-013"></a>Versione 0.13

Nessun note sulla versione per le versioni precedenti disponibili. 
