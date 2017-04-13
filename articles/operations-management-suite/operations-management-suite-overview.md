<properties
   pageTitle="Panoramica di gestione famiglia di prodotti (OMS) operazioni | Microsoft Azure"
   description="Famiglia di prodotti (OMS, le operazioni di gestione Order) è basato sul cloud IT soluzione Microsoft che consente di gestire e proteggere le in locale e infrastruttura cloud.  In questo articolo sono riportati i diversi servizi inclusi in OMS e vengono forniti collegamenti in base al contenuto dettagliato."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>Quali sono le operazioni di gestione famiglia di prodotti (OMS)?

Famiglia di prodotti (OMS, le operazioni di gestione Order) è basato sul cloud IT soluzione Microsoft che consente di gestire e proteggere le in locale e infrastruttura cloud.  Poiché OMS viene implementato come un servizio basato su cloud, è possibile che in esecuzione rapidamente con un investimento minimo in servizi di infrastruttura.  Nuove caratteristiche vengono forniti automaticamente, evitando manutenzione ed eseguire l'aggiornamento dei costi.

Oltre a fornire servizi efficace autonomamente, è possibile integrare OMS con i componenti di System Center, ad esempio System Center Operations Manager per estendere investimenti gestione esistenti nel cloud.  System Center e OMS possono collaborare per fornire un ambiente di gestione dei ibrido completo.

Nelle sezioni seguenti vengono forniscono una descrizione di alto livello delle aree valore diverso della OMS e i servizi che assicura.  È possibile fare riferimento all'architettura OMS per una panoramica dei diversi componenti OMS prima di esaminare la documentazione per ognuno.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Analisi e Analitica](media/operations-management-suite-overview/icon-insight-analytics.png) Analisi e Analitica

[Registro Analitica](http://azure.microsoft.com/documentation/services/log-analytics) consente di raccogliere, correlare, eseguire una ricerca e agire sui dati di log e le prestazioni generati da sistemi operativi e applicazioni. Fornisce informazioni operative in tempo reale tramite ricerca integrata e dashboard personalizzati per analizzare rapidamente milioni di record per tutti i carichi di lavoro e i server indipendentemente dalla loro posizione fisica.

È possibile aggiungere facilmente soluzioni Analitica Log che definiscono dati da raccogliere e la logica per l'analisi.  Soluzioni possono includere funzionalità aggiuntiva che viene automaticamente inviata al agenti con minime o nessuna configurazione.  Oltre a utilizzare gli strumenti di analisi forniti da singole solutions, è possibile eseguire ricerche personalizzate tra l'intero set di dati per correlare i dati tra i sistemi e applicazioni.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automazione e controllo](media/operations-management-suite-overview/icon-automation-control.png) Automazione e controllo

Automazione Azure consente di automatizzare processi amministrativi con [runbook](../automation/automation-runbook-types.md) basata su PowerShell ed eseguiti nel cloud Azure.  Runbook possono accedere a ogni prodotto o servizio che può essere gestito con PowerShell incluse le risorse in altre aree, ad esempio servizi Web di Amazon (AWS).  Runbook possono essere eseguite anche in un server nel centro dati locali per gestire le risorse locali.

Automazione Azure fornisce gestione della configurazione con [PowerShell DSC](../automation/automation-dsc-overview.md).  È possibile creare e gestire le risorse DSC ospitate in Azure e applicarle ai sistemi locali e cloud di definire e applicare automaticamente la configurazione.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protezione e ripristino](media/operations-management-suite-overview/icon-protection-recovery.png) Protezione e ripristino di emergenza

[Eseguire il Backup Azure](http://azure.microsoft.com/documentation/services/backup) consente di proteggere i dati dell'applicazione e conserva per anni senza qualsiasi investimento di capitale e con costi operativi minimi.  È possibile eseguire il backup di dati dal server di Windows fisici e virtuali oltre carichi di lavoro di applicazione, ad esempio SQL Server e SharePoint.  Può inoltre essere utilizzato da System Center dati Protection Manager (DPM) per replicare dati protetti Azure per la ridondanza e archivio a lungo termine.

[Il ripristino del sito di Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribuisce alla continuità aziendale e una strategia di ripristino (BCDR) di emergenza da coordinazione replica, failover e ripristino di macchine virtuali di Hyper-V in locale, macchine virtuali VMware e fisico server di Windows o Linux. È possibile replicare computer a un centro dati secondario o estendere il nuovo centro di dati mediante la replica Azure. Il ripristino del sito offre failover semplice e ripristino per carichi di lavoro. Si integra con meccanismi di ripristino di emergenza, ad esempio SQL Server AlwaysOn e fornisce i piani di ripristino per failover semplice di carichi di lavoro a più livelli in più computer.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS sicurezza e conformità](media/operations-management-suite-overview/icon-security-compliance.png) Sicurezza e conformità
Sicurezza e conformità consente di identificare, valutare e ridurre i rischi di sicurezza per l'infrastruttura.  Queste caratteristiche di OMS sono implementate tramite più soluzioni in Analitica Log che analizzare i dati di log e la configurazione di systems agente per agevolare la protezione in corso del proprio ambiente.

- [Soluzione di sicurezza e controllo](oms-security-getting-started.md ) raccoglie e analizza gli eventi di protezione sui sistemi gestiti per identificare attività sospetto.
- [Soluzione Antimalware](log-analytics-malware.md ) report sullo stato della protezione antimalware sui sistemi gestiti.  
- Soluzione gli aggiornamenti del sistema esegue un'analisi degli aggiornamenti della protezione e altri aggiornamenti sui sistemi gestiti in modo che per identificare facilmente sistemi che richiedono patch.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [Log Analitica](http://azure.microsoft.com/documentation/services/log-analytics).
- Informazioni sulle [automazione Azure](../automation/automation-intro.md).
- Informazioni sui [Backup Azure](http://azure.microsoft.com/documentation/services/backup).
- Informazioni sui [siti Azure ripristino](http://azure.microsoft.com/documentation/services/site-recovery).
