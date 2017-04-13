<properties
    pageTitle="Panoramica di Microsoft Azure IoT Suite | Microsoft Azure"
    description="Panoramica su come Azure IoT Suite offre internet delle soluzioni preconfigurate operazioni per raccogliere, analizzare e archiviare i dati, offrire visualizzazioni e integrare con altri sistemi."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>Che cos'è Azure IoT famiglia?

Azure internet dei servizi di aspetti (IoT) offrono una vasta gamma di funzionalità. Questi servizi di voto enterprise consentono di:

- Raccolta dati da dispositivi
- Analizzare flussi dati di animazione
- Archiviare e grandi quantità di dati della query
- Visualizzare i dati in tempo reale e nella cronologia
- Integrazione con i sistemi di back office

Per offrire queste funzionalità, pacchetti di Azure IoT Suite insieme più servizi Azure con le estensioni personalizzate come *soluzioni preconfigurate*. Queste soluzioni preconfigurate sono implementazione base serie comuni IoT soluzione che consente di ridurre il tempo che necessario per fornire le soluzioni IoT. Utilizzo [IoT software development kit][lnk-sdks], è possibile personalizzare ed estendere queste soluzioni in base alle proprie esigenze. È anche possibile utilizzare queste soluzioni come esempi o modelli quando si sviluppano soluzioni IoT nuove.

Il video seguente offre un'introduzione alla famiglia di prodotti IoT Azure:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure servizi IoT nella famiglia di prodotti IoT Azure

Le soluzioni preconfigurate usano in genere i seguenti servizi:

- Core alla famiglia di prodotti IoT Azure è [Azure IoT Hub] [ lnk-iot-hub] servizio. Questo servizio fornisce le funzionalità di messaggistica dispositivo nel cloud e cloud al dispositivo e funge da gateway nel cloud e altri servizi IoT Suite chiave. Il servizio consente di ricevere messaggi dai dispositivi in scala e inviare i comandi per i dispositivi.

- [Azure Analitica flusso] [ lnk-asa] fornisce analisi dei dati in animazione. Famiglia di prodotti IoT utilizza il servizio per l'elaborazione di telemetria in arrivo, eseguire l'aggregazione e rilevare eventi. Le soluzioni preconfigurate anche usare analitica flusso per elaborare i messaggi informativi che contengono dati, ad esempio le risposte di metadati o comando da dispositivi. Le soluzioni utilizzano flusso Analitica per elaborare i messaggi provenienti da dispositivi e inviare i messaggi ad altri servizi.

- [Spazio di archiviazione Azure] [ lnk-azure-storage] e [Azure DocumentDB] [ lnk-document-db] le funzionalità di archiviazione dati. Le soluzioni preconfigurate utilizzano archiviazione blob per archiviare telemetria e renderlo disponibile per l'analisi. Le soluzioni utilizzano DocumentDB per archiviare i metadati e attivare le funzionalità di gestione di dispositivi delle soluzioni.

- [Azure Web Apps] [ lnk-web-apps] e [Microsoft Power BI] [ lnk-power-bi] le funzionalità di visualizzazione dei dati. Flessibilità di Power BI consente di creare rapidamente il dashboard interattivi che utilizzano dati IoT famiglia di prodotti.

Per una panoramica dell'architettura di una soluzione IoT tipica, vedere [Microsoft Azure e Internet di elementi (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Soluzioni preconfigurate

Famiglia di prodotti IoT include preconfigurate soluzioni che consentono di rapidamente le informazioni e per esplorare scenari IoT comuni, ad esempio *il monitoraggio remoto* e la *manutenzione previsione*, che consente di Azure IoT Suite. È possibile distribuire queste soluzioni all'abbonamento Azure e quindi eseguire uno scenario di IoT completamento, to-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una panoramica delle operazioni consentite in IoT famiglia di prodotti e sui relativi componenti principale, è possibile acquisire familiarità con le soluzioni preconfigurate nella famiglia di prodotti IoT, vedere [quali sono IoT Azure preconfigurato soluzioni?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
