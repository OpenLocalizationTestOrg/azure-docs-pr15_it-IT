<properties
    pageTitle="Portare in primo piano progettazione del processo di Analitica Cortana | Microsoft Azure" 
    description="Spiega fini della progettazione di funzionalità e vengono forniti esempi del proprio ruolo del processo di miglioramento dei dati di apprendimento."
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
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Caratteristica di progettazione del processo di Analitica Cortana 

In questo argomento illustra fini della progettazione di funzionalità e vengono forniti esempi del proprio ruolo del processo di miglioramento dei dati di apprendimento. Gli esempi utilizzati per illustrare questo processo vengono disegnati da Azure Machine Learning Studio. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare caratteristiche per i dati in ambienti diversi. Questa attività è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Portare in primo piano ingegneristica tentativi per aumentare la potenza di previsione di formazione algoritmi mediante la creazione di caratteristiche da dati non elaborati semplificare il processo di apprendimento. La progettazione e la selezione delle caratteristiche da una parte di TDSP delineata la [Qual è la procedura di scienze di dati del Team?](data-science-process-overview.md) Caratteristica Progettazione e la selezione sono parti del passaggio **caratteristiche di sviluppo** del TDSP. 

* **Progettazione di funzionalità**: questo processo tenta di creare le caratteristiche aggiuntive di pertinenti dalle caratteristiche non elaborate esistenti nei dati e per aumentare la potenza stima dell'algoritmo di apprendimento.

* **selezione delle caratteristiche**: questo processo seleziona il subset principali funzionalità dei dati originali durante un tentativo di ridurre la dimensionalità del problema formazione.

In genere **Progettazione funzionalità** viene applicato prima di tutto per generare caratteristiche aggiuntive e quindi il passaggio di **selezione delle caratteristiche** viene eseguito per eliminare le funzionalità irrilevante, ridondanti o altamente correlate.

I dati di formazione usati in apprendimento spesso possono essere migliorati per l'estrazione delle funzionalità raccolti i dati non elaborati. Esempio di una caratteristica ingegneria nel contesto di imparare a classificare le immagini di caratteri scritta a mano è la creazione di un po' densità mappa creata dai dati di distribuzione bit elaborati. Questa mappa consente di individuare i bordi dei caratteri in modo più efficiente rispetto all'uso direttamente la distribuzione non elaborata.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Creazione di feature dai dati - portare in primo piano progettazione

Dati di training è costituito da una matrice composta da esempi record oppure osservazioni archiviate in righe, ognuno dei quali dispone di un set di caratteristiche (variabili o campi memorizzati nelle colonne). Le caratteristiche specificate nella struttura della sperimentazione previsto per identificare le tendenze nei dati. Sebbene molti dei campi di dati non elaborati possono essere inclusi direttamente in set di funzionalità selezionata per formare un modello, è spesso le maiuscole/minuscole che è necessario essere creata da sulle caratteristiche di dati non elaborati per generare un set di dati di training avanzata caratteristiche aggiuntive di (ingegneria).

Quali tipi di caratteristiche devono essere creato per migliorare il set di dati quando un modello di formazione? Ingegneria caratteristiche che migliorano la formazione forniscono informazioni che consentono di distinguere meglio le tendenze nei dati. Si prevede che le nuove caratteristiche per fornire informazioni aggiuntive che non chiaramente acquisita o facilmente visibili nel set di funzionalità originale o esistente. Ma questo processo è un'immagine. Audio e produttive decisioni richiedono alcune del settore.

Quando si avvia con apprendimento Azure, è più semplice da comprendere questo processo viene utilizzando esempi forniti di Studio. Di seguito sono illustrati due esempi:

* Un esempio di regressione [stima del numero di canoni bicicletta](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in una prova controllata nel punto in cui sono noti i valori di destinazione
* Un esempio di classificazione di data mining di testo utilizzando [l'Hashing caratteristica](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Esempio 1: Aggiunta di caratteristiche temporale per modello di regressione ###

Utilizzare la prova "richiesta la previsione di biciclette" Azure Machine Learning Studio per dimostrare come decodificare caratteristiche per un'attività di regressione. L'obiettivo di questa prova consiste nel prevedere la domanda per bikes, vale a dire il numero di canoni bicicletta all'interno di una mese/giorno/ora. Il set di dati "bicicletta noleggio set di dati UCI" viene usato come i dati di input non elaborati. Questo set di dati si basa sui dati reali della società di capitale Bikeshare che gestisce una rete noleggio bicicletta in Data Center di Washington negli Stati Uniti. Il set di dati rappresenta il numero di canoni bicicletta all'interno di un'ora specifica di un giorno nel 2011 e anno 2012 e contiene 17379 righe e 17 colonne. Il set di funzionalità non elaborato contiene condizioni meteorologiche (temperatura/umidità/vento) e il tipo del giorno (per le festività/giorno). Il campo prevedere è "cnt", il numero che rappresenta noleggi bicicletta all'interno di un'ora specifica e che intervalli compreso tra 1 e 977.

Con l'obiettivo di costruzione efficienti caratteristiche nei dati di training, quattro regressione modelli sono progettati con lo stesso algoritmo ma con quattro set di dati diversi formazione. I quattro set di dati rappresentano gli stessi dati di input non elaborati, ma con un numero crescente di caratteristiche impostare. Queste caratteristiche sono raggruppate in quattro categorie:

1. A = meteo, per le festività + weekday + festivi caratteristiche per il giorno previsto
2. B = numero di biciclette che sono state noleggio in ognuna delle ultime 12 ore
3. C = numero di biciclette che sono state noleggio in ognuna delle precedenti 12 giorni nella stessa ora
4. D = numero di biciclette che sono state noleggio in ognuna delle settimane 12 precedente della stessa ora e il giorno stesso

Oltre A set di funzionalità, che contiene già i dati non elaborati originali, i tre insiemi di caratteristiche vengono creati mediante la funzionalità di processo di produzione. Caratteristica imposta B acquisisce molto recente richiesta per la bici. Caratteristica imposta C acquisisce la domanda per bikes un'ora specifica. Caratteristica imposta D acquisisce richiesta per bikes particolare ora e specifico giorno della settimana. Formazione quattro su set di dati ogni include funzionalità set, A + B, A + B + C e A + B + C + D.

In prova apprendimento Azure questi quattro set di dati di formazione vengono create tramite quattro diramazioni da pre-elaborata set di dati di input. Ad eccezione di sinistra ramo la maggior parte delle ognuna di queste diramazioni contiene un modulo [Esegui Script di R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , in cui un set di derivato funzioni (funzione impostate B, C e D) sono rispettivamente impostata e aggiunta al set di dati importati. La figura seguente illustra lo script R usato per creare il set di caratteristiche B nella seconda diramazione a sinistra.

![creazione di caratteristiche](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

Confronto tra i risultati delle prestazioni di quattro modelli sono riepilogati nella tabella seguente. I migliori risultati sono indicati dalle caratteristiche A + B + C. Nota che la frequenza degli errori diminuisce quando set di caratteristiche aggiuntive vengono incluse nei dati di training. Consente di verificare il nostro presume che il set di funzionalità B, C fornisce altre informazioni rilevanti per l'attività di regressione. Ma aggiungendo la caratteristica D sembra che non forniscono la riduzione aggiuntiva la frequenza degli errori.

![confronto dei risultati](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Esempio 2: Creazione di caratteristiche di Data Mining di testo  

Progettazione di funzionalità viene applicata ampiamente nelle attività correlata alla data mining di testo, ad esempio analisi di classificazione e di valutazione in documenti. Ad esempio, quando si desidera classificare i documenti in più categorie, un tipico presupposto è che word/frasi incluse in una categoria di documento sono meno probabile che venga eseguita in un'altra categoria di documento. In altre parole, la frequenza della distribuzione di parole o frasi in grado di caratterizzare categorie documento diverso. Nelle applicazioni di data mining di testo, poiché singoli elementi del contenuto di testo in genere utilizzati come i dati di input, la caratteristica di processo di produzione è necessario per creare le caratteristiche relative frequenza parola o frase.

Per ottenere questa attività, una tecnica denominata **caratteristica hashing** viene applicata per trasformare in modo efficiente funzionalità del testo non autorizzato in indici. Invece di associazione di ogni caratteristica di testo (parole o frasi) a un indice specifico, questo metodo funziona l'applicazione di una funzione hash per le caratteristiche e usando i rispettivi valori hash come indici direttamente.

In apprendimento Azure, è un modulo [L'Hashing caratteristica](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) che consente di creare questi parola o frase caratteristiche in modo appropriato. Nella figura seguente è illustrato un esempio dell'uso di questo modulo. Il set di dati di input contiene due colonne: la classificazione di libro compreso tra 1 e 5 e il contenuto effettivo revisione. L'obiettivo di questo modulo [Caratteristica Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) è per recuperare un insieme di nuove caratteristiche che mostra la frequenza di occorrenza del testo corrispondente / frasi in particolare libro esaminare. Per utilizzare il modulo, è necessario completare la procedura seguente:

* Prima di tutto, selezionare la colonna che contiene il testo di input ("Col2" in questo esempio).
* In secondo luogo, impostare "Hashing bitsize" 8, vale a dire 2 ^ 8 = 256 caratteristiche verranno create. Word/fase in tutto il testo verrà hashing su indici di 256. Il parametro "Hashing bitsize" compreso tra 1 e 31. Le parole / frasi hanno meno probabilità di essere l'hashing nell'indice della stessa se impostandolo come un valore maggiore.
* Infine, impostare il parametro "G N" per 2. Si ottiene la frequenza di occorrenza di unigrams (una caratteristica di ogni parola) e bigrams (una caratteristica per ciascuna coppia di parole adiacenti) dal testo immesso. Il parametro "G N" compreso tra 0 e 10, che indica il numero massimo di parole sequenziale da includere in una caratteristica.  

![Modulo "Portare in primo piano Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figura seguente mostra cosa il queste funzionalità nuovo aspetto come.

![Esempio di "Portare in primo piano Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Conclusioni

Caratteristiche ingegneria e selezionati aumentano l'efficienza del processo di formazione che tenta di estrarre le informazioni chiave contenute nei dati. Facilitano inoltre la potenza di questi modelli per classificare i dati di input in modo accurato e prevedere più efficiente dei risultati di interesse. Caratteristica Progettazione e la selezione anche possibile combinare per rendere più dal punto di vista gestibile le risorse. Ciò avviene tramite ottimizzazione e provare a ridurre il numero di funzionalità necessarie per calibrare o formare un modello. Matematicamente, le caratteristiche per formare il modello sono un numero minimo di variabili indipendenti che illustrano le tendenze nei dati e quindi prevedere risultati correttamente.

Si noti che non è sempre necessariamente per l'esecuzione di selezione delle caratteristiche di progettazione o funzionalità. Se è necessaria o meno dipende dai dati si dispone o la raccolta, l'algoritmo che è selezionare e l'obiettivo di prova.
 
