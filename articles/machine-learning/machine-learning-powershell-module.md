<properties
    pageTitle="Modulo PowerShell per l'apprendimento | Microsoft Azure"
    description="Il modulo PowerShell per l'apprendimento Azure è disponibile in modalità anteprima pubblico. Usare PowerShell per creare e gestire le aree di lavoro, esperimenti, servizi web e molto altro."
    keywords="Provare regressione lineare, algoritmi risorse computer, esercitazione risorse computer, tecniche di modellazione previsione, prova di scienze di dati"
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
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Modulo PowerShell per l'apprendimento Microsoft Azure

Il modulo PowerShell per l'apprendimento Azure è un potente strumento che consente di utilizzare Windows PowerShell per gestire le aree di lavoro, esperimenti, set di dati, serivces web e molto altro.

È possibile visualizzare la documentazione e scaricare il modulo, insieme al codice sorgente completo, in [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>Che cos'è del modulo PowerShell per risorse computer?

Il modulo PowerShell risorse computer è un. Basata su rete modulo DLL che consente di gestire completamente le aree di lavoro di apprendimento Azure, esperimenti, set di dati, servizi web ed endpoint del servizio web da Windows PowerShell. Insieme al modulo, è possibile scaricare il codice sorgente completo che include un correttamente separati [c# API layer](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Indica che è possibile fare riferimento a tale DLL dal progetto .NET e gestire apprendimento Azure tramite codice .NET. Inoltre, la DLL dipende dal sottostante API REST che è possibile sfruttare le direttamente dal client preferito.

## <a name="what-can-i-do-with-the-powershell-module"></a>Cosa può fare con del modulo PowerShell?

Ecco alcune delle attività che è possibile eseguire con questo modulo PowerShell. Consultare la [documentazione completa](https://aka.ms/amlps) per queste e molte altre funzioni.

- Effettuare il provisioning di una nuova area di lavoro utilizzando un certificato di gestione ([Nuovo AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Esportare e importare un file JSON che rappresenta un grafico di prova ([AmlExperimentGraph esportazione](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Importazione AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Eseguire una prova ([Start AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Creare un servizio web fuori una prova di Office ([Nuovo AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Creare un nuovo endpoint su un servizio web pubblicato ([Aggiungi AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Richiamare un record di risorse e/o BES endpoint del servizio web ([Richiama AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [AmlWebServicBESEndpoint richiama](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Ecco un rapido esempio dell'utilizzo di PowerShell per eseguire una prova esistente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Per un caso di utilizzo in modo più completo, vedere l'articolo sull'utilizzo del modulo PowerShell per automatizzare un'operazione molto in genere richieste: [creare numerosi modelli di apprendimento e web endpoint del servizio da una prova tramite PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Come iniziare?

Per iniziare con PowerShell risorse computer, scaricare il [pacchetto di rilascio](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). È necessario sbloccare la DLL scaricato decompressi e quindi importarli nel proprio ambiente di PowerShell. La maggior parte dei cmdlet di richiede è fornire l'ID dell'area di lavoro, il token di autorizzazione dell'area di lavoro e l'area Azure che l'area di lavoro. Il modo più semplice per fornire queste è tramite un file di config.json predefinita, è illustrato in dettaglio nelle istruzioni di installazione. Naturalmente, è possibile anche duplicare l'albero fra e modificare/compilare il codice localmente utilizzando Visual Studio.

## <a name="next-steps"></a>Passaggi successivi

Del modulo PowerShell continuerà a essere migliorata ed espanso durante questo periodo di anteprima. Tenere sotto controllo la [Business Intelligence Cortana e computer risorse Blog](https://blogs.technet.microsoft.com/machinelearning/) per ulteriori informazioni e notizie.
