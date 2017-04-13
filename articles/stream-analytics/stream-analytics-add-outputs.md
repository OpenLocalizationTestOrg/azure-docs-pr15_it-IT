<properties 
    pageTitle="Configurazione dei dati di output per i processi di flusso Analitica | Microsoft Azure" 
    description="Configurare gli output per i processi di flusso Analitica | segmento di percorso di apprendimento."
    keywords="dati di output, lo spostamento dei dati"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Configurazione dei dati di output per i processi di flusso Analitica

Azure processi Analitica flusso possono essere connesso a uno o più output di dati, che definisce una connessione a un sink dati esistenti. Durante il processo di flusso Analitica elaborazione e trasforma i dati in arrivo, un flusso di dati output eventi è scritto nell'output del processo.

Il flusso di dati Analitica output possono essere utilizzati per le origini dashboard in tempo reale o avvisi, impostare un trigger flussi di lavoro spostamento dei dati o semplicemente archiviare i dati per l'elaborazione batch in un secondo momento. Flusso Analitica ha prima classe integrazione con diversi servizi di Azure, descritte in dettaglio qui.

Per aggiungere un output per il lavoro Analitica flusso:

1. Nel portale classica di Azure, fare clic su **output** e quindi fare clic su **Aggiungi Output** nel processo di flusso Analitica.

    ![Aggiungi output](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Nel portale di Azure fare clic sul riquadro di **output** nel processo di flusso Analitica.

    ![Portal Azure Aggiungi output](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Specificare il tipo di output:

    ![Scegliere il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Portale di Azure scegliere il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Specificare un nome descrittivo per l'output nella casella **Alias di Output** . Questo nome può essere utilizzato nella query del processo in un secondo momento per fare riferimento all'output della.  
    
    Compilare il resto della proprietà della connessione necessari per connettersi all'output.  Questi campi possono variare in base al tipo di output e sono definiti in modo dettagliato qui.  

    ![Aggiungere le proprietà di output di dati](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. A seconda del tipo di output, potrebbe essere necessario specificare come i dati sono o formattati. Di seguito vengono descritte le impostazioni di serializzazione specifico per ogni tipo di output.

    Compilare il resto della proprietà della connessione necessari per connettersi all'origine dati. Questi campi possono variare in base al tipo del tipo di input e di origine e sono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).  

    ![Aggiungere l'output dei dati a hub di evento](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Output dei dati del portale Azure a hub di evento](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] Qualsiasi elemento di output aggiunto al processo, occorre prima viene avviato il processo e gli eventi avviare il flusso. Ad esempio, se si Usa archiviazione Blob come un output, il processo non creerà un account di archiviazione automatica. È necessario essere creati dall'utente prima di avviata il processo ASA.

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
