<properties 
    pageTitle="Che cos'è Azure Machine Learning Studio? | Microsoft Azure"
    description="Panoramica di Azure ML Studio, uno strumento di trascinamento della selezione per creare rapidamente modelli da una raccolta di pronte per l'uso di algoritmi e moduli."
    keywords="Azure apprendimento automatico, azure ml, studio ml"
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
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>Che cos'è Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio è uno strumento di collaborazione, trascinamento della selezione che è possibile utilizzare per creare, testare e distribuire soluzioni analitica stima sui dati. Computer risorse Studio pubblica modelli come servizi web che possono essere facilmente utilizzati da app personalizzate o strumenti di Business Intelligence, ad esempio Excel.

Computer risorse Studio è nel punto in cui soddisfano scienza dati, analitica previsione, le risorse cloud e i dati.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>L'area di lavoro Machine Learning Studio interattivo

Per sviluppare un modello di analisi di stima, in genere utilizzare dati di una o più origini, trasformare e analizzare i dati tramite le varie la modifica dei dati e le funzioni statistiche e generare un set di risultati. Sviluppo di un modello simile è un processo iterativo. Se si modificano le diverse funzioni e i relativi parametri, i risultati convergono finché non è certi di disporre di un modello di esperto ed efficace.

**Azure Machine Learning Studio** offre un'area di lavoro interattivo, grafica per creare, testare e continuerà a lavorare su un modello di analisi di stima. Si trascinamento e ***set di dati*** e analisi dei ***moduli*** in un'area di lavoro interattivo, collegarli per formare un ***provare***, che è possibile eseguire in computer risorse Studio. Per ripetere l'operazione sulla struttura del modello, modificare la prova, salvare una copia se lo si desidera ed eseguire nuovamente. Quando si è pronti, è possibile convertire la ***formazione sperimentare l'utilizzo*** di una ***prova di Office***e quindi pubblicarlo come ***servizio web*** in modo che il modello è possibile accedervi da altri utenti.

>[AZURE.TIP] Per scaricare e stampare un diagramma che fornisce una panoramica delle funzionalità di Studio risorse computer, vedere [il diagramma di panoramica delle funzionalità di Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).

Non c'è bisogno di programmazione, basta visivamente connessione set di dati e moduli per creare il modello di analisi di stima.

![Diagramma di Studio ML Azure: creare esperimenti, leggere i dati per molte origini, scrivere dati punteggio, scrivere modelli.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Guida introduttiva a computer risorse Studio

Quando si inizia il [Computer risorse Studio](https://studio.azureml.net) viene visualizzata la pagina **Home** . Da qui è possibile visualizzare la documentazione, video, webinar e trovare altre risorse utili.

Sono disponibili tre schede nella parte superiore: **Home** (nel punto in cui si avvia), **Studio**e **raccolta**.

### <a name="studio"></a>Studio

Fare clic sulla scheda **Studio** e verrà chiesto di accedere con l'account Microsoft o l'account aziendale o dell'istituto di istruzione. Dopo aver effettuato l'accesso, sulla sinistra verrà visualizzato schede seguenti:

- **Progetti** - insiemi di esperimenti, set di dati, i blocchi appunti e altre risorse che rappresenta un singolo progetto
- **ESPERIMENTI** - esperimenti creata, eseguire e salvati come bozze
- **Servizi WEB** - servizi Web che è stato distribuito da esperimenti
- **I blocchi appunti** - Jupyter blocchi appunti creati
- **Set di dati** - set di dati caricato in Studio
- **Modelli di esperti** - modelli qualificato esperimenti e salvata in Studio
- **Impostazioni** - un insieme di impostazioni che è possibile utilizzare per configurare l'account e risorse.

### <a name="gallery"></a>Raccolta

Fare clic sulla scheda **raccolta** e verrà eseguita nella raccolta di Business Intelligence Cortana. La raccolta è una posizione in cui una community di scienziati dati e sviluppatori condivisibile soluzioni create utilizzando componenti della famiglia di prodotti Intelligence Cortana.

Per ulteriori informazioni sulla raccolta, vedere [Condividi e scoprire soluzioni della raccolta di Business Intelligence Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componenti di una prova

Una prova è costituito da set di dati che forniscono dati analitici moduli, che si connette insieme a creare un modello di analisi di stima. In particolare, una prova valido con queste caratteristiche:

- La prova ha almeno un set di dati e un modulo
- Set di dati potrebbero essere collegati solo per i moduli
- Possono essere collegati a set di dati o altri moduli
- Tutte le porte di input per i moduli devono avere alcune connessione il flusso di dati
- Tutti i necessari per ogni modulo devono essere impostati

È possibile creare una prova da zero oppure è possibile utilizzare una prova di esempio esistente come modello. Per ulteriori informazioni, vedere [esperimenti di esempio di utilizzo per creare nuovi esperimenti](machine-learning-sample-experiments.md).

Per un esempio di creazione di una prova semplice, vedere [creare una semplice prova Azure Machine Learning Studio](machine-learning-create-experiment.md).

Per una procedura dettagliata più completa di creazione di una soluzione di previsione analitica, vedere [sviluppare una soluzione previsione con Azure apprendimento](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Set di dati

Un set di dati sono dati che sono stati caricati in computer risorse Studio in modo che può essere utilizzato il processo di modellazione. Sono disponibili con computer risorse Studio per è possibile sperimentare vari set di dati di esempio ed è possibile caricare set di dati a seconda delle necessità. Ecco alcuni esempi di set di dati inclusi:

- **MPG dati per diversi automobili** - miglia gallone (MPG) i valori per automobili identificate da numeri di cilindri, potenza e così via.
- **Dati cancro petto** - dati di diagnosi cancro petto.
- **Dati della foresta generato** - dimensioni fire foresta in Portogallo nord-est.

Quando si crea una prova è possibile scegliere dall'elenco di set di dati disponibili a sinistra dell'area di disegno.

Per un elenco di set di dati di esempio inclusi in computer risorse Studio, vedere [usare i set di dati di esempio in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Moduli

Un modulo è un algoritmo che è possibile eseguire sui dati. Computer risorse Studio è disponibile un numero di moduli compreso tra le funzioni di ingresso dei dati e formazione punteggio e processi di convalida. Ecco alcuni esempi di moduli inclusi:

- [Convertire in ARFF] [ convert-to-arff] -converte un set di dati .NET serializzato in formato File attributo relazione (ARFF).
- [Calcolare statistiche scuola elementare] [ elementary-statistics] -calcola statistiche scuola elementare, ad esempio Media, deviazione standard e così via.
- [Regressione lineare] [ linear-regression] -consente di creare un modello online sfumatura in base a discesa regressione lineare.
- [Punteggio modello] [ score-model] -punteggi un modello di classificazione o regressione esperto.

Quando si crea una prova è possibile scegliere dall'elenco dei moduli disponibili a sinistra dell'area di disegno.  

Un modulo abbia un insieme di parametri che è possibile utilizzare per configurare algoritmi interno del modulo. Quando si seleziona un modulo nell'area di lavoro, i parametri del modulo vengono visualizzati nel riquadro delle **proprietà** a destra dell'area di disegno. È possibile modificare i parametri in tale riquadro per ottimizzare il modello.

Per alcuni Guida esplorare la raccolta di grandi dimensioni degli algoritmi risorse computer disponibili, vedere [come scegliere algoritmi per l'apprendimento di Microsoft Azure computer](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuzione di un servizio web analitica previsione

Quando il modello di previsione analitica è pronto, è possibile distribuire come un servizio web direttamente dal computer risorse Studio. Per ulteriori informazioni su questa procedura, vedere [distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
