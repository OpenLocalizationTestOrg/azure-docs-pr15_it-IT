<properties
    pageTitle="Usare i set di dati di esempio nel computer risorse Studio | Microsoft Azure"
    description="Descrizioni dei set di dati nei modelli di esempio inclusi in Studio ML. È possibile utilizzare questi set di dati di esempio per gli esperimenti."
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
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Utilizzare gli insiemi di dati di esempio in Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Quando si crea una nuova area di lavoro in apprendimento Azure, un numero di set di dati di esempio ed esperimenti è inclusi per impostazione predefinita. Molte di queste set di dati di esempio vengono utilizzate dai modelli di esempio nella [Raccolta di Business Intelligence Cortana Azure](http://gallery.cortanaintelligence.com/)e gli altri utenti sono inclusi come esempi dei diversi tipi di dati in genere utilizzati in apprendimento.

Alcuni di questi insiemi di dati sono disponibili in archiviazione Blob Azure. Per i set di dati nella tabella seguente fornisce un collegamento diretto. È possibile usare questi set di dati in esperimenti con i [Dati di importazione] [ import-data] modulo.

Il resto di questi set di dati di esempio sono elencati sotto **Salvato set di dati** nel riquadro a sinistra dell'area di prova modulo quando si apre o si crea una nuova prova ML Studio.
È possibile usare uno di questi set di dati nel proprio test trascinandola all'area di disegno di prova.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nome del set di dati</th>
  <th align=left>Descrizione di set di dati</th>
</tr>

<tr>
  <td valign=top>Set di dati di adulto censimento reddito binario classificazione</td>
  <td valign=top>
Un sottoinsieme del database censimento 1994, utilizzando lavoro adulti età ai 16 con indice reddito > 100.<p> </p><b>Sintassi:</b> classificare persone che usano fasce di utenti a prevedere se un utente si aggiudica le oltre 50K all'anno.<p> </p><b>Ricerche correlate:</b> Kohavi, r, Becker, b, (1996). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Set di dati codici degli aeroporti</td>
  <td valign=top>
Codici degli aeroporti degli Stati Uniti.<p> </p>Questo set di dati contiene una riga per ogni degli aeroporti degli Stati Uniti, fornire il numero di ID aeroporto e il nome e posizione città e stato.
  </td>
</tr>

<tr>
  <td valign=top>Dati di prezzo veicoli (materie)</td>
  <td valign=top>
Informazioni sulle automobili da verificare e del modello, inclusi il prezzo, caratteristiche, ad esempio il numero di cilindri MPG, nonché un punteggio di rischio assicurazione.<p> </p>Il punteggio di rischio inizialmente associata automatico prezzo e sono state modificate per i rischi effettivo in un processo noto attuari come symboling. Valore + 3 indica che l'automatica è rischiosa e un valore compreso tra -3 che si tratta probabilmente abbastanza attendibili.<p> </p><b>Sintassi:</b> prevedere il punteggio di rischio dalle funzionalità mediante regressione o multivariate classificazione. <p> </p><b>Ricerche correlate:</b> Schlimmer, J.C. (1987). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Set di dati di bicicletta UCI noleggio</td>
  <td valign=top>
UCI bicicletta noleggio set di dati basati sui dati reali della società di capitale Bikeshare che gestisce una rete noleggio bicicletta in Data Center di Washington.<p> </p>Il set di dati con una riga per ogni ora di ogni giorno nel 2011 e 2012, per un totale di 17,379 righe. L'intervallo di ogni ora noleggi bici è compreso tra 1 e 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Bill Gates immagine RGB</td>
  <td valign=top>
File di immagine disponibile al pubblico convertiti in dati CSV.<p> </p>Il codice per la conversione dell'immagine viene fornito nella pagina di dettaglio <strong>colore quantizzazione utilizzo del servizio cluster K-medie</strong> modello.
  </td>
</tr>

<tr>
  <td valign=top>Dati donazioni sanguigna</td>
  <td valign=top>
Un sottoinsieme di dati dal database donatore sanguigna di trasfusioni Service Center di Hsin Chu città, Taiwan.<p> </p>Dati donatore includono i mesi dall'ultimo donazioni) e la frequenza o il numero totale di donazioni, il tempo trascorso dall'ultimo donazioni e quantità di sanguigna donato.<p> </p><b>Sintassi:</b> l'obiettivo è prevedere tramite classificazione se donatore donato sanguigna nel marzo 2007, in cui 1 indica un donatore durante il periodo di destinazione e 0 un non donatore. <p> </p><b>Ricerche correlate:</b> arabo, i. c, (2008). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica <p> </p>Arabo, si-Cheng, Yang, re-Jang e Ting, toccare Ming, "scoperta sul modello RFM utilizzando la sequenza di Bernoulli" sistemi esperto con le applicazioni, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Recensioni da Amazon</td>
  <td valign=top>
Analisi di materiale di Amazon adottata dal sito Web amazon.com ricercatori University di Pennsylvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">valutazione in</a>). Documento di ricerca, vedere "biografie, Bollywood, caselle di esplosione e Blenders: adeguamento di dominio per la classificazione di valutazione in" John Blitzer, Mark Dredze e Fernando Pereira; Associazione di elaborazione linguistica (ACL) 2007.<p> </p>Il set di dati originali sono recensioni 975K con valutazioni 1, 2, 3, 4 o 5. Le revisioni sono stati scritti in lingua inglese e provengono dal periodo di tempo 1997 2007. Questo set di dati è stata campionato verso il basso in base ai 10K revisioni.
  </td>
</tr>

<tr>
  <td valign=top>Dati cancro petto</td>
  <td valign=top>
Uno dei tre correlati cancro set di dati fornito dall'istituto Oncology che viene visualizzato spesso nella documentazione di risorse computer. Combina informazioni di diagnostica con le funzionalità di analisi di laboratorio di circa 300 campioni di tessuti.<p> </p><b>Sintassi:</b> classificare il tipo di tumori, in base a 9 attributi, alcune delle quali sono lineare e altri per categoria. <p> </p><b>Ricerche correlate:</b> O.L. Wohlberg, W.H., via e numero civico, W.N. e Mangasarian, (1995). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Caratteristiche di cancro petto <td valign=top>
Il set di dati contiene informazioni per aree geografiche sospetti 102K (candidati) di immagini raggi x, ognuno descritto da 117 caratteristiche. Le caratteristiche sono proprietari e il relativo significato non è protetta per i creatori di set di dati (il settore sanitario Siemens). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Petto cancro Info</td>
  <td valign=top>
Il set di dati contiene informazioni aggiuntive per ogni area sospetto dell'immagine raggi x. Ogni esempio fornisce informazioni (ad esempio etichetta, pazienti ID, le coordinate di patch rispetto all'immagine intera) sul numero di riga corrispondente nel set di dati petto cancro caratteristiche. Pazienti contiene un numero di esempi. Per pazienti che hanno un cancro, alcuni esempi sono positivi e alcune sono negativi. Per pazienti che non dispongono di un cancro, tutti gli esempi sono negativi. Il set di dati sono disponibili esempi di 102K. Il set di dati è distorta, 0,6% dei punti sono positivi, il resto sono negativi. Il set di dati è stata effettuata disponibile il settore sanitario Siemens.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency etichette condivise</td>
  <td valign=top>
Etichette da in attesa KDD Cup 2009 cliente relazione stima (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etichette di varianza CRM condivise</td>
  <td valign=top>
Etichette da in attesa KDD Cup 2009 cliente relazione stima (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>CRM set di dati condivisi</td>
  <td valign=top>
Questi dati provengono da in attesa KDD Cup 2009 cliente relazione stima (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Il set di dati contiene 50K clienti della società di telecomunicazione francese arancione. Ogni cliente ha 230 caratteristiche resi anonimi, 190 delle quali sono numerici e 40 sono per categoria. Le caratteristiche sono molto caricato in memoria bassa.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM la vendita upselling partendo etichette condivise</td>
  <td valign=top>
Etichette da in attesa KDD Cup 2009 cliente relazione stima (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Dati di regressione l'efficienza energetica</td>
  <td valign=top>
Insieme di profili di energia simulata, in base a 12 forme edificio diverso. Edifici diverso rispetto a differenziano 8 caratteristiche, ad esempio vetro area, la distribuzione area vetro e l'orientamento.<p> </p><b>Sintassi:</b> consente di prevedere l'efficienza di valutazione di base in uno dei due risposte reale valutate regressione o la classificazione. Per la classificazione di classe multiple, è arrotondare la variabile di risposta per eccesso all'intero più vicino. <p> </p><b>Ricerche correlate:</b> Xifara, risposte e Tsanas, risposte (2012). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Volo ritardi dati</td>
  <td valign=top>
Passeggeri volo puntuale dati sulle prestazioni prelevati dalla raccolta di dati TranStats di reparto IT di trasporto (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">Puntuale</a>).<p> </p>Il set di dati si riferisce al periodo di tempo aprile-ottobre 2013. Prima di caricare in Azure ML Studio, il set di dati è stata elaborata come segue:<ul><li>Il set di dati è stato filtrato in modo da coprire solo i 70 più occupati aeroporti continentali degli Stati Uniti</li><li>Voli annullati sono state sono indicati come posticipato di più di 15 minuti</li><li>Sono stati filtrati voli deviati</li><li>Le colonne seguenti sono state selezionate: anno, mese, DayofMonth, DayOfWeek, vettore, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15 e annullato</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Prestazioni in fase di volo (materie)</td>
  <td valign=top>
Record di arrivi dei voli aerei e le partenze all'interno degli Stati Uniti dal ottobre 2011.<p> </p><b>Sintassi:</b> prevedere ritardi volo. <p> </p><b>Ricerche correlate:</b> dal reparto IT di trasporto <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Insieme di strutture viene attivata dati</td>
  <td valign=top>
Contiene dati meteo, ad esempio temperatura e umidità indici e la velocità del vento, da un'area del nord-est Portogallo, combinato con record di strutture generato.<p> </p><b>Sintassi:</b> si tratta di un'attività di regressione difficile, in cui l'obiettivo è prevedere l'area masterizzato della foresta generato. <p> </p><b>Ricerche correlate:</b> Cortez, p. & Morais, risposte (2008). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica <p> </p>[Cortez e Morais, 2007] P. Cortez e Morais risposte. Un approccio Mining dati di utilizzo dei dati meteorologiche prevedere foresta generato. In J. Neves, F. m Santos e J. Machado Eds., nuove tendenze nei artificiale Intelligence procedimenti di 2007 EPIA dal 13 - portoghese conferenza su artificiale Intelligence dicembre, Guimarães, Portogallo, pagine 512-523, 2007. APPIA, ISBN 13 978-989-95618-0-9. Disponibile in: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Tedesco sulla carta di credito UCI set di dati</td>
  <td valign=top>
Il Statlog UCI (carta di credito tedesco) set di dati (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tedesco + carta di credito + dati</a>), utilizzando il file german.data.<p> </p>Il set di dati classifica persone, descritte da un insieme di attributi, come i rischi di carta di credito bassa o alta. Ogni esempio rappresenta una persona. Sono disponibili caratteristiche di 20, numerici e di categoriale e un'etichetta binaria (il valore di rischio di carta di credito). Movimenti rischio elevato hanno etichetta = 2, movimenti rischio bassa hanno etichetta = 1. Il costo di interpretato come un esempio di basso rischio come alta è 1, mentre è "5" il costo di interpretato come un esempio ad alto rischio come bassa.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titoli dei filmati IMDB</td>
  <td valign=top>
Il set di dati contiene le informazioni relative filmati che sono stati classificati in Twitter TWEET: IMDB filmato ID, nome filmato e perimetro, anno di produzione. Sono 17 KB filmati il set di dati. Il set di dati è stata introdotta in carta "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: un filmato valutazione set di dati raccolti dal Twitter. Workshop Crowdsourcing e calcolo umano per sistemi consigliere, CrowdRec a 2013 RecSys."
  </td>
</tr>

<tr>
  <td valign=top>Dati di classe iride due</td>
  <td valign=top>
Si tratta probabilmente del database comunemente utilizzato da trovare nella documentazione di riconoscimento del motivo. Il set di dati è relativamente piccolo, che contiene 50 esempi di unità di misura petalo da tre tipi di iride.<p> </p><b>Sintassi:</b> prevedere il tipo di iride sono indicate le misure.  <p> </p><b>Ricerche correlate:</b> Fisher, R.A. (1988). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>TWEET filmato</td>
  <td valign=top>
Il set di dati è una versione estesa del set di dati Tweetings filmato. Il set di dati sono 170K valutazioni per i filmati, estratte da strutturati TWEET su Twitter. Ogni istanza rappresenta un tweet e una tupla: ID utente, IMDB filmato ID, valutazione, timestamp, numero di Preferiti per questo tweet e il numero di retweets di questo tweet. Il set di dati è stata effettuata disponibili a detto, s Dooms, Loni B. e Tikk D. per consigliere sistemi verifica 2014.
  </td>
</tr>

<tr>
  <td valign=top>Dati MPG per automobili diversi</td>
  <td valign=top>
Questo set di dati è una versione leggermente modificata del set di dati forniti dalla libreria di StatLib della Carnegie Mellon University. Il set di dati è stato utilizzato in spiegazione di associazione statistiche American 1983.<p> </p>Gli elenchi dei dati consumo carburante per diverse automobili in miglia gallone, insieme a informazioni ad esempio il numero di cilindri, spostamento motore, potenza, peso totale e l'accelerazione.<p> </p><b>Sintassi:</b> prevedere carburante in base a attributi distinti multivalore 3 e 5 continua. <p> </p><b>Ricerche correlate:</b> StatLib, Carnegie Mellon University (1993). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr>
  <td valign=top>Set di dati Pima Indians diabete binario classificazione</td>
  <td valign=top>
Un sottoinsieme di dati dall'istituto nazionale diabete e quest ' e il relativo malattie database. Il set di dati è stato filtrato per concentrarsi su pazienti femminile del patrimonio indiane Pima. I dati includono dati medici, ad esempio glucosio e livelli di sciroppo, nonché dello stile di vita.<p> </p><b>Sintassi:</b> prevedere se l'oggetto è diabete (binaria classificazione). <p> </p><b>Ricerche correlate:</b> Sigillito v (1990). Apprendimento automatico UCI Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica </td>
</tr>

<tr>
  <td valign=top>Dati del cliente ristorante</td>
  <td valign=top>
Un set di metadati relativi a clienti, tra cui fasce di utenti e le preferenze.<p> </p><b>Sintassi:</b> utilizzare questo set di dati, in combinazione con gli altri due ristorante insiemi di dati, per organizzare la formazione e collaudare il sistema di consigliere. <p> </p><b>Ricerche correlate:</b> Bache, k e Lichman, m (2013). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica.
  </td>
</tr>

<tr>
  <td valign=top>Dati delle funzionalità ristorante</td>
  <td valign=top>
Un set di metadati relativi ristoranti e le relative funzionalità, ad esempio alimentari tipo, stile pranzo e posizione.<p> </p><b>Sintassi:</b> utilizzare questo set di dati, in combinazione con gli altri due ristorante insiemi di dati, per organizzare la formazione e collaudare il sistema di consigliere. <p> </p><b>Ricerche correlate:</b> Bache, k e Lichman, m (2013). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica.
  </td>
</tr>

<tr>
  <td valign=top>Valutazioni ristorante</td>
  <td valign=top>
Contiene valutazioni assegnate dagli utenti per ristoranti su una scala da 0 a 2.<p> </p><b>Sintassi:</b> utilizzare questo set di dati, in combinazione con gli altri due ristorante insiemi di dati, per organizzare la formazione e collaudare il sistema di consigliere. <p> </p><b>Ricerche correlate:</b> Bache, k e Lichman, m (2013). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica.
  </td>
</tr>

<tr>
  <td valign=top>Acciaio Annealing classe più set di dati</td>
  <td valign=top>
Questo set di dati contiene una serie di record da acciaio annealing prove con gli attributi fisici (larghezza, spessore, tipi di tipo (serpentina, foglio, ecc.) dell'oggetto risultante acciaio.<p> </p><b>Sintassi:</b> prevedere uno dei due attributi di classe numerico; durezza o forza della funzione. Potrebbero anche analizzare correlazioni tra gli attributi.<p> </p>Tipi di acciaio seguono uno standard di set, definita da SAE e da altre organizzazioni. Si sta cercando uno specifico 'grade"(la variabile di classe) e si vuole conoscere i valori necessari. <p> </p><b>Ricerche correlate:</b> britannica, D. e Buntine, w, (NA). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni e informatica <p> </p>Una guida utile per tipi di acciaio sono disponibili qui: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Cannocchiale dati</td>
  <td valign=top>
Record di elevata energia gamma particella burst insieme rumore, entrambi simulata mediante un processo Monte Carlo.<p> </p>Lo scopo della simulazione è stato per migliorare la precisione della terra senso Cherenkov gamma dispositivi, mediante metodi statistici per distinguere tra il segnale desiderato (Cherenkov ionizzanti docce) e rumore (docce hadronic avviate da raggi cosmici in atmosfera superiore).<p> </p>I dati sono stati pre-elaborati per creare un cluster allungato con il valore long asse è orientato verso il centro di fotocamera. Caratteristiche di questa ellisse (spesso denominate parametri Hillas) sono fra i parametri di immagine che possono essere usati per discriminazioni.<p> </p><b>Sintassi:</b> prevedere se l'immagine di una festa rappresenta rumore segnale o uno sfondo.<p> </p><b>Note:</b> la precisione di classificazione semplice non è significativa per i dati, dopo la classificazione di un evento di sfondo come segnale peggio la classificazione di un evento segnale come sfondo. Per il confronto dei diversi classificatori grafico ROC deve essere utilizzato. La probabilità di accettazione di un evento di sfondo come segnale deve essere seguito uno dei seguenti soglie: 0,01, 0,02, 0,05, 0,1 o 0,2.<p> </p>Si noti inoltre che è sottovalutato il numero di eventi in background (h per docce hadronic), mentre in unità di misura reale, la classe h o rumore rappresenta la maggior parte degli eventi. <p> </p><b>Ricerche correlate:</b> tipologie: blocchi, R.K. (1995). UCI apprendimento automatico Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: Università della California, dell'istituto di istruzione di informazioni </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Set di dati meteo</td>
  <td valign=top>
Osservazioni terra meteo oraria da NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">dati uniti da 201304 a 201310</a>).<p> </p>I dati meteo coprono osservazioni dalle elettriche presenti meteo aeroporto, relativi al periodo di tempo aprile-ottobre 2013. Prima di caricare in Azure ML Studio, il set di dati è stata elaborata come segue:<ul><li>ID di espansione meteo mappati a aeroporto corrispondente ID</li><li>Sono state filtrate le elettriche presenti meteo non associate a 70 aeroporti più occupati</li><li>La colonna Data è stato suddiviso in colonne distinte anno, mese e giorno</li><li>Le colonne seguenti sono state selezionate: AirportID, anno, mese, giorno, ora, fuso orario, SkyCondition, visibilità, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, velocità del vento, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 set di dati</td>
  <td valign=top>
Dati dipende dal Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) in base a trovare articoli di ogni società S & P 500 memorizzato come dati XML.<p> </p>Prima di caricare in Azure ML Studio, il set di dati è stata elaborata come segue:<ul><li>Estrarre il contenuto di testo per ogni società specifica</li><li>Rimuovere la formattazione wiki</li><li>Rimuovere i caratteri non alfanumerici</li><li>Convertire tutto il testo in lettere minuscole</li><li>Categorie di società noti sono stati aggiunti</li></ul><p> </p>Si noti che per alcune aziende un articolo trovato, in modo che il numero di record è inferiore a 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Il set di dati contiene dati dei clienti e le indicazioni sulle proprie risposte a una campagna posta diretta. Ogni riga rappresenta un cliente. Il set di dati contiene 9 caratteristiche sulle fasce di utenti utente e il comportamento e 3 colonne etichetta (visitare, conversione e dedicato).  Visita è una colonna binaria che indica che un cliente visitati dopo la campagna di marketing, conversione indica un cliente acquistato qualcosa e dedicato è la quantità dedicata.  Il set di dati è stata effettuata disponibili Kevin Hillstrom per MineThatData posta elettronica Analitica e dati Mining in attesa.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Caratteristiche di esempi di test nel set di dati di news Reuters RCV1 V2. Il set di dati sono articoli 781K insieme gli ID (prima colonna del set di dati). Ogni articolo è suddiviso in token, stopworded e derivata. Il set di dati è stata effettuata disponibili David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Caratteristiche di esempi di formazione nel set di dati di news Reuters RCV1 V2. Il set di dati sono articoli 23K insieme gli ID (prima colonna del set di dati). Ogni articolo è suddiviso in token, stopworded e derivata. Il set di dati è stata effettuata disponibili David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Set di dati dalla scoperta Cup 1999 KDD e di Data Mining strumenti concorrenza (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>Il set di dati è stato scaricato e archiviati in archiviazione Blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e include sia formazione e verifica set di dati. Il set di dati di formazione ha circa 126K righe e 43 colonne, ad esempio le etichette; 3 colonne fanno parte di informazioni sull'etichetta e 40 colonne, composta da caratteristiche numerici e stringa/categoria, sono disponibili per il modello di formazione. I dati di test hanno circa 22,5 KB provare esempi con le stesse 43 colonne come i dati di formazione.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Assegnazioni argomento per trovare articoli di news nel set di dati di news Reuters RCV1 V2. Articolo può essere assegnato ad argomenti diversi. Il formato di ogni riga è "<topic name>  <document id> 1". Il set di dati contiene le assegnazioni argomento 2.6M. Il set di dati è stata effettuata disponibili David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Questi dati provengono dalla richiesta di valutazione delle prestazioni KDD Cup 2010 studente (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">valutazione delle prestazioni student</a>). Dati usati sono l'insieme di formazione Algebra_2008_2009 (Stamper, j, Niculescu-Mizil, risposte, Ritter, s, Gordon, G.J. e Koedinger, K.R. (2010). Algebra si 2008 2009. Verifica set di dati con KDD Cup 2010 didattici dati Mining in attesa. Trovarlo <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> o <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Il set di dati è stato scaricato e archiviati in archiviazione Blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e sono presenti file di log da uno studente lezioni private di sistema. Le funzionalità fornite includono ID problema e la breve descrizione, ID studente, timestamp e il numero di tentativi lo studente apportato prima di risolvere il problema nel modo appropriato. Il set di dati originale ha record 8,9 M, questo set di dati è stato eseguito per le prime righe di 100 KB. Il set di dati sono presenti le 23 colonne delimitato da tabulazione tipi diversi: valore numerico, per categoria e timestamp.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
