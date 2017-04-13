<properties
    pageTitle="Che cos'è il traffico Manager | Microsoft Azure"
    description="In questo articolo utili per comprendere meglio che cos'è il traffico Manager, e se è la scelta di routing il traffico appropriata per l'applicazione"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Panoramica di gestione del traffico

Gestione il traffico di Microsoft Azure consente di controllare la distribuzione del traffico utente per gli endpoint servizio nei data center diverso. Endpoint servizio supportati da Gestione il traffico includono macchine virtuali di Azure, Web Apps e servizi cloud. Utilizzare il traffico Manager con endpoint esterni, non Azure.

Il traffico Manager utilizza il sistema DNS (Domain Name) per indirizzare le richieste di client per l'endpoint più appropriato in base a un [metodo di routing del traffico](traffic-manager-routing-methods.md) e l'integrità dei punti finali. Gestore del traffico offre una gamma di metodi di routing del traffico in base alle proprie esigenze di applicazione diverso, endpoint del [monitoraggio](traffic-manager-monitoring.md)e failover automatico. Gestore del traffico è flessibile per errore, incluso l'errore di un'intera regione Azure.

## <a name="traffic-manager-benefits"></a>Vantaggi di gestore del traffico

Gestore del traffico consente di:

- **Migliorare la disponibilità delle applicazioni critiche**

    Il traffico Manager offre disponibilità per le applicazioni di monitoraggio gli endpoint e fornendo failover automatico quando un endpoint non è raggiungibile.

- **Migliorare la velocità di risposta per le applicazioni di prestazioni elevate**

    Azure consente di eseguire servizi cloud o siti Web nei data center tutto il mondo. Gestore del traffico consente di migliorare la velocità di risposta dell'applicazione indirizza il traffico all'endpoint con la latenza di rete più bassa per il client.

- **Eseguire la manutenzione di servizio senza tempi di inattività**

    È possibile eseguire le operazioni di manutenzione pianificata nelle applicazioni senza tempi di inattività. Gestore del traffico indirizzare il traffico al endpoint alternativo durante la gestione è in corso.

- **Combinare locale e applicazioni basate su Cloud**

    Gestore del traffico supporta esterni e i punti finali non Azure in modo da utilizzare con ibrido cloud locale distribuzioni, tra cui "burst-a-cloud," "eseguire la migrazione-a-cloud," e "failover nel cloud" scenari.

- **Distribuire il traffico per distribuzioni complesse di grandi dimensioni**

    Uso [dei profili di gestore del traffico annidata](traffic-manager-nested-profiles.md), routing del traffico metodi possono essere combinati per creare regole sofisticate e flessibile per supportare le esigenze di distribuzioni più complesse.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul [funzionamento del gestore del traffico](traffic-manager-how-traffic-manager-works.md).

- Informazioni su come sviluppare applicazioni disponibilità utilizzando [il monitoraggio di gestore del traffico endpoint](traffic-manager-monitoring.md).

- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportata da Gestione il traffico.

- [Creare un profilo di gestore del traffico](traffic-manager-manage-profiles.md).

