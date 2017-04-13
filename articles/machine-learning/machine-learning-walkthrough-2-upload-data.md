<properties
    pageTitle="Passaggio 2: Caricare dati in una prova di apprendimento | Microsoft Azure"
    description="Passaggio 2 di sviluppare una procedura dettagliata soluzione stima: Upload archiviata dati pubblici Azure Machine Learning Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Procedura dettagliata passaggio 2: Caricare dati esistenti in una prova di apprendimento Azure

Questo è il secondo passaggio della procedura dettagliata, [sviluppare una soluzione analitica previsione in apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Caricare dati esistenti**
3.  [Creare una nuova prova](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Organizzare la formazione e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accedere al servizio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Per sviluppare un modello di previsione per il rischio di carta di credito, è necessario che è possibile utilizzare per organizzare la formazione e quindi verificare il modello di dati. Per questa procedura dettagliata, si userà "UCI Statlog (dati carta di credito tedesco) Set di dati" dal repository UCI apprendimento. È possibile trovarlo qui:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ml/DataSets/Statlog+(German+Credit+Data)</a>

Si userà il file denominato **german.data**. Scaricare questo file sul disco rigido locale.  

Questo set di dati contiene le righe di 20 variabili per 1000 candidati passati per carta di credito. Queste 20 variabili rappresentano vettore caratteristica del set di dati, che fornisce le caratteristiche di identificazione per ogni richiedente carta di credito. Una colonna aggiuntiva in ogni riga rappresenta calcolato rischio il suo, con 700 candidati identificati come un rischio bassa carta di credito e 300 come rischio alto.

Il sito Web UCI viene fornita una descrizione degli attributi di vettore caratteristica che includono informazioni finanziarie, cronologia carta di credito, lo stato e informazioni personali. Per ogni candidato una valutazione binaria è stato specificato che indica se sono una bassa o alta i rischi di credito.  

Si userà questi dati per formare un modello di previsione analitica. Al termine, il nostro modello dovrebbe essere possibile accettare le informazioni per nuovi utenti e prevedere se si tratta di un rischio bassa o alta.  

Ecco una rotazione interessante. La descrizione del set di dati spiega che interpretato come una persona come un rischio bassa quando sono effettivamente un rischio elevato è 5 volte superiori all'istituto finanziario rispetto al interpretato come un rischio bassa più alta. Un modo semplice per tenerne conto nel nostro test è la duplicazione (5 volte) queste voci che rappresentano un utente con un rischio elevato. Quindi, se il modello interpreta un rischio elevato come bassa, non produrrà tale errore di classificazione 5 volte, una volta per ogni duplicato. Ciò aumenta il costo di questo errore nei risultati della formazione.  

##<a name="convert-the-dataset-format"></a>Convertire il formato di set di dati
Il set di dati originale utilizza un formato separati da celle vuote. Computer risorse Studio funziona meglio con un file di valori separati da virgola (CSV), in modo che è necessario convertire il set di dati sostituendo spazi con una virgola.  

Esistono diversi modi per convertire i dati. Un modo è tramite il comando di Windows PowerShell seguente:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Un altro modo è tramite il comando sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

In entrambi i casi, è stata creata una versione dei dati in un file denominato **german.csv** che verrà usato nel nostro test separati da virgola.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Caricare il set di dati in computer risorse Studio

Una volta i dati sono stati convertiti in formato CSV, è necessario caricare nel computer risorse Studio. Per ulteriori informazioni sull'utilizzo di Studio risorse computer, vedere [Microsoft Azure Machine Learning Studio Home](https://studio.azureml.net/).

1.  Aprire apprendimento Studio ([https://studio.azureml.net](https://studio.azureml.net)). Quando viene chiesto di accedere, utilizzare l'account specificato come il proprietario dell'area di lavoro.
1.  Fare clic sulla scheda **Studio** nella parte superiore della finestra.
1.  Fare clic su **+ Nuovo** nella parte inferiore della finestra.
1.  Selezionare **set di dati**.
1.  Selezionare **FILE locale da**.
1.  Nella finestra di dialogo **Carica un nuovo set di dati** , fare clic su **Sfoglia** e individuare il file **german.csv** che è stato creato.
1.  Immettere un nome per il set di dati. Per questa procedura dettagliata, è necessario chiamarlo "UCI dei dati di carte di credito tedesco".
1.  Per tipo di dati, selezionare **File CSV generico senza intestazione (. nh.csv)**.
1.  Se si desidera, aggiungere una descrizione.
1.  Fare clic su **OK**.  

![Caricare il set di dati][1]  


Consente di caricare i dati in un modulo di set di dati che è possibile utilizzare in una prova.

> [AZURE.TIP] Per gestire set di dati caricati in Studio, fare clic sulla scheda **set di dati** a sinistra della finestra di Studio.

Per ulteriori informazioni sull'importazione di diversi tipi di dati in una prova, vedere [importare i dati di formazione in Azure Machine Learning Studio](machine-learning-data-science-import-data.md).

**Argomento successivo: [Crea un nuovo prova](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
