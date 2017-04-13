<properties
    pageTitle="Passaggio 6: Accedere al servizio web di apprendimento | Microsoft Azure"
    description="Passaggio 6 di sviluppare una procedura dettagliata soluzione stima: accedere a un servizio web apprendimento Azure active."
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
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Procedura dettagliata passaggio 6: Accedere al servizio web di apprendimento Azure

Questo è l'ultimo passaggio della procedura dettagliata, [sviluppare una soluzione analitica previsione in apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Caricare dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.  [Creare una nuova prova](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Organizzare la formazione e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Accedere al servizio web**

----------

Nel passaggio precedente in questa procedura dettagliata è distribuito un servizio web che utilizza il nostro modello stima i rischi di carta di credito. A questo punto gli utenti devono essere in grado di inviare dati e ricevere i risultati. 

Il servizio web è un servizio web Azure che può ricevere e restituire dati utilizzando le API REST in uno dei due modi:  

-   **Richiesta/risposta** : l'utente invia una o più righe di dati di carta di credito al servizio tramite un protocollo HTTP e il servizio risponderà con uno o più set di risultati.
-   **Esecuzione batch** - l'utente archivia uno o più righe di dati di carta di credito in un archivio blob Azure e quindi invia la posizione blob al servizio. Il servizio punteggi tutte le righe di dati di input blob, archivia i risultati in un altro blob e restituisce l'URL di tale contenitore.  

Il modo più semplice e veloce per accedere al servizio web è tramite i modelli di App Web disponibili nelle [App Web di Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).
Questi modelli di app web è possono creare un'app web personalizzata che conosca il servizio web dati di input e cosa verrà restituito. È sufficiente è fornire l'accesso al servizio web e i dati e il modello di tutte le altre applicazioni.

Per ulteriori informazioni sull'utilizzo di modelli di app web, vedere [consumo un servizio web apprendimento Azure con un modello di app web](machine-learning-consume-web-service-with-web-app-template.md).

È anche possibile sviluppare un'applicazione personalizzata per accedere al servizio web usando il codice starter prevista R, c# e linguaggi di programmazione Python.
Sono disponibili informazioni dettagliate [sull'utilizzo di un servizio web apprendimento Azure che è stato pubblicato da un computer di prova di apprendimento](machine-learning-consume-web-services.md).
