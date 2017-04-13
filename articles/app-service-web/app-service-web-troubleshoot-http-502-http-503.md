<properties
    pageTitle="Risolvere 502 gateway non valido, 503 Servizio non disponibili errori | Microsoft Azure"
    description="Risoluzione dei problemi 502 gateway non valido e 503 Servizio non disponibile gli errori di nelle applicazioni web ospitato in Azure App servizio."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 gateway non valido, 503 Servizio non è disponibile, errore 503, errore 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Risolvere gli errori HTTP di "502 gateway non valido" e "503 Servizio non disponibile" nelle applicazioni web Azure

"502 gateway non valido" e "503 Servizio non disponibile" sono gli errori comuni nelle applicazioni web ospitato in [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714). In questo articolo consente di risolvere i problemi di questi errori.

Se necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile contattare i Azure esperti di [Azure MSDN e nei forum di Overflow dello Stack](https://azure.microsoft.com/support/forums/). In alternativa, è possibile inoltre archiviare una richiesta di assistenza Azure. Fare clic sul **Supporto di accedere**al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintomo

Quando si visualizza il web app, viene restituito un HTTP "502 Gateway non valido" errore o un HTTP errore "503 Servizio non disponibile".

## <a name="cause"></a>Causa

Questo problema è spesso causato da problemi a livello dell'applicazione, ad esempio:

-   richieste di impiegando troppo tempo
-   applicazione che utilizza memoria/CPU elevata
-   applicazione di un arresto anomalo a causa di un'eccezione.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Procedure di risoluzione dei problemi per risolvere gli errori "503 Servizio non disponibile" e "502 gateway non valido"

Risoluzione dei problemi può essere suddiviso in tre attività distinte, in sequenza:

1.  [Osservare e controllare il comportamento dell'applicazione](#observe)
2.  [Raccolta dati](#collect)
3.  [Mitigare il problema](#mitigate)

[Applicazione del servizio Web Apps](/services/app-service/web/) offre diverse opzioni di ogni passaggio.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. verificare e monitorare il comportamento dell'applicazione

####    <a name="track-service-health"></a>Verificare l'integrità dei servizi

Microsoft Azure publicizes ogni volta che è presente una riduzione delle prestazioni o interruzione del servizio. È possibile verificare lo stato del servizio nel [Portale di Azure](https://portal.azure.com/). Per ulteriori informazioni, vedere [l'integrità dei servizi di rilevamento](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Eseguire il monitoraggio delle app web

Questa opzione consente di scoprire se l'applicazione in caso di eventuali problemi. In blade dell'applicazione web, fare clic sul riquadro **richieste ed errori** . E **l'unità di misura metriche** verrà visualizzate tutte le metriche è possibile aggiungere.

Alcuni dei valori che è possibile monitorare per un'app web

-   Media della memoria insieme di lavoro
-   Tempo medio di risposta
-   Tempo CPU
-   Working set della memoria
-   Richieste di

![eseguire il monitoraggio delle app web verso la risoluzione di errori HTTP 502 gateway non valido e 503 Servizio non disponibile](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Per ulteriori informazioni, vedere:

-   [Monitoraggio App Web nel servizio App Azure](web-sites-monitor.md)
-   [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. raccolta dati

####    <a name="use-the-azure-app-service-support-portal"></a>Usare il portale di supporto servizio App Azure

Web App consente di risolvere i problemi relativi all'app web esaminando HTTP registri, i registri eventi, processo immagini e altro. È possibile accedere a tutte le informazioni tramite il portale di supporto in **http://&lt;il nome dell'app >.scm.azurewebsites.net/Support**

Portale di Azure App servizio supporto offre tre schede separate per supportare i tre passaggi di uno scenario di risoluzione dei problemi comune:

1.  Osservare comportamento corrente
2.  Analizzare in base alla raccolta delle informazioni di diagnostica e l'esecuzione di analisi incorporati
3.  Mitigare

Se il problema si verifica immediatamente, fare clic su **Analizza** > **diagnostica** > **Diagnosi ora** per creare una sessione di diagnostica per l'utente, che verranno raccolte HTTP registrati, Visualizzatore eventi, immagini, i registri di errori PHP e PHP elaborare report memoria.

Una volta raccolti i dati, verrà anche eseguire un'analisi sui dati e fornire un rapporto HTML.

Nel caso in cui si desidera scaricare i dati, per impostazione predefinita, verrebbero archiviata nella cartella D:\home\data\DaaS.

Per ulteriori informazioni nel portale di Azure App per assistenza, vedere [Nuovi aggiornamenti con estensione di sito di supporto per i siti Web di Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Utilizzare la Console di Debug Kudu

Web App viene fornito con una console di debug che è possibile utilizzare per il debug, esplorare e caricamento di file, come i punti finali JSON per ottenere informazioni sull'ambiente. Questo comando si chiama la _Console Kudu_ o _Servizi Dashboard_ per un'app web.

È possibile accedere questo dashboard facendo clic sul collegamento **https://&lt;il nome dell'app >.scm.azurewebsites.net/**.

Alcune delle operazioni che fornisce Kudu sono:

-   impostazioni dell'ambiente per l'applicazione
-   flusso di registro
-   immagine della diagnostica
-   eseguire il debug console in cui è possibile eseguire i cmdlet di Powershell e comandi DOS base.


Un'altra utile funzionalità di Kudu consiste nel caso in cui l'applicazione sta generando eccezioni prima possibilità, è possibile utilizzare Kudu e scarica lo strumento di SysInternals Procdump per creare la memoria. Le immagini della memoria istantanee del processo e spesso consentono di risolvere i problemi più complicate con l'app web.

Per ulteriori informazioni sulle funzionalità disponibili in Kudu, vedere [siti Web di Azure strumenti online che è necessario conoscere sulle](/blog/windows-azure-websites-online-tools-you-should-know-about/).

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

 ![Riavviare app per risolvere gli errori HTTP 502 gateway non valido e 503 Servizio non disponibile](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

È anche possibile gestire applicazioni web tramite Powershell Azure. Per ulteriori informazioni, vedere [Uso Azure PowerShell Gestione risorse Azure](../powershell-azure-resource-manager.md).
