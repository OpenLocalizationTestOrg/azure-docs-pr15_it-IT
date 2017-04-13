<properties 
    pageTitle="Risolvere i problemi di applicazione approfondimenti in un progetto web Java" 
    description="Guida alla risoluzione dei: monitoraggio App linguaggio live con informazioni dettagliate sui applicazione." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Risoluzione dei problemi e domande e risposte per informazioni dettagliate sui applicazione per Java

Domande o problemi con [Visual Studio applicazione approfondimenti in linguaggio][java]? Ecco alcuni suggerimenti.


## <a name="build-errors"></a>Errori di generazione

*In Eclisse, quando si aggiungono SDK approfondimenti applicazione tramite Maven o Gradle, viene visualizzato genera o checksum errori di convalida.*

* Se la dipendenza <version> elemento Usa un motivo con caratteri jolly (ad esempio (Maven) `<version>[1.0,)</version>` (Gradle) o `version:'1.0.+'`), provare a specificare una versione specifica invece come `1.0.2`. Vedere le [note sulla versione](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) per la versione più recente.

## <a name="no-data"></a>Senza dati 

*Aggiunto approfondimenti applicazione e si è verificato nell'app, ma mai conosco dati nel portale.*

* Attendere qualche minuto e fare clic su Aggiorna. I tipi di grafici aggiornare periodicamente se stessi, ma è anche possibile aggiornare manualmente. L'intervallo di aggiornamento varia a seconda dell'intervallo di tempo del grafico.
* Verificare di disporre di una chiave strumentazione definita nel file ApplicationInsights.xml (nella cartella di risorse del progetto)
* Verificare che sia presente alcun `<DisableTelemetry>true</DisableTelemetry>` nodo nel file xml.
* Nel firewall, potrebbe essere necessario aprire porte TCP 80 e 443 per il traffico in uscita a dc.services.visualstudio.com. Vedere l' [elenco completo delle eccezioni firewall](app-insights-ip-addresses.md)
* In Microsoft Azure avviare discussioni, esaminare il mapping di stato del servizio. Se esistono alcune indicazioni degli avvisi, attendere finché non vengono restituiti su OK e quindi chiudere e riaprire il blade applicazione approfondimenti applicazione.
* Attivare la registrazione nella finestra della console IDE mediante l'aggiunta di un `<SDKLogger />` elemento sotto il nodo principale nel file ApplicationInsights.xml (nella cartella di risorse del progetto) e controllo per le voci preceduto [errore].
* Assicurarsi che il file ApplicationInsights.xml corretto correttamente caricato da SDK linguaggio esaminando messaggi di output della console di un'istruzione "file di configurazione è stato trovato".
* Se non viene trovato il file di configurazione, controllare i messaggi di output per visualizzare in cui si sta cercando il file di configurazione e assicurarsi che la ApplicationInsights.xml si trova in uno di questi percorsi di ricerca. In regola, è possibile posizionare il file di configurazione vicino SDK di approfondimenti applicazione JARs. Ad esempio: in Tomcat, implicazioni cartella WEB-INF/raccolta.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Utilizzato per visualizzare dati, ma è stata interrotta

* Controllare il [blog di stato](http://blogs.msdn.com/b/applicationinsights-status/).
* È stato raggiunto il quota mensile di punti dati? Aprire Impostazioni/Quota e prezzi per scoprire. In caso affermativo, è possibile aggiornare il piano o pagare capacità aggiuntiva. Vedere i [prezzi combinazione](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Non è visualizzato tutti i dati che sta previsto

* Aprire le quote e prezzi blade e controllare se [campioni](app-insights-sampling.md) è in esecuzione. (100% di trasmissione significa che non è stato in operazione campioni) Per accettare solo una parte di telemetria che arrivano a partire da un'applicazione, è possibile impostare il servizio di informazioni approfondite dell'applicazione. Consente di mantenere la quota di telemetria mensile. 

## <a name="no-usage-data"></a>Nessun dato di utilizzo

*Vengono visualizzati dati relativi a convocazioni e tempi di risposta, ma non visualizzazione pagina, browser o dati utente.*

L'app è stato configurato per l'invio di telemetria dal server. Dopo il passaggio successivo consiste nel [configurare le pagine web per l'invio di telemetria dal web browser][usage].

In alternativa, se il client è un'app in un [telefono o un altro dispositivo][platforms], è possibile inviare telemetria da tale posizione. 

Utilizzare la stessa chiave strumentazione per configurare il client e server telemetria. I dati verranno visualizzati nella stessa risorsa applicazione approfondimenti e sarà possibile correlare eventi di client e server.



## <a name="disabling-telemetry"></a>La disattivazione di telemetria

*Come è possibile disattivare insieme di telemetria?*

Nel codice:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**O** 

Aggiornare ApplicationInsights.xml (nella cartella di risorse del progetto). Aggiungere quanto segue sotto il nodo principale:

    <DisableTelemetry>true</DisableTelemetry>

Utilizzando il metodo XML, è necessario riavviare l'applicazione quando si modifica il valore.

## <a name="changing-the-target"></a>Modifica della destinazione

*Come è possibile modificare la risorsa Azure progetto invia dati a?*

* [È possibile ottenere il tasto strumentazione della nuova risorsa.][java]
* Se si aggiunti approfondimenti applicazione al progetto utilizzando il Toolkit di Azure per Eclisse, a destra fare clic su progetto web, selezionare **Azure**, **Configurare approfondimenti applicazione**e modificare la chiave.
* In caso contrario, aggiornare la chiave in ApplicationInsights.xml nella cartella risorse del progetto.


## <a name="the-azure-start-screen"></a>Schermata start Azure

*Ricerca di [Azure portal](https://portal.azure.com). Mappa Aiutami qualcosa mia applicazione?*

No, che mostra lo stato del server Azure tutto il mondo.

*Da inizio Azure bacheca (schermata iniziale), come si trovano dati sulle app?*

Presupponendo [configurare le app per informazioni dettagliate sui applicazione][java], fare clic su Sfoglia, selezionare applicazione approfondimenti e selezionare la risorsa app creata per l'app. Per ottenere sono più rapidamente in futuro, è possibile aggiungere l'app alla scheda start.

## <a name="intranet-servers"></a>Server di rete Intranet

*È possibile monitorare un server personale Intranet?*

Sì, posto che il server può inviare telemetria al portale di informazioni dettagliate sui applicazione tramite internet pubblica. 

Nel firewall, potrebbe essere necessario aprire porte TCP 80 e 443 per il traffico in uscita per dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Conservazione dei dati 

*Per quanto tempo viene mantenuti dati nel portale È protetto?*

Vedere [l'informativa sulla privacy e la conservazione dei dati][data].

## <a name="next-steps"></a>Passaggi successivi

*È possibile impostare applicazione approfondimenti per Java server app. Altre possono eseguire?*

* [Controllare la disponibilità delle pagine web][availability]
* [Monitorare l'utilizzo della pagina web][usage]
* [Tenere traccia dell'utilizzo e diagnosticare i problemi di App dispositivo][platforms]
* [Scrivere il codice per tenere traccia dell'utilizzo dell'app][track]
* [Raccogliere i registri diagnostici][javalogs]


## <a name="get-help"></a>È possibile ottenere assistenza

* [Overflow dello stack](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 