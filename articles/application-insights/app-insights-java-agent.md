<properties 
    pageTitle="Eseguire il monitoraggio delle dipendenze, le eccezioni e tempi di esecuzione delle applicazioni web Java" 
    description="Esteso il monitoraggio del sito Web con informazioni dettagliate sui applicazione Java" 
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
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Eseguire il monitoraggio delle dipendenze, le eccezioni e tempi di esecuzione delle applicazioni web Java

*Informazioni dettagliate sui applicazione è in anteprima.*

Se si dispone di [Strumentazione il linguaggio web app con informazioni dettagliate sui applicazione][java], è possibile utilizzare l'agente di linguaggio per ottenere informazioni approfondite più approfondite, senza alcuna modifica codice:


* **Dipendenze:** Dati sulle chiamate effettuate dall'applicazione ad altri componenti, tra cui:
 * **Resto chiamate** effettuate tramite HttpClient, OkHttp e RestTemplate (primavera).
 * **Redis** chiamate tramite il client Jedis. Se la chiamata richiede più tempo 10s, l'agente recupera anche gli argomenti di chiamata.
 * **[Chiamate JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, DB Oracle o Apache Derby DB. sono supportate le chiamate "executeBatch". Per MySQL e PostgreSQL, se la chiamata richiede più tempo 10s, l'agente riporta il piano di query. 
* **Catturati eccezioni:** Dati sulle eccezioni che vengono gestiti dal codice.
* **Tempo di esecuzione metodo:** Dati relativi il tempo che necessario per eseguire i metodi specifici.

Per usare l'agente di linguaggio, è installarlo nel server. App web di deve essere dotata di [Applicazione approfondimenti linguaggio SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Installare l'agente di applicazione approfondimenti per Java

1. Nel computer che esegue il server di linguaggio, [scaricare l'agente](https://aka.ms/aijavasdk).
2. Modificare lo script di avvio dell'applicazione server e aggiungere JVM seguenti:

    `javaagent:`*percorso completo del file di VASO agente*

    Ad esempio, in Tomcat su un computer Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Riavviare il server applicazioni.

## <a name="configure-the-agent"></a>Configurare l'agente

Creare un file denominato `AI-Agent.xml` e posizionarla nella stessa cartella del file di VASO agente.

Impostare il contenuto del file xml. Modificare l'esempio seguente per includere o omettere le caratteristiche desiderate. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

È necessario attivare eccezione di report e l'intervallo di metodo per i singoli metodi.

Per impostazione predefinita, `reportExecutionTime` è vera e `reportCaughtExceptions` è false.

## <a name="view-the-data"></a>Visualizzare i dati

In risorse applicazione approfondimenti aggregato remoto dipendenza e metodo tempi di esecuzione viene visualizzata [sotto il riquadro delle prestazioni][metrics]. 

Per cercare singole istanze della dipendenza, eccezione e metodo nei report, aprire [ricerca][diagnostic]. 

[Diagnosi dei problemi relativi alle dipendenze - altre informazioni](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Domande? Problemi?

* Nessun dato? [Eccezioni firewall set](app-insights-ip-addresses.md)
* [Risoluzione dei problemi di linguaggio](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
