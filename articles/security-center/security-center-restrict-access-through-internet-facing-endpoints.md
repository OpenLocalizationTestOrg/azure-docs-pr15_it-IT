<properties
   pageTitle="Limitare l'accesso tramite i punti finali esposto a Internet in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **limitare l'accesso tramite endpoint esposto a Internet**."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Limitare l'accesso tramite i punti finali esposto a Internet in Centro protezione di Azure

Centro protezione di Azure indicazioni limitare l'accesso tramite i punti finali esposto a Internet se uno qualsiasi dei gruppi di sicurezza di rete (NSGs) contiene uno o più regole in entrata che consentono l'accesso da "qualsiasi" indirizzo IP di origine. Apertura di accesso a "uno" può consentire a utenti malintenzionati accedere alle risorse di. Centro protezione consiglia di modificare le regole in entrata per limitare l'accesso a indirizzi IP di origine che in realtà devono accedere.

Per qualsiasi porta web non con "uno" come origine, viene generato questo suggerimento.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio. Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. In **blade raccomandazioni**, selezionare **limitare l'accesso tramite endpoint esposto a Internet**.
![Limitare l'accesso tramite Internet affiancate endpoint][1]

2. Verrà aperta e il **limitare l'accesso tramite endpoint esposto a Internet**. Questo blade Elenca le macchine () con le regole in entrata che creano un potenziale problema di protezione. Selezionare una macchina virtuale.
![Selezionare una macchina virtuale][2]

3. Consente di visualizzare e il **NSG** gruppo di sicurezza di rete informazioni correlate le regole in entrata e la macchina virtuale associata. Selezionare **Modifica regole in entrata** per procedere con la modifica di una regola in entrata.
![Blade gruppo di sicurezza di rete][3]

4. Scegliere la regola in ingresso per modificare e il **regole di protezione in ingresso** . In questo esempio, se si seleziona **AllowWeb**.
![Regole di protezione in ingresso][4]

  Nota è inoltre possibile selezionare **le regole predefinite** per visualizzare il set di regole predefiniti contenuti NSGs tutti. Non è possibile eliminare le regole predefinite ma, perché sono state assegnate una priorità più bassa, possono essere sostituite da regole create. Ulteriori informazioni sulle [regole predefinite](../virtual-network/virtual-networks-nsg.md#default-rules).
![Regole predefinite][5]

5. Nella e **AllowWeb** modifica le proprietà della regola in entrata, in modo che l' **origine** è un indirizzo IP o il blocco di indirizzi IP. Per ulteriori informazioni sulle proprietà della regola in entrata, vedere [regole di NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Modifica regola in entrata][6]

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare il suggerimento Security Center "Limitare l'accesso tramite endpoint esposto a Internet". Per ulteriori informazioni sull'attivazione NSGs e le regole, vedere gli articoli seguenti:

- [Che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Come gestire NSGs tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md)- informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md)- informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md)- informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md)-- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md)- trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/)- ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
