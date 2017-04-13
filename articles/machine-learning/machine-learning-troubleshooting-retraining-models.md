<properties
    pageTitle="Risoluzione dei problemi Retraining di un servizio Web classica di Azure Machine Learning | Microsoft Azure"
    description="Individuare e correggere riscontra problemi comuni quando si formazione il modello per un servizio Web di Azure computer risorse."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Risoluzione dei problemi di formazione di un servizio Web classica di Azure Machine Learning

## <a name="retraining-overview"></a>Panoramica di formazione

Quando si distribuisce una prova di Office come servizio web punteggio è un modello statico. Se sono disponibili nuovi dati o se consumer dell'API sono previsti i propri dati, è necessario essere training il modello. 

Per una procedura dettagliata completamento del processo dei di un servizio Web classico, vedere [Reimpostare Machine Learning modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>I grafici

Quando è necessario reimpostare il servizio Web, è necessario aggiungere alcune parti aggiuntive:

* Un servizio Web distribuito da prova formazione. La prova deve avere un modulo di **Output servizio Web** allegato per l'output del modulo **Modello treno** .  

    ![Collegare l'output di servizio web al modello di treno.][image1]

* Un nuovo endpoint aggiunti al servizio Web punteggio.  È possibile aggiungere endpoint a livello di programmazione tramite il codice di esempio fare riferimento a livello di programmazione nei modelli di reimpostare l'apprendimento argomento o tramite il portale classico Azure.

È quindi possibile utilizzare c# codice dalla pagina della Guida all'API del servizio Web formazione per reimpostare modello. Dopo avere valutato i risultati e sono effettivamente quelli con loro, è aggiornare il modello esperto punteggio servizio web usando il nuovo punto finale che è stato aggiunto.

Con tutti gli elementi in posizione, i passaggi principali da eseguire per reimpostare il modello sono i seguenti:

1.  Chiamare il servizio Web di formazione: la chiamata è per Batch esecuzione servizio (BES), non la richiesta di risposta servizio (record). È possibile usare il codice di esempio c# nella pagina della Guida all'API per effettuare la chiamata. 
2.  Individuare i valori per *BaseLocation*, *RelativeLocation*e *SasBlobToken*: I valori vengono restituiti nell'output dalla chiamata al servizio Web formazione. 
      ![con l'output di esempio i e i valori BaseLocation, RelativeLocation e SasBlobToken.][image6]
3.  Aggiornare l'endpoint aggiunto dal servizio web punteggio con il nuovo modello di formazione: utilizzando il codice di esempio fornito nei modelli di reimpostare l'apprendimento a livello di programmazione, aggiornare il nuovo endpoint aggiunto al modello di punteggio con il modello appena esperto dal servizio Web formazione.

## <a name="common-obstacles"></a>Ostacoli comuni

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verificare se è disponibile l'URL di PATCH corretto

L'URL PATCH in uso deve essere associata a nuovo endpoint punteggio che è stato aggiunto al punteggio servizio Web. Esistono diversi modi per ottenere l'URL PATCH:

**Opzione 1: a livello di programmazione**

Per ottenere l'URL corretto PATCH:

1.  Eseguire il codice di esempio [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  Output di AddEndpoint, trovare il valore di *HelpLocation* e copiare l'URL.

    ![HelpLocation nell'output di esempio addEndpoint.][image2]

3.  Incollare l'URL in un browser per passare a una pagina che fornisce collegamenti alla Guida per il servizio Web.
4.  Fare clic sul collegamento di **Aggiornamento delle risorse** per aprire la pagina di Guida patch.

**Opzione 2: Usare il portale classico Azure**

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com).
2.  Aprire la scheda di apprendimento. 
     ![Scheda inclinati computer.][image4]
3.  Scegliere il nome dell'area di lavoro, quindi **Servizi Web**.
4.  Fare clic su servizio Web di punteggio in uso. (Se non è stato modificato il nome predefinito del servizio web, finirà in [punteggio Exp]..)
5.  Fare clic su **Aggiungi Endpoint**.
6.  Una volta aggiunto il punto finale, fare clic sul nome dell'endpoint. Fare clic su **Aggiorna risorse** per aprire la pagina Guida patch.

>[AZURE.NOTE] Se è stato aggiunto l'endpoint del servizio Web formazione anziché il servizio Web previsione, viene visualizzato l'errore seguente quando si fa clic sul collegamento **Aggiorna risorsa** : indica, ma questa caratteristica non è supportata o disponibile in questo contesto. Nessuna risorsa aggiornabile il servizio Web. Ci scusiamo per eventuali inconvenienti e si sta lavorando su come migliorare il flusso di lavoro.

![Nuovo dashboard all'endpoint.][image3]

Pagina della Guida PATCH contiene URL PATCH è necessario utilizzare e fornisce il codice di esempio che è possibile utilizzare per chiamarlo.

![URL di patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verificare che si sta aggiornando l'endpoint punteggio corretto

* Non ripristinato il servizio Web di formazione: operazione patch deve essere eseguita il punteggio servizio Web.
* Non ripristinato l'endpoint del servizio Web predefinito: operazione patch deve essere eseguita la nuova punteggio endpoint del servizio Web che è stato aggiunto.

È possibile verificare quali servizio Web l'endpoint è visitando il portale classico Azure. 

>[AZURE.NOTE] Assicurarsi che si sta aggiungendo l'endpoint al servizio Web previsione, non il servizio Web di formazione. Se è stata distribuita correttamente un corso di formazione e un servizio Web previsione, è necessario verificare due diversi servizi Web elencati. Il servizio Web stima deve terminare con "[stima spese]".

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com).
2.  Aprire la scheda di apprendimento. 
     ![Area di lavoro formazione automatica dell'interfaccia utente.][image4]
3.  Selezionare l'area di lavoro.
4.  Fare clic su **servizi Web**.
5.  Selezionare il servizio Web previsione.
6.  Verificare che il nuovo endpoint è stato aggiunto al servizio Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Selezionare l'area di lavoro cui il servizio web per assicurarsi che si trova nell'area corretta

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com).
2.  Selezionare l'apprendimento dal menu.
      ![Area risorse di computer dell'interfaccia utente.][image4]
3.  Verificare la posizione dell'area di lavoro.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png