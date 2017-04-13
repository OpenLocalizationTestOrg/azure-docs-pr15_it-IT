<properties
    pageTitle="Reimpostare i modelli di apprendimento a livello di programmazione | Microsoft Azure"
    description="Informazioni su come reimpostare un modello e aggiornare il servizio web per utilizzare il modello appena esperto in Azure apprendimento a livello di programmazione."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Reimpostare i modelli di apprendimento a livello di programmazione  

In questa procedura dettagliata, si imparerà a livello di programmazione reimpostare un servizio Web di Azure Machine Learning utilizzando c# e il servizio di esecuzione Batch risorse nel computer.

Dopo avere training del modello, le procedure dettagliate seguenti viene illustrato come aggiornare il modello del servizio web stima:

- Se è stato distribuito un servizio web classica nel portale di computer servizi Web di risorse, vedere [reimpostare un servizio web classica](machine-learning-retrain-a-classic-web-service.md). 
- Se è stato distribuito un nuovo servizio web, vedere [reimpostare un nuovo servizio web utilizzando i cmdlet Gestione risorse computer](machine-learning-retrain-new-web-service-using-powershell.md).

Per una panoramica del processo dei, vedere [reimpostare un modello di apprendimento computer](machine-learning-retrain-machine-learning-model.md).

Se si vuole iniziare con il servizio web nuova caratteristica Gestione risorse Azure in base a esistente, vedere [reimpostare un servizio web previsione esistente](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Creare una prova di formazione
 
In questo esempio si utilizzerà "esempio 5: Evaluate treno, Test, per la classificazione binaria: adulto set di dati" dagli esempi di Microsoft Azure apprendimento. 
    
Per creare la prova:

1.  Eseguire l'accesso a Microsoft Azure lavorazione Studio di apprendimento. 
2.  Nell'angolo destro nella parte inferiore del dashboard, fare clic su **Nuovo**.
3.  Selezionare Microsoft Samples esempio 5.
4.  Per rinominare la prova, nella parte superiore dell'area di prova, selezionare il nome di prova "esempio 5: Evaluate treno, Test, per la classificazione binaria: adulto set di dati".
5.  Modello di tipo censimento.
6.  Nella parte inferiore della prova area di lavoro, fare clic su **Esegui**.
7.  Fare clic su **Imposta servizio web** e selezionare **servizio web Retraining**. 

    ![Primi esperimenti.][2]

Diagramma 2: Iniziale prova.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Creazione di una prova di Office e la pubblicazione come un servizio web  

Creare una prova di Predicative.

1.  Nella parte inferiore dell'area di prova, fare clic **Su servizio Web** e selezionare **Previsione servizio Web**. Verranno salvate il modello come modello esperti e aggiunge i moduli di Input e di Output servizio web. 
2.  Fare clic su **Esegui**. 
3.  Al termine dell'esecuzione di prova, fare clic su **Servizio Web distribuzione [classica]** o **Distribuire un servizio Web [New]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>La distribuzione di prova formazione come un servizio web di formazione

Per reimpostare il modello di formazione, è necessario distribuire la prova di formazione che è stato creato come un servizio web Retraining. Questo servizio web è necessario un modulo di *Output servizio Web* connesso alla * [Modello treno] [ train-model] * modulo, per poter creare nuovi modelli di esperti.

1. Per tornare alla prova formazione, fare clic sull'icona esperimenti nel riquadro sinistro, quindi fare clic su prova denominato censimento modello.  
2. Nella casella di ricerca di elementi di prova di ricerca, digitare servizio web. 
3. Trascinare un modulo di *Input del servizio Web* nell'area di lavoro di prova e connettere l'output al modulo *Dati mancanti libera* .  In questo modo che i dati viene elaborati esattamente come i dati originali di formazione.
4. Trascinare due moduli di *servizio web Output* nell'area di lavoro di prova. Connettere l'output del modulo *Modello treno* uno e l'output del modulo di *Valutare modello* a altro. L'output di servizio web per **Il modello treno** offre il nuovo modello di esperto. L'output associato al **Modello di valutare** restituisce output del modulo, ovvero i risultati delle prestazioni.
5. Fare clic su **Esegui**. 

Successivamente è necessario distribuire prova formazione come servizio web che produce un modello di formazione e risultati del modello di valutazione. A tale scopo, il successivo insieme di azioni dipendono se si lavora con un servizio web classica o di un nuovo servizio web.  
  
**Servizio web classica**

Nella parte inferiore dell'area di prova, fare clic **Su servizio Web** e selezionare **Distribuire il servizio Web [classica]**. Il servizio Web **Dashboard** viene visualizzato con la chiave dell'API e la pagina di Guida API per l'esecuzione Batch. Solo il metodo di esecuzione Batch può essere utilizzato per la creazione di modelli di esperti.

**Nuovo servizio web**

Nella parte inferiore dell'area di prova, fare clic **Su servizio Web** e selezionare **Distribuire [New] servizio Web**. Portale di Azure servizio Web computer servizi Web di risorse viene visualizzata la pagina del servizio web Distribuisci. Digitare un nome per il servizio web e scegliere un piano di pagamento, quindi fare clic su **Distribuisci**. Solo il metodo di esecuzione del Batch può essere utilizzato per la creazione di modelli di esperti

In entrambi i casi, al termine dell'esecuzione, prova il flusso di lavoro risultante dovrebbe essere come segue:

![Flusso di lavoro risultante dopo l'esecuzione.][4]

Diagramma 3: Risultante dopo l'esecuzione del flusso di lavoro.

## <a name="retrain-the-model-with-new-data-using-bes"></a>Reimpostare il modello con i nuovi dati usando BES

In questo esempio si utilizza c# per creare l'applicazione i. È anche possibile usare il codice di esempio Python o R per eseguire questa operazione.

Per chiamare API formazione:

1. Creare un'applicazione Console c# in Visual Studio (-> Nuovo progetto -> Windows Desktop -> applicazione Console).
2.  Accedere al portale del servizio Web risorse computer.
3.  Se si lavora con un servizio web classica, fare clic su **Servizi Web classica**.
    1.  Fare clic su servizio web in uso.
    2.  Fare clic su endpoint predefinito.
    3.  Fare clic su **utilizzare**.
    4.  Nella parte inferiore della pagina **consumo** , nella sezione **Codice di esempio** fare clic su **Batch**.
    5.  Procedere al passaggio 5 di questa procedura.
4.  Se si lavora con un nuovo servizio web, fare clic su **Servizi Web**.
    1.  Fare clic su servizio web in uso.
    2.  Fare clic su **utilizzare**.
    3.  Nella parte inferiore della pagina consumo, nella sezione **Codice di esempio** fare clic su **Batch**.
5.  Copiare il codice c# per l'esecuzione batch e incollarla nel file Program.cs, assicurarsi prima di tutto lo spazio dei nomi rimane intatta.

Aggiungere il pacchetto Nuget Microsoft.AspNet.WebApi.Client come specificato nei commenti. Per aggiungere il riferimento alla Microsoft.WindowsAzure.Storage.dll, potrebbero prima di tutto necessario installare la libreria di client per i servizi Microsoft Azure lo spazio di archiviazione. Per ulteriori informazioni, vedere [Servizi di archiviazione di Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Aggiornare la dichiarazione apikey

Individuare la dichiarazione di **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Nella sezione **informazioni di base consumo** della pagina **consumo** individuare la chiave primaria e copiare la dichiarazione di **apikey** .

### <a name="update-the-azure-storage-information"></a>Aggiornare le informazioni dello spazio di archiviazione di Azure

Il codice di esempio BES carica un file da un'unità locale (ad esempio "C:\temp\CensusIpnput.csv") per lo spazio di archiviazione di Azure, viene elaborato e i risultati vengono scritte allo spazio di archiviazione Azure.  

Per eseguire questa operazione, è necessario recuperare le informazioni di nome, chiave e contenitore account lo spazio di archiviazione per l'account di archiviazione dal portale di Azure classico e aggiornamento dei valori corrispondenti nel codice. 

1. Accedere al portale di Azure classico.
1. Nella colonna di spostamento sinistra, fare clic su **archiviazione**.
1. Nell'elenco di account di archiviazione, selezionarne uno per archiviare il modello retrained.
1. Nella parte inferiore della pagina, fare clic su **Gestisci i tasti di scelta**.
1. Copiare e salvare la **Chiave primaria di Access** e chiudere la finestra di dialogo. 
1. Nella parte superiore della pagina, fare clic su **contenitori**.
1. Selezionare un contenitore esistente o crearne uno nuovo e salvare il nome.

Individuare le dichiarazioni *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e aggiornare i valori salvati dal portale di Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
È inoltre necessario verificare che il file di input è disponibile nel percorso specificato nel codice. 

### <a name="specify-the-output-location"></a>Specificare il percorso di output

Quando si specifica il percorso di output nel Payload richiesta, è necessario specificare l'estensione del file specificato in *RelativeLocation* come ilearner. 

Vedere nell'esempio seguente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] I nomi delle posizioni dei output potrebbero essere diversi da quelli in questa procedura dettagliata in base all'ordine in cui è stato aggiunto i moduli di output servizio web. Dopo aver configurato la formazione sperimentare due uscite, i risultati includono informazioni sulla posizione di archiviazione per entrambi gli elementi.  

![Formazione output][6]

Diagramma 4: Formazione output.

## <a name="evaluate-the-retraining-results"></a>Valutare i risultati
 
Quando si esegue l'applicazione, l'output include il token di URL e SA necessario per accedere ai risultati di valutazione.

È possibile visualizzare i risultati delle prestazioni del modello retrained combinando la *BaseLocation*, *RelativeLocation*e *SasBlobToken* dalla casella risultati di output per *USCITA2* (come illustrato nell'immagine di output i precedente) e incollare l'URL completo nella barra degli indirizzi del browser.  

Esaminare i risultati per determinare se il modello appena esperto esegue sufficiente per sostituire quello esistente.

Copiare la *BaseLocation*, *RelativeLocation*e *SasBlobToken* i risultati di output, verranno utilizzati durante il processo dei.

## <a name="next-steps"></a>Passaggi successivi

Se il servizio web previsione è stata distribuita facendo clic su **Servizio Web distribuzione [classica]**, vedere [reimpostare un servizio web classica](machine-learning-retrain-a-classic-web-service.md).

Se il servizio web previsione è stata distribuita facendo clic su **Distribuire [New] servizio Web**, vedere [reimpostare un nuovo servizio web utilizzando i cmdlet Gestione risorse computer](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/