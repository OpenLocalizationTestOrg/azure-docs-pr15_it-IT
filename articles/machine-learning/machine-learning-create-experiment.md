<properties
    pageTitle="Un esperimento semplice in computer Learning Studio | Microsoft Azure"
    description="In questa esercitazione learning automatica illustra un esperimento science semplificata dei dati. È possibile prevedere il prezzo di un auto tramite un algoritmo di regressione."
    keywords="sperimentare regressione lineare, gli algoritmi di apprendimento automatica, esercitazione learning automatica, modellazione predittiva tecniche, dati science esperimento"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Esercitazione di apprendimento automatica: creare il primo esperimento science dati in Azure automatica di apprendimento Studio

In questa esercitazione learning automatica illustra un esperimento science dati semplice. Verrà creato un modello di regressione lineare che prevede il prezzo di un automobile in base a diverse variabili come effettuare e specifiche tecniche. A tale scopo, utilizzeremo Azure automatica Learning Studio per sviluppare ed eseguire un'iterazione su una semplice predittiva sperimentare analitica.

*Analitica predittiva* è un tipo di science dati che utilizza dati correnti per prevedere i risultati futuri. Per un esempio più semplice di analitica predittiva, guardare Science dati per principianti video 4: [prevedere una risposta con un semplice modello](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (fase di esecuzione: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>In che modo facilita Studio Learning automatica?

Studio di apprendimento automatica semplifica impostare un esperimento mediante trascinamento e i moduli preprogrammati con le tecniche di modellazione predittiva. Per eseguire l'esperimento e prevedere una risposta, si utilizzerà automatica Learning Studio per *creare un modello*, *treno il modello*e *punteggio e test del modello*.

Immettere automatica Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Se è stato effettuato l'accesso a computer Learning Studio prima, fare clic su **accesso qui**. In caso contrario, fare clic su **Esegui iscrizione** e scegliere tra le opzioni di pagamento.

Per ulteriori informazioni generali sulla macchina Learning Studio, vedere [che cos'è automatica Learning Studio?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Cinque passaggi per creare un esperimento

In questo computer apprendimento esercitazione, si verranno eseguire cinque passaggi di base per creare un esperimento in computer di apprendimento Studio per creare, formazione e il modello di punteggio:

- Creare un modello
    - [Passaggio 1: Caricare i dati]
    - [Passaggio 2: Pre-elaborazione dei dati]
    - [Passaggio 3: Definire le funzionalità]
- Formazione del modello
    - [Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]
- Punteggio e il modello di testing
    - [Passaggio 5: Prevedere nuovi prezzi automobili]

[Passaggio 1: Caricare i dati]: #step-1-get-data
[Passaggio 2: Pre-elaborazione dei dati]: #step-2-preprocess-data
[Passaggio 3: Definire le funzionalità]: #step-3-define-features
[Passaggio 4: Scegliere e applicare un algoritmo di apprendimento]: #step-4-choose-and-apply-a-learning-algorithm
[Passaggio 5: Prevedere nuovi prezzi automobili]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Passaggio 1: Caricare i dati

Un numero di set di dati di esempio inclusi in computer Learning Studio che è possibile scegliere tra ed è possibile importare dati da un numero di origini. In questo esempio verrà utilizzato il set di dati di esempio incluso, **dati sui prezzi Automobile (Raw)**.
Questo set di dati include le voci per un numero di singoli automobili, incluse le informazioni su come effettuare, modello, specifiche tecniche e prezzo.

1. Avviare un nuovo esperimento facendo clic su **+ Nuovo** nella parte inferiore della finestra di Studio Learning automatica, selezionare **SPERIMENTARE**e selezionare **Sperimentare vuoto**. Selezionare il nome di esperimento predefinito nella parte superiore dell'area di lavoro e assegnargli un nome significativo, ad esempio, **stima prezzo Automobile**.

2. A sinistra dell'esperimento area di disegno è una tavolozza dei set di dati e moduli. Tipo **automobile** nella casella di ricerca nella parte superiore di questa tavolozza per individuare il set di dati l'etichetta **dati sui prezzi Automobile (Raw)**.

    ![Ricerca tavolozza][screen1a]

3. Trascinare il set di dati all'area di disegno esperimento.

    ![Set di dati][screen1]

Per visualizzare l'aspetto di dati, fare clic su porta di output nella parte inferiore del dataset automobili e quindi scegliere **Visualizza**.

![Porta di output di modulo][screen1c]

Le variabili di set di dati verranno visualizzati come colonne e ogni istanza di un'automobile verrà visualizzata come una riga. La colonna estremità destra (colonna 26 e intitolato "prezzo") è la variabile di destinazione che verranno esaminati tenta di prevedere.

![Visualizzazione dei set di dati][screen1b]

Chiudere la finestra di visualizzazione facendo clic sulla "**x**" nell'angolo superiore destro.

## <a name="step-2-preprocess-data"></a>Passaggio 2: Pre-elaborazione dei dati

Un set di dati in genere richiede alcuni pre-elaborazione prima che può essere analizzata. È possibile notare i valori mancanti presenti nelle colonne delle righe diverse. Questi valori mancanti devono essere puliti in modo che il modello può analizzare i dati in modo corretto. In questo caso, si verranno rimuovere tutte le righe contenenti valori mancanti. Inoltre, la colonna **perdite normalizzato** ha una parte considerevole di valori mancanti, in modo che si sarà escludere la colonna dal modello del tutto.

> [AZURE.TIP] La pulizia di valori mancanti dai dati di input è un prerequisito per utilizzando la maggior parte dei moduli.

Innanzitutto è rimuoverà la colonna **perdite normalizzati** e si verranno quindi rimuovere tutte le righe che vi sono dati mancanti.

1. Digitare **Selezionare le colonne** nella casella di ricerca nella parte superiore della tavolozza modulo per trovare la [Selezione di colonne nel set di dati] [ select-columns] modulo e quindi trascinare per esperimento tela e connettersi alla porta di output del dataset **dati sui prezzi Automobile (Raw)** . In questo modulo consente di selezionare le colonne di dati si desidera includere o escludere nel modello.

2. Selezionare la [Selezione di colonne nel set di dati] [ select-columns] modulo e fare clic **sulla barra di avvio selettore di colonna** nel riquadro delle **proprietà** .

    - A sinistra, fare clic su **con le regole**
    - Nella casella **Inizia con**, fare clic su **tutte le colonne**. In questo modo [Seleziona colonne nel set di dati] [ select-columns] superare tutte le colonne (ad eccezione di quelli siamo da escludere).
    - Da elenchi a discesa, selezionare **escludere** e **i nomi di colonna**e quindi fare clic su all'interno della casella di testo. Viene visualizzato un elenco di colonne. Selezionare **le perdite normalizzati**e verrà aggiunto alla casella di testo.
    - Fare clic sul pulsante segno di spunta (OK) per chiudere il selettore di colonna.

    ![Selezionare le colonne][screen3]

    Il riquadro delle proprietà per la **Selezione di colonne nel set di dati** indica che tutte le colonne verrà attraversati dal set di dati, ad eccezione di **perdite normalizzato**.

    ![Selezionare le colonne nelle proprietà del set di dati][screen4]

    > [AZURE.TIP] È possibile aggiungere un commento a un modulo facendo doppio clic del modulo e immissione di testo. Ciò consente di visualizzare rapidamente operazioni l'esperimento il modulo. In questo caso, fare doppio clic sulla [Selezione di colonne nel set di dati] [ select-columns] modulo e digitare il commento "escludere perdite normalizzato."

3. Trascinare i [Dati mancanti pulita] [ clean-missing-data] modulo per l'esperimento tela e connettersi al [Seleziona colonne nel set di dati] [ select-columns] modulo. Nel riquadro **proprietà** , selezionare **Rimuovi intera riga** in **modalità di pulizia** per pulire i dati di rimozione di righe che contengono valori mancanti. Fare doppio clic sul modulo e digitare il commento "Rimuovere righe valore mancanti".

    ![Proprietà dei dati mancanti pulita][screen4a]

4. Eseguire l'esperimento facendo clic su **Esegui** in area di prova.

Al termine dell'esperimento, tutti i moduli di dispongono di un segno di spunta verde per indicare che è stato eseguito correttamente. Si noti inoltre lo stato di **Installazione completata in esecuzione** nell'angolo superiore destro.

![Esperimento prima eseguita][screen5]

Tutto svolto nella esperimento a questo punto è eliminare i dati. Se si desidera visualizzare il set di dati puliti, fare clic su porta di output sinistro dei [Dati mancanti pulita] [ clean-missing-data] modulo ("pulito dataset") e selezionare **Visualizza**. Si noti che nella colonna **perdite normalizzato** non è più inclusa e non sono presenti valori mancanti.

Ora che i dati sono puliti, siamo pronti per specificare quali funzionalità verranno esaminati da utilizzare nel modello di previsione.

## <a name="step-3-define-features"></a>Passaggio 3: Definire le funzionalità

Nel computer di formazione, *funzionalità* sono le singole proprietà misurabili di determinati elementi o si è interessati. Il set di dati, ogni riga rappresenta una automobile e ogni colonna è una funzionalità di tale automobile.

Ricerca di una buona set di caratteristiche per la creazione di un modello di stima richiede la sperimentazione e informazioni sul problema da risolvere. Alcune caratteristiche sono migliore per la previsione destinazione rispetto ad altri. Inoltre, alcune caratteristiche sono una correlazione sicura con altre caratteristiche (ad esempio, città-mpg confronto strada mpg), in modo che non aggiunge quantità nuove informazioni per il modello e possono essere rimosse.

È opportuno creare un modello che utilizza un sottoinsieme delle caratteristiche nel set di dati. Si torna e selezionare diverse caratteristiche, eseguire di nuovo l'esperimento e verificare se si ottengono risultati migliori. Ma per iniziare, provare le funzionalità seguenti:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Trascinare un'altra [Selezione colonne nel set di dati] [ select-columns] modulo per esperimento tela e connettersi alla porta di output sinistro dei [Dati mancanti pulita] [ clean-missing-data] modulo. Fare doppio clic sul modulo e digitare "Selezionare le funzionalità per la previsione."

2. Fare clic su **avvio selettore di colonna** nel riquadro delle **proprietà** .

3. Fare clic su **con le regole**.

4. Nella casella **Iniziano con** fare clic su **colonne non**e selezionare **Includi** e **i nomi di colonna** nella riga filtro. Immettere l'elenco di nomi di colonna. In questo modo il modulo superare solo colonne in cui viene specificato.

    > [AZURE.TIP] Eseguendo l'esperimento è stato verificato che le definizioni delle colonne per i dati di passano il set di dati attraverso i [Dati mancanti pulita] [ clean-missing-data] modulo. Altri moduli che si connette saranno pertanto anche informazioni dal set di dati.

5. Fare clic sul pulsante segno di spunta (OK).

![Selezionare le colonne][screen6]

Ciò genera il set di dati che verrà utilizzato nell'algoritmo di apprendimento nei passaggi successivi. In seguito, è possibile ottenere e riprovare a una selezione delle caratteristiche diversa.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Passaggio 4: Scegliere e applicare un algoritmo di apprendimento

Ora che i dati sono pronti, la costruzione di un modello di stima costituita da testare e corsi di formazione. I dati verrà utilizzata per formare il modello e quindi verificare il modello per visualizzare la distanza è possibile prevedere i prezzi. Per il momento, non è importante perché è necessario formare e verificare un modello.

*Classificazione* e *regressione* sono due tipi di computer sorvegliati informazioni tecniche. Classificazione prevede una risposta da un insieme definito di categorie, ad esempio un colore (red, blue o verde). Consente di prevedere un numero di regressione.

Dal momento che si desidera prevedere prezzo, ovvero un numero, verrà utilizzato un modello di regressione. In questo esempio, ci sarà formare un modello di *regressione lineare* semplice e nel passaggio successivo è sarà testarlo.

1. I dati viene utilizzato per la formazione e verifica la suddivisione in formazione separata e set di test. Selezionare quindi trascinare i [Dati divisi] [ split] modulo per l'esperimento tela e connettersi all'output dell' ultimo [Seleziona colonne nel set di dati] [ select-columns] modulo. Impostato da 0,75 **frazione delle righe nel primo set di dati di output** . In questo modo, è sarà utilizzare il 75% dei dati di formare il modello e tenere premuto il 25% per i test.

    > [AZURE.TIP] Se si modifica il parametro **Generatore** , si possono produrre diversi esempi casuali per test e corsi di formazione. Questo parametro determina il seeding del generatore di numeri pseudocasuale.

2. Eseguire l'esperimento. In questo modo, la [Selezione di colonne nel set di dati] [ select-columns] e [Dati divisi] [ split] moduli da passare le definizioni delle colonne per i moduli verranno aggiunte successivamente.  

3. Per selezionare l'algoritmo di programmazione, espandere la categoria **Di apprendimento automatica** della tavolozza dei colori modulo a sinistra dell'area di disegno e quindi espandere **Modello inizializzare**. Verranno visualizzati diverse categorie di moduli che possono essere utilizzati per inizializzare gli algoritmi di apprendimento automatica.

    Per questo esperimento selezionare [Regressione lineare] [ linear-regression] modulo sotto la categoria di **regressione** (è possibile anche trovare il modulo digitando "regressione lineare" nella casella di ricerca tavolozza) e trascinarlo all'area di disegno esperimento.

4. Trovare e trascinare il [Modello treno] [ train-model] modulo all'area di disegno esperimento. Collegare la porta di input sinistra l'output di [Regressione lineare] [ linear-regression] modulo. Collegare la porta di input appropriata per l'output di dati di formazione (porta sinistra) dei [Dati divisi] [ split] modulo.

5. Selezionare il [Modello treno] [ train-model] modulo, fare clic su **avvio selettore di colonna** nel riquadro **proprietà** e quindi selezionare la colonna **prezzo** . Questo è il valore che il modello verrà prevedere.

    ![Selezionare la colonna "prezzo"][screen7]

6. Eseguire l'esperimento.

Il risultato è un modello di regressione adeguatamente formati che può essere utilizzato per punteggio nuovi esempi per previsioni.

![Applicare l'algoritmo di programmazione automatica][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Passaggio 5: Prevedere nuovi prezzi automobili

Ora che sono state addestrati il modello mediante il 75% dei dati, è possibile utilizzarlo per punteggio il rimanente 25% dei dati per verificare il corretto funzioni il nostro modello.

1. Trovare e trascinare il [Modello di punteggio] [ score-model] modulo a esperimento tela e collegare la porta di input sinistra l'output del [Modello treno] [ train-model] modulo. Collegare la porta di input destra a test l'output dei dati (destra porta) dei [Dati divisi] [ split] modulo.  

    ![Modulo modello punteggio][screen8a]

2. Per eseguire l'esperimento e visualizzare l'output dal [Modello punteggio] [ score-model] modulo, fare clic su porta di output e quindi scegliere **Visualizza**. L'output mostra i valori previsti per il prezzo e i valori dei dati di test.  

3. Infine, per verificare la qualità dei risultati, selezionare e trascinare il [Modello di valutare] [ evaluate-model] modulo per l'esperimento tela e collegare la porta di input sinistra l'output del [Modello di punteggio] [ score-model] modulo. (Vi sono due porte inpue perché il [Modello di valutare] [ evaluate-model] modulo può essere utilizzato per confrontare due modelli.)

4. Eseguire l'esperimento.

Per visualizzare l'output del [Modello di valutare] [ evaluate-model] modulo, fare clic su porta di output e quindi scegliere **Visualizza**. Per il nostro modello vengono visualizzate le statistiche seguenti:

- **Media di errore assoluto** (Modo): la media di errori assoluti (un *errore* è la differenza tra il valore previsto e il valore effettivo).
- **Errore quadrati Media radice** (RMSE): la radice quadrata della media dei quadrati degli errori di stime apportate al set di dati di test.
- **Errore relativo a assoluto**: la media di errori assoluti rispetto al assoluto della differenza tra i valori effettivi e la media di tutti i valori effettivi.
- **Errore relativo a quadrati**: la media dei quadrati degli errori relativa al quadrato della differenza tra i valori effettivi e la media di tutti i valori effettivi.
- **Calcolo del coefficiente di determinazione**: noto anche come il **valore R al quadrato**, si tratta di una metrica statistica che indica come anche un modello soddisfa i dati.

Per ogni errore sono preferibile statistiche più piccole. Un valore inferiore indica che le stime più fedelmente i valori effettivi. Ai fini **Del coefficiente di determinazione**, il relativo valore è vicino a uno (1.0), al meglio le stime.

![Risultati della valutazione][screen9]

Esperimento finale dovrebbe essere simile al seguente:

![Esercitazione di apprendimento automatica: completare esperimento di regressione lineare che utilizza tecniche di modellazione predittiva.][screen10]

## <a name="next-steps"></a>Passaggi successivi

Ora che una volta completata un'esercitazione learning automatica prima e disporre l'esperimento imposta, è possibile eseguire un'iterazione per il miglioramento del modello. Ad esempio, è possibile modificare la funzionalità utilizzate nella previsione. È inoltre possibile modificare le proprietà di [Regressione lineare] [ linear-regression] algoritmo o prova completamente un algoritmo diverso. È anche possibile aggiungere più computer apprendimento algoritmi per l'esperimento contemporaneamente e confrontare due tramite il [Modello di valutare] [ evaluate-model] modulo.

> [AZURE.TIP] Utilizzare il pulsante **Salva con nome** in area di prova per copiare un'iterazione dell'esperimento. È possibile visualizzare tutte le iterazioni dell'esperimento facendo clic su **Visualizza cronologia eseguire** sotto l'area di disegno. Vedere [Manage sperimentare iterazioni in Azure automatica Learning Studio] [ runhistory] per ulteriori informazioni.

[runhistory]: machine-learning-manage-experiment-iterations.md

Quando si è soddisfatti del modello, è possibile distribuire come un servizio web da utilizzare per prevedere prezzi automobili utilizzando nuovi dati. Vedere [distribuzione di un servizio web di apprendimento Azure] [ publish] per ulteriori informazioni.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Per una procedura dettagliata più estesa e dettagliata delle modellazione predittiva tecniche per la creazione, formazione, punteggio e la distribuzione di un modello, vedere [Develop una soluzione di stima tramite l'apprendimento Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
