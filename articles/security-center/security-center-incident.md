<properties
   pageTitle="Gestione problemi di protezione in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento consente di usare le funzionalità di Azure Security Center per gestire i problemi di protezione."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Gestione problemi di protezione in Centro protezione di Azure 
Valutazione e l'analisi di avvisi di sicurezza può essere necessario molto tempo per analisti sicurezza più esperti e per molti è difficile sapere anche il punto di inizio. Utilizzando [analitica](security-center-detection-capabilities.md) per connettere le informazioni tra distinti [gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md), Centro protezione è possibile ottenere una singola visualizzazione di una campagna di attacco e tutti gli avvisi correlati: è possibile comprendere rapidamente le azioni che ha preso il pirata informatico e le risorse interessate.

In questo documento viene illustrato come utilizzare la funzionalità degli avvisi di protezione in Centro protezione per agevolare la gestione problemi di protezione.


## <a name="what-is-a-security-incident"></a>Che cos'è un problema di sicurezza?

In Centro protezione, un incidente è un'aggregazione di tutti gli avvisi per una risorsa che si adattano al [interrompere catena](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) modelli. Interventi visualizzati nel riquadro [Gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md) e blade. Un evento imprevisto, verrà visualizzate l'elenco dei relativi avvisi, che consente di ottenere ulteriori informazioni su ogni occorrenza.

## <a name="managing-security-incidents"></a>Gestione dei problemi di protezione

È possibile esaminare i problemi di protezione corrente esaminando il riquadro degli avvisi di sicurezza. Accedere al portale di Azure e seguire la procedura seguente per visualizzare ulteriori dettagli su ogni incidente:

1. Nel dashboard di Centro protezione, verrà visualizzato il riquadro **gli avvisi di sicurezza** .

    ![Il riquadro degli avvisi di protezione in Centro protezione](./media/security-center-incident/security-center-incident-fig1.png)

2.  Fare clic sul riquadro per espandere e se viene rilevato un problema di sicurezza, verrà visualizzato sotto il grafico gli avvisi di sicurezza come illustrato di seguito:

    ![Incidente](./media/security-center-incident/security-center-incident-fig2.png)

3.  Si noti che la descrizione ai problemi di sicurezza presenta un'icona diversa rispetto agli altri avvisi. Fare clic su di essa per visualizzare ulteriori dettagli su questo problema.

    ![Incidente](./media/security-center-incident/security-center-incident-fig3.png)

4.  **Evento imprevisto** blade risulta più i dettagli relativi a questo problema di protezione, che include la descrizione completa, la gravità (che in questo caso sia sufficientemente alta), lo stato corrente (in questo caso è ancora *attivo*, che implica l'utente non è un'azione per *ignorare* - questa operazione può essere gestita facendo clic su destro del mouse su evento imprevisto in e il **avvisi di sicurezza** ) , risorsa attaccata (in questo caso *VM1*), per correggere i passaggi per l'evento imprevisto relativi e nel riquadro inferiore disporre gli avvisi che sono stati inclusi in questo evento imprevisto. Se si desidera ottenere ulteriori informazioni su ogni avviso, fare clic sul it e un altro blade verrà aperta, come illustrato di seguito:

    ![Incidente](./media/security-center-incident/security-center-incident-fig4.png)

Informazioni su questo blade variano in base all'avviso. Per ulteriori informazioni su come gestire gli avvisi, vedere [la gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) . Alcune importanti considerazioni relative a questa funzionalità:

- Un nuovo filtro consente di personalizzare la visualizzazione a solo evento imprevisto, solo gli avvisi o entrambi. 
- Lo stesso avviso può esistere come parte di un incidente (se applicabile), oltre che da rendere visibili come un avviso autonomo. 
- La chiusura di un incidente non consente di chiudere gli avvisi correlati.

## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come utilizzare la funzionalità di incidente di protezione in Centro protezione. Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Gestire e rispondere agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md)
- [Funzionalità di rilevamento di Centro protezione di Azure](security-center-detection-capabilities.md)
- [Centro protezione di Azure Guida alla pianificazione e operazioni](security-center-planning-and-operations-guide.md)
- [Gestire e rispondere agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md)
- [Domande frequenti su Centro protezione di azure](security-center-faq.md)- trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/)- Trova blog su Azure sicurezza e conformità.
