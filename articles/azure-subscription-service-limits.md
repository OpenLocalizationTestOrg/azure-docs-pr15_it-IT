<properties
    pageTitle="Abbonamento a Microsoft Azure e limiti del servizio, le quote e vincoli"
    description="Fornisce un elenco di sottoscrizione di Azure comuni e limiti del servizio, le quote e vincoli. Include informazioni su come aumentare limiti oltre a valori massimo."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limiti di servizio, Azure sottoscrizione e le quote e vincoli

## <a name="overview"></a>Panoramica

In questo documento specifica alcuni dei limiti di Microsoft Azure più comuni. Non comprende attualmente tutti i servizi di Azure. Nel tempo, questi limiti verranno espanso e aggiornati per coprire più della piattaforma.

Visitare la pagina [Panoramica prezzi Azure](https://azure.microsoft.com/pricing/) per altre informazioni sui prezzi Azure. Non esiste, è possibile valutare i costi con la [Calcolatrice prezzi](https://azure.microsoft.com/pricing/calculator/) o visitando la pagina dei dettagli prezzi per un servizio (ad esempio [Macchine virtuali di Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Se si desidera generare il limite oltre il **Limite predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza costi aggiuntivi](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). I limiti non possono essere generati sopra il valore **Massimo** nella tabella seguente. Se non vi è alcuna colonna di **Un limite massimo** , la risorsa specificata non ha limiti modificabili.

## <a name="limits-and-the-azure-resource-manager"></a>Limiti e la gestione di risorse di Azure

A questo punto è possibile combinare più Azure risorse a un singolo gruppo risorse Azure. Quando si usano i gruppi di risorse, limiti che una volta erano globali diventano gestiti a livello locale in Gestione risorse di Azure. Per ulteriori informazioni sui gruppi di risorse di Azure, vedere [Panoramica di gestione di risorse Azure](azure-resource-manager/resource-group-overview.md).

In limiti, è stato aggiunto una nuova tabella in modo da rispecchiare eventuali differenze tra i limiti quando si utilizza la gestione di risorse di Azure. È ad esempio una tabella di **Sottoscrizione limiti** e una tabella di **Sottoscrizione limiti - Gestione risorse di Azure** . Quando si applica un limite per entrambi gli scenari, viene visualizzata solo nella prima tabella. Se non diversamente specificato, limiti sono globali tra tutte le aree.

> [AZURE.NOTE] È importante sottolineare che le quote per le risorse gruppi Azure siano per paese accessibili tramite l'abbonamento e non per abbonamento, come sono le quote di gestione dei servizi. Utilizzare le quote di base come esempio. Se è necessario richiedere un aumento di quota con il supporto per core, è necessario decidere quante core che si desidera utilizzare in quali aree geografiche e quindi apportare una richiesta delle quote di gruppo di risorse Azure core specifica per gli importi e aree geografiche che si desidera. Pertanto, se è necessario utilizzare 30 core in Europa occidentale per eseguire l'applicazione; in particolare, è consigliabile richiedere 30 core europei ovest. Ma non sarà disponibile una quota core aumentare in un'altra regione - solo ovest Europa avrà la quota di 30-core.
<!-- -->
Di conseguenza, potrebbero risultare utili per valutare la possibilità di decidere quali le quote di gruppo di risorse Azure necessario per il carico di lavoro in un'area di un e richiedere l'importo in ogni area geografica in cui si prevede di distribuzione. Vedere [risoluzione dei problemi di distribuzione](./resource-manager-common-deployment-errors.md) per altre informazioni sull'individuazione le quote corrente per aree specifiche.

## <a name="service-specific-limits"></a>Limiti di specifico del servizio

- [Active Directory](#active-directory-limits)
- [Gestione dell'API](#api-management-limits)
- [Servizio di App](#app-service-limits)
- [Gateway di applicazioni](#application-gateway-limits)
- [Informazioni dettagliate sui applicazione](#application-insights-limits)
- [Automazione](#automation-limits)
- [Cache Redis Azure](#azure-redis-cache-limits)
- [RemoteApp Azure](#azure-remoteapp-limits)
- [Copia di backup](#backup-limits)
- [Batch](#batch-limits)
- [Servizi BizTalk](#biztalk-services-limits)
- [RETE CDN](#cdn-limits)
- [Servizi cloud](#cloud-services-limits)
- [Dati Factory](#data-factory-limits)
- [Dati Lake Analitica](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Hub di evento](#event-hubs-limits)
- [Hub IoT](#iot-hub-limits)
- [Archivio di chiave](#key-vault-limits)
- [Servizi multimediali di Microsoft](#media-services-limits)
- [Impegno per dispositivi mobili](#mobile-engagement-limits)
- [Servizi di telefonia mobile](#mobile-services-limits)
- [Monitoraggio](#monitoring-limits)
- [Autenticazione a più fattori](#multi-factor-authentication)
- [Rete](#networking-limits)
- [Servizio di notifica Hub](#notification-hub-service-limits)
- [Approfondimenti operativi](#operational-insights-limits)
- [Gruppo di risorse](#resource-group-limits)
- [Utilità di pianificazione](#scheduler-limits)
- [Ricerca](#search-limits)
- [Bus di servizio](#service-bus-limits)
- [Ripristino del sito](#site-recovery-limits)
- [Database SQL](#sql-database-limits)
- [Spazio di archiviazione](#storage-limits)
- [Sistema StorSimple](#storsimple-system-limits)
- [Flusso Analitica](#stream-analytics-limits)
- [Abbonamento](#subscription-limits)
- [Gestore del traffico](#traffic-manager-limits)
- [Macchine virtuali](#virtual-machines-limits)
- [Set di scala macchina virtuale](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Limiti di abbonamento
#### <a name="subscription-limits"></a>Limiti di abbonamento
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti di sottoscrizione - Gestione risorse di Azure

Limitazioni seguenti quando si utilizza la gestione risorse di Azure e Azure gruppi di risorse. Limiti che non sono state modificate in Gestione risorse di Azure non sono elencati di seguito. Fare riferimento alla tabella precedente per questi limiti.

Per informazioni sulla gestione limiti per le richieste di Manager delle risorse, vedere [le richieste di limitazione Manager delle risorse](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Limiti di gruppo di risorse

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti di macchine virtuali
#### <a name="virtual-machine-limits"></a>Limiti di macchina virtuale
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti di macchine virtuali - Gestione risorse di Azure

Limitazioni seguenti quando si utilizza la gestione risorse di Azure e Azure gruppi di risorse. Limiti che non sono state modificate in Gestione risorse di Azure non sono elencati di seguito. Fare riferimento alla tabella precedente per questi limiti.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limiti di set di scala macchina virtuale

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limiti di rete

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limiti di rete
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limiti di Gateway di applicazioni

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limiti per il traffico Manager

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limiti DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limiti di archiviazione

Per ulteriori informazioni sui limiti di spazio di archiviazione account, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limiti di servizio di archiviazione

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limiti del disco macchina virtuale

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Per ulteriori informazioni, vedere [le dimensioni di macchina virtuale](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Account di archiviazione standard**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Account di archiviazione Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limiti di archiviazione Provider di risorse

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Limiti di servizi cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Limiti di servizio di App
I limiti di servizio App seguenti includono limiti per Web Apps, App Mobile, API App e App logica.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limiti di utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti di batch

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Limiti di servizi BizTalk
Nella tabella seguente mostra i limiti per i servizi Biztalk Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>Limiti di DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Quote di elencati con un asterisco (*) [può essere modificato contattando il supporto di Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limiti di impegno mobile

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Limiti della ricerca

Livelli prezzi determinano la capacità e limiti del servizio di ricerca. Di seguito sono riportati i livelli:

- Servizio multi-tenant *gratuito* , condiviso con altri utenti sottoscrittori Azure destinato valutazione e sviluppo piccoli progetti.
- *Base* fornisce le risorse di elaborazione dedicate per carichi di lavoro di produzione proporzioni ridotte, con un massimo di tre per carichi di lavoro query disponibilità.
- *Standard (S1, S2, S3, S3 ad alta densità)* è per carichi di lavoro di produzione più grandi. All'interno di livello standard sono presenti più livelli in modo che è possibile scegliere una configurazione delle risorse per scenari specifici.

**Limiti per abbonamento**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per ogni servizio di ricerca**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Per informazioni più granulare altri limiti, tra cui la dimensione dei documenti query al secondo, le chiavi, le richieste e le risposte, vedere [limiti di servizio di ricerca di Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti di servizi multimediali

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limiti di rete CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti di servizi mobili

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limiti di monitoraggio

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limiti di Hub servizio di notifica

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limiti di hub di evento

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limiti di Bus di servizio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub limiti

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limiti di Factory dati

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Dati Analitica Lake limiti
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Flusso Analitica limiti
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limiti di Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Limiti di RemoteApp Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti di sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Limiti di approfondimenti operativi

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limiti di backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limita il ripristino del sito

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Applicazione informazioni dettagliate sui limiti

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limiti di gestione delle API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limiti di Azure Cache Redis

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti di archivio di chiave

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limiti di Database SQL

I limiti di Database SQL, vedere [Limiti delle risorse di Database SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Vedere anche

[Informazioni sulle aumenti e limiti di Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Macchina virtuale e le dimensioni di servizio Cloud per Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Formati per i servizi Cloud](cloud-services/cloud-services-sizes-specs.md)
