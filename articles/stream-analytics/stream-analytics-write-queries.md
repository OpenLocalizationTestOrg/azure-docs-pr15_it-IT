<properties 
    pageTitle="Come scrivere query in flusso Analitica | Microsoft Azure" 
    description="Scrivere query flusso Analitica e i dati di query | segmento di percorso di apprendimento."
    keywords="come scrivere query, eseguire query sui dati, scrivere una query, la scrittura di query"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Come scrivere query in flusso Analitica

Scrittura di query per l'elaborazione di logica di Azure flusso Analitica flusso è implementata come "permanente query" definito prima di un processo viene avviato ed eseguito sui dati come raggiunge il processo. La trasformazione dei dati è espresso in un linguaggio di query SQL simili, è ampiamente un sottoinsieme di T-SQL con alcune estensioni lingua aggiunta come [finestre](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilizzati per esprimere semantica temporale.

## <a name="writing-queries"></a>Scrittura di query: ##

1. Il processo di Analitica flusso nel portale di gestione di Azure, fare clic su **Query**.

    ![Query di selezione](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    Nel portale di Azure, fare clic su **Query**.

    ![Selezionare l'anteprima di Query](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Nuovi processi dispone di un modello di query per iniziare. Modello di query esegue una query "pass-through" progetti tutti i campi da eventi di input nell'output.  

    - Se è stata definita almeno un input e output per il lavoro, è possibile sostituire il segnaposto "[YourOutputAlias]" e "[YourInputAlias]" campi con gli alias di input e di output che si desidera utilizzano prima di tutto. È inoltre possibile creare e testare la query nel portale classica di Azure senza la definizione di input e output nel processo.
    - Se si desidera eseguire altre operazioni rispetto a un semplice pass-through, è possibile modificare la definizione della query. Per iniziare a utilizzare la creazione di query, ecco alcune query comuni vengono acquisite motivi [qui](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Finestra di dati della query](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Uso per convalidare i dati di query: ##

È possibile verificare che la query si comporta come previsto eseguendo nel browser uno o più file JSON locale contenente i dati di test. Non verrà avviare il processo o avere qualsiasi implicazioni fatturazione.

> [AZURE.NOTE] Attualmente test di query nel browser non è supportato nel portale di Azure.  

1.  Assicurarsi che non siano presenti errori nella query (in caso contrario il pulsante prova sarà disabilitato) e quindi fare clic sul pulsante Test.  

    ![Eseguire query sui dati Test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Viene chiesto di specificare i file per ognuna di input di cui fa riferimento la query. In questo esempio, la query modello rimanga come-è, in modo che la finestra di dialogo è richiesta l'input denominato "yourinputalias".  

    ![Query di dati di test](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Passare a un file di test. Sono disponibili in [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) numerosi file di esempio ed è inoltre possibile recuperare dati di esempio dal proprio input flusso di dati tramite la funzione di dati di esempio nella scheda Input.  

    ![Input per la query](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Dopo aver chiuso la finestra di dialogo, verrà eseguita la query sui dati di test per visualizzare i risultati nella parte inferiore della pagina della Query.  

    ![Riepilogo di query](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
