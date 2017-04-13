<properties
   pageTitle="Inserire una domanda è possibile rispondere con dati - formulare domande | Microsoft Azure"
   description="Informazioni su come formulare una domanda di scienze dati in dati scienza per principianti video 3. Include un confronto di classificazione e regressione domande."
   keywords="domande di scienze dati, formulare domande, regressione domande e domande di classificazione, nitide domanda"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="ask-a-question-you-can-answer-with-data"></a>Inserire una domanda che è possibile rispondere con i dati

## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Ricerca di scienze dati per serie principianti

Informazioni su come formulare una domanda di scienze dati in dati scienza per principianti video 3. Questo video include un confronto delle domande per algoritmi di classificazione e di regressione.

Per ottenere il massimo dalla serie, guardarli tutti. [Passare all'elenco di video](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Altri video in questa serie

*Ricerca di scienze di dati per principianti* è una breve introduzione a scienza dati cinque brevi video.

  * Video 1: [Le risposte di scienze dati 5 domande](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minuti 14 secondi)*
  * Video 2: [è pronto per scienza dati i dati?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
  * Video 3: Inserire una domanda che è possibile rispondere con i dati
  * [Prevedere una risposta con un modello semplice](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) video 4: *(7 min 42 sec)*
  * Video 5: [Copia di lavoro di altre persone a scopo di scienze di dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Trascrizione: Inserire una domanda che è possibile rispondere con i dati

Introduzione a video terzo della serie "Dati scienza per principianti".  

In questa occorrenza, si otterranno alcuni suggerimenti per la formulazione una domanda che è possibile rispondere con i dati.

È possibile ottenere informazioni alla fine di questo video, se prima di tutto video due precedenti in questa serie: "la ricerca di scienze dati 5 domande possa rispondere" e "È i dati sono pronti per scienza dati?"

## <a name="ask-a-sharp-question"></a>Inserire una domanda nitida

Abbiamo parlato come ricerca di scienze di dati è il processo di utilizzo di nomi (denominati anche categorie o etichette) e i numeri per prevedere una risposta a una domanda. Ma non può essere qualsiasi domanda; è necessario essere un *domanda nitida.*

Una domanda rendere più non è disponibile rispondere con un nome o un numero. Deve essere una domanda nitida.

Si supponga che è trovato Lanterna con un qualunque informazione che verrà affermo rispondere alle eventuali domande che è possibile porre. Ma si tratta di un qualunque informazione operazioni dannose e ha verrà tenta di effettuare la sua risposta come rendere più e confusione come ha può accedere al computer con. Si desidera aggiungere l'immagine verso il basso con una domanda in modo enti governativi che ha non possa aiutare ma sapere di cosa si vuole conoscere.

Se si volesse porre una domanda rendere più, ad esempio "Qual è il problema si verifica con un titolo azionario?", può rispondere a qualunque informazione, "il prezzo modificherà". Una risposta truthful, ma non è molto utile.

Ma se si volesse porre una domanda nitida, come "I prezzo di vendita il titolo da prossima settimana?", qualunque informazione non è possibile Guida ma per ottenere uno specifico rispondere e prevedere un prezzo di vendita.

## <a name="examples-of-your-answer-target-data"></a>Esempi di risposte alle tue domande: dati di destinazione

Una volta è formulare la domanda, controllare se si dispongono di esempi della risposta nei dati.

Se la domanda "Cosa prezzo di vendita il titolo sarà prossima settimana?" quindi è necessario assicurarsi che i dati includono la cronologia delle quotazioni.

Se la domanda "quali auto nel mio parco agirà da dopo l'errore"? quindi è necessario assicurarsi che i dati includono informazioni su errori precedenti.

![Dati di destinazione - esempi di risposte alle tue domande. Formula una domanda di scienze di dati.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

In questi esempi di risposte costituiscono un valore di destinazione. Un valore di destinazione è cosa si sta tentando di prevedere sui punti di dati in futuro, se si tratta di una categoria o un numero.

Se non si dispone di eventuali dati di destinazione, è necessario ottenere alcune. Non sarà possibile la risposta senza di esso.

## <a name="reformulate-your-question"></a>/Baseless o riformulare la domanda

In alcuni casi è possibile modifica la domanda per ottenere una risposta più utile.

La domanda "È il punto dati o B?" stima categoria (o etichetta o il nome) di un elemento. Per tale operazione, viene utilizzato un *algoritmo di classificazione*.

La domanda "Quantità?" o "Quanti?" prevede un importo. Per tale operazione serve un *algoritmo di regressione*.

Per vedere come è possibile trasformare questi, osservare la domanda, "quale articolo di cronaca è più interessanti per questo visualizzatore?" Verrà richiesto di specificare una stima di una singola scelta da molte possibilità - in altre parole "È presente o B o C oppure D?" - e utilizza un algoritmo di classificazione.

Ma questa domanda può risultare più semplice rispondere se si modifica il valore "come interessante sono ogni storia in questo elenco per il lettore?" A questo punto è possibile assegnare ogni articolo un punteggio numerico e quindi è facile individuare l'articolo punteggi. Si tratta di un trovare della domanda classificazione in una domanda di regressione o quantità?

![/Baseless o riformulare la domanda. Domanda di classificazione e domanda di regressione.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Come è possibile porre che una domanda è un'indicazione di algoritmo che consente di ottenere una risposta.

Si trovano che determinate famiglie di algoritmi - simili a quelli riportati in questo esempio brano news - sono strettamente correlate. È possibile /baseless o riformulare la domanda per utilizzare l'algoritmo che fornisce le risposte più utili.

Ma più importante a questa domanda nitida - la domanda che è possibile rispondere con i dati. E assicurarsi di disporre i dati appropriati per rispondere.

Abbiamo parlato alcuni principi di base per porre una domanda che è possibile rispondere con i dati.

Assicurarsi di guardare i video "Dati scienza per principianti" da Microsoft Azure apprendimento.


## <a name="next-steps"></a>Passaggi successivi

  * [Provare una prima dati scienza sperimentare Machine Learning Studio](machine-learning-create-experiment.md)
  * [Per un'introduzione alle apprendimento in Microsoft Azure](machine-learning-what-is-machine-learning.md)
