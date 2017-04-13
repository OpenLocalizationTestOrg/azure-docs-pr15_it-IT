<properties
   pageTitle="Aggiungere un firewall di applicazione web in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare i suggerimenti di Centro protezione di Azure **Aggiungi un firewall di applicazione web** e **Finalize applicazione protezione**."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Aggiungere un firewall di applicazione web in Centro protezione di Azure

Centro protezione di Azure potrebbe è consigliabile aggiungere un firewall di applicazione web (WAF) da un partner Microsoft per proteggere le applicazioni web. In questo documento illustra un esempio di come eseguire questa operazione.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **consigli** selezionare **sicura applicazione web che utilizza il firewall di applicazione web**.
![Applicazione web protetta][1]

2. In e **proteggere le applicazioni web usando il firewall di applicazione web** il selezionare un'applicazione web. Verrà visualizzata e il **componente un Firewall di applicazione Web** .
![Aggiungere un firewall di applicazione web][2]
3. È possibile scegliere di utilizzare un firewall di applicazione web esistente, se disponibile oppure è possibile crearne uno nuovo. In questo esempio sono non disponibili WAFs esistente in modo che verrà creato un nuovo WAF.

4. Per creare un nuovo WAF, selezionare una soluzione dall'elenco di partner integrate. In questo esempio abbiamo selezionare **Barracuda Firewall di applicazione Web**.
5. E il **Firewall di applicazione Web Barracuda** apre fornendo informazioni sulla soluzione partner. Selezionare **Crea** e l'informazioni.
![Blade informazioni firewall][3]

6. Verrà visualizzata e il **Nuovo Firewall di applicazione Web** in cui è possibile eseguire i passaggi di **Configurazione di macchine Virtuali** e fornire **Informazioni WAF**. Selezionare **la macchina virtuale**.

7. In e il **Macchine Virtuali configurazione** immettere le informazioni necessarie per il computer virtuale che verrà eseguita la WAF di selezione.
![Configurazione di macchine Virtuali][4]
8. Tornare a e il **Nuovo Firewall di applicazione Web** e selezionare **Informazioni WAF**. In e il **WAF informazioni** configurare WAF stesso. Passaggio 7 consente di configurare la macchina virtuale in cui verrà eseguita la WAF e passaggio 8 consente di eseguire il provisioning WAF stesso.

## <a name="finalize-application-protection"></a>Finalizzare protezione dell'applicazione

1. Tornare a e il **consigli** . Dopo aver creato WAF, denominata **Finalize applicazione protezione**è stata generata una nuova voce. Questa voce indica che è necessario completare il processo di collegare i WAF all'interno della rete virtuale Azure effettivamente in modo che è possibile proteggere l'applicazione.
![Finalizzare protezione dell'applicazione][5]

2. Selezionare **protezione dell'applicazione Finalize**. Verrà aperta una nuova pala. È possibile vedere che c'è un'applicazione web che deve avere il traffico reindirizzato.
3. Selezionare l'applicazione web. Viene visualizzata una pala che fornisce i passaggi per completare la configurazione di firewall applicazione web. Completare i passaggi e quindi selezionare **il traffico limitata**. Nel Centro sicurezza produrrà quindi cablaggio-up dell'utente.
![Limitare il traffico][6]

> [AZURE.NOTE] È possibile proteggere più applicazioni web nel Centro protezione mediante l'aggiunta di queste applicazioni per le distribuzioni WAF esistenti. Accessori WAF (creati tramite il modello di distribuzione di Manager delle risorse) devono essere distribuiti a una rete separata. Accessori WAF (creati tramite il modello di distribuzione classica) sono limitati all'uso di un gruppo di sicurezza di rete. Il supporto verrà esteso a una distribuzione completamente personalizzata di un dispositivo WAF (classica) in futuro. Ulteriori informazioni sui [classica e modelli di distribuzione di Manager delle risorse](../azure-classic-rm.md) per le risorse Azure.

I registri di tale WAF ora sono totalmente integrati. Centro protezione è possibile avviare automaticamente la raccolta e analizzare i log in modo che è possibile fornire l'interfaccia utente gli avvisi di sicurezza importanti per l'utente.

## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come implementare il suggerimento Security Center "Aggiungi un'applicazione web". Per ulteriori informazioni sulla configurazione di un firewall di applicazione web, vedere gli articoli seguenti:

- [Configurazione di un Firewall di applicazione Web (WAF) per l'ambiente del servizio di App](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
