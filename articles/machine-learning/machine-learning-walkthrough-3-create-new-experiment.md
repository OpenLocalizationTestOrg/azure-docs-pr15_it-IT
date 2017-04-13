<properties
    pageTitle="Passaggio 3: Creare una nuova prova di apprendimento | Microsoft Azure"
    description="Passaggio 3 di sviluppare una procedura dettagliata soluzione stima: creare una nuova prova di formazione in Azure Machine Learning Studio."
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
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Procedura dettagliata passaggio 3: Creare una nuova prova di apprendimento Azure

Questo è il terzo passaggio della procedura dettagliata, [sviluppare una soluzione analitica previsione in apprendimento Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Caricare dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.  **Creare una nuova prova**
4.  [Organizzare la formazione e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accedere al servizio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Il passaggio successivo in questa procedura dettagliata consiste nel creare una prova Studio risorse computer che utilizza il set di dati che vengono caricati.  

1.  Studio, fare clic su **+ Nuovo** nella parte inferiore della finestra.
2.  Selezionare **prova**e quindi selezionare "Prova vuota". Selezionare il nome di prova predefinito nella parte superiore dell'area di disegno e assegnargli un nome significativo

    > [AZURE.TIP] È buona norma compilare **Riepilogo** e una **Descrizione** per la prova nel riquadro delle **proprietà** . Queste proprietà offrono la possibilità di documenti di prova, in modo che chiunque utilizzi su di esso in un secondo momento comprendere gli obiettivi e metodologia.

3.  Nel riquadro a sinistra della virgola di prova modulo area di lavoro, quindi espandere **Salvato set di dati**.
4.  Individuare il set di dati creato in **Set di dati personali** e trascinarlo nell'area di lavoro. È inoltre disponibile il set di dati, immettere il nome nella casella di **ricerca** sopra il riquadro.  

##<a name="prepare-the-data"></a>Preparare i dati
È possibile visualizzare le prime 100 righe dei dati e alcune informazioni statistiche per il set di dati intero scegliendo **Visualizza**la porta di output del set di dati (il piccolo cerchio nella parte inferiore).  

Perché il file di dati non è disponibile con intestazioni di colonna, Studio ha fornito generici titoli *(Col1, Col2 ecc.)*. Buone intestazioni non sono essenziali per la creazione di un modello, ma rendono più facile lavorare con i dati di prova. Inoltre, quando si pubblica infine questo modello in un servizio web, le intestazioni di identificare le colonne per l'utente del servizio.  

È possibile aggiungere le intestazioni di colonna utilizzando i [Metadati modificare] [ edit-metadata] modulo.
Utilizzare i [Metadati modificare] [ edit-metadata] modulo per modificare i metadati associati a un set di dati. In questo caso, è possibile offrire più nomi per le intestazioni di colonna. 

Utilizzo [Dei metadati modificare][edit-metadata], è necessario specificare le colonne da modificare (in questo caso, tutti gli elementi.) Successivamente, specificare l'azione da eseguire sulle colonne (in questo caso, modificare le intestazioni di colonna.)

1.  Nel riquadro modulo digitare "metadati" nella casella di **ricerca** . Si noterà [Modifica metadati] [ edit-metadata] vengono visualizzati nell'elenco dei moduli.
2.  Fare clic e trascinare i [Metadati modificare] [ edit-metadata] modulo su area di lavoro e rilasciarlo sotto il set di dati abbiamo aggiunto in precedenza.
3.  Connettere il set di dati per [Modificare i metadati][edit-metadata]: fare clic su porta di output di set di dati (il piccolo cerchio nella parte inferiore del set di dati.) Quindi, trascinare la porta di input di [Modificare i metadati] [ edit-metadata] (nella parte superiore del modulo piccolo cerchio), quindi rilasciare il pulsante del mouse. Il modulo e il set di dati rimanere connessi anche se uno spostarsi sull'area di lavoro.

    La prova dovrebbe essere simile al seguente:  

    ![Aggiunta di metadati di modifica][2]
    
    Il punto esclamativo rosso indica che è ancora non impostare le proprietà per questo modulo. È necessario descritto più avanti.
    
    > [AZURE.TIP] È possibile aggiungere un commento a un modulo facendo doppio clic sul modulo e l'immissione di testo. Questo consente di visualizzare a colpo d'occhio tutte le nel test il modulo. In questo caso, fare doppio clic su [Modifica metadati] [ edit-metadata] modulo e digitare il commento "Aggiungi intestazioni di colonna". Fare clic su qualsiasi altro nell'area di lavoro per chiudere la casella di testo. Per visualizzare il commento, fare clic sulla freccia in giù nel modulo.

4.  Selezionare [Modifica metadati][edit-metadata], quindi nel riquadro **proprietà** a destra dell'area di disegno, fare clic su **Avvia selettore di colonna**.
5.  Nella finestra di dialogo **Selezionare le colonne** , selezionare tutte le righe in **Colonne disponibili** e fare clic su > per spostare le **Colonne selezionate**.
La finestra di dialogo dovrebbe risultare analoga alla seguente: ![selettore di colonna con tutte le colonne selezionate][4]
7.  Fare clic sul segno di spunta **OK** .
8.  Nel riquadro delle **proprietà** , cercare il parametro **nuovi nomi di colonna** . In questo campo, immettere un elenco dei nomi per le 21 colonne set di dati, separati da virgole e in ordine delle colonne. È possibile ottenere i nomi delle colonne della documentazione di set di dati nel sito Web UCI o per comodità è possibile copiare e incollare l'elenco seguente:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Riquadro attività proprietà aspetto simile al seguente:

    ![Proprietà dei metadati di modifica][1]

> [AZURE.TIP] Se si desidera verificare le intestazioni di colonna, eseguire la prova (sotto l'area di prova, scegliere **Esegui** ). Termine dell'esecuzione (venga visualizzato un segno di spunta verde sulla [Modifica metadati][edit-metadata]), fare clic su porta di output di [Modificare i metadati] [ edit-metadata] modulo e selezionare **Visualizza**. È possibile visualizzare l'output di tutti i moduli nello stesso modo per visualizzare lo stato di avanzamento dei dati tramite la prova.

##<a name="create-training-and-test-datasets"></a>Creare formazione e set di dati di test

Il passaggio successivo della prova consiste nel generare set di dati separato che verrà usato per la formazione e verifica il nostro modello.

A tale scopo, Microsoft utilizza i [Dati divisi] [ split] modulo.  

1.  Trovare i [Dati divisi] [ split] modulo, trascinarlo nell'area di lavoro e connetterlo al ultima [Modifica metadati] [ edit-metadata] modulo.
2.  Per impostazione predefinita, il rapporto di divisione è 0,5 e il parametro **Randomized dividere** è impostato. Questo significa che mezzo casuale dei dati di output tramite una porta i [Dati divisi] [ split] modulo e metà a altra. È possibile modificare queste, nonché il parametro **Generatore** , per modificare la divisione tra formazione e verifica dei dati. In questo esempio, è necessario tenuti come-è.
    
    > [AZURE.TIP] La proprietà **frazione di righe nel primo set di dati di output** determina la quantità di dati output tramite la porta di output a sinistra. Ad esempio, se si imposta il rapporto su 0,7, quindi 70% dei dati è output tramite la porta a sinistra e 30% tramite la porta appropriata.  
    
3. Fare doppio clic sui [Dati divisi] [ split] modulo e immettere il commento, "formazione/test dati divisi 50%". 

È possibile utilizzare l'output dei [Dati divisi] [ split] modulo è ad esempio, tuttavia si sceglie di usare l'output sinistro come dati di formazione e destra output dei dati di test.  

Come indicato nel sito Web UCI, il costo di interpretato come un rischio elevato come bassa supera cinque volte il costo di interpretato come un rischio bassa più alta. Per questa situazione, è generare un nuovo set di dati che riflette questa funzione di costo. Il nuovo set di dati, ogni esempio ad alto rischio viene replicato cinque volte, mentre non viene replicato ogni esempio basso rischio.   

Effettuare la replica tramite codice R:  

1.  Trovare e trascinare l' [Esecuzione di Script R] [ execute-r-script] modulo nella prova tela e collegare la porta di output a sinistra dei [Dati divisi] [ split] modulo per la porta input ("Dataset1") prima di [Eseguire Script R] [ execute-r-script] modulo.
2. Fare doppio clic su [Esegui Script R] [ execute-r-script] modulo e immettere il commento, "Impostare rettifica costo".
2.  Nel riquadro **proprietà** eliminare il testo predefinito nel parametro **R Script** e immettere questo script:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


È necessario eseguire l'operazione di replica stessa per ogni output dei [Dati divisi] [ split] modulo in modo che i dati di test e formazione abbiano lo stesso adeguamento di costo.

1.  Pulsante destro del mouse [Eseguire Script R] [ execute-r-script] modulo e selezionare **Copia**.
2.  Pulsante destro del mouse nell'area di lavoro di prova e scegliere **Incolla**.
3.  Collegare la porta input prima di [Eseguire Script R] [ execute-r-script] modulo a destra di output porta i [Dati divisi] [ split] modulo. 
4.  Nella parte inferiore dell'area di disegno, fare clic su **Esegui**. 

> [AZURE.TIP] La copia del modulo di eseguire Script R contiene lo stesso script del modulo originale. Quando si copia e Incolla di un modulo nell'area di lavoro, la copia conserva tutte le proprietà della cella originale.  

Il nostro prova ora ha un aspetto simile al seguente:

![Aggiunta di script R e modulo divisa][3]

Per ulteriori informazioni sull'uso di script R in esperimenti, vedere [prolunga lo sperimentare R](machine-learning-extend-your-experiment-with-r.md).

**Argomento successivo: [treno e valutare i modelli di](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
