<properties
 pageTitle="Procedura dettagliata di manutenzione stima | Microsoft Azure"
 description="Una procedura dettagliata della soluzione di manutenzione stima preconfigurata IoT Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
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

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procedura dettagliata soluzione manutenzione stima preconfigurata

## <a name="introduction"></a>Introduzione

La soluzione di manutenzione stima preconfigurata IoT Suite è una soluzione-to-end per uno scenario di business che prevede il punto di una volta che si verifichino errori. È possibile utilizzare questa soluzione preconfigurata tempestiva per le attività, ad esempio ottimizzazione manutenzione. La soluzione combina chiavi servizi Azure IoT Suite, incluso un [Apprendimento Azure] [ lnk_machine_learning] area di lavoro. Un'area di lavoro contiene esperimenti, in base a un set di dati di esempio pubblico, per stimare la rimanente utile vita_utile (RUL) di un motore aerei. La soluzione implementata completamente lo scenario business IoT come punto di partenza per è possibile pianificare e implementare una soluzione che soddisfa esigenze specifiche.

## <a name="logical-architecture"></a>Architettura logica

Nel diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![][img-architecture]

Gli elementi blu sono servizi Azure vengono effettuato il provisioning in posizione selezionata durante il provisioning della soluzione preconfigurata. È possibile effettuare il provisioning della soluzione preconfigurata nell'area degli Stati Uniti orientali, Nord Europa o Asia orientale.

Alcune risorse non sono disponibili in aree geografiche in cui viene effettuato il provisioning della soluzione preconfigurata. Gli elementi arancioni nel diagramma rappresentano i servizi Azure effettuato il provisioning in più vicina disponibile area (sud Central US, Europa occidentale o Asia sudorientale) concesso l'area selezionata.

L'elemento verde è un dispositivo simulato che rappresenta un motore aerei. È possibile ottenere ulteriori informazioni questi dispositivi simulati nella sezione seguente.

Gli elementi grigi rappresentano componenti che implementano funzionalità di *gestione di dispositivi* . La versione corrente della soluzione di manutenzione stima preconfigurata non fornisce queste risorse. Per ulteriori informazioni sulla gestione di dispositivi, fare riferimento a [remote preconfigurato soluzione di monitoraggio][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivi simulati

Soluzione preconfigurato, un dispositivo simulato rappresenta un motore aerei. La soluzione viene eseguito il provisioning con due motori di cui eseguire il mapping a un singolo aerei. Ogni motore genera quattro tipi di telemetria: sensore 9, 11 sensore, sensore 14 e 15 sensore fornire i dati necessari per il modello di apprendimento calcolare il rimanente utile vita_utile (RUL) per il motore. Ogni dispositivo simulato invia messaggi di telemetria seguenti a IoT Hub:

*Ciclo di conteggio*. Un ciclo rappresenta un volo completato di lunghezza variabile tra 2 a 10 ore in cui i dati di telemetria sono acquisiti ogni mezz'ora durante il volo.

*Telemetria*. Sono disponibili quattro sensori che rappresentano gli attributi di modulo. I sensori sono contrassegnati generico sensore 9, 11 sensore, sensore 14 e 15 sensore. Questi 4 sensori rappresentano telemetria sufficiente per ottenere risultati utili dal modello di apprendimento per RUL. Questo modello viene creato da un set di dati pubblico che include i dati sensore motore reali. Per ulteriori informazioni su come la creazione del modello di set di dati originale, vedere il [Modello manutenzione previsione di Cortana Intelligence raccolta][lnk-cortana-analytics].

I dispositivi simulati possono gestire i comandi seguenti inviati da un hub IoT:

| Comando | Descrizione |
|---------|-------------|
| StartTelemetry | Controlla lo stato della simulazione.<br/>Avvia il dispositivo l'invio di telemetria     |
| StopTelemetry  | Controlla lo stato della simulazione.<br/>Interrompere il dispositivo l'invio di telemetria |

IoT Hub fornisce riconoscimento comando dispositivo.

## <a name="azure-stream-analytics-job"></a>Processo di flusso Analitica Azure

**Processo: telemetria** interviene il flusso di telemetria di dispositivo in ingresso utilizzando due istruzioni. Il primo seleziona telemetria tutti i dispositivi e invia questi dati al blob lo spazio di archiviazione nel punto in cui è visualizzato in web app. La seconda istruzione calcola i valori Media sensore in una finestra scorrevole di due minuti e invia i dati tramite l'hub di eventi a un **processore eventi**.

## <a name="event-processor"></a>Processore eventi

**Processore eventi** vengono accettati i valori Media sensore per un ciclo completato. I passaggi di tali valori a un'API esposti di apprendimento qualificato modello per calcolare RUL per un modulo.

## <a name="azure-machine-learning"></a>Apprendimento Azure

Per ulteriori informazioni su come la creazione del modello di set di dati originale, vedere il [Modello manutenzione previsione di Cortana Intelligence raccolta][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Di seguito è riportata alcuni

In questa sezione illustra le componenti della soluzione, viene descritto il caso pensato e vengono forniti esempi.

### <a name="predictive-maintenance-dashboard"></a>Dashboard di manutenzione previsione

Questa pagina nell'applicazione web utilizza controlli PowerBI JavaScript (vedere [ottenere informazioni e gli elementi visivi repository][lnk-powerbi]) per visualizzare:

- Dati di output di processi flusso Analitica nell'archiviazione blob.
- Il numero RUL e circolare al motore aerei.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Osservare il comportamento della soluzione cloud

Nel portale di Azure, passare al gruppo di risorse con il nome della soluzione che si è scelto di visualizzare le risorse di provisioning.

![][img-resource-group]

Quando viene effettuato il provisioning della soluzione preconfigurata, si riceve un messaggio di posta elettronica con un collegamento all'area di lavoro di apprendimento. È anche possibile spostarsi nell'area di lavoro di apprendimento da [azureiotsuite.com] [ lnk-azureiotsuite] pagina per la soluzione provisioning quando è nello stato **Pronto** .

![][img-machine-learning]

Nel portale soluzione è possibile vedere che l'esempio viene eseguito il provisioning con quattro dispositivi simulati per rappresentare due aereo con due motori per aereo, ognuna con quattro sensori. Quando prima passare al portale di soluzione, la simulazione viene interrotto.

![][img-simulation-stopped]

Fare clic su **Start simulazione** per iniziare la simulazione in cui è possibile vedere la cronologia di sensore RUL, cicli, e cronologia RUL popolare il dashboard.

![][img-simulation-running]

Quando RUL è inferiore a 160 (un limite non autorizzato scelto dimostrativo), il portale di soluzione viene visualizzato un simbolo di avviso accanto a Visualizza RUL ed evidenzia il motore di aereo in giallo. Si noti come valori RUL dispone di una tendenza verso il basso generale generale, ma sono in genere in rimbalzo alto e verso il basso. Questo comportamento risulta dalla lunghezza del ciclo di variabile e la precisione del modello.

![][img-simulation-warning]

La simulazione completa richiede circa 35 minuti per completare 148 cicli. La soglia RUL 160 verrà soddisfatti per la prima volta circa 5 minuti e motori di raggiunto il limite circa 8 minuti.

La simulazione scorre il set di dati completo per 148 cicli e liquida ai valori del ciclo e RUL finali.

È possibile interrompere la simulazione in qualsiasi momento, ma facendo clic su **Avvia simulazione** riproduce la simulazione dall'inizio del set di dati.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere eseguito la soluzione di manutenzione stima preconfigurata può essere necessario modificarlo, vedere [indicazioni su come personalizzare soluzioni preconfigurate][lnk-customize].

Post di blog TechNet [Manutenzione stima IoT Suite - roba da smanettoni -](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) include ulteriori dettagli sulla soluzione manutenzione stima preconfigurata.

È anche possibile esplorare alcune delle altre caratteristiche e le soluzioni di famiglia IoT preconfigurato:

- [Domande frequenti su IoT famiglia di prodotti][lnk-faq]
- [Protezione IoT sin][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
