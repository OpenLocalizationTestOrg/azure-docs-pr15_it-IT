<properties
    pageTitle="Analisi di valutazione in utilizzando Azure flusso Analitica e apprendimento Azure | Microsoft Azure"
    description="Come usare una funzione definita dall'utente e apprendimento in un processo di flusso Analitica"
    keywords=""
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
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analisi di valutazione in utilizzando Azure flusso Analitica e apprendimento Azure #

In questo articolo consentono di configurare rapidamente un processo semplice Azure flusso Analitica, con l'integrazione di apprendimento Azure. Si verrà usare un modello di apprendimento analitica di valutazione in dalla raccolta di Business Intelligence Cortana per analizzare i dati del testo di flusso e determinare il punteggio di valutazione in tempo reale. Le informazioni in questo articolo consentono di comprendere gli scenari, ad esempio analitica di valutazione in tempo reale il flusso di dati di Twitter, analizzare i record del cliente chat con personale di supporto e valutare i commenti nel forum, blog e i video, oltre a molti altri in tempo reale, previsione punteggio scenari.

In questo articolo offre un file CSV di esempio con il testo come input nell'archiviazione Blob Azure, illustrato nell'immagine seguente. Il processo applica il modello di valutazione in analitica come una funzione definita dall'utente (utente) i dati di testo di esempio dall'archivio blob. Il risultato finale viene inserito nello stesso archivio blob in un altro file CSV. 

![Flusso Analitica lavorazione risorse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Nella figura seguente viene illustrata questa configurazione. Per uno scenario più realistico, è possibile sostituire lo spazio di archiviazione Blob con streaming Twitter dati da un input Azure evento hub. Inoltre, è possibile creare una visualizzazione in tempo reale di [Microsoft Power BI](https://powerbi.microsoft.com/) di ciascun aggregazione.    

![Flusso Analitica lavorazione risorse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Prerequisiti di

Prerequisiti per completare le attività vengono illustrate in questo articolo sono i seguenti:

-   Un abbonamento attivo a Azure.
-   Un file CSV con alcuni dati al suo interno. È possibile scaricare il file illustrato nella figura 1 da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv)oppure è possibile creare un file. In questo articolo si presuppone utilizzare quello fornito per il download in GitHub.

Alto livello, per completare le attività illustrate in questo articolo è necessario eseguire le operazioni seguenti:

1.  Caricare il file CSV di input a archiviazione Blob Azure.
2.  Aggiungere un modello di valutazione in analitica dalla raccolta di Business Intelligence Cortana nell'area di lavoro di apprendimento Azure.
3.  Distribuire il modello come un servizio web nell'area di lavoro di apprendimento.
4.  Creare un processo di flusso Analitica che chiama il servizio web come una funzione per determinare valutazione in per il testo di input.
5.  Avviare il processo di flusso Analitica e osservare l'output.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Caricare il file CSV di input a archiviazione Blob

Per questo passaggio, è possibile usare qualsiasi file CSV, ad esempio quello già specificato come disponibile per il download in GitHub. È possibile usare [Esplora archivi Azure](http://storageexplorer.com/) o Visual Studio per caricare il file oppure è possibile utilizzare codice personalizzato. Serve esempi in base a Visual Studio.

1.  In Visual Studio, fare clic su **Azure** > **lo spazio di archiviazione** > **Allegare esterni lo spazio di archiviazione**. Immettere un **nome dell'Account** e la **chiave Account**.  

    ![Flusso Analitica lavorazione risorse, Esplora Server](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Espandere archiviazione allegato nel passaggio 1, fare clic su **Crea contenitore Blob**e quindi immettere un nome logico. Dopo aver creato il contenitore, aprirlo per visualizzarne il contenuto. (Sarà vuoto a questo punto).  

    ![Flusso apprendimento Analitica, creare blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Per caricare il file CSV, fare clic su **Carica Blob**e quindi fare clic su **file dal disco locale**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Aggiungere il modello di valutazione in analitica della raccolta di Business Intelligence Cortana

1.  Scaricare il [modello di valutazione in previsione analitica](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dalla raccolta di Business Intelligence Cortana.  
2.  Nel computer risorse Studio, fare clic su **Apri in Studio**.  

    ![Flusso apprendimento Analitica, Apri Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Accedere a passare all'area di lavoro. Selezionare il percorso che meglio si adatta al percorso desiderato.
4.  Fare clic su **Esegui** nella parte inferiore della pagina.  
5.  Dopo il processo viene eseguito correttamente, fare clic su **Servizio Web di distribuzione**.
6.  È possibile utilizzare il modello di valutazione in analitica. Per convalidare, fare clic sul pulsante **Test** e fornire input di testo, ad esempio, "Mi piace Microsoft". Il test deve restituire un risultato simile al seguente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Flusso Analitica apprendimento, dati di analisi](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Nella colonna **App** fare clic sul collegamento per **Excel 2010 o cartella di lavoro precedente** ottenere il codice di API e l'URL è necessario in seguito consente di impostare il processo di flusso Analitica. (Questo passaggio è necessario solo per utilizzare un modello di apprendimento dall'area di lavoro di un altro account Azure. In questo articolo si presuppone che questo è il caso, per risolvere questo scenario.)  

Si noti chiave del servizio web URL e accesso dal file di Excel scaricato, come illustrato di seguito:  

![Flusso Analitica apprendimento, visualizzazione rapida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creare un processo di flusso Analitica che viene utilizzato il modello di apprendimento

1.  Accedere al [portale di Azure](https://manage.windowsazure.com).  
2.  Fare clic su **Nuovo** > **servizi dati** > **Analitica flusso** > **Creazione rapida**. Immettere un nome per il lavoro in **Nome processo**, immettere il paese appropriato per il processo **nell'area**e quindi selezionare l'account in **Account di archiviazione monitoraggio internazionali**.    
3.  Una volta creato il processo, nella scheda **input** fare clic su **Aggiungi un Input**.  

    ![Flusso apprendimento Analitica, aggiungere input di apprendimento](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  La prima pagina della procedura guidata **Aggiungere Input** , fare clic su **flusso di dati**e quindi fare clic su **Avanti**. Nella pagina successiva, fare clic su **Archiviazione Blob** come input e quindi fare clic su **Avanti**.  
5.  Nella pagina **Impostazioni di archiviazione Blob** della procedura guidata specificare il nome di un contenitore di blob di un account di archiviazione definita in precedenza durante il caricamento dei dati. Fare clic su **Avanti**. **Formato di serializzazione evento**, fare clic su **CSV**. Accettare i valori predefiniti per il resto della pagina **Impostazioni serializzazione** . Fare clic su **OK**.  
6.  Nella scheda **output** , fare clic su **Aggiungi un Output**.  

    ![Flusso apprendimento Analitica, aggiungere output](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Fare clic su **Archiviazione Blob**e quindi immettere gli stessi parametri, ad eccezione del contenitore. Il valore di **Input** è stato configurato per la lettura dal contenitore denominato "test" in cui è stato caricato il file **CSV** . Per l' **Output**, immettere "testoutput". I nomi dei contenitori devono essere diversi. Verificare l'esistenza di questo contenitore.     
8.  Fare clic su **Avanti** per configurare le **Impostazioni serializzazione**dell'output. Come con l' **Input**, fare clic su **CSV**e quindi fare clic su **OK** .
9.  Nella scheda **funzioni** , fare clic su **Aggiungi una funzione di risorse del computer**.  

    ![Flusso apprendimento Analitica, aggiungere la funzione di apprendimento](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Nella pagina **Impostazioni del servizio Web risorse computer** individuare il servizio web, endpoint predefinito e area di lavoro di apprendimento. Applicare le impostazioni manualmente per acquisire familiarità con la configurazione di un servizio web per qualsiasi area di lavoro, purché si conosce l'URL e la chiave dell'API di questo articolo. Immettere l' endpoint **URL** e la **chiave dell'API**. Fare clic su **OK**.    

    ![Apprendimento flusso Analitica, apprendimento servizio web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. Nella scheda **Query** , modificare la query come segue:    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Fare clic su **Salva** per salvare la query.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Avviare il processo di flusso Analitica e osservare l'output

1.  Fare clic su **Avvia** nella parte inferiore della pagina del processo.
2.  Nella **Finestra di dialogo avviare Query**, fare clic su **Ora personalizzati**e quindi selezionare un'ora prima di durante il caricamento il file CSV archiviazione Blob. Fare clic su **OK**.  

    ![Apprendimento flusso Analitica, ora personalizzati](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Passare a archiviazione Blob utilizzando lo strumento utilizzato per caricare il file CSV, ad esempio Visual Studio.
4.  Alcuni minuti dopo l'avvio del processo, viene creato il contenitore di output e viene caricato un file CSV.  
5.  Aprire il file nell'editor CSV predefinito. Deve essere visualizzata sarà simile al seguente:  

    ![Visualizzazione Analitica flusso apprendimento, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusioni

In questo articolo viene illustrato come creare un processo di flusso Analitica che legge i dati del testo di flusso e applica analitica di valutazione in ai dati in tempo reale. È possibile eseguire tutte queste operazioni senza dover preoccuparsi di complessità della creazione di un modello di valutazione in analitica. Questo è uno dei vantaggi della famiglia di prodotti di Business Intelligence Cortana.

È inoltre possibile visualizzare metriche relativi alla funzione apprendimento Azure. A tale scopo, fare clic sulla scheda **Monitor** . Vengono visualizzate tre metriche relativi alla funzione.  

- **Funzione richieste** indica il numero di richieste inviate a un servizio web di apprendimento.  
- **Funzione eventi** indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta a un servizio web di apprendimento contiene più di 1.000 eventi.  
- **Le richieste di funzione di errore** indica il numero di richieste non riuscite al servizio web di apprendimento.  

    ![Flusso Analitica apprendimento, visualizzazione del monitor apprendimento](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
