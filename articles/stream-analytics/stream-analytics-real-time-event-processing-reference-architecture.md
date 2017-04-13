<properties 
    pageTitle="Eventi in tempo reale l'elaborazione di elaborazione dell'evento flusso Analitica | Microsoft Azure" 
    description="Informazioni su come un insieme di servizi Azure può interagire per consentire l'elaborazione di eventi in tempo reale e analitica." 
    keywords="elaborazione in tempo reale, elaborazione dell'evento, l'architettura di riferimento"
    services="stream-analytics,event-hubs,storage,sql-database" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="stream-analytics" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Fare riferimento a architettura: eventi in tempo reale con Microsoft Azure flusso Analitica di elaborazione

Architettura di riferimento per eventi in tempo reale di elaborazione con Azure flusso Analitica deve fornire un progetto generico per la distribuzione di una piattaforma in tempo reale come una soluzione di elaborazione del flusso di servizio (PaaS) con Microsoft Azure.

## <a name="summary"></a>Riepilogo

In genere, le soluzioni analitica sono state basate sulle funzionalità, ad esempio ETL (estratto, trasformazione, carico) e data warehouse, memorizzazione dei dati prima dell'analisi. Requisiti di modifica, inclusi altri dati rapidamente in entrata, siano conducendo questo modello esistente per il limite. Sebbene non sia una nuova funzione, l'approccio non è stato ampiamente adottato tra tutti verticali di settore la possibilità di analizzare i dati all'interno di spostamento flussi prima dello spazio di archiviazione è una soluzione. 

Microsoft Azure fornisce un vasta catalogo delle tecnologie analitica che sono in grado di supportare una matrice di soluzione diversi scenari e requisiti. Selezionare servizi Azure per distribuire per una soluzione-to-end può essere un'operazione impegnativa specificata nell'ambito dell'offerta. In questo documento è progettato per descrivere le funzionalità e l'interazione tra i vari servizi Azure che supportano la soluzione streaming evento. Vengono illustrati alcuni scenari in cui i clienti possono trarre vantaggio da questo tipo di approccio.

## <a name="contents"></a>Contenuto

- Gruppo di dirigenti
- Introduzione a Analitica in tempo reale
- Proposta di valore dei dati in tempo reale in Azure
- Scenari comuni per Analitica in tempo reale
- Architettura e i componenti
    - Origini dati
    - Livello di integrazione dei dati
    - Livello Analitica in tempo reale
    - Livello di archiviazione di dati
    - Presentazione / consumo livello
- Conclusioni

**Autore:** Charles Feddersen, progettista soluzione, dati approfondimenti centro di eccellenza, Microsoft Corporation

**Pubblicato:** Gennaio 2015

**Revisione:** 1.0

**Download:** [Elaborazione con Microsoft Azure flusso Analitica eventi in tempo reale](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 
