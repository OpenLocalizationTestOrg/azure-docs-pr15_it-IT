<properties
    pageTitle="Reimpostare un nuovo servizio web utilizzando i cmdlet di PowerShell di gestione risorse computer | Microsoft Azure"
    description="Informazioni su come reimpostare un modello e aggiornare il servizio web per utilizzare il modello appena esperto in Azure apprendimento utilizzando i cmdlet di PowerShell Gestione risorse di computer a livello di programmazione."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Reimpostare un nuovo servizio web utilizzando i cmdlet di PowerShell Gestione risorse di computer

Quando si reimpostare un nuovo servizio web, aggiornare la definizione di servizio web previsione per il nuovo modello di esperto fare riferimento.  

## <a name="prerequisites"></a>Prerequisiti

È necessario avere impostare una prova di formazione e una stima prova come illustrato nella [reimpostare l'apprendimento modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] Prova Office deve essere distribuito come un servizio web di apprendimento Manager delle risorse Azure in base a (nuovo). 
 
Per ulteriori informazioni sui servizi web di distribuzione, vedere [distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md).

Questo processo è necessario che sia installato i cmdlet di apprendimento Machine Azure. Per informazioni di installazione i cmdlet di apprendimento, vedere il riferimento di [Azure Machine Learning cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) su MSDN.

Copiare le informazioni seguenti dall'output i:

* BaseLocation
* RelativeLocation

I passaggi da sono:

1.  Accedere al proprio account Manager delle risorse di Azure.
2.  Ottenere la definizione del servizio web
3.  Esportare la definizione di servizio Web come JSON
4.  Aggiornare il riferimento a blob ilearner nel JSON.
5.  Importare il JSON in definizione di un servizio Web
6.  Aggiornare il servizio web con nuova definizione di servizio Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Accedere al proprio account Manager delle risorse di Azure

È necessario prima di tutto accedere al proprio account Azure dall'interno dell'ambiente di PowerShell utilizzando il cmdlet [AzureRmAccount Aggiungi](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Ottenere la definizione del servizio Web

Successivamente, è possibile ottenere il servizio Web chiamando il cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . Definizione del servizio Web è una rappresentazione interna del modello di esperti del servizio web e non può essere modificata direttamente. Verificare che si desidera recuperare la definizione di servizio Web per la stima prova e non la prova di formazione.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Per determinare il nome del gruppo di risorse di un servizio web esistente, eseguire il cmdlet Get-AzureRmMlWebService senza parametri per visualizzare i servizi web nell'abbonamento. Individuare il servizio web e quindi osservare l'ID di servizio web. Il nome del gruppo di risorse è il quarto elemento ID, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è SouthCentralUS di MachineLearning predefinito.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

In alternativa, per determinare il nome del gruppo di risorse di un servizio web esistente, accedere al portale di servizi Web di Microsoft Azure macchina risorse. Selezionare il servizio web. Il nome del gruppo di risorse è il quinto elemento l'URL del servizio web subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è SouthCentralUS di MachineLearning predefinito.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Esportare la definizione di servizio Web come JSON

Per modificare la definizione al modello di esperti usare appena esperti modello, è necessario innanzitutto utilizzare il cmdlet [AzureRmMlWebService Esporta](https://msdn.microsoft.com/library/azure/mt767935.aspx) per esportare in un file in formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Aggiornare il riferimento a blob ilearner nel JSON.

Attività, individuare [modello esperto], aggiornare il valore di *uri* del nodo *locationInfo* con l'URI del blob ilearner. URI viene generato combinando *BaseLocation* e *RelativeLocation* dall'output di BES formazione chiamata.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importare il JSON in definizione di un servizio Web

È necessario utilizzare il cmdlet [AzureRmMlWebService importazione](https://msdn.microsoft.com/library/azure/mt767925.aspx) di file JSON modificato riconvertire in definizione di un servizio Web che è possibile utilizzare per l'aggiornamento di prova Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aggiornare il servizio web con nuova definizione di servizio Web

Infine, si cmdlet [AzureRmMlWebService di aggiornamento](https://msdn.microsoft.com/library/azure/mt767922.aspx) consente di aggiornare prova previsione.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Riepilogo

Utilizzare i cmdlet di gestione dei computer risorse PowerShell, è possibile aggiornare il modello di esperto di un servizio Web previsione, abilitazione di scenari ad esempio:

* Modello periodico formazione con i nuovi dati.
* Distribuzione di un modello per i clienti con l'obiettivo di informali di reimpostare il modello utilizzando i propri dati.
