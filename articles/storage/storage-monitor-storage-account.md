<properties
    pageTitle="Procedura per il monitoraggio di un account di archiviazione | Microsoft Azure"
    description="Informazioni su come eseguire il monitoraggio un account di archiviazione in Azure tramite il portale di Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitor di un account di archiviazione nel portale di Azure

## <a name="overview"></a>Panoramica

È possibile monitorare l'account di archiviazione dal [Portale di Azure](https://portal.azure.com). Quando si configura l'account di archiviazione per il monitoraggio tramite il portale, lo spazio di archiviazione di Azure utilizza [Analitica lo spazio di archiviazione](http://msdn.microsoft.com/library/azure/hh343270.aspx) per tenere traccia delle metriche per il proprio account e registrare i dati di richiesta.

> [AZURE.NOTE]Altri costi associati all'analisi monitoraggio dei dati nel [Portale di Azure](https://portal.azure.com). Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">lo spazio di archiviazione Analitica e fatturazione</a>. <br />

> Spazio di archiviazione File Azure attualmente supporta metriche di archiviazione Analitica, ma non supporta ancora la registrazione. È possibile abilitare metriche di archiviazione dei File di Azure tramite il [Portale di Azure](https://portal.azure.com).

> Gli account di archiviazione a un tipo di replica di spazio di archiviazione zona ridondanti (ZRS) non si hanno la metrica o la funzionalità di registrazione abilitata al momento. 

> Per una Guida dettagliata sull'utilizzo di archiviazione Analitica e altri strumenti per identificare, diagnosi e risoluzione dei problemi relativi a spazio di archiviazione di Azure, vedere [Monitor, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Procedura: configurare il monitoraggio per un account di archiviazione

1. Nel [Portale di Azure](https://portal.azure.com), fare clic su **archiviazione**e quindi scegliere il nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configura**e scorrere fino alle impostazioni di **monitoraggio** blob, tabella e i servizi di coda.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. **Monitoraggio**, impostare il livello di monitoraggio e i criteri di conservazione dei dati per ogni servizio:

    -  Per impostare il livello di monitoraggio, selezionare una delle operazioni seguenti:

      **Minimo** - raccoglie metriche, ad esempio percentuali in ingresso/uscita, disponibilità, latenza e successo aggregate blob, tabella e i servizi di coda.

      **Dettagliato** - oltre le metriche minime raccoglie lo stesso set di criteri di misurazione per ogni operazione di archiviazione in API di servizio di archiviazione Azure. Metrica dettagliata Abilita analisi più dettagliata dei problemi che si verificano durante le operazioni dell'applicazione.

      **Disattivare** - consente di disattivare il monitoraggio. Controllo dati esistenti viene mantenuto fino alla fine del periodo di conservazione.

- Per impostare i criteri di conservazione dei dati, **conservazione (in giorni)**, digitare il numero di giorni di dati per la conservazione da 1 a 365. Se non si desidera impostare un criterio di conservazione, immettere zero. Se non c'è alcun criterio di conservazione, è possibile eliminare i dati di monitoraggio. È consigliabile impostare un criterio di conservazione in base a quanto tempo si desidera mantenere dati analitica lo spazio di archiviazione per l'account in modo che i dati analitica inutilizzati e obsoleti possono essere eliminati dal sistema senza costi.

4. Al termine della configurazione di monitoraggio, fare clic su **Salva**.

È consigliabile iniziare vedono il monitoraggio dei dati nel dashboard e la pagina **Monitor** dopo circa un'ora.

Fino a quando non si configura il monitoraggio di un account di archiviazione, non monitoraggio sono raccolti dati e i tipi di grafici metriche nella pagina **Monitor** e dashboard sono vuote.

Dopo aver impostato i livelli di monitoraggio e criteri di conservazione, è possibile scegliere quale delle statistiche disponibili per monitorare nel [Portale di Azure](https://portal.azure.com)e che il formato per tracciare nei grafici metriche. Viene visualizzato un insieme di predefinito metriche di ogni livello monitoraggio. È possibile utilizzare **Metriche Aggiungi** per aggiungere o rimuovere metriche dall'elenco metriche.

Metrica è archiviata in account di archiviazione in quattro tabelle denominate $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Per ulteriori informazioni, vedere [Sull'archiviazione Analitica metriche](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Procedura: personalizzare il dashboard per il monitoraggio

Nel dashboard, è possibile scegliere il formato fino a sei per tracciare nel grafico metriche da nove metriche disponibili. Per ogni servizio (blob, tabella e coda), sono disponibili le metriche di disponibilità, percentuale di completamento e le richieste di totale. Metriche disponibili nel dashboard di sono gli stessi per il monitoraggio minimo o dettagliato.

1. Nel [Portale di Azure](https://portal.azure.com), fare clic su **archiviazione**e quindi fare clic sul nome dell'account di archiviazione per aprire il dashboard.

2. Per modificare i criteri di misurazione vengono tracciati nel grafico, eseguire una delle operazioni seguenti:

    - Per aggiungere una nuova metrica al grafico, fare clic sulla casella di controllo colorata accanto all'intestazione metriche descritti nella tabella sotto il grafico.

    - Per nascondere una metrica viene tracciata del grafico, deselezionare la casella di controllo colorata accanto all'intestazione metrica.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Per impostazione predefinita, il grafico mostra le tendenze, la visualizzazione solo il valore corrente di ogni unità di misura metriche (l'opzione **relativa** nella parte superiore del grafico). Per visualizzare un asse Y in modo da visualizzare valori assoluti, selezionare **assoluto**.

4. Per modificare l'intervallo di tempo Visualizza grafico metriche, selezionare 6 ore, 24 ore o 7 giorni nella parte superiore del grafico.


## <a name="how-to-customize-the-monitor-page"></a>Procedura: personalizzare la pagina Monitor

Nella pagina **Monitor** , è possibile visualizzare l'elenco completo dei parametri per l'account di archiviazione.

- Se l'account di archiviazione include monitoraggio minimo configurato, metriche, ad esempio in ingresso/uscita, disponibilità, latenza e le percentuali di successo vengono aggregate della blob, tabella e i servizi di coda.

- Se l'account di archiviazione ha configurato il monitoraggio dettagliato, le metriche sono disponibili una risoluzione più dettagliato delle operazioni di archiviazione singoli oltre le aggregazioni a livello di servizio.

Usare le procedure seguenti per scegliere le metriche di archiviazione per visualizzare i grafici metriche e tabella vengono visualizzate nella pagina **Monitor** . Queste impostazioni non influiscono raccolta, l'aggregazione e lo spazio di archiviazione dei dati nell'account di archiviazione di controllo.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Procedura: aggiungere metriche alla tabella metriche


1. Nel [Portale di Azure](https://portal.azure.com), fare clic su **archiviazione**e quindi fare clic sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Monitor**.

    Verrà visualizzata la pagina **Monitor** . Per impostazione predefinita, la tabella metriche consente di visualizzare un sottoinsieme di metriche che sono disponibili per il monitoraggio. L'illustrazione mostra la visualizzazione di Monitor predefinito per un account di archiviazione con monitoraggio dettagliato configurato per tutti e tre i servizi. Consente di **Aggiungere metriche** per selezionare metriche che si desidera eseguire il monitoraggio da tutte le metriche disponibili.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Quando si selezionano le metriche, prendere in considerazione i costi. Esistono transazioni e i costi di uscita associati all'aggiornamento Visualizza monitoraggio. Per ulteriori informazioni, vedere [lo spazio di archiviazione Analitica e fatturazione](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Fare clic su **Aggiungi metriche**.

    L'aggregazione che sono disponibili in Monitoraggio minime sono nella parte superiore dell'elenco. Se la casella di controllo è selezionata, l'unità di misura metriche viene visualizzato nell'elenco metrica.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Passare il mouse sul lato destro della finestra di dialogo per visualizzare una barra di scorrimento che è possibile trascinare per scorrere statistiche aggiuntive nella visualizzazione.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Fare clic sulla freccia verso il basso per una metrica per espandere l'elenco di operazioni ambito è l'unità di misura metriche in modo da includere. Selezionare ogni operazione che si desidera visualizzare nella tabella metriche nel [Portale di Azure](https://portal.azure.com).

    Nella figura seguente è stato espanso l'unità di misura metriche percentuale di errore di autorizzazione.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Dopo aver selezionato la metrica per tutti i servizi, fare clic su OK (segno di spunta) per aggiornare la configurazione del monitoraggio. Metrica selezionata vengono aggiunti alla tabella metriche.

7. Per eliminare una metrica della tabella, fare clic su unità di misura metriche per selezionarlo e quindi fare clic su **Elimina metrica**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Procedura: personalizzare il grafico metriche nella pagina Monitor

1. Nella pagina **Monitor** per l'account di archiviazione, nella tabella metriche, selezionare il formato fino a 6 per tracciare nel grafico metriche. Per selezionare una metrica, fare clic sulla casella di controllo sul lato sinistro. Per rimuovere una metrica dal grafico, deselezionare la casella di controllo.

2. Per passare al grafico tra valori relativi (valore finale visualizzata solo) e assoluto (asse Y visualizzato), selezionare **relativo** o **assoluto** nella parte superiore del grafico.

3.  Per modificare il tempo variare metriche grafico Visualizza, selezionare **6 ore**, **24 ore**o **7 giorni** nella parte superiore del grafico.



## <a name="how-to-configure-logging"></a>Procedura: configurare la registrazione

Per ognuno dei servizi di archiviazione disponibili con l'account di archiviazione (blob, tabella e coda), salvare i registri di diagnostica per le richieste di lettura, le richieste di scrittura e/o le richieste di eliminazione e impostare i criteri di conservazione dei dati per ognuno dei servizi.

1. Nel [Portale di Azure](https://portal.azure.com), fare clic su **archiviazione**e quindi fare clic sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configura**e usare la freccia in giù sulla tastiera per scorrere fino a **registrazione**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Per ogni servizio (blob, tabella e coda), configurare le operazioni seguenti:

    - I tipi di richiesta di accesso: richieste di lettura, le richieste di scrittura e le richieste di eliminazione.

    - Il numero di giorni per mantenere i dati registrati. Immettere zero è se non si desidera impostare un criterio di conservazione. Se non si imposta un criterio di conservazione, è possibile eliminare i log.

4. Fare clic su **Salva**.

I registri di diagnostica vengono salvati in un contenitore di blob denominato $logs nell'account di archiviazione. Per informazioni sull'accesso contenitore $logs, vedere [Sull'archiviazione Analitica la registrazione](http://msdn.microsoft.com/library/azure/hh343262.aspx).
