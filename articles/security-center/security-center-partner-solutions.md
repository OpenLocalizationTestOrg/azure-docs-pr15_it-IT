<properties
   pageTitle="Gestione di soluzioni dei partner nel Centro protezione di Azure | Microsoft Azure"
   description="In questo documento illustra come Azure Security Center considerare monitor a colpo d'occhio lo stato di integrità delle soluzioni partner integrato con l'abbonamento Azure."
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

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitoraggio delle soluzioni dei partner con Azure Security Center

In questo documento illustra come controllare lo stato di integrità delle soluzioni partner nel Centro protezione di Azure.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio. Questa non è una Guida dettagliata.

## <a name="monitoring-partner-solutions"></a>Monitorare le soluzioni partner

Sezione **soluzioni dei Partner** nel e **Centro protezione** consente di monitor a colpo d'occhio lo stato di integrità delle soluzioni partner integrato con l'abbonamento Azure.
![Affiancare le soluzioni partner][1]

Il riquadro di **soluzioni dei Partner** consente di visualizzare il numero di soluzioni dei partner e un riepilogo di queste soluzioni di stato.

Lo **stato** di una soluzione partner può essere:

- Protette (verde): non c'è alcun problema di integrità.
- Danneggiamento (rosso) - esiste un problema di integrità che richiede attenzione immediata.
- Arrestato reporting (arancione) - la soluzione ha smesso di creazione di report la propria integrità.
- Lo stato di protezione sconosciuto (arancione) - lo stato della soluzione è sconosciuto al momento a causa di un processo non riuscito di aggiunta di una nuova risorsa alla soluzione esistente.
- Non segnalati (grigio), la soluzione non segnala nulla ancora, lo stato della soluzione potrebbe essere segnalato se è stato appena connesso e viene effettuata la distribuzione.

Se non esistono soluzioni integrate con l'abbonamento verrà provincia sul riquadro che non esistono soluzioni. Selezionare il riquadro di **soluzioni dei Partner** modo sarà possibile aprire e il **consigli** per distribuire soluzioni di sicurezza dei partner.
![Senza le soluzioni partner][2]

Per visualizzare lo stato delle soluzioni partner:

1. Selezionare il riquadro di **soluzioni dei Partner** . Viene visualizzata una pala un elenco delle soluzioni partner connessi al Centro protezione.
![Soluzioni partner][3]

2. Selezionare una soluzione partner. In questo esempio, consente di selezionare la soluzione **WAF2 F5** .  Blade comparirà che lo stato della soluzione partner e la soluzione le risorse associate. Selezionare **console di soluzioni** per aprire l'ambiente di gestione dei partner per questa soluzione.
![Dettagli soluzioni partner][4]

3. Tornare a e il **WAF2 F5** e selezionare **app collegamento**. Verrà visualizzata e il **Collegamento applicazioni** . Qui è possibile connettere le risorse per la soluzione dei partner.
![Collegamento risorse soluzioni partner][5]

## <a name="see-also"></a>Vedere anche
In questo documento sono stati introdotti alla sezione **Soluzioni dei Partner** nel Centro protezione. Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) , informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) , informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Blog di sicurezza di Windows Azure](http://blogs.msdn.com/b/azuresecurity/) , ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
