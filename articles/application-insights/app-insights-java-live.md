<properties 
    pageTitle="Informazioni dettagliate sui applicazione per Java web apps già presenti live" 
    description="Avviare un'applicazione web che è già in esecuzione nel server di monitoraggio" 
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
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Informazioni dettagliate sui applicazione per Java web apps già presenti live

*Informazioni dettagliate sui applicazione è in anteprima.*

Se si dispone di un'applicazione web che è già in esecuzione nel server J2EE, è possibile avviare Monitoraggio con [Informazioni dettagliate sui applicazione](app-insights-overview.md) senza la necessità di apportare modifiche al codice o ricompilare il progetto. Con questa opzione per informazioni su richieste HTTP inviate a server, le eccezioni non gestite e contatori.

È necessario un abbonamento a [Microsoft Azure](https://azure.com).

> [AZURE.NOTE] La procedura descritta in questa pagina aggiunge SDK all'app web in fase di esecuzione. Questa strumentazione runtime è utile se non si desidera aggiornare o ricompilare il codice sorgente. Ma, se possibile, invece è consigliabile è [aggiungere SDK al codice sorgente](app-insights-java-get-started.md) . Che offre altre opzioni, ad esempio la scrittura di codice per tenere traccia delle attività dell'utente.

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. ottenere una chiave di strumentazione approfondimenti applicazione

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com)
2. Creare una nuova risorsa applicazione approfondimenti

    ![Fare clic su + e scegliere applicazione approfondimenti](./media/app-insights-java-live/01-create.png)
3. Impostare il tipo di applicazione all'applicazione web Java.

    ![Inserire un nome, scegliere Java web app e fare clic su Crea](./media/app-insights-java-live/02-create.png)
4. Trovare la chiave strumentazione della nuova risorsa. È necessario incollare la chiave del progetto di codice subito.

    ![Nella nuova visualizzazione di risorse, fare clic su proprietà e copiare la chiave Strumentazione](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. scaricare SDK

1. Scaricare l' [applicazione approfondimenti SDK per Java](https://aka.ms/aijavasdk). 
2. Nel server, estrarre il contenuto SDK alla directory da cui vengono caricati i file binari di progetto. Se si usa Tomcat, questa directory è in genere in`webapps\<your_app_name>\WEB-INF\lib`


## <a name="3-add-an-application-insights-xml-file"></a>3. aggiungere un file xml approfondimenti applicazione

Creare ApplicationInsights.xml nella cartella in cui è stato aggiunto il SDK. Inserirvi XML riportato di seguito.

Sostituisce il tasto strumentazione ottenuto dal portale di Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* Il tasto strumentazione inviato con tutti gli elementi di telemetria e indica approfondimenti applicazione per visualizzare la risorsa.
* Il componente richiesta HTTP è facoltativo. Invia automaticamente telemetria su richieste e i tempi di risposta al portale.
* Correlazione eventi è un componente aggiuntivo per il componente di richiesta HTTP. Assegna un identificatore a ogni richiesta ricevuta dal server e aggiunge l'identificatore come proprietà su tutti gli elementi di telemetria come la proprietà 'Operation.Id'. Consente di correlare telemetria associato a ogni richiesta mediante l'impostazione di un filtro di [ricerca diagnostica](app-insights-diagnostic-search.md).


## <a name="4-add-an-http-filter"></a>4. aggiungere un filtro HTTP

Individuare e aprire il file Web di un progetto e unire seguente frammento di codice sotto il nodo web app in cui sono configurati i filtri di applicazione.

Per ottenere risultati migliori, il filtro deve essere mappato prima di tutti gli altri filtri.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## <a name="5-check-firewall-exceptions"></a>5. eccezioni firewall controllo

Potrebbe essere necessario [impostare le eccezioni per inviare i dati in uscita](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. riavviare l'app web

## <a name="7-view-your-telemetry-in-application-insights"></a>7. visualizzare il telemetria in applicazione approfondimenti

Tornare alla risorsa di applicazione approfondimenti nel [portale di Microsoft Azure](https://portal.azure.com).

Viene visualizzato e il Panoramica telemetria sulle richieste HTTP. (Se non è presente, attendere alcuni secondi e quindi fare clic su Aggiorna.)

![dati di esempio](./media/app-insights-java-live/5-results.png)
 

Fare clic sui grafici per visualizzare informazioni più dettagliate la metrica. 

![](./media/app-insights-java-live/6-barchart.png)

 

E quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.
 
![](./media/app-insights-java-live/7-instance.png)


[Ulteriori informazioni sulle metriche.](app-insights-metrics-explorer.md)



## <a name="next-steps"></a>Passaggi successivi

* [Aggiungi telemetria alle pagine web](app-insights-web-track-usage.md) monitor pagina visualizzazioni e metriche utente.
* [Impostare i test web](app-insights-monitor-web-app-availability.md) per assicurarsi che l'applicazione rimane attivo e rispondere.
* [Acquisire le tracce di log](app-insights-java-trace-logs.md)
* [Log e ricerca eventi](app-insights-diagnostic-search.md) di diagnosticare i problemi.


 
