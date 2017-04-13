<properties
    pageTitle="Punteggio profili (Azure ricerca resto versione API 2015-02-28-versione di anteprima) | Microsoft Azure | Anteprima di ricerca Azure API"
    description="Ricerca Azure è un servizio di ricerca cloud ospitato che supporta l'ottimizzazione dei risultati classificati in base ai profili punteggio definite dall'utente."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Profili punteggio (ricerca Azure resto versione API 2015-02-28-versione di anteprima)

> [AZURE.NOTE] In questo articolo vengono illustrati i profili punteggio in [Anteprima 28 di 02 2015](search-api-2015-02-28-preview.md). Attualmente non vi è alcuna differenza tra il `2015-02-28` versione descritte su [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) e `2015-02-28-Preview` versione descritta di seguito, ma sono disponibili in questo documento comunque per fornire la copertura documento attraverso l'intera API.

## <a name="overview"></a>Panoramica

Punteggio si intende il calcolo di un punteggio di ricerca per tutti gli elementi restituiti nei risultati della ricerca. Il punteggio è un indicatore di pertinenza di un elemento nel contesto dell'operazione di ricerca corrente. Punteggio, più pertinenti l'elemento. Nei risultati della ricerca, gli elementi sono rango ordinato dal più grande a bassa, in base al ricerca punteggio calcolato per ogni elemento.

Ricerca Azure utilizza predefinito punteggio per calcolare un punteggio iniziale, ma è possibile personalizzare il calcolo in base a un punteggio profilo. Profili punteggio offrono un controllo maggiore sulla classificazione degli elementi nei risultati della ricerca. Ad esempio, è consigliabile promuovere gli elementi in base ai ricavi potenziali, alzare di livello elementi più recenti o forse promuovere gli elementi che sono stati in magazzino troppo lungo.

Un profilo punteggio fa parte della definizione dell'indice composta da campi, funzioni e parametri.

Per avere un'idea dell'aspetto di un profilo punteggio, nell'esempio seguente mostra un semplice profilo denominato 'geografico'. Questo tipo di ottimizzare gli elementi con il termine di ricerca nel `hotelName` campo. Utilizza anche la `distance` la funzione per ottimizzare gli elementi che si trovano all'interno di dieci chilometri della posizione corrente. Se un utente cerca il termine "DCI" e 'DCI' possono essere parte del nome del albergo, i documenti contenenti alberghi con 'DCI' verranno visualizzati superiori nei risultati della ricerca.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Per usare questo profilo punteggio, la query è predisposta per specificare il profilo nella stringa di query. Nella query riportata di seguito, si noterà parametri query `scoringProfile=geo` nella richiesta.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Questa query cerca il termine 'DCI' e passa alla posizione corrente. Si noti che la query include altri parametri, ad esempio `scoringParameter`. Parametri di query sono descritti nei [Documenti di ricerca (API di ricerca Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Fare clic su [esempio](#example) per visualizzare un esempio più dettagliato di un profilo punteggio.

## <a name="what-is-default-scoring"></a>Che cos'è punteggio predefinita?

Punteggio calcola un punteggio di ricerca per ogni elemento in un set di risultati ordinato classificazione. Tutti gli elementi in un set di risultati di ricerca sono assegnato un punteggio di ricerca, quindi classificati più alto al più basso. Gli elementi con i punteggi maggiori vengono restituiti all'applicazione. Per impostazione predefinita, vengono restituite le prime 50, ma è possibile utilizzare il `$top` parametro per restituire un numero maggiore o minori di elementi (fino a 1000 in una singola risposta).

Per impostazione predefinita, un punteggio di ricerca viene calcolato in base alle proprietà statistiche dei dati e la query. Ricerca Azure consente di trovare i documenti che includono i termini di ricerca nella stringa di query (alcuni o tutti, a seconda del tipo `searchMode`), che favorisce nei documenti che contengono molte istanze del termine di ricerca. Il punteggio ricerca aumenta ancora maggiore se il termine risulti raro corpus dati, ma comuni all'interno del documento. Base per questo approccio per pertinenza elaborazione è noto come TF IDF o (termini documento inversa frequenza frequenza).

Supponendo che non esiste alcun ordinamento personalizzato, i risultati vengono quindi classificati punteggio ricerca prima vengono restituiti all'applicazione chiamante. Se `$top` non è specificato, 50 elementi ricerche più alti punteggio vengono restituiti.

I valori di punteggio ricerca possono essere ripetuti all'interno di un set di risultati. Ad esempio, potrebbe essere 10 elementi con un punteggio di 1.2, 20 gli elementi con un punteggio di 1.0 e 20 con un punteggio di 0,5. Nel caso più accessi stesso punteggio di ricerca, l'ordine degli stessi elementi punteggio non è definito e non stabile. Eseguire la query ed è possibile che venga visualizzato elementi posizione MAIUSC. Dato due elementi con un punteggio identico, non ci sono garanzie quale viene visualizzata per prima.

## <a name="when-to-use-custom-scoring"></a>Quando utilizzare punteggio personalizzato

È consigliabile creare uno o più profili punteggio quando l'impostazione predefinita la classificazione comportamento non accede fino in riunione obiettivi commerciali. Ad esempio, è possibile decidere di pertinenza delle ricerche preferibile elementi appena aggiunti. Analogamente, potrebbe essere un campo che contiene il margine o un altro campo che indica lo stato dei ricavi potenziali. Aumento accessi che porta vantaggi per le aziende può essere importante stabilire se utilizzare profili punteggio.

In base pertinenza ordinamento anche implementato tramite i profili di classificazione. Valutare la possibilità di pagine dei risultati di ricerca sono state usate in passato che consentono di ordinare in base a prezzo, data, classificazione o pertinenza. Risultati di ricerca di Azure, profili punteggio unità l'opzione "pertinenza". La definizione di pertinenza viene controllata dall'utente, solo gli obiettivi aziendali e il tipo di esperienza di ricerca che si desidera eseguire.

<a name="example"></a>
## <a name="example"></a>Esempio

Come indicato, il punteggio personalizzato implementato tramite il punteggio profili definiti in uno schema di indice.

In questo esempio viene illustrato lo schema di un indice con due profili punteggio (`boostGenre`, `newAndHighlyRated`). Le query sull'indice che include uno dei due profilo come un parametro di query verrà utilizzato il profilo per ottenere il set di risultati.

[Eseguire questo esempio](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Flusso di lavoro

Per implementare un comportamento punteggio personalizzato, aggiungere un profilo punteggio allo schema che definisce l'indice. È possibile creare più profili punteggio all'interno di un indice, ma è possibile specificare un profilo solo in fase di una query specificata.

Iniziare con il [modello](#bkmk_template) fornito in questo argomento.

Specificare un nome. I profili punteggio sono facoltativi, ma se si aggiunge una, il nome è obbligatorio. Assicurarsi di seguire le convenzioni di denominazione per i campi (inizia con una lettera, evitando caratteri speciali e le parole riservate). Per ulteriori informazioni, vedere [Regole di denominazione](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

Corpo del profilo punteggio sia impostato dai campi ponderati e funzioni.

### <a name="weights"></a>Spessori ###

Il `weights` proprietà di un profilo punteggio specifica coppie di nome da assegnare spessore relativo a un campo. Nell' [esempio](#example), i campi albumTitle, perimetro e artistName sono invece un incremento 1,5, 5 e 2. Perché perimetro viene aumentata in modo molto superiore rispetto alle altre? Se la ricerca dei dati che è leggermente omogeneo (come nel caso con 'perimetro' nel `musicstoreindex`), potrebbe essere necessario una variazione più grande in peso relativo. Ad esempio, nel `musicstoreindex`, 'estremamente' viene visualizzata come entrambi perimetro e nelle descrizioni identico infinità perimetro. Se si vuole perimetro in descrizione perimetro, il campo genere sarà necessario un quantità maggiore peso relativo.

### <a name="functions"></a>Funzioni ###

Funzioni vengono utilizzate per gli altri calcoli necessari per contesti specifici. Tipi di funzione valido sono `freshness`, `magnitude`, `distance` e `tag`. Parametri esclusive ogni funzione.

  - `freshness`deve essere utilizzato quando si desidera promuovere come nuova o precedente di un elemento è. Questa funzione può essere utilizzata solo con campi datetime (`Edm.DataTimeOffset`). Nota la `boostingDuration` attributo viene utilizzato solo con la funzione di validità.
  - `magnitude`dovrebbe essere usata quando si desidera promuovere in base a come alta o bassa un valore numerico. Scenari in cui chiamare per questa funzione includono incrementare dal margine di profitto, prezzo più alto, prezzo più basso o conteggio di download. È possibile annullare l'intervallo, elevato a bassa, se si desidera che il modello inverso (ad esempio, per gli elementi Preamplificazione basso prezzo più alta elementi). Dato un intervallo di prezzi da 100 $ a $1, è necessario impostare `boostingRangeStart` 100 e `boostingRangeEnd` da 1 a promuovere gli elementi di un prezzo inferiore. Questa funzione è utilizzabili solo con campi double e integer.
  - `distance`dovrebbe essere usata quando si desidera promuovere prossimità o area geografica. Questa funzione può essere utilizzata solo con `Edm.GeographyPoint` campi.
  - `tag`dovrebbe essere usata quando si desidera promuovere da tag in comune tra documenti e le query di ricerca. Questa funzione può essere utilizzata solo con `Edm.String` e `Collection(Edm.String)` campi.
  
#### <a name="rules-for-using-functions"></a>Regole per l'uso di funzioni ####

  - Tipo di funzione (validità, la grandezza, distanza, tag) deve essere in minuscolo.
  - Funzioni non possono contenere valori null o vuoti. Se si include fieldname, in particolare, è necessario impostarla su un valore.
  - Funzioni possono essere applicate solo ai campi possibile filtrare. Per ulteriori informazioni sui campi possibile filtrare, vedere [Create Index](search-api-2015-02-28-preview.md#CreateIndex) .
  - Funzioni possono essere applicate solo ai campi che sono definiti nell'elenco campi di un indice.

Dopo avere definito l'indice, creare l'indice caricando lo schema di indice, seguito da documenti. Per informazioni su queste operazioni, vedere [Create Index](search-api-2015-02-28-preview.md#CreateIndex) e [aggiungere o aggiornare i documenti](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) . Dopo aver creato l'indice, è necessario avere un profilo di punteggio funziona che interagisce con i dati di ricerca.

<a name="bkmk_template"></a>
## <a name="template"></a>Modello
Questa sezione illustra la sintassi e il modello per i profili di classificazione. Fare riferimento alla [Guida di riferimento attributo indice](#bkmk_indexref) nella sezione successiva per le descrizioni degli attributi.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
            ...
          }
        },
        "functions": (optional) [
          {
            "type": "magnitude | freshness | distance | tag",
            "boost": # (positive number used as multiplier for raw score != 1),
            "fieldName": "...",
            "interpolation": "constant | linear (default) | quadratic | logarithmic",

            "magnitude": {
              "boostingRangeStart": #,
              "boostingRangeEnd": #,
              "constantBoostBeyondRange": true | false (default)
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Punteggio di riferimento sulle proprietà del profilo

**Nota** Una funzione punteggio può essere applicata solo ai campi che supportano.

| Proprietà | Descrizione |
|----------|-------------|
| `name`   | Obbligatorio. Questo è il nome del profilo punteggio. Segue le stesse convenzioni di denominazione di un campo. Devono iniziare con una lettera, non possono contenere punti, punteggiatura o @ simboli e non può iniziare con la frase "azureSearch" (maiuscole/minuscole). |
| `text` | Contiene la proprietà spessori. |
| `weights` | Facoltativo. Una coppia di nome valore che specifica un nome di campo e lo spessore relativo. Spessore relativo deve essere un intero positivo o un numero a virgola mobile. È possibile specificare il nome del campo senza un peso corrispondente. Spessori vengono utilizzati per indicare la priorità di un campo rispetto a altro. |
| `functions` | Facoltativo. Si noti che una funzione punteggio può essere applicata solo ai campi che supportano. |
| `type` | Necessario per le funzioni di classificazione. Indica il tipo di funzione da utilizzare. I valori validi includono `magnitude`, `freshness`, `distance` e `tag`. È possibile includere più di una funzione in ogni profilo punteggio. Il nome della funzione deve essere in minuscolo. |
| `boost` | Necessario per le funzioni di classificazione. Numero positivo utilizzato come moltiplicatore per punteggio non elaborato. Non può essere uguale a 1. |
| `fieldName` | Necessario per le funzioni di classificazione. Una funzione punteggio può essere applicata solo ai campi che fanno parte dell'insieme di campi dell'indice e che è possibile filtrare. Inoltre, ogni tipo di funzione introduce restrizioni aggiuntive (validità viene usato con i campi datetime, la grandezza con numeri interi o due campi, distanza con campi posizione e tag con stringa o stringa raccolta campi). È possibile specificare solo un singolo campo per ogni definizione di funzione. Ad esempio, per usare la grandezza due volte nello stesso profilo, sarà necessario includere due grandezza definizioni, uno per ogni campo. |
| `interpolation` | Necessario per le funzioni di classificazione. Definisce la pendenza per cui il punteggio incrementare aumenta dall'inizio dell'intervallo fino alla fine dell'intervallo. I valori validi includono `linear` (impostazione predefinita), `constant`, `quadratic`, e `logarithmic`. Per informazioni dettagliate, vedere [impostare interpolazione](#bkmk_interpolation) . |
| `magnitude` | La grandezza punteggio funzione viene utilizzata per modificare valutazioni in base all'intervallo dei valori per un campo numerico. Alcuni esempi di utilizzo più comune di queste sono:<ul><li>Classificazioni a stelle: modificare la classificazione in base al valore all'interno del campo "Stelle". Dopo aver configurato due elementi pertinenti, verrà visualizzato prima di tutto l'elemento con il livello di sicurezza.</li><li>Margine: Dopo aver configurato due documenti pertinenti, un rivenditore potrebbe essere necessario promuovere i documenti contenenti i margini superiori prima di tutto.</li><li>Fare clic su conteggi: per le applicazioni di traccia fare clic su tramite azioni a prodotti o pagine, è possibile utilizzare la grandezza per gli elementi Preamplificazione comandi sono particolarmente utili per ottenere il massimo il traffico.</li><li>Scaricare conteggi: per le applicazioni che traccia download, il consente di funzione grandezza promuovere gli elementi che hanno il maggior numero di download.</li></ul> |
| `magnitude:boostingRangeStart` | Imposta il valore di inizio dell'intervallo in cui è totalizza grandezza. Il valore deve essere un numero intero o numero a virgola mobile. Per classificazioni a stelle da 1 a 4, dovrebbe essere 1. Per i margini oltre il 50%, trattarsi 50. |
| `magnitude:boostingRangeEnd` | Imposta il valore di fine dell'intervallo in cui è totalizza grandezza. Il valore deve essere un numero intero o numero a virgola mobile. Per classificazioni a stelle da 1 a 4, trattarsi 4. |
| `magnitude:constantBoostBeyondRange` | Valori validi sono VERO o FALSO (impostazione predefinita). Quando è impostato su true, Preamplificazione completo continuerà da applicare ai documenti che hanno un valore per il campo di destinazione che supera il limite superiore dell'intervallo. Se è false, l'incremento di questa funzione non verranno applicata ai documenti con un valore per il campo di destinazione che non rientrano nell'intervallo. |
| `freshness` | La validità punteggio funzione viene utilizzata per modificare la classificazione punteggi per gli elementi in base ai valori nei campi DateTimeOffset. Ad esempio, un elemento con una data più recente può essere un livello superiore rispetto gli elementi meno recenti. Si noti che è anche possibile rango elementi quali eventi del calendario con date future in modo che gli elementi più vicino a quella odierna possono essere un livello superiore rispetto a quelli ulteriormente in futuro. Nella versione corrente del servizio, verrà corretta un'estremità dell'intervallo per l'ora corrente. L'altra estremità sia un'ora in passato in base alla `boostingDuration`. Promuovere un intervallo di tempo in futuro usare un valore negativo `boostingDuration`. Frequenza di incremento viene modificato da un massimo e minimo dipende dalla interpolazione applicata al profilo punteggio (vedere la figura seguente). Per invertire il fattore di miglioramento applicato, selezionare un fattore di incremento di minore di 1. |
| `freshness:boostingDuration` | Imposta una scadenza dopo momento verrà interrotto per un documento specifico. Vedere [Set boostingDuration] [#bkmk_boostdur] nella sezione seguente per sintassi ed esempi. |
| `distance` | Chiusura il punteggio dei documenti in base a come la distanza punteggio viene utilizzata per modificare o estrema sono relativi a un'area geografica di riferimento. Come parte della query in un parametro viene specificato il percorso di riferimento (utilizzando il `scoringParameter` parametro di query) come un lon lat argomento. |
| `distance:referencePointParameter` | Parametro da passare nelle query da utilizzare come riferimento. scoringParameter è un parametro di query. Vedere [I documenti di ricerca](search-api-2015-02-28-preview.md#SearchDocs) per le descrizioni dei parametri di query. |
| `distance:boostingDistance` | Numero che indica la distanza in chilometri dal percorso di riferimento nel punto in cui l'aumento della finisce. |
| `tag` | Il tag punteggio funzione viene utilizzato per modificare il punteggio dei documenti in base ai tag nei documenti e le query di ricerca. Vengano aumentati documenti che contengono tag simili a quelle delle query di ricerca. I contrassegni per la ricerca viene specificato come parametro punteggio in ogni richiesta di ricerca (utilizzando il `scoringParameter` parametro di query). |
| `tag:tagsParameter` | Parametro da passare nelle query per specificare tag per una determinata richiesta. `scoringParameter`è un parametro di query. Vedere [I documenti di ricerca](search-api-2015-02-28-preview.md#SearchDocs) per le descrizioni dei parametri di query. |
| `functionAggregation` | Facoltativo. Si applica solo quando vengono specificate funzioni. I valori validi includono: `sum` (impostazione predefinita), `average`, `minimum`, `maximum`, e `firstMatching`. Un punteggio di ricerca è un singolo valore calcolato dal più variabili, incluse le funzioni più. Questo attributi indica come incrementi di tutte le funzioni vengono combinati in un singolo Preamplificazione aggregazione che viene quindi applicata al punteggio documento di base. Il punteggio di base è in base al valore tf idf calcolato dal documento e la query di ricerca. |
| `defaultScoringProfile` | Quando si esegue una richiesta di ricerca, se non viene specificato alcun profilo punteggio, quindi punteggio predefinito è utilizzato tf-idf (solo). Predefinito punteggio nome profilo è possibile impostare qui causa la ricerca di Azure all'utilizzo del profilo quando viene fornito alcun profilo specifico nella richiesta di ricerca. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Interpolazione set

Interpolazione consentono di definire la pendenza per cui il punteggio incrementare aumenta dall'inizio dell'intervallo fino alla fine dell'intervallo. È possibile utilizzare interpolazione seguenti:

  - `Linear`: Per gli elementi all'interno dell'intervallo max e min, Preamplificazione applicata all'elemento verrà eseguita in una quantità costantemente decrescente. Lineare è interpolazione predefinita per un profilo punteggio.
  - `Constant`: Per gli elementi che si trovano all'interno dell'inizio e fine intervallo, un aumento costante verrà applicato ai risultati di ricerca.
  - `Quadratic`: Rispetto a un'interpolazione lineare contenente un aumento costantemente decrescente, quadratica inizialmente ridurrà a velocità inferiore e quindi come questo si avvicina la data di fine, riduce intervalli molto più alto. Questa opzione interpolazione non è consentita nel tag funzioni di classificazione.
  - `Logarithmic`: Rispetto a un'interpolazione lineare contenente una costante diminuzione Preamplificazione, logaritmica inizialmente ridurrà a velocità superiore e quindi quando questo si avvicina la data di fine, riduce in un intervallo di dimensioni inferiore. Questa opzione interpolazione non è consentita nel tag funzioni di classificazione.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Set boostingDuration

`boostingDuration`è un attributo della funzione validità. Utilizzare per impostare una scadenza punto dopo momento verrà interrotto per un documento specifico. Ad esempio, per promuovere una linea o marchio per un periodo promozionale di 10 giorni, specificare il periodo di 10 giorni come "P10D" per tali documenti. O per promuovere eventi imminenti della settimana successiva specificano "-P7D".

`boostingDuration`deve essere formattato come valore di "dayTimeDuration" XSD (un sottoinsieme di un valore di durata ISO8601 con restrizioni). Il modello per questa operazione è: `[-]P[nD][T[nH][nM][nS]]`.

La tabella seguente contiene alcuni esempi.

| Durata | boostingDuration |
|----------|------------------|
| 1 giorno | "P1D" |
| 2 giorni e 12 ore | "P2DT12H" |
| 15 minuti | "PT15M" |
| 30 giorni, 5 ore 10 minuti, e 6.334 secondi | "P30DT5H10M6.334S" |

Per altri esempi, vedere [Schema XML: tipi di dati (il sito web w3)](http://www.w3.org/TR/xmlschema11-2/).

**Vedere anche**
[all'API REST di servizio di ricerca Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN <br/>
[Creare l'indice (ricerca Azure API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) su MSDN<br/>
[Aggiungere un punteggio profilo un indice di ricerca](http://msdn.microsoft.com/library/azure/dn798928.aspx) su MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
