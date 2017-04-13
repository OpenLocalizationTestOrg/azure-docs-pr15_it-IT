<properties
   pageTitle="Protezione alla rete aziendale in Centro protezione di Azure | Microsoft Azure"
   description="Gli indirizzi di documento consigli in Centro protezione di Azure che consentono di proteggere la rete Azure e rimangono in conformità con criteri di sicurezza."
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

# <a name="protecting-your-network-in-azure-security-center"></a>Protezione alla rete aziendale in Centro protezione di Azure

Centro protezione di Azure analizza lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli che guidano il processo di configurazione controlli necessari.  Procedure consigliate si applicano ai tipi di risorse Azure: macchine (), rete, SQL e le applicazioni.

Questo articolo riguarda suggerimenti relativi alla rete.  Rete consigli riguardano firewall generazione successivo, gruppi di sicurezza di rete, configurare le regole di traffico in ingresso e altro.  Utilizzare la tabella seguente come riferimento identifichi i suggerimenti di rete disponibili e ciascuno di essi verrà cosa se si applica.

## <a name="available-network-recommendations"></a>Suggerimenti per reti disponibili

|Suggerimento|Descrizione|
|-----|-----|
|[Aggiungere un Firewall di generazione successivo](security-center-add-next-generation-firewall.md)|Si consiglia di aggiungere un Firewall di generazione successivo (NGFW) da un partner Microsoft per aumentare le protezioni.|
|[Instradare il traffico attraverso NGFW solo](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Si consiglia di configurare regole di gruppo (NSG) sicurezza di rete che forzare il traffico in ingresso per la macchina virtuale tramite il NGFW.|
|[Abilitare i gruppi di sicurezza di rete in subnet o macchine virtuali](security-center-enable-network-security-groups.md)|Si consiglia di abilitare NSGs in subnet o macchine virtuali.|
|[Limitare l'accesso tramite Internet affiancate endpoint](security-center-restrict-access-through-internet-facing-endpoints.md)|Si consiglia di configurare le regole di traffico in ingresso per NSGs.|

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sui consigli che si applicano ad altri tipi di risorse Azure, vedere gli articoli seguenti:

- [Protezione macchine virtuali nel Centro protezione di Azure](security-center-virtual-machine-recommendations.md)
- [Proteggere le applicazioni nel Centro protezione di Azure](security-center-application-recommendations.md)
- [Proteggere il servizio di SQL Azure in Centro protezione di Azure](security-center-sql-service-recommendations.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
