<properties
   pageTitle="Versione di aggiornamento del sistema operativo nel Centro protezione di Azure | Microsoft Azure"
   description="In questo articolo viene illustrato come implementare il suggerimento di Azure Security Center **versione del sistema operativo di aggiornamento**."
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

# <a name="update-os-version-in-azure-security-center"></a>Aggiornare la versione del sistema operativo in Centro protezione di Azure

Per macchine () nei servizi cloud, Azure Security Center indicazioni aggiornare il sistema operativo (SO) se è disponibile una versione più recente.  Cloud solo servizi web e lavoro ruoli in fase di produzione vengono monitorate bande orarie.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. In e il **raccomandazioni** , selezionare **versione del sistema operativo di aggiornamento**.
![Aggiornare una versione del sistema operativo][1]

2. Verrà aperta e il **versione del sistema operativo di aggiornamento**. Seguire i passaggi descritti in questo blade per aggiornare la versione del sistema operativo.

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare il suggerimento Security Center "Versione dell'aggiornamento del sistema operativo". Per ulteriori informazioni sui servizi cloud e aggiornare la versione del sistema operativo per un servizio cloud, vedere:

- [Cenni preliminari sui servizi cloud](../cloud-services/cloud-services-choose-me.md)
- [Come aggiornare un servizio cloud](../cloud-services/cloud-services-update-azure-service.md)
- [Come configurare i servizi Cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
