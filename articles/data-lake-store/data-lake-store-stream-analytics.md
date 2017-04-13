<properties
   pageTitle="Il flusso di dati da Analitica flusso in archivio dati Lake | Azure"
   description="Utilizzare Azure flusso Analitica per il flusso di dati in archivio Lake dati di Azure"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Dati relativi al flusso da Azure lo spazio di archiviazione Blob all'archivio dati Lake tramite Analitica flusso Azure

In questo articolo si imparerà usare archivio Lake dati di Azure come un output per un processo di Azure flusso Analitica. In questo articolo viene illustrato un semplice scenario che legge i dati da un blob Azure lo spazio di archiviazione (input) e scrive i dati in archivio Lake dati (output).

>[AZURE.NOTE] In questa fase, la creazione e la configurazione di output di archivio di dati Lake per Analitica flusso è supportata solo nel [Portale classica Azure](https://manage.windowsazure.com). Di conseguenza, alcune parti di questa esercitazione verranno utilizzato il portale classica Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Attivare l'abbonamento Azure** per dati Lake archivio Public Preview. Vedere [le istruzioni](data-lake-store-get-started-portal.md#signup).

- **Account di archiviazione di azure**. Utilizzare un contenitore di blob da questo account per i dati per un processo Analitica flusso di input. Per questa esercitazione, si supponga che si crea un account di archiviazione denominata **datalakestoreasa** e un contenitore all'interno dell'account denominato **datalakestoreasacontainer**. Dopo aver creato il contenitore, caricare un file di dati di esempio. È possibile ottenere un file di dati di esempio dal [Repository fra Lake dei dati di Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). È possibile usare vari client, ad esempio [Esplora archivi Azure](http://storageexplorer.com/), per caricare dati in un contenitore di blob.

    >[AZURE.NOTE] Se si crea l'account dal portale di Azure, assicurarsi che viene creato con il modello di distribuzione **classica** . In questo modo che l'account di archiviazione è accessibile dal portale classica di Azure, perché è la funzione per creare un processo di flusso Analitica. Per istruzioni su come creare un account di archiviazione dal portale di Azure utilizzando la distribuzione classica, vedere [creare un account di archiviazione Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > In alternativa, è possibile creare un account di archiviazione dal portale classica di Azure.

- **Account azure dati Lake Store**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Creare un processo Analitica flusso

Inizierà creando un processo di flusso Analitica che include un'origine di input e di destinazione di uscita. Per questa esercitazione, l'origine è un contenitore di blob Azure e la destinazione è archivio Lake dati.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).

2. Da sinistra nella parte inferiore dello schermo, fare clic su **Nuovo**, **ODS**, **Flusso Analitica**, **Creazione rapida**. Fornire i valori, come illustrato di seguito e quindi fare clic su **Crea flusso Analitica processo**.

    ![Creare un processo Analitica flusso] (./media/data-lake-store-stream-analytics/create.job.png "Creare un processo di flusso Analitica")

## <a name="create-a-blob-input-for-the-job"></a>Creare un input Blob per il processo

1. Aprire la pagina per il processo di flusso Analitica, fare clic sulla scheda **input** e quindi fare clic su **Aggiungi un Input** per avviare una procedura guidata.

2. Nella pagina **Aggiungi un input per il lavoro** , selezionare **flusso di dati**e quindi fare clic sulla freccia avanti.

    ![Aggiungere un input al proprio lavoro] (./media/data-lake-store-stream-analytics/create.input.1.png "Aggiungere un input al proprio lavoro")

3. Nella pagina **Aggiungi un flusso di dati per il lavoro** , selezionare **archiviazione Blob**e quindi fare clic sulla freccia avanti.

    ![Aggiungere un flusso di dati per il processo] (./media/data-lake-store-stream-analytics/create.input.2.png "Aggiungere un flusso di dati per il processo")

4. Nella pagina **impostazioni di archiviazione Blob** fornire dettagli per l'archiviazione blob che verrà utilizzata come origine dati di input.

    ![Specificare le impostazioni di archiviazione blob] (./media/data-lake-store-stream-analytics/create.input.3.png "Specificare le impostazioni di archiviazione blob")

    * **Invio di un alias di input**. Si tratta di un nome univoco fornito per il processo di input.
    * **Selezionare un account di archiviazione**. Verificare che l'account di archiviazione nella stessa regione il processo di flusso Analitica o si comporta costi aggiuntivi di spostamento dei dati tra le aree.
    * **Specifica un contenitore di archiviazione**. È possibile scegliere di creare un nuovo contenitore o selezionare un contenitore esistente.

    Fare clic sulla freccia avanti.

5. Nella pagina **impostazioni di serializzazione** impostare il formato di serializzazione come **CSV**, delimitatore **scheda**, come codifica come **UTF8**e quindi fare clic sul segno di spunta.

    ![Specificare le impostazioni della serializzazione] (./media/data-lake-store-stream-analytics/create.input.4.png "Specificare le impostazioni della serializzazione")

6. Dopo aver con la procedura guidata, verrà aggiunto l'input blob sotto la scheda **input** e la colonna **diagnosi** deve essere visualizzata **OK**. È possibile verificare la connessione per l'input in modo esplicito anche mediante il pulsante **Test connessione** nella parte inferiore.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Creare un archivio Lake dati di output per il processo

1. Aprire la pagina per il processo di flusso Analitica, fare clic sulla scheda **output** e quindi fare clic su **Aggiungi un Output** per avviare una procedura guidata.

2. Nella pagina **Aggiungi un output per il processo** selezionare **Archivio Lake dati**e quindi fare clic sulla freccia avanti.

    ![Aggiungere un output per il lavoro] (./media/data-lake-store-stream-analytics/create.output.1.png "Aggiungere un output per il lavoro")

3. Nella pagina sulla **connessione di autorizzazione** , se è già stato creato un account di archivio Lake dati, fare clic su **Autorizzare ora**. In caso contrario, fare clic su **Iscriviti ora** per creare un nuovo account. Per questa esercitazione, supponiamo già un account di archivio di dati Lake creato (come indicato nella sezione dei prerequisiti). Verrà automaticamente autorizzato utilizzando le credenziali con cui è stato effettuato l'accesso al portale classica Azure.

    ![Autorizzare archivio Lake dati.] (./media/data-lake-store-stream-analytics/create.output.2.png "Autorizzare archivio Lake dati.")

4. Nella pagina **Impostazioni relative all'archivio dati Lake** immettere le informazioni come illustrato nella schermata seguente.

    ![Impostazioni relative all'archivio di Lake dati specifica] (./media/data-lake-store-stream-analytics/create.output.3.png "Impostazioni relative all'archivio di Lake dati specifica")

    * **Invio di un alias di output**. Si tratta di un nome univoco che disponibili per l'output di processo.
    * **Specificare un account di archivio di dati Lake**. È necessario avere già creato, come indicato nella sezione dei prerequisiti.
    * **Specificare un motivo prefisso percorso**. È necessario per identificare i file di output scritta per l'archivio Lake dati tramite il processo di flusso Analitica. Poiché i titoli di output scritto dal processo sono in formato GUID, che include un prefisso consenta di identificare l'output scritto. Se si desidera includere un indicatore di data e ora come parte del prefisso assicurarsi di includere `{date}/{time}` nel modello di prefisso. Se si include questa operazione, i campi **Formato di data **e **Ora formato** abilitati ed è possibile selezionare il formato desiderato.

    Fare clic sulla freccia avanti.

5. Nella pagina **Impostazioni serializzazione** , impostare il formato di serializzazione come **CSV**, delimitatore **scheda**, come codifica come **UTF8**e quindi fare clic sul segno di spunta.

    ![Specificare il formato di output] (./media/data-lake-store-stream-analytics/create.output.4.png "Specificare il formato di output")

6. Dopo aver con la procedura guidata, l'output di archivio di dati Lake verrà aggiunto nella scheda **output** e la colonna **diagnosi** deve essere visualizzata **OK**. È possibile verificare la connessione per l'output in modo esplicito anche tramite il pulsante **Test connessione** nella parte inferiore.

## <a name="run-the-stream-analytics-job"></a>Eseguire il processo di flusso Analitica

Per eseguire un processo di flusso Analitica, è necessario eseguire una query nella scheda Query. Per questa esercitazione, è possibile eseguire la query di esempio sostituendo i segnaposto con il processo di input e di output alias, come illustrato nella schermata seguente.

![Eseguire query] (./media/data-lake-store-stream-analytics/run.query.png "Eseguire query")

Fare clic su **Salva** dalla parte inferiore dello schermo e quindi fare clic su **Start**. Nella finestra di dialogo selezionare **Ora personalizzati**e quindi selezionare una data passata, ad esempio **1/1/2016**. Fare clic sul segno di spunta per avviare il processo. È possibile richiedere qualche minuto per avviare il processo.

![Impostare il tempo processo] (./media/data-lake-store-stream-analytics/run.query.2.png "Impostare il tempo processo")

Dopo che si avvia il processo, fare clic sulla scheda **Monitor** per visualizzare la modalità di elaborazione dei dati.

![Processo di monitor] (./media/data-lake-store-stream-analytics/run.query.3.png "Processo di monitor")

Infine, è possibile usare il [Portale di Azure](https://portal.azure.com) per aprire l'account di archivio Lake dati e verificare se i dati è stato scritto all'account.

![Output di verifica] (./media/data-lake-store-stream-analytics/run.query.4.png "Verifica output")

Nel riquadro di esplorazione di dati, le impostazioni di output si noti che l'output è scritto in una cartella come specificato nell'archivio dati Lake (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Vedere anche

* [Creare un cluster di HDInsight per l'uso dei dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
