<properties
    pageTitle="Tipi di compilazione e la qualità di modello: consigli API | Microsoft Azure"
    description="Azure Machine Learning consigli - Guida introduttiva"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Tipi di compilazione e la qualità del modello #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Tipi di generazione supportati ##

L'API consigli supporta attualmente due tipi di generazione: *recommendation* e *delle rate delle imposte*. Ogni integrato con algoritmi diversi, ognuno con punti di forza diversi. In questo documento sono descritti questi build e tecniche per il confronto la qualità dei modelli generato.

Se si dispone non già fatto, è consigliabile completare la [Guida introduttiva](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Tipo di generazione Recommendation ###

Il tipo di generazione recommendation utilizza GPO matrice per fornire consigli. Genera vettori [caratteristica affidabili](https://en.wikipedia.org/wiki/Latent_variable) in base a sotto controllo le transazioni per descrivere ogni elemento e quindi utilizza tali vettori affidabili per confrontare elementi simili.

Se si formazione il modello in base a acquistati nel negozio elettronici e fornisce un telefono Lumia 650 come input al modello, il modello restituirà un insieme di elementi che si trovano acquistare agli utenti che possono acquistare un telefono Lumia 650. Gli elementi potrebbero non essere complementari. In questo esempio, è possibile che il modello verrà restituito altri telefoni perché utenti come 650 Lumia potrebbero ad esempio gli altri telefoni.

La compilazione di recommendation è due funzionalità che rendono interessante:

* *Supporta la compilazione di recommendation *fredda elemento* posizionamento * *

Gli elementi che non dispongono di utilizzo significativa sono denominati voci fredde. Ad esempio, se si riceve una spedizione di un telefono che non è mai vendute, il sistema non è possibile dedurre consigli per questo prodotto transazioni solo. Questo errore indica che il sistema di imparare a utilizzare le informazioni del prodotto stesso.

Se si desidera utilizzare posizionamento degli articoli fredda, è necessario fornire informazioni sulle caratteristiche per tutti gli elementi del catalogo. Di seguito è riportato le prime righe del catalogo potrebbero aspetto (formato nota chiave = valore per le caratteristiche).

>Pro2 6CX 00001, superficie, superficie, digitare = Hardware, lo spazio di archiviazione = 128 GB di memoria = 4g, produttore = Microsoft

>00013 73h, riattivazione Xbox 360, gioco, tipo = Software, in lingua inglese, valutazione di = = consolidato

>WAH 0F05, Minecraft Xbox 360, gioco, * tipo = Software, in lingua spagnolo, valutazione di = = gioventù

È anche necessario impostare i parametri di compilazione seguenti:

| Il parametro di generazione         | Note
|------------------     |-----------
|*useFeaturesInModel*     | Impostare su **true**.  Indica se è possano utilizzare le funzionalità per migliorare il modello di recommendation.
|*allowColdItemPlacement*   | Impostare su **true**. Indica se il suggerimento anche propagare elementi freddo tramite analogie caratteristica.
| *modelingFeatureList*   | Elenco delimitato da virgole di nomi da utilizzare nella build recommendation per migliorare il suggerimento. Ad esempio, "lingua, lo spazio di archiviazione" nell'esempio precedente.

**La compilazione di recommendation supporta consigli utente**

Creare un suggerimento supporta [consigli utente](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Questo errore indica che può fornire i consigli personalizzati per gli utenti in base alle loro cronologie transazione. Per ulteriori suggerimenti utente, è possibile fornire l'ID utente o la cronologia recente delle transazioni per l'utente.

Uno esempio classico di cui applicare consigli utente si trova all'accesso aggiuntivo nella pagina di benvenuto. È possibile alzare di livello il contenuto che si applica a utenti specifici.

È anche possibile applicare un tipo di generazione consigli quando l'utente sta per estrarre. A questo punto, è l'elenco degli elementi che l'utente sta per acquistare che è possibile fornire basate sui carrello mercato corrente.

#### <a name="recommendations-build-parameters"></a>Parametri di compilazione consigli

| Nome  |   Descrizione |    Tipo <br>  valori validi, <br> (valore predefinito)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   Il numero di iterazioni che esegue il modello è rappresentato dal tempo totale di elaborazione e la precisione del modello. Maggiore il numero, più accurata il modello, ma il tempo di elaborazione richiede più tempo.  |   Numero intero, <br>  10 e 50 <br>(40)
| *NumberOfModelDimensions* |   Il numero di dimensioni si riferisce al numero di funzionalità che il modello tenterà di trovare all'interno dei dati. Aumento del numero di dimensioni consentirà migliore ottimizzazione dei risultati in cluster più piccoli. Tuttavia, troppe dimensioni impedirà il modello di ricerca correlazioni tra gli elementi. |  Numero intero, <br> 10-40 <br>(20) |
| *ItemCutOffLowerBound* |  Definisce il numero minimo di punti di utilizzo che un elemento deve essere nel affinché possa essere considerato parte del modello. |        Numero intero, <br> 2 o più <br> (2) |
| *ItemCutOffUpperBound* |  Definisce il numero massimo di punti che deve essere un elemento affinché possa essere considerato parte del modello. |  Numero intero, <br>2 o più<br> (2147483647) |
|*UserCutOffLowerBound* |   Definisce il numero minimo di transazioni che un utente è eseguita per essere considerato parte del modello. | Numero intero, <br> 2 o più <br> (2)
| *UserCutOffUpperBound* |  Definisce il numero massimo di transazioni che un utente è eseguita per essere considerato parte del modello. | Numero intero, <br>2 o più <br> (2147483647)|
| *UseFeaturesInModel* |    Indica se è possano utilizzare le funzionalità per migliorare il modello di recommendation. |     Valore booleano<br> Predefinito: vero
|*ModelingFeatureList* |    Elenco delimitato da virgole dei nomi delle funzionalità da utilizzare nella build recommendation per migliorare il suggerimento. Dipende le caratteristiche più importanti. |    Stringa, fino a 512 caratteri
| *AllowColdItemPlacement* |    Indica se il suggerimento anche propagare elementi freddo tramite analogie caratteristica. | Valore booleano <br> Predefinito: False
| *EnableFeatureCorrelation*    | Indica se è possano utilizzare le funzionalità in motivi. | Valore booleano <br> Predefinito: False
| *ReasoningFeatureList* |  Elenco delimitato da virgole di nomi da utilizzare per ragionamento frasi, ad esempio spiegazioni recommendation. Dipende le caratteristiche più importanti per i clienti. | Stringa, fino a 512 caratteri
| *EnableU2I* | Abilitare i consigli personalizzati, detti anche utente per l'elemento consigli (U2I). | Valore booleano <br>Predefinito: vero
|*EnableModelingInsights* | Consente di definire se deve essere eseguita la valutazione offline per raccogliere informazioni dettagliate sui modellazione (metriche, precisione e diversità). Se è impostato su true, un sottoinsieme di dati non essere utilizzato per la formazione perché è necessario essere riservato per la verifica del modello. Per ulteriori informazioni su [valutazioni offline](#OfflineEvaluation). | Valore booleano <br> Predefinito: False
| *SplitterStrategy* | Se enable modellazione approfondimenti è impostato su *true*, si tratta come dividere il dati per la valutazione.  | Stringa, *RandomSplitter* o *LastEventSplitter* <br>Predefinito: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Tipo di compilazione delle rate delle imposte ###

La compilazione (delle rate delle imposte) insieme spesso acquistata esegua un'analisi che conta il numero di volte in cui due o tre diversi prodotti condivisa con la stessa. Ordina quindi set in base a una funzione analogie (**CO-occorrenze**, **Jaccard**, **sollevare**).

Pensare **Jaccard** e **sollevare** modi per normalizzare occorrenze condivisa.  Questo errore indica che gli elementi verranno restituiti solo se sono in cui è stato acquistato insieme a elemento valore iniziale.

In questo esempio telefono Lumia 650, verrà restituito telefono X solo se è stato acquistato telefono X nella stessa sessione telefono Lumia 650. Poiché potrebbe essere poco probabile, si aspetta elementi complementare 650 Lumia da restituire. ad esempio, una protezione dello schermo o un adattatore per 650 Lumia.

Attualmente, due elementi vengono considerati acquistato nella stessa sessione se si verificano in una transazione con la stessa ID utente e il timestamp.

Build delle rate delle imposte non supportano elementi freddo, poiché per definizione prevedono due elementi da acquistare la stessa transazione. Durante la compilazione delle rate delle imposte possono restituire set di elementi (ovvero), non supportano i consigli personalizzati perché accettano un elemento singolo valore iniziale come input.


#### <a name="fbt-build-parameters"></a>Parametri di compilazione delle rate delle imposte

| Nome  |   Descrizione |       Tipo  <br> valori validi, <br> (valore predefinito)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Quando avvengono il modello è. Numero di occorrenze di creazione di elementi da considerare per la modellazione. |  Numero intero, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limita il numero di elementi in un set di frequente.| Numero intero  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Punteggio minimo che deve avere un set di frequente da includere nei risultati restituiti. Maggiore migliore. | Effettuare un doppio <br> 0 e versioni successive <br> (0)
| *FbtSimilarityFunction* | Definisce la funzione analogie da utilizzare per la compilazione. **Sollevare** favorisce serendipity **CO-occorrenza** favorisce prevedibilità e **Jaccard** rappresenta un compromesso tra le due. | Stringa,  <br>  <i>cooccurrence, ascensore, jaccard</i><br> Predefinito: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Creazione di valutazione e la selezione ##

Questa guida consentono di determinare se è necessario utilizzare una build consigli o delle rate delle imposte, ma non fornisce una risposta definitiva nei casi in cui è possibile utilizzare uno di essi. Inoltre, anche se si conosce che si desidera utilizzare un tipo di compilazione delle rate delle imposte, è comunque consigliabile scegliere **Jaccard** o **sollevare** come funzione analogie.

Il modo migliore per scegliere tra due diverse generazioni è per verificarne il funzionamento di scenari reali (valutazione online) e tenere traccia di un tasso di conversione per le build di diversi. Il tasso di conversione Impossibile misurato in base alle recommendation clic, il numero effettivo acquisti da consigli illustrati o anche sugli importi di acquisto effettivo quando sono stati visualizzati suggerimenti diversi. È possibile selezionare l'unità di misura metriche tasso di conversione in base all'obiettivo aziendale.

In alcuni casi, è consigliabile valutare attentamente il modello non in linea prima pubblicarla in produzione. Durante la valutazione offline non è pensata come sostitutivo di valutazione in linea, può essere utilizzata come una metrica.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Valutazione non in linea  ##

L'obiettivo di una valutazione offline consiste nel prevedere precisione (il numero di utenti che verrà acquistato uno degli elementi consigliati) e la diversità dei suggerimenti (il numero di elementi consigliati).
Come parte della valutazione metriche precisione e diversità, il sistema trova un campione di utenti e divide le transazioni per tali utenti in due gruppi: il set di dati di formazione e il set di dati di test.

> [AZURE.NOTE]Per utilizzare metriche offline, è necessario disporre timestamp dei dati di utilizzo.
> Dati ora sono necessari dividere l'uso correttamente tra set di dati di test e formazione.

> Inoltre, valutazione offline non può produrre risultati per i file di utilizzo di piccole dimensioni. Per la valutazione eseguire un'analisi dettagliata, deve essere un minimo di 1.000 i punti di utilizzo il set di dati di test.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Precisione in k ###
Nella tabella seguente rappresenta l'output della valutazione offline precisione in k.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Percentuale |   13,75 | 18.04   | 21 |  24.31 | 26.61
|Utenti di prova |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Utenti considerati | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Utenti non considerati | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
Nella tabella precedente, *k* rappresenta il numero di suggerimenti visualizzati al cliente. La tabella è simile alla seguente: "Se durante il periodo di prova, è stata visualizzata sola indicazione ai clienti, solo 13,75 degli utenti da acquistate che il prodotto consigliato." L'istruzione si basa sul presupposto che il modello è configurato con dati di acquisto. È possibile che questa che la precisione 1 è 13,75.

Si noterà che come più elementi si riferiscono al cliente, la probabilità del cliente acquisto di un articolo consigliato aumenta. Per la prova precedente, la probabilità quasi raddoppia alla percentuale di 26.61 quando 5 elementi indicati.

#### <a name="percentage"></a>Percentuale
Viene visualizzata la percentuale di utenti che interagiscono con almeno uno dei suggerimenti *k* . La percentuale viene calcolata dividendo il numero di utenti che interagiscono con almeno un suggerimento per il numero totale di utenti considerati. Vedere utenti considerati per altre informazioni.

#### <a name="users-in-test"></a>Utenti di prova
Dati in questa riga rappresentano il numero totale di utenti nel set di dati di test.

#### <a name="users-considered"></a>Utenti considerati
Un utente viene considerato solo se il sistema consigliato almeno *k* elementi in base al modello generato utilizzando il set di dati di formazione.

#### <a name="users-not-considered"></a>Utenti non considerati
Dati in questa riga rappresentano tutti gli utenti non considerati. Gli utenti che non ha ricevuto almeno *k* consigliati elementi.

Utente non viene considerato = gli utenti di prova, gli utenti considerati

<a name="Diversity"></a>
### <a name="diversity"></a>Diversità ###
Metrica diversità misura il tipo di elementi consigliati. Nella tabella seguente rappresenta l'output della valutazione offline diversità.

|Intervallo percentile |    0-90|  90-99| 99-100
|------------------|--------|-------|---------
|Percentuale        | 34.258 | 55.127| 10.615


Totale gli elementi consigliati: 100.000

Elementi univoci consigliati: 954

#### <a name="percentile-buckets"></a>Intervalli di percentile
Ogni intervallo percentile è rappresentato da un intervallo specifico (valori minimo e massimo in un intervallo compreso tra 0 e 100). Gli elementi vicino 100 sono gli elementi più popolari e gli elementi vicino 0 sono meno comuni. Ad esempio, se il valore percentuale per l'intervallo percentile 99 100 10.6, significa che 10.6 percentuale delle raccomandazioni restituiti solo i primi in una percentuale elementi più popolari. Il valore più piccolo secchiello percentile sarà inclusivo e il valore massimo è completo, ad eccezione di 100.
#### <a name="unique-items-recommended"></a>Elementi univoci consigliati
Gli elementi univoci consigliati unità di misura metriche Mostra il numero di elementi distinti che sono stati restituiti per la valutazione.
#### <a name="total-items-recommended"></a>Numero totale di elementi consigliati
I numero totale di elementi consigliati unità di misura metriche Mostra il numero di elementi consigliati. Alcune possono essere duplicati.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Metriche di valutazione non in linea ###
Le metriche offline precisione e diversità possono essere utile quando si seleziona la compilazione da utilizzare. Parametri di compilazione durante la compilazione, come parte delle rate delle imposte o recommendation:

-   Impostare il parametro di compilazione *enableModelingInsights* su **true**.
-   Facoltativamente, selezionare *splitterStrategy* (uno dei due *RandomSplitter* o *LastEventSplitter*).
*RandomSplitter* divide i dati di utilizzo in treno e test imposta in base alla percentuale di test specificato *randomSplitterParameters* e casuale inizializzare valori.
*LastEventSplitter* divide i dati di utilizzo in treno e testare set in base all'ultima transazione per ogni utente.

Questo attiverà un build che utilizza solo un sottoinsieme dei dati per la formazione e il resto dei dati per calcolare le metriche di valutazione.  Una volta completata la compilazione, per ottenere l'output della valutazione, è necessario chiama [Get genera metriche API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), passando il rispettivo *modelId* e *buildId*.

 Di seguito è riportato l'output JSON per la valutazione di esempio.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
