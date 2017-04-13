<properties
   pageTitle="Introduzione all'integrazione di Microsoft Azure log | Microsoft Azure"
   description="Informazioni sull'integrazione di Azure log, le relative funzionalità chiave e sul suo funzionamento."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introduzione all'integrazione di Microsoft Azure log (Preview)

Informazioni sull'integrazione di Azure log, le relative funzionalità chiave e sul suo funzionamento.

## <a name="overview"></a>Panoramica

Piattaforma come modello PaaS e infrastruttura come servizio (IaaS) ospitato in Azure generare una grande quantità di dati nei registri protezione. Questi log contengono informazioni importanti che consentono di business intelligence e potenti approfondite violazioni, minacce interne ed esterne, problemi di conformità alle normative e alterazioni di rete, host e attività dell'utente.

Integrazione di Azure log consente di integrare i registri non elaborati dalle risorse Azure i sistemi di informazioni sulla protezione e gestione di evento (SIEM) locale. Integrazione di Azure log raccoglie Azure diagnostica di Windows *(tampone)* macchine virtuali, nonché diagnostica da soluzioni dei partner, ad esempio un Firewall di applicazione Web (WAF). Questa integrazione fornisce un dashboard unificato per tutte le attività, in locale o nel cloud, in modo da aggregare, correlare, analizzare e un avviso per gli eventi di protezione.

![Integrazione di Azure log][1]

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?

Azure produce registrazione completa per ogni servizio Azure. Questi registri sono categorizzati per due tipi principali:

- **Log di controllo/Gestione**, che forniscono visibilità operazioni creare Manager delle risorse Azure, aggiornamento ed eliminazione. Azure i log di controllo è illustrato un esempio di questo tipo di log.
- **Registri piano dati**, che forniscono visibilità gli eventi generati come parte dell'utilizzo di una risorsa Azure. Esempi di questo tipo di log sono eventi Windows sistema, la sicurezza e registri applicazione in una macchina virtuale.

Integrazione di Azure log supporta attualmente l'integrazione del log di controllo di Azure, i registri macchina virtuale e gli avvisi di Centro protezione di Azure.

Se hai domande sull'integrazione di Log di Azure, inviare un messaggio di posta elettronica a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati introdotti all'integrazione di Azure log. Per altre informazioni sull'integrazione di Azure log e i tipi di registri supportati, vedere gli articoli seguenti:

- [Integrazione di Microsoft Azure Log per i registri Azure (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) -download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione sull'integrazione di Azure log.
- [Guida introduttiva a integrazione log Azure](security-azure-log-integration-get-started.md) : in questa esercitazione viene illustrata l'installazione dell'integrazione di Azure log e integrazione dei registri da Azure dello spazio di archiviazione, i log di controllo Azure e gli avvisi di Centro protezione.
- [Passaggi di configurazione per partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : questo post di blog viene illustrato come configurare l'integrazione di Azure log per l'uso con le soluzioni partner Splunk ArcSight HP e IBM QRadar.
- [Registro azure integrazione domande frequenti domande frequenti](security-azure-log-integration-faq.md) - domande frequenti su questo fornisce le risposte alle domande sull'integrazione di Azure log.
- [Gli avvisi di Centro protezione di integrazione con Azure accedere integrazione](../security-center/security-center-integrating-alerts-with-log-integration.md) : il documento viene illustrato come sincronizzare gli avvisi di Centro protezione, insieme agli eventi di sicurezza macchina virtuale raccolti da Azure diagnostica e i log di controllo Azure, con i log analitica o soluzione SIEM.
- [Nuove funzionalità di diagnostica Windows Azure e i log di controllo Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : questo post di blog viene presentato il log di controllo di Azure e altre caratteristiche che consentono di ottengono informazioni approfondite nelle operazioni delle risorse di Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
