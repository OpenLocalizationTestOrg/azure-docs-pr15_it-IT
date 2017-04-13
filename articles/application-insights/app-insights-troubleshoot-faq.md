<properties 
    pageTitle="Risoluzione dei problemi e domande sull'applicazione approfondimenti" 
    description="Destinati in Visual Studio applicazione approfondimenti chiaro e non funziona? Provare qui." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Domande - approfondimenti applicazione per ASP.NET

## <a name="configuration-problems"></a>Problemi di configurazione

*Si è verificati problemi con la configurazione personale:*

* [App .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'app già in esecuzione](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnostica Windows Azure](app-insights-azure-diagnostics.md)
* [Linguaggio web app](app-insights-java-troubleshoot.md)
* [Altre piattaforme](app-insights-platforms.md)

*Viene non visualizzato dati dal server*

* [Eccezioni firewall set](app-insights-ip-addresses.md)
* [Configurare un server di ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurare un server Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>È possibile utilizzare informazioni dettagliate sui applicazione con...?

[Vedere piattaforme][platforms]


## <a name="is-it-free"></a>È gratuito?

* Sì, se si sceglie gratuito [prezzi livello](app-insights-pricing.md). È possibile ottenere la maggior parte delle caratteristiche e le quote e dei dati. 
* È necessario fornire i dati sulla carta di credito per registrare con Microsoft Azure, ma non in base alle tariffe saranno a meno che non si usa un altro pagato per Azure servizio o l'aggiornamento in modo esplicito a un livello di pagamento.
* Se l'app invia più dati rispetto a quota mensile per il livello gratuito, si interrompe la registrazione. In tal caso, è possibile selezionare per avviare il pagamento o attendere finché non viene reimpostata la quota alla fine del mese.
* Dati di sessione e l'uso di base non sono soggetto a una quota.
* È inoltre disponibile una prova gratuita di 30 giorni, durante il quale si riceve le caratteristiche a pagamento gratuitamente.
* Ogni risorsa applicazione ha una quota separata e impostare il livello prezzo indipendente tutti gli altri.

#### <a name="what-do-i-get-if-i-pay"></a>Che cos'è ottenere se paga?

* Una [quota mensile dei dati](https://azure.microsoft.com/pricing/details/application-insights/)più grande.
* Opzione di pagamento elaborazione per continuare la raccolta di dati sopra la quota mensile. Se i dati supera la quota, viene addebitata per Mb.
* È possibile [esportare continua](app-insights-export-telemetry.md).


## <a name="q14"></a>Cosa modificare applicazione approfondimenti progetto?

I dettagli dipendono dal tipo di progetto. Per un'applicazione web:


+ Consente di aggiungere questi file al progetto:

 + ApplicationInsights.config. 
 + ai.js


+ Consente di installare questi pacchetti NuGet:

 -  *API approfondimenti application* - API di base

 -  *Applicazione approfondimenti API per le applicazioni Web* - utilizzato per l'invio di telemetria dal server

 -  *Applicazione approfondimenti API JavaScript Applications* - utilizzato per l'invio di telemetria dal client

    I pacchetti di includono questi assembly:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Consente di inserire elementi in:

 - Config

 - Packages.config

+ (Nuovi progetti solo - se si [aggiungere informazioni dettagliate sui applicazione a un progetto esistente][start], è necessario eseguire questa operazione manualmente.) Inserisce frammenti di codice client e server per poterli inizializzare con l'ID di risorsa approfondimenti applicazione. Ad esempio, in un'app MVC codice viene inserito nella pagina master Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Come si esegue l'aggiornamento da versioni precedenti di SDK?

Vedere [le note sulla versione](app-insights-release-notes.md) per SDK appropriato per il tipo di applicazione. 



## <a name="update"></a>Come è possibile modificare la risorsa Azure progetto invia dati a?

In Esplora soluzioni fare doppio clic `ApplicationInsights.config` e scegliere **Aggiorna applicazione approfondimenti**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato viene modificato il tasto strumentazione in ApplicationInsights.config, che determina quale server SDK invia i dati. A meno che non si deseleziona "Aggiorna tutto", cambierà anche il tasto nel punto in cui viene visualizzato nelle pagine web.


#### <a name="data"></a>Per quanto tempo viene mantenuti dati nel portale È protetto?

Da un'occhiata [conservazione dei dati e la Privacy][data].

## <a name="logging"></a>Registrazione

#### <a name="post"></a>Come è possibile visualizzare i dati inviati nella ricerca diagnostica?

Abbiamo non registrare i dati POST automaticamente, ma è possibile utilizzare una chiamata TrackTrace: inserire i dati nel parametro message. Si ha un limite di dimensioni più inferiore ai limiti per le proprietà di stringa, anche se non è possibile filtrare. 

## <a name="security"></a>Sicurezza

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>I dati sono al sicuro nel portale? Per quanto tempo è conservato?

Vedere [dati criteri di conservazione e la Privacy][data].


## <a name="q17"></a>Ho è abilitata tutti gli elementi nell'applicazione approfondimenti?

<table border="1">
<tr><th>Verrà visualizzato</th><th>Come ottenerlo</th><th>Perché si desidera</th></tr>
<tr><td>Grafici di disponibilità</td><td><a href="../app-insights-monitor-web-app-availability/">Test Web</a></td><td>Fonti di informazioni che è un'applicazione web</td></tr>
<tr><td>Delle prestazioni app server: tempi di risposta,...
</td><td><a href="../app-insights-asp-net/">Aggiungere informazioni dettagliate sui applicazione al progetto</a><br/>o <br/><a href="../app-insights-monitor-performance-live-website-now/">Installare AI stato Monitor nel server</a> (o scrivere codice personalizzato per <a href="../app-insights-api-custom-events-metrics/#track-dependency">tenere traccia delle dipendenze</a>)</td><td>Rilevare i problemi di prestazioni</td></tr>
<tr><td>Telemetria dipendenza</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installare AI stato Monitor a un server</a></td><td>Diagnosticare i problemi con i database o altri componenti esterni</td></tr>
<tr><td>È possibile ottenere le tracce dello stack dall'eccezioni</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserire le chiamate TrackException nel codice</a> (ma alcune sono segnalati automaticamente)</td><td>Rilevare e diagnosticare eccezioni</td></tr>
<tr><td>Tracce di log di ricerca</td><td><a href="../app-insights-search-diagnostic-logs/">Aggiungere una scheda di registrazione</a></td><td>Diagnosticare eccezioni, problemi di prestazioni</td></tr>
<tr><td>Nozioni fondamentali sull'utilizzo di client: visualizzazioni delle pagine, le sessioni,...</td><td><a href="../app-insights-javascript/">Inizializzatore JavaScript nelle pagine web</a></td><td>Analitica l'uso</td></tr>
<tr><td>Metrica personalizzata client</td><td><a href="../app-insights-api-custom-events-metrics/">Verifica chiama nelle pagine web</a></td><td>Migliorare l'esperienza utente</td></tr>
<tr><td>Metrica personalizzata di server</td><td><a href="../app-insights-api-custom-events-metrics/">Verifica chiamate nel codice lato server</a></td><td>Funzionalità di business intelligence</td></tr>
</table>


## <a name="automation"></a>Automazione

È possibile [scrivere gli script di PowerShell](app-insights-powershell.md) per creare e aggiornare le risorse approfondimenti applicazione.

## <a name="more-answers"></a>Altre risposte

* [Forum approfondimenti applicazione](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 