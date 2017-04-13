<properties 
    pageTitle="Il debug mediante un'operazione e servizio registra in flusso Analitica | Microsoft Azure" 
    description="Utilizzare procedure Analitica flusso operazione log" 
    keywords="registri del servizio"
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Eseguire il debug processi flusso Analitica utilizzando i registri di servizio e operazione

Tutti i servizi di Azure forniscono messaggi operativi registrazione agli utenti per registrare i dettagli relativi alle operazioni di gestione. In Azure flusso Analitica, questo informazioni possono essere usate per il debug, ad esempio visualizzare lo stato dei processi, lo stato di avanzamento di processo e i messaggi di errore per tenere traccia dello stato di avanzamento di un processo nel tempo, da iniziare a elaborazione per generare l'output.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Trovare l'operazione registra nel portale di gestione di Azure

Registri operazioni è possibile accedervi in due modi:  

- Dashboard del processo di flusso Analitica  
- Servizi di gestione nel portale di Azure classica  

## <a name="dashboard-of-the-stream-analytics-job"></a>Dashboard del processo di flusso Analitica

Nella scheda Dashboard del processo viene visualizzato un collegamento ai registri corrispondenti di un processo di flusso Analitica. Se si fa clic in corrispondenza del collegamento, esso verrà impostato i filtri in modo che venga visualizzato più recente registri relativi al lavoro specifico.

  ![Selezionare servizi di gestione dei registri](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Gestione dei servizi

Per passare manualmente alla registri operazioni di flusso Analitica e altri servizi nel portale di Azure classica:

1.  Fare clic su **Gestione dei servizi** del [portale di Azure classica](https://manage.windowsazure.com).
2.  Il nome **Del servizio**, selezionare **Analitica flusso** per il **tipo** e il nome del processo.  

  ![Selezionare Analitica flusso](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Trovare i log di controllo nel portale di Azure ##

Per trovare registri operativi per il lavoro Analitica flusso nel portale di Azure, fare clic su **Sfoglia** e quindi selezionare **registri di controllo**.

  ![Portale Azure selezionare Analitica flusso](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Verrà aperta una pala con gli eventi negli ultimi sette giorni per tutte le risorse in un abbonamento.  È possibile filtrare per visualizzare gli eventi di un tipo di specificare o l'intervallo di tempo facendo clic sul comando **filtro** .

  ![Portale Azure selezionare Analitica flusso](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Informazioni log

È possibile filtrare l'intervallo di tempo e stato per visualizzare i registri per il lavoro.

Nel portale di gestione di Azure, fare clic sul pulsante **Dettagli** nella parte inferiore della finestra per visualizzare ulteriori dettagli sull'evento selezionato. 

  ![Selezionare dettagli](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Nel portale di Azure, fare clic su una voce del log per visualizzare gli eventi dettagliati all'interno.

  ![Portale Azure selezionare dettagli](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

A questo punto, è possibile aprire e il **dettaglio** facendo clic sull'evento.

  ![Portale Azure selezionare dettagli](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Eseguire il debug di un processo non riuscito

Nel portale di gestione di Azure, fare clic sull'icona di ricerca e il tipo "non è riuscita". In questo modo un risultato di tutti i registri di errori. 

  ![Il debug di un processo non riuscito](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Nel portale di Azure, è possibile filtrare dal livello del messaggio da visualizzare gli eventi **critici** .

  ![Debug portale Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

È possibile selezionare una qualsiasi degli errori e fare clic su **Dettagli** per ulteriori informazioni sull'errore.  Alcuni messaggi di errore vengono inoltre forniscono informazioni limitare il problema. 

  ![Dettagli dell'operazione](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Nel caso in cui è necessario contattare il [supporto tecnico](https://azure.microsoft.com/support/options/) o fornire informazioni al team tramite [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), tenere presente i dettagli dell'operazione, in particolare l' **ID di correlazione**. 

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
