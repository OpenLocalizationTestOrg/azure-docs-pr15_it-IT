
<properties
   pageTitle="Indicazioni Azure | modelli e procedure | Microsoft Azure"
   description="Procedure consigliate e indicazioni per Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Indicazioni Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Il team di modelli e procedure di Microsoft fa parte di Azure clienti avviso. Il nostro obiettivo è aiutare gli sviluppatori, progettisti e professionisti IT sulla piattaforma Microsoft Azure. Abbiamo orientamenti che mostra procedure consigliate per la creazione di soluzioni basate su cloud in Azure.

## <a name="checklists"></a>Elenchi di controllo

Questi elenchi sono di riferimento rapido per la revisione gli aspetti fondamentali della disponibilità e scalabilità. 

- [Elenco di controllo di disponibilità][AvailabilityChecklist] 

    Riepilogo delle procedure consigliate per garantire la disponibilità.

- [Elenco di controllo scalabilità][ScalabilityChecklist]

    Riepilogo delle procedure consigliate per la progettazione e implementazione scalable dei servizi e la gestione di gestione dei dati.

## <a name="best-practices-articles"></a>Procedure ottimali articoli

In questi articoli sono informazioni più dettagliate dei concetti importanti comunemente associate cloud computing. 

- [API di progettazione][APIDesign] 

    Informazioni sui problemi di progettazione per la progettazione dell'API web.

- [Implementazione dell'API][APIImplementation] 

    Un set di procedure consigliate per l'implementazione e la pubblicazione di un web API.

- [Guida alla protezione API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Una discussione dei problemi di autenticazione e autorizzazione (ad esempio, i tipi di token, protocolli di autorizzazione, flussi di autorizzazione e riduzione dei rischi per).

- [Indicazioni per il ridimensionamento automatico][AutoscalingGuidance] 

    Riepilogo delle considerazioni per la scala soluzioni senza la necessità di intervento manuale.

- [Indicazioni di processi in background][BackgroundJobsGuidance] 

    Descrizione delle opzioni disponibili e procedure consigliate per l'implementazione di attività che deve essere eseguita in background, in modo indipendente da qualsiasi in primo piano o operazioni interattive.

- [Contenuto indicazioni per la rete (CDN)][CDNGuidance] 

    Indicazioni generali e procedure consigliate per usare la rete CDN per ridurre il carico sulle applicazioni e ottimizzare la disponibilità e delle prestazioni.

- [Indicazioni per la memorizzazione nella cache][CachingGuidance] 

    Riepilogo delle modalità di utilizzare la cache per migliorare le prestazioni e scalabilità del sistema.

- [Guida di partizionamento dati][DataPartitioningGuidance]

    Strategie che è possibile utilizzare dati partizione per migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni.

- [Guida di monitoraggio e diagnostica][MonitoringandDiagnosticsGuidance] 

    Indicazioni per la registrazione come gli utenti utilizzano i singoli sistemi di monitorare l'utilizzo delle risorse e in genere monitoraggio dell'integrità e le prestazioni del sistema.

- [Convenzioni di denominazione consigliate][naming-conventions] 

    Convenzioni di denominazione consigliate per le risorse Azure.

- [Riprovare indicazioni generali][RetryGeneralGuidance] 

    Informazioni sui concetti generali per la gestione degli errori temporanei relativi.

- [Riprovare indicazioni specifiche del servizio][RetryServiceSpecificGuidance]

    Riepilogo delle caratteristiche di tentativi per molti dei servizi di Azure, incluse le informazioni che consentono di utilizzare, adattare o estendere il meccanismo di tentativi per tale servizio.

## <a name="scenario-guides"></a>Scenario Guide

- [Elasticsearch in esecuzione in Azure][elasticsearch] 
    
    Elasticsearch è un motore di ricerca Apri origine scalabilità e un database. È adatto per situazioni che richiedono il rilevamento delle informazioni contenute in grandi set di dati e analisi rapida. Questa guida esamina alcuni aspetti da considerare quando si progetta un cluster di Elasticsearch.

- [Gestione delle identità per le applicazioni multi-tenant][identity-multitenant] 
    
    Rappresentato è un'architettura in cui più tenant di condividere un'applicazione ma sono isolati da una a altra. Questa guida viene illustrato come gestire le identità utente in un'applicazione multi-tenant, con [Azure Active Directory] [ AzureAD] per gestire l'accesso e l'autenticazione.
    
- [Sviluppo di soluzioni di dati](https://msdn.microsoft.com/library/dn749874.aspx)

    Questa guida viene illustrato l'uso di HDInsight per gli scenari, ad esempio esplorazione iterativo, come un data warehouse per i processi ETL e integrazione con i sistemi di BI esistenti. Include anche informazioni su concetti di dati, pianificazione e le soluzioni di dati di progettazione e implementazione di queste soluzioni.
    
## <a name="patterns"></a>Motivi

- [Modelli di progettazione cloud: Architettura linee guida per le applicazioni Cloud](https://msdn.microsoft.com/library/dn568099.aspx)

    Modelli di progettazione cloud è una raccolta di modelli di progettazione e gli argomenti della Guida correlati. Articulates il vantaggio di applicare motivi mostrando come ogni parte può essere visualizzati in architetture di applicazioni cloud.
    
- [Ottimizzazione delle prestazioni per le applicazioni Cloud](https://github.com/mspnp/performance-optimization)

    Questa guida è un'esplorazione dei anti-modelli di comuni che impediscono l'app da proporzioni dei caratteri in condizioni di carico. Include esempi di otto motivi contro e una [Panoramica di analisi delle prestazioni](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e una Guida per [la valutazione delle prestazioni in metriche chiave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Architetture di riferimento

Il nostro architetture di riferimento sono disposti per scenario.
Ogni singolo architettura offre le procedure consigliate e procedure e un componente eseguibile che incorpora i suggerimenti.

La raccolta corrente di architetture di riferimento è disponibile in [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Linee guida sulla resilienza

Questi argomenti viene descritto come progettare applicazioni che sono guasti a in un ambiente distribuito cloud.   

- [Panoramica sulla resilienza][ResiliencyOvervew]

     Modalità di compilazione di applicazioni sulla piattaforma Azure che consentono di ripristinare gli errori delle e continuare a funzionare. Descrive un approccio strutturato a scopo di verifica, dalla progettazione alla implementazione, distribuzione e sulle operazioni.

- [Elenco di controllo sulla resilienza][resiliency-checklist]

    Un elenco di controllo di suggerimenti utili per pianificare una varietà di gestione degli errori che possono verificarsi.

- [Analisi modalità degli errori][resiliency-fma] 

    Analisi di modalità degli errori (FMA) sono un processo per la creazione di flessibilità in un sistema, identificando i punti di errore possibili. Come punto di partenza per il processo FMA, in questo articolo contiene un catalogo di gestione degli errori potenziali e le soluzioni di attenuazione. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

