<properties
 pageTitle="Azure IoT preconfigurato soluzioni | Microsoft Azure"
 description="Descrizione IoT Azure preconfigurato soluzioni e l'architettura con collegamenti a risorse aggiuntive."
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

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quali sono le soluzioni di Azure IoT Suite preconfigurato?

Le soluzioni di Azure IoT Suite preconfigurato sono implementazioni serie comuni IoT soluzione che è possibile distribuire in Azure con l'abbonamento. È possibile utilizzare le soluzioni preconfigurate:

- Come punto di partenza per soluzioni IoT.
- Per informazioni sui modelli comuni IoT soluzione Progettazione e sviluppo.

Ogni soluzione preconfigurate è implementazione di completamento, to end che utilizza dispositivi simulati per generare telemetria.

Oltre la distribuzione e l'esecuzione di soluzioni in Azure, è possibile scaricare il codice sorgente completo e quindi personalizzare ed estendere la soluzione in base alle specifiche esigenze IoT.

> [AZURE.NOTE] Per distribuire una delle soluzioni preconfigurate, visitare [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L'articolo [Introduzione a soluzioni IoT preconfigurato] [ lnk-getstarted-preconfigured] vengono fornite ulteriori informazioni su come distribuire ed eseguire una delle soluzioni.

Nella tabella seguente mostra come le soluzioni eseguire il mapping a caratteristiche specifiche IoT:

| Soluzione | Acquisizione di dati | Dispositivo identità | Comando e controllo | Le regole e azioni | Stima Analitica |
|------------------------|-----|-----|-----|-----|-----|
| [Monitoraggio remoto][lnk-getstarted-preconfigured] | Sì | Sì | Sì | Sì | -   |
| [Manutenzione previsione][lnk-predictive-maintenance] | Sì | Sì | Sì | Sì | Sì |

- *Acquisizione di dati*: ingresso dei dati in scala nel cloud.
- *Identità dispositivo*: gestione delle identità univoche di tutti i dispositivi connessi.
- *Comando e controllo*: inviare messaggi a un dispositivo dal cloud cause del dispositivo eseguire un'azione.
- *Le regole e azioni*: soluzione back-end utilizza le regole per agire sui dati specifici di dispositivo nel cloud.
- *Stima analitica*: soluzione back-end applica analizzati i dati di dispositivo nel cloud per prevedere quando azioni specifiche devono avvenire. Ad esempio analisi telemetria motore aereo per stabilire quando è dovuta manutenzione del motore.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Panoramica della soluzione di monitoraggio preconfigurato remoto

Avendo scelto illustrare la soluzione preconfigurata monitoraggio remota in questo articolo perché illustra diversi elementi della struttura comuni che condividono le altre soluzioni.

Il diagramma seguente illustra gli elementi principali della soluzione di monitoraggio remoto. Nelle sezioni seguenti vengono forniscono ulteriori informazioni su questi elementi.

![Architettura di soluzione monitoraggio preconfigurato remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivi

Quando si distribuisce una soluzione di preconfigurato monitoraggio remoto, quattro dispositivi simulati sono pre-provisioning della soluzione che consente di simulare un dispositivo di raffreddamento. Questi dispositivi simulati contiene un modello di temperatura e umidità predefinito che genera telemetria. Questi dispositivi simulati sono inclusi per illustrare il flusso di-to-end di dati tramite la soluzione e specificare un'origine utile di telemetria e un valore di destinazione per i comandi se ci si trova uno sviluppatore di back-end tramite la soluzione come punto di partenza per l'implementazione personalizzata.

Quando un dispositivo si connette a IoT Hub prima della soluzione preconfigurato monitoraggio remoto, il messaggio di informazioni dispositivo inviato a hub IoT consente di enumerare l'elenco dei comandi in grado di rispondere per il dispositivo. Della soluzione preconfigurato monitoraggio remoto, i comandi sono: 

- *Dispositivo di ping*: risponde a questo comando il riconoscimento. Questo è utile per verificare che il dispositivo sia ancora attivo e in attesa.
- *Avviare telemetria*: indica il dispositivo per iniziare a inviare telemetria.
- *Interrompere telemetria*: indica il dispositivo per interrompere l'invio di telemetria.
- *Modifica Imposta punto temperatura*: controlla i valori di telemetria temperatura simulato invia il dispositivo. Questo è utile per verificare la logica di back-end.
- *Diagnostica telemetria*: controlla se il dispositivo deve inviare la temperatura esterna come telemetria.
- *Modifica dello stato di dispositivo*.: imposta la proprietà dei metadati di stato dispositivo che riporta il dispositivo. Questo è utile per verificare la logica di back-end.

È possibile aggiungere più dispositivi simulati alla soluzione che generare telemetria stesso e rispondere ai comandi stesso. 

## <a name="iot-hub"></a>Hub IoT

In questa soluzione preconfigurata istanza IoT Hub corrisponde al *Gateway Cloud* in una tipica [architettura di soluzione IoT][lnk-what-is-azure-iot].

Un hub IoT riceve telemetria da dispositivi in un unico endpoint. Un hub IoT mantiene inoltre i punti finali specifico di dispositivo in cui ogni dispositivi possono recuperare i comandi che vengono inviati.

Hub IoT rende disponibili tramite telemetria lato servizio leggere endpoint telemetria ricevuto.

## <a name="azure-stream-analytics"></a>Flusso Azure Analitica

La soluzione preconfigurata utilizza tre [Azure flusso Analitica] [ lnk-asa] processi (ASA) per filtrare il flusso di telemetria dei dispositivi di:


- *Processo DeviceInfo* - invia dati a un hub di eventi che indirizza dispositivo registrazione specifici messaggi, inviati quando un dispositivo si connette prima di tutto o in risposta a un comando di **Modifica dello stato di dispositivo** per la soluzione dispositivo del Registro di sistema (un database DocumentDB). 
- *Processo di telemetria* - invia tutti telemetria elaborato a archiviazione blob Azure per refrigerazione e calcola le aggregazioni di telemetria che vengono visualizzati nel dashboard soluzione.
- *Processo di regole* - filtra il flusso di telemetria per i valori che superano le soglie regola e vengono restituiti i dati a un hub di evento. Quando una regola viene attivata, la visualizzazione del dashboard del portale soluzione viene visualizzato l'evento come una nuova riga nella tabella della cronologia allarmi e attiva un'azione in base alle impostazioni definite nelle visualizzazioni delle regole e azioni nel portale di soluzione.

In questa soluzione preconfigurata processi ASA fanno parte di allo **IoT soluzione back-end** in una tipica [architettura di soluzione IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Processore eventi

In questa soluzione preconfigurata processore di eventi fa parte di **soluzione IoT back-end** in una tipica [architettura di soluzione IoT][lnk-what-is-azure-iot].

I processi **DeviceInfo** e **regole** ASA inviare l'output a un hub di evento per il recapito della ad altri servizi di back-end. La soluzione utilizza un [EventPocessorHost] [ lnk-event-processor] istanza in esecuzione in una [WebJob][lnk-web-job]per leggere i messaggi da questi hub evento. **EventProcessorHost** utilizza i dati **DeviceInfo** per aggiornare i dati di dispositivo del database DocumentDB e utilizza i dati di **regole** per richiamare la logica app e visualizzare gli avvisi nel portale di soluzione di aggiornamento.

## <a name="device-identity-registry-and-documentdb"></a>Registro di sistema di dispositivo identità e DocumentDB

Ogni hub IoT include un [Registro di sistema di dispositivo identità] [ lnk-identity-registry] in cui sono archiviati i tasti di dispositivo. Queste informazioni vengono usate IoT Hub autenticare i dispositivi - un dispositivo che deve essere registrato e ha un tasto valido prima che sia possibile connetterlo al hub.

Questa soluzione consente di memorizzare informazioni aggiuntive sui dispositivi, ad esempio il proprio stato, i comandi che supportano e altri metadati. La soluzione utilizza un database DocumentDB per memorizzare dati specifici dispositivo e il portale soluzione recupera i dati da questo database DocumentDB per l'interfaccia utente e la modifica.

La soluzione anche necessario mantenere le informazioni del Registro di sistema identità dispositivo sincronizzati con il contenuto del database DocumentDB. **EventProcessorHost** utilizza i dati dal processo di analitica flusso **DeviceInfo** per gestire la sincronizzazione.

## <a name="solution-portal"></a>Portale soluzione

![Dashboard di soluzione][img-dashboard]

Il portale di soluzione è un'interfaccia utente basata sul web che viene distribuita nel cloud come parte della soluzione preconfigurata. Consente di:

- Visualizzazione della cronologia di telemetria e avviso in un dashboard.
- Effettuare il provisioning di nuovi dispositivi.
- Gestire e monitorare i dispositivi.
- Inviare i comandi di dispositivi specifici.
- Gestire le regole e azioni.

In questa soluzione preconfigurata portale soluzione dei moduli di parte di **soluzione IoT back-end** e parte di **elaborazione e integrazione applicativa** in tipica [architettura di soluzione IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle architetture delle soluzioni IoT, vedere [services di Microsoft Azure IoT: architettura di riferimento][lnk-refarch].

A questo punto si sa quale una soluzione preconfigurata è, è possibile iniziare da distribuire la soluzione *monitoraggio remoto* preconfigurato: [iniziare a utilizzare le soluzioni preconfigurate][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md