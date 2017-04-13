<properties
    pageTitle="Una soluzione previsione per il rischio di carta di credito con apprendimento | Microsoft Azure"
    description="Una procedura dettagliata che mostra come creare una soluzione analitica previsione per la valutazione dei rischi di carta di credito in Azure Machine Learning Studio."
    keywords="rischio, soluzione analitica previsione, la valutazione dei rischi"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Procedura dettagliata: Sviluppare una soluzione analitica previsione per la valutazione dei rischi carta di credito apprendimento Azure

Si supponga che occorre prevedere rischio dei singoli in base alle informazioni che forniscono in un'applicazione di carta di credito.  

Valutazione dei rischi di carta di credito un problema complesso, naturalmente, ma è possibile semplificare un po' i parametri della domanda. È quindi possibile utilizzarli come un esempio di come è possibile utilizzare Microsoft Azure Machine Learning con computer risorse Studio e il servizio web di apprendimento per creare una soluzione di previsione analitica.  

In questa procedura dettagliata, seguire il processo di sviluppo di un modello di previsione analitica Studio risorse computer e quindi distribuirlo come un servizio web apprendimento Azure. Si verrà iniziare con dati sui rischi di carta di credito disponibile al pubblico, sviluppare e formare un modello di stima in base a tali dati e quindi distribuisce il modello come un servizio web che può essere usato da altri utenti per la valutazione dei rischi di carta di credito.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Per scaricare e stampare un diagramma che fornisce una panoramica delle funzionalità di Studio risorse computer, vedere [il diagramma di panoramica delle funzionalità di Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).

Per creare una soluzione di valutazione dei rischi di carta di credito, seguire la procedura seguente:  

1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Caricare dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.  [Creare una nuova prova](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Organizzare la formazione e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accedere al servizio web](machine-learning-walkthrough-6-access-web-service.md)

In questa procedura dettagliata si basa su una versione semplificata del [Classfication binario: stima dei rischi di credito](http://go.microsoft.com/fwlink/?LinkID=525270) prova di esempio nella [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com/).
