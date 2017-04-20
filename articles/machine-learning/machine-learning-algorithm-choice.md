<properties
    pageTitle="Scegliere gli algoritmi di apprendimento automatica | Microsoft Azure"
    description="Come scegliere gli algoritmi di apprendimento automatica Azure sorvegliati e senza supervisione informazioni di in sperimentazione clustering, classificazione o di regressione."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Scegliere gli algoritmi di apprendimento Microsoft Azure

La risposta alla domanda "Quali computer apprendimento algoritmo utilizzare?" è sempre "Dipende." Dipende la dimensione, la qualità e natura dei dati. Dipende dalle quali si desidera procedere con la risposta. Dipende dal modo matematico dell'algoritmo è stato convertito nelle istruzioni per il computer in uso. E dipende in quanto tempo è necessario. Ricercatori dati più esperti Impossibile stabilire algoritmo che verrà eseguite ottimale prima di tentare di essi.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>L'algoritmo di programmazione automatica schede di riferimento rapido

**Microsoft Azure automatica di apprendimento algoritmo scheda Foglio** consente di scegliere il computer destro per le soluzioni analitica predittiva dalla libreria Microsoft Azure automatica apprendimento algoritmi di algoritmo di apprendimento.
In questo articolo illustra come utilizzare questo strumento.

> [AZURE.NOTE] Per scaricare il foglio di riferimento rapido sul e Seguiremo in questo articolo, accedere alla [macchina apprendimento foglio di riferimento rapido sul algoritmo per Microsoft Azure automatica di apprendimento Studio](machine-learning-algorithm-cheat-sheet.md).

In questo foglio di riferimento rapido sul dispone di un gruppo di destinatari specifico presente: scienziato un dati che inizia con livello di età universitaria l'apprendimento, tenta di scegliere un algoritmo per iniziare con Azure automatica di apprendimento Studio. Ciò significa che tale scelta risulta alcune generalizzazioni e oversimplifications, ma è riportati gli in direzione sicura. Significa inoltre che esistono molti algoritmi non elencati di seguito. Man mano che aumenta l'apprendimento Azure in modo da includere un insieme più completo dei metodi disponibili, essi verranno aggiunte.

Questi suggerimenti sono compilato commenti e suggerimenti da una quantità elevata di dati scienziati ed esperti di didattica automatica. È non accetta in tutto il contenuto, ma si è tentato di armonizzare il nostro opinioni in accordarsi approssimativa. La maggior parte delle istruzioni di disaccordo iniziano con "Dipende..."

### <a name="how-to-use-the-cheat-sheet"></a>Come utilizzare il foglio di riferimento rapido sul

Leggere le etichette in percorso e l'algoritmo del grafico come "per * &lt;etichetta percorso&gt; * utilizzare * &lt;algoritmo&gt;*." Ad esempio, "per *la velocità* utilizzare *due regressione logistica di classe*." In alcuni casi più di una succursale verrà applicate.
Talvolta non è adatta perfettamente. Si sta deve essere di generale suggerimenti, in modo che non c'è viene esatta.
Diversi scienziati di dati che parlato con detto che l'unico modo sicuro per trovare l'algoritmo migliore consiste nel provare a tutti gli utenti.

Di seguito è riportato un esempio dalla [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com/) di un esperimento tenta algoritmi diversi con gli stessi dati e vengono confrontati i risultati: [confronto multi-classe classificatori: lettera riconoscimento](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Per scaricare e stampare un diagramma che offre una panoramica delle funzionalità di computer Learning Studio, vedere [il diagramma panoramica delle funzionalità di Azure automatica Learning Studio](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>E le versioni di apprendimento

### <a name="supervised"></a>Supervisione

Gli algoritmi di apprendimento sorvegliati effettuare stime in base a un insieme di esempi. Ad esempio, cronologiche quotazioni utilizzabile per tentativi pericolo prezzi futuri. Ogni esempio viene utilizzato per la formazione è contrassegnato con il valore di interesse, in questo caso il prezzo. Un algoritmo di apprendimento sorvegliati cerca i modelli di etichette tali valori. È possibile utilizzare le informazioni potrebbero essere rilevanti, ovvero il giorno della settimana, il periodo, dati finanziari dell'azienda, il tipo di settore, la presenza di eventi geopolicitical eccesso, ovvero e ogni algoritmo cerca i diversi tipi di formati. Dopo l'algoritmo ha trovato il formato ottimale è possibile, tale formato viene utilizzato per previsioni per i dati di test priva di etichetta, ovvero i prezzi domani.

Questo è un tipo più comuni e utile di apprendimento. Con un'eccezione di tutti i moduli in Azure automatica apprendimento sono controllati gli algoritmi di formazione. Esistono diversi tipi specifici di apprendimento sorvegliato rappresentati all'interno di apprendimento automatica Azure: rilevamento della classificazione, regressione e anomalia.

* **Classificazione**. Quando i dati vengono utilizzati per prevedere una categoria, apprendimento sorvegliato acronimo di classificazione. Ciò avviene durante l'assegnazione di un'immagine come immagine di un "gatto" o 'cane'. Quando sono presenti solo due scelte, si tratta **della classe due** o **classificazione binomiale**. Quando sono presenti più categorie, come quando la previsione confermato in torneo basket NCAA, il problema è noto come **multi-classe classificazione**.

* **Regressione**. Quando viene da prevedere un valore come per quotazioni, apprendimento sorvegliato viene chiamato regressione.

* **Rilevamento di anomalie in**. In alcuni casi l'obiettivo è di identificare i punti di dati che sono semplicemente non comune. Rilevamento truffe di false, ad esempio, eventuali modelli di spesa inusuale carta di credito sono sospetto. Eventuali variazioni sono numerosi e gli esempi di formazione così pochi, che non è realizzabile per scoprire quali attività falsi simile. L'approccio di rilevamento delle anomalie in seguito è semplicemente apprendimento le attività normali simile (utilizzando le transazioni non falsi una cronologia) e identificare tutti gli elementi presenta differenze significative.

### <a name="unsupervised"></a>Senza supervisione

In senza supervisione apprendimento, coordinate non dispongono Nessuna etichetta associata. In realtà, l'obiettivo di un algoritmo di apprendimento senza supervisione è per organizzare i dati in un modo o per descrivere la struttura. Ciò può significare il raggruppamento in cluster o durante la ricerca di modi diversi di osservando dati complessa in modo che venga visualizzato più semplice o più organizzata.

### <a name="reinforcement-learning"></a>Apprendimento di rafforzamento

Nel rafforzamento di apprendimento, l'algoritmo Ottiene la scelta dell'azione in risposta a ogni singola coordinata. Inoltre, l'algoritmo di apprendimento riceve un segnale di ricompensa un'ora breve 24h in seguito, che indica come buona la decisione è stata.
Basata su queste, l'algoritmo consente di modificare la propria strategia per poter ottenere ricompensa più alto. Non sono attualmente Nessun rafforzamento moduli algoritmo Azure automatica informazioni di formazione. Apprendimento di rafforzamento è comune in robotics, dove il set di letture sensore in un punto nel tempo è un punto di dati e l'algoritmo deve scegliere l'azione successiva del robot. È inoltre che un naturale adatta per Internet di aspetti applicazioni.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerazioni per la scelta di un algoritmo

### <a name="accuracy"></a>Precisione

Ottenere la risposta più precisa possibile non è sempre necessario.
Talvolta può essere adeguata, in base alle quali si desidera utilizzare per ottenere un'approssimazione. In tal caso, potrebbe essere in grado di ridurre il tempo di elaborazione drasticamente dall'utilizzo di ulteriori metodi approssimativi. Un altro vantaggio di ulteriori metodi approssimativi è la tendenza a evitare [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tempo di formazione

Il numero di minuti oppure ore necessarie per formare un modello può variare notevolmente da algoritmi. Formazione ora è spesso vincolata a precisione, ovvero uno accompagna in genere l'altra. Inoltre, alcuni algoritmi sono molto riservate al numero di coordinate rispetto ad altri.
Quando i tempi di guidare la scelta dell'algoritmo, soprattutto se il set di dati è di grandi dimensioni.

### <a name="linearity"></a>Linearità

Effettuare una quantità elevata di algoritmi di apprendimento automatica utilizzare di linearità. Gli algoritmi di classificazione lineare si presuppongono che classi possono essere separate da una linea retta (o il relativo numero superiore di dimensioni analogico). Queste includono regressione logistica e supportare macchine vettoriale (così com'è implementata in Azure apprendimento).
Gli algoritmi di regressione lineare si presuppongono che tendenze dei dati eseguire una linea retta. Questi presupposti non male per alcuni problemi, ma in altri offrono accuratezza verso il basso.

![Classe non lineare bounday][1]

***Limite di classe non lineare*** *-l'utilizzo di un algoritmo di classificazione lineare genera accuratezza bassa*

![Dati con una tendenza lineare][2]

***Dati con una tendenza lineare*** *-Se si esegue un metodo di regressione lineare potrebbe generare quantità errori più grandi del necessario*

Nonostante i pericoli algoritmi lineari sono molto diffusi come prima linea di attacco. Tendono a essere tramite algoritmo semplice e veloce per formare.

### <a name="number-of-parameters"></a>Numero di parametri

I parametri sono comandi che un scienziato dati ottiene per attivare quando si configura un algoritmo. Sono numeri che influiscono sul comportamento dell'algoritmo, ad esempio la tolleranza di errore o il numero di iterazioni o opzioni tra le varianti di comportamento l'algoritmo. Il tempo di formazione e accuratezza dell'algoritmo talvolta può essere molto riservate per ottenere le impostazioni di destra. Gli algoritmi con i parametri di un numero elevato richiedono in genere, la maggior parte dei tentativi ed errori per trovare una combinazione ottimale.

In alternativa, non esiste un blocco di moduli [parametro completa](machine-learning-algorithm-parameters-optimize.md) in Azure l'apprendimento che tenta automaticamente tutte le combinazioni di parametri a qualsiasi livello di dettaglio scelto. Mentre si tratta di un modo straordinario per verificare di che aver occupati lo spazio parametro, il tempo necessario per formare un modello esponenziale con il numero di parametri.

Il vantaggio offerto è che con numerosi parametri in genere indica che un algoritmo disponga di una maggiore flessibilità. È spesso possibile ottenere accuratezza ottima. Disponibili che è possibile trovare la combinazione di impostazioni di parametro.

### <a name="number-of-features"></a>Numero di funzionalità

Per alcuni tipi di dati, il numero di funzionalità può essere molto grande rispetto al numero di coordinate. Si tratta spesso nel caso di genetics o dati di tipo testo. Numero elevato di funzionalità può costringe alcuni algoritmi di apprendimento, esecuzione formazione tempi lunghi unfeasibly. Supporto vettoriale macchine sono particolarmente adatte a questo case (vedere di seguito).

### <a name="special-cases"></a>Casi speciali

Alcuni algoritmi learning fare particolare ipotesi relative alla struttura dei dati o i risultati desiderati. Se è possibile trovare che si adatta alle proprie esigenze, può fornire è risultati più utili, stime più accurate o tempi di formazione.

|**Algoritmo di**|**Precisione**|**Tempo di formazione**|**Linearità**|**Parametri di**|**Note**|
|---|:---:|:---:|:---:|:---:|---|
|**Classificazione di livello 2**| | | | | |
|[regressione logistica](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[insieme di strutture decisione](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[jungle decisione](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Footprint di memoria insufficiente|
|[albero decisionale innalzamento](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Footprint di memoria di grandi dimensioni|
|[rete neurale](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[È possibile personalizzare ulteriormente l'](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[perceptron calcolata la Media](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[supporto vettoriale automatica](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|Salutari per il set di caratteristiche di grandi dimensioni|
|[supporto localmente approfondita vettoriale automatica](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|Salutari per il set di caratteristiche di grandi dimensioni|
|[Macchina punto degli Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Multi-classe classificazione**| | | | | |
|[regressione logistica](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[insieme di strutture decisione](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[jungle decisione](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Footprint di memoria insufficiente|
|[rete neurale](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[È possibile personalizzare ulteriormente l'](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[1-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Vedere le proprietà del metodo della classe due selezionata|
|**Regressione**| | | | | |
|[lineare](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayes lineare](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[insieme di strutture decisione](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[albero decisionale innalzamento](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Footprint di memoria di grandi dimensioni|
|[tabelle della legge del foresta Fast](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Distribuzioni anziché stime punto|
|[rete neurale](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[È possibile personalizzare ulteriormente l'](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Tecnicamente registro lineare. Per la previsione conteggi|
|[ordinale](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|Per la previsione ordinamento classificazione|
|**Rilevamento di anomalie in**| | | | | |
|[supporto vettoriale automatica](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Particolarmente utile per set di caratteristiche di grandi dimensioni|
|[Rilevamento di anomalie in base PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-medie](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|Un algoritmo di clustering|


**Proprietà algoritmo:**

**●** - Mostra accuratezza, tempi di training fast e l'utilizzo di linearità

**○** - Mostra una buona accuratezza e tempi di training moderato

## <a name="algorithm-notes"></a>Note algoritmo

### <a name="linear-regression"></a>Regressione lineare

Come accennato in precedenza, [regressione lineare](https://msdn.microsoft.com/library/azure/dn905978.aspx) si inserisce una riga (o piano o hyperplane) al set di dati. Si tratta di un componente di base, semplice e veloce, ma potrebbe essere eccessivamente semplificato per alcuni problemi.
Controllo di seguito per un' [esercitazione di regressione lineare](machine-learning-linear-regression-in-azure.md).

![Dati con una tendenza lineare][3]

***Dati con una tendenza lineare***

### <a name="logistic-regression"></a>Regressione logistica

Anche se molte include "regressione" nel nome, regressione logistica è effettivamente un potente strumento per la classificazione delle [due classi](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) . È semplice e veloce. Il fatto che viene utilizzato il metodo di un '-curva con data shaping anziché una linea retta consente una scelta naturale per la divisione di dati in gruppi. Limiti di classe lineare consente di regressione logistica, pertanto quando si utilizza questa funzionalità, verificare che è un'approssimazione lineare che possibile live con.

![Regressione logistica ai dati di livello 2 con una sola funzionalità][4]

***Un test di regressione logistica ai dati di livello 2 con una sola funzionalità*** *-il limite di classe è il punto in cui la curva logistica è più vicino entrambe le classi*

### <a name="trees-forests-and-jungles"></a>Alberi, gli insiemi di strutture e nelle foreste

Decisione foreste ([regressione](https://msdn.microsoft.com/library/azure/dn905862.aspx), [due classi](https://msdn.microsoft.com/library/azure/dn906008.aspx)e [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), nelle foreste decisione ([livello due](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e decisionali innalzamento ([regressione](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [due della classe](https://msdn.microsoft.com/library/azure/dn906025.aspx)) sono tutti basati su decisionali, una macchina fondamentali concetto di formazione. Esistono molte varianti di decisionali, ma eseguono tutti la stessa operazione, ovvero suddividere lo spazio di funzionalità in aree con principalmente la stessa etichetta. Può trattarsi di aree di categoria coerenza o del valore costante, a seconda se si sta eseguendo la classificazione o regressione.

![Albero decisionale suddivide uno spazio di funzionalità][5]

***Una struttura ad albero decisionale uno spazio caratteristica viene suddiviso in aree di circa uniformi valori***

Poiché uno spazio funzionalità può essere suddivisi in aree arbitrariamente piccole, è facile immaginare dividendo precisione sufficiente disporre di un punto di dati per ogni area, ovvero un esempio di sovradattamento extreme. Per evitare questo problema, un insieme di strutture di grandi dimensioni vengono create con particolare attenzione matematico effettuate che le strutture ad albero non correlate. La media di "decisione insieme di strutture" è una struttura ad albero che consente di evitare sovradattamento. Gli insiemi di strutture decisione può utilizzare una quantità elevata di memoria. Nelle foreste decisione sono un valore variant che utilizza le quantità di memoria inferiore velocizza la volta formazione leggermente superiore.

Innalzamento decisionali evitare sovradattamento limitando il numero di volte che è possibile suddividere e come un numero ridotto di coordinate sono consentiti in ogni area. L'algoritmo crea una sequenza di strutture, ognuno dei quali viene a conoscenza di compensare l'errore è uscito dalla struttura ad albero prima. Il risultato è un apprendimento molto preciso tende a utilizzare una quantità elevata di memoria. Per una descrizione completa tecnica, consultare [paper originale di test di Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Regressione quantile Fast foresta](https://msdn.microsoft.com/library/azure/dn913093.aspx) è una variazione del decisionali per il caso speciale in cui si desidera conoscere non solo il valore dei dati all'interno di un'area, ma anche la distribuzione sotto forma di quantiles (Luna) tipico.

### <a name="neural-networks-and-perceptrons"></a>Perceptrons e reti neurali

Reti neurali sono disponibili cervello apprendimento algoritmi relativi a problemi [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [due classi](https://msdn.microsoft.com/library/azure/dn905947.aspx)e [regressione](https://msdn.microsoft.com/library/azure/dn905924.aspx) . E sono disponibili per una serie infinita, ma le reti neurali all'interno di apprendimento Azure sono tutti del modulo di grafici aciclici diretti. Che indica che le funzionalità di input vengono passate inoltra (mai all'indietro) attraverso una sequenza di livelli prima viene trasformato in output. In ogni livello, gli input ponderati in varie combinazioni, sommati e passati al livello successivo. Questa combinazione di semplici calcoli determina la possibilità di per saperne di classe sofisticata tendenze dei limiti e i dati, apparentemente direttamente. Molti layered reti di questo tipo, eseguire il "apprendimento completa" che combustibili tanto tech report e più solo.

In questo ad alte prestazioni non disponibili gratuitamente, tuttavia. Reti neurali possono richiedere molto tempo per formare, in particolare per grandi quantità di dati con una quantità elevata di caratteristiche. Inoltre presentano più parametri rispetto alla maggior parte degli algoritmi, il che significa che sweep parametro espande notevolmente l'ora di formazione.
E per quelle overachievers che si desidera [specificare la propria struttura di rete](http://go.microsoft.com/fwlink/?LinkId=402867), i valori possibili sono inexhaustible.

<a name="boundaries-learned-by-neural-networks6"></a>![Limiti maturati dal reti neurali][6]
---------------------------

***I limiti maturati per reti neurali possono essere complessa e irregolare***

La [classe due calcolata perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) è risposta reti neurali per esponenziale dei tempi di formazione. Utilizza una struttura di rete che offre dei limiti di classe lineare. È quasi base per gli standard attuali, ma dispone di una lunga storia di lavorare in modo affidabile ed è devono essere sufficientemente piccolo per scoprire rapidamente.

### <a name="svms"></a>SVMs

Supporto vettoriale macchine (SVMs) trovare il limite che separa le classi da come ampio margine possibile. Se le due classi non possono essere chiaramente separate, gli algoritmi di trovare il migliore limite che è possibile. Come è scritto in Azure l'apprendimento, [SVM due classe](https://msdn.microsoft.com/library/azure/dn905835.aspx) ciò con solo una linea retta. (Nella parlare SVM, viene utilizzato un kernel lineare.) Semplifica l'approssimazione lineare, è possibile eseguire piuttosto rapidamente. Dove indicati è con funzionalità marcato dati, come testo o genomiche. In questi casi SVMs siano in grado di separare le classi più rapidamente e con minore sovradattamento della maggior parte degli altri algoritmi, oltre che richiedono solo una quantità di memoria necessario.

![Supporto vettoriale classe all'esterno del computer][7]

***Una tipica supporto vettoriale classe all'esterno del computer ingrandisce il margine separando due classi***

Un altro prodotto di Microsoft Research, [SVM localmente approfondita due classe](https://msdn.microsoft.com/library/azure/dn913070.aspx) è un valore variant non lineare di SVM che mantiene la maggior parte dell'efficienza di memoria e velocità della versione lineare. È la soluzione ideale per cui l'approccio lineare non fornisce risposte abbastanza accurate dei casi. Gli sviluppatori è stato eliminato fast suddividendo il problema in una serie di piccole dimensioni problemi SVM lineare. Leggere la [descrizione completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) per informazioni dettagliate su come vengono estratte off questo invece.

Utilizzando un'estensione intelligente di SVMs non lineare, [una classe SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) Disegna un limite che indicano che l'intero set di dati. È utile per il rilevamento anomalia. Le nuove coordinate che non rientrano estrema tale limite sono sufficientemente frequenti da degne di nota.

### <a name="bayesian-methods"></a>Metodi Bayes

I metodi Bayes presentano una qualità elevata auspicabile: evitano sovradattamento. Questo scopo sono presupposti in anticipo sulla distribuzione probabilmente della risposta. Un altro prodotto secondario di questo approccio è che dispongono di parametri richiesti molti. Apprendimento Azure è entrambi gli algoritmi Bayes per la classificazione ([macchina punto degli Bayes due classe](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e regressione ([Bayes regressione lineare](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Si noti che queste si presuppone che è possibile suddividere i dati o adattamento con una linea retta.

Presenti in una nota cronologica macchine punto degli Bayes sono state sviluppate presso Microsoft Research. Presentano alcune operazioni teorica estremamente meravigliose su cui si basano. Student interessato viene indirizzata a un [articolo originale in JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e un [blog dettagliati da Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmi specializzati

Se si dispone di un obiettivo specifico può essere soluzione esiste già. All'interno dell'insieme apprendimento Azure sono algoritmi specializzati in classificazione stima ([ordinale regressione](https://msdn.microsoft.com/library/azure/dn906029.aspx)), count stima ([Poisson regressione](https://msdn.microsoft.com/library/azure/dn905988.aspx)) e il rilevamento anomalia (una basata [sull'analisi dei componenti principali](https://msdn.microsoft.com/library/azure/dn913102.aspx) e una basata su [supporta automatica vettoriale](https://msdn.microsoft.com/library/azure/dn913103.aspx)s).
E non esiste un'unico algoritmo di clustering anche ([K-medie](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Rilevamento di anomalie in base PCA][8]

***Rilevamento di anomalie in base PCA*** *-la maggior parte dei dati può essere suddiviso in una distribuzione stereotypical; punti la deviazione drasticamente dalle che distribuzione sono sospette*

![Set di dati raggruppati mediante mezzi K][9]

***Un set di dati sono raggruppato in 5 cluster utilizzando mezzi K***

È inoltre disponibile un insieme sci [classificatore multiclass uno v completa](https://msdn.microsoft.com/library/azure/dn905887.aspx), che interrompe il problema di classificazione classe N un problema di classificazione dei due classe N-1. Accuratezza, ora addestramento e le proprietà linearità dipendono dal classificatore due classe utilizzati.

![Classe due classificatori combinati per formare un classificatore tre classe][10]

***Una coppia di due classe classificatori consente di ottenere un classificatore tre classe***

Apprendimento automatica Azure include anche l'accesso a un framework di apprendimento computer potente sotto il titolo del [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW definisce la categorizzazione di seguito, in quanto possono venire a problemi di classificazione e di regressione e anche informazioni da parzialmente priva di etichetta dati. È possibile configurare per l'utilizzo di uno qualsiasi di un numero di formazione algoritmi, funzioni perdita e algoritmi di ottimizzazione. È stato progettato completamente backup per essere efficienti, parallelo e velocizzare. Gestisce il set di funzionalità estremamente grande con un minimo sforzo evidente.
Avvio e condotto da John Langford del Microsoft Research proprio VW è una Formula una voce nel campo titolo car algoritmi di. Non tutti i problemi rientra VW, ma in questo caso, spetta all'utente, potrebbe essere la pena a scalare la curva di apprendimento nella relativa interfaccia. È inoltre disponibile come [codice sorgente open autonomo](https://github.com/JohnLangford/vowpal_wabbit) in diverse lingue.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
