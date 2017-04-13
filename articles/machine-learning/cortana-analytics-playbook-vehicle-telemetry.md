<properties 
    pageTitle="Playbook soluzione di veicolo telemetria analitica | Microsoft Azure" 
    description="Utilizzare le funzionalità di Business Intelligence Cortana per ottenere in tempo reale e stima approfondimenti sulla veicolo salute e favorire abitudini." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Playbook soluzione di veicolo telemetria analitica

Questo **menu** fornisce collegamenti ai capitoli di questo playbook. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Panoramica
Computer superiore sono state spostate fuori laboratorio e parcheggiate in nostro garage! Questi automobili all'avanguardia contengono numerose di sensori, in modo che possano per rilevare e monitorare milioni di eventi ogni secondo. Si prevede che il 2020, la maggior parte di questi Auto verrà siano stata collegata a internet. Si supponga di tocco in questo ampia varietà di dati per la migliore sicurezza di classe, l'affidabilità e Guida esperienza! Microsoft ha reso questa da sogno in realtà con Intelligence Cortana.

Business Intelligence Cortana Microsoft è completamente gestiti dati e famiglia analitica avanzato che consente di trasformare i dati in azione intelligente. Vogliamo presentare il modello di soluzione Cortana Intelligence veicolo telemetria Analitica. Questa soluzione illustra come Concessionarie auto, veicoli produttori e compagnie di assicurazione possono usare le funzionalità di Business Intelligence Cortana per ottenere in tempo reale e le abitudini di previsione informazioni approfondite sul veicolo salute e la Guida. 

La soluzione implementata come un [motivo architettura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) con potenziali della piattaforma Intelligence Cortana per la versione completa in tempo reale ed elaborazione batch. Soluzione: 

- fornisce un simulatore telematiche veicolo
- Consente di sfruttare evento hub per il caricamento di milioni di eventi di telemetria veicolo simulato in Azure 
- utilizza flusso Analitica per ottenere in tempo reale informazioni dettagliate sui integrità veicolo
-  rende i dati nel spazio di archiviazione a lungo termine per analitica batch più complesse. 
- Consente di sfruttare apprendimento per il rilevamento di anomalie in tempo reale e l'elaborazione per ottenere informazioni approfondite stima batch.
- utilizza HDInsight per trasformare i dati in scala e Factory di dati per gestire la gestione, programmazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch 
- offre questa soluzione un dashboard RTF per i dati in tempo reale e visualizzazioni analitica previsione usando Power BI

## <a name="architecture"></a>Architettura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1-architettura di soluzione Analitica telemetria veicolo*

Questa soluzione include i seguenti **componenti di Business Intelligence Cortana** e illustra la loro integrazione to end


- **Evento hub** per il caricamento di milioni di eventi di telemetria veicolo in Azure.
- **Flusso Analitica** per ottenere in tempo reale informazioni dettagliate sui integrità veicolo e rende i dati nel spazio di archiviazione a lungo termine per analitica batch più complesse.
- **Apprendimento** per il rilevamento di anomalie in tempo reale ed elaborazione batch per ottenere informazioni approfondite previsione.
- **HDInsight** viene utilizzata per trasformare i dati in scala
- **Data Factory** gestisce orchestrazione, pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch.
- **Power BI** offre questa soluzione un dashboard RTF per i dati in tempo reale e visualizzazioni analitica previsione.

Questa soluzione accede a due diverse **origini dati**: 

- **Simulata veicolo segnali e diagnostica**: un simulatore telematiche veicolo genera informazioni di diagnostica e segnala che corrisponde allo stato del veicolo e il modello di Guida in un determinato momento. 
- **Catalogo veicolo**: un set di dati di riferimento che contiene un numero VIN al mapping del modello.
