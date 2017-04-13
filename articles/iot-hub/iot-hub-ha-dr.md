<properties
 pageTitle="Hub IoT HA e DR | Microsoft Azure"
 description="Illustra tutte le caratteristiche che consentono di creare soluzioni IoT disponibile con emergenza funzionalità di ripristino."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub alto disponibilità e ripristino di emergenza

Come servizio Azure IoT Hub fornisce disponibilità (HA) utilizzando ridondanti a livello di Azure area geografica, senza ulteriore lavoro necessario affinché la soluzione. Inoltre, Azure offre numerose caratteristiche che consentono di creare soluzioni con ripristino (DR) di emergenza o area geografica tra disponibilità, se necessario. È necessario progettare e preparare le soluzioni in modo da sfruttare le funzionalità di DR se si desidera fornire globale, area tra disponibilità per gli utenti o dispositivi. L'articolo [Indicazioni tecniche di Azure Business continuità](../resiliency/resiliency-technical-guidance.md) descrive le caratteristiche incorporate di Azure per la continuità aziendale e DR. Carta [di emergenza e disponibilità per applicazioni Azure][] fornisce indicazioni architettura su strategie per le applicazioni Azure ottenere HA e DR.

## <a name="azure-iot-hub-dr"></a>Azure DR IoT Hub
Oltre all'interno dell'area HA, IoT Hub implementata meccanismi di failover di emergenza che non richiedono alcun intervento da parte dell'utente. IoT Hub DR automatica viene avviata e ha un obiettivo di tempo di ripristino (RTO) 2 26 ore e i seguenti obiettivi di punto di ripristino (RPOs).

| Funzionalità | RILASCIATO |
| ------------- | --- |
| Disponibilità servizio per le operazioni del Registro di sistema e comunicazioni | Perdita CName |
| Dati dell'identità del Registro di sistema di dispositivo identità | 0-5 minuti la perdita di dati |
| Messaggi di dispositivo nel cloud | Tutti i messaggi non letti vengono persi |
| Operazioni di monitoraggio dei messaggi | Tutti i messaggi non letti vengono persi |
| Messaggi cloud al dispositivo | 0-5 minuti la perdita di dati |
| Coda di commenti e suggerimenti cloud al dispositivo | Tutti i messaggi non letti vengono persi |

## <a name="regional-failover-with-iot-hub"></a>Failover internazionali con IoT Hub

Trattare di topologie di distribuzione soluzioni IoT non è compreso nell'ambito di questo articolo, ma ai fini della disponibilità e il ripristino di emergenza è verrà preso in considerazione il modello di distribuzione *failover internazionali* .

In un modello failover internazionali soluzione back-end è in esecuzione principalmente in un'unica posizione Data Center, ma un hub IoT aggiuntiva e back-end vengono distribuiti in un'altra posizione Data Center ai fini della failover, nel caso in cui hub IoT in Data Center del principale presenta un'interruzione del servizio o la connettività di rete dal dispositivo al Data Center principale qualche verrà interrotto. Dispositivi di utilizzano un endpoint del servizio secondario ogni volta che il gateway primario non può essere raggiunto. Grazie alla funzionalità failover tra regione, la disponibilità di soluzione può essere migliorata oltre la disponibilità di una singola regione.

Un alto livello, per implementare un modello di failover internazionali con IoT Hub, sarà necessario le operazioni seguenti.

* **Un hub IoT e dispositivo routing logica secondario**: in caso di un'interruzione del servizio nella propria area principale, dispositivi devono iniziare la connessione a seconda del paese secondario. Natura compatibile con lo stato della maggior parte dei servizi coinvolti, vengono in genere per gli amministratori di soluzione avviare il processo di failover tra l'area geografica. Il modo migliore per comunicare nuovo endpoint ai dispositivi, mantenendo il controllo del processo, è necessario controllare regolarmente un servizio *concierge* relativo all'endpoint attivo corrente. Il servizio concierge può essere un'applicazione web semplice che viene replicata e mantenere raggiungibile utilizzando il reindirizzamento DNS tecniche (ad esempio, [il traffico][]di Azure gestione).
* **La replica del Registro di sistema di identità** - per essere utilizzabile, IoT secondario hub deve contenere tutte le identità di dispositivo in grado di connettersi alla soluzione. La soluzione deve mantenere replicato geografico backup delle identità dispositivo e caricarli in hub IoT secondario prima di passare punto finale attivo per i dispositivi. La funzionalità di esportazione dispositivo identità di IoT Hub è molto utile in questo contesto. Per ulteriori informazioni, vedere [Guida di sviluppo di Hub IoT - del Registro di sistema di identità][].
* Tutto lo stato e i dati che sono stati creati nel sito secondario devono essere eseguita la migrazione di **unione logica** - quando l'area principale nuovamente disponibile, eseguire il backup all'area principale. Si riferisce prevalentemente al dispositivo identità e applicazione dei metadati, che devono essere uniti con hub IoT principale e altri archivi di specifiche di un'applicazione nell'area principale. Per semplificare questo passaggio, è consigliabile utilizzare idempotente operazioni. In questo modo ripercussioni non solo da un eventuale distribuzione coerenza degli eventi, ma anche da duplicati o di recapito in ordine degli eventi. Inoltre, la logica dell'applicazione deve essere progettata per tollerare eventuali incoerenze o "leggermente" dalla data stato. A causa di più tempo impiegato per il sistema "correggere" Questa è basata sugli obiettivi del punto di ripristino (rilasciato).

## <a name="next-steps"></a>Passaggi successivi

Seguire questi collegamenti per altre informazioni sull'IoT Hub Azure:

- [Guida introduttiva a hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è Azure IoT Hub?][]

[Ripristino di emergenza e disponibilità per applicazioni Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Gestore del traffico Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guida di sviluppo IoT Hub - del Registro di sistema di identità]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
