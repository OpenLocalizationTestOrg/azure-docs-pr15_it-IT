<properties
   pageTitle="Protezione macchine virtuali nel Centro protezione di Azure | Microsoft Azure"
   description="Gli indirizzi di documento consigli in Centro protezione di Azure che consentono di proteggere le macchine virtuali e rimangono in conformità con criteri di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Protezione macchine virtuali nel Centro protezione di Azure

Centro protezione di Azure analizza lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli che guidano il processo di configurazione controlli necessari.  Procedure consigliate si applicano ai tipi di risorse Azure: macchine (), rete, SQL e le applicazioni.

Questo articolo riguarda consigli che si applicano alle macchine virtuali.  Raccolta di dati, applicare gli aggiornamenti di sistema, il provisioning antimalware, riguardano consigli macchine Virtuali crittografia dischi macchine Virtuali e altro ancora.  Utilizzare la tabella seguente come riferimento identifichi i consigli macchine Virtuali disponibili e ciascuno di essi verrà cosa se si applica.

## <a name="available-vm-recommendations"></a>Consigli macchine Virtuali disponibili

|Suggerimento|Descrizione|
|-----|-----|
|[Abilitare la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md)|Si consiglia di attivare la raccolta di dati nei criteri di sicurezza per ciascuna delle sottoscrizioni e tutte le macchine () le sottoscrizioni.|
|[Correggere le vulnerabilità del sistema operativo](security-center-remediate-os-vulnerabilities.md)|Si consiglia di allineare le configurazioni OS con le regole di configurazione consigliata, ad esempio non salvataggio password.|
|[Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md)|Si consiglia di distribuire aggiornamenti critici e protezione di sistema mancanti in macchine virtuali.|
|[Riavviare il computer dopo l'aggiornamento del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Si consiglia riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema.|
|[Installare Endpoint Protection](security-center-install-endpoint-protection.md)|Si consiglia di effettuare il provisioning programmi antimalware macchine virtuali (solo macchine virtuali Windows).|
|[Risolvere gli avvisi di integrità Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Si consiglia di risolvere gli errori di protezione endpoint.|
|[Attivare l'agente di macchine Virtuali](security-center-enable-vm-agent.md)|Consente di visualizzare che richiedono macchine virtuali agente di macchine Virtuali. Per eseguire questa procedura patch analisi previsione analisi e programmi antimalware, è necessario installare l'agente di macchine Virtuali in macchine virtuali. Agente di macchine Virtuali è installato per impostazione predefinita per le macchine virtuali distribuiti da Azure Marketplace. L'articolo [agente macchine Virtuali ed estensioni-parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine Virtuali.|
| [Applicare la crittografia del disco](security-center-apply-disk-encryption.md) |Si consiglia di crittografare il disco di macchine Virtuali utilizzando la crittografia del Azure (Windows e macchine virtuali Linux). La crittografia è consigliata per i volumi sistema operativo e dati di una macchina virtuale.|
| [Aggiornare una versione del sistema operativo](security-center-update-os-version.md) | Si consiglia di aggiornare la versione del sistema operativo (SO) per il servizio Cloud per la versione più recente disponibile per la famiglia di sistemi.  Per ulteriori informazioni sui servizi Cloud, vedere [Panoramica di servizi Cloud](../cloud-services/cloud-services-choose-me.md). |
| [Valutazione delle vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) | Consiglia di installare una soluzione di valutazione di una vulnerabilità in di una macchina virtuale. |
| [Correggere vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Consente di vedere rilevata dalla soluzione di valutazione di una vulnerabilità installata le macchine Virtuali vulnerabilità delle applicazioni e sistema. |

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sui consigli che si applicano ad altri tipi di risorse Azure, vedere gli articoli seguenti:

- [Proteggere le applicazioni nel Centro protezione di Azure](security-center-application-recommendations.md)
- [Protezione alla rete aziendale in Centro protezione di Azure](security-center-network-recommendations.md)
- [Proteggere il servizio di SQL Azure in Centro protezione di Azure](security-center-sql-service-recommendations.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
