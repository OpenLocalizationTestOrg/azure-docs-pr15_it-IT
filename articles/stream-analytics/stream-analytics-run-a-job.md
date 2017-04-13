<properties 
    pageTitle="Modalità di avvio del flusso di processi in flusso Analitica | Microsoft Azure" 
    description="Come eseguire un processo di trasmissione in Azure flusso Analitica | segmento di percorso di apprendimento."
    keywords="processi di flussi"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Come eseguire un processo di trasmissione in Azure flusso Analitica

Quando un processo di input, query e output tutti specificati che è possibile avviare il processo di flusso Analitica.

Per avviare il processo:

1.  Nel portale di Azure classica nel dashboard di processo, fare clic su **Avvia** nella parte inferiore della pagina.

    ![Avvia processo pulsante](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    Nel portale di Azure, fare clic su **Avvia** nella parte superiore della pagina processo.

    ![Processo di avvio del portale Azure pulsante](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Specificare un valore di **Output iniziare** per determinare quando questo processo verrà avviato un output. L'impostazione predefinita per i processi non precedentemente avviata è **Ora di inizio processo**, il che significa che il processo verrà avviato immediatamente elaborazione dei dati. È inoltre possibile specificare un'ora **personalizzati** in passato (per l'utilizzo di dati cronologici) o successive (di ritardo elaborazione fino a un secondo momento). Per i casi quando un processo è stato avviato e arrestato, in precedenza l'opzione **Ultima arrestato** è disponibile per riprendere il processo durante l'ultimo output ed evitare perdite di dati.  

    ![Avviare il flusso di lavoro ora](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portale Avvia trasmissione processo ora](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confermare la selezione. Lo stato del processo cambierà a *partire da* e poco sposterà *all'esecuzione di* una volta avviato il processo. È possibile monitorare l'avanzamento dell'operazione di **avvio** nell' **Hub di notifica**:

    ![stato di avanzamento flusso di lavoro](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Portale Azure streaming lo stato di avanzamento di processo](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
