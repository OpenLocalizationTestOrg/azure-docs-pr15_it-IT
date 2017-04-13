<properties
   pageTitle="Come usare le origini dati 'dati grande' | Microsoft Azure"
   description="Articolo procedure evidenziazione motivi per l'uso del catalogo dati di Azure con origini dati 'dati grande', tra cui archiviazione Blob Azure, Lake di dati di Azure e Hadoop HDFS."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Per imparare a gestire le origini dati nel catalogo dati di Azure

## <a name="introduction"></a>Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestita che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. In altre parole, **Catalogo dati di Azure** è tutto sulle persone risoluzione alla scoperta di comprendere e consente di origini dati e alle organizzazioni di risoluzione per ottenere il massimo da origini dati esistenti, inclusi i dati di grandi dimensioni.

**Catalogo dati di Azure** supporta la registrazione di Azure Blog archiviazione BLOB e directory, nonché file Hadoop HDFS e directory. Le origini dati, semistrutturata natura offre flessibilità, ma significa che gli utenti devono considerare come le origini dati sono organizzate per ottenere il massimo da li registra con **Catalogo dati di Azure**.

## <a name="directories-as-logical-data-sets"></a>Directory come set di dati logico

Un modello comune per l'organizzazione delle origini dati è considerato directory logico set di dati. Directory di primo livello vengono utilizzate per definire un set di dati, mentre le sottocartelle definiscono partizioni e i file che contengono archiviare i dati.

È possibile eseguire un esempio di questo modello.

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

In questo esempio, vehicle_maintenance_events e location_tracking_events rappresentano logico set di dati. Ognuna di queste cartelle contiene file di dati che sono organizzati per anno e mese in sottocartelle. Ognuna di queste cartelle potrebbe contenere centinaia o migliaia di file.

In questo modello, la registrazione di singoli file con **Il catalogo dati di Azure** probabilmente non avere senso. Se, tuttavia, registrare le directory che rappresentano gli insiemi di dati che saranno significativi per gli utenti che utilizzano con i dati.

## <a name="reference-data-files"></a>File di dati di riferimento

Un motivo complementare è archiviare il set di dati di riferimento come singoli file. Questi set di dati possono essere considerati come lato 'small' dei dati e spesso sono simili alle dimensioni in un modello di dati analitici. File di dati di riferimento includono i record che vengono utilizzati per fornire un contesto per la maggior parte dei file di dati archiviati in un' posizione nell'archivio di dati.

È possibile eseguire un esempio di questo modello.

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando un scienziato analyst o dati funziona con i dati contenuti nelle strutture di directory più grande, è possono utilizzare i dati in questi file di riferimento per fornire informazioni più dettagliate per entità che fanno riferimento le solo nome o l'ID nel set di dati più grande.

In questo modello può essere utile per registrare i file di dati di riferimento individuale con **Catalogo dati di Azure**. Ogni file rappresenta un set di dati e ciascuno di essi può essere annotato e individuato singolarmente.

## <a name="alternate-patterns"></a>Criteri alternativi

I modelli descritti in precedenza solo in due modi possibili che potrebbe essere organizzato un archivio di dati, ma ogni implementazione sarà diverso. Indipendentemente da come sono strutturate le origini dati, durante la registrazione delle origini dati con **Il catalogo dati di Azure**, focalizzare l'attenzione su come registrare il file e directory che rappresentano gli insiemi di dati che saranno del valore ad altri utenti all'interno dell'organizzazione. Registrazione di tutti i file e directory possibile messaggi secondari catalogo, rendendo più difficile agli utenti di trovare le informazioni desiderate.

## <a name="summary"></a>Riepilogo
La registrazione di origini dati con **Il catalogo dati di Azure** li rende più facile individuare e comprendere. Registrando e annotazione del file di dati e directory che rappresentano logico set di dati, si possono aiutare gli utenti di trovare e usare le origini dati che siano necessari.
