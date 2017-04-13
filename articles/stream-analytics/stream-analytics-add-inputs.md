<properties
    pageTitle="Aggiungere un input di dati per i processi di flusso Analitica | Microsoft Azure"
    description="Informazioni su come collegare un'origine dati per il compito di Analitica flusso del flusso di input di dati dai dati evento hub o un riferimento dallo spazio di archiviazione di Blog."
    keywords="dati di input, i dati di flusso"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Aggiungere un flussi dati di input o un riferimento dati a un processo di flusso Analitica

Informazioni su come collegare un'origine dati per il processo di flusso Analitica come input di dati dai dati evento hub o un riferimento dallo spazio di archiviazione Blob di flusso.

Azure processi Analitica flusso possono essere connesso a input di dati di uno o più, ognuno dei quali definisce una connessione a un'origine dati esistente. Come dati viene inviati all'origine dati, è utilizzato dal processo flusso Analitica e il flusso di dati in tempo reale di elaborazione. Flusso Analitica ha prima classe integrazione con [Gli hub evento Azure](https://azure.microsoft.com/services/event-hubs/) e [archiviazione Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) all'interno e all'esterno della sottoscrizione del processo.

In questo articolo è un passaggio nel [percorso formativo: flusso Analitica](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Immissione dei dati: il flusso di dati e riferimento

Esistono due diversi tipi di input in flusso Analitica: flussi di dati e dati di riferimento.

- **Flussi di dati**: processi flusso Analitica devono includere almeno un dato flusso input per essere utilizzato ed trasformato dal processo. Azure archiviazione Blob e Azure evento hub sono supportati come origini di input flusso di dati. Hub evento Azure vengono utilizzate per raccogliere flussi di eventi da dispositivi collegati, servizi e applicazioni. Archiviazione Blob Azure può essere utilizzati come un'origine di input per il caricamento di massa dei dati come flusso.  
- **Dati di riferimento**: Analitica flusso supporta un secondo tipo di dati di riferimento denominato input supplementare.  Anziché dati in movimento, questi dati sono statico o rallentano la modifica.  In genere utilizzato per eseguire ricerche e correlazioni con flussi di dati per creare un set di dati più ampio.  Archiviazione Blob Azure è attualmente l'origine di input supportato solo per i dati di riferimento.  

Per aggiungere un input per il lavoro Analitica flusso:

1. Nel portale di Azure fare clic su **input** e quindi fare clic su **Aggiungi un Input** nel processo di flusso Analitica.

    ![Azure portale classico - aggiungere un input.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Nel portale di Azure fare clic sul riquadro di **input** nel processo di flusso Analitica.  

    ![Portale di Azure - aggiungere input di dati.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Specificare il tipo di input: **flusso di dati** o **dati di riferimento**.

    ![Aggiungere i dati corretti di input, in streaming o riferimento a](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Aggiungere i dati corretti di input, in streaming o riferimento a](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Se si crea un input flusso di dati, specificare il tipo di origine per l'input.  Durante la creazione di dati di riferimento come solo Blob lo spazio di archiviazione è supportata in questa fase, è possibile saltare questo passaggio.

    ![Aggiunta di input di dati del flusso di dati](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Aggiunta di input di dati del flusso di dati](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Specificare un nome descrittivo per questo input nella casella Alias Input.  Questo nome da utilizzare nella query del processo in un secondo momento per fare riferimento all'input.

    Compilare il resto della proprietà della connessione necessari per connettersi all'origine dati. Questi campi possono variare in base al tipo del tipo di input e di origine e sono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).  

    ![Aggiunta di input di dati di evento hub](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Specificare le impostazioni di serializzazione per i dati di input:
    - Per assicurarsi che le query funzionano nel modo previsto, specificare il **Formato di serializzazione evento** dei dati in arrivo.  Formati di serializzazione supportate sono JSON, CSV e Avro.
    - Verificare la **codifica** per i dati.  UTF-8 è il formato di codifica supportato solo in questo momento.

    ![Impostazioni della serializzazione di dati per i dati di input](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Impostazioni della serializzazione di dati per i dati di input](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. Dopo aver completato la creazione di input, verrà verificata Analitica flusso che è possibile connettersi all'origine di input.  È possibile visualizzare lo stato dell'operazione di Test connessione nell'hub notifica.

    ![Test connessione la trasmissione immissione di dati](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Test connessione la trasmissione immissione di dati](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>È possibile ottenere assistenza con il flusso dei dati di input
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
