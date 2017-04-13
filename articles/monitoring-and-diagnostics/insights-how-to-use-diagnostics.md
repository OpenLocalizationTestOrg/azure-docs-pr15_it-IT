<properties
    pageTitle="Abilita il monitoraggio e diagnostica in Microsoft Azure | Microsoft Azure "
    description="Informazioni su come configurare la diagnostica per le risorse di Azure."
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

# <a name="enable-monitoring-and-diagnostics"></a>Abilitare il monitoraggio e diagnostica

Nel [Portale di Azure](https://portal.azure.com), è possibile configurare accattivanti e dall'aspetto frequenti, monitoraggio e diagnostica dati sulle risorse. Per configurare la diagnostica a livello di programmazione, è possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) .

Dati di diagnostica, monitoraggio e unità di misura metriche in Azure vengono salvati in un account di archiviazione di propria scelta. In questo modo è possibile utilizzare qualsiasi utensili che si desidera leggere i dati da una finestra di esplorazione di spazio di archiviazione di Power BI per gli strumenti di terze parti.

## <a name="when-you-create-a-resource"></a>Quando si crea una risorsa

La maggior parte dei servizi consentono di attivare la diagnostica quando li si prima crea nel [Portale di Azure](https://portal.azure.com).

1. Passare a **Nuovo** e scegliere la risorsa che interessa.

2. Selezionare **configurazione facoltativa**.
    ![Blade diagnostica](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Fare clic **su**selezionare **diagnostica**. È necessario scegliere l'account di archiviazione che si desidera diagnostica da salvare. Si verrà addebitato velocità normale per lo spazio di archiviazione e le transazioni quando si invia diagnostica a un account di archiviazione.

4. Fare clic su **OK** e creare la risorsa.

## <a name="change-settings-for-an-existing-resource"></a>Modificare le impostazioni per una risorsa esistente

Se una risorsa è già stato creato e si desidera modificare le impostazioni di diagnostica (per modificare il livello di raccolta dati, ad esempio), è possibile che a destra nel portale di Azure.

1. Passare alla risorsa e fare clic sul comando **Impostazioni** .

2. Selezionare **diagnostica**.

3. E il **diagnostica** è tutte le possibili diagnostica e monitoraggio raccolta dati per tale risorsa. Per alcune risorse è anche possibile scegliere un criterio di **conservazione** per i dati, per pulire dal proprio account di archiviazione.
    ![Diagnostica di spazio di archiviazione](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Dopo aver scelto le impostazioni, fare clic sul comando **Salva** . Potrebbe richiedere un po' per controllare i dati per la visualizzazione se si desidera abilitare per la prima volta.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Categorie della raccolta di dati per macchine virtuali
Per macchine virtuali di tutte le metriche e registri delle verranno registrati a intervalli di un minuto, in modo da poter sempre le informazioni più aggiornate su computer.

- **Metriche di base** : metriche integrità relative alla macchina virtuale, ad esempio memoria e processore
- **Metriche di rete e web** : metriche relativi ai connessioni di rete e servizi web
- **Metrica .NET** : metriche sulle applicazioni .NET e ASP.NET in esecuzione nel computer virtuale
- **Metrica SQL** : se si esegue Microsoft SQL Service, le misure delle prestazioni
- **Registri applicazione eventi di Windows** : gli eventi di Windows inviati al canale applicazione
- **Registro di sistema di eventi di Windows** : eventi di Windows che vengono inviati al canale di sistema. Inoltre, sono inclusi tutti gli eventi da [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Log sicurezza eventi di Windows** : gli eventi di Windows inviati al canale di sicurezza
- **Registri dell'infrastruttura di diagnostica** : la registrazione per quanto riguarda l'infrastruttura di raccolta diagnostica
- **I registri di IIS** : registri relativi ai server IIS

Si noti che al momento non sono supportate alcune distribuzioni di Linux e, l'agente di Guest deve essere installata nel computer virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano gli eventi operativi o metriche incrociato una determinata soglia.
* [Metrica servizio monitor](insights-how-to-customize-monitoring.md) per verificare che il servizio è disponibile e rispondere.
* [Ridimensionamento automatico conteggio delle istanze](insights-how-to-scale.md) per assicurarsi che la scala di servizio in base alla domanda.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si vuole conoscere esattamente come l'esecuzione del codice nel cloud.
* [Visualizzare gli eventi e registri di controllo](insights-debugging-with-events.md) per informazioni su tutti gli elementi che è avvenuto nel servizio di posta.
* [L'integrità dei servizi di rilevamento](insights-service-health.md) per sapere quando Azure è stato rilevato interruzioni di servizio o riduzione delle prestazioni.
