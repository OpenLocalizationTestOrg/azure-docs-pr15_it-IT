<properties
    pageTitle="Panoramica degli avvisi in Microsoft Azure | Microsoft Azure"
    description="Avvisi consentono di eseguire il monitoraggio delle metriche di Azure delle risorse, eventi o registri e ricevere una notifica quando viene soddisfatta la condizione specificata."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Panoramica degli avvisi in Microsoft Azure


In questo articolo vengono illustrati quali avvisi, le prestazioni e su come iniziare a loro utilizzo.  

## <a name="what-are-alerts"></a>Quali sono gli avvisi?
Avvisi di un metodo di monitoraggio delle risorse Azure metriche, eventi, o log e quindi viene inviata una notifica quando la condizione specificata vengono soddisfatti.

È possibile ricevere avvisi in base a:

- **Valori metriche**: questo avviso attiva quando il valore di una metrica specificato supera una soglia assegnare in entrambe le direzioni. Vale a dire sia attiva quando condizione prima di tutto e quindi in seguito quando che condizione è non sia più soddisfatta.
- **Eventi del log di attività**: questo avviso consente di attivare tutti gli eventi o solo quando si verifica un determinato numero di eventi.

## <a name="alerts-in-different-azure-services"></a>Avvisi di diversi servizi di Azure

Avvisi sono disponibili diversi servizi, tra cui:

- **Applicazione approfondimenti**: Abilita web test e unità di misura metriche avvisi. Vedere [impostare gli avvisi di applicazione approfondimenti](../application-insights/app-insights-alerts.md) e [la disponibilità di monitoraggio e la velocità di risposta di qualsiasi sito Web](../application-insights/app-insights-monitor-web-app-availability.md).
- **Registro Analitica (operazioni Management Suite)**: abilita il routing dei registri diagnostici per Log Analitica. Operazioni Management Suite consente di unità di misura metriche, registro e altri tipi di avviso. Per ulteriori informazioni, vedere [gli avvisi di Log Analitica](../log-analytics/log-analytics-alerts.md).  
- **Azure Monitor**: Abilita avvisi in base a valori metriche e gli eventi del log di attività. Monitor Azure include l' [API REST di Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).  Per ulteriori informazioni, vedere [usare il portale di Azure, PowerShell o la riga di comando per creare gli avvisi](insights-alerts-portal.md).

## <a name="alert-actions"></a>Azioni degli avvisi
È possibile configurare un avviso per eseguire le operazioni seguenti:

- Inviare notifiche tramite posta elettronica per l'amministratore del servizio per coamministratori o agli indirizzi di posta elettronica aggiuntivi specificate.
- Chiamare un webhook, che consente di avviare azioni di automazione aggiuntivi.

 ![Avvisi spiegati](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Passaggi successivi

Ottenere informazioni sulle regole di avviso e la loro configurazione utilizzando:

- [Portale di Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interfaccia di riga di comando (CLI)](insights-alerts-command-line-interface.md)
- [API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
