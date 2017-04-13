<properties
   pageTitle="Correggere le vulnerabilità del sistema operativo in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **vulnerabilità eseguire OS**."
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

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Correggere le vulnerabilità del sistema operativo in Centro protezione di Azure

Centro protezione di Azure analizza ogni giorno il sistema operativo macchine () (sistema operativo) per le configurazioni che potrebbero essere più esposto agli attacchi la macchina virtuale e vengono consigliati modifiche alla configurazione di queste vulnerabilità. Per ulteriori informazioni sulle configurazioni specifiche che si sta controllando, vedere l' [elenco delle regole di configurazione consigliata](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Nel Centro sicurezza consigliati risolvere vulnerabilità quando configurazione del sistema operativo della macchina di virtuale corrispondono alle regole di configurazione consigliata.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **consigli** selezionare **vulnerabilità eseguire OS**.
![Correggere le vulnerabilità del sistema operativo][1]

    E il **OS correggere vulnerabilità** viene aperto e gli elenchi nelle macchine virtuali con configurazioni del sistema operativo che non corrispondono le regole di configurazione consigliata.  Per ogni macchina virtuale e l'identifica:

   - **Regole di errore** : il numero di regole che non è stata di configurazione del sistema operativo della macchina virtuale.
   - **Tempo ultima analisi** , la data e l'ora che Security Center ultima analisi configurazione del sistema operativo della macchina virtuale.
   - **Stato** - lo stato corrente della vulnerabilità:

        - Apri: La vulnerabilità non stata identificata ancora
        - In corso: La vulnerabilità attualmente applicata, è richiesta alcuna azione dall'utente
        - Risoluzione: La vulnerabilità è stata già esaminata (quando il problema è stato risolto, la voce è in grigio)
  - **GRAVITÀ** - tutte le vulnerabilità sono impostate su gravità bassa, ovvero una vulnerabilità deve essere inoltrata ma non richiede attenzione immediata.

Selezionare una macchina virtuale. Blade di quella macchina virtuale viene aperto e vengono visualizzate le regole che non sono riuscita.
   ![Regole di configurazione non sono riuscita][2]

Selezionare una regola. In questo esempio, consente di selezionare **Password deve soddisfare i requisiti di complessità**. Blade apre che descrive l'impatto e la regola non riuscita. Rivedere i dettagli e valutare la possibilità di modalità di applicazione configurazioni del sistema operativo.
  ![Descrizione della regola non riuscito][3]

  Nel Centro sicurezza utilizza comuni configurazione enumerazione (CCE) per assegnare gli identificatori univoci per le regole di configurazione. In questo blade sono disponibili le informazioni seguenti:

  - NOME - Nome della regola
  - GRAVITÀ - CCE gravità valore critico, importanti o di avviso
  - CCIED - CCE nome univoco per la regola
  - Descrizione: Descrizione della regola
  - VULNERABILITÀ - Spiegazione di vulnerabilità o rischio se non viene applicata regola
  - IMPATTO - Impatto aziendale quando vengono applicate regole
  - VALORE previsto - Valore previsto quando Security Center analizza la configurazione del sistema operativo macchine Virtuali con la regola
  - OPERAZIONE regola - Regola operazione utilizzato dal Centro protezione durante l'analisi della configurazione del sistema operativo macchine Virtuali con la regola
  - VALORE effettivo - Valore restituito dopo l'analisi della configurazione del sistema operativo macchine Virtuali con la regola
  - RISULTATO della valutazione –-risultato dell'analisi: passare Fail


## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare il suggerimento Security Center "Remediate OS vulnerabilità". È possibile esaminare il set di regole di configurazione [di seguito](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Nel Centro sicurezza utilizza CCE (comuni configurazione enumerazione) per assegnare gli identificatori univoci per le regole di configurazione. Visitare il sito [CCE](http://cce.mitre.org) per altre informazioni.

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
