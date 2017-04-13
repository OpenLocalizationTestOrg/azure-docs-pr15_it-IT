<properties
   pageTitle="Risolvere gli avvisi di integrità di protezione endpoint in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **gli avvisi di integrità risolvere Endpoint Protection**."
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Risolvere gli avvisi di integrità di protezione endpoint in Centro protezione di Azure

Centro protezione di Azure indicazioni risolvere rilevato endpoint protection integrità avvisi.  Centro protezione consente di vedere quali macchine () all'endpoint protection errori e il numero di errori.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio. Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. In **blade raccomandazioni**, selezionare **Risolvi Endpoint Protection integrità avvisi**.
![Risolvere gli avvisi di integrità di endpoint protection][1]

2. Verrà aperta e il **Endpoint Protection errore** in cui sono elencati macchine virtuali con errori e il numero di errori per ogni macchina virtuale. Selezionare una macchina virtuale dall'elenco.
![Errore di protezione endpoint][2]

3. Blade **Elenco errori** viene aperto per la macchina virtuale selezionata, la visualizzazione di un elenco di errori. Selezionare un errore nell'elenco per altre informazioni. Verrà aperta una pala con informazioni sull'errore selezionato.
![Elenco di errori][3]
  ![evento di errore][4]

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
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
