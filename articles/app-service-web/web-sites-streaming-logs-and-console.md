<properties 
    pageTitle="Console e trasmissione dei log" 
    description="Panoramica della console e registri di flussi" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Log e Console di flusso

## <a name="streaming-logs"></a>Trasmissione dei log

Il **portale di Azure** fornisce un visualizzatore di log flusso integrata che consente di visualizzare gli eventi di traccia dalle applicazioni **Servizio App** in tempo reale.  

Impostazione di questa caratteristica richiede pochi semplici passaggi:

- Scrivere le tracce nel codice
- Abilitazione delle applicazioni **registri diagnostici** per l'app
- Visualizzare il flusso dall'interfaccia utente incorporata **Streaming registri** nel **portale di Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Come scrivere tracce nel codice ###

Scrivere le tracce nel codice è semplice.  In c# è semplice come scrivere il codice seguente:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace risiede nello spazio dei nomi Diagnostics.

In un'app Node è possibile scrivere il codice per ottenere lo stesso risultato:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Come attivare e visualizzare la trasmissione log
![][BrowseSitesScreenshot]Diagnostica abilitata alla scala cronologica per app. Prima di tutto esplorazione al sito per attivare questa caratteristica.  
  
![][DiagnosticsLogs]Dal menu impostazioni, scorrere fino alla sezione **monitoraggio** e fare clic su **Registri diagnostici (1)**. Quindi **Attiva (2)** **Applicazione registrazione (file System)** o **Applicazione di registrazione (blob)** l'opzione **livello** consente di modificare il livello di gravità di tracce per acquisire. Se si sta tentando solo acquisire familiarità con la caratteristica, impostare il livello **dettagliato** per assicurarsi che tutte le istruzioni di analisi sono stati raccolti.

Fare clic su **Salva** nella parte superiore e il e si è pronti per la visualizzazione dei registri.

>[AZURE.NOTE] Maggiore il **livello di gravità** altre risorse vengono utilizzate per log e le altre tracce vengono prodotti. Verificare che **il livello di gravità** è configurato per il livello di dettaglio corretto per un sito di traffico elevato o di produzione. 

![][StreamingLogsScreenshot]Per visualizzare il **log di flusso** da all'interno del portale Azure, fare clic sul **flusso di registro (1)** anche nella sezione **monitoraggio** del menu impostazioni. Se l'app è attiva la scrittura di istruzioni di analisi, sarà necessario visualizzarle nella **trasmissione (2) log dell'interfaccia utente** nella quasi in tempo reale.

## <a name="console"></a>Console
Il **portale di Azure** console accedere all'app. È possibile esplorare i file system dell'applicazione ed eseguire gli script di powershell/cmd. Sono associate mediante le stesse autorizzazioni impostate come codice app in esecuzione durante l'esecuzione di comandi della console. Accesso a protetto directory o esecuzione di script che richiedono autorizzazioni elevate è bloccato.  

![][ConsoleScreenshot]Dal menu impostazioni, scorrere fino alla sezione **Strumenti di sviluppo** e fare clic su **Console (1)** e dell'interfaccia utente **(2) console** verrà visualizzata a destra.

Per acquisire familiarità con la **console**, provare a eseguire comandi di base come:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
