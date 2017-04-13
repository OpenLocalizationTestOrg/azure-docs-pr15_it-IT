<properties
   pageTitle="Installare Endpoint Protection in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **Installare Endpoint Protection**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Installare Endpoint Protection in Centro protezione di Azure

Centro protezione di Azure indicazioni provisioning un'applicazione di antimalware per il Azure macchine () se antimalware non è già stato attivato. Questa indicazione si applica solo a macchine virtuali di Windows.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Selezionare **Installa Endpoint Protection**e il **consigli** .
![Selezionare Installa Endpoint Protection][1]

2. E **l'Installare Endpoint Protection** verrà visualizzata con un elenco di macchine virtuali senza antimalware abilitato. Selezionare nell'elenco macchine virtuali che si desidera installare antimalware in e fare clic su **Installa in macchine virtuali**.
![Selezionare macchine virtuali di installare antimalware in][2]

3. Verrà visualizzata e il **Selezionare Endpoint Protection** che consente di selezionare la soluzione antimalware che si desidera utilizzare. In questo esempio, se si seleziona **Microsoft Antimalware**.
![Selezionare l'Endpoint Protection][3]

4. Ulteriori informazioni sulla soluzione antimalware. Selezionare **Crea**.
![Creare una soluzione antimalware][4]

5. Immettere le impostazioni di configurazione necessari e **l'Estensione aggiungere** e fare clic su **OK**. Per ulteriori informazioni sulle impostazioni di configurazione, vedere [predefiniti e personalizzati Antimalware configurazione](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../azure-security-antimalware.md) è ora attivo in macchine virtuali selezionate.

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare il suggerimento Security Center "Installa Endpoint Protection." Per ulteriori informazioni sull'attivazione di un programma antimalware in Azure, vedere gli articoli seguenti:

- [Microsoft Antimalware per i servizi Cloud e macchine virtuali](../azure-security-antimalware.md) - informazioni su come distribuire Microsoft antimalware.

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
