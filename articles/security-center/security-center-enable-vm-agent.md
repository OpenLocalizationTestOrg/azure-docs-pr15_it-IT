<properties
   pageTitle="Abilitare l'agente di macchine Virtuali nel Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **Abilitare agente macchine Virtuali**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Abilitare l'agente di macchine Virtuali nel Centro protezione di Azure

Agente di macchine Virtuali deve essere installato macchine () nell'ordine per [consentire la raccolta di dati](security-center-enable-data-collection.md).  Centro protezione di Azure consente di vedere quali macchine virtuali richiedono l'agente di macchine Virtuali e consiglia di abilitare l'agente di macchine Virtuali su tali macchine virtuali.

Agente di macchine Virtuali è installato per impostazione predefinita per le macchine virtuali distribuiti da Azure Marketplace. L'articolo [agente macchine Virtuali ed estensioni-parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine Virtuali.


> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio. Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra **blade raccomandazioni**, selezionare **Abilita agente macchine Virtuali**.
![Attivare l'agente di macchine Virtuali][1]

2. Verrà aperta e il **Macchine Virtuali agente mancanti o non risponde**. Questo blade Elenca le macchine virtuali che richiedono l'agente di macchine Virtuali. Seguire le istruzioni e il per installare l'agente di macchine Virtuali.
![Agente di macchine Virtuali mancante][2]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md)- informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md)- informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md)- informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md)-- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md)- trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/)- ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
