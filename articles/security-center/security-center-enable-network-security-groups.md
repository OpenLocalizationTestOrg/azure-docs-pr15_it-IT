<properties
   pageTitle="Abilitare i gruppi di sicurezza di rete in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **Abilitare gruppi di sicurezza di rete**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-network-security-groups-in-azure-security-center"></a>Abilitare i gruppi di sicurezza di rete in Centro protezione di Azure

Centro protezione di Azure indicazioni abilitare un gruppo di sicurezza di rete (NSG) se non è già stato attivato. NSGs contiene un elenco delle regole di controllo elenco di accesso che consentire o meno il traffico di rete per le istanze di macchine Virtuali in una rete virtuale. NSGs può essere associato subnet o singole istanze di macchine Virtuali all'interno di tale subnet. Quando un NSG è associata a una subnet, vengono applicate le regole ACL a tutte le istanze di macchine Virtuali di tale subnet. Inoltre, è possibile limitare il traffico verso un macchine Virtuali singoli ulteriormente associando un NSG direttamente a quella macchina virtuale. Per informazioni su altre, vedere [che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se non si dispone NSGs abilitato, Security Center presenterà due suggerimenti per l'utente: abilitare dei gruppi di sicurezza di rete subnet e abilitare dei gruppi di sicurezza di rete in macchine virtuali. Scegliere il livello, subnet o macchine Virtuali, applicare NSGs.


> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella e **raccomandazioni** , selezionare **Abilita gruppi di sicurezza di rete** in subnet o in macchine virtuali.
![Abilitare i gruppi di sicurezza di rete][1]

2. Verrà aperta e il **Configurare gruppi di sicurezza mancanti di rete** per subnet o per macchine virtuali, a seconda di recommendation selezionata. Selezionare una subnet o una macchina virtuale per configurare un NSG su.

  ![Configurare NSG per subnet][2]

  ![Configurare NSG per macchine Virtuali][3]
3. Selezionare un NSG esistente e il **gruppo di sicurezza di rete scegliere** o selezionare questa opzione per creare un nuovo NSG.

  ![Scegliere il gruppo di sicurezza di rete][4]

Se si crea un nuovo NSG, seguire la procedura descritta in [come gestire NSGs tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) per creare un NSG e impostare le regole di protezione.

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare il suggerimento Security Center "Abilitare di gruppi di sicurezza di rete" per subnet o macchine virtuali. Per ulteriori informazioni sull'attivazione NSGs, vedere gli articoli seguenti:

- [Che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Come gestire NSGs tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
