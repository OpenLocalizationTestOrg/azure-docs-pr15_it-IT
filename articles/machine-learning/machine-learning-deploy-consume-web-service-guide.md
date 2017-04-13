<properties
    pageTitle="Azure Machine Learning Web Services: Distribuzione e consumo | Microsoft Azure"
    description="Risorse per la distribuzione e l'utilizzo di servizi web."
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
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning Web Services: Distribuzione e consumo

È possibile utilizzare Azure Machine formativo per distribuire i flussi di lavoro e i modelli come servizi web di apprendimento. Questi servizi web possono essere utilizzati quindi per chiamare i modelli di apprendimento dalle applicazioni Internet a eseguire le stime in tempo reale o in modalità batch. Poiché i servizi web sono RESTful, è possibile chiamare le varie lingue e piattaforme, ad esempio .NET e Java, programmazione e dalle applicazioni, ad esempio Excel.

Nelle sezioni successive disponibili collegamenti a procedure dettagliate, codice e documentazione utili per iniziare.

## <a name="deploy-a-web-service"></a>Distribuire un servizio web

### <a name="with-azure-machine-learning-studio"></a>Con l'apprendimento Azure Studio

Computer risorse Studio e il portale di servizi Web di Microsoft Azure macchina risorse consentono di distribuzione e la gestione di un servizio web senza scrivere codice.

I collegamenti seguenti offrono informazioni generali su come distribuire un nuovo servizio web:

* Per una panoramica su come distribuire un nuovo servizio web che si basa sulla gestione di risorse di Azure, vedere [distribuire un nuovo servizio web](machine-learning-webservice-deploy-a-web-service.md).
* Per una procedura dettagliata su come distribuire un servizio web, vedere [distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md).
* Per una procedura dettagliata completa su come creare e distribuire un servizio web, vedere [procedura dettagliata passaggio 1: creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md).
* Per esempi specifici che distribuisce un servizio web, vedere:

    * [Procedura dettagliata passaggio 5: Distribuire il servizio web di apprendimento Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Come distribuire un servizio web per più paesi](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con provider di risorse servizi web API (API di Azure Manager delle risorse)

Il provider di risorse di apprendimento Azure per i servizi web consente di distribuzione e gestione dei servizi web, utilizzando chiamate API REST. Per informazioni dettagliate, vedere la Guida di riferimento [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) su MSDN.

### <a name="with-powershell-cmdlets"></a>I cmdlet di PowerShell

Azure provider di risorse di apprendimento per i servizi web consente di distribuzione e gestione dei servizi web, utilizzando i cmdlet di PowerShell.

Per utilizzare i cmdlet, è necessario prima di tutto accedere al proprio account Azure dall'interno dell'ambiente di PowerShell utilizzando il cmdlet [AzureRmAccount Aggiungi](https://msdn.microsoft.com/library/mt619267.aspx) . Se si ha familiarità con i comandi di PowerShell basati su Manager delle risorse di chiamata, vedere [Uso di PowerShell Azure Gestione risorse Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Per esportare la prova di Office, usare [il codice di esempio](https://github.com/ritwik20/AzureML-WebServices). Dopo aver creato il file .exe dal codice, è possibile digitare:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Eseguire l'applicazione crea un modello JSON servizio web. Per utilizzare il modello per distribuire un servizio web, è necessario aggiungere le informazioni seguenti:

* Chiave e il nome dell'account di archiviazione

    È possibile ottenere il nome dell'account di archiviazione e la chiave da [Azure portale](https://portal.azure.com/) o di [Azure portale classica](http://manage.windowsazure.com/).
* ID del piano impegno

    È possibile ottenere l'ID di piano dal portale di [Servizi Web di Windows Azure Machine Learning](https://services.azureml.net) l'accesso e facendo clic su un nome di piano.

Aggiungerli al modello JSON come figlio del nodo di *proprietà* allo stesso livello, come il nodo *MachineLearningWorkspace* .

Ecco un esempio:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Visualizzare il codice di esempio per informazioni dettagliate e articoli seguenti:

* Guida di riferimento [Azure Machine Learning cmdlet]( https://msdn.microsoft.com/library/azure/mt767952.aspx) su MSDN
* Esempio [procedura dettagliata](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) su GitHub

## <a name="consume-the-web-services"></a>Utilizzare i servizi web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Dai servizi Web di apprendimento Azure computer (test) dell'interfaccia utente

È possibile testare il servizio web dal portale di servizi Web di Windows Azure computer risorse. Include un test il servizio di richiesta di risposta (record) e interfacce servizi esecuzione Batch (BES).

* [Distribuire un nuovo servizio web](machine-learning-webservice-deploy-a-web-service.md)
* [Distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Procedura dettagliata passaggio 5: Distribuire il servizio web di apprendimento Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Da Excel

È possibile scaricare un modello di Excel che utilizza il servizio web:

* [Utilizzo di un servizio web apprendimento Azure da Excel](machine-learning-consuming-from-excel.md)
* [Componente aggiuntivo di Excel per servizi Web di Windows Azure Machine Learning](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>Da un client basati su REST

Servizi Web di Azure Machine Learning sono API REST. È possibile utilizzare queste API da piattaforme diverse, ad esempio .NET, Python, R, Java e così via. Il **consumo** per il servizio web nel [portale di servizi Web di Microsoft Azure macchina risorse](https://services.azureml.net) è incluso il codice di esempio che consentono di iniziare. Per ulteriori informazioni, vedere [come utilizzare un servizio web Azure apprendimento che è stato distribuito da un computer di prova di apprendimento](machine-learning-consume-web-services.md).

