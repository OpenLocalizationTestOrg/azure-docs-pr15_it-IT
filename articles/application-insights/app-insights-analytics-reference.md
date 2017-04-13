<properties 
    pageTitle="Guida di riferimento in Analitica nell'applicazione approfondimenti | Microsoft Azure" 
    description="Guida di riferimento per le istruzioni in Analitica, il potente strumento di ricerca di informazioni approfondite dell'applicazione. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Guida di riferimento per Analitica

[Analitica](app-insights-analytics.md) è la funzionalità di ricerca potenti di [Informazioni approfondite dell'applicazione](app-insights-overview.md). Queste pagine descrivono il linguaggio di query Analitica.

> [AZURE.NOTE] [Unità di test Analitica il nostro simulata dati](https://analytics.applicationinsights.io/demo) se l'app non invia dati a informazioni dettagliate sui applicazione ancora.

## <a name="index"></a>Indice


**Consentire** [consentire](#let-clause)


**Operatori e query** [CONTA. numeri](#count-operator)  |  [valutare](#evaluate-operator) | [estendere](#extend-operator) | [join](#join-operator) | [limite](#limit-operator) | [mvexpand](#mvexpand-operator) | [analizzare](#parse-operator) | [progetto](#project-operator) | [non al computer di project](#project-away-operator) | [intervallo](#range-operator) | [ridurre](#reduce-operator) | [il rendering delle direttiva](#render-directive) | [limitare la clausola](#restrict-clause) | [Ordina](#sort-operator) | [riepilogare](#summarize-operator) | [eseguire](#take-operator) | [superiore](#top-operator) | [annidate superiore](#top-nested-operator) | [unione](#union-operator) | [nel punto in cui](#where-operator) | [dove aggiuntivo](#where-in-operator)

**Aggregazioni** [qualsiasi](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [Media](#avg) | [buildschema](#buildschema) | [conteggio](#count) | [CONTA.Se](#countif) | [DB.conta.numeri](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentili](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [somma](#sum) | [varianza](#variance)

**Scalari** [Valori letterali booleani](#boolean-literals)  |  [Operatori booleani](#boolean-operators) | [cast](#casts) | [confronti scalari](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Numeri** [Operatori aritmetici](#arithmetic-operators)  |  [Valori letterali numerici](#numeric-literals) | [Ass](#abs) | [collocazione](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [casuale](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Data e ora** [Data e ora le espressioni](#date-and-time-expressions)  |  [Data e ora](#date-and-time-literals) | [fa](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [ora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Stringa** [GUID](#guids)  |  [Offuscato stringhe letterali](#obfuscated-string-literals) | [Stringhe letterali](#string-literals) | [confronti](#string-comparisons) | [countof](#countof) | [estrarre](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [sostituire](#replace) | [dividere](#split) | [strcat](#strcat) | [strlen](#strlen) | [sottostringa](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrici, oggetti e dinamiche** [Valori letterali in forma di matrice e degli oggetti](#array-and-object-literals)  |  [Funzioni oggetto dinamico](#dynamic-object-functions) | [oggetti dinamici nelle clausole consentono](#dynamic-objects-in-let-clauses) | [espressioni JSON percorso](#json-path-expressions) | [nomi](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervallo](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Consentire

### <a name="let-clause"></a>consentire clausola

**Tabulare consentire - denominazione di una tabella**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Valore scalare consentire - un valore per la denominazione**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda consentire - denominazione di una funzione**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Una clausola consentono associa un [nome](#names) a una risultato in formato tabella, un valore scalare o una funzione. La clausola è un prefisso da una query e l'ambito dell'associazione è la query. (Consentono di non consentono agli elementi nome utilizzati in un secondo momento durante la sessione.)

**Sintassi**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *tipo:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:* Query non preceduta da una clausola consentono di.

**Esempi**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Self-join:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Operatori e query

Una query sul telemetria è costituita da un riferimento a un flusso di origine, seguito da una pipeline di filtri. Per esempio:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Ogni filtro con il carattere barra verticale preceduta `|` è un'istanza di un *operatore*, con alcuni parametri. L'input all'operatore è la tabella che rappresenta il risultato della pipeline precedente. Nella maggior parte dei casi, tutti i parametri sono [espressioni scalare](#scalars) applicati alle colonne dell'input. In alcuni casi, i parametri sono i nomi delle colonne di input e in alcuni casi, il parametro è una seconda tabella. Il risultato di una query è sempre una tabella, anche se contiene solo una colonna e una riga.

Query possono contenere le interruzioni di riga singola, ma vengono terminate da una riga vuota. Possono contenere commenti tra `//` e di fine della riga.

Una query può essere preceduta da uno o più [consentire clausole](#let-clause), che definiscono scalari, tabelle o le funzioni utilizzate all'interno della query.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`viene utilizzato negli esempi di query riportata di seguito per indicare la tabella di origine o pipeline precedente.
> 

### <a name="count-operator"></a>operatore Count

Il `count` operatore restituisce il numero di record (righe) nel set di record di input.

**Sintassi**

    T | count

**Argomenti**

* *T*: I dati delle tabelle nei cui record sono da contare.

**Restituisce**

Questa funzione restituisce una tabella con un singolo record e una colonna di tipo `long`. Il valore della cella solo è il numero di record in *T*. 

**Esempio**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>valutare operatore

`evaluate`è un meccanismo di estensione che consente di algoritmi specializzati da aggiungere alla query.

`evaluate`deve essere l'ultimo operatore nella pipeline di query (ad eccezione di un possibile `render`). Non è necessario visualizzato nel corpo di una funzione.

[valutare autocluster](#evaluate-autocluster) | [valutare carrello](#evaluate-basket) | [valutare diffpatterns](#evaluate-diffpatterns) | [valutare extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>valutare autocluster

     T | evaluate autocluster()

AutoCluster Trova modelli comuni degli attributi distinti (dimensioni) dei dati e consente di ridurre i risultati della query originale (se si tratta di 100 o 100 righe k) a un numero limitato di modelli. AutoCluster sviluppato per analizzare errori (eccezioni, anomalo) ma potenzialmente lavorare su una qualsiasi serie di dati filtrati. 

**Sintassi**

    T | evaluate autocluster( arguments )

**Restituisce**

AutoCluster restituisce una serie (in genere piccola) di serie che acquisire parti dei dati con valori comuni condivisi tra più attributi distinti. Ogni modello è rappresentato da una riga nei risultati. 

Le prime due colonne sono il conteggio e la percentuale di righe dalla query originale acquisite dal modello. Le colonne rimanenti sono dalla query originale e il relativo valore è un valore specifico dalla colonna o ' *' indicare i valori delle variabili. 

Si noti che i modelli non contigui: potrebbero essere sovrapposte e non tratta in genere tutte le righe originale. Alcune righe non deve essere in qualsiasi modello.

**Suggerimenti**

* Usare `where` e `project` nel condotto di input per ridurre i dati a solo cosa si è interessati a.
* Dopo avere individuato una riga interessante, è consigliabile eseguire il drill-dentro ulteriormente aggiungendo i valori specifici per il `where` filtro.

**Argomenti (facoltativi)**

* `output=all | values | minimal` 

    Il formato dei risultati. Le colonne conteggio e la percentuale di sempre vengono visualizzate nei risultati. 

 * `all`-tutte le colonne dell'input sono output
 * `values`-esclude le colonne con solo ' *' nei risultati
 * `minimal`-Filtra anche le colonne che sono identiche per tutte le righe nella query originale. 


* `min_percent=`*doppia* (impostazione predefinita: 1)

    Copertura percentuale minima di righe generate.

    Esempio:`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int* (impostazione predefinita: 25) 

    Il numero di semi determina il numero di punti di ricerca locale iniziale dell'algoritmo. In alcuni casi, a seconda della struttura dei dati, aumentando il numero di semi aumenta il numero (o qualità) dei risultati attraverso lo spazio di ricerca avanzata in più lentamente compromesso di query. L'argomento num_seeds ha ridurne i risultati in entrambe le direzioni in modo decrescente sotto 5 otterranno miglioramenti delle prestazioni trascurabile e aumento maggiore di 50 raramente genererà altri motivi.

    Esempio:`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < doppie < 1*+ (impostazione predefinita: 0,5)

    Consente di controllare il saldo tra generica (copertura elevata) e informativi (molti valori condivisi). Size_weight crescente in genere riduce il numero di modelli e ogni modello tende per coprire una percentuale maggiore. Riduzione size_weight in genere produce più specifici criteri con più valori condivisi e dimensioni copertura percentuale. Il roba da smanettoni formula restituisce la media geometrica ponderata tra il punteggio generico normalizzato e punteggio informativo con size_weight e size_weight 1 come il peso. 

    Esempio:`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*column_name*

    Considera ogni riga di input in base al peso specificato (per impostazione predefinita ogni riga ha uno spessore di "1"), utilizzo comune di una colonna di peso è ampio campione account o il bucket/aggregazione dei dati che già incorporati in ogni riga.

    Esempio:`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>valutare carrello

     T | evaluate basket()

Carrello Trova tutti i criteri di frequenti degli attributi distinti (dimensioni) dei dati e restituirà tutti i modelli di frequenti che passato la soglia di frequenza nella query originale. Carrello sicuramente trovare tutti i modelli di frequenti nella finestra di dati, ma non necessariamente in fase di esecuzione polinomiale. La fase di esecuzione della query lineare il numero di righe, ma in alcuni casi potrebbe non essere esponenziale del numero di colonne (dimensioni). Carrello si basa sull'algoritmo Apriori originariamente sviluppato per il di data mining analisi carrello. 

**Restituisce**

Tutti i modelli visualizzati in più di una frazione specificata (impostazione predefinita 0,05) degli eventi.

**Argomenti (facoltativi)**


* `threshold=`*0.015 < doppie < 1* (impostazione predefinita: 0,05) 

    Imposta il rapporto minimo tra le righe da prendere in considerazione frequenti (modelli con riduzione della velocità non verranno restituiti).

    Esempio:`T | evaluate basket("threshold=0.02")`


* `weight_column=`*column_name*

    Considera ogni riga di input in base al peso specificato (per impostazione predefinita ogni riga ha uno spessore di "1"), utilizzo comune di una colonna di peso è ampio campione account o il bucket/aggregazione dei dati che già incorporati in ogni riga.

    Esempio: T | valutare basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (impostazione predefinita: 5)

    Imposta il numero massimo di dimensioni non correlate al carrello limitato per impostazione predefinita per ridurre la fase di esecuzione di query.


* `output=minimize` | `all` 

    Il formato dei risultati. Le colonne conteggio e la percentuale di sempre vengono visualizzate nei risultati.

 * `minimize`-esclude le colonne con solo ' *' nei risultati.
 * `all`-tutte le colonne di input vengono restituiti.




#### <a name="evaluate-diffpatterns"></a>valutare diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns contiene un confronto di due set di dati della stessa struttura e trovare modelli di attributi distinti (dimensioni) caratterizzano le differenze tra due set di dati. Diffpatterns sviluppato per analizzare errori (ad esempio, per gli errori di confronto a non errori in un determinato intervallo di tempo), ma solo potenzialmente differenze tra le due set di dati della stessa struttura. 

**Sintassi**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Restituisce**

Diffpatterns restituisce una serie (in genere piccola) di serie che acquisire diverse parti dei dati in due set (ad esempio, un motivo acquisizione gran parte delle righe nel set di dati prima e a bassa percentuale delle righe nel secondo set). Ogni modello è rappresentato da una riga nei risultati.

Le prime quattro colonne sono il conteggio e la percentuale di righe dalla query originale acquisite dal modello in ogni set, quinta colonna è la differenza (in punti percentuali assoluto) tra le due serie. Le colonne rimanenti sono dalla query originale e il relativo valore è un valore specifico dalla colonna o * indicare i valori delle variabili. 

Si noti che i modelli non sono distinti: potrebbero essere sovrapposte e non tratta in genere tutte le righe originale. Alcune righe non deve essere in qualsiasi modello.

**Suggerimenti**

* Utilizzare la posizione e project nel condotto di input per ridurre i dati a solo cosa si è interessati a.

* Dopo avere individuato una riga interessante, è consigliabile eseguire il drill-dentro ulteriormente aggiungendo i valori specifici per la posizione in cui filtrare.

**Argomenti**

* `split=`*nome di colonna* (obbligatorio)

    La colonna deve essere presente esattamente due valori. Se necessario, creare tale colonna:

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*stringa*

    Indica l'algoritmo cercare solo i modelli che hanno una percentuale superiore nel set di dati di destinazione, la destinazione deve essere uno dei due valori della colonna divisa.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < doppie < 1* (impostazione predefinita: 0,05) 

    Imposta la differenza minima motivo (rapporto) tra le due serie.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    Il formato dei risultati. Le colonne conteggio e la percentuale di sempre vengono visualizzate nei risultati. 

 * `minimize`-esclude le colonne con solo ' *' nei risultati
 * `all`-tutte le colonne dell'input sono output

* `weight_column=`*column_name*

    Considera ogni riga di input in base al peso specificato (per impostazione predefinita ogni riga ha uno spessore di "1"). Un utilizzo comune di una colonna di peso è ampio campione account o il bucket/aggregazione dei dati che già incorporati in ogni riga.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>valutare extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns viene utilizzato per arricchire una tabella con più semplici le colonne in modo dinamico estratte fuori (semi) colonne strutturate in base al tipo. Supporta al momento solo colonne json entrambi dinamiche e stringa serializzazione di jsons.


* `max_columns=`*int* (impostazione predefinita: 10) 

    Il numero di nuove colonne aggiunte è dinamico e può essere molto grande (in realtà è il numero di chiavi distinti in tutti i record json) in modo che è necessario limitare. Le nuove colonne vengono ordinate nel decrescente in base alle loro frequenza e fino a max_columns vengono aggiunti alla tabella.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*doppia* (impostazione predefinita: 10.0) 

    Un altro modo per limitare le nuove colonne, ignorando le colonne il cui frequenza è inferiore a min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool* (impostazione predefinita: VERO) 

    Se true il nome della colonna complessa verrà aggiunti come un prefisso ai nomi colonne estratto.


* `prefix_delimiter=`*stringa* (impostazione predefinita: "_") 

    Se add_prefix = true questo parametro definisce il delimitatore utilizzato per concatenare i nomi delle nuove colonne.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool* (impostazione predefinita: false) 

    Se true resteranno colonne (json) originali nella tabella di output.


* `output=query | table` 

    Il formato dei risultati. 

 * `table`-L'output è la stessa tabella come ricevuto meno le colonne inpue specificate più nuove colonne che sono stati estratti da colonne di input.
 * `query`-L'output è una stringa che rappresenta la query apportate per ottenere il risultato come tabella. 




### <a name="extend-operator"></a>estendere operatore

     T | extend duration = stopTime - startTime

Aggiungere uno o più colonne calcolate in una tabella. 


**Sintassi**

    T | extend ColumnName = Expression [, ...]

**Argomenti**

* *T:* La tabella di input.
* *ColumnName:* Il nome delle colonne da aggiungere. [Nomi](#names) tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o '_' caratteri. Usare `['...']` o `["..."]` per citare le parole chiave o i nomi con altri caratteri.
* *Espressione:* Calcolo applicati alle colonne esistenti.

**Restituisce**

Una copia della tabella di input, con le altre colonne specificate.

**Suggerimenti**

* Usare [`project`](#project-operator) se, tuttavia, se si vogliono rilasciare o rinominare alcune colonne.
* Non usare `extend` semplicemente per ottenere un nome più breve da utilizzare in un'espressione di tempo. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Le colonne native della tabella sono state indicizzate; il nuovo nome definisce una colonna aggiuntiva che non è indicizzata, in modo che la query è soggetto a eseguito più lentamente.

**Esempio**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>operatore di join

    Table1 | join (Table2) on CommonColumn

Unisce le righe di due tabelle per i valori corrispondenti della colonna specificata.


**Sintassi**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argomenti**

* *Tabella1* - il "lato sinistro' del join.
* *Tabella2* - 'destra' del join. Può essere un'espressione di query annidate che restituisce una tabella.
* *CommonColumn* - una colonna che contiene lo stesso nome in due tabelle.
* *Tipo* - specifica la modalità di far corrispondere le righe di due tabelle.

**Restituisce**

Una tabella con:

* Una colonna per ogni colonna in ognuna delle due tabelle, incluse le chiavi corrispondente. Le colonne del lato destro vengono modificate automaticamente se sono presenti conflitti di nome.
* Una riga per ogni corrispondenza tra le tabelle di input. Una corrispondenza è una riga selezionata da una tabella con lo stesso valore per tutte le `on` campi come una riga in altra tabella. 

* `Kind`non viene specificato

    Viene trovata una corrispondenza solo una riga a partire dal lato sinistro di ogni valore della `on` chiave. L'output contiene una riga per ogni corrispondenza di questa riga con le righe da destra.

* `Kind=inner`
 
     Esiste una riga nell'output per tutte le combinazioni di righe corrispondenti da sinistro e destro.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Oltre alle corrispondenze interne, è una riga per ogni riga di sinistra (e/o a destra), anche se non ha alcuna corrispondenza. In questo caso, le celle di dati non corrispondenti output contengono valori null.

* `kind=leftanti`

     Restituisce tutti i record relativi al lato sinistro che non dispongono di corrispondenze da destra. Nella tabella dei risultati include solo le colonne da sinistra. 
 
Se sono presenti più righe con gli stessi valori per tali campi, vengono fornite le righe per tutte le combinazioni.

**Suggerimenti**

Per ottenere prestazioni ottimali:

* Usare `where` e `project` per ridurre il numero di righe e colonne nelle tabelle di input, prima di `join`. 
* Se una tabella è sempre più piccole rispetto a altro, è possibile utilizzarla come (reindirizzato) lato sinistro del join.
* Le colonne per la corrispondenza di join devono avere lo stesso nome. Utilizzare l'operatore di progetto se è necessario rinominare una colonna in una delle tabelle.

**Esempio**

Ottenere sull'attività da un file di log in cui alcune voci contrassegna l'inizio e fine di un'attività. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>operatore di limite

     T | limit 5

Restituisce il numero specificato di righe dalla tabella di input. Non ci sono garanzie record che vengono restituiti. (Per restituire record specifici, utilizzare [`top`](#top-operator).)

**Alias**`take`

**Sintassi**

    T | limit NumberOfRows


**Suggerimenti**

`Take`è un modo semplice ed efficiente per visualizzare un esempio dei risultati quando si lavora in modo interattivo. Tenere presente che non garantisce per produrre tutte le righe particolare o per presentarli in un ordine specifico.

Esiste un limite implicito al numero di righe restituite al client, anche se non si usa `take`. Per sollevare questo limite, utilizzare la `notruncation` opzione richiesta client.



### <a name="mvexpand-operator"></a>operatore mvexpand

    T | mvexpand listColumn 

Espande un elenco da una cella (JSON) dinamiche digitato in modo che ogni voce è una riga separata. Tutte le altre celle in una riga espansa vengono duplicate. 

(Vedere anche [`summarize makelist`](#summarize-operator) che esegue la funzione opposta.)

**Esempio**

Si supponga la tabella di input:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Salve"|{"chiave": "valore"}|
|2|"tutti"|[0,1, "k", "v"]|

    mvexpand D

Risultato è:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Salve"|{"chiave": "valore"}|
|2|"tutti"|0|
|2|"tutti"|1|
|2|"tutti"|"k"|
|2|"tutti"|"v"|


**Sintassi**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argomenti**

* *ColumnName:* Nel risultato, matrici nella colonna specificata vengono espanse a più righe. 
* *ArrayExpression:* Un'espressione restituendo una matrice. Se questo modulo, viene aggiunta una nuova colonna e viene mantenuto uno esistente.
* *Nome:* Un nome per la nuova colonna.
* *Typename:* Viene eseguito il cast sull'espressione espansa a un tipo specifico
* *RowLimit:* Numero massimo di righe generate da ogni riga originale. Il valore predefinito è 128.

**Restituisce**

Più righe per ognuno dei valori in una matrice nella colonna specificata o nell'espressione di matrice.

La colonna espansa è sempre tipo dinamico. Utilizzare un cast, ad esempio `todatetime()` o `toint()` se si desidera calcolare o valori aggregati.

Due modalità di espansione elenco proprietà sono supportate:

* `bagexpansion=bag`: I contenitori di proprietà vengono espanse in contenitori di immissione singola proprietà. Verrà visualizzata l'espansione predefinito.
* `bagexpansion=array`: I contenitori di proprietà vengono suddivisi in due elementi `[` *chiave*`,`*valore* `]` matrice strutture, che consente l'accesso uniforme ai valori e chiavi (così come, ad esempio, che esegue un'aggregazione distinct count tramite i nomi delle proprietà). 

**Esempi**


    exceptions | take 1 
  	| mvexpand details[0]

Divide un record di eccezione in righe per ogni elemento nel campo dettagli.



### <a name="parse-operator"></a>operatore di analisi

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Estrae i valori da una stringa. Consente di corrispondenza di espressioni semplici o regolare.

**Sintassi**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argomenti**

* `T`: Tabella di input.
* `kind`: 
 * `simple`(impostazione predefinita): il `Match` le stringhe sono stringhe normali.
 * `relaxed`: se il testo non analizza come tipo di una colonna, la colonna è impostata su null e l'analisi continua 
 * `regex`: il `Match` stringhe sono espressioni regolari.
* `Text`: Una colonna o un'altra espressione che restituisce o può essere convertita in una stringa.
* *Corrispondenza:* Corrispondere alla parte successiva della stringa ed eliminarlo.
* *Colonna:* Assegnare la parte successiva della stringa a questa colonna. Se non esiste, viene creata la colonna.
* *Tipo:* Analizzare la parte successiva della stringa come tipo specificato, ad esempio int, double Data. 


**Restituisce**

Tabella di input, esteso in base all'elenco di colonne.

Gli elementi di `with` clausola vengono confrontati con il testo di origine, a sua volta. Ogni elemento chews disattivare un blocco di testo di origine: 

* Stringa letterale o espressione Riposiziona il cursore corrispondente per la lunghezza della corrispondenza.
* In un'analisi regex un'espressione regolare può utilizzare l'operatore di riduzione ', ' spostare presto alla corrispondenza seguente.
* Un nome di colonna a un tipo di analizza il testo come tipo specificato. A meno che non tipo = Media, un'operazione di analisi invalida corrispondenti l'intera serie.
* Un nome di colonna senza un tipo o con il tipo di "stringa" copia il numero minimo di caratteri per ottenere la corrispondenza seguente.
* ' *' Ignora il numero minimo di caratteri per ottenere la corrispondenza seguente. È possibile utilizzare '*' all'inizio o alla fine del modello, o successivo a un tipo diverso da stringa o tra le corrispondenze di stringhe.

Tutti gli elementi in un modello di analisi deve corrispondere correttamente. in caso contrario, non verrà creato alcun risultato. Eccezioni a questa regola sono che quando tipo = Media, se l'analisi una variabile digitata non riesce, il resto dell'analisi continua.

**Esempi**

*Semplice:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | contatore | presenta | Anno
---|---|---|---
1 | 2 | SOCKS | 63

*Relaxed:*

Quando l'input contiene una corrispondenza corretta per ogni colonna digitato, una media analisi produce gli stessi risultati di un'analisi semplice. Ma se una delle colonne digitate non analizza correttamente, una media analisi continuerà a elaborare il resto del modello, mentre si arresta e cessa generare un risultato di un'analisi semplice.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | presenta | Anno
---|---|---
1 |  SOCKS | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

risorsa | sezione | blocco | versione finale | precedente
---|---|---|---|---
Utilità di pianificazione | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>operatore di progetto

    T | project cost=price*quantity, price

Selezionare le colonne da includere, rinominare o eliminare e inserire le nuove colonne calcolate. L'ordine delle colonne nel risultato è specificato dall'ordine degli argomenti. Solo le colonne specificate negli argomenti sono incluse nel risultato: tutti gli altri nell'input vengono eliminati.  (Vedere anche `extend`.)


**Sintassi**

    T | project ColumnName [= Expression] [, ...]

**Argomenti**

* *T:* La tabella di input.
* *ColumnName:* Il nome di una colonna da visualizzare nell'output. Se non esiste alcun *espressione*, una colonna di tale nome deve essere visualizzato nell'input. [Nomi](#names) tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o '_' caratteri. Usare `['...']` o `["..."]` per citare le parole chiave o i nomi con altri caratteri.
* *Espressione:* Espressione scalare facoltativo che fanno riferimento le colonne di input. 

    Si tratta di una persona per restituire una nuova colonna calcolata con lo stesso nome di una colonna esistente di input.

**Restituisce**

Tabella che contiene colonne indicate come argomenti e uno o più righe della tabella di input.

**Esempio**

Nell'esempio seguente mostra diversi tipi di modifiche che possono essere eseguite con la `project` operatore. Tabella di input `T` ha tre colonne di tipo `int`: `A`, `B`, e `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>operatore non al computer di Project

    T | project-away column1, column2, ...

Escludere le colonne specificate. Il risultato contiene tutte le colonne inpue ad eccezione di quelli che verrà specificato un nome.

### <a name="range-operator"></a>operatore di intervallo

    range LastWeek from ago(7d) to now() step 1d

Genera una tabella a colonna singola di valori. Si noti che non dispone di una pipeline di input. 

|LastWeek|
|---|
|12/2015/05 09:10:04.627|
|09:10:04.627 2015-12-06|
|...|
|09:10:04.627 12-12-2015|



**Sintassi**

    range ColumnName from Start to Stop step Step

**Argomenti**

* *ColumnName:* Il nome della colonna singola nella tabella di output.
* *Start:* Il valore più piccolo nell'output.
* *Interrompere:* Il valore più alto da generare l'output (o un limite per il valore più alto, se *passaggio* passaggi da eseguire su questo valore).
* *Passaggio:* Differenza tra due valori consecutivi. 

Gli argomenti devono essere numeri, date o timespan valori. Sono non è possibile fare riferimento alle colonne di una tabella. (Se si desidera calcolare l'intervallo in base a una tabella di input, utilizzare la [ *funzione*di intervallo](#range), forse con l' [operatore mvexpand](#mvexpand-operator).) 

**Restituisce**

Una tabella con una singola colonna denominata *ColumnName*, il cui valore sia *avviare* *iniziare* + *passaggio*… fino alla versione *interrompere*.

**Esempio**  

```AIQL
range Steps from 1 to 8 step 3
```

Una tabella con una singola colonna denominata `Steps` il cui tipo è `long` e il cui valore sia `1`, `4`, e `7`.

**Esempio**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Una tabella di mezzanotte gli ultimi sette giorni. La funzione collocazione (piano) riduce ogni volta che all'inizio del giorno.

**Esempio**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Mostra l'utilizzo della `range` operatore può essere utilizzato per creare una piccola, ad hoc, tabella delle dimensioni che verrà utilizzata per introdurre zeri iniziali in cui i dati di origine non contengono valori.

### <a name="reduce-operator"></a>ridurre di operatore

    exceptions | reduce by outerMessage

Il tentativo di raggruppare i record analoghi. Per ogni gruppo, restituisce l'operatore di `Pattern` viene considerato meglio il gruppo e il `Count` di record di tale gruppo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintassi**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argomenti**

* *ColumnName:* La colonna da esaminare. Deve essere di tipo stringa.
* *Soglia:* Un valore nell'intervallo {0..1}. Il valore predefinito è 0,001. Per gli input di grandi dimensioni, soglia dovrebbe essere minimo. 

**Restituisce**

Due colonne, `Pattern` e `Count`. In molti casi motivo sarà un valore dalla colonna completo. In alcuni casi, è possibile identificare i termini comuni e sostituire le parti variabili con ' *'.

Ad esempio, il risultato di `reduce by city` possono includere: 

|Motivo | Conta. numeri |
|---|---|
| SAN * | 5182 |
| Saint * | 2846 |
| Mosca | 3726 |
| \*-on-\* | 2730 |
| Milano | 27163 |


### <a name="render-directive"></a>il rendering delle direttiva

    T | render [ table | timechart  | barchart | piechart ]

Rendering indica il livello di presentazione per la visualizzazione della tabella. Dovrebbe essere l'ultimo elemento di barra verticale. Si tratta di una comoda alternativa all'utilizzo di controlli sullo schermo, che consente di salvare una query con un metodo di presentazione specifico.

### <a name="restrict-clause"></a>limitare la clausola 

Specifica il set di nomi di tabella disponibili agli operatori che seguono. Per esempio:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>operatore di ordinamento 

    T | sort by country asc, price desc

Ordinare le righe della tabella di input in base a uno o più colonne.

**Alias**`order`

**Sintassi**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argomenti**

* *T:* La tabella di input per ordinare.
* *Colonna:* Colonna di *T* in base al quale ordinare. Il tipo dei valori deve essere numerico, data, ora o stringa.
* `asc`Ordinare in ordine crescente, in basso sufficientemente alto. Il valore predefinito è `desc`, decrescente in alto al più piccolo.

**Esempio**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Tutte le righe di tracce di tabella che dispone di uno specifico `ActivityId`, ordinati dal loro timestamp.

### <a name="summarize-operator"></a>riepilogare operatore

Viene prodotta una tabella che aggrega il contenuto della tabella di input.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Tabella che mostra il numero, medio corrispondente in un'altra e set di città in ogni paese. Esiste una riga nell'output per ogni paese distinti. Le colonne di output mostrano il conteggio, durata media, città e paese. Tutte le altre colonne input vengono ignorati.


    T | summarize count() by price_range=bin(price, 10.0)

Una tabella che mostra quanti elementi presenti prezzi in ogni intervallo [0,10.0], [10.0,20.0], e così via. In questo esempio è una colonna per il conteggio e uno per l'intervallo di prezzo. Tutte le altre colonne input vengono ignorati.


**Sintassi**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argomenti**

* *Colonna:* Nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione. [Nomi](#names) tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o '_' caratteri. Usare `['...']` o `["..."]` per citare le parole chiave o i nomi con altri caratteri.
* *Aggregazione:* Una chiamata a una funzione di aggregazione come `count()` o `avg()`, con i nomi delle colonne come argomenti. Vedere [le aggregazioni](#aggregations).
* *GroupExpression:* Un'espressione applicati alle colonne che fornisce un set di valori distinti. In genere è un nome di colonna che fornisce già un insieme di valori, limitato o `bin()` con una colonna numerica o temporale come argomento. 

Se si fornisce un'espressione numerica o temporale senza utilizzare `bin()`, Analitica viene applicato automaticamente con un intervallo di `1h` per ore, o `1.0` per i numeri.

Se non si fornisce un *GroupExpression,* l'intera tabella di riepilogo in una riga di output singola.



**Restituisce**

Le righe di input sono organizzate in gruppi con gli stessi valori di `by` espressioni. Quindi vengono calcolate le funzioni di aggregazione specificato sopra ogni gruppo, la creazione di una riga per ogni gruppo. Il risultato contiene il `by` anche almeno una colonna per ogni e colonne calcolate aggregazione. (Alcune funzioni di aggregazione restituiscono più colonne).

Il risultato sarà il numero di righe distinte combinazioni di `by` valori. Se si desidera riepilogare su intervalli di valori numerici, utilizzare `bin()` per ridurre gli intervalli di valori distinti.

**Nota**

Anche se è possibile fornire le espressioni non autorizzate per l'aggregazione le espressioni di raggruppamento, è preferibile usare nomi di colonna semplice o applicare `bin()` a una colonna numerica.



### <a name="take-operator"></a>richiedere agli operatori

Alias del [limite](#limit-operator)


### <a name="top-operator"></a>operatore Top

    T | top 5 by Name desc nulls first

Restituisce i primi *N* record ordinato in base alle colonne specificate.


**Sintassi**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argomenti**

* *NumberOfRows:* Il numero di righe di *T* per restituire.
* *Sort_expression:* Un'espressione in base al quale ordinare le righe. Indica in genere solo un nome di colonna. È possibile specificare più sort_expression.
* `asc`o `desc` (impostazione predefinita) possono essere visualizzati per controllare se selezione è effettivamente dal "fine" o "inizio" nell'intervallo.
* `nulls first`o `nulls last` controlli nel punto in cui vengono visualizzati i valori null. `First`il valore predefinito per `asc`, `last` è il valore predefinito per `desc`.


**Suggerimenti**

`top 5 by name`in apparenza corrisponde al `sort by name | take 5`. Tuttavia, viene eseguito più velocemente e sempre restituisce ordinato risultati ottimali, mentre `take` non garantisce tali.

### <a name="top-nested-operator"></a>operatore annidate superiore

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Restituisce risultati gerarchici, in cui ogni livello è un drill-down dal livello precedente. È utile per rispondere a domande sembrare "quali sono le richieste di 5 superiore e per ognuna di esse, quali sono intervalli superiore 3 le prestazioni e per ognuna di esse, sono i paesi primi 3 le richieste provenienti da?"

**Sintassi**

   T | annidate Top N della colonna in base aggregazione [,...]

**Argomenti**

* N:int - numero di righe da restituire o passare a livello avanzato. In una query con tre livelli dove N è 5, 3 e 3, il numero totale di righe sarà 45.
* COLONNA - una colonna di raggruppamento per l'aggregazione. 
* AGGREGAZIONE - una [funzione di aggregazione](#aggregations) da applicare a ogni gruppo di righe. I risultati di questi aggregazioni determinerà i principali gruppi da visualizzare.


### <a name="union-operator"></a>operatore di unione

     Table1 | union Table2, Table3

Accetta due o più tabelle e vengono restituite le righe di tutti gli elementi. 

**Sintassi**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argomenti**

* *Tabella1*, *Tabella2* ...
 *  Il nome di una tabella, ad esempio `requests`, o una tabella definita in una [clausola consentire](#let-clause); o
 *  Una query espressione, ad esempio`(requests | where success=="True")`
 *  Un set di tabelle specificate con un carattere jolly. Ad esempio `e*` formano l'unione di tutte le tabelle definite in clausole consentono precedenti il cui nome inizia con "e", insieme a tabella 'eccezioni'.
* `kind`: 
 * `inner`-Il risultato sarà il subset di colonne comuni a tutte le tabelle di input.
 * `outer`-Il risultato con tutte le colonne che si verificano in uno qualsiasi di input. Le celle che non sono state definite da una riga di input sono impostate su `null`.
* `withsource=`*ColumnName:* Se specificato, l'output includerà una colonna denominata *ColumnName* il cui valore indica la tabella di origine ha contribuito ogni riga.

**Restituisce**

Una tabella con il numero di righe quanti sono in tutte le tabelle di input e il numero di colonne quanti è i nomi di colonna univoca in input.

**Esempio**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Unione di tutte le tabelle i cui nomi iniziano "tt".


**Esempio**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Il numero di utenti distinti che hanno prodotto in una `exceptions` evento o una `traces` evento sopra il giorno precedente. Nel risultato, nella colonna 'SourceTable' indica "Query" o "Comando".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Questa versione più efficiente lo stesso risultato. Filtra ogni tabella prima di creare l'unione.

### <a name="where-operator"></a>in operatore

     requests | where resultCode==200

Filtra una tabella a un sottoinsieme di righe che soddisfano un predicato.

**Alias**`filter`

**Sintassi**

    T | where Predicate

**Argomenti**

* *T:* Input tabulare cui record devono essere filtrati.
* *Predicati:* A `boolean` [espressione](#boolean) sopra le colonne di *T*. Viene valutata per ogni riga di *T*.

**Restituisce**

Le righe di *T* per il quale *predicati* risulta `true`.

**Suggerimenti**

Per ottenere prestazioni migliori:

* **Confronto di semplice utilizzo** tra i nomi di colonna e costanti. ('Costante' significa costante sulla tabella, in modo `now()` e `ago()` sono corretti, e pertanto sono scalari assegnati valori utilizzando un [ `let` clausola](#let-clause).)

    Ad esempio, si preferisce `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Prima di condizioni di Simplest**: se si dispone di più clausole di congiunzione con `and`, inserire innanzitutto le clausole che includono un'unica colonna. In modo `Timestamp > ago(1d) and OpId == EventId` migliore viceversa.


**Esempio**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Record che non risalgono a 1 ora e dal sito di origine denominata "Kuskus" e due colonne dello stesso valore di. 

Si noti che vengono inseriti il confronto tra due colonne ultima, perché non è possibile utilizzare l'indice e impone un'analisi.


### <a name="where-in-operator"></a>operatore di WHERE

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Sintassi**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Argomenti**

* `col`: Una colonna nella tabella.
* `expr1`...: Un elenco di espressioni scalare.

Usare `in` viene utilizzato per includere solo le righe in cui `col` è uguale a una delle espressioni `expr1...`.

Usare `!in` da includere solo le righe in cui `col` non è uguale a una o più espressioni `expr1...`.  


## <a name="aggregations"></a>Aggregazioni

Aggregazioni vengono utilizzate per combinare valori nei gruppi creati in [riepilogare operazione](#summarize-operator). In questa query, ad esempio dcount() è una funzione di aggregazione:

    requests | summarize dcount(name) by success

### <a name="any"></a>qualsiasi 

    any(Expression)

In modo casuale seleziona un'unica riga del gruppo e restituisce il valore dell'espressione specificata.

È utile, ad esempio, quando alcuni colonna contiene un numero elevato di valori simili (ad esempio, una colonna "testo di errore") e si desidera campionare tale colonna una volta per un valore univoco di chiave composta gruppo. 

**Esempio**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Trova una riga nel gruppo viene ridotto a icona/massima *ExprToMaximize*e restituisce il valore di *ExprToReturn* (o `*` per restituire l'intera riga).

**Suggerimento**: le colonne passato tramite viene modificate automaticamente. Per assicurarsi che si sta utilizzando i nomi di destro, esaminare i risultati tramite `take 5` prima inviare i risultati in un altro operatore.

**Esempi**

Per ogni nome richiesta Mostra quando si è verificato la richiesta più lunga:

    requests | summarize argmax(duration, timestamp) by name

Mostra tutti i dettagli della richiesta più lunga, non solo il timestamp:

    requests | summarize argmax(duration, *) by name


Trovare il valore minimo di ogni unità di misura metriche, insieme all'ora e altri dati:

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>Media

    avg(Expression)

Calcola la media di *espressione* tra il gruppo.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Restituisce uno schema minimo che consente l'accesso a tutti i valori di *DynamicExpression*. 

Il tipo di colonna parametro deve essere `dynamic` -un elenco di proprietà o in forma di matrice. 

**Esempio**

    exceptions | summarize buildschema(details)

Risultato:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Si noti che `indexer` viene utilizzato per contrassegnare nel punto in cui è consigliabile utilizzare un indice numerico. Per questo schema, alcuni percorsi validi da essere (presupponendo che tali indici di esempio presenti nell'intervallo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Esempio**

Si supponga che la colonna di input sono previsti tre valori dinamici:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Lo schema risultante sarà:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Lo schema indica che:

* L'oggetto radice è un contenitore con quattro proprietà denominato x, y, z e t.
* La proprietà denominata "x" che potrebbero essere di tipo "int" o di tipo "stringa".
* La proprietà "y" che potrebbero di uno di tipo "double" o un altro contenitore con una proprietà chiamato "w" di tipo "stringa".
* Il ``indexer`` parola chiave indica che "z" e "t" sono matrici.
* Ogni elemento della matrice "z" è un valore int o una stringa.
* "t" è una matrice di stringhe.
* Ogni proprietà è facoltativa in modo implicito e qualsiasi matrice può essere vuota.

##### <a name="schema-model"></a>Modello di schema

La sintassi dello schema restituito è:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Sono equivalenti a un sottoinsieme delle annotazioni di tipo macchina codificata come un valore dinamico. Macchina, dovrebbero essere lo schema di esempio:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>Conta. numeri

    count([ Predicate ])

Restituisce il numero di righe per il quale *predicati* restituiscono `true`. Se viene specificato alcun *predicati* , restituisce il numero totale di record nel gruppo. 

**Suggerimento delle prestazioni**: usare `summarize count(filter)` anziché`where filter | summarize count()`

> [AZURE.NOTE] Evitare l'utilizzo di Count () per calcolare il numero di richieste, eccezioni o altri eventi che si sono verificati. Quando [campioni](app-insights-sampling.md) è in esecuzione, il numero di punti dati conservate nell'applicazione approfondimenti sarà inferiore al numero di eventi originali. Utilizzare invece `summarize sum(itemCount)...`. La proprietà itemCount corrisponde al numero di eventi originali che sono rappresentati da ogni punto dati mantenuti.

### <a name="countif"></a>Conta.Se

    countif(Predicate)

Restituisce il numero di righe per il quale *predicati* restituiscono `true`.

**Suggerimento delle prestazioni**: usare `summarize countif(filter)` anziché`where filter | summarize count()`

> [AZURE.NOTE] Evitare di utilizzare CONTA.Se per calcolare il numero di richieste, eccezioni o altri eventi che si sono verificati. Quando [campioni](app-insights-sampling.md) è in esecuzione, il numero di punti dati saranno inferiore al numero di eventi effettivi. Utilizzare invece `summarize sum(itemCount)...`. La proprietà itemCount corrisponde al numero di eventi originali che sono rappresentati da ogni punto dati mantenuti.

### <a name="dcount"></a>DCount

    dcount( Expression [ ,  Accuracy ])

Restituisce una stima del numero di valori distinti di *espr* nel gruppo. (Per elencare i valori distinti, utilizzare [`makeset`](#makeset).)

*Precisione*, se specificato, controlli equilibrio tra velocità e l'accuratezza.

 * `0`= calcolo almeno accurata e veloce.
 * `1`l'impostazione predefinita, che consente di distribuire volta accuratezza e calcolo. informazioni sull'errore 0,8%.
 * `2`= più accurato e più lenta calcolo; informazioni sull'errore 0,4%.

**Esempio**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Restituisce una stima del numero di valori distinti di *espr* di righe nel gruppo per cui *predicati* è vera. (Per elencare i valori distinti, utilizzare [`makeset`](#makeset).)

*Precisione*, se specificato, controlli equilibrio tra velocità e l'accuratezza.

 * `0`= calcolo almeno accurata e veloce.
 * `1`l'impostazione predefinita, che consente di distribuire volta accuratezza e calcolo. informazioni sull'errore 0,8%.
 * `2`= più accurato e più lenta calcolo; informazioni sull'errore 0,4%.

**Esempio**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makeList

    makelist(Expr [ ,  MaxListSize ] )

Restituisce un `dynamic` in forma di matrice (JSON) di tutti i valori di *espr* nel gruppo. 

* *MaxListSize* è un limite di integer facoltativo al numero massimo di elementi restituiti (impostazione predefinita è *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Restituisce un `dynamic` in forma di matrice (JSON) del set di valori distinti che *espr* accetta nel gruppo. (Suggerimento: per contare solo i valori distinti, utilizzare [`dcount`](#dcount).)
  
*  *MaxSetSize* è un limite di integer facoltativo al numero massimo di elementi restituiti (impostazione predefinita è *128*).

**Esempio**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Vedere anche la [ `mvexpand` operatore](#mvexpand-operator) per la funzione opposta.


### <a name="max-min"></a>le funzioni min, Max

    max(Expr)

Calcola il numero massimo di *espr*.
    
    min(Expr)

Calcola il valore minimo di *espr*.

**Suggerimento**: questo offre la min o max sul proprio - ad esempio, la massima o prezzo più basso. Ma se si vuole che altre colonne nella riga - ad esempio, il nome del fornitore con il prezzo più basso - usare [argmin o argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, i percentili, percentilew, percentilesw

    percentile(Expression, Percentile)

Restituisce una stima per *espressione* il percentile specificato nel gruppo. La precisione dipende la densità della popolazione nell'area il percentile.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Ad esempio `percentile()`, ma viene calcolato un numero di valori del percentile (più velocemente che non calcolo singolarmente ogni percentile).

    percentilew(Expression, WeightExpression, Percentile)

Media ponderata percentile. Utilizzare questa opzione di dati.  `WeightExpression`è un numero intero che indica il numero di righe originale è rappresentato da ogni riga aggregato.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Ad esempio `percentilew()`, ma viene calcolato un numero di valori del percentile.

**Esempi**


Il valore di `duration` maggiore di 95% del campione impostato e minore di 5% del set di esempio, calcolato per ogni nome richiesta:

    request 
  	| summarize percentile(duration, 95)
      by name

Omettere "per..." per il calcolo per l'intera tabella.

Calcolare contemporaneamente i percentili diverse per i nomi dei diversi richiesta:

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

I risultati mostrano che per /Events/Index richiesta, 5% di richieste di tipo riceveranno una risposta in meno di 2.44s metà in 3.52s, e 5% è inferiore a 6.85s.

Calcolare statistiche più:

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Percentili Media ponderati

Utilizzare le funzioni Media ponderata percentile nei casi in cui i dati sono stati pre-aggregati. 

Si supponga ad esempio, l'app esegue migliaia di operazioni al secondo e si vuole conoscere la relativa latenza. Soluzione semplice, è possibile generare una richiesta di informazioni dettagliate sui applicazione o un evento personalizzato per ogni operazione. Questo creare molte il traffico, anche se campioni adattata diventeranno applicabili per ridurlo. Ma si decide di implementare la soluzione migliore: scrivere codice nell'app per aggregare i dati prima di inviarlo a informazioni dettagliate sui applicazione. Riepilogo aggregato verrà inviato a intervalli regolari, ridurre la velocità dati forse ai punti qualche minuto.

Il codice accetta un flusso di unità di misura latenza in millisecondi. Per esempio:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Viene contato sono indicate le misure di bin seguenti:`{ 10, 20, 30, 40, 50, 100 }`

Periodicamente, rende una serie di chiamate TrackEvent, uno per ogni intervallo, con misura personalizzata in ciascuna chiamata: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

In Analitica, viene visualizzato un tale gruppo di eventi alla seguente:

`opCount` | `latency`| significato
---|---|---
8 | 10 | = 8 operazioni nella classe 10 ms
6 | 20 | = 6 operazioni nella classe 20 ms
3 | 30 | = 3 operazioni nella classe 30 ms
1 | 40 | = operazioni 1 nella classe 40ms

Per ottenere un quadro preciso della distribuzione originale della latenza di evento, serve `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

I risultati sono gli stessi come se usato normale `percentiles` su un insieme di unità di misura originale.

> [AZURE.NOTE] I percentili ponderati non sono applicabili [dati campionati](app-insights-sampling.md), in cui ogni riga campionato rappresenta un campione di righe originale, anziché una classe. Le funzioni di normale percentile sono appropriate per dati campionati.

#### <a name="estimation-error-in-percentiles"></a>Errore di stima in percentili

Aggrega i percentili fornisce un valore approssimativo mediante [T classificata](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Alcuni punti importanti: 

* I limiti dell'errore stima variano in base al valore percentile richiesto. L'accuratezza migliore è alla fine delle [0..100] scalare, è necessario percentili 0 e 100 sono l'esatta valori minimo e massimo della distribuzione. L'accuratezza diminuisce verso il centro della scala. Si tratta di peggiore nella mediana, è limitata 1%. 
* Limiti di errore osservano su priorità, non sul valore. Si supponga percentile (X, 50) restituito valore XML. La stima garantisce che almeno 49% e al massimo 51% dei valori di X sono inferiori XML. Nessun limite teorico è la differenza tra XML ed effettivo valore mediano di X.

### <a name="stdev"></a>funzione StDev

     stdev(Expr)

Restituisce la deviazione standard di *espr* sopra il gruppo.

### <a name="variance"></a>Varianza

    variance(Expr)

Restituisce la varianza di *espr* sopra il gruppo.

### <a name="sum"></a>somma

    sum(Expr)

Restituisce la somma di *espr* sopra il gruppo.                      


## <a name="scalars"></a>Scalari

[cast](#casts) | [confronti](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

I tipi supportati sono:

| Tipo      | Altri nomi   | Tipo equivalente di .NET |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Cast

È possibile eseguire il cast da un tipo a altro. In generale, se la conversione senso, funziona:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Verificare se una stringa può essere convertita in un tipo specifico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Confronti scalari

||
---|---
`<` |Meno
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non è uguale al
`!=`|Non è uguale al 
`in`| Operando di destra è una matrice (dinamica) e operando sinistro è uguale a uno dei relativi elementi.
`!in`| Operando di destra è una matrice (dinamica) e operando sinistro non è uguale a uno dei relativi elementi.




### <a name="gettype"></a>GetType

**Restituisce**

Stringa che rappresenta il tipo di spazio di archiviazione sottostante dell'argomento singola. Questa operazione è particolarmente utile quando si dispone dei valori di tipo `dynamic`: in questo caso `gettype()` implicano la modalità di codifica di un valore.

**Esempi**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hash

**Sintassi**

    hash(source [, mod])

**Argomenti**

* *origine*: origine scalare in cui viene calcolato il valore hash.
* *resto*: il modulo valore da applicare al risultato.

**Restituisce**

Xxhash (long) valore scalare specificato, modulo mod valore specificato (se specificato).

**Esempi**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

Il `iff()` funzione restituisce il primo argomento (predicato) e restituisce il valore di argomenti il secondo o terzo a seconda che sia il predicato `true` o `false`. Il secondo e terzo argomento deve essere dello stesso tipo.

**Sintassi**

    iff(predicate, ifTrue, ifFalse)


**Argomenti**

* *predicato:* Un'espressione che restituisce un `boolean` valore.
* *ifTrue:* Un'espressione tramite cui viene valutata e il relativo valore restituito dalla funzione *Se restituisce* `true`.
* *ifFalse:* Un'espressione tramite cui viene valutata e il relativo valore restituito dalla funzione *Se restituisce* `false`.

**Restituisce**

Questa funzione restituisce il valore di *ifTrue* se *restituisce* `true`, o il valore di *ifFalse* in caso contrario.

**Esempio**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, notnull

    isnull(parsejson("")) == true

Accetta un singolo argomento e indica se è null.

**Sintassi**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Restituisce**

True o false a seconda se il valore è null o non null.


|x|IsNull(x)
|---|---
| "" | FALSO
|"x" | FALSO
|parsejson("")|vero
|parsejson("[]")|FALSO
|parsejson("{}")|FALSO

**Esempio**

    T | where isnotnull(PossiblyNull) | count

Si noti che ci sono altri modi per ottenere questo effetto:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Viene valutata un'espressione o una query e restituisce il risultato come un singolo valore. Questa funzione è utile per i calcoli a fasi; ad esempio, calcolare un totale di eventi e significa che usa una previsione.

**Sintassi**

    toscalar(query)
    toscalar(scalar)

**Restituisce**

L'argomento valutato. Se l'argomento è una tabella, viene restituita la prima colonna della prima riga. (Buona norma consiste nel disporre che l'argomento è solo una colonna e riga).

**Esempio**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Valori letterali booleani

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Operatori booleani

    and 
    or 

    

## <a name="numbers"></a>Numeri

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Valori letterali numerici

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Operatori aritmetici

|| |
|---|-------------|
| + | Aggiungere         |
| - | Sottrarre    |
| * | Moltiplicare    |
| / | Dividere      |
| % | Modulo      |
||
|`<` |Meno
|`<=`|Minore o uguale a
|`>` |Maggiore
|`>=`|Maggiore o uguale a
|`<>`|Non è uguale al
|`!=`|Non è uguale al 


### <a name="abs"></a>Ass

**Sintassi**

    abs(x)

**Argomenti**

* x - un numero intero, reale o timespan

**Restituisce**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>collocazione, floor

Arrotondato per difetto a un numero intero più di una dimensione specificata. Usato molto nel [`summarize by`](#summarize-operator) query. Se si dispone di una serie di valori sparsi, essi verranno raggruppati in una piccola serie di valori specifici.

Alias `floor`.

**Sintassi**

     bin(value, roundTo)
     floor(value, roundTo)

**Argomenti**

* *valore:* Numero, data o timespan. 
* *roundTo:* "Dimensione bin". Numero, data o timespan che divide *valore*. 

**Restituisce**

Al multiplo più vicino di *roundTo* sotto *valore*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Esempi**

Espressione | Risultato
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


Nell'espressione seguente viene calcolato un istogramma di durate, con dimensioni dell'intervallo di 1 secondo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>Arrotonda

Un alias per [`bin()`](#bin).

### <a name="gamma"></a>gamma

La [funzione gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Sintassi**

    gamma(x)

**Argomenti**

* *x:* Un numero reale

Per numeri interi positivi `gamma(x) == (x-1)!` , ad esempio, `gamma(5) == 4 * 3 * 2 * 1`.

Vedere anche [loggamma](#loggamma).


### <a name="log"></a>log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`deve essere un numero reale > 0. In caso contrario, viene restituito null.

### <a name="loggamma"></a>loggamma


Logaritmo naturale del valore assoluto della [funzione gamma](#gamma).

**Sintassi**

    loggamma(x)

**Argomenti**

* *x:* Un numero reale


### <a name="rand"></a>casuale

Un generatore di numeri casuali.

* `rand()`-un numero reale tra 0,0 e 1.0
* `rand(n)`-un numero intero compreso tra 0 e n-1




### <a name="sqrt"></a>RADQ

Funzione radice quadrata.  

**Sintassi**

    sqrt(x)

**Argomenti**

* *x:* Un numero reale > = 0.

**Restituisce**

* Un numero positivo in modo che`sqrt(x) * sqrt(x) == x`
* `null`Se l'argomento è negativo o non può essere convertito in un `real` valore. 




### <a name="toint"></a>ToInt

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>ToLong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>ToDouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Data e ora


[fa](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [ora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Valori letterali data e ora

|||
---|---
**DateTime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Ora è sempre in formato UTC. Se si omette la data specifica un'ora oggi.
`now()`|L'ora corrente.
`now(`-*TimeSpan*`)`|`now()-`*TimeSpan*
`ago(`*TimeSpan*`)`|`now()-`*TimeSpan*
**TimeSpan**|
`2d`|2 giorni
`1.5h`|1,5 ore 
`30m`|30 minuti
`10s`|10 secondi
`0.1s`|secondo 0,1
`100ms`| 100 millisecondi
`10microsecond`|
`1tick`|100ns
`time("15 seconds")`|
`time("2")`| 2 giorni
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Espressioni di data e ora

Espressione |Risultato
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Meno
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non è uguale al
`!=`|Non è uguale al 




### <a name="ago"></a>fa

Sottrae il periodo di tempo specificato dall'ora UTC del corrente. Ad esempio `now()`, questa funzione può essere utilizzata più volte in un'istruzione e l'ora UTC viene fatto riferimento saranno gli stessi per tutte le istanze.

**Sintassi**

    ago(a_timespan)

**Argomenti**

* *a_timespan*: intervallo da cui sottrarre l'ora UTC corrente (`now()`).

**Restituisce**

    now() - a_timespan

**Esempio**

Tutte le righe con un timestamp nell'ultima ora:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Estrae una parte specifica di una data come numero intero.

**Sintassi**

    datepart(part, datetime)

**Argomenti**

* `part:String`-{"Year", "Mese", "Giorno", "ora", "Minuto", "Secondo", "Millisecondi", "Microsecondo", "Nanosecondi"}
* `datetime`

**Restituisce**

Valore Long che rappresenta la parte specificata.


### <a name="dayofmonth"></a>DayOfMonth

    dayofmonth(datetime("2016-05-15")) == 15 

Numero ordinale del giorno del mese.

**Sintassi**

    dayofmonth(a_date)

**Argomenti**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>DayOfWeek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Numero intero di giorni che vanno dal precedente domenica, come un `timespan`.

**Sintassi**

    dayofweek(a_date)

**Argomenti**

* `a_date`: A `datetime`.

**Restituisce**

Il `timespan` poiché mezzanotte all'inizio dell'oggetto precedente domenica, arrotondato per difetto al numero intero di giorni che vanno.

**Esempi**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DayOfYear

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Numero ordinale del giorno dell'anno.

**Sintassi**

    dayofyear(a_date)

**Argomenti**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Ottenere il numero del mese (1-12) da un valore datetime.

**Esempio**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getYear

Ottenere l'anno da un valore datetime.

**Esempio**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>a questo punto

    now()
    now(-2d)

Ora UTC corrente, sfalsato facoltativamente un intervallo di tempo specificato. Questa funzione può essere utilizzata più volte in un'istruzione e l'orario di cui viene fatto riferimento saranno gli stessi per tutte le istanze.

**Sintassi**

    now([offset])

**Argomenti**

* *offset:* A `timespan`, aggiunto all'ora UTC corrente. Predefinito: 0.

**Restituisce**

Ora UTC corrente come un `datetime`.

    now() + offset

**Esempio**

Determina l'intervallo dopo l'evento identificato dal predicato:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Verificare se una stringa è una data valida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Il risultato dell'intero rappresenta il numero della settimana da ISO 8601 standard. Il primo giorno della settimana è domenica, e la prima settimana dell'anno è settimana che contiene il primo giovedì dell'anno. (Negli ultimi giorni di un anno pertanto possono contenere alcuni dei giorni della settimana 1 dell'anno successivo, o i primi giorni possono contenere alcune della settimana 52 o 53 dell'anno precedente).


## <a name="string"></a>Stringa

[countof](#countof) | [estrarre](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [sostituire](#replace) | [dividere](#split) | [strcat](#strcat) | [strlen](#strlen) | [sottostringa](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Stringhe letterali

Le regole sono gli stessi visualizzati nei JavaScript.

Stringhe possono essere racchiusa in singolo o virgolette doppie. 

Barra rovesciata (`\`) viene utilizzato per come carattere di escape `\t` (scheda), `\n` (nuova riga) e le istanze di inclusione di virgolette.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Offuscato stringhe letterali

Offuscato stringhe letterali sono stringhe che Analitica verrà coprano in caso di output la stringa (ad esempio, quando si individua). Il processo di offuscamento sostituisce offuscati tutti i caratteri da un avvio (`*`) caratteri.

Per creare una stringa offuscata letterale, anteporre `h` o "H". Per esempio:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Confronti di stringhe

Operatore|Descrizione|Maiuscole/minuscole|Esempio true
---|---|---|---
`==`|È uguale a |Sì| `"aBc" == "aBc"`
`<>` `!=`|Non è uguale al|Sì| `"abc" <> "ABC"`
`=~`|È uguale a |No| `"abc" =~ "ABC"`
`!~`|Non è uguale al |No| `"aBc" !~ "xyz"`
`has`|Right parte (RHS) è un intero termine in sinistra parte (LHS)|No| `"North America" has "america"`
`!has`|RHS non è un termine completo in LHS|No|`"North America" !has "amer"` 
`hasprefix`|RHS è il prefisso di un termine in LHS|No|`"North America" hasprefix "ame"`
`!hasprefix`|RHS non è un prefisso dei termini in LHS|No|`"North America" !hasprefix "mer"`
`hassuffix`|RHS è un suffisso di un termine in LHS|No|`"North America" hassuffix "rth"`
`!hassuffix`|RHS non è un suffisso dei termini in LHS|No|`"North America" !hassuffix "mer"`
`contains` | RHS si presenta come una sottostringa LHS|No| `"FabriKam" contains "BRik"`
`!contains`| RHS non è presente in LHS|No| `"Fabrikam" !contains "xyz"`
`containscs` | RHS si presenta come una sottostringa LHS|Sì| `"FabriKam" contains "Kam"`
`!containscs`| RHS non è presente in LHS|Sì| `"Fabrikam" !contains "Kam"`
`startswith`|RHS è una sottostringa iniziale di LHS.|No|`"Fabrikam" startswith "fab"`
`!startswith`|RHS non è una sottostringa iniziale di LHS.|No|`"Fabrikam" !startswith "abr"`
`endswith`|RHS è una sottostringa terminal di LHS.|No|`"Fabrikam" endswith "kam"`
`!endswith`|RHS non è una sottostringa terminal LHS.|No|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contiene una corrispondenza per RHS|Sì| `"Fabrikam" matches regex "b.*k"`
`in`|Uguale a uno degli elementi|Sì|`"abc" in ("123", "345", "abc")`
`!in`|Non è uguale a uno degli elementi|Sì|`"bc" !in ("123", "345", "abc")`

Usare `has` o `in` se si sta verificando la presenza di un termine interamente lessicale -, ovvero un simbolo o una parola alfanumerica delimitato da caratteri non alfanumerici o inizio o alla fine del campo. `has`Consente di eseguire più veloce `contains`, `startswith` o `endswith`. Prima di queste query viene eseguito più velocemente:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta le occorrenze di una sottostringa in una stringa. Stringa semplice corrispondenze si sovrappongono; non Regex corrispondenze.

**Sintassi**

    countof(text, search [, kind])

**Argomenti**

* *testo:* Stringa.
* *ricerca:* Stringa semplice o espressioni regolari in modo che corrisponda all'interno di *testo*.
* *tipo:* `"normal"|"regex"` Predefinito `normal`. 

**Restituisce**

Il numero di volte in cui la stringa di ricerca può essere individuata nel contenitore. Stringa semplice corrispondenze si sovrappongono; non Regex corrispondenze.

**Esempi**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (non 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>estrarre

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

È possibile ottenere una corrispondenza per un' [espressione](#regular-expressions) da una stringa di testo. Facoltativamente, converte quindi sottostringa estratta al tipo indicato.

**Sintassi**

    extract(regex, captureGroup, text [, typeLiteral])

**Argomenti**

* *regex:* Un' [espressione](#regular-expressions).
* *captureGroup:* Un numero positivo `int` costante che indica il gruppo di acquisizione per estrarre. 0 indica l'intera corrispondenza, 1 per il valore corrispondono al primo '(' parentesi')' nell'espressione regolare, 2 o più per successive tra parentesi.
* *testo:* A `string` la ricerca.
* *typeLiteral:* Un valore letterale facoltativo digitare (ad esempio, `typeof(long)`). Se, sottostringa estratta viene convertita in questo tipo. 

**Restituisce**

Se *regex* trovata una corrispondenza nel *testo*: sottostringa corrispondente contro di gruppo di acquisizione indicato *captureGroup*, se lo si desidera convertire in *typeLiteral*.

Se non vi è alcuna corrispondenza o la conversione di tipo non riesce: `null`. 

**Esempi**

La stringa di esempio `Trace` viene eseguita la ricerca per una definizione per `Duration`. La corrispondenza viene convertita in `real`, quindi moltiplicato per una costante di tempo (`1s`) in modo che `Duration` è di tipo `timespan`. In questo esempio è uguale a 123.45 secondi:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

In questo esempio corrisponde al contenuto `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notempty

    isempty("") == true

True se l'argomento è una stringa vuota o null.
Vedere anche [isnull](#isnull).


**Sintassi**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Restituisce**

Indica se l'argomento è una stringa vuota o isnull.

|x|isEmpty(x)
|---|---
| "" | vero
|"x" | FALSO
|parsejson("")|vero
|parsejson("[]")|FALSO
|parsejson("{}")|FALSO


**Esempio**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>ParseURL

Dividere un URL in sue parti.

**Sintassi**

    parseurl(urlstring)

**Argomenti**

* *urlstring:* UN URL.

**Restituisce**

Oggetto contenente le parti come stringhe.

**Esempio**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>Sostituisci

Sostituire tutte le corrispondenze regex con un'altra stringa.

**Sintassi**

    replace(regex, rewrite, text)

**Argomenti**

* *regex:* [Espressioni regolari](https://github.com/google/re2/wiki/Syntax) per cercare *testo*. Può contenere gruppi di acquisizione '('tra parentesi')'. 
* *riscrittura:* Regex sostituzione delle voci corrispondenti eseguiti da *matchingRegex*. Usare `\0` per fare riferimento a Confronta intero, `\1` per il primo gruppo di acquisizione, `\2` e così via per i gruppi di acquisizione successivi.
* *testo:* Stringa.

**Restituisce**

*testo* dopo la sostituzione di tutte le corrispondenze di *regex* con le valutazioni di *riscrittura*. Corrispondenze non si sovrappongono.

**Esempio**

Istruzione:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Sono previsti i risultati seguenti:

| x    | str | sostituito|
|---|---|---|
| 1    | Numero è 1.000000  | Numero di stato: 1.000000|
| 2    | Numero è 2.000000  | Numero di stato: 2.000000|
| 3    | Numero è 3.000000  | Numero di stato: 3.000000|
| 4    | Numero è 4.000000  | Numero di stato: 4.000000|
| 5    | Numero è 5,000000  | Numero di stato: 5,000000|
 



### <a name="split"></a>dividere

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide la stringa specificata in base a un delimitatore specificato e restituisce una matrice di stringhe con sottostringhe contenute. Facoltativamente, può essere una sottostringa specifica restituito se esiste.

**Sintassi**

    split(source, delimiter [, requestedIndex])

**Argomenti**

* *origine*: la stringa di origine che verrà divisa in base al delimitatore specificato.
* *delimitatore*: il delimitatore utilizzato per dividere la stringa di origine.
* *requestedIndex*: un indice in base zero facoltativo `int`. Se, in forma di matrice la stringa restituita conterrà sottostringa richiesta se è presente. 

**Restituisce**

Matrice di stringhe che contiene le sottostringhe della stringa specificata origine sono racchiusi tra delimitatori specificato.

**Esempi**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatena tra 1 e 16 argomenti, devono essere stringhe.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Lunghezza di una stringa.

### <a name="substring"></a>sottostringa

    substring("abcdefg", 1, 2) == "bc"

Estrarre una sottostringa da una stringa origine specificato a partire da un determinato indice. Facoltativamente, è possibile specificare la lunghezza della sottostringa richiesta.

**Sintassi**

    substring(source, startingIndex [, length])

**Argomenti**

* *origine:* Stringa di origine che verrà eseguita la sottostringa da.
* *startingIndex:* Posizione carattere iniziale in base zero della sottostringa richiesta.
* *lunghezza:* Parametro facoltativo che può essere utilizzato per specificare il numero di caratteri richiesto nella sottostringa. 

**Restituisce**

Una sottostringa dalla stringa specificata. La sottostringa iniziando dalla posizione startingIndex (in base zero) e continua fino alla fine dei caratteri stringa o lunghezza, se specificato.

**Esempi**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>ToLower

    tolower("HELLO") == "hello"

Converte una stringa in lettere minuscole.

### <a name="toupper"></a>ToUpper

    toupper("hello") == "HELLO"

Converte una stringa in lettere maiuscole.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Matrici, oggetti e dinamiche

[valori letterali](#dynamic-literals) | [cast](#casting-dynamic-objects) | [operatori](#operators) | [consentire clausole](#dynamic-objects-in-let-clauses)
<br/>
[ArrayLength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervallo](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Ecco il risultato di una query di un'eccezione approfondimenti applicazione. Il valore di `details` in forma di matrice.

![](./media/app-insights-analytics-reference/310.png)

**Indicizzazione:** Indice matrici e oggetti come JavaScript:

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Ma utilizzare `arraylength` e altre funzioni Analitica (non "Length"!)

**Il cast** In alcuni casi è necessario eseguire il cast di un elemento che estrarre da un oggetto, poiché il tipo di potrebbe variare. Ad esempio `summarize...to` è necessario un tipo specifico:

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Valori letterali** Per creare una matrice espliciti o un oggetto elenco proprietà, scrivere come stringa JSON e cast:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Per effettuare il pull distanza tra le proprietà di un oggetto in righe separate, utilizzare mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** Per trovare tutti i percorsi in un oggetto complesso:

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** Per trovare lo schema minimo che consente l'accesso a tutti i valori dell'espressione nella tabella:

    exceptions | summarize buildschema(details)

Risultato:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Si noti che `indexer` viene utilizzato per contrassegnare nel punto in cui è consigliabile utilizzare un indice numerico. Per questo schema, alcuni percorsi validi da essere (presupponendo che tali indici di esempio presenti nell'intervallo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Valori letterali in forma di matrice e degli oggetti

Per creare un valore letterale dinamico, utilizzare `parsejson` (alias `todynamic`) con un JSON argomento stringa:

* `parsejson('[43, 21, 65]')`-una matrice di numeri
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-un singolo valore di tipo dinamico contenente un numero
* `parsejson('"21"')`-un singolo valore di tipo dinamico contenente una stringa

Nota Diversamente da quanto succede JavaScript, JSON impone l'utilizzo di virgolette (`"`) all'interno di stringhe. È facile offerta una stringa con codifica JSON letterali con virgolette (`'`).

In questo esempio viene creato un valore dinamico e successivamente, utilizzare i campi:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Funzioni di oggetto dinamico

|||
|---|---|
| *valore* `in` *in forma di matrice*| True se esiste un elemento di *matrice* che = = *valore*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valore* `!in` *in forma di matrice*| True se nessun elemento della *matrice* che = = *valore*
|[`arraylength(`in forma di matrice`)`](#arraylength)| Null se non è una matrice
|[`extractjson(`percorso, oggetto`)`](#extractjson)|Utilizza percorso per spostarsi nell'oggetto.
|[`parsejson(`origine`)`](#parsejson)| Viene convertita una stringa JSON in un oggetto dinamico.
|[`range(`da fare, passaggio`)`](#range)| Matrice di valori
|[`mvexpand`listColumn](#mvexpand-operator) | Replica una riga per ogni valore in un elenco in una cella specificata.
|[`summarize buildschema(`colonna`)`](#buildschema) |Deriva lo schema di tipo di contenuto di colonna
|[`summarize makelist(`colonna`)`](#makelist)| Semplifica gruppi di righe e lo inserisce i valori della colonna in una matrice.
|[`summarize makeset(`colonna`)`](#makeset) | Semplifica gruppi di righe e lo inserisce i valori della colonna in una matrice, senza duplicazione.

### <a name="dynamic-objects-in-let-clauses"></a>Oggetti dinamici nelle clausole consentono


[Consentire clausole](#let-clause) archivio dinamici valori come stringhe, in modo che tale due clausole sono equivalenti e che entrambi i `parsejson` (o `todynamic`) prima di essere utilizzati:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>ArrayLength

Il numero di elementi in una matrice dinamica.

**Sintassi**

    arraylength(array)

**Argomenti**

* *in forma di matrice:* A `dynamic` valore.

**Restituisce**

Il numero di elementi *in forma di matrice*, o `null` se *matrice* non è una matrice.

**Esempi**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

È possibile ottenere un elemento specificato fuori testo JSON utilizzando un'espressione di percorso. Se lo si desidera convertire la stringa estratta a un tipo specifico.


**Sintassi**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Restituisce**

Questa funzione esegue una query di JsonPath nell'origine dati che contiene una stringa JSON valida, se lo si desidera convertire il valore in un altro tipo a seconda il terzo argomento.



**Esempio**

La notazione [parentesi] e la notazione sono equivalenti:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Suggerimenti per le prestazioni**

* Applicare clausole where prima di utilizzare`extractjson()`
* Provare a utilizzare una corrispondenza di espressioni regolari con [estrarre](#extract) . Eseguire molto più rapidamente e se viene visualizzato il JSON da un modello.
* Usare `parsejson()` se è necessario estrarre più di un valore dal JSON.
* È consigliabile verificano JSON analizzati predefiniti in acquisizione mediante la dichiarazione di tipo della colonna da dinamico.

### <a name="json-path-expressions"></a>Espressioni JSON Path

|||
|---|---|
|`$`|Oggetto di primo livello|
|`@`|Oggetto corrente|
|`[0]`|Indice di matrice|
|`.`o`[0]` | Figlio|

*(È non attualmente implementare i caratteri jolly, la ricorsione, un'unione o sezioni.)*




### <a name="parsejson"></a>parsejson

Interpreta un `string` come [valore JSON](http://json.org/)) e restituisce il valore come `dynamic`. È superiore all'uso di `extractjson()` quando è necessario estrarre più di un elemento di un oggetto composto JSON.

**Sintassi**

    parsejson(json)

**Argomenti**

* *json:* Un documento JSON.

**Restituisce**

Un oggetto di tipo `dynamic` specificato da *json*.

**Esempio**

Nell'esempio seguente, quando `context_custom_metrics` è un `string` simile alla seguente: 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

quindi il frammento seguente recupera il valore della `duration` intervallo aperto nell'oggetto e da che recupera due bande orarie, `duration.value` e  `duration.min` (`118.0` e `110.0`rispettivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>intervallo

Il `range()` funzione (non deve essere confuso con la `range` operatore) genera una matrice dinamica tenendo una serie di valori la stessa spaziatura.

**Sintassi**

    range(start, stop, step)

**Argomenti**

* *start:* Il valore del primo elemento della matrice risultante. 
* *interrompere:* Il valore dell'ultimo elemento della matrice risultante oppure il valore minimo è maggiore dell'ultimo elemento della matrice risultante e all'interno di un numero intero più del *passaggio* da *start*.
* *passaggio:* Differenza tra due elementi consecutivi della matrice.

**Esempi**

Nell'esempio seguente viene restituito `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

Nell'esempio seguente restituisce una matrice contenente tutti i giorni nell'anno 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte una stringa in un valore dinamico.

### <a name="treepath"></a>TreePath

    treepath(dynamic_object)

Consente di enumerare tutte le espressioni di percorso che identificano foglie un oggetto dinamico. 

**Restituisce**

Matrice di espressioni di percorso.

**Esempi**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Nota che "[0]" indica la presenza di una matrice, ma non viene specificato l'indice utilizzato da un percorso specifico.

### <a name="zip"></a>CAP

    zip(list1, list2, ...)

Combina un insieme di elenchi in un elenco di tuple.

* `list1...`: Un elenco di valori

**Esempi**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Nomi

Nomi possono contenere fino a 1024 caratteri. Maiuscole e minuscole e possono contenere lettere, cifre e caratteri di sottolineatura (`_`). 

Offerta un nome utilizzando ["... '] or [" ... "] per includere altri caratteri o usare una parola chiave come nome. Per esempio:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' percorso\\valido\n\'x\''] | Usare \ per evitare i caratteri|
|["d-e.=/f#\n"] | |
|[@'path\file'] | Nessun escape - \ letterale|
|[@"\now& quindi\"] | |
|[in] | Uso di una parola chiave del linguaggio come un nome|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


