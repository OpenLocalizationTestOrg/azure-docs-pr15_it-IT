<properties
 pageTitle="Stima manutenzione preconfigurato soluzione | Microsoft Azure"
 description="Descrizione della soluzione di manutenzione stima preconfigurata IoT Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Panoramica della soluzione di manutenzione stima preconfigurata

La soluzione di *manutenzione stima* preconfigurato è una delle [soluzioni preconfigurate] [ lnk_preconfigured_solutions] rilasciato come parte di [Microsoft Azure IoT Suite][lnk_iot_suite]. Questa soluzione si integra insieme di telemetria di dispositivo in tempo reale con un modello di stima creato con [Azure apprendimento][lnk_machine_learning].


Con famiglia di prodotti IoT Azure aziende possono in modo semplice e rapido connettersi ed eseguire il monitoraggio delle risorse e analizzare i dati in tempo reale. La soluzione di manutenzione stima preconfigurata richiede che i dati e utilizza RTF dashboard e visualizzazioni per fornire alle aziende con nuovo business intelligence che consentono di unità efficienza e migliorare i profitti.

## <a name="the-scenario"></a>Lo Scenario

Fabrikam è delle compagnie aeree internazionali incentrato sui grande esperienza prezzi competitiva. Una delle cause dei ritardi volo è problemi di manutenzione e la manutenzione motore aereo è particolarmente complessa. Errore del motore durante volo occorre evitare tutti i costi, in modo Fabrikam esamina periodicamente i motori ed è conforme a un programma di manutenzione pianificata. Tuttavia, motori non sempre, usare lo stesso. Alcuni manutenzione non necessarie viene eseguita nei motori. Più importante, si verificano problemi che possono terra un aereo fino a quando non viene eseguita la manutenzione. Causando ritardi, in particolare se un aereo si trova nella posizione in cui destro tecnici o parti riserva non sono disponibili.

I motori di aereo Fabrikam vengono forniti con sensori di cui eseguire il monitoraggio delle condizioni del motore durante volo. Fabrikam consente di raccogliere i dati sensore raccolti durante il volo Azure IoT famiglia di prodotti. Dopo anni accumulare del motore operativi e i dati di errore, scienziati dati Fabrikam hanno configurato un modo per stimare la rimanente utile vita_utile (RUL) di un motore aerei. Che cos'è identificato è una correlazione tra i dati provenienti da quattro sensori motore con un'usura motore che conduce ad eventuale errore. Mentre Fabrikam continua eseguire un controllo periodico per garantire la sicurezza, ora possibile utilizzare i modelli per calcolare RUL per ogni motore dopo ogni volo utilizzando il telemetria raccolti dai motori durante il volo. Fabrikam può ora prevedere futuri punti di errore e piano per la manutenzione e ripristinare in anticipo.

> [AZURE.NOTE] Il modello di soluzione utilizza dati usura motore effettivo.

Prevedendo il punto di quando è necessaria una manutenzione Fabrikam può ottimizzare le operazioni per ridurre i costi. I coordinatori manutenzione usare le utilità di pianificazione per pianificare la manutenzione coincidente con un aereo interruzione in corrispondenza di una specifica località e garantire tempo sufficiente per gli aerei da fuori servizio senza interruzioni di programmazione. Fabrikam possono pianificare tecnici Per garantire aereo vengono gestiti in modo efficiente senza tempo di attesa. Responsabili del controllo inventario riceveranno piani di manutenzione, in modo che possano ottimizzare il processo di ordinamento e all'inventario delle parti di riserva. Tutto ciò consente Fabrikam per ridurre i tempi di terra aerei e ridurre i costi operativi garantendo la sicurezza di persone, personale.

Per comprendere come [Azure IoT Suite] [ lnk_iot_suite] disponibili i clienti di funzionalità necessarie comprendere tutte le potenzialità di manutenzione previsione, approvare questo [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>La modalità di compilazione della soluzione di manutenzione previsione

La soluzione si basa su un modello di apprendimento Azure esistente come modello per visualizzare queste funzionalità partendo dal dispositivo telemetria raccolto mediante servizi IoT famiglia di prodotti. Microsoft ha creato un [modello di regressione] [ lnk_regression_model] del motore di aereo e pubblicare il modello completo dati<sup>\[1\]</sup>e istruzioni dettagliate su come utilizzare il modello.

La soluzione di manutenzione stima preconfigurata IoT Azure viene utilizzato il modello di regressione creato dal modello. è distribuito in abbonamento Azure ed è esposto tramite un'API generata automaticamente. La soluzione include un sottoinsieme dei dati di test che rappresenta 4 (pari a 100 totale) motori e 4 (di 21 totale) flussi di dati sensore che forniscono un risultato preciso dal modello di esperti.

*\[1\] Saxena risposte e Goebel k (2008). "Turbofan motore riduzione delle simulazione Set di dati", NASA Ames Prognostics dati archivio (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), Centro ricerche Ames, campo Moffett, autorità di certificazione*

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come IoT Azure consente scenari di manutenzione previsione, leggere [acquisire valore da Internet di elementi][lnk_capture_value].

Scrivere una [procedura dettagliata] [ lnk-predictive-walkthrough] della previsione manutenzione preconfigurato soluzione.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

È anche possibile esplorare alcune delle altre caratteristiche e le soluzioni di famiglia IoT preconfigurato:

- [Domande frequenti su IoT famiglia di prodotti][lnk-faq]
- [Protezione IoT sin][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
