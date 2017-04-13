<properties
    pageTitle="Reimpostare un modello di apprendimento | Microsoft Azure"
    description="Informazioni su come reimpostare un modello e aggiornare il servizio Web per utilizzare il modello appena esperto in Azure apprendimento."
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Reimpostare un modello di apprendimento

Come parte del processo di operationalization dei modelli di risorse computer Azure apprendere computer, il modello è qualificato e salvato. È quindi utilizzare per creare un servizio Web predicative. Il servizio Web può quindi essere utilizzato in siti web, dashboard e App per dispositivi mobili. 

Modelli creati tramite l'apprendimento non sono in genere statici. Sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati nel modello deve essere training. 

Formazione può verificarsi con frequenza. Con la funzionalità di API di formazione programmazione, è possibile reimpostare il modello utilizzando le API formazione a livello di programmazione e aggiornare il servizio Web con il modello appena esperto. 

In questo documento viene descritto il processo i e viene illustrato come utilizzare le API di formazione.

## <a name="why-retrain-defining-the-problem"></a>Perché reimpostare: definizione del problema  

Come parte del processo di formazione di apprendimento, un modello è stato eseguito utilizzando un set di dati. Modelli creati tramite l'apprendimento non sono in genere statici. Sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati nel modello deve essere training.

In questi scenari, API di programmazione offre un modo comodo per consentono o consumer dell'API creare un client che può, in base al regolare o occasionale, reimpostare il modello utilizzando i propri dati. Possono quindi valutare i risultati di formazione e aggiornare l'API del servizio Web per utilizzare il modello appena esperto.

>[AZURE.NOTE] Se si dispone di una prova di formazione esistente e servizio Web nuovo, è consigliabile estrarre ripetizione sincronizzazione iniziale di un servizio Web stima esistente anziché seguendo la procedura dettagliata indicata nella sezione seguente.

## <a name="end-to-end-workflow"></a>Flusso di lavoro-to-end 

Il processo prevede i seguenti componenti: A formazione prove ed una prova stima pubblicato come un servizio Web. Per abilitare la formazione di un modello di formazione, prova formazione deve essere pubblicata come servizio Web con l'output di un modello di esperto. In questo modo l'accesso al modello di API per formazione. 

I passaggi seguenti si applicano ai servizi di nuovo e Web classico:

Creare il servizio Web stima iniziale:

* Creare una prova di formazione
* Creare una prova di Office web
* Distribuire un servizio web previsione

Reimpostare il servizio Web:

* Aggiornare prova di formazione per consentire di formazione
* Distribuire il servizio web i
* Utilizzare il codice servizio di esecuzione Batch per reimpostare il modello

Per una procedura dettagliata dei passaggi precedenti, vedere [reimpostare l'apprendimento modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md).

Se è stato distribuito un servizio Web classica:

* Creare un nuovo Endpoint del servizio Web previsione
* Ottenere le PATCH URL e il codice
* Utilizzare l'URL PATCH in modo che puntino l'Endpoint di nuovo il modello retrained 

Per una procedura dettagliata dei passaggi precedenti, vedere [reimpostare un servizio Web classica](machine-learning-retrain-a-classic-web-service.md).

Se si verificano problemi formazione un servizio Web classico, vedere [risoluzione dei problemi di formazione di un servizio Web classica di Azure Machine Learning](machine-learning-troubleshooting-retraining-models.md).

Se è stato distribuito un servizio Web nuova:

* Accedere al proprio account Manager delle risorse di Azure
* Ottenere la definizione del servizio Web
* Esportare la definizione di servizio Web come JSON
* Aggiornare il riferimento per la `ilearner` blob nel JSON
* Importare il JSON in definizione di un servizio Web
* Aggiornare il servizio Web con nuova definizione di servizio Web

Per una procedura dettagliata dei passaggi precedenti, vedere [reimpostare un servizio Web nuovo utilizzando i cmdlet di PowerShell di gestione risorse computer](machine-learning-retrain-new-web-service-using-powershell.md).

Il processo per l'impostazione di formazione per un servizio Web classico prevede i passaggi seguenti:

![Panoramica del processo di formazione][1]

Il processo per l'impostazione di formazione per un servizio Web nuovo prevede i passaggi seguenti:

![Panoramica del processo di formazione][7]

## <a name="other-resources"></a>Altre risorse

- [Modelli di formazione e aggiornamento di apprendimento Azure con Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Creare numerosi modelli di apprendimento e web endpoint servizio da una prova tramite PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- Il video [Elenco parti fornitore formazione modelli utilizzando API](https://www.youtube.com/watch?v=wwjglA8xllg) viene illustrato come reimpostare i modelli di apprendimento creati in Azure apprendimento uso delle API di formazione e PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

