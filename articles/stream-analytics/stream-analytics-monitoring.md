<properties 
    pageTitle="Informazioni sulle flusso Analitica processo monitoraggio | Microsoft Azure" 
    description="Informazioni sulle flusso Analitica processo monitoraggio" 
    keywords="monitoraggio di query"
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

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Comprendere il monitoraggio di flusso Analitica processo e come eseguire il monitoraggio delle query

## <a name="introduction-the-monitor-page"></a>Introduzione: Monitor pagina

Portale di gestione di Azure e Azure portale superficie metriche di prestazioni chiave che possono essere utilizzate per monitorare e risolvere i problemi di prestazioni del processo e query. 

Nel portale di gestione di Azure, fare clic sulla scheda **Monitor** di un processo in esecuzione flusso Analitica per visualizzare queste metriche. Esiste un ritardo di al massimo un minuto nelle metriche prestazioni compare nella pagina Monitor.  

  ![Monitoraggio processo Dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

Nel portale di Azure, passare al processo di flusso Analitica interessa vedere metriche per e visualizzare la sezione **monitoraggio** .  

  ![Processo di monitoraggio portale Azure Dashboard](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

La prima volta che un processo di flusso Analitica viene creato in un'area, sarà necessario configurare la diagnostica per tale area. A tale scopo, verrà visualizzato fare clic su un punto qualsiasi della sezione di **monitoraggio** e e il **diagnostica** . Qui è possibile abilitare la diagnostica e specificare un account di archiviazione per controllare i dati.  

  ![Azure portale configurare query diagnostica](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriche disponibili per Analitica flusso


| Unità di misura metriche | Definizione |
|--------|-------------|
| Utilizzo di % SO | Utilizzo delle unit Streaming assegnato a un processo dalla scheda scala del processo. Deve questo indicatore raggiungere 80% o precedente, non esiste alta probabilità che elaborazione dell'evento può essere ritardata o smesso di stato di avanzamento. |
| Eventi di input | Quantità di dati ricevuti tramite il processo di flusso Analitica, numero di eventi. Può essere utilizzato per convalidare gli eventi vengono inviati all'origine di input. |
| Eventi di output | Quantità di dati inviati tramite il processo di flusso Analitica alla destinazione di output, numero di eventi. |
| Eventi in ordine | Numero di eventi ricevuto dall'ordine in cui sono stati eliminati o assegnato un adeguamento della data e ora in base ai criteri di ordinamento evento. Questa operazione può essere influenzata dalla configurazione dell'impostazione della finestra di tolleranza ordine. |
| Errori di conversione dei dati | Numero di errori di conversione dei dati sostenuti tramite un processo di flusso Analitica. |
| Errori di runtime | Numero di errori che si verificano durante l'esecuzione di un processo di flusso Analitica. |
| Eventi di Input in ritardo | Numero di eventi ricevuti in ritardo dall'origine che essere eliminata o loro timestamp è stato modificato in base alla configurazione di criteri di ordinamento evento dell'impostazione di ritardo arrivo tolleranza finestra. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Personalizzazione di monitoraggio nel portale di gestione di Azure ##

Tutte le 6 metriche possono essere visualizzate in un grafico.

Per passare dalla visualizzazione relativi valori (valore finale solo per ogni unità di misura metriche) e assoluto (asse Y visualizzato), selezionare relativo o assoluto nella parte superiore del grafico.

  ![Monitor query relativo assoluto](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Metrica può essere visualizzata nel grafico Monitor in aggregazioni di 1 ora, 12 ore, 7 giorni o 24 ore.

Per modificare l'intervallo di tempo Visualizza grafico metriche, selezionare 1 ora, 24 ore o 7 giorni nella parte superiore del grafico.

  ![Monitor query scala cronologica](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

È possibile impostare regole che possono inviare una notifica tramite posta elettronica nel caso in cui il processo supera una soglia predefinita. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizzazione di monitoraggio nel portale di Azure ##

È possibile modificare il tipo di grafico, metriche mostrate, intervallo di tempo le impostazioni di modifica del grafico. Per informazioni dettagliate, vedere [come personalizzare il monitoraggio](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Scala cronologica di Azure Query portale Monitor](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Stato dei processi

Lo stato dei processi Analitica flusso può essere visualizzato nel portale classica di Azure in cui è visualizzato un elenco dei processi. Per visualizzare l'elenco dei processi, fare clic sull'icona di flusso Analitica nel portale classica di Azure.

| Stato | Definizione |
|--------|------------|
| Creato | Un processo è stato creato, tuttavia non è stato avviato. |
| Avvio | Un utente fa clic su Avvia il processo e il processo di avvio |
| In esecuzione | Il processo allocato, l'elaborazione di input o in attesa per l'elaborazione di input. Se il processo Mostra stato di esecuzione senza produrre output, è probabile che l'intervallo di tempo di elaborazione dei dati è di grandi dimensioni o la logica di query complessa. Un altro motivo può essere che attualmente non è presente alcuna eventuali dati inviati al processo. |
| Interruzione | Un utente fa clic su Interrompi il processo e il processo verrà arrestato. |
| Arrestato | Il processo è stata interrotta. |
| Ridotto | Questo stato indica che un processo di flusso Analitica si è verificati errori temporanei (per ex. Gli errori di ingresso/usciti, errori di elaborazione, gli errori di conversione e così via). Il processo è ancora in esecuzione, tuttavia sono disponibili numerose errori generati. Questo processo richiede attenzione dei clienti e il cliente può vedere i registri di operazioni per gli errori. |
| Non è riuscito | Indica che il processo non riuscita a causa errori e ha smesso di elaborazione. Il cliente ha bisogno di esaminare i registri di operazioni per eseguire il debug degli errori. |
| Eliminazione | Indica che il processo viene eliminato. |

## <a name="diagnosis"></a>Diagnosi

Nel portale di gestione di Azure dashboard processo fornisce informazioni su in cui è necessario cercare l'output diagnosi, vale a dire input, e/o le operazioni di accesso. È possibile fare clic sul collegamento per passare alla posizione appropriata per esaminare la diagnosi.

  ![Monitor query errore](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

Fare clic su risorsa input o output fornisce informazioni di diagnostica dettagliate. Questo viene aggiornato con le informazioni più recenti di diagnosi durante il processo è in esecuzione.

  ![Diagnostica di query](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
