<properties
   pageTitle="Centro protezione di Azure e Azure macchine virtuali | Microsoft Azure"
   description="In questo documento aiuta a capire come Azure Security Center possono proteggere il macchine virtuali di Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Centro protezione di Azure e macchine virtuali di Azure

[Centro protezione di Azure](https://azure.microsoft.com/services/security-center/) consente di impedire, rilevare e rispondere a rischi. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Questo articolo descrive come Security Center consente di proteggere il macchine virtuali di Azure (macchine Virtuali).

## <a name="why-use-security-center"></a>Perché usare Security Center?

Centro protezione consente di proteggere i dati di macchina virtuale in Azure fornendo visibilità delle impostazioni di protezione del computer virtuale. Quando nel Centro sicurezza protegge nelle macchine virtuali, le seguenti funzionalità saranno disponibili:

- Impostazioni di sicurezza del sistema operativo (OS) con le regole di configurazione consigliata
- Protezione di sistema e aggiornamenti critici mancanti
- Consigli di protezione endpoint
- Convalida di crittografia disco
- Monitoraggio e la valutazione delle vulnerabilità
- Individuazione

Oltre a proteggere il macchine virtuali di Azure, Security Center vengono forniti anche il monitoraggio della protezione e gestione per i servizi Cloud, servizi di App, reti virtuali e altro ancora. 

>[AZURE.NOTE] Vedere [Introduzione al Centro protezione di Azure](security-center-intro.md) per altre informazioni su Centro protezione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare con Azure Security Center, è necessario conoscere e tenere presente quanto segue:

- È necessario disporre di un abbonamento a Microsoft Azure. Per ulteriori informazioni sui livelli di gratuita e standard del Centro protezione, vedere [Prezzi Centro protezione](https://azure.microsoft.com/pricing/details/security-center/) .
- Pianificare l'adozione di Centro protezione, vedere [pianificazione di Azure nel Centro sicurezza e le operazioni di Guida](security-center-planning-and-operations-guide.md) per ulteriori informazioni sull'utilizzo di pianificazione e le operazioni.
- Per informazioni sul supporto di sistema operativo, vedere [Domande frequenti domande frequenti Azure Security Center](security-center-faq.md). 

## <a name="set-security-policy"></a>Set di criteri di sicurezza

Raccolta di dati deve essere abilitato in modo tale Azure Security Center è possibile raccogliere le informazioni necessarie per fornire consigli e gli avvisi generati in base a criteri di sicurezza configurate. Nella figura riportata di seguito, è possibile vedere che **la raccolta di dati** è stata **attivata**.

Criteri di sicurezza definisce il set di controlli consigliata per le risorse all'interno della sottoscrizione specificata o un gruppo di risorse. Prima di attivare i criteri di sicurezza, è necessario disporre di raccolta dati abilitata, Security Center raccoglie i dati da macchine virtuali per valutare lo stato di protezione, fornire suggerimenti per la sicurezza e un avviso a rischi. In Centro protezione, è possibile definire criteri per le sottoscrizioni Azure o gruppi di risorse in base alle esigenze di protezione della società e il tipo di applicazioni o riservatezza del contenuto dei dati di ogni abbonamento. 

![Criteri di sicurezza](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Per ulteriori informazioni su ogni **criteri di prevenzione della** disponibili, vedere [impostare i criteri di sicurezza](security-center-policies.md) articolo.

## <a name="manage-security-recommendations"></a>Gestire i suggerimenti per la sicurezza

Nel Centro sicurezza analizza lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli. I suggerimenti guidano il processo di configurazione controlli necessari.

Dopo aver impostato un criterio di protezione, Security Center analizza lo stato di protezione delle risorse per identificare i potenziali vulnerabilità. I suggerimenti vengono visualizzati in un formato di tabella in cui ogni riga rappresenta un suggerimento particolare. Nella tabella seguente vengono forniti alcuni esempi di suggerimenti per macchine virtuali di Azure e ciascuno di essi verrà cosa se si applica. Quando si seleziona un suggerimento, vengono fornite informazioni che illustra come implementare il suggerimento nel Centro protezione.

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
| [Valutazione delle vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) | Consiglia di installare una soluzione di valutazione di una vulnerabilità in di una macchina virtuale. |
| [Correggere vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Consente di vedere rilevata dalla soluzione di valutazione di una vulnerabilità installata le macchine Virtuali vulnerabilità delle applicazioni e sistema. |

>[AZURE.NOTE] Per ulteriori informazioni sui consigli, vedere l'articolo [suggerimenti per la sicurezza di gestione](security-center-recommendations.md) .

## <a name="monitor-security-health"></a>Monitorare l'integrità del titolo

Dopo aver attivato [criteri di sicurezza](security-center-policies.md) per le risorse dell'abbonamento, Centro protezione verranno analizzare la protezione delle risorse per identificare i potenziali vulnerabilità.  È possibile visualizzare lo stato di protezione delle risorse, insieme a eventuali problemi e **dell'integrità di protezione delle risorse** . Quando si fa clic su **macchine virtuali** nella sezione integrità **protezione delle risorse** , verrà aperto e il **macchine virtuali** con suggerimenti per le macchine virtuali. 

![Integrità di sicurezza](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gestire e risposta agli avvisi di sicurezza

Nel Centro sicurezza automaticamente raccoglie, analizza e si integra dati log le risorse Azure, della rete e le soluzioni partner connessi (ad esempio firewall ed endpoint protection soluzioni), per rilevare minacce effettive e ridurre i falsi positivi. Sfruttando un'aggregazione diverso delle [funzionalità di rilevamento](security-center-detection-capabilities.md)di Centro protezione è possibile generare gli avvisi di sicurezza priorità per esaminare il problema e fornire consigli su come risolvere i possibili attacchi rapidamente.

![Avvisi di sicurezza](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Selezionare un avviso di sicurezza per ulteriori informazioni su eventi che lo ha attivato l'avviso e cosa, se presente, è necessario eseguire per risolvere un attacco operazioni. Gli avvisi di sicurezza sono raggruppati per [tipo](security-center-alerts-type.md) e la data.


## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
