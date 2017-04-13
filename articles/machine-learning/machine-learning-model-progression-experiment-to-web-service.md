<properties
    pageTitle="Come un modello di apprendimento avanza da una prova a un servizio Web operationalized | Microsoft Azure"
    description="Panoramica dei meccanismi di come l'avanza modello di apprendimento Azure per lo sviluppo prove a un servizio Web operationalized."
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


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Come un modello di apprendimento avanza da una prova a un servizio Web operationalized

Azure Machine Learning Studio fornisce un'area di disegno interattiva che consente di sviluppare, eseguire, testare e scorrere un ***verificarne l'effetto*** che rappresenta un modello di analisi di stima. Sono disponibili una vasta gamma di moduli che è possibile:

* Dati di input nella prova
* Modificare i dati
* Formare un modello di utilizzo degli algoritmi risorse computer
* Punteggio del modello
* Valutare i risultati
* Valori finali di output

Dopo aver verificato con la prova, è possibile distribuirlo come un ***servizio Web di Microsoft Learning Machine Azure classica*** o un ***servizio Web di apprendimento Machine Azure nuovo*** in modo che gli utenti possono inviare nuovi dati e ricevere restituire i risultati.

In questo articolo viene fornita una panoramica dei meccanismi di come l'avanzamento del modello apprendimento da per lo sviluppo prove a un servizio Web operationalized.

>[AZURE.NOTE] Ci sono altri modi per sviluppare e distribuire modelli di apprendimento computer, ma in questo articolo è incentrato sull'utilizzo di Studio formazione computer. Per informazioni sulla creazione di un servizio Web stima classico con R, vedere il blog [creare e distribuire Office Web Apps con RStudio e Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Sebbene Azure Machine Learning Studio è progettato per sviluppare e distribuire un *modello di analisi di stima*, è possibile utilizzare Studio per lo sviluppo di una prova che non include un modello di analisi di stima. Ad esempio, una prova potrebbe solo i dati di input, modificarlo e quindi restituire i risultati. Come una prova di analisi di stima, è possibile distribuire questa prova non stima come un servizio Web, ma è un processo semplice perché l'esperimenti non formazione o classificazione di un modello di apprendimento. Sebbene non sia tipico usare Studio in questo modo, è necessario includerli nella discussione in modo che in questo modo, una spiegazione completa del funzionamento di Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Sviluppo e la distribuzione di un servizio Web previsione

Ecco i passaggi di una soluzione tipica successivo quando si sviluppano e distribuirlo Machine Learning Studio:

![Flusso di distribuzione](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - fasi di un modello di analisi di stima tipica*

### <a name="the-training-experiment"></a>Prova formazione

***Formazione su provare*** è la fase iniziale di sviluppo al servizio Web nel computer risorse Studio. Lo scopo di prova formazione è fornire una posizione in cui sviluppare, testare, scorrere e infine formare un modello di apprendimento. È possibile anche formare più modelli contemporaneamente come cercare la soluzione migliore, ma dopo aver eseguito sperimentare si seleziona un unico qualificato del modello ed eliminare tutte le altre applicazioni da di prova. Per un esempio di sviluppo di un'analisi previsione sperimentare l'utilizzo, vedere [sviluppare una soluzione analitica previsione per la valutazione dei rischi carta di credito apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Prova Office

Dopo avere inserito un modello di esperto di prova la formazione, fare clic **Su servizio Web** e selezionare **Previsione servizio Web** nel computer risorse Studio per avviare il processo di conversione la prova di formazione per una ***prova di Office***. Lo scopo di prova previsione consiste nell'utilizzare il modello di esperto accumulare nuovi dati, con l'obiettivo di infine diventando operationalized come un servizio Web Azure.

Questa conversione viene eseguita tramite la procedura seguente:

-   Convertire il set di moduli utilizzati per la formazione in un unico modulo e salvarlo come modello esperto

-   Eliminare tutti i moduli estranei non correlati alla classificazione

-   Aggiungere le porte di input e di output che verrà utilizzato il servizio Web finale

Potrebbero esserci altre modifiche che si desidera rendere di prova la stima pronto per la distribuzione come un servizio Web. Ad esempio, se necessario il servizio Web per generare l'output solo un sottoinsieme dei risultati, è possibile aggiungere un modulo filtro prima la porta di output.

In questo processo di conversione, prova formazione non verrà eliminato. Una volta completato il processo, sono presenti due schede Studio: uno per prova formazione e uno per la stima prova. In questo modo è possibile apportare modifiche a prova formazione prima di distribuire il servizio Web e ricostruire prova previsione. Oppure è possibile salvare una copia di prova formazione per avviare un'altra riga di sperimentazione.

>[AZURE.NOTE] Quando si fa clic su **Servizio Web previsione** si avvia un processo automatico per convertire la prova di formazione in una prova di Office e questo metodo funziona bene nella maggior parte dei casi. Se la prova formazione complesso (ad esempio, si dispone di più percorsi corsi di formazione che si uniscono), è preferibile eseguire manualmente la conversione. Per ulteriori informazioni, vedere [conversione di una prova di formazione di apprendimento per una prova di Office](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>Il servizio Web

Dopo aver verificato che la prova previsione è pronto, è possibile distribuire il servizio come un servizio di Web classico o sulla gestione di risorse di Azure in base a un servizio Web nuovo. Per rendono operativi il modello mediante la distribuzione come *servizio Web di apprendimento classico computer*, fare clic su **Servizio Web di distribuzione** e selezionare **Distribuire il servizio Web [classica]**. Per distribuire come *servizio Web di apprendimento nuovo computer*, fare clic su **Servizio Web di distribuzione** e selezionare **Distribuire [New] servizio Web**. Gli utenti possono ora invia dati al modello di utilizzo del servizio Web API REST e riceve i risultati. Per ulteriori informazioni, vedere [come utilizzare un servizio Web di Microsoft Azure Machine Learning che è stato distribuito da un computer di prova di apprendimento](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Il caso non tipico: creazione di un servizio Web non previsione

Se la prova non formare un modello di analisi di stima, quindi non necessarie per creare una prova di formazione e un punteggio prova - c'è solo una prova ed è possibile distribuire come un servizio Web. Computer risorse rileva se la prova contiene un modello di stima per analizzare i moduli che sono state usate.

Dopo aver effettuato sulla prova e si è soddisfatti:

1.  Fare clic **Su servizio Web** e selezionare **Formazione servizio Web** - vengono automaticamente aggiunti nodi di input e di output

2.  Fare clic su **Esegui**

3. Fare clic su **Servizio Web di distribuzione** e selezionare **La distribuzione del servizio Web [classica]** o **Distribuire un servizio Web [New]** a seconda dell'ambiente in cui si desidera distribuire.

A questo punto è distribuito il servizio Web ed è possibile accedere e gestire analoga a un servizio Web previsione.

## <a name="updating-your-web-service"></a>Aggiornamento del servizio Web

Ora che è stata distribuita la prova come un servizio Web, cosa accade se è necessario aggiornarla?

Dipende dal è necessario aggiornare:

**Si desidera modificare l'input o output o che si desidera modificare la modalità di modifica dati al servizio Web**

Se non si modificano il modello, ma solo da modificare la modalità di gestione dati al servizio Web, è possibile modificare prova previsione e quindi selezionare **Distribuire servizio Web** e selezionare di nuovo **La distribuzione del servizio Web [classica]** o **Distribuire un servizio Web [New]** . Il servizio Web viene interrotto, viene distribuito prova di previsione aggiornato e si riavvia il servizio Web.

Ecco un esempio: si supponga che la prova previsione restituirà l'intera riga di dati di input con i risultati previsti. È possibile che il servizio Web per restituire il risultato. Così è possibile aggiungere un modulo di **Colonne di progetto** in prova previsione, appena prima la porta di output, escludere colonne ad eccezione del risultato. Quando si fa clic **Servizio Web di distribuzione** , seleziona di nuovo **La distribuzione del servizio Web [classica]** o **Distribuire un servizio Web [New]** viene aggiornato il servizio Web.

**Si desidera reimpostare il modello con i nuovi dati**

Se si desidera mantenere il modello di apprendimento, ma si vuole reimpostare con i nuovi dati, sono disponibili due opzioni:

1.  **Reimpostare il modello mentre è in esecuzione il servizio Web** - se si desidera reimpostare il modello durante la stima servizio Web è in esecuzione, è possibile eseguire questa operazione sono riportate alcune modifiche a prova formazione per renderla una ***prova i***, quindi è possibile distribuire come un **servizio *web i* **. Per istruzioni su come eseguire questa operazione, vedere [reimpostare l'apprendimento modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md).

2.  **Tornare a prove formazione originale ed utilizzare dati di formazione su diversi per sviluppare il modello** - prova la previsione è collegato al servizio Web, ma prova formazione non è collegato direttamente in questo modo. Se si modifica la prova formazione originale e fare clic **Su servizio Web**, esso verrà creata una *nuova* previsione verificarne l'effetto che, quando viene distribuito, verrà creato un *Nuovo* servizio Web. Appena non si aggiorna il servizio Web originale.

    Se è necessario modificare la prova di formazione, aprirlo e fare clic su **Salva con nome** per creare una copia. Per lasciare invariate originale prova di formazione, previsione prova e servizio Web. È ora possibile creare un nuovo servizio Web con le modifiche. Dopo aver distribuito il nuovo servizio Web è quindi possibile decidere se arrestare il servizio Web precedente o mantenerlo in esecuzione insieme a quello nuovo.

**Si desidera formare un modello diverso**

Se si desidera apportare modifiche alla prova previsione originale, ad esempio selezionando un algoritmo di apprendimento computer diverso, si tenta di un metodo di formazione diversi e così via, quindi è necessario eseguire la seconda procedura descritta in precedenza per il modello di formazione: aprire prova formazione, fare clic su **Salva con nome** per creare una copia e quindi si avvia verso il basso il nuovo percorso di sviluppo di un modello , la creazione di prova previsione e distribuzione del servizio web. Verrà creato un nuovo sito Web servizio non correlati a quello originale, è possibile decidere quali una o entrambe, per continuare l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sviluppo e prova, vedere gli articoli seguenti:

-   la conversione di prova - [convertire una prova di formazione di apprendimento per una prova di Office](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   distribuzione del servizio Web - [distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   formazione modello - [reimpostare l'apprendimento modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md)

Per esempi dell'intero processo, vedere:

-   [Esercitazione risorse macchina: creare il primo prova in Azure Machine formazione Studio](machine-learning-create-experiment.md)

-   [Procedura dettagliata: Sviluppare una soluzione analitica previsione per la valutazione dei rischi carta di credito apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md)