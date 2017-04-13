<properties
    pageTitle="Tenere traccia di integrità dei servizi Azure utilizzando i registri delle attività Monitor Azure | Microsoft Azure"
    description="Scoprire quando Azure è stato rilevato interruzioni di servizio o riduzione delle prestazioni. "
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Tenere traccia di integrità dei servizi Azure utilizzando i registri delle attività Monitor Azure

Azure publicizes ogni volta che è presente una riduzione delle prestazioni o interruzione del servizio. È possibile esplorare questi eventi nel portale di Azure ed è anche possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per accedere all'intera serie di eventi a livello di programmazione.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Esplorare i log di integrità dei servizi per l'abbonamento

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nella **pagina iniziale** verrà visualizzato un riquadro intitolato **l'integrità dei servizi**. Fare clic su di esso.

    ![Home Page](./media/insights-service-health/Insights_Home.png)

3. Verrà visualizzato un elenco di tutte le aree di Azure. Fare clic su un'area per visualizzare la query registro attività che mostra interventi di assistenza che sono interessati a una sottoscrizione nelle ultime 24 ore.

    ![Integrità dei servizi di abbonamento Log di attività](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. È possibile visualizzare i dettagli di un evento imprevisto dei singoli facendo clic su evento nella tabella.

5. Modificare **Timespan** per visualizzare un periodo di tempo più tempo.

## <a name="next-steps"></a>Passaggi successivi

* [Disponibilità di monitoraggio e la velocità di risposta di qualsiasi pagina web](../application-insights/app-insights-monitor-web-app-availability.md) con informazioni dettagliate sui applicazione in modo che è possibile scoprire se la pagina è verso il basso.
