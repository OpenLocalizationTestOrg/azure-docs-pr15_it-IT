<properties
    pageTitle="Impostare avvisi per le query di flusso Analitica | Microsoft Azure"
    description="Informazioni sulle flusso Analitica gli avvisi"
    keywords="impostare avvisi"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Impostare avvisi per i processi di Azure flusso Analitica

## <a name="introduction-monitor-page"></a>Introduzione: Pagina monitoraggio

È possibile impostare avvisi per attivare un avviso quando una metrica raggiunge una condizione specificata.

Ad esempio, "se gli eventi di Output per l'ultimi 15 minuti < 100, inviare id di posta elettronica di notifica tramite posta elettronica: xyz@company.com”.

Possono essere configurate in metriche tramite il portale e/o regole possono essere configurati [a livello di programmazione](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sui dati registri operazioni.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Impostare avvisi tramite il portale classica Azure

Esistono due modi per configurare avvisi nel portale di Azure classica:  

1.  Scheda **Monitor** del processo di flusso Analitica  
2.  Registro operazioni in servizi di gestione  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Impostare un avviso tramite la scheda Monitor del processo nel portale

1.  Nella scheda monitor, selezionare l'unità di misura metriche e fare clic sul pulsante **Aggiungi regola** nella parte inferiore del dashboard e le regole di configurazione.  

    ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Definire il nome e una descrizione dell'avviso  

    ![Creare una regola](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Immettere le soglie, finestra di valutazione degli avvisi e le azioni dell'avviso  

    ![Definire le condizioni](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Impostare avvisi tramite i registri di operazioni

1.  Fare clic sulla scheda **avvisi** in servizi di gestione del [Portale classica Azure](https://manage.windowsazure.com).  
2.  Fare clic su **Aggiungi regola**  

    ![Criteri](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Definire il nome e una descrizione dell'avviso. Selezionare 'Flusso Analitica' come tipo di servizio e il nome del processo come nome del servizio.  

    ![Definire avviso](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Impostare avvisi nel portale di Azure ##

Nel portale di Azure, individuare il processo di flusso Analitica che interessa gli avvisi in e fare clic sulla sezione **monitoraggio** .  In e **l'unità di misura metriche** visualizzata, fare clic sul comando **Aggiungi avviso** .

  ![Configurazione del portale Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

È possibile assegnare un nome alla regola di avviso e scegliere una descrizione che verrà visualizzato nel messaggio di posta elettronica di notifica.

Quando si seleziona metriche è necessario scegliere una condizione e soglia valore per la metrica.

  ![Azure portale selezionare unità di misura metriche](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Per informazioni dettagliate sulla configurazione di avvisi nel portale di Azure, vedere [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
