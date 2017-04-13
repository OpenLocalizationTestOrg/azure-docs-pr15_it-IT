<properties
   pageTitle="È pronto per scienza dati i dati? Valutazione dei dati | Microsoft Azure"
   description="Informazioni su 4 criteri per i dati siano pronti a Scienze di dati. Ricerca di scienze di dati per principianti video 2 è concreti esempi per agevolare la valutazione di dati di base."
   keywords="dati rilevanti, valutare i dati, preparare i dati, criteri di dati, dati pronti"
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


# <a name="is-your-data-ready-for-data-science"></a>È pronto per scienza dati i dati?

## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Ricerca di scienze dati per serie principianti

Informazioni su come calcolare i dati in modo che vengano soddisfatti i criteri di base per essere pronti per Scienze di dati.

Per ottenere il massimo dalla serie, guardarli tutti. [Passare all'elenco di video](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## <a name="other-videos-in-this-series"></a>Altri video in questa serie

*Ricerca di scienze di dati per principianti* è una breve introduzione a scienza dati cinque brevi video.

  * Video 1: [Le risposte di scienze dati 5 domande](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minuti 14 secondi)*
  * Video 2: È pronto per scienza dati i dati?
  * Video 3: [Porre una domanda è possibile rispondere con dati](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
  * [Prevedere una risposta con un modello semplice](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) video 4: *(7 min 42 sec)*
  * Video 5: [Copia di lavoro di altre persone a scopo di scienze di dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Trascrizione: È pronto per scienza dati i dati?

Introduzione a "È i dati per scienza dati?" video secondo nella serie di *Dati scienza per principianti*.  

Prima di scienze di dati è possono che vengano le risposte desiderate, è necessario assegnare alcune materia di alta qualità per l'uso con. Come effettuare una pizza meglio i componenti di iniziare con migliore del prodotto finale.

## <a name="criteria-for-data"></a>Criteri per i dati

Quindi, nel caso di scienze di dati, esistono alcuni elementi che è necessario raccogliere.

È necessario i dati:

  * I contenuti pertinenti
  * Connesso
  * Accurata
  * Sufficiente per l'uso con

## <a name="is-your-data-relevant"></a>Riguarda i dati?

In modo che il primo elemento, è necessario i dati rilevanti.

![Dati rilevanti e irrilevanti dati - valutare i dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Osservare la tabella sul lato sinistro. Vengono soddisfatte sette persone all'esterno di barre di Boston, misurato il livello di alcole sanguigna, la media di battuta rosso Sox loro gioco ultimo e il prezzo di latte nell'archivio comodità più vicino.

Si tratta in tutti i dati perfettamente legittimi. È solo guasto è che non è rilevante. È non presente alcuna relazione più evidenti tra questi numeri. Se è assegnato il prezzo corrente della media di battuta Sox rosso, non è possibile potrebbero indovinare contenuti personali alcole sanguigna.

Esaminare la tabella a destra. Questa volta abbiamo misurato ogni persona corpo massa e contare il numero di bevande avute.  I numeri in ogni riga sono ora pertinenti tra loro. Se è assegnato il corpo massa e il numero di limetta fresca ho, è possibile creare un'ipotesi al mio sanguigna alcole contenuto.

## <a name="do-you-have-connected-data"></a>La connessione dati?

L'elemento successivo è dati connessi.

![Connessione dati e dati disconnessi - criteri dati, dati pronti](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Ecco alcuni dati rilevanti sulla qualità del hamburger: griglia temperatura, lo spessore ad e classificazione in alimentari locali rivista. Ma gli spazi vuoti nella tabella sul lato sinistro.

La maggior parte dei set di dati non contiene alcuni valori. Viene in genere necessario holes … e modi per risolvere tale. Ma se c'è troppo mancanti, i dati iniziano aspetto formaggio Svizzera.

Se osserva la tabella sul lato sinistro, esiste in modo che tutti i dati mancanti, è difficile sorgere con qualsiasi tipo di relazione tra lo spessore di temperatura e ad griglia. Questo è un esempio di dati disconnessi.

La tabella a destra, tuttavia, si riempie e completata - un esempio di dati connessi.

## <a name="is-your-data-accurate"></a>È accurato i dati?

L'elemento successivo che è necessario è maggiore precisione. Di seguito sono quattro i siti di destinazione che si desidera legati al frecce.

![Accuratezza dei dati e dati non accurati - criteri di dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Osservare la destinazione in alto a destra. Abbiamo un raggruppamento ravvicinato direttamente all'interno di tiro a segno. Naturalmente, ovvero accurato. In modo anomalo, in lingua di scienze di dati, le prestazioni sul lato destro destinazione sotto di essa viene considerata anche accurate.

Se si volesse mappare al centro di queste frecce, si vedrà che è molto simile il tiro a segno. Le frecce sono distribuite tutto all'interno di destinazione, in modo che è considerati preciso, ma è centrate tiro a segno, quindi si considerate accurati.

Esaminare la destinazione angolo superiore sinistro. Di seguito il nostro frecce raggiunto molto vicini, un raggruppamento Ravvicinato. Sono precisi, ma che siano inesatte poiché l'interfaccia di fuori di tiro a segno. E, naturalmente, le frecce nella parte inferiore sinistra destinazione accurati e preciso. Questo Bonaldi deve pratica.

## <a name="do-you-have-enough-data-to-work-with"></a>Viene offerto abbastanza dati da utilizzare con?

Infine, principio #4 - è necessario avere abbastanza dati.

![Si dispone di dati sufficiente per l'analisi? Valutazione dei dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Pensare a ciascun punto dati della tabella come un pennello in un disegno. Se è disponibile solo alcune, il disegno può essere molto sfocato - è difficile stabilire invariabile.

Se si aggiungono alcuni più tratti di pennello, il disegno viene avviato ottenere un po' più nitidi.

Dopo aver poco abbastanza tratti, è possibile vedere tanto che basta per alcune decisioni generali. È in un punto che desidera conoscere visitare? Appare chiaro, che è simile alla acqua pulita: Sì, ovvero nel punto in cui sta andare in vacanza.

Mentre si aggiungono altri dati, diventa più chiara l'immagine e poter prendere decisioni più dettagliate. È possibile esaminiamo tre alberghi banca a sinistra. Si conosce, mi piace davvero funzionalità all'architettura di quella in primo piano. Verrà mantenere, planimetrie terza.

Con dati rilevanti, connesso e la precisione dei e è soddisfacente, dispone di tutti i componenti è necessario eseguire scienza alcuni dati di alta qualità.

Assicurarsi di estrarre gli altri quattro video su *Scienza dati per principianti* da Microsoft Azure Machine Learning.




## <a name="next-steps"></a>Passaggi successivi

  * [Provare una prima dati scienza sperimentare Machine Learning Studio](machine-learning-create-experiment.md)
  * [Per un'introduzione alle apprendimento in Microsoft Azure](machine-learning-what-is-machine-learning.md)
