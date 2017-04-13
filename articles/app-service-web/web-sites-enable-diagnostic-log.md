<properties
    pageTitle="Attivare la registrazione diagnostica per web apps nel servizio App Azure"
    description="Informazioni su come attivare la registrazione diagnostica e aggiungere strumentazione all'applicazione, nonché come accedere alle informazioni registrate da Azure."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="cephalin"/>

# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Attivare la registrazione diagnostica per web apps nel servizio App Azure

## <a name="overview"></a>Panoramica

Azure offre diagnostica per agevolare il debug di un' [applicazione servizio web app](http://go.microsoft.com/fwlink/?LinkId=529714). In questo articolo verrà spiegato come attivare la registrazione diagnostica e aggiungere strumentazione all'applicazione, nonché come accedere alle informazioni registrate da Azure.

In questo articolo utilizza il [Portale di Azure](https://portal.azure.com), PowerShell Azure e l'interfaccia della riga di comando di Azure (Azure CLI) per l'uso con registri diagnostici. Per informazioni sull'utilizzo dei registri diagnostici utilizzando Visual Studio, vedere [Risoluzione dei problemi di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostica server Web e diagnostica delle applicazioni

Servizio App web App forniscono funzionalità diagnostica per la registrazione delle informazioni dal server web e l'applicazione web. Questi sono suddivise logicamente in **diagnostica server web** e **diagnostica delle applicazioni**.

### <a name="web-server-diagnostics"></a>Diagnostica server Web

È possibile attivare o disattivare i tipi di registri seguenti:

- **Registrazione dettagliata errore** - informazioni dettagliate sull'errore per i codici di stato HTTP che indicano un errore (codice di stato 400 o versione successiva). Questo potrebbe contenere informazioni che consentono di determinare il motivo per cui viene restituito il codice di errore.
- **Traccia richieste non riuscite** - informazioni dettagliate sulle richieste non riuscite, tra cui una traccia dei componenti IIS utilizzato per elaborare la richiesta e il tempo trascorso in ogni componente. Può essere utile se si sta tentando di migliorare le prestazioni del sito o isolare la causa un errore HTTP specifico da restituire.
- **Registrazione Server web** - informazioni sulle transazioni HTTP utilizzando il [formato di file di registro esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Questo è utile per determinare complessiva metriche di sito, ad esempio il numero di richieste gestite o il numero di richieste provengono da un indirizzo IP specifico.

### <a name="application-diagnostics"></a>Diagnostica delle applicazioni

Applicazione di diagnostica consente di acquisire informazioni sul prodotto da un'applicazione web. Applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) per registrare le informazioni nel log di diagnostica dell'applicazione. Per esempio:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

In fase di esecuzione è possibile recuperare i registri per agevolare la risoluzione dei problemi. Per ulteriori informazioni, vedere [risoluzione dei problemi di Azure web apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Servizio di App web App accedere anche informazioni sulla distribuzione quando si pubblica il contenuto in un'app web. Si verifica questo evento automaticamente e non sono presenti impostazioni di configurazione per la registrazione di distribuzione. Registrazione di distribuzione consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si utilizza uno script di distribuzione personalizzato, utilizzare la registrazione di distribuzione per determinare il motivo per cui lo script.

## <a name="enablediag"></a>Come abilitare diagnostica

Per abilitare la diagnostica nel [Portale di Azure](https://portal.azure.com), passare a e il per un'app web e fare clic su **Impostazioni > registri di diagnostica**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte dei registri](./media/web-sites-enable-diagnostic-log/logspart.png)

Quando si abilita **la diagnostica delle applicazioni** è inoltre possibile scegliere il **livello**. Questa impostazione consente di filtrare le informazioni acquisite informazioni **informativo**, **avviso** o **errore** . Impostare questo valore su **dettagliato** accederà tutte le informazioni di prodotto dall'applicazione.

> [AZURE.NOTE] Diversamente da quanto succede modifica config, attivazione della diagnostica applicazione o modifica dei livelli del registro diagnostico Cestino non il dominio dell'applicazione che l'applicazione viene eseguita all'interno.

Nella scheda **Configura** app Web [portale classica](https://manage.windowsazure.com) , è possibile selezionare **lo spazio di archiviazione** o di un **sistema di file** per **la registrazione server web**. Selezione di **spazio di archiviazione** consente di selezionare un account di archiviazione, quindi un contenitore di blob scritta per i log. Tutti gli altri per **diagnostica sito** registri solo nel file System.

La scheda di **Configura** [classica portale](https://manage.windowsazure.com) Web app è installato anche altre impostazioni per diagnostica applicazione:

* **File system** - archivia le informazioni di diagnostica applicazione nel file System app web. Questi file possono essere accessibile tramite FTP o scaricati come un archivio Zip tramite PowerShell Azure o Azure della riga di comando CLI (interfaccia Azure).
* **Archiviazione tabella** - archivia le informazioni di diagnostica applicazione del nome dell'Account di archiviazione di Azure e tabella specificata.
* **Archiviazione blob** - archivia le informazioni di diagnostica applicazione nel contenitore di Account di archiviazione di Azure e blob specificato.
* **Periodo di conservazione** - per impostazione predefinita, i registri non vengono automaticamente eliminati dallo **spazio di archiviazione blob**. Selezionare **set di criteri di conservazione** e immettere il numero di giorni per mantenere i log se si desidera eliminare automaticamente i registri.

>[AZURE.NOTE] Se si [rigenerare tasti di scelta del proprio account di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), è necessario reimpostare la configurazione di registrazione relative a utilizzare i tasti aggiornati. Per procedere come segue:
>
> 1. Nella scheda **Configura** , impostare la funzionalità di registrazione relative a **disattivato**. Salvare l'impostazione.
> 2. Attivare la registrazione per l'archivio blob di account di archiviazione o la tabella. Salvare l'impostazione.

Qualsiasi combinazione di file system, archiviazione tabelle o archiviazione blob può essere attivato nello stesso momento e hanno configurazioni a livello di singoli log. Ad esempio, che si desidera registrare errori e avvisi da nell'archiviazione blob a lungo termine della registrazione, durante l'attivazione della registrazione di sistema di file con un livello di dettaglio.

Mentre tutti i percorsi di archiviazione tre forniscono le stesse informazioni di base per gli eventi registrati, **archiviazione tabella** e **archiviazione blob** registro ulteriori informazioni, ad esempio l'ID dell'istanza, ID thread e l'ora più granulare (formato dei segni di graduazione) di accesso al **file system**.

> [AZURE.NOTE] Informazioni archiviate in **archiviazione tabella** o **archiviazione blob** essere accessibili solo tramite un client di spazio di archiviazione o un'applicazione in grado di funzionare direttamente con questi sistemi di archiviazione. Ad esempio Visual Studio 2013 contiene una finestra di esplorazione di spazio di archiviazione che può essere utilizzato per esplorare lo spazio di archiviazione tabella o blob e HDInsight può accedere ai dati archiviati in archiviazione blob. È inoltre possibile scrivere un'applicazione che accede archiviazione Azure usando uno dei [Azure SDK](/downloads/#).

> [AZURE.NOTE] Diagnostica può anche essere attivata da Azure PowerShell utilizzando il cmdlet **Set-AzureWebsite** . Se non è stato installato PowerShell Azure o non configurata in modo da usare l'abbonamento di Azure, vedere [come usare PowerShell Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a>Procedura: scaricare dei log

Informazioni di diagnostica archiviate nel file System di web app è possibile accedervi direttamente tramite FTP. Può essere scaricato anche come archivio Zip tramite PowerShell Azure o la riga di comando di Azure.

La struttura della directory in cui vengono memorizzati i log è il seguente:

* **Registri delle applicazioni** : /LogFiles/applicazione /. Questa cartella contiene uno o più file di testo contenente informazioni sul prodotto per la registrazione delle applicazioni.

* **Non è riuscita tracce richiesta** - file di registro/W3SVC # # # /. Questa cartella contiene un file XSL e uno o più file XML. Assicurarsi di scaricare il file XSL nella stessa directory come XML file perché il file XSL fornisce funzionalità per la formattazione e filtrare il contenuto dei file XML quando vengono visualizzati in Internet Explorer.

* **I registri di errori dettagliate** : /LogFiles/DetailedErrors /. Questa cartella contiene uno o più file htm che forniscono informazioni dettagliate per eventuali errori HTTP che si sono verificati.

* **Log Server web** : /LogFiles/http/RawLogs. Questa cartella contiene uno o più file di testo formattati utilizzando il [formato di file di registro esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Registri di distribuzione** : file di registro/fra. Questa cartella contiene registri generati dai processi di distribuzione interna usati dalle App web Azure, nonché registri per le distribuzioni di operazioni.

### <a name="ftp"></a>FTP

Per accedere a informazioni di diagnostica tramite FTP, visitare il **Dashboard** dell'app web nel [portale classica](https://manage.windowsazure.com). Nella sezione **occhio** , utilizzare il collegamento **Log diagnostici FTP** per accedere ai file di log tramite FTP. La voce di **Distribuzione/FTP utente** sono elencati il nome utente che deve essere utilizzato per accedere al sito FTP.

> [AZURE.NOTE] Se non è impostata la voce **Utente distribuzione/FTP** o si è dimenticata la password per l'utente, è possibile creare un nuovo utente e password tramite il collegamento **reimpostare le credenziali di distribuzione** nella sezione **occhio** del **Dashboard**.

### <a name="download-with-azure-powershell"></a>Scaricare con PowerShell Azure

Per scaricare i file di log, avviare una nuova istanza di Azure PowerShell e usare il comando seguente:

    Save-AzureWebSiteLog -Name webappname

Verranno salvate i log per l'applicazione web specificato per il **-nome** parametro in un file denominato **logs.zip** nella directory corrente.

> [AZURE.NOTE] Se non è stato installato PowerShell Azure o non configurata in modo da usare l'abbonamento di Azure, vedere [come usare PowerShell Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="download-with-azure-command-line-interface"></a>Scaricare con Azure interfaccia della riga di comando

Per scaricare i file di log tramite l'interfaccia di riga di comando di Azure, aprire un prompt dei comandi di nuovo, PowerShell, Bash o sessione terminale e immettere il comando seguente:

    azure site log download webappname

Verranno salvate i log per l'applicazione web denominata 'webappname' in un file denominato **diagnostics.zip** nella directory corrente.

> [AZURE.NOTE] Se non è stato installato l'interfaccia della riga di comando di Azure Azure CLI () o non configurata in modo da usare l'abbonamento di Azure, vedere [come utilizzare CLI Azure](../xplat-cli-install.md).

## <a name="how-to-view-logs-in-application-insights"></a>Procedura: visualizza i registri nell'applicazione approfondimenti

Visual Studio applicazione approfondimenti sono disponibili gli strumenti per il filtraggio e la ricerca dei registri e per correlare i registri con le richieste e altri eventi.

1. Aggiungere un progetto in Visual Studio SDK approfondimenti di applicazione.
 * In Esplora soluzioni fare clic con il pulsante destro del progetto e scegliere Aggiungi approfondimenti applicazione. L'utente verrà guidato procedure dettagliate che includono la creazione di una risorsa approfondimenti applicazione. [Ulteriori informazioni](../application-insights/app-insights-asp-net.md)
2. Aggiungere il pacchetto di comunicare ascoltatore traccia al progetto.
 * Fare clic con il pulsante destro del progetto e scegliere Gestisci pacchetti NuGet. Selezionare `Microsoft.ApplicationInsights.TraceListener` [altre informazioni](../application-insights/app-insights-asp-net-trace-logs.md)
3. Caricare il progetto ed eseguirlo per generare i dati di log.
4. Nel [Portale di Azure](https://portal.azure.com/), individuare la nuova risorsa applicazione approfondimenti e aprire **ricerca**. Si noterà ai dati di log, oltre a richiesta, l'uso e altri telemetria. Alcuni telemetria può richiedere alcuni minuti per arrivare: fare clic su Aggiorna. [Ulteriori informazioni](../application-insights/app-insights-diagnostic-search.md)

[Ulteriori informazioni sulle prestazioni di verifica con informazioni dettagliate sui applicazione](../application-insights/app-insights-azure-web-apps.md)

##<a name="streamlogs"></a>Procedura: flusso dei log

Quando si sviluppa un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. Può essere eseguita dal flusso delle informazioni di registrazione all'ambiente di sviluppo tramite PowerShell Azure o la riga di comando di Azure.

> [AZURE.NOTE] Alcuni tipi di buffer di registrazione di scrittura per il file di log, causando eventi nell'ordine corretto all'interno del flusso. Ad esempio una voce del log di applicazione che si verifica quando un utente accede a una pagina visualizzata all'interno del flusso prima della voce di log HTTP corrispondente per la richiesta della pagina.

> [AZURE.NOTE] Registro streaming flusso verrà eseguito anche informazioni scritte in qualsiasi file di testo archiviati nel **D:\\home\\file di registro\\ ** cartella.

### <a name="streaming-with-azure-powershell"></a>Streaming con PowerShell Azure

Per informazioni di registrazione del flusso, iniziare una nuova istanza di Azure PowerShell e usare il comando seguente:

    Get-AzureWebSiteLog -Name webappname -Tail

Verrà la connessione all'app web specificato per il **-nome** parametro e avviare il flusso di informazioni nella finestra di PowerShell log verificarsi degli eventi in web app. Eventuali informazioni scritte a file che terminano con estensione txt,. log o htm archiviati nella directory /LogFiles (d: / home/registri) verranno trasmesso alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, utilizzare la **-messaggio** parametro. Per esempio:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Per filtrare i tipi di log specifico, ad esempio HTTP, utilizzare la **-percorso** parametro. Per esempio:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Per visualizzare un elenco di percorsi disponibili, utilizzare il parametro - ListPath.

> [AZURE.NOTE] Se non è stato installato PowerShell Azure o non configurata in modo da usare l'abbonamento di Azure, vedere [come usare PowerShell Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="streaming-with-azure-command-line-interface"></a>Streaming con Azure interfaccia della riga di comando

Per informazioni di registrazione del flusso di, aprire un prompt dei comandi di nuovo, PowerShell, Bash o sessione terminale e immettere il comando seguente:

    azure site log tail webappname

Verrà connettersi all'app web denominato 'webappname' e avviare il flusso di informazioni nella finestra log verificarsi degli eventi in web app. Eventuali informazioni scritte a file che terminano con estensione txt,. log o htm archiviati nella directory /LogFiles (d: / home/registri) verranno trasmesso alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, utilizzare la **-filtro** parametro. Per esempio:

    azure site log tail webappname --filter Error

Per filtrare i tipi di log specifico, ad esempio HTTP, utilizzare la **-percorso** parametro. Per esempio:

    azure site log tail webappname --path http

> [AZURE.NOTE] Se non è stato installato l'interfaccia della riga di comando di Azure o non configurata in modo da usare l'abbonamento di Azure, vedere [come a utilizzare Azure interfaccia riga di comando](../xplat-cli-install.md).

##<a name="understandlogs"></a>Procedura: comprendere i registri di diagnostica

### <a name="application-diagnostics-logs"></a>Registri diagnostica delle applicazioni

Applicazione di diagnostica archivia le informazioni in un formato specifico per le applicazioni di .NET, a seconda che si memorizza i registri per il file system, archiviazione tabelle o archiviazione blob. La base dei dati archiviati è lo stesso tra i tre tipi di spazio di archiviazione, la data e ora che si è verificato l'evento, l'ID di processo che ha generato l'evento, il tipo di evento (informazioni, avviso, errore) e il messaggio dell'evento.

__File system__

Ogni riga ha eseguito l'accesso al file system o ricevuti tramite streaming sarà nel formato seguente:

    {Date}  PID[{process id}] {event type/level} {message}

Ad esempio, un evento di errore da visualizzare simile al seguente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Registrazione nel file System fornisce le informazioni di base dei tre metodi disponibili, fornire solo l'ora, id processo, livello di eventi e messaggio.

__Archiviazione tabella__

Durante l'accesso allo spazio di archiviazione tabella, proprietà aggiuntive vengono utilizzate per facilitare la ricerca di dati archiviati nella tabella, nonché informazioni più dettagliate sull'evento. Le proprietà seguenti (colonne) vengono utilizzate per ogni entità (riga) archiviato nella tabella.

Nome della proprietà|Valore/formato
---|---
PartitionKey|Data/ora dell'evento nel formato yyyyMMddHH
RowKey|Un valore GUID che identifica l'entità
Timestamp|La data e l'ora in cui si è verificato l'evento
EventTickCount|La data e l'ora in cui si è verificato l'evento, in formato di graduazione (maggiore precisione)
NomeApplicazione|Il nome dell'app web
Livello|Livello di evento (ad esempio errori, avvisi, informazioni)
EventId|L'ID di evento dell'evento<p><p>Valore predefinito è 0 se non specificato
ID istanza|Istanza dell'applicazione web che si è verificato il pari
PID|ID di processo
TID|L'ID del thread che ha generato l'evento
Messaggio|Messaggio di dettagli evento

__Archiviazione BLOB__

Durante la registrazione per lo spazio di archiviazione blob, i dati vengono archiviati in formato con valori delimitati da virgole (CSV). Simile all'archivio tabelle, campi aggiuntivi connessi per fornire informazioni più dettagliate sull'evento. Le proprietà seguenti vengono usate per ogni riga del file CSV:

Nome della proprietà|Valore/formato
---|---
Data|La data e l'ora in cui si è verificato l'evento
Livello|Livello di evento (ad esempio errori, avvisi, informazioni)
NomeApplicazione|Il nome dell'app web
ID istanza|Istanza dell'applicazione web che si è verificato l'evento
EventTickCount|La data e l'ora in cui si è verificato l'evento, in formato di graduazione (maggiore precisione)
EventId|L'ID di evento dell'evento<p><p>Valore predefinito è 0 se non specificato
PID|ID di processo
TID|L'ID del thread che ha generato l'evento
Messaggio|Messaggio di dettagli evento

I dati archiviati in un blob è simile al seguente:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] Come rappresentato in questo esempio, la prima riga del log conterrà le intestazioni di colonna.

### <a name="failed-request-traces"></a>Non è riuscita tracce richiesta

Non è riuscita richiesta tracce vengono archiviate in file XML denominati __fr # # # XML__. Per rendere più semplice visualizzare le informazioni registrate, viene fornito un foglio di stile XSL denominato __freb.xsl__ nella stessa directory come file XML. Apertura di un file XML in Internet Explorer utilizzerà il foglio di stile XSL per fornire una visualizzazione delle informazioni sulla traccia formattata. Verranno visualizzate simile al seguente:

![richiesta non riuscita visualizzata nel browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Registri di errori dettagliate

I registri di errori dettagliate sono documenti HTML che forniscono informazioni più dettagliate sugli errori HTTP che si sono verificati. Dal momento che sono semplicemente documenti HTML, possono essere visualizzati utilizzando un web browser.

### <a name="web-server-logs"></a>Log server Web

I log formattati utilizzando il [formato di file di registro esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Queste informazioni possono essere letti utilizzando un editor di testo o analizzato utilizzando le utilità, ad esempio [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] I registri prodotti da Azure web App non supporta i campi __nomecomputer s__, __s-ip__o __cs versione__ .

##<a name="nextsteps"></a>Passaggi successivi

- [Come eseguire il monitoraggio delle applicazioni Web](/manage/services/web-sites/how-to-monitor-websites/)
- [Risoluzione dei problemi di Azure web App in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Analizzare i registri web app in HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale di vecchio al nuovo portale vedere: [Guida di riferimento per lo spostamento tra il portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 
