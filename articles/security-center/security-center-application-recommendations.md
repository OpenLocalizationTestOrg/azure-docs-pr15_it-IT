<properties
   pageTitle="Proteggere le applicazioni nel Centro protezione di Azure | Microsoft Azure"
   description="Gli indirizzi di documento consigli in Centro protezione di Azure che consentono di proteggono le applicazioni Azure e rimangono in conformità con criteri di sicurezza."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-applications-in-azure-security-center"></a>Proteggere le applicazioni nel Centro protezione di Azure

Centro protezione di Azure analizza lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli che guidano il processo di configurazione controlli necessari.  Procedure consigliate si applicano ai tipi di risorse Azure: macchine (), rete, SQL e le applicazioni.

Questo articolo riguarda consigli che si applicano alle applicazioni.  Applicazione consigli riguardano la distribuzione di un firewall di applicazione web.  Utilizzare la tabella seguente come riferimento identifichi i suggerimenti per la applicazioni disponibili e ciascuno di essi verrà cosa se si applica.

## <a name="available-application-recommendations"></a>Suggerimenti per la applicazioni disponibili

|Suggerimento|Descrizione|
|-----|-----|
|[Aggiungere un firewall di applicazione web](security-center-add-web-application-firewall.md)|Si consiglia di distribuire un firewall di applicazione web (WAF) per gli endpoint web. È possibile proteggere più applicazioni web nel Centro protezione mediante l'aggiunta di queste applicazioni per le distribuzioni WAF esistenti. Accessori WAF (creati tramite il modello di distribuzione di Manager delle risorse) devono essere distribuiti a una rete separata. Accessori WAF (creati tramite il modello di distribuzione classica) sono limitati all'uso di un gruppo di sicurezza di rete. Il supporto verrà esteso a una distribuzione completamente personalizzata di un dispositivo WAF (classica) in futuro.|
|[Finalizzare protezione dell'applicazione](security-center-add-web-application-firewall.md#finalize-application-protection)|Per completare la configurazione di un WAF, il traffico deve essere reindirizzato al accessorio WAF. Seguendo queste indicazioni completeranno le modifiche di installazione necessari.|

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sui consigli che si applicano ad altri tipi di risorse Azure, vedere gli articoli seguenti:

- [Protezione macchine virtuali nel Centro protezione di Azure](security-center-virtual-machine-recommendations.md)
- [Protezione alla rete aziendale in Centro protezione di Azure](security-center-network-recommendations.md)
- [Proteggere il servizio di SQL Azure in Centro protezione di Azure](security-center-sql-service-recommendations.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
