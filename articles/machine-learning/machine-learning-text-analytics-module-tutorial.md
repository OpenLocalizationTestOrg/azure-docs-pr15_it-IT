<properties
    pageTitle="Creare testo analitica modelli in Azure Machine Learning Studio | Microsoft Azure"
    description="Come creare testo modelli analitica Azure Machine Learning Studio utilizzo dei moduli per la pre-elaborazione di testo, g N o l'hashing caratteristica"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Creare testo analitica modelli in Azure Machine Learning Studio

È possibile utilizzare apprendimento Azure per creare e rendono operativi i modelli di testo analitica. Questi modelli consentono di risolvere, ad esempio problemi di analisi di classificazione o valutazione in documenti.

In una prova di analitica testo, è necessario che in genere:

 1. Pulire e pre-elaborazione set di dati testo
 2. Estrarre vettori caratteristica numerico dal testo pre-elaborato
 3. Modello di classificazione o regressione treno
 4. Punteggio e convalidare il modello
 5. Distribuire il modello di produzione

In questa esercitazione si acquisiscono questa procedura come esaminare un modello di analisi di valutazione in mediante set di dati di Amazon recensioni (vedere questo documento ricerche "biografie, Bollywood, caselle di esplosione e Blenders: adeguamento di dominio per la classificazione di valutazione in" John Blitzer, Mark Dredze e Fernando Pereira; Associazione di elaborazione linguistica (ACL) 2007.) Questo set di dati è costituito da punteggi revisione (1-2 o 4-5) e un testo in formato libero. L'obiettivo è prevedere il punteggio revisione: bassa (1 - 2) o decrescente (4-5).

È possibile trovare esperimenti descritti in questa esercitazione nella raccolta di Business Intelligence Cortana:

[Prevedere recensioni] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prevedere recensioni - prova Office] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Passaggio 1: Pulire e pre-elaborazione set di dati testo

Iniziamo la prova dividendo i punteggi di revisione in intervalli minimo e massimo per categoria per formulare il problema come due classe classificazione. Serve [Modifica metadati] (https://msdn.microsoft.com/library/azure/dn905986.aspx) e moduli [gruppo categoria Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Creare etichette](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Quindi, si rimuovono il testo usando il modulo [pre-elaborazione testo] (https://msdn.microsoft.com/library/azure/mt762915.aspx). La pulizia riduce il rumore in set di dati, consentono di trovare le caratteristiche più importanti e migliorare la precisione del modello finale. Abbiamo rimuovere parole non significative - parole di uso comune, ad esempio "i" o "a" - e numeri, caratteri speciali, caratteri duplicati, indirizzi di posta elettronica e URL. È inoltre convertire il testo in lettere minuscole, lemmatize le parole e rilevare limiti frase quindi indicate da "|||" simbolo di testo pre-elaborazione.

![Pre-elaborazione di testo](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Cosa fare se si desidera utilizzare un elenco personalizzato di parole non significative? È possibile passare in come input facoltativo. È anche possibile utilizzare personalizzato c# sintassi delle espressioni regolari per sostituire sottostringhe e rimuovere le parole da parte di testo parlato: sostantivi, verbi o aggettivi.

Dopo avere completata la pre-elaborazione, abbiamo dividere i dati in treno e testare set.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Passaggio 2: Estrarre vettori caratteristica numerico dal testo pre-elaborato

Per creare un modello di dati di tipo testo, in genere è necessario convertire il testo in formato libero in vettori caratteristica numerico. In questo esempio, viene utilizzata [estrarre il controllo grammaticale N funzionalità dal testo] modulo (https://msdn.microsoft.com/library/azure/mt762916.aspx) per trasformare i dati di testo in tale formato. In questo modulo viene accettata una colonna di parole separati da uno spazio vuoto e calcola un dizionario di parole o g N di parole, che vengono visualizzati nel set di dati. Quindi, viene conteggiata conta quante volte ogni parola o N-g, viene visualizzato in ogni record e crea vettori caratteristica da quelle. In questa esercitazione si impostare dimensioni del controllo grammaticale N a 2, in modo che i vettori caratteristica includono singole parole e combinazioni di parole successive.

![Estrarre N g](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Si applica TF * conta IDF (termini frequenza inversa documento frequenza) peso al controllo grammaticale N. Questo approccio aggiunge peso delle parole che vengono visualizzati di frequente in un singolo record, ma sono rari attraverso l'intero set di dati. Altre opzioni includono binario, TF e conservare peso.

Tali funzioni di testo sono spesso dimensionalità elevato. Ad esempio, se il corpus ha 100.000 parole univoche, lo spazio di funzionalità avrebbe 100.000 dimensioni o più se N g vengono utilizzati. Il modulo di estrarre il controllo grammaticale N funzionalità fornisce un set di opzioni per ridurre la dimensionalità. È possibile scegliere escludere le parole che breve o lungo, troppo insolito o sono troppo frequenti il valore di previsione significativo. In questa esercitazione si escludere g N che vengono visualizzati in meno di 5 record o superiore all'80% di record.

Inoltre, è possibile usare funzionalità di selezione per selezionare solo le caratteristiche che sono più correlati con la destinazione di previsione. Selezione delle caratteristiche del chi quadrato viene utilizzato per selezionare 1000 caratteristiche. È possibile visualizzare la terminologia di parole selezionate o g N facendo clic su output destra del modulo di estrazione N-g.

In alternativa all'utilizzo delle funzionalità di controllo grammaticale N estrarre, è possibile utilizzare l'Hashing caratteristica modulo. Anche se tale [caratteristica Hashing] (https://msdn.microsoft.com/library/azure/dn906018.aspx) non ha le funzionalità di selezione caratteristica di compilazione o TF * IDF peso.

## <a name="step-3-train-classification-or-regression-model"></a>Passaggio 3: Formare modello di classificazione o regressione

A questo punto trasformato il testo in colonne numeriche caratteristica. Il set di dati contiene ancora colonne stringa da fasi precedenti, in modo che serve Seleziona colonne nel set di dati per escluderli.

Serve quindi [due classe regressione logistica] (https://msdn.microsoft.com/library/azure/dn905994.aspx) per prevedere il nostro obiettivo: punteggio revisione bassa o alta. A questo punto, il problema di analitica testo è stato trasformato in un problema di classificazione regolare. È possibile utilizzare gli strumenti disponibili in Azure apprendimento per migliorare il modello. Ad esempio, è possibile sperimentare diverse classificatori per scoprire come precisa forniscono oppure utilizzare hyperparameter ottimizzazioni per migliorare la precisione.

![Treno e punteggio](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Passaggio 4: Punteggio e convalidare il modello

Descritto convalidare il modello di esperto? Abbiamo punteggio contro il set di dati di test e valutare l'accuratezza. Tuttavia, il modello appreso terminologia di g N e loro spessori dal set di dati di formazione. Di conseguenza, bisogna serve tale terminologia e tali spessori durante l'estrazione di caratteristiche dai dati di test, anziché creare nuovamente la terminologia. Pertanto abbiamo aggiungere modulo estrarre le funzionalità di controllo grammaticale N al punteggio ramo di prova, connettersi la terminologia di output da ramo formazione e impostare la modalità di terminologia di sola lettura. È anche disattivare il filtro di g N base alla frequenza impostando il valore minimo a 1 istanza e massimo al 100% e disattivare la funzionalità di selezione.

Dopo la colonna di testo nei dati di test è stata trasformata in colonne numeriche caratteristica, è escludere le colonne di stringa da fasi precedenti come ramo formazione. Serve quindi valutare modello modulo per valutare l'accuratezza e modulo modello punteggio per rendere le stime.

## <a name="step-5-deploy-the-model-to-production"></a>Passaggio 5: Distribuire il modello di produzione

Il modello è quasi pronto per la distribuzione di produzione. Quando viene distribuito come servizio web, accetta come input stringa di testo in formato libero e restituire una stima "alta" o "bassa". Viene utilizzata la terminologia g N acquisita per trasformare il testo alle caratteristiche e modello di regressione logistica qualificato per la stima da queste caratteristiche. 

Per configurare la prova previsione, è necessario salvare innanzitutto terminologia g N come set di dati e il modello di regressione logistica esperti del ramo formazione di prova. È quindi salvare prova usando "Salva con nome" per creare un grafico di prova per la stima prova. Microsoft rimuovere il modulo dati divisi e ramo formazione da di prova. È quindi connettere il controllo grammaticale N terminologia e il modello salvato in precedenza alle caratteristiche di controllo grammaticale N estrarre e moduli di modello di punteggio, rispettivamente. È anche possibile rimuovere il modulo di valutare modello.

Abbiamo inserire le colonne selezionate nel modulo set di dati prima di modulo di pre-elaborazione del testo per rimuovere la colonna etichetta e deselezionare l'opzione "Colonna Aggiungi punteggio al set di dati" nel modulo di punteggio. In questo modo, il servizio web non richiede l'etichetta che sta tentando di prevedere e non non eco le caratteristiche di input in risposta.

![Prova Office](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

A questo punto si dispone di una prova che può essere pubblicato come un servizio web e chiamato utilizzando l'esecuzione di richiesta di risposta o del batch API.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui moduli analitica testo da [documentazione di MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
