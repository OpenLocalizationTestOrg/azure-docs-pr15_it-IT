<properties
    pageTitle="Analisi varianza del cliente con apprendimento | Microsoft Azure"
    description="Case study di sviluppo di un modello integrato per l'analisi e il punteggio varianza del cliente"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analisi varianza del cliente tramite apprendimento Azure

##<a name="overview"></a>Panoramica
In questo argomento presenta un'implementazione di riferimento di un progetto di analisi varianza del cliente integrato con Azure Machine Learning Studio. Vengono illustrati i modelli generici associati per modo globale per risolvere il problema di varianza del cliente industriali. È inoltre, valutare l'accuratezza dei modelli che vengono compilati utilizzando l'apprendimento ed è valutare le istruzioni per l'ulteriore sviluppo.  

### <a name="acknowledgements"></a>Riconoscimenti

Questa prova è stato sviluppato e testato Serge Berger, capitale dati del settore scientifico presso Microsoft e Roger Barga, in precedenza Product Manager per Microsoft Azure apprendimento. Il team di Azure documentazione esprime riconosce le competenze e ringraziamenti per la condivisione di questo documento.

>[AZURE.NOTE] Dati utilizzati per questa prova non sono disponibili al pubblico. Per informazioni su come creare un modello di apprendimento computer per l'analisi varianza, vedere: [telecomunicazioni varianza del modello](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) nella [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>Il problema di varianza del cliente
Imprese nel mercato consumer e in tutti sectors enterprise sono necessario gestire la varianza. In alcuni casi varianza è eccessiva e influenza decisioni relative ai criteri. La soluzione consiste nel prevedere churners tendenza ad alta e risolvere le proprie esigenze tramite un servizio concierge, campagne di marketing o applicando dispense speciale. Questi approcci possono variare da standard di settore e anche da un cluster di consumer specifica all'interno di un settore (ad esempio telecomunicazioni).

Il fattore comune è che imprese necessarie per ridurre al minimo i clienti di conservazione speciale. In questo modo, una metodologia naturale, è possibile punteggio tutti i clienti con la probabilità di varianza e le prime N quelli di indirizzi. Clienti principali potrebbero essere quelli più redditizi; in scenari più sofisticati, ad esempio, una funzione di profitto è impiegata durante la selezione di candidati per dispensa speciale. Tuttavia, queste considerazioni sono solo una parte della strategia globale per la gestione di varianza. Aziende dovranno tener rischio account e i rischi associati tolleranza, il livello e il costo di intervento e segmentazione plausibili cliente.  

##<a name="industry-outlook-and-approaches"></a>Approcci e outlook di settore
Gestione sofisticata varianza del è un segno di un consolidato del settore. L'esempio classico è il settore delle telecomunicazioni nel punto in cui gli abbonati a noti spesso passare da un provider a altro. La varianza volontaria è un problema principale. Inoltre, provider hanno accumulata conoscenza significativa *varianza del driver*, quali sono i fattori che i clienti di unità per passare.

Scelta del ricevitore o un dispositivo è ad esempio, un driver noto varianza dell'azienda cellulare del. Di conseguenza, un criterio popolari consiste nel subsidize il prezzo di un ricevitore per gli abbonati a nuovi e in carica un prezzo completo per i clienti esistenti per un aggiornamento. Storicamente, questo criterio ha portato a clienti hopping da un provider a un'altra per ottenere un nuovo sconto, che a sua volta, è richiesto provider per perfezionare le proprie strategie.

Instabili nelle offerte di ricevitore sono un fattore che invalida molto rapidamente i modelli di varianza del basati sui modelli di ricevitore attuali. Telefoni cellulari sono inoltre non solo per i dispositivi di telecomunicazione; sono inoltre istruzioni modo (considerare iPhone) e queste variabili predittive social non rientrano nell'ambito del set di dati di telecomunicazione regolare.

Il risultato per una modellazione consiste nel fatto che si non è possibile definire un criterio di audio semplicemente eliminando noti motivi per cui la varianza. Infatti, una strategia di modellazione continua, inclusi modelli classici quantificare variabili per categoria (ad esempio decisionali) è **obbligatorio**.

Usa grande set di dati dei clienti, organizzazioni siano eseguendo analitica dati (in particolare, il rilevamento di varianza in base a dati) come un modo efficace per il problema. È possibile trovare altre informazioni sull'approccio dati al problema della varianza nei consigli sulla sezione ETL.  

##<a name="methodology-to-model-customer-churn"></a>Metodologia di varianza del cliente modello
Un processo di risoluzione dei problemi comune per risolvere la varianza del cliente è illustrato in figure 1-3:  

1.  Un modello di rischio consente di valutare la possibilità di azioni influenzano probabilità e dei rischi.
2.  Un modello di intervento consente di considerare il livello di intervento Impossibile influenza sulla probabilità di varianza e la quantità di cliente valore durata (CLV).
3.  Questa analisi consente a un'analisi qualitativa che escalation a una campagna di marketing preventiva destinata a segmenti di clienti per inviare l'offerta ottimale.  

![][1]

Questo approccio dall'aspetto professionale in avanti è il modo migliore per gestire la varianza del, ma è dotato di complessità: è necessario sviluppare un sistema di rilevazione più modello e tracciare le dipendenze tra i modelli. Come illustrato nella figura seguente, è possibile encapsulated l'interazione tra modelli:  

![][2]

*Figura 4: Unificata sistema per più modello*  

Interazione tra i modelli è fondamentale se siamo recapitare una strategia di conservazione dei clienti. Ogni modello necessariamente diminuisce nel tempo; di conseguenza, l'architettura è un ciclo implicito (simile al sistema per l'impostazione lo standard di data mining di dati di data mining NITIDO, [***3***]).  

Ciclo di complessivo di marketing di decisione rischio segmentazione/scomposizione è ancora una struttura GRG applicabile a molti problemi aziendali. Analisi varianza sono semplicemente un rappresentante sicuro di questo gruppo dei problemi perché utilizza un tutte le caratteristiche di un problema aziendale complesso che non consente una soluzione di previsione semplificata. Aspetti sociali della approccio moderno alle varianza non vengono evidenziati in particolare in quella, ma le caratteristiche di social networking sono encapsulated nel sistema per una modellazione come avviene in qualsiasi modello.  

In questo caso un componente aggiuntivo di interessante è analitica di dati. Telecomunicazioni odierna e i rivenditori raccolgono dati completi sui propri clienti ed è possibile facilmente prevedere l'esigenza di connettività più modelli, verrà resi una linea di tendenza comune fornite nuove tendenze, ad esempio i Internet di elementi e i dispositivi universali, che consentono di business impiegare soluzioni smart in più livelli.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementazione di sistema per la modellazione in computer risorse Studio
Dato il problema appena descritto, che cos'è il modo migliore per implementare una modellazione integrata e il punteggio approccio? In questa sezione è illustrerà come abbiamo utilizzando questo Azure Machine Learning Studio.  

L'approccio più modello è necessario durante la progettazione di un sistema di rilevazione globale per la varianza. Anche la parte dell'approccio (stima) punteggio dovrebbe essere più modello.  

Il diagramma seguente illustra il prototipo creata, che utilizza quattro algoritmi punteggio in computer risorse Studio per stimare la varianza. Il motivo per l'uso di un approccio più modello non è disponibile solo per creare un classificatore insieme per aumentare l'accuratezza, ma per proteggere adattamento in eccesso e per migliorare la selezione delle caratteristiche precise.  

![][3]

*Figura 5: Prototipo una varianza modellazione approccio del*  

Nelle sezioni seguenti vengono forniscono ulteriori informazioni sulle prototipo punteggio modello implementato tramite Machine Learning Studio.  

###<a name="data-selection-and-preparation"></a>Preparazione e la selezione di dati
I dati utilizzati per creare i modelli e i clienti di punteggio è stata ottenuta da una soluzione verticale CRM, con i dati offuscati per proteggere la privacy dei clienti. I dati contengono informazioni sugli 8.000 abbonamenti negli Stati Uniti e combina tre origini: il provisioning di dati (abbonamento metadati), dei dati delle attività (utilizzo del sistema) e dati di supporto tecnico clienti. I dati non includono aziende di tutte le informazioni relative ai clienti; ad esempio, non include indici di fedeltà metadati o carta di credito.  

Per semplicità, ETL e processi di pulizia dei dati vengono fuori ambito perché si presuppone che la preparazione dei dati è già stati eseguiti altrove.   

Selezione delle caratteristiche per una modellazione dipende dal peso preliminare punteggio del set di variabili predittive, inclusi nel processo che utilizza il modulo foresta casuali. Per l'implementazione in computer risorse Studio, viene calcolata la media, mediana e gli intervalli per le caratteristiche representative. Abbiamo aggiunto, ad esempio, le aggregazioni per i dati qualitativi, ad esempio i valori minimi e massimo per attività dell'utente.    

È inoltre acquisite informazioni temporale per i sei mesi più recente. Abbiamo dati per un anno e stabilito che anche se si sono verificati tendenze forma statistica significative, l'effetto sulla varianza notevolmente ridotta dopo sei mesi.  

Il punto più importante è che l'intero processo, inclusi ETL, funzionalità di selezione e modellazione è stato implementato mediante origini dati in Microsoft Azure Machine Studio risorse.   

I diagrammi seguenti illustrano i dati che è stati utilizzati.  

![][4]

*Figura 6: Estratto dell'origine dati (offuscata)*  

![][5]


*Figura 7: Caratteristiche estratti dall'origine dati*
 
> Si noti che questi dati sono privati e pertanto non possono essere condiviso il modello e i dati.
> Tuttavia, per un modello simile utilizzando i dati disponibili al pubblico, vedere in questo esempio provare nella [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com/): [Varianza del cliente telecomunicazioni](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Per ulteriori informazioni su come è possibile implementare un modello di analisi varianza mediante Cortana Intelligence Suite, è consigliabile anche [in questo video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) , Senior Program Manager relative a Wee Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algoritmi usati nel prototipo

È stato usato algoritmi di quattro computer risorse seguenti per creare il prototipo (senza personalizzazione):  

1.  Regressione logistica (LR)
2.  Albero decisionale invece un incremento (BT)
3.  Perceptron Media (PA)
4.  Supporto computer vettore (SVM)  


Il diagramma seguente illustra una parte dell'area di progettazione di prova, che indichi l'ordine in cui sono stati creati i modelli:  

![][6]  


*Figura 8: Creazione di modelli in computer risorse Studio*  

###<a name="scoring-methods"></a>Metodi di classificazione
Abbiamo totalizza quattro modelli utilizzando un set di dati di formazione su etichetta.  

È inoltre inviato il punteggio set di dati a un modello comparabile creato utilizzando la versione desktop di SA Enterprise mining 12. Sono state misurate l'accuratezza del modello SA e tutti i modelli di computer risorse Studio quattro.  

##<a name="results"></a>Risultati
In questa sezione viene presentato il nostro conclusioni sull'accuratezza dei modelli, in base al punteggio set di dati.  

###<a name="accuracy-and-precision-of-scoring"></a>Precisione ed di classificazione
In genere, l'implementazione di apprendimento Azure è dietro SA precisione di circa 10-15% (Area in curva o AUC).  

Tuttavia, l'unità di misura metriche più importanti della varianza del è il tasso di errore di classificazione: vale a dire di churners primi N come previste per l'utilità di classificazione, chi ha effettivamente **non** varianza e ancora ricevuto un trattamento speciale? Il diagramma seguente confronta il tasso di errore di classificazione per tutti i modelli:  

![][7]


*Figura 9: Passau prototipo riquadro curva*

###<a name="using-auc-to-compare-results"></a>Utilizzo di AUC per confrontare i risultati
Area in curva (AUC) è una metrica che rappresenta una misura globale di *separability* tra le distribuzioni di punteggi per popolazioni positivi e negativi. È simile al grafico ricevitore operatore caratteristica (ROC) tradizionale, ma una differenza importante è l'unità di misura metriche AUC non richiede di scegliere un valore di soglia. Se, tuttavia, i risultati di riepilogo su **tutte** le opzioni possibili. Invece il grafico ROC tradizionale viene visualizzata la tariffa positiva l'asse verticale e il tasso falso positivo sull'asse orizzontale e la soglia di classificazione varia.   

AUC è in genere utilizzati come una misura di patrimonio per algoritmi diversi (o sistemi diversi) perché consente di modelli da confrontare con i rispettivi valori AUC. Si tratta di un approccio più diffuso nel settore, ad esempio Meteorologia e biosciences. In questo modo, AUC rappresenta uno strumento più diffuso per valutare le prestazioni classificatore.  

###<a name="comparing-misclassification-rates"></a>Confronto tra tassi di errore di classificazione
Abbiamo confrontati tassi di errore di classificazione nel set di dati in questione utilizzando i dati di CRM di circa 8.000 sottoscrizioni.  

-   Il tasso di errore di classificazione SA era 10-15%.
-   Il tasso di errore di classificazione Machine Learning Studio è stato 15-20% per churners superiore 200-300.  

Nel settore delle telecomunicazioni, è importante risolvere solo i clienti che hanno il rischio più elevato di varianza offrendo loro un servizio concierge o altri speciale. A questo proposito, l'implementazione di Studio risorse computer di ottenere risultati paragonabile modello SA.  

Analogamente, la precisione è più importante della precisione perché siamo prevalentemente interessati correttamente la classificazione churners potenziali.  

Il diagramma seguente da Wikipedia illustra la relazione in un elemento grafico interessanti e di facile comprensione:  

![][8]

*Figura 10: Compromesso tra precisione ed*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Precisione ed risultati per il modello di albero decisionale invece un incremento  

Il diagramma seguente vengono visualizzati i risultati non elaborati punteggio utilizzando il prototipo di apprendimento per il modello di albero decisionale invece un incremento possono essere accurato tra i quattro modelli:  

![][9]

*Figura 11: Le caratteristiche del modello struttura decisione invece un incremento*

##<a name="performance-comparison"></a>Confronto delle prestazioni
Vengono confrontati la velocità in cui è stata totalizza dati utilizzando i modelli di Studio risorse computer e un modello comparabile creato utilizzando la versione desktop di SA Enterprise mining 12.1.  

Nella tabella seguente vengono riepilogate le prestazioni degli algoritmi:  

*Tabella 1. Prestazioni generali (precisione) degli algoritmi*

| LR|BT|PUNTO DI ACCESSO|SVM|
|---|---|---|---|
|Modello Media|Il modello migliore|Ottimali|Modello Media|

I modelli di ospitato in computer risorse Studio buoni SA 15-25% della velocità di esecuzione, ma la precisione è stato principalmente sulla Val_nom.  

##<a name="discussion-and-recommendations"></a>Discussione e suggerimenti
Nel settore telecomunicazioni diverse pratiche hanno pratica per analizzare la varianza del, tra cui:  

-   Derivano metriche per quattro categorie principali:
    -   **Entità (ad esempio un abbonamento)**. Effettuare il provisioning di informazioni di base su sottoscrizione e/o che è il soggetto varianza del cliente.
    -   **Attività**. Ottenere tutte le informazioni sull'utilizzo possibili sono correlate a entità, ad esempio, il numero di account di accesso.
    -   **Supporto tecnico**. Raccogliere informazioni dal cliente registri di supporto per indicare se l'abbonamento dovuto problemi o interazioni con il servizio di supporto.
    -   **Competitivi e i dati aziendali**. Ottenere le informazioni possibili relative al cliente (ad esempio, può essere disponibile o difficile tenere traccia).
-   Utilizzare priorità di selezione delle caratteristiche di unità. Implica che il modello di albero decisionale invece un incremento sia sempre un approccio promettendo servizi.  

L'uso di questi quattro categorie crea l'effetto che un approccio semplice di tipo *deterministico* basato sugli indici di formato in base a fattori adeguati per categoria, dovrebbe essere sufficienti per identificare i clienti il rischio di varianza. Purtroppo, anche se questo concetto sembra plausibile, è una conoscenza falsa. Il motivo è che la varianza è un effetto temporale fattori per varianza sono in genere in stati temporanei. Cosa conduce un cliente da prendere in considerazione lasciando oggi potrebbe essere diversi domani e sicuramente sarà diversi sei mesi da oggi. Di conseguenza, un *probabilistica* modello è indispensabile.  

Questo importante osservare spesso trascurato in business, che in genere preferisce un approccio business intelligence orientata analitica, soprattutto perché è un semplice vendere e consente l'accesso automazione semplice.  

La promessa della modalità self-service analitica utilizzando computer formazione Studio invece che quattro categorie di informazioni classificate dalla divisione o reparto diventano un'origine utile per imparare a computer varianza.  

Un'altra funzionalità interessante a essere recapitata in apprendimento Azure è la possibilità di aggiungere un modulo personalizzato per l'archivio dei moduli predefiniti che sono già disponibili. Questa funzionalità, creato in pratica, consente di selezionare le raccolte e creare modelli per mercati verticali. Si tratta di un differenziatore importanti di apprendimento Azure sul mercato.  

Ci auguriamo che continuare in questo argomento in futuro, in particolare correlati a analitica di dati.
  
##<a name="conclusion"></a>Conclusioni
In questo articolo vengono illustrati sottoposte a problema comuni di varianza del cliente utilizzando un framework generico. Verrà considerato un prototipo per i modelli di classificazione e implementato tramite l'apprendimento Azure. Infine, viene valutata l'accuratezza e le prestazioni della soluzione prototipo per quanto riguarda comparabile algoritmi SA.  

**Per ulteriori informazioni:**  

Vostra si? Inviare commenti e suggerimenti. Inviare commenti e suggerimenti su una scala da 1 (scarso) a 5 (eccellente), come si valutare questo documento e il motivo per cui si hanno questa valutazione? Per esempio:  

-   Il giudizio in quanto contiene buoni esempi, schermate eccellenti, deselezionare la scrittura o un altro motivo?
-   Il giudizio in basso a causa di scarsa esempi, schermate sfocata o poco chiaro?  

Questo feedback sarà utili per migliorare la qualità di white paper che è rilascio.   

[Inviare commenti e suggerimenti](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Riferimenti
Analitica previsione [1] : oltre alle previsioni occidentale McKnight, Information Management, luglio/agosto 2011, p.18-20.  

Articolo di Wikipedia [2] : [precisione ed](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [data mining tenendo premuto il tasto NITIDO 1.0: dettagliate Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [dati Marketing: esercitare i clienti in modo più efficace e guidare valore](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telecomunicazioni varianza del modello](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) nella [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Appendice

![][10]

*Figura 12: Snapshot di una presentazione nella varianza del prototipo*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
