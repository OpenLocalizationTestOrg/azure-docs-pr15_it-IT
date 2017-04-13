<properties
   pageTitle="Indice indicazioni tecniche sulla resilienza | Microsoft Azure"
   description="Indice di articoli tecnici su informazioni e progettazione flessibile, disponibilità, applicazioni con tolleranza, nonché strategia di emergenza ripristino e continuità aziendale"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Indicazioni tecniche sulla resilienza Azure

##<a name="introduction"></a>Introduzione

Riunione disponibilità e ai requisiti di ripristino di emergenza richiede due tipi di conoscenze:

- Tecnica approfondita delle funzionalità della piattaforma cloud
- Conoscere sulla progettazione di un servizio distribuito correttamente

Questa serie di articoli è descritta la prima: la funzionalità e limitazioni della piattaforma Azure per quanto riguarda adattabilità (a volte chiamato continuità aziendale). Se si è interessati in quest'ultimo, vedere la serie di articolo incentrata sulla [disponibilità per applicazioni Azure e il ripristino di emergenza](https://aka.ms/drtechguide). Sebbene questa serie di articoli tocca sui modelli di progetto e architettura, che non lo stato attivo della serie. Per indicazioni per la progettazione, è possibile consultare il materiale nella sezione [risorse aggiuntive](#additional-resources) .

Le informazioni sono organizzate negli articoli seguenti:

- [Ripristino da errori locali](resiliency-technical-guidance-recovery-local-failures.md).
Può avere esito negativo hardware fisico (ad esempio, unità, server e dispositivi di rete). Le risorse possono esaurite quando picchi di carico. Questo articolo descrive le funzionalità offerte da Azure per mantenere disponibilità in queste condizioni.

- [Ripristino di un'interruzione di Azure del servizio a livello di area geografica](resiliency-technical-guidance-recovery-loss-azure-region.md).
Errori diffusi sono rari, ma sono teorico possibili. Aree intere possono diventare isolate a causa di errori di rete o può essere danneggiati fisico di emergenza naturale. In questo articolo viene spiegato come utilizzare Azure per creare applicazioni che includono aree geografiche diverse aree geografiche.

- [Ripristino locali in Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Cloud modifica sensibilmente economico di emergenza, che consente alle organizzazioni di utilizzare Azure per stabilire un secondo sito per il ripristino. È possibile eseguire questa operazione una frazione del costo di creazione e gestione di un Data Center secondario. In questo articolo vengono illustrate le funzionalità offerte da Azure per estendere un data center locale nel cloud.

- [Ripristino di danneggiamento dei dati o eliminazioni accidentali](resiliency-technical-guidance-recovery-data-corruption.md).
Le applicazioni possono avere bug tali dati danneggiati. Operatori in modo non corretto possono eliminare dati importanti. Questo articolo spiega cosa Azure sono disponibili per il backup dei dati e il ripristino a un punto precedente l'ora.

##<a name="additional-resources"></a>Risorse aggiuntive

- [Ripristino di emergenza e disponibilità per applicazioni basate su Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
In questo articolo è una panoramica dettagliata di disponibilità e ripristino. Coprire la verifica della replica manuale per riferimento e dati delle transazioni. Le sezioni finali forniscono riepiloghi dei diversi tipi di topologie di ripristino di emergenza che si estendono su Azure aree per il massimo livello di disponibilità.

- [Elenco di controllo di disponibilità](resiliency-high-availability-checklist.md).
In questo articolo è riportato un elenco di caratteristiche, servizi, e che consentono di aumentano l'adattabilità e la disponibilità dell'applicazione.

- [Guida di Microsoft Azure servizio adattabilità](resiliency-service-guidance-index.md).
In questo articolo è un indice di servizi di Azure e vengono forniti collegamenti a istruzioni di ripristino di emergenza e indicazioni per la progettazione.

- [Panoramica: Cloud business continuità e database di emergenza Database SQL di](../sql-database/sql-database-business-continuity.md).
In questo articolo fornisce le tecniche di Database SQL Azure per disponibilità. Centra principalmente sulle strategie di backup e ripristino. Se si utilizza il Database di SQL Azure nel servizio cloud, è consigliabile esaminare questo documento e le relative risorse correlate.

- [Disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
In questo articolo vengono illustrate le opzioni di disponibilità che potrebbero essere utili quando si utilizza infrastruttura come servizio (IaaS) per ospitare i servizi di database. Vengono illustrate di disponibilità AlwaysOn, che rispecchiano database, la distribuzione dei log e backup e ripristino. Varie esercitazioni viene illustrato come utilizzare queste tecniche.

- [Procedure consigliate per la progettazione di grandi dimensioni pagina servizi nel servizi Cloud Windows Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
In questo articolo è incentrato sullo sviluppo di architetture cloud scalabilità. Molte delle tecniche che utilizzano per migliorare la scalabilità per migliorare la disponibilità. Inoltre, se l'applicazione non è possibile scalare in condizioni di carico maggiore, è necessario scalabilità diventa un problema di disponibilità.

- [Eseguire il backup e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
In questo articolo fornisce indicazioni tecniche su come eseguire il backup e ripristino di Microsoft SQL Server in macchine virtuali di Microsoft Azure in esecuzione.

- [Sicura: indicazioni per architetture cloud flessibili](https://channel9.msdn.com/Series/FailSafe).
In questo articolo vengono fornite indicazioni per la creazione di architetture cloud flessibili, indicazioni per l'implementazione di tali architetture tecnologie Microsoft e ricette per l'implementazione di tali architetture per scenari specifici.

- [Case study tecnico: utilizzo delle tecnologie cloud per migliorare il ripristino di emergenza](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Questo case study viene illustrato come Microsoft IT Azure per migliorare il ripristino di emergenza.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie basata su indicazioni tecniche per adattabilità Azure. Se si è interessati in altri articoli in questa serie di lettura, è possibile iniziare con [il ripristino da errori locali](resiliency-technical-guidance-recovery-local-failures.md).
