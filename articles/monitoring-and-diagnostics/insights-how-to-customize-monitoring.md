<properties
    pageTitle="Panoramica delle metriche di Microsoft Azure | Microsoft Azure"
    description="Informazioni su come personalizzare i grafici monitoraggio in Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Panoramica delle metriche di Microsoft Azure

Tutti i servizi di Azure tenere traccia di metriche chiave che consentono di eseguire il monitoraggio dell'integrità, prestazioni, disponibilità e l'uso dei servizi. È possibile visualizzare queste metriche nel portale di Azure ed è anche possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere a livello di programmazione l'insieme completo di metriche.

Per alcuni servizi, potrebbe essere necessario attivare la diagnostica per vedere le metriche. Per altri utenti, ad esempio macchine virtuali, verrà visualizzato un insieme di metriche di base ma necessario per abilitare l'intero set di metriche ad alta frequenza. Vedere [abilitare il monitoraggio e diagnostica](insights-how-to-use-diagnostics.md) per altre informazioni.

## <a name="using-monitoring-charts"></a>Utilizzare grafici di monitoraggio

Creare grafici uno qualsiasi dei criteri di misurazione loro in qualsiasi periodo di tempo scelto.

1. Nel [Portale di Azure](https://portal.azure.com/), fare clic su **Sfoglia**e quindi una risorsa a cui si è interessati a monitoraggio.

2. La sezione **monitoraggio** contiene le metriche più importanti per ogni risorsa Azure. Ad esempio un'app web è **richieste ed errori**, in cui come macchina virtuale avrebbe **percentuale di CPU** e **di lettura e scrittura**:  ![lens monitoraggio](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Fare clic su uno di essi comandi verranno visualizzate e **l'unità di misura metriche** . È una tabella in cui sono mostrate le aggregazioni di metriche (ad esempio, Media, minima e massima, in intervallo di tempo che si è scelto) e l'oltre il grafico. Di seguito che sono le regole di avviso per la risorsa.
    ![Metrica blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Per personalizzare le righe che vengono visualizzati, fare clic sul pulsante **Modifica** nel grafico o il comando **Modifica grafico** e il metrico.

5. In e il modifica Query è possibile eseguire tre elementi:
    - Modifica dell'intervallo di tempo
    - Cambiare l'aspetto tra barre e a linee
    - Scegliere metics diversi ![modifica Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Modifica dell'intervallo di tempo è sufficiente selezionare un intervallo diverso (ad esempio **Ultima ora**) e fare clic su **Salva** nella parte inferiore della stessa e. È anche possibile scegliere **personalizzata**, che consente di scegliere un periodo di tempo durante le ultime 2 settimane. Ad esempio, è possibile visualizzare l'interi due settimane oppure, semplicemente 1 ora da ieri. Digitare nella casella di testo per immettere un'ora diversa.
    ![Intervallo di tempo personalizzato](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Di sotto dell'intervallo di tempo, è il canale scegliere qualsiasi numero di metriche da visualizzare nel grafico.

8. Quando si fa clic su Salva le modifiche verranno salvate della risorsa. Ad esempio, se si dispone di due macchine virtuali e si modifica un grafico su uno, non ostacoli l'altro.

## <a name="creating-side-by-side-charts"></a>Creazione di grafici side-by-side

Con la personalizzazione potente nel portale è possibile aggiungere i grafici che si vuole.

1. Nel menu **…** nella parte superiore e il fare clic su **Aggiungi sezioni**:  
    ![Menu aggiunta](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Quindi, è possibile selezionare un grafico dalla **raccolta** sul lato destro dello schermo:  ![raccolta](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se l'unità di misura metriche desiderato non è visibile, è sempre possibile aggiungere uno dei metriche preimpostate e **modificare** il grafico per visualizzare la metrica che è necessario.

## <a name="monitoring-usage-quotas"></a>Monitorare le quote di uso

La maggior parte delle metriche mostrano le tendenze nel tempo, ma alcuni dati, ad esempio le quote di uso sono le informazioni in un momento con una determinata soglia.

È anche possibile vedere le quote di uso sul e per le risorse con le quote di:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Ad esempio con metriche, è possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere a livello di programmazione l'insieme completo delle quote di uso.

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere notifiche](insights-receive-alert-notifications.md) quando un'unità di misura metriche supera una soglia.
* [Abilitare il monitoraggio e diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche ad alta frequenza dettagliate del servizio.
* [Ridimensionamento automatico conteggio delle istanze](insights-how-to-scale.md) per assicurarsi che il servizio è disponibile e rispondere.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si vuole conoscere esattamente come l'esecuzione del codice nel cloud.
* Utilizzare [Applicazione approfondimenti per App JavaScript e pagine web](../application-insights/app-insights-web-track-usage.md) per ottenere analitica client sui browser che visita una pagina web.
* [Disponibilità di monitoraggio e la velocità di risposta di qualsiasi pagina web](../application-insights/app-insights-monitor-web-app-availability.md) con informazioni dettagliate sui applicazione in modo che è possibile scoprire se la pagina è verso il basso.
