<properties
pageTitle="Creare più modelli da una prova | Microsoft Azure"
description="Usare PowerShell per creare più modelli di apprendimento e web endpoint del servizio con lo stesso algoritmo ma formazione diverso set di dati."
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Creare numerosi modelli di apprendimento e web endpoint servizio da una prova tramite PowerShell

Ecco un problema di risorse computer comune: si desidera creare numerosi modelli che dispongono stesso flusso di lavoro di formazione e utilizzano lo stesso algoritmo ma di formazione diverso set di dati come input. In questo articolo viene illustrato come eseguire questa operazione in scala di Azure Machine Learning Studio usando solo una singola prova.

Ad esempio, supponiamo che si è proprietari impresa franchising noleggio bicicletta globale. Si desidera creare un modello di regressione per stimare la domanda noleggio in base a dati cronologici. Si dispone di 1.000 posizioni noleggio in tutto il mondo e aver raccolto un set di dati per ogni percorso in cui sono incluse funzionalità importanti, ad esempio data, ora, meteo, il traffico specifici per ogni posizione.

Impossibile formare il modello di una sola volta tramite una versione unita di tutti i set di dati in tutte le posizioni. Ma poiché ognuna delle posizioni dei ha un ambiente univoco, un approccio più efficace per formare il modello di regressione separatamente utilizzando il set di dati per ogni posizione. In questo modo, ogni modello di esperto può prendere in considerazione le dimensioni dell'archivio diverso, volume, geography, popolazione, ambiente il traffico orientata bicicletta e *così via*.

Che può essere l'approccio ottimale, ma non si vuole creare 1.000 esperimenti formazione in apprendimento Azure con ognuno di essi che rappresenta un percorso univoco. Oltre a essere un'attività piuttosto complessa, è inoltre sembra molto efficiente in quanto ogni prova avranno le stesse componenti fatta eccezione per il set di dati di formazione.

Per in futuro sarà possibile eseguire questa utilizzando [apprendimento Azure formazione API](machine-learning-retrain-models-programmatically.md) e automatizzare le attività con [Azure Machine Learning PowerShell](machine-learning-powershell-module.md).

> [AZURE.NOTE] Per rendere il campione eseguito più velocemente, è necessario ridurre il numero di posizioni da 1.000 a 10. Ma gli stessi principi e procedure si riferiscono a 1.000 percorsi. L'unica differenza è che se si desidera formare da 1.000 set di dati è consigliabile valutare di eseguire gli script di PowerShell seguenti in parallelo. Come eseguire questa operazione è lo scopo di questo articolo, ma è possibile trovare esempi di PowerShell multithreading su Internet.  

## <a name="set-up-the-training-experiment"></a>Configurare la prova formazione

Verranno utilizzare un esempio [formazione verificarne l'effetto](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) che abbiamo creato nella [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com). Aprire questa prova nell'area di lavoro di [Azure Machine Learning Studio](https://studio.azureml.net) .

>[AZURE.NOTE] Per seguire insieme in questo esempio, si desidera utilizzare un'area di lavoro standard anziché un'area di lavoro gratuito. Si sta creando un endpoint per ogni cliente - per un totale di 10 endpoint - e che richiedono un'area di lavoro standard poiché un'area di lavoro gratuito è limitato a 3 punti finali. Se si dispone di un'area di lavoro gratuita, è sufficiente modificare gli script di seguito per consentire solo 3 posizioni.

La prova utilizza un modulo di **Importazione dei dati** per importare il set di dati di formazione *customer001.csv* da un account di archiviazione Azure. Si supponga abbiamo raccolti formazione set di dati da tutti i percorsi di noleggio bicicletta e li archiviato nella stessa posizione di archiviazione blob con nomi di file compreso tra *rentalloc001.csv* e *rentalloc10.csv*.

![immagine](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Nota il modulo **Treno modello** è stato aggiunto un modulo di **Output servizio Web** .
Quando questa prova viene distribuito come un servizio web, l'endpoint associato che output restituirà il modello di esperto nel formato di un file .ilearner.

Si noti inoltre che è necessario impostare un parametro di servizio web per l'URL che usa il modulo **Importa dati** . In questo modo come utilizzare il parametro per specificare i set di dati individuali di formazione per formare il modello per ogni posizione.
Ci sono altri modi che è ottenere lo stesso risultato, ad esempio utilizzando una query SQL con un parametro di servizio web per recuperare dati da un database SQL Azure o semplicemente tramite un modulo di **Input del servizio Web** per passare a un set di dati al servizio web.

![immagine](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

A questo punto, si esegue questa prova formazione utilizzando il valore predefinito *rental001.csv* come il set di dati di formazione. Se si visualizza l'output del modulo di **valuta** (l'output scegliere **effetti grafici**), è possibile vedere è ottenere una buona prestazioni *AUC* = 0.91. A questo punto si è pronti per distribuire un servizio web fuori questa prova di formazione.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuire la formazione e servizi web di classificazione

Per distribuire il servizio web di formazione, fare clic sul pulsante **Di servizio Web** sotto l'area di prova e selezionare **Distribuire il servizio Web**. Chiamare il servizio web "" formazione noleggio bicicletta".

A questo punto è necessario distribuire il punteggio servizio web.
A tale scopo, è possibile fare clic **Su servizio Web** sotto l'area di e selezionare **Previsione servizio Web**. In questo modo si crea un punteggio prova.
È necessario apportare alcune modifiche minime per farlo funzionare come un servizio web, ad esempio per rimuovere la colonna etichetta "cnt" dai dati di input e limitare l'output solo l'ID dell'istanza e il corrispondente previsto valore.

Per salvare il lavoro, è possibile aprire semplicemente [prova stima](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) nella raccolta è già stata preparata.

Per distribuire il servizio web, eseguire prova previsione, quindi fare clic sul pulsante **Distribuire servizio Web** sotto l'area. Assegnare un nome al servizio web punteggio "Bicicletta noleggio punteggio" ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Creare 10 endpoint di servizi web identiche con PowerShell

Questo servizio web viene fornito con un endpoint predefinito. Ma non siamo tanto interessati l'endpoint predefinito dal momento che non possono essere aggiornato. È necessario consiste nel creare 10 endpoint aggiuntive, uno per ogni posizione. È necessario ripetere l'operazione con PowerShell.

Prima di tutto è impostare l'ambiente di PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Eseguire il comando PowerShell seguente:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

A questo punto abbiamo creato 10 punti finali e contengono lo stesso modello di esperto formato su *customer001.csv*. È possibile visualizzarli nel portale di gestione di Azure.

![immagine](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aggiornare i punti finali per l'utilizzo di set di dati separato formazione tramite PowerShell

Il passaggio successivo consiste nel aggiornare i punti finali con i modelli in modo univoco formati su singoli dati di ogni cliente. Ma prima di tutto è necessario per produrre questi modelli dal servizio web **Bicicletta noleggio formazione** . Passiamo al servizio web **Bicicletta noleggio formazione** . È necessario chiamare il punto finale BES 10 volte con 10 formazione diverso set di dati per produrre 10 diversi modelli. Si userà il cmdlet di PowerShell **InovkeAmlWebServiceBESEndpoint** per eseguire questa operazione.

È anche necessario fornire le credenziali per l'account di archiviazione blob in `$configContent`, vale a dire, i campi `AccountName`, `AccountKey` e `RelativeLocation`. Il `AccountName` può essere uno dei nomi account, come illustrato nel **Portale di gestione di Azure classico** (scheda*archiviazione* ). Quando si fa clic su un account di archiviazione, la `AccountKey` possano essere trovati tramite il pulsante **Gestisci i tasti di scelta** nella parte inferiore e copiare la *Chiave primaria di Access*. Il `RelativeLocation` è il percorso rispetto lo spazio di archiviazione in cui verrà archiviato un nuovo modello. Ad esempio, il percorso `hai/retrain/bike_rental/` dello script sotto punta a un contenitore denominato `hai`, e `/retrain/bike_rental/` sono sottocartelle. Al momento non è possibile creare sottocartelle tramite il portale dell'interfaccia utente, ma sono disponibili [Diverse finestre di esplorazione Azure lo spazio di archiviazione](../storage/storage-explorers.md) che consentono di eseguire questa operazione. È consigliabile creare un nuovo contenitore nello spazio di archiviazione per archiviare i modelli di esperti nuovi (file .ilearner) come indicato di seguito: dalla pagina lo spazio di archiviazione, fare clic sul pulsante **Aggiungi** nella parte inferiore e denominarla `retrain`. In conclusione, le modifiche necessarie al script riportato di seguito riguardano `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] L'endpoint BES è la modalità supportata sola per questa operazione. Record di risorse non può essere utilizzato per la creazione di modelli di esperti.

Come si può notare, invece di costruzione di 10 diversi BES processo json file di configurazione, abbiamo in modo dinamico invece di creare la stringa di configurazione e feed al parametro *jobConfigString* del cmdlet **InvokeAmlWebServceBESEndpoint** , dal momento che è necessario conservare una copia nel disco.

Se tutto va bene, dopo un certo periodo è necessario verificare 10 file .ilearner, da *model001.ilearner* a *model010.ilearner*nell'account di archiviazione Azure. Ora sono pronti aggiornare il nostro 10 endpoint di servizi web punteggio con questi modelli utilizzando il cmdlet di PowerShell **Patch AmlWebServiceEndpoint** . È necessario ricordare nuovamente che è possibile patch solo i punti finali non predefinito a livello di programmazione creato in precedenza.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Deve essere eseguita abbastanza rapidamente. Al termine dell'esecuzione, è necessario è stata creata 10 endpoint servizio web previsione, ogni che contiene un modello di esperto in modo univoco qualificato nel set di dati specifico in una posizione noleggio, da una prova di formazione singola. Per verificare questa impostazione, è possibile provare a chiamare il queste endpoint utilizzando il cmdlet **InvokeAmlWebServiceRRSEndpoint** offrendo gli stessi dati di input e previsto visualizzare i risultati di stima diverso dal momento che i modelli di qualificati con set di formazione diverso.

## <a name="full-powershell-script"></a>Script di PowerShell completo

Ecco l'elenco di codice sorgente completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
