<properties
    pageTitle="Ricevere notifiche di avviso per i servizi di Azure | Microsoft Azure"
    description="Ricevere una notifica quando sono soddisfatte le condizioni di regole di avviso."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Ricevere notifiche di avviso

È possibile ricevere un avviso in base al monitoraggio metriche per o gli eventi sul provider di servizi Azure.

Per una regola di avviso su un valore metrico quando il valore di una metrica specificata supera una soglia assegnata, la regola di avviso diventa attiva e invia una notifica. Per una regola di avviso gli eventi, una regola può inviare una notifica a *ogni* evento o, solo quando si verifica un determinato numero di eventi.

Quando si crea una regola di avviso, è possibile selezionare le opzioni per inviare un messaggio di notifica per l'amministratore di servizio e coamministratori o a un altro amministratore che è possibile specificare. Quando la regola diventa attiva e quando una condizione di avviso viene risolta, viene inviato un messaggio di posta elettronica di notifica.

È possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per configurare e ottenere informazioni sulle regole di avviso a livello di programmazione.

## <a name="create-an-alert-rule"></a>Creare una regola di avviso

1. Nel [portale](https://portal.azure.com/), fare clic su **Sfoglia**e quindi una risorsa a cui si è interessati a monitoraggio.

2. Fare clic sul riquadro **regole di avviso** in lens **operazioni** .

3. Fare clic sul comando **Aggiungi avviso** .

    ![Aggiungere un avviso](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. È possibile assegnare un nome alla regola di avviso e scegliere una descrizione che verrà visualizzato nel messaggio di posta elettronica di notifica.

5. Quando si seleziona **metriche** è necessario scegliere una condizione e soglia valore per la metrica. Questo è il periodo di tempo Azure viene usata per monitorare e tracciato attività avviso.

    ![Condizione e soglia](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. È anche possibile scegliere **eventi**e riceve una notifica quando si verifica un evento.

    ![Eventi](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Infine, è possibile inviare notifica tramite posta elettronica per gli amministratori di responsabili.

Dopo aver fatto clic **salvare**, nell'arco di qualche minuto sarà sempre informati ogni volta che l'unità di misura metriche scelto supera il limite.

## <a name="managing-your-alert-rules"></a>Gestire le regole di avviso

Dopo aver creato una regola di avviso, è possibile visualizzare un'anteprima dell'avviso soglia confrontata la metrica rispetto al giorno precedente.

![Eventi](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Naturalmente, è possibile modificare questa regola di avviso e **disabilitare** o **abilitare** se si desidera temporaneamente interrompere la ricezione delle notifiche su di esso.

## <a name="next-steps"></a>Passaggi successivi

* [Configura webhooks sugli avvisi](insights-webhooks-alerts.md) per indirizzare notifiche i diversi canali
* [Metrica servizio monitor](insights-how-to-customize-monitoring.md) per verificare che il servizio è disponibile e rispondere.
* [Abilitare il monitoraggio e diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche ad alta frequenza dettagliate del servizio.
* [Disponibilità di monitoraggio e la velocità di risposta di qualsiasi pagina web](../application-insights/app-insights-monitor-web-app-availability.md) con informazioni dettagliate sui applicazione in modo che è possibile scoprire se la pagina è verso il basso.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si vuole conoscere esattamente come l'esecuzione del codice nel cloud.
* [Visualizzare gli eventi e registri di controllo](insights-debugging-with-events.md) per informazioni su tutti gli elementi che è avvenuto nel servizio di posta.
* [L'integrità dei servizi di rilevamento](insights-service-health.md) per sapere quando Azure è stato rilevato interruzioni di servizio o riduzione delle prestazioni.
