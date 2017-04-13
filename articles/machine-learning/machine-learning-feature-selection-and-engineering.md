<properties
    pageTitle="Portare in primo piano progettazione e la selezione di apprendimento Azure | Microsoft Azure"
    description="Spiega allo scopo di selezione delle caratteristiche e Progettazione funzionalità e vengono forniti esempi del proprio ruolo nel processo di ottimizzazione dei dati di apprendimento."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Portare in primo piano progettazione e la selezione di apprendimento Azure

In questo argomento illustra gli scopi di progettazione di funzionalità e selezione delle caratteristiche del processo di ottimizzazione dei dati di apprendimento. Viene illustrato cosa questi processi implicano l'invio tramite esempi forniti da Azure Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

I dati di formazione usati in apprendimento spesso possono essere migliorati per la selezione o l'estrazione delle funzionalità raccolti i dati non elaborati. Esempio di una caratteristica ingegneria nel contesto di imparare a classificare le immagini di caratteri scritta a mano è una mappa di bit densità creata dai dati di distribuzione bit elaborati. Questa mappa consente di individuare in modo più efficiente rispetto alla distribuzione elaborata bordi dei caratteri.

Caratteristiche ingegneria e selezionati aumentano l'efficienza del processo di formazione che tenta di estrarre le informazioni chiave contenute nei dati. Facilitano inoltre la potenza di questi modelli per classificare i dati di input in modo accurato e prevedere più efficiente dei risultati di interesse. Caratteristica Progettazione e la selezione anche possibile combinare per rendere più dal punto di vista gestibile le risorse. Ciò avviene tramite ottimizzazione e provare a ridurre il numero di funzionalità necessarie per calibrare o formare un modello. Matematicamente, le caratteristiche per formare il modello sono un numero minimo di variabili indipendenti che illustrano le tendenze nei dati e quindi prevedere risultati correttamente.

La progettazione e la selezione delle caratteristiche è una parte di un processo più grande, in genere è costituito da quattro passaggi:

* Raccolta di dati
* Miglioramento dei dati
* Costruzione del modello
* Post-elaborazione

Progettazione e la selezione costituiscono la fase di miglioramento dei dati di apprendimento. Tre aspetti di questo processo possono distinti ai fini:

* **Pre-elaborazione di dati**: questo processo tenta di verificare che i dati raccolti siano pulito e coerenti. Include le attività, ad esempio integrazione più set di dati, la gestione dati mancanti, dati incoerente per la gestione e la conversione di tipi di dati.
* **Portare in primo piano engineering**: questo processo tenta di creare le caratteristiche aggiuntive di pertinenti dalle caratteristiche non elaborate esistenti nei dati e per aumentare stima power all'algoritmo di apprendimento.
* **Selezione delle caratteristiche**: questo processo seleziona il subset chiave originale funzionalità dei dati per ridurre la dimensionalità del problema formazione.

Solo in questo argomento è descritta la progettazione di funzionalità e funzionalità selezione gli aspetti del processo di miglioramento dei dati. Per ulteriori informazioni sul passaggio pre-elaborazione dati, vedere [pre-elaborazione dei dati di Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## <a name="creating-features-from-your-data--feature-engineering"></a>Creazione di feature dai dati, ossia funzionalità progettazione

Dati di training è costituito da una matrice composta da esempi record oppure osservazioni archiviate in righe, ognuno dei quali dispone di un set di caratteristiche (variabili o campi memorizzati nelle colonne). Le caratteristiche specificate nella struttura della sperimentazione previsto per identificare le tendenze nei dati. Sebbene molti dei campi di dati non elaborati possono essere inclusi direttamente in set di funzionalità selezionata per formare un modello, le caratteristiche aggiuntive di ingegneria spesso necessario essere creata da sulle caratteristiche di dati non elaborati per generare un set di dati di formazione avanzata.

Quali tipi di caratteristiche devono essere creato per migliorare il set di dati quando un modello di formazione? Ingegneria caratteristiche che migliorano la formazione forniscono informazioni che consentono di distinguere meglio le tendenze nei dati. Si prevede che le nuove caratteristiche per fornire informazioni aggiuntive non chiaramente acquisite o facilmente evidente il set di funzionalità originale o esistente, ma questo processo è un'immagine. Audio e produttive decisioni richiedono alcune del settore.

Quando si avvia con apprendimento Azure, è più semplice da comprendere questo processo viene utilizzando esempi forniti nei computer risorse Studio. Di seguito sono illustrati due esempi:

* Un esempio di regressione ([stima del numero di canoni bicicletta](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) in una prova controllata nel punto in cui sono noti i valori di destinazione
* Un esempio di classificazione di data mining di testo utilizzando [l'Hashing caratteristica][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Esempio 1: Aggiunta di caratteristiche temporale per un modello di regressione ###

Per illustrare i passaggi realizzare caratteristiche per un'attività di regressione, utilizzare la prova "richiesta la previsione di biciclette" Azure Machine Learning Studio. L'obiettivo di questa prova consiste nel prevedere la domanda per bikes, vale a dire il numero di canoni bicicletta all'interno di un mese specifico giorno o un'ora. Il set di dati **bicicletta noleggio UCI set di dati** viene utilizzato come i dati di input non elaborati.

Il set di dati si basa sui dati reali della società di capitale Bikeshare che gestisce una rete noleggio bicicletta in Data Center di Washington negli Stati Uniti. Il set di dati rappresenta il numero di canoni bicicletta all'interno di un'ora specifica di un giorno da 2011 a 2012 e contiene 17379 righe e 17 colonne. Il set di funzionalità non elaborato contiene condizioni meteorologiche (temperatura, umidità, vento) e il tipo del giorno (per le festività o del giorno della settimana). Il campo prevedere è **cnt**, un numero che rappresenta noleggi bicicletta all'interno di un'ora specifica e che compreso tra 1 e 977.

Per costruire efficienti caratteristiche nei dati di training, quattro modelli di regressione sono progettati con lo stesso algoritmo, ma con quattro set di dati diversi formazione. Quattro set di dati rappresentano gli stessi dati di input non elaborati, ma con un numero crescente di caratteristiche impostare. Queste caratteristiche sono raggruppate in quattro categorie:

1. A = meteo, per le festività + weekday + festivi caratteristiche per il giorno previsto
2. B = numero di biciclette che sono state noleggio in ognuna delle ultime 12 ore
3. C = numero di biciclette che sono state noleggio in ognuna delle precedenti 12 giorni nella stessa ora
4. D = numero di biciclette che sono state noleggio in ognuna delle settimane 12 precedente della stessa ora e il giorno stesso

Oltre A set di funzionalità, che contiene già i dati non elaborati originali, i tre insiemi di caratteristiche vengono creati mediante la funzionalità di processo di produzione. Caratteristica imposta B acquisisce la domanda per la bici recente. Caratteristica imposta C acquisisce la domanda per bikes un'ora specifica. Caratteristica imposta D acquisisce richiesta per bikes particolare ora e specifico giorno della settimana. Ognuno dei quattro set di dati di formazione inclusi i set di funzionalità, A + B, A + B + C e A + B + C + D, rispettivamente.

In prova apprendimento Azure questi quattro set di dati di formazione vengono create tramite quattro diramazioni del set di dati di input pre-elaborazione. Ad eccezione di diramazione all'estrema sinistra, ognuna di queste diramazioni contiene uno [Script R eseguire] [ execute-r-script] modulo in cui un set di derivato caratteristiche (set di funzionalità B, C e D) rispettivamente costruzione e aggiunte al set di dati importato. La figura seguente illustra lo script R usato per creare il set di caratteristiche B nella seconda diramazione a sinistra.

![Creare un set di funzionalità](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

La tabella seguente riepiloga il confronto tra i risultati delle prestazioni di quattro modelli. I migliori risultati sono indicati dalle caratteristiche A + B + C. Si noti che la frequenza degli errori diminuisce quando set di funzionalità aggiuntive vengono incluse nei dati di training. In questo modo si verifica il nostro presume che il set di funzionalità B e C informazioni aggiuntive pertinenti per l'attività di regressione. Aggiunta di set di funzionalità D non sembra per fornire ulteriori riduzioni la frequenza degli errori.

![Confrontare i risultati delle prestazioni](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a>Esempio 2: Creazione di funzionalità in data mining di testo  

Progettazione di funzionalità viene applicata ampiamente nelle attività correlata alla data mining di testo, ad esempio analisi di classificazione e di valutazione in documenti. Ad esempio, quando si desidera classificare i documenti in più categorie, un tipico presupposto è che le parole o frasi incluse in una categoria di documento sono meno probabile che venga eseguita in un'altra categoria di documento. In altre parole, la frequenza della distribuzione parola o una frase in grado di caratterizzare categorie documento diverso. Nelle applicazioni di data mining di testo, la caratteristica di processo di produzione è necessarie per creare le caratteristiche relative frequenza parola o frase perché singoli elementi del contenuto di testo in genere utilizzati come i dati di input.

Per ottenere questa attività, una tecnica denominata *caratteristica hashing* viene applicata per trasformare in modo efficiente funzionalità del testo non autorizzato in indici. Invece di associazione di ogni caratteristica di testo (parole o frasi) a un indice specifico, questo metodo funziona applicando una funzione hash per le caratteristiche e usando i rispettivi valori hash come indici direttamente.

In apprendimento Azure, è una [Caratteristica Hashing] [ feature-hashing] modulo creato queste caratteristiche di word o una frase. La figura seguente mostra un esempio dell'uso di questo modulo. Set di dati di input contiene due colonne: la classificazione di libro compreso tra 1 a 5 e l'effettivo esaminare i contenuti. L'obiettivo di questa [Caratteristica Hashing] [ feature-hashing] modulo è recuperare nuove caratteristiche che mostrano la frequenza di occorrenza delle corrispondenti parole o frasi all'interno di revisione Rubrica particolare. Per utilizzare il modulo, è necessario completare la procedura seguente:

1. Selezionare la colonna che contiene il testo di input (**Col2** in questo esempio).
2. Impostare *Hashing bitsize* 8, vale a dire 2 ^ 8 = 256 caratteristiche vengono create. La parola o frase nel testo quindi l'hashing su indici di 256. Il parametro *bitsize Hashing* compreso tra 1 e 31. Se il parametro è impostato su un numero maggiore, le parole o frasi sono meno probabile che hashing nell'indice della stessa.
3. Impostare il parametro *N g* a 2. Consente di recuperare la frequenza di occorrenza di unigrams (una caratteristica di ogni parola) e bigrams (una caratteristica per ciascuna coppia di parole adiacenti) dal testo immesso. Il parametro *N g* compreso tra 0 e 10, che indica il numero massimo di parole sequenziale da includere in una caratteristica.  

![Portare in primo piano hashing modulo](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

La figura seguente mostra nuove caratteristiche di aspetto.

![Esempio di hashing caratteristica](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Caratteristiche dai dati - selezione delle caratteristiche di filtro  ##

*Selezione delle caratteristiche* è un processo che in genere viene applicato alla costruzione di set di dati di formazione per le attività di modellazione previsione, ad esempio la classificazione o regressione attività. L'obiettivo consiste nel selezionare un sottoinsieme delle caratteristiche di set di dati originale che consente di ridurre le dimensioni utilizzando un set di funzionalità minimo per rappresentare la maggior quantità di variazione dei dati. Questo sottoinsieme di funzionalità contiene le funzionalità sola da includere per formare il modello. Selezione delle caratteristiche ha due funzioni principali:

* Selezione delle caratteristiche spesso aumenta la precisione di classificazione eliminando irrilevante, ridondanti o strettamente collegata a una funzionalità.
* Selezione delle caratteristiche di diminuisce il numero di funzionalità, semplificando così il processo di formazione di modello più efficiente. Questa operazione è particolarmente importante per professionisti costosa formare, ad esempio macchine di vettore di supporto.

Sebbene selezione delle caratteristiche di ricerca per ridurre il numero delle caratteristiche di set di dati utilizzato per formare il modello, non in genere a cui fa riferimento il termine *riduzione dimensionalità.* Metodi di selezione caratteristica estraggono un sottoinsieme di funzionalità originale nei dati senza convertirli.  Metodi di riduzione dimensionalità utilizzano ingegneria caratteristiche che possono trasformare le caratteristiche originale e quindi modificarle. Analisi dei componenti dell'entità, l'analisi di correlazione canonico e scomposizione valore singolare esempi di metodi di riduzione dimensionalità.

Categoria ampiamente applicata uno dei metodi di selezione di funzionalità in un contesto controllato è selezione delle caratteristiche basate su filtro. Nel valutare la correlazione tra ogni caratteristica e l'attributo di destinazione, questi metodi applicano una misura statistica per assegnare un punteggio a ogni caratteristica. Le caratteristiche sono quindi classificate il punteggio, è possibile utilizzare per impostare la soglia per mantenere o eliminare una funzionalità specifica. Le misure statistiche usate in questi metodi includono la correlazione di Pearson, informazione reciproca e il test del chi quadrato.

Azure Machine Learning Studio include i moduli di selezione delle caratteristiche. Come mostrato nella figura seguente, questi moduli includono [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] [Fisher lineare Discriminant analisi]e[fisher-linear-discriminant-analysis].

![Esempio di selezione caratteristiche](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Ad esempio, utilizzare la [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] modulo con il testo di esempio di data mining descritto in precedenza. Si supponga di voler creare un modello di regressione dopo la creazione di un set di 256 caratteristiche tramite la [Caratteristica Hashing] [ feature-hashing] modulo e che la variabile di risposta è **Col1** e rappresenta un libro rivedere valutazione compreso tra 1 e 5. Impostare **caratteristica punteggio metodo** **Correlazione di Pearson**, **colonna di destinazione** a **Col1**e il **numero di funzionalità desiderate** su **50**. [Selezione delle caratteristiche di filtro basato] sul modulo[ filter-based-feature-selection] quindi produce un set di dati che contiene 50 caratteristiche con l'attributo di destinazione **Col1**. La figura seguente mostra il flusso di questa prova e i parametri di input.

![Esempio di selezione caratteristiche](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

La figura seguente mostra i set di dati risultanti. Nella correlazione di Pearson tra se stesso e per l'attributo di destinazione **Col1**è totalizza basate su ogni caratteristica. Le caratteristiche con i punteggi migliori vengono mantenute.

![Set di dati di selezione feature basata su filtro](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

La figura seguente mostra i punteggi corrispondenti delle funzionalità selezionate.

![Punteggi feature selezionata](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Applicando questa [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] modulo, 50 di 256 caratteristiche selezionate sono in quanto hanno più caratteristiche correlate con la destinazione della variabile **Col1** in base al punteggio metodo **Correlazione di Pearson**.

## <a name="conclusion"></a>Conclusioni
Progettazione di caratteristiche e funzionalità di selezione sono necessari due passaggi in genere eseguiti per preparare i dati di formazione durante la creazione di un modello di apprendimento. In genere, Progettazione funzionalità viene applicato prima di tutto per generare caratteristiche aggiuntive e quindi il passaggio di selezione funzionalità viene eseguito per eliminare le funzionalità irrilevante, ridondanti o altamente correlate.

Non è sempre necessariamente per l'esecuzione di selezione delle caratteristiche di progettazione o funzionalità. Se è necessario dipende da dati o la raccolta, l'algoritmo che scelto e l'obiettivo di prova.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
