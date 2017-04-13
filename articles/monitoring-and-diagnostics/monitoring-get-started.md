<properties
    pageTitle="Guida introduttiva di Azure Monitor | Microsoft Azure"
    description="Iniziare a utilizzare Monitor Azure per comprendere il funzionamento di risorse e intervenire in base ai dati."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Guida introduttiva di Azure Monitor

Monitor Azure è il servizio di piattaforma che fornisce un'unica origine per il monitoraggio delle risorse Azure. Con Azure Monitor, è possibile visualizzare, richiedere, instradare, archiviare e intervenire metriche e registri provenienti da risorse in Azure. Per lavorare con i dati utilizzando Monitor portale e il, [I cmdlet di PowerShell Monitor](./insights-powershell-samples.md), [Multipiattaforma CLI](insights-cli-samples.md)o [API REST di Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx). In questo articolo sono procedure di alcuni degli elementi essenziali di Azure Monitor, tramite il portale dimostrativo.

1. Nel portale, passare a **più servizi** e trovare l'opzione **Monitor** . Fare clic sull'icona della stella per aggiungere questa opzione all'elenco Preferiti in modo che sia sempre facilmente accessibile dalla barra di spostamento sinistro.

    ![Eseguire il monitoraggio nell'elenco servizi](./media/monitoring-get-started/monitor-more-services.png)

2. Fare clic sull'opzione di **Monitor** per aprire la e il **Monitor** . Questo blade riunisce tutte le impostazioni e dati monitoraggio in un'unica visualizzazione consolidata. Viene aperta prima di tutto alla sezione **registro attività** .

    ![Spostamento di blade monitor](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] Le opzioni di **notifiche di servizio** e **gruppi di notifica** visualizzate sopra verranno visualizzata solo per gli utenti che si aggiungono l'anteprima privato di queste caratteristiche.

    Monitor Azure sono previsti tre categorie di base dei dati di controllo: I **log di attività**, **metriche**e **registri diagnostici**.

3. Fare clic su **attività accedere** per assicurarsi che venga visualizzata la sezione registro attività.

    ![Blade Log attività](./media/monitoring-get-started/monitor-act-log-blade.png)

    [**Registro attività**](./monitoring-overview-activity-logs.md) descrive tutte le operazioni eseguite sulle risorse nell'abbonamento. Utilizzando il registro attività, è possibile determinare il ' cosa, chi e il momento ' per le operazioni di creazione, aggiornare o eliminare operazioni sulle risorse nell'abbonamento. Ad esempio, il registro attività indica quando è stata interrotta un'app web e che ha smesso. Eventi del Log di attività sono archiviate nella piattaforma e disponibili per eseguire una query per 90 giorni.
   
    È possibile creare e salvare le query per i filtri comuni quindi aggiungere le query a un dashboard portale più importanti in modo da scoprire sempre se si sono verificati eventi che soddisfano i criteri.

4. Filtrare la visualizzazione per un determinato gruppo di risorse nell'ultima settimana, quindi fare clic sul pulsante **Salva** .

    ![Salvare l'attività delle query di log](./media/monitoring-get-started/monitor-act-log-save.png)

5. A questo punto, fare clic sul pulsante **Aggiungi** .

    ![Fare clic su Aggiungi per registro attività](./media/monitoring-get-started/monitor-act-log-pin.png)

    La maggior parte delle visualizzazioni in questa procedura dettagliata possono essere aggiunti a un dashboard. Consente di creare un'unica fonte di informazioni per i dati operativi su servizi. 

6. Tornare al dashboard. È ora possibile visualizzare il query e numero di risultati, verrà visualizzate nel dashboard. Ciò è utile se si desidera visualizzare rapidamente le azioni di alto profilo che si sono verificati recente nell'abbonamento, ad esempio. un nuovo ruolo è stato assegnato oppure una macchina virtuale è stata eliminata.

    ![Registro attività aggiunte al dashboard](./media/monitoring-get-started/monitor-act-log-db.png)

7. Tornare alla sezione **Monitor** e fare clic sulla sezione **metriche** . È innanzitutto necessario selezionare una risorsa, filtro e selezionando utilizzando le opzioni a discesa nella parte superiore e il.

    ![Filtro delle risorse per le metriche](./media/monitoring-get-started/monitor-met-filter.png)

    Tutte le risorse Azure generano [**metriche**](./monitoring-overview-metrics.md). Questa visualizzazione riunisce tutti metriche in un'unica interfaccia in modo che è possibile comprensione delle modalità di esecuzione delle risorse.

8. Dopo aver selezionato una risorsa, sul lato sinistro della stessa e vengono visualizzate tutte le metriche disponibili. È possibile grafico contemporaneamente più metriche selezionando metriche e modificare l'intervallo di tempo e tipo di grafico. È inoltre possibile visualizzare tutti gli avvisi metrici impostare questa risorsa.

    ![Metrica blade](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Alcuni metriche sono disponibili solo abilitando [Approfondimenti applicazione](../application-insights/app-insights-overview.md) e/o Windows o Linux Azure diagnostica per la risorsa.

9. Quando si è soddisfatti del grafico, è possibile utilizzare il pulsante **Aggiungi** per aggiungere al dashboard.

10. Tornare a e il **Monitor** e fare clic su **registri diagnostici**.

    ![Blade registri diagnostici](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Registri diagnostici**](monitoring-overview-of-diagnostic-logs.md) sono registri emessi *da* una risorsa che forniscono dati sul funzionamento di tale risorsa specifica. Ad esempio contatori regola gruppo di sicurezza di rete e dei registri del flusso di lavoro di logica di App sono entrambi i tipi di registri diagnostici. I registri possono essere archiviati in un account di archiviazione, in streaming a un Hub di eventi e/o inviati al [Log Analitica](../log-analytics/log-analytics-overview.md). Log Analitica è Microsoft Business intelligence operativi per la ricerca avanzata e gli avvisi.
   
    Nel portale è possibile visualizzare e filtrare un elenco di tutte le risorse nell'abbonamento a identificare se hanno registri diagnostici abilitati.

11. Fare clic su una risorsa e il log diagnostici. Se registri diagnostici vengono archiviati in un account di archiviazione, verrà visualizzato un elenco dei registri orari che è possibile scaricare direttamente.

    ![Registri diagnostici per una risorsa](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    È anche possibile fare clic su **Impostazioni di diagnostica**, che consente di impostare o modificare le impostazioni di archiviazione per un account di archiviazione, streaming a un evento hub o inviando il file in un'area di lavoro Analitica Log.

    ![Abilitare i registri diagnostici](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Se è stata configurata registri diagnostici per Log Analitica, è possibile cercare quindi loro nella sezione **ricerca dei registri** del portale.

12. Passare alla sezione **avvisi** della stessa e Monitor.

    ![pala avvisi per pubblico](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Qui è possibile gestire tutti [**gli avvisi**](./monitoring-overview-alerts.md) sulle risorse del Azure. Sono inclusi gli avvisi metriche, eventi del log di attività (in privato preview), test web approfondimenti applicazione (posizioni) e diagnostica preventiva approfondimenti applicazione. Avvisi consente di attivare un messaggio di posta elettronica da inviare o un POST HTTP a un URL webhook.
   
13. Fare clic su **Aggiungi avviso metrica** per creare un avviso.

    ![aggiungere un avviso per metriche](./media/monitoring-get-started/monitor-alerts-add.png)

    È quindi possibile aggiungere un avviso per il dashboard per vedere facilmente il proprio stato in qualsiasi momento.

14. Sezione del Monitor include anche collegamenti a [Informazioni dettagliate sui applicazione](../application-insights/app-insights-overview.md) applicazioni e soluzioni di gestione [Log Analitica](../log-analytics/log-analytics-overview.md) . Questi altri prodotti Microsoft hanno integrazione con Azure Monitor.

15. Se non si usa applicazione approfondimenti o Log Analitica, è molto probabile che che Azure Monitor dispone di una relazione con la registrazione di monitoraggio, corrente e prodotti avvisi. Vedere la [pagina relativa ai partner](./monitoring-partners.md) per un elenco completo e istruzioni su come integrare.

La procedura seguente e bloccare tutte le sezioni pertinenti in un dashboard, è possibile creare visualizzazioni complete dell'applicazione e infrastruttura come questo:

![Dashboard Monitor Azure](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Passaggi successivi
- Leggere la [Panoramica del Monitor Azure](./monitoring-overview.md)
