<properties
   pageTitle="Aggiungere un firewall di generazione successivo in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare i suggerimenti di Centro protezione di Azure **Aggiungi un Firewall di generazione successivo** e **traffice Route tramite NGFW solo**."
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Aggiungere un Firewall di generazione successivo in Centro protezione di Azure

Centro protezione di Azure potrebbe è consigliabile aggiungere un firewall di generazione successivo (NGFW) da un partner Microsoft per aumentare le protezioni. In questo documento illustra un esempio di come eseguire questa operazione.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **raccomandazioni** , selezionare **Aggiungi un Firewall di generazione successiva**.
![Aggiungere un Firewall di generazione successivo][1]

2. In e il **componente un Firewall di generazione successivo** , selezionare un endpoint.
![Selezionare un endpoint][2]

3. Verrà visualizzata una seconda pala **Aggiungi un Firewall di generazione successiva** . È possibile scegliere di utilizzare una soluzione esistente, se disponibile oppure è possibile crearne uno nuovo. In questo esempio sono non disponibili le soluzioni esistenti in modo che verrà creato un nuovo NGFW.
![Creare nuovo Firewall generazione successiva][3]

4. Per creare un nuovo NGFW, selezionare una soluzione dall'elenco di partner integrate. In questo esempio abbiamo selezionare **Il punto di controllo**.
![Scegliere soluzione successiva generazione di un Firewall][4]

5. Verrà visualizzata la e il **Punto di controllo** , fornendo informazioni sulla soluzione partner. Selezionare **Crea** e l'informazioni.
![Blade informazioni firewall][5]

6. Verrà visualizzata e il **computer virtuale crea** . Immettere le informazioni necessarie per la riattivazione una macchine () che verrà eseguita la NGFW. Seguire i passaggi e fornire le informazioni NGFW obbligatorie. Fare clic su OK per applicare.
![Creare macchina virtuale per eseguire NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Instradare il traffico attraverso NGFW solo

Tornare a e il **consigli** . Dopo avere aggiunto una NGFW tramite Centro protezione, chiamato **instradare il traffico attraverso NGFW solo**è stata generata una nuova voce. Questo suggerimento viene creato solo se è stato installato il NGFW tramite Centro protezione. Se si dispone di endpoint esposto a Internet, Security Center indicazioni configurare regole gruppo di sicurezza di rete che impongono il traffico in ingresso per la macchina virtuale tramite il NGFW.

1. Nella finestra **blade raccomandazioni**, selezionare **instradare il traffico attraverso NGFW solo**.
![Instradare il traffico attraverso NGFW solo][7]

2. Verrà aperta e l' **indirizzare il traffico attraverso NGFW solo** in cui sono elencati macchine virtuali che è possibile inviare il traffico a. Selezionare una macchina virtuale dall'elenco.
![Selezionare una macchina virtuale][8]

3. Viene visualizzata una pala per la macchina virtuale selezionata correlate le regole in entrata. Una descrizione vengono fornite ulteriori informazioni sulle possibili passaggi successivi. Selezionare **Modifica regole in entrata** per procedere con la modifica di una regola in entrata. La previsione è che **origine** non è impostata su **qualsiasi** per gli endpoint esposto a Internet collegati con la NGFW. Per ulteriori informazioni sulle proprietà della regola in entrata, vedere [regole di NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurare le regole per limitare l'accesso][9]
![Modifica regola in entrata][10]

## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come implementare il suggerimento Security Center "Aggiungi un Firewall di generazione successivo". Per ulteriori informazioni su NGFWs e la soluzione partner punto di controllo, vedere gli articoli seguenti:

- [Nuova generazione Firewall](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Punto di controllo vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
