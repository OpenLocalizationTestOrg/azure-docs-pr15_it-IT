<properties
    pageTitle="Rallentare le prestazioni di app web nel servizio App | Microsoft Azure"
    description="In questo articolo consente di risolvere i problemi di prestazioni lente web app in Azure App servizio."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="prestazioni ottimali app Web, app lenta app lenta"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Risolvere i problemi di prestazioni lente web app in Azure App servizio

In questo articolo consente di risolvere i problemi di prestazioni lente web app in [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714).

Se necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile contattare i Azure esperti di [Azure MSDN e nei forum di Overflow dello Stack](https://azure.microsoft.com/support/forums/). In alternativa, è possibile inoltre archiviare una richiesta di assistenza Azure. Fare clic sul **Supporto di accedere**al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintomo

Quando si Esplora il web app, il caricamento di pagine lentamente e talvolta timeout.

## <a name="cause"></a>Causa

Questo problema è spesso causato da problemi a livello dell'applicazione, ad esempio:

-   richieste di impiegando troppo tempo
-   applicazione che utilizza memoria/CPU elevata
-   applicazione di un arresto anomalo a causa di un'eccezione.

## <a name="troubleshooting-steps"></a>Risoluzione dei problemi

Risoluzione dei problemi può essere suddiviso in tre attività distinte, in sequenza:

1.  [Osservare e controllare il comportamento dell'applicazione](#observe)
2.  [Raccolta dati](#collect)
3.  [Mitigare il problema](#mitigate)

[Applicazione del servizio Web Apps](/services/app-service/web/) offre diverse opzioni di ogni passaggio.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. verificare e monitorare il comportamento dell'applicazione

#### <a name="track-service-health"></a>Verificare l'integrità dei servizi

Microsoft Azure publicizes ogni volta che è presente una riduzione delle prestazioni o interruzione del servizio. È possibile verificare lo stato del servizio nel [Portale di Azure](https://portal.azure.com/). Per ulteriori informazioni, vedere [l'integrità dei servizi di rilevamento](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Eseguire il monitoraggio delle app web

Questa opzione consente di scoprire se l'applicazione in caso di eventuali problemi. In blade dell'applicazione web, fare clic sul riquadro **richieste ed errori** . E **l'unità di misura metriche** verrà visualizzate tutte le metriche è possibile aggiungere.

Alcuni dei valori che è possibile monitorare per un'app web

-   Media della memoria insieme di lavoro
-   Tempo medio di risposta
-   Tempo CPU
-   Working set della memoria
-   Richieste di

![eseguire il monitoraggio delle prestazioni web app](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Per ulteriori informazioni, vedere:

-   [Monitoraggio App Web nel servizio App Azure](web-sites-monitor.md)
-   [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Controllare lo stato dell'endpoint web

Se si esegue un'app web in **Standard** prezzi livello, Web Apps consente di eseguire il monitoraggio 2 endpoint di 3 aree geografiche.

Endpoint monitoraggio Configura di percorsi distribuito geografico per testare il tempo di risposta e il tempo di attività di URL web del test web. Il test esegue un'operazione HTTP GET sull'URL web per determinare il tempo di risposta e i tempi di attività da ogni posizione. Ogni posizione configurata esegue un test ogni cinque minuti.

Tempi di attività viene controllata tramite codici di risposta HTTP e il tempo di risposta espresso in millisecondi. Test di monitoraggio non riesce se il codice di risposta HTTP è maggiore o uguale a 400 o se la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se i test di monitoraggio completata da tutti i percorsi specificati.

Per configurare l'account, vedere [come: monitorare lo stato dell'endpoint web](web-sites-monitor.md#webendpointstatus).

Vedere anche [mantenere i siti Web Azure su più Endpoint monitoraggio - con Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) per un video sull'endpoint monitoraggio.

#### <a name="application-performance-monitoring-using-extensions"></a>Applicazione il monitoraggio delle prestazioni utilizzando le estensioni

È inoltre possibile monitorare le prestazioni dell'applicazione sfruttando _estensioni del sito_.

Ogni servizio App web app offre un punto finale di gestione extensible che consente di sfruttare un gruppo di potenti strumenti distribuito come estensioni del sito. Questi strumenti compresi tra editor di codice sorgente come [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) e gli strumenti di gestione delle risorse connesse, ad esempio un database MySQL connessi a un'app web.

[Informazioni dettagliate sui applicazione Azure](/services/application-insights/) e [Nuovo Relic](/marketplace/partners/newrelic/newrelic/) sono due estensioni sito disponibili di monitoraggio delle prestazioni. Per utilizzare Relic nuovo, installare un agente in fase di esecuzione. Per utilizzare approfondimenti applicazione Azure, si rigenerare il codice a un SDK e installare anche un'estensione che fornisce l'accesso ai dati aggiuntivi. SDK consente di scrivere codice per monitorare l'utilizzo e le prestazioni dell'app in modo più dettagliato.

Per usare applicazione approfondimenti, vedere [monitoraggio delle prestazioni nelle applicazioni web](../application-insights/app-insights-web-monitor-performance.md).

Per usare Relic nuovo, vedere [Gestione delle prestazioni dell'applicazione Relic nuovo in Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />
### <a name="2-collect-data"></a>2. raccolta dati

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>Attivare la registrazione diagnostica per un'app web

L'ambiente Web Apps offre funzionalità di diagnostica per la registrazione delle informazioni dal server web e l'applicazione web. Questi sono suddivise logicamente in diagnostica server web e diagnostica delle applicazioni.

##### <a name="web-server-diagnostics"></a>Diagnostica server Web

È possibile attivare o disattivare i tipi di registri seguenti:

-   **Registrazione dettagliata errore** - informazioni dettagliate sull'errore per i codici di stato HTTP che indicano un errore (codice di stato 400 o versione successiva). Questo potrebbe contenere informazioni che consentono di determinare il motivo per cui viene restituito il codice di errore.
-   **Traccia richieste non riuscite** - informazioni dettagliate sulle richieste non riuscite, tra cui una traccia dei componenti IIS utilizzato per elaborare la richiesta e il tempo trascorso in ogni componente. Può essere utile se si sta tentando di migliorare le prestazioni di app web o isolare la causa un errore HTTP specifico.
-   **Registrazione Server web** - informazioni sulle transazioni HTTP utilizzando il formato di file di registro esteso W3C. Questo è utile per determinare complessiva metriche di app web, ad esempio il numero di richieste gestite o il numero di richieste provengono da un indirizzo IP specifico.

##### <a name="application-diagnostics"></a>Diagnostica delle applicazioni

Applicazione di diagnostica consente di acquisire informazioni sul prodotto da un'applicazione web. Applicazioni ASP.NET possono utilizzare il `System.Diagnostics.Trace` classe per registrare le informazioni nel log di diagnostica dell'applicazione.

Per istruzioni dettagliate su come configurare l'applicazione per la registrazione, vedere [attivare la registrazione diagnostica per App web di Azure App servizio](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Utilizzare analisi remota

Nel servizio di App Azure, è possibile profilo in modalità remota Web Apps, App API e WebJobs. Se il processo viene eseguito più lentamente del previsto, o la latenza delle richieste HTTP superiori rispetto al normale e l'utilizzo della CPU del processo anche sia sufficientemente alto, è possibile in remoto il processo del profilo e ottenere gli stack di campioni di CPU per analizzare le attività di processo e i percorsi hot codice.

Per ulteriori informazioni, vedere [supporto di analisi remota nel servizio di App Azure](/blog/remote-profiling-support-in-azure-app-service).


#### <a name="use-the-azure-app-service-support-portal"></a>Usare il portale di supporto servizio App Azure

Web App consente di risolvere i problemi relativi all'app web esaminando HTTP registri, i registri eventi, processo immagini e altro. È possibile accedere a tutte le informazioni tramite il portale di supporto in **http://&lt;il nome dell'app >.scm.azurewebsites.net/Support**

Portale di Azure App servizio supporto offre tre schede separate per supportare i tre passaggi di uno scenario di risoluzione dei problemi comune:

1.  Osservare comportamento corrente
2.  Analizzare in base alla raccolta delle informazioni di diagnostica e l'esecuzione di analisi incorporati
3.  Mitigare

Se il problema si verifica immediatamente, fare clic su **Analizza** > **diagnostica** > **Diagnosi ora** per creare una sessione di diagnostica per l'utente, che verranno raccolte HTTP registrati, Visualizzatore eventi, immagini, i registri di errori PHP e PHP elaborare report memoria.

Una volta raccolti i dati, verrà anche eseguire un'analisi sui dati e fornire un rapporto HTML.

Nel caso in cui si desidera scaricare i dati, per impostazione predefinita, verrebbero archiviata nella cartella D:\home\data\DaaS.

Per ulteriori informazioni nel portale di Azure App per assistenza, vedere [Nuovi aggiornamenti con estensione di sito di supporto per i siti Web di Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Utilizzare la Console di Debug Kudu

Web App viene fornito con una console di debug che è possibile utilizzare per il debug, esplorare e caricamento di file, come i punti finali JSON per ottenere informazioni sull'ambiente. Questo comando si chiama la _Console Kudu_ o _Servizi Dashboard_ per un'app web.

È possibile accedere questo dashboard facendo clic sul collegamento **https://&lt;il nome dell'app >.scm.azurewebsites.net/**.

Alcune delle operazioni che fornisce Kudu sono:

-   impostazioni dell'ambiente per l'applicazione
-   flusso di registro
-   immagine della diagnostica
-   eseguire il debug console in cui è possibile eseguire i cmdlet di Powershell e comandi DOS base.


Un'altra utile funzionalità di Kudu consiste nel caso in cui l'applicazione sta generando eccezioni prima possibilità, è possibile utilizzare Kudu e scarica lo strumento di SysInternals Procdump per creare la memoria. Le immagini della memoria istantanee del processo e spesso consentono di risolvere i problemi più complicate con l'app web.

Per ulteriori informazioni sulle funzionalità disponibili in Kudu, vedere [Strumenti di servizi di Windows Azure siti del Team che è necessario conoscere sulle](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. mitigare il problema

####    <a name="scale-the-web-app"></a>Ridimensionare il web app

Nel servizio App Azure per migliorare le prestazioni e la velocità effettiva, è possibile modificare la scala in cui si esegue l'applicazione. Scalabilità un'app web prevede due azioni correlate: modificare il piano di servizio App a un livello superiore prezzo e configurazione di alcune impostazioni dopo il cambio al livello superiore prezzo.

Per ulteriori informazioni sulle proporzioni dei caratteri, vedere [scala un'app web nel servizio App Azure](web-sites-scale.md).

Inoltre, è possibile scegliere di eseguire l'applicazione su più istanze. Questa operazione non solo vengono fornite altre funzionalità di elaborazione, ma consente inoltre parte del tolleranza. Se il processo di spostarsi verso il basso in una sola istanza, l'altra istanza continuerà le risposte alle richieste.

È possibile impostare la scala per diventare un manuale o automatico.

####    <a name="use-autoheal"></a>Utilizzare AutoHeal

AutoHeal Ricicla il processo di lavoro per l'app in base alle impostazioni che scegliere (ad esempio le modifiche alla configurazione, le richieste, limiti di memoria o il tempo necessario per eseguire una richiesta). La maggior parte dei casi Cestino il processo è il modo più veloce per risolvere un problema. Anche se è sempre possibile riavviare l'applicazione web da direttamente all'interno del portale di Azure, AutoHeal per l'esecuzione automatica dell'utente. È sufficiente è aggiungere alcuni trigger dello stesso file principale per un'app web. Si noti che queste impostazioni da lavorare allo stesso modo anche se l'applicazione non è un .net uno.

Per ulteriori informazioni, vedere [Correzione automatica di siti Web Azure](/blog/auto-healing-windows-azure-web-sites/).

####    <a name="restart-the-web-app"></a>Riavviare l'applicazione web

Si tratta spesso il modo più semplice per recuperare dalla copia unica problemi. Nel [Portale di Azure](https://portal.azure.com/)nella blade dell'applicazione web, sono disponibili le opzioni per interrompere o riavviare l'app.

 ![Riavviare web app per risolvere i problemi di prestazioni](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

È anche possibile gestire applicazioni web tramite Powershell Azure. Per ulteriori informazioni, vedere [Uso Azure PowerShell Gestione risorse Azure](../powershell-azure-resource-manager.md).
