<properties
    pageTitle="Introduzione all'API di esportazione impegno per dispositivi mobili"
    description="Informazioni sulle nozioni fondamentali sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per utilizzare al meglio in strumenti personalizzati"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Introduzione all'API di esportazione impegno per dispositivi mobili

## <a name="introduction"></a>Introduzione

In questo documento verranno fornite le nozioni fondamentali sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per utilizzare al meglio in strumenti personalizzati.

## <a name="pre-requisites"></a>Prerequisiti

Esportazione di dati non elaborati da Mobile coinvolgimento richiede:

- Configurazione di autenticazione API per essere in grado di utilizzare le API (vedere [configurazione manuale di autenticazione](mobile-engagement-api-authentication-manual.md)),
- Utilizzare le API REST o [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
- Un account di archiviazione Azure.

>[AZURE.NOTE] Anche consigliabile eccellente [Esplora archivi di Microsoft Azure](http://storageexplorer.com/), almeno durante la fase di sviluppo come offre un'interfaccia utente di facile da usare per interagire con lo spazio di archiviazione di Azure.

## <a name="what-can-be-exported"></a>Cosa può essere esportato?

Coinvolgimento mobile consente agli utenti per la raccolta di molti tipi di dati e pertanto sono disponibili numerosi tipi di esportazione adatta a questi tipi di dati diversi.
Esistono 2 tipi essenziali di esportazione:

- Snapshot: utilizzato in genere per esportare i dati che rappresenta uno stato e per il quale coinvolgimento Mobile non ha una cronologia. Ad esempio inclusi tag (informazioni app), i token o push campagna feedback. Di conseguenza questi tipi di esportazione non sono correlati a una data.
- cronologiche: questo tipo di esportazione viene utilizzato per i dati che viene accumulato nel tempo, ad esempio manifestazioni o attività, ad esempio.

Nella tabella seguente sono descritte ampiamente tutte le possibili esportazioni:

| Tipo di esportazione | Tipo di dati | Descrizione                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Snapshot    | Push      | Genera l'esportazione dei feedback Push campagne alla scala cronologica per deviceid o l'ID utente                                                              |
| Snapshot    | Tag       | Genera l'esportazione dei tag (informazioni app) associati a ogni dispositivi                                                                       |
| Snapshot    | Dispositivo    | Genera l'esportazione della maggior parte dei dati sui dispositivi, ad esempio technicals (modello, impostazioni locali, fuso orario,...), il tag, visto primo,... |
| Snapshot    | Token     | Genera l'esportazione di tutti i token validi                                                                                                 |
| Nella cronologia  | Attività  | Genera l'esportazione di tutte le attività per ogni dispositivi in un determinato periodo di tempo                                                           |
| Nella cronologia  | Evento     | Genera l'esportazione di tutte le attività per ogni dispositivi in un determinato periodo di tempo                                                           |
| Nella cronologia  | Processo       | Genera l'esportazione di tutti i processi per ogni dispositivi in un determinato periodo di tempo                                                                 |
| Nella cronologia  | Errore     | Genera l'esportazione di tutti gli errori per ogni dispositivi in un determinato periodo di tempo                                                               |

## <a name="how-does-it-work"></a>Come funziona?

Esportazioni sono lunghe esecuzione di attività che potrebbero generare file di dati di grandi dimensioni. Per questo motivo, non possono essere richiamati per restituire immediatamente un file da scaricare.
Per esportare i dati da coinvolgimento Mobile, sarà necessario creare un **Processo di esportazione** tramite API in cui è in genere specificare:

- Tipo di esportazione (snapshot o nella cronologia)
- Il tipo di dati
- Il **Contenitore di spazio di archiviazione di Azure** (inclusa una SA valido con scrittura) in cui verrà scritto il risultato dell'esportazione.

Si noti che può richiedere alcuni minuti per il lavoro per l'avvio e quindi essere eseguire da alcuni secondi per piccoli App a diverse ore per le app con molti utenti o attività.

Una volta creato il processo, è possibile controllare il relativo stato per verificare se è ancora in esecuzione o se è stata completata.

Dopo il processo è stata eseguita correttamente, il file di dati risultante è disponibile per il contenitore di archiviazione fornito.
