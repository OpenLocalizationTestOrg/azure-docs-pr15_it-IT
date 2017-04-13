<properties
    pageTitle="Passaggio 4: Organizzare la formazione e valutare i modelli di analitici stima | Microsoft Azure"
    description="Passaggio 4 di sviluppare una procedura dettagliata soluzione stima: treno, punteggio e valutare più modelli di Azure Machine Learning Studio."
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


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Procedura dettagliata passaggio 4: Organizzare la formazione e valutare i modelli di analitici previsione

In questo argomento contiene il quarto passaggio della procedura dettagliata, [sviluppare una soluzione analitica previsione in apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Caricare dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.  [Creare una nuova prova](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Organizzare la formazione e valutare i modelli**
5.  [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accedere al servizio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Uno dei vantaggi dell'utilizzo di Azure Machine formazione Studio per la creazione di modelli di apprendimento computer è la possibilità di provare a più di un tipo di modello alla volta in una prova e confrontare i risultati. Questo tipo di sperimentazione consente di individuare la soluzione migliore per il problema.

Nel test che è stiamo sviluppo in questa procedura dettagliata, si verrà creare due diversi tipi di modelli e quindi confrontare i relativi risultati punteggio per decidere quale algoritmo di cui si desidera utilizzare nel nostro test finale.  

Sono disponibili diversi modelli, è possibile utilizzare da. Per visualizzare i modelli disponibili, espandere il nodo **Apprendimento** nella tavolozza di modulo e quindi espandere **Inizializzare modello** e i nodi di sotto di esso. Ai fini di questa prova, è necessario selezionare il supporto vettore computer (SVM) e i moduli di classe due aumentata strutture decisionali.    

> [AZURE.TIP] Per ottenere ulteriori informazioni sulla scelta quale algoritmo di apprendimento migliore si adatta il problema specifico che si sta tentando di risolvere, vedere [come scegliere algoritmi per l'apprendimento di Microsoft Azure](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Formare i modelli
Prima di tutto, impostare il modello di albero decisionale invece un incremento:  

1.  Individuare l' [Albero decisionale aumentata due classe] [ two-class-boosted-decision-tree] modulo nella tavolozza di modulo e trascinarlo nell'area di lavoro.
2.  Trovare il [Modello treno] [ train-model] modulo, trascinarlo nell'area di lavoro e connettere l'output del modulo di albero decisionale invece un incremento la porta di input a sinistra ("modello inesperti") del [Modello treno] [ train-model] modulo.
    
    L' [Albero decisionale aumentata due classe] [ two-class-boosted-decision-tree] modulo viene inizializzato il modello generico e il [Modello formare] [ train-model] formazione dati vengono utilizzati per formare il modello. 
     
3.  Collegare l'output a sinistra ("set di risultati") di sinistra [Eseguire Script R] [ execute-r-script] modulo a destra di input porta ("set di dati") del [Modello treno] [ train-model] modulo.

    > [AZURE.TIP] Non è più necessario due di input e uno di output di [Eseguire Script R] [ execute-r-script] modulo per questo esercizio, in modo che sono tenuti non collegato. 

4.  Selezionare il [Modello treno] [ train-model] modulo. Nel riquadro **proprietà** , fare clic su **Avvia selettore di colonna**, selezionare **Tutti i tipi** di elenco a discesa verso il basso in **Colonne disponibili** e immettere "Credito rischio" nel campo di testo. Selezionare **Tutti i tipi di** nell'elenco a discesa in **Colonne selezionate**. Selezionare "Credito rischio" e fare clic sulla freccia evidenziati per spostarla in **Colonne selezionate**. 
5.  Fare clic su **Salva**.


La parte di prova ora ha un aspetto simile al seguente:  

![Training di un modello][1]

Successivamente, è necessario impostare il modello SVM.  

Innanzitutto, una piccola spiegazione SVM. Decisionali invece un incremento funzionano bene con le funzionalità di qualsiasi tipo. Tuttavia, poiché il modulo SVM genera un classificatore lineare, il modello che genera è visualizzato l'errore di test migliore quando tutte le caratteristiche numeriche avere la stessa scala. Per convertire tutte le caratteristiche numeriche la stessa scala, viene utilizzata una trasformazione "Tanh" (con i [Dati normalizzare] [ normalize-data] modulo.) Consente di trasformare i numeri nell'intervallo [0,1]. Funzioni stringa vengono convertiti dal modulo SVM alle caratteristiche per categoria e quindi alle caratteristiche di 1/0 binari, in modo che non è necessario trasformare manualmente funzioni stringa. Inoltre, non si desidera trasformare il rischio colonna (21) - è numerico, ma è il valore che è stiamo training del modello per prevedere, pertanto è necessario lasciare invariato.  

Per configurare il modello SVM, eseguire le operazioni seguenti:

1.  Trovare la [Classe due supporto vettore computer] [ two-class-support-vector-machine] modulo nella tavolozza di modulo e trascinarlo nell'area di lavoro.
2.  Pulsante destro del mouse nel [Modello di treno] [ train-model] modulo, selezionare **Copia**, pulsante destro del mouse nell'area di lavoro e selezionare **Incolla**. La copia del [Modello treno] [ train-model] modulo ha la stessa selezione della colonna dell'originale.
3.  Collegare l'output del modulo SVM alla porta input a sinistra ("modello inesperti") del secondo [Modello treno] [ train-model] modulo.
4.  Trovare i [Dati normalizzare] [ normalize-data] modulo e trascinarlo nell'area di lavoro.
5.  Connettere l'input di questo modulo per l'output a sinistra di sinistra [Eseguire Script R] [ execute-r-script] modulo (si noti che la porta di output di un modulo può essere connesso a più di un altro modulo).
6.  Collegare la porta di output a sinistra ("trasformato set di dati") dei [Dati normalizzare] [ normalize-data] porta ("set di dati") del secondo [Treno modello] di modulo a destra input[ train-model] modulo.
7.  Nel riquadro delle **proprietà** per i [Dati normalizzare] [ normalize-data] modulo, selezionare **Tanh** per il parametro del **metodo di trasformazione** .
8.  Fare clic su **Avvia selettore di colonna**, non selezionare "colonne" per **Iniziare con**, selezionare **Includi** nella prima casella di riepilogo, selezionare **il tipo di colonna** nella seconda casella di riepilogo e selezionare **numerico** nell'elenco a discesa di terzo. Specifica che vengono trasformati tutte le colonne numeriche (e solo numerici).
9.  Fare clic sul segno più (+) a destra della riga: consente di creare una riga di elenchi a discesa. Selezionare **Escludi** dall'elenco a discesa prima, selezionare **i nomi di colonna** nella seconda casella di riepilogo e fare clic sul campo di testo e scegliere "Credito rischio" dall'elenco di colonne. Consente di specificare che la colonna di rischio deve essere ignorata (è necessario eseguire questa operazione poiché questa colonna è un valore numerica e in modo che in caso contrario viene trasformato).
10. Fare clic su **OK**.  


[Normalizzare dati] [ normalize-data] modulo è ora configurato per eseguire una trasformazione Tanh in tutte le colonne numeriche ad eccezione della colonna di rischio.  

Questa parte del nostro prova dovrebbe essere simile al seguente:  

![Formazione secondo modello][2]  

##<a name="score-and-evaluate-the-models"></a>Punteggio e valutare i modelli

Microsoft Usa i dati di test è stati separati dai [Dati divisi] [ split] modulo accumulare i modelli di esperti. È quindi possibile confrontare i risultati dei due modelli per verificare che segue risultati migliori.  

1.  Trovare il [Modello di punteggio] [ score-model] modulo e trascinarlo nell'area di lavoro.
2.  Connettere il [Modello treno] [ train-model] modulo che sia connesso a [Due classe aumentata decisione albero] [ two-class-boosted-decision-tree] modulo a sinistra di input porta del [Modello di punteggio] [ score-model] modulo.
3.  Collegare la porta input destra del [Modello di punteggio] [ score-model] modulo per l'output a sinistra di destra [Eseguire Script R] [ execute-r-script] modulo.

    Il [Modello di punteggio] [ score-model] modulo può richiedere informazioni sul credito dai dati di test, eseguirlo tramite il modello e confrontare le stime generazione del modello con la colonna di rischio effettivo carta di credito nei dati di test ora.

4.  Copiare e incollare il [Modello di punteggio] [ score-model] modulo per creare una seconda copia o trascinare un nuovo modulo nell'area di lavoro.
5.  Collegare la porta di input a sinistra del modulo nel modello di SVM (in altre parole, connettersi alla porta di output del [Modello treno] [ train-model] modulo che sia connesso a [Due Class supporto vettore Machine] [ two-class-support-vector-machine] modulo).
6.  Per il modello SVM, è necessario eseguire la stessa trasformazione per i dati di test come è stato fatto i dati di formazione. Quindi copiare e incollare i [Dati normalizzare] [ normalize-data] modulo per creare una seconda copia e connetterlo all'output a sinistra di destra [Eseguire Script R] [ execute-r-script] modulo.
7.  Collegare la porta input destra del [Modello di punteggio] [ score-model] modulo per l'output a sinistra dei [Dati normalizzare] [ normalize-data] modulo.  

Per valutare i risultati di punteggio, serve un [Modello di valutare] [ evaluate-model] modulo.  

1.  Trovare il [Modello di valutare] [ evaluate-model] modulo e trascinarlo nell'area di lavoro.
2.  Collegare la porta di input a sinistra alla porta di output del [Modello di punteggio] [ score-model] modulo associato al modello di albero decisionale invece un incremento.
3.  Collegare la porta input destra a altro [Modello punteggio] [ score-model] modulo.  

Per eseguire la prova, fare clic sul pulsante **Esegui** sotto l'area. Può richiedere alcuni minuti. Indicatore di rotazione su ciascun modulo indica che è in esecuzione e un segno di spunta verde al termine il modulo. Quando tutti i moduli dispongono di un segno di spunta, la prova è terminata.

La prova dovrebbe essere simile al seguente:  

![Valutazione di entrambi i modelli][3]

Per controllare i risultati, fare clic su porta di output del [Modello di valutare] [ evaluate-model] modulo e selezionare **Visualizza**.  

Il [Modello di valutare] [ evaluate-model] modulo viene prodotta una coppia di curve e metriche che consentono di confrontare i risultati dei modelli di punteggio due. È possibile visualizzare i risultati come curve ricevitore operatore caratteristica (ROC), precisione/richiamo curve o curve ascensore. Dati aggiuntivi visualizzati includono una matrice di confusione, cumulativa i valori per l'area sotto la curva (AUC) e altre metriche. È possibile modificare il valore di soglia spostando il dispositivo di scorrimento verso sinistra o destra e verificarne l'effetto il set di criteri di misurazione.  

A destra del grafico, fare clic su **set di dati Scored** o **Scored set di dati per confrontare** per evidenziare la curva associata e visualizzare i parametri associati riportata di seguito. Nella legenda per le curve "Totalizza un set di dati" corrisponde alla porta di input a sinistra del [Modello di valutare] [ evaluate-model] modulo - in questo caso, si tratta del modello di albero decisionale invece un incremento. "Totalizza un set di dati per confrontare" corrisponde alla porta di input destra - modello SVM in questo caso. Quando si fa clic su una delle etichette, curva per quest ' ultimo viene evidenziato e visualizzare le metriche corrispondente, come illustrato nella figura seguente.  

![Curve ROC per i modelli][4]

Esaminando questi valori, è possibile decidere quale sia il modello più vicino alla fornisce i risultati che si sta cercando. È possibile tornare indietro e continuerà a lavorare sulla prova modificando i valori in modelli diversi. 

> [AZURE.TIP] Ogni volta che si esegue la prova un record di tale iterazione da mantenere nella cronologia eseguire. È possibile visualizzare queste iterazioni e tornare a uno di essi, facendo clic su **Visualizza cronologia eseguire** sotto l'area. È anche possibile fare clic su **Eseguire precedente** nel riquadro delle **proprietà** per tornare alla iterazione immediatamente precedente di si dispone di aperto.
> 
È possibile creare una copia di qualsiasi iterazione la prova facendo clic su **Salva con nome** sotto l'area. Utilizzare le proprietà di **Riepilogo** e la **Descrizione** di prova per tenere traccia di ciò che si tentato le iterazioni di prova.

>  Per ulteriori informazioni, vedere [gestire provare iterazioni in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Argomento successivo: [distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
