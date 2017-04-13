<properties
    pageTitle="Reimpostare un servizio web stima esistente | Microsoft Azure"
    description="Informazioni su come reimpostare un modello e aggiornare il servizio web per utilizzare il modello appena esperto in Azure apprendimento."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Reimpostare un servizio web stima esistente

In questo documento viene descritto il processo i per lo scenario seguente:

* Si dispone di una prova di formazione e una stima vedere distribuiti come un servizio web operationalized.
* Si dispone di nuovi dati che si desidera il servizio web stima da utilizzare per eseguire la classificazione.

Iniziare con il servizio web esistente ed esperimenti, è necessario eseguire la procedura seguente:

1. Aggiornare il modello.
    1. Modificare la prova di formazione per consentire di output e input di servizio web.
    2. Distribuire prova formazione come i servizio web.
    3. Consente di reimpostare il modello Batch esecuzione servizio (BES di prova formazione).
2. Utilizzare i cmdlet di Azure Machine Learning PowerShell per aggiornare prova previsione.
    1.  Accedere al proprio account Manager delle risorse di Azure.
    2.  È possibile ottenere la definizione di servizio web.
    3.  Esportare la definizione di servizio web come JSON.
    4.  Aggiornare il riferimento a blob ilearner nel JSON.
    5.  È possibile importare la JSON in definizione di un servizio web.
    6.  Aggiornare il servizio web con la definizione di un nuovo servizio web.

## <a name="deploy-the-training-experiment"></a>Distribuire prova formazione

Per distribuire prova formazione come i servizio web, è necessario aggiungere web servizio ingressi e uscite nel modello. Mediante la connessione di un modulo di *Output servizio Web* per la prova * [Modello treno] [ train-model] * modulo, si attiva la prova di formazione per produrre un nuovo modello di formazione che è possibile utilizzare la prova previsione. Se si dispone di un modulo di *Valutare modello* , è anche possibile allegare l'output del servizio web per ottenere i risultati di valutazione come output.

Per aggiornare la prova di formazione:

1. Connettere un modulo di *Input di servizio Web* per l'input di dati (ad esempio, un modulo *Dati mancanti libera* ). In genere, si desidera assicurarsi che i dati di input viene elaborati nello stesso modo i dati originali di formazione.
2. Collegare un modulo di *Output servizio Web* per l'output del modulo di *Modello treno* .
3. Se si dispone di un modulo di *Modello di valutazione* e si desidera restituire i risultati di valutazione, collegare un modulo di *Output servizio Web* per l'output del modulo di *Valutare modello* .

Eseguire la prova.

Successivamente, è necessario distribuire prova formazione come servizio web che produce un modello di formazione e risultati del modello di valutazione.  

Nella parte inferiore dell'area di prova, fare clic **Su servizio Web**e quindi selezionare **Distribuire [New] servizio Web**. Portale di servizi Web di Windows Azure Machine Learning viene visualizzata la pagina **Distribuire servizio Web** . Digitare un nome per il servizio web, scegliere un piano di pagamento e quindi fare clic su **Distribuisci**. È possibile utilizzare il metodo di esecuzione Batch solo per la creazione di modelli di esperti.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Reimpostare il modello con i nuovi dati usando BES

In questo esempio si utilizza c# per creare l'applicazione i. Utilizzare il codice di esempio Python o R per eseguire questa operazione.

Per chiamare i API:

1. Creare un'applicazione console c# in Visual Studio (**Nuovo** > **progetto** > **Desktop Windows** > **Applicazione Console**).
2.  Accedere al portale di computer servizi Web di risorse.
3.  Fare clic su servizio web che si sta lavorando.
2.  Fare clic su **utilizzare**.
3.  Nella parte inferiore della pagina **consumo** , nella sezione **Codice di esempio** fare clic su **Batch**.
5.  Copiare il codice c# per l'esecuzione batch e incollarla nel file Program.cs. Assicurarsi che lo spazio dei nomi rimane intatta.

Aggiungere il pacchetto NuGet Microsoft.AspNet.WebApi.Client, come specificato nei commenti. Per aggiungere il riferimento alla Microsoft.WindowsAzure.Storage.dll, potrebbero prima di tutto necessario installare la [libreria di client per i servizi di archiviazione Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La schermata seguente mostra la pagina **consumo** nel portale di servizi Web di Windows Azure computer risorse.

![Utilizzo di pagina][1]

### <a name="update-the-apikey-declaration"></a>Aggiornare la dichiarazione apikey

Individuare la dichiarazione **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Nella sezione **informazioni di base consumo** della pagina **consumo** individuare la chiave primaria e copiare la dichiarazione di **apikey** .

### <a name="update-the-azure-storage-information"></a>Aggiornare le informazioni dello spazio di archiviazione di Azure

Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\temp\CensusIpnput.csv") per lo spazio di archiviazione di Azure, viene elaborato e i risultati vengono scritte allo spazio di archiviazione Azure.  

Per aggiornare le informazioni dello spazio di archiviazione di Azure, è necessario recuperare il nome dell'account di archiviazione, chiave e le informazioni sul contenitore per il proprio account di archiviazione dal portale di classica Azure e quindi Aggiorna correspondi dopo avere eseguito la prova il flusso di lavoro risultante dovrebbe essere simile al seguente:

![Flusso di lavoro risultante dopo l'esecuzione][4]valori di gas nel codice.

1. Accedere al portale di classica Azure.
1. Nella colonna di spostamento sinistra, fare clic su **archiviazione**.
1. Nell'elenco di account di archiviazione, selezionarne uno per archiviare il modello retrained.
1. Nella parte inferiore della pagina, fare clic su **Gestisci i tasti di scelta**.
1. Copiare e salvare la **Chiave primaria di Access** e chiudere la finestra di dialogo.
1. Nella parte superiore della pagina, fare clic su **contenitori**.
1. Selezionare un contenitore esistente o crearne uno nuovo e salvare il nome.

Individuare la *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* dichiarazioni e aggiornare i valori che è stato salvato dal portale di classica.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

È anche necessario assicurarsi che il file di input è disponibile nel percorso specificato nel codice.

### <a name="specify-the-output-location"></a>Specificare il percorso di output

Quando si specifica il percorso di output nel Payload richiesta, è necessario specificare l'estensione del file specificato nel *RelativeLocation* come `ilearner`. Vedere nell'esempio seguente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Di seguito è illustrato un esempio di output i: ![formazione output][6]

## <a name="evaluate-the-retraining-results"></a>Valutare i risultati

Quando si esegue l'applicazione, l'output include il token di firme accesso condiviso che sono necessari per accedere ai risultati di valutazione e l'URL.

È possibile visualizzare i risultati delle prestazioni del modello retrained combinando la *BaseLocation*, *RelativeLocation*e *SasBlobToken* dalla casella risultati di output per *USCITA2* (come illustrato nell'immagine di output i precedente) e incollare l'URL completo nella barra degli indirizzi del browser.  

Esaminare i risultati per determinare se il modello appena esperto esegue sufficiente per sostituire quello esistente.

Copiare la *BaseLocation*, *RelativeLocation*e *SasBlobToken* i risultati di output.

## <a name="retrain-the-web-service"></a>Reimpostare il servizio web

Quando si reimpostare un nuovo servizio web, aggiornare la definizione di servizio web previsione per il nuovo modello di esperto fare riferimento. Definizione del servizio web è una rappresentazione interna del modello di esperti del servizio web e non può essere modificata direttamente. Verificare che si desidera recuperare la definizione di servizio web per la stima prova e non la prova di formazione.

## <a name="sign-in-to-azure-resource-manager"></a>Accedere a Gestione risorse Azure

È necessario prima di tutto accedere al proprio account Azure dall'interno dell'ambiente di PowerShell utilizzando il cmdlet [AzureRmAccount Aggiungi](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Ottenere l'oggetto di definizione del servizio Web

Successivamente, è possibile ottenere l'oggetto di definizione del servizio Web chiamando il cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Per determinare il nome del gruppo di risorse di un servizio web esistente, eseguire il cmdlet Get-AzureRmMlWebService senza parametri per visualizzare i servizi web nell'abbonamento. Individuare il servizio web e quindi osservare l'ID di servizio web. Il nome del gruppo di risorse è il quarto elemento ID, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è SouthCentralUS di MachineLearning predefinito.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

In alternativa, per determinare il nome del gruppo di risorse di un servizio web esistente, accedere al portale di servizi Web di Windows Azure computer risorse. Selezionare il servizio web. Il nome del gruppo di risorse è il quinto elemento l'URL del servizio web subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è SouthCentralUS di MachineLearning predefinito.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Esportare l'oggetto di definizione del servizio Web come JSON

Per modificare la definizione del modello di formazione per utilizzare il modello appena esperto, è necessario utilizzare prima di tutto il cmdlet [AzureRmMlWebService Esporta](https://msdn.microsoft.com/library/azure/mt767935.aspx) per esportare in un file in formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aggiornare il riferimento a blob ilearner

Attività, individuare [modello esperto], aggiornare il valore di *uri* del nodo *locationInfo* con l'URI del blob ilearner. URI viene generato combinando *BaseLocation* e *RelativeLocation* dall'output di BES formazione chiamata.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importare il JSON in un oggetto di definizione del servizio Web

È necessario utilizzare il cmdlet [AzureRmMlWebService importazione](https://msdn.microsoft.com/library/azure/mt767925.aspx) di file JSON modificato riconvertire in un oggetto di definizione del servizio Web che è possibile utilizzare per aggiornare la prova predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aggiornamento del servizio web

Infine, utilizzare il cmdlet di [Aggiornamento AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) per aggiornare prova previsione.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
