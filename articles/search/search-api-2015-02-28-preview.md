<properties
   pageTitle="Azure servizio resto versione API 2015-02-28-Anteprima ricerca | Microsoft Azure | Anteprima di ricerca Azure API"
   description="Azure servizio resto versione API 2015-02-28-Anteprima ricerca include funzionalità sperimentazione, ad esempio ricerche moreLikeThis e analisi del linguaggio naturale."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API REST servizio di ricerca Azure: Versione 2015-02-28-Anteprima

In questo articolo è la documentazione di riferimento `api-version=2015-02-28-Preview`. Questa versione di anteprima estende la versione corrente generalmente disponibile, [versione api = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), fornendo le caratteristiche di sperimentazione seguenti:

- `moreLikeThis`parametro nei [Documenti di ricerca](#SearchDocs) API della query. Trova altri documenti relativi a un altro documento specifico.

Alcune altre parti di `2015-02-28-Preview` API REST sono descritte separatamente. Sono inclusi:

- [I profili di classificazione](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indicizzatori](search-api-indexers-2015-02-28-preview.md)

Azure servizio di ricerca è disponibile in più versioni. Per informazioni dettagliate, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

## <a name="apis-in-this-document"></a>API in questo documento

API del servizio di ricerca Azure supporta due sintassi di URL per le operazioni di API: semplice e OData (per informazioni dettagliate, vedere [supporto di OData Azure ricerca API ()](http://msdn.microsoft.com/library/azure/dn798932.aspx) ). L'elenco seguente viene illustrata la sintassi semplice.

[Creare l'indice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Aggiorna indice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Ottenere indice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Elenco degli indici](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Ottenere le statistiche di indice analitico](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Analizzatore test](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Eliminare un indice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Aggiungere, eliminare e aggiornare i dati all'interno di un indice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Cerca documenti](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Documento di ricerca](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Numero di documenti](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Suggerimenti](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Operazioni di indice

È possibile creare e gestire gli indici di servizio di ricerca di Azure tramite semplice le richieste HTTP (POST, GET, inserire, eliminare) relative a una risorsa di indice specificato. Per creare un indice analitico, è prima di tutto registrare un documento JSON che descrive lo schema di indice. Lo schema definisce i campi di indice, i tipi di dati e come possono essere utilizzati (ad esempio, in ricerche full-text, filtri, ordinamento o faceting). Definisce inoltre punteggio profili, suggesters e altri attributi per configurare il comportamento dell'indice.

Nell'esempio seguente fornisce un'illustrazione di uno schema utilizzato per la ricerca informazioni albergo con il campo descrizione definito in due lingue. Si noti come attributi controllano come il campo viene utilizzato. Ad esempio il `hotelId` viene utilizzato come chiave documento (`"key": true`) e viene esclusa dal ricerche full-text (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Una volta creato l'indice, viene caricata documenti da inserire l'indice. Vedere [aggiungere o aggiornare i documenti](#AddOrUpdateDocuments) per il passaggio successivo.

Per un video introduttivo su l'indicizzazione nella ricerca di Azure, vedere il [canale 9 Cloud coprire episodio nella ricerca Azure](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Creare l'indice

Un indice è il mezzo principale di organizzare e cercare i documenti nella ricerca di Azure, simile a come una tabella consente di organizzare i record in un database. Ogni indice ha un insieme di documenti che tutti sono conformi allo schema di indice (i nomi dei campi, tipi di dati e proprietà), ma gli indici specificano anche costrutti aggiuntivi (suggesters, profili punteggio e opzioni CORS) che definiscono altre funzionalità di ricerca.

È possibile creare un nuovo indice all'interno di un servizio di ricerca di Azure mediante una richiesta HTTP POST o inserire. Corpo della convocazione è uno schema JSON che specifica l'indice e informazioni di configurazione.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile utilizzare inserire e specificare il nome dell'indice sull'URI. Se l'indice non esiste, verrà creato.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Creazione di un indice determina la struttura dei documenti archiviati e usati nelle operazioni di ricerca. Compilare l'indice è un'operazione distinta. Per questo passaggio, è possibile utilizzare un [indicizzatore](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponibile per le origini dati supportate) o un'operazione di [aggiunta, aggiornamento o eliminare documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx) . L'indice invertito viene generato quando i documenti vengono registrati.

**Nota**: il numero massimo di indici consentiti varia a seconda del livello di prezzi. Il servizio gratuito consente fino a 3 indici. Servizio standard consente 50 indici per servizio di ricerca. Per informazioni dettagliate, vedere [limiti e vincoli](http://msdn.microsoft.com/library/azure/dn798934.aspx) .

**Richiesta**

HTTPS è necessario per tutte le richieste di assistenza. Utilizzo del metodo di un POST o inserire, è possibile creare la richiesta di **Create Index** . Quando si usa POST, è necessario specificare un nome di indice nel corpo della richiesta insieme alla definizione dello schema di indice. Con caricamento, il nome dell'indice fa parte dell'URL. Se non è presente l'indice, viene creato. Se esiste già, viene aggiornato alla nuova definizione.

Il nome dell'indice deve essere minuscola, iniziare con una lettera o un numero, ha senza barre o punti e inferiore a 128 caratteri. Dopo il nome dell'indice a partire da una lettera o un numero, il resto del nome è possibile includere qualsiasi lettera, numero e trattini, purché i trattini non sono consecutivi.

Il `api-version` è necessario. Per un elenco di versioni disponibili, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `Content-Type`: Obbligatorio. Impostare questa opzione`application/json`
- `api-key`: Obbligatorio. Il `api-key` viene utilizzata per
- eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Create Index** deve includere un `api-key` intestazione impostare la chiave di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere sia il nome di servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

<a name="RequestData"></a>
**Nella sintassi corpo della convocazione**

Il corpo della convocazione contiene una definizione di schema che contiene l'elenco dei campi di dati all'interno di documenti che verranno caricati l'indice, tipi di dati, attributi, nonché un elenco facoltativo di punteggio profili utilizzati per punteggio documenti corrispondenti in fase di query.

Si noti che per una richiesta POST, è necessario specificare il nome dell'indice nel corpo della richiesta.

Nell'indice analitico solo può essere un campo chiave. Deve essere di tipo stringa. Questo campo rappresenta l'identificatore univoco per ogni documento archiviato all'interno dell'indice.

Parti di un indice sono i seguenti:

- `name`
- `fields`che verrà caricata l'indice, inclusi nome, il tipo di dati e proprietà che definiscono le azioni consentite nel campo.
- `suggesters`usato per le query di ricerca incrementale o completamento automatico.
- `scoringProfiles`usato per la classificazione punteggio di ricerca personalizzata. Per informazioni dettagliate, vedere [aggiungere punteggio profili](https://msdn.microsoft.com/library/azure/dn798928.aspx) .
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` consente di definire come i documenti/query sono suddivisi in token indicizzabile/ricercabile. Per informazioni dettagliate, vedere [analisi dei risultati di ricerca di Azure](https://aka.ms//azsanalysis) .
- `defaultScoringProfile`Consente di sovrascrivere il valore predefinito punteggio comportamenti.
- `corsOptions`per consentire query origine tra l'indice.

La sintassi per strutturare payload richiesta è come indicato di seguito. Una richiesta di esempio è disponibili ulteriori in questo argomento.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Attributi indice**

Durante la creazione di un indice analitico, è possono impostare gli attributi seguenti. Per informazioni dettagliate sui profili di punteggio e punteggio, vedere [aggiungere profili punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Imposta il nome del campo.

`type`-Imposta il tipo di dati per il campo. Per un elenco dei tipi supportati, vedere [Tipi di dati supportati](#DataTypes) .

`searchable`-Il campo viene contrassegnato come full-text grado di ricerca. Questo errore indica che verrà apportati analisi, ad esempio parole durante l'indicizzazione. Se si imposta un `searchable` campo a un valore come "giorno sole", internamente che verrà divisa in singoli token "sole" e "giorno". In questo modo ricerche full-text per i termini che seguono. Campi di tipo `Edm.String` o `Collection(Edm.String)` sono `searchable` per impostazione predefinita. Altri tipi di campo non può essere `searchable`.

  - **Nota**: `searchable` campi occupano spazio dell'indice di poiché Azure ricerca verrà archiviata un'ulteriore versione token del valore del campo per ricerche full-text. Se si desidera risparmiare spazio nell'indice e non è necessario un campo da includere nelle ricerche, impostare `searchable` a `false`.

`filterable`-Consente il campo a cui fa `$filter` query. `filterable`è diverso dal ruolo `searchable` in modalità di gestione delle stringhe. Campi di tipo `Edm.String` o `Collection(Edm.String)` che sono `filterable` non vengono sottoposti a parole, in modo che i confronti siano per corrispondenze esatte. Ad esempio, se si imposta un campo `f` a "sole giorno", `$filter=f eq 'sunny'` non troverà alcuna corrispondenza, ma `$filter=f eq 'sunny day'` verrà. Tutti i campi sono `filterable` per impostazione predefinita.

`sortable`-Per impostazione predefinita di ordinamento dei risultati per punteggio, ma in esperienze molti utenti rendersi necessario ordinare in base a campi nei documenti. Campi di tipo `Collection(Edm.String)` non può essere `sortable`. Tutti gli altri campi vengono `sortable` per impostazione predefinita.

`facetable`-In genere utilizzati in una presentazione dei risultati di ricerca che include i passaggi in base alla categoria (ad esempio, cercare fotocamere e vedere accessi da marchio megapixel, prezzo, ecc.). Questa opzione non può essere utilizzata con i campi di tipo `Edm.GeographyPoint`. Tutti gli altri campi vengono `facetable` per impostazione predefinita.

  - **Nota**: i campi di tipo `Edm.String` che sono `filterable`, `sortable`, o `facetable` possono contenere al massimo 32 KB lunghezza. Ciò avviene perché questi campi, vengono trattati come una singola parola chiave e la lunghezza massima di un termine di ricerca di Azure è 32KB. Se è necessario memorizzare più testo superiore in un campo singola stringa, sarà necessario impostare in modo esplicito `filterable`, `sortable`, e `facetable` a `false` la definizione dell'indice.

  - **Nota**: se sono previsti gli attributi indicati impostati su un campo `true` (`searchable`, `filterable`, `sortable`, o`facetable`) il campo in modo efficace è esclusi dall'indice invertito. Questa opzione è utile per i campi che non vengono utilizzati nelle query, ma sono necessari nei risultati della ricerca. Esclusione di tali campi dall'indice migliora le prestazioni.

`key`-Contrassegna il campo come contenente gli identificatori univoci per i documenti all'interno dell'indice. Un solo campo deve essere selezionato come il `key` campo che deve essere di tipo `Edm.String`. Campi chiavi possono essere utilizzati per cercare documenti direttamente tramite l' [API di ricerca](#LookupAPI).

`retrievable`-Indica se il campo può essere restituito un risultato di ricerca.  Questo è utile quando si desidera utilizzare un campo (ad esempio margine) come filtro, ordinamento o il punteggio meccanismo ma non si desidera che il campo deve essere visibile per l'utente finale. Questo attributo deve essere `true` per `key` campi.

`analyzer`-Imposta il nome dell'analizzatore da utilizzare per il campo in ora di ricerca e indicizzazione. Per il set di valori consentito vedere [analisi](https://msdn.microsoft.com/library/mt605304.aspx). Questa opzione può essere utilizzata solo con `searchable` non è possibile impostare i campi e i dati insieme al `searchAnalyzer` o `indexAnalyzer`.  Una volta scelto analizzatore, non può essere modificato per il campo.

`searchAnalyzer`-Imposta il nome dell'analizzatore utilizzato in fase di ricerca relativa al campo. Per il set di valori consentito vedere [analisi](https://msdn.microsoft.com/library/mt605304.aspx). Questa opzione può essere utilizzata solo con `searchable` campi. Devono essere impostata con `indexAnalyzer` e non è possibile impostare in combinazione con la `analyzer` opzione. In un campo esistente, è possibile aggiornare questo analizzatore.

`indexAnalyzer`-Imposta il nome dell'analizzatore utilizzato in fase di indicizzazione per il campo. Per il set di valori consentito vedere [analisi](https://msdn.microsoft.com/library/mt605304.aspx). Questa opzione può essere utilizzata solo con `searchable` campi. Devono essere impostata con `searchAnalyzer` e non è possibile impostare in combinazione con la `analyzer` opzione. Una volta scelto analizzatore, non può essere modificato per il campo.

`suggesters`-Imposta la modalità di ricerca e l'origine di contenuto per i suggerimenti per i campi. Per informazioni dettagliate, vedere [Suggesters](#Suggesters) .

`scoringProfiles`-Definisce punteggio comportamenti personalizzati che consentono di determinare quali gli elementi visualizzati superiori nei risultati della ricerca. Punteggio profili sono costituiti da funzioni e spessori di campo. Per ulteriori informazioni sugli attributi utilizzato in un punteggio profilo, vedere [aggiungere i profili di classificazione](https://msdn.microsoft.com/library/azure/dn798928.aspx) .

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Supporto delle lingue**

Campi che supportano le ricerche subire analisi frequente che la maggior parte comporta l'interruzione delle parole, normalizzazione testo e filtrando termini. Per impostazione predefinita, i campi che supportano le ricerche nella ricerca Azure vengono analizzati con l' [analizzatore Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) che suddivide il testo in elementi in base alle regole["segmentazione testo Unicode"](http://unicode.org/reports/tr29/) . Inoltre, analizzatore standard converte tutti i caratteri le lettere minuscole. Documenti indicizzati e termini di ricerca verranno illustrate l'analisi durante l'elaborazione di query e di indicizzazione.

Ricerca Azure supporta diverse lingue. Ogni lingua richiede un analizzatore di testo standard spiegate le caratteristiche di una determinata lingua. Ricerca Azure offre due tipi di analisi:

- 35 analisi supportati da Lucene.
- 50 analisi supportati da proprietaria lingua di Microsoft elaborazione tecnologia utilizzata in Office e Bing.

Alcuni sviluppatori preferiscono la soluzione più intuitivo, semplice, Apri origine Lucene. Analisi del Lucene sono più rapidamente, ma l'analisi di Microsoft hanno avanzati, ad esempio lemmatizzazione, word decompounding (in lingue come tedesco, danese, olandese, svedese, norvegese, estone, fine, ungherese, slovacca) e il riconoscimento entità (URL, messaggi di posta elettronica, date, numeri). Se possibile, è necessario eseguire confronti di analisi Lucene e Microsoft per decidere quale un meglio.

***Le modalità di confronto***

Analizzatore Lucene per l'inglese estende analizzatore standard. Rimuove Possessivi (del finali) da parole, si applica derivanti da [Porter derivanti algoritmo](http://tartarus.org/~martin/PorterStemmer/)e rimuove inglese [interrompere le parole](http://en.wikipedia.org/wiki/Stop_words).

Confronto, analizzatore Microsoft esegue lemmatizzazione anziché derivanti. Significa che è possibile gestire moduli word declinate e irregolari molto più informazioni sui risultati nei risultati di ricerca più pertinenti (controllo modulo 7 di [Azure ricerca MVA presentazione](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) per informazioni dettagliate).

L'indicizzazione con analisi Microsoft è mediamente due o tre volte inferiore equivalenti Lucene, a seconda della lingua. Le prestazioni della ricerca non dovrebbero essere influenzata in modo significativo per le query di dimensioni medio.

***Configurazione***

Per ogni campo nella definizione di indice, è possibile impostare il `analyzer` proprietà per un nome di analisi che specifica la lingua e fornitore. Analizzatore stesso verrà applicata quando indicizzazione e la ricerca per il campo.
Ad esempio, è possibile impostare i campi separati per le descrizioni albergo inglese, francese e spagnolo presenti side-by-side nello stesso indice. Utilizzare il [parametro di query 'searchFields'](#SearchQueryParameters) per specificare il campo specifiche della lingua per eseguire una ricerca nelle query. È possibile esaminare gli esempi di query che includono la `analyzer` proprietà nei [Documenti di ricerca](#SearchDocs). 

***Elenco di analisi***

Di seguito è l'elenco delle lingue supportate insieme nomi analyzer Lucene e Microsoft.

<table style="font-size:12">
    <tr>
        <th>Lingua</th>
        <th>Nome dell'analisi Microsoft</th>
        <th>Nome analyzer Lucene</th>
    </tr>
    <tr>
        <td>Arabo</td>
        <td>AR.Microsoft</td>
        <td>AR.Lucene</td>      
    </tr>
    <tr>
        <td>Armeno</td>
        <td></td>
        <td>hy.Lucene</td>
    </tr>
    <tr>
        <td>Bengalese</td>
        <td>BN.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Basco</td>
        <td></td>
        <td>eu.Lucene</td>
    </tr>
    <tr>
        <td>Bulgaro</td>
        <td>BG.Microsoft</td>
        <td>BG.Lucene</td>
    </tr>
    <tr>
        <td>Catalano</td>
        <td>CA.Microsoft</td>
        <td>CA.Lucene</td>          
    </tr>
    <tr>
        <td>Cinese semplificato</td>
        <td>zh Hans.microsoft</td>
        <td>zh Hans.lucene</td>     
    </tr>
    <tr>
        <td>Cinese tradizionale</td>
        <td>zh Hant.microsoft</td>
        <td>zh Hant.lucene</td>     
    <tr>
    <tr>
        <td>Croato</td>
        <td>hr.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Ceco</td>
        <td>cs.Microsoft</td>
        <td>cs.Lucene</td>      
    </tr>    
    <tr>
        <td>Danese</td>
        <td>da.Microsoft</td>
        <td>da.Lucene</td>      
    </tr>    
    <tr>
        <td>Olandese</td>
        <td>NL.Microsoft</td>
        <td>NL.Lucene</td>  
    </tr>    
    <tr>
        <td>Inglese</td>        
        <td>en.Microsoft</td>
        <td>en.Lucene</td>      
    </tr>
    <tr>
        <td>Estone</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finlandese</td>
        <td>Fi.Microsoft</td>
        <td>Fi.Lucene</td>      
    </tr>    
    <tr>
        <td>Francese</td>
        <td>fr.Microsoft</td>
        <td>fr.Lucene</td>      
    </tr>
    <tr>
        <td>Galiziano</td>
        <td></td>
        <td>GL.Lucene</td>      
    </tr>
    <tr>
        <td>Tedesco</td>
        <td>de.Microsoft</td>
        <td>de.Lucene</td>      
    </tr>
    <tr>
        <td>Greco</td>
        <td>EL.Microsoft</td>
        <td>EL.Lucene</td>      
    </tr>
    <tr>
        <td>Gujarati</td>
        <td>GU.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Ebraico</td>
        <td>he.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.Lucene</td>      
    </tr>
    <tr>
        <td>Ungherese</td>      
        <td>hu.Microsoft</td>
        <td>hu.Lucene</td>
    </tr>
    <tr>
        <td>Islandese</td>
        <td>Is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonesiano (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.Lucene</td>      
    </tr>
    <tr>
        <td>Irlandese</td>
        <td></td>
        <td>GA.Lucene</td>
    </tr>
    <tr>
        <td>Italiano</td>
        <td>IT.Microsoft</td>
        <td>IT.Lucene</td>      
    </tr>
    <tr>
        <td>Giapponese</td>
        <td>ja.Microsoft</td>
        <td>ja.Lucene</td>
        
    </tr>
    <tr>
        <td>Kannada</td>
        <td>Ka.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coreano</td>
        <td>ko.Microsoft</td>
        <td>ko.Lucene</td>
    </tr>
    <tr>
        <td>Lettone</td>        
        <td>LV.Microsoft</td>
        <td>LV.Lucene</td>  
    </tr>
    <tr>
        <td>Lituano</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malese (alfabeto latino)</td>
        <td>ms.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marathi</td>
        <td>Mr.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norvegese</td>
        <td>NB.Microsoft</td>
        <td>No.Lucene</td>      
    </tr>
    <tr>
        <td>Farsi</td>
        <td></td>
        <td>fa.Lucene</td>      
    </tr>
    <tr>
        <td>Polacco</td>
        <td>PL.Microsoft</td>
        <td>PL.Lucene</td>      
    </tr>
    <tr>
        <td>Portoghese (Brasile)</td>
        <td>Br.microsoft pt</td>
        <td>Br.lucene pt</td>       
    </tr>
    <tr>
        <td>Portoghese (Portogallo)</td>
        <td>Pt.microsoft pt</td>        
        <td>Pt.lucene pt</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>PA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Rumeno</td>
        <td>Ro.Microsoft</td>
        <td>Ro.Lucene</td>
    </tr>
    <tr>
        <td>Russo</td>
        <td>RU.Microsoft</td>
        <td>RU.Lucene</td>  
    </tr>
    <tr>
        <td>Serbo (alfabeto cirillico)</td>
        <td>cyrillic.microsoft SR</td>
        <td></td>
    </tr>
    <tr>
        <td>Serbo (alfabeto latino)</td>
        <td>latin.microsoft SR</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovacco</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Sloveno</td>
        <td>SL.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Spagnolo</td>
        <td>es.Microsoft</td>
        <td>es.Lucene</td>
    </tr>
    <tr>
        <td>Svedese</td>
        <td>SV.Microsoft</td>
        <td>SV.Lucene</td>
    </tr>

    <tr>
        <td>Tamil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Tailandese</td>
        <td>th.Microsoft</td>
        <td>th.Lucene</td>
    </tr>
    <tr>
        <td>Turco</td>
        <td>TR.Microsoft</td>
        <td>TR.Lucene</td>      
    </tr>
    <tr>
        <td>Ucraino</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamita</td>
        <td>vi.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Ricerca di Azure fornisce inoltre configurazioni analyzer indipendente dalla lingua</td>
    <tr>
        <td>Piegatura ASCII standard</td>
        <td>standardasciifolding.Lucene</td>
        <td>
        <ul>
            <li>Segmentazione testo Unicode (Tokenizer Standard)</li>
            <li>Filtro pieghevole ASCII - converte i caratteri Unicode che non appartengono al set di caratteri ASCII primi 127 in equivalenti ASCII. Ciò è utile per la rimozione di segni diacritici.</li>
        </ul>
        </td>
    </tr>
</table>

Analisi del tutto con nomi annotati con <i>lucene</i> sono con tecnologia [analisi del linguaggio di Lucene Apache](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Sono disponibili ulteriori informazioni su ASCII piegatura filtro [qui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` definisce i campi in un indice vengono utilizzati per supportare completamento automatico nelle ricerche. In genere stringhe di ricerca parziale vengono inviate all' [API suggerimenti](#Suggestions) mentre l'utente digita una query di ricerca e l'API restituisce un insieme di frasi suggerite. Un suggester definite dall'utente nell'indice determina quali campi vengono utilizzati per creare i termini di ricerca incrementale. Per informazioni di configurazione, vedere [Suggesters](#Suggesters) .

**I profili di classificazione**

A `scoringProfile` definisce punteggio comportamenti personalizzati che consentono di determinare quali gli elementi visualizzati superiori nei risultati della ricerca. Punteggio profili sono costituiti da funzioni e spessori di campo. A questo scopo, specificare un profilo in base al nome nella stringa di query.

Punteggio profilo predefinito viene eseguito in background per calcolare un punteggio di ricerca per tutti gli elementi in un set di risultati. È possibile utilizzare interno, senza nome profilo punteggio. In alternativa, impostare `defaultScoringProfile` per utilizzare un profilo personalizzato come predefinito, richiamato ogni volta che un profilo personalizzato non viene specificato nella stringa di query.

Per informazioni dettagliate, vedere [aggiungere profili punteggio per un indice di ricerca (Azure ricerca servizio all'API REST)](search-api-scoring-profiles-2015-02-28-preview.md) .

**Opzioni CORS**

Javascript sul lato client non è possibile chiamare qualsiasi API per impostazione predefinita poiché impedisce tutte le richieste di origine tra il browser. Attivare CORS (origine tra la condivisione delle risorse) impostando la `corsOptions` attributo per consentire le query di origine di croce per l'indice. Si noti che solo query API supporto CORS per motivi di sicurezza. Per CORS, è possono impostare le opzioni seguenti:

- `allowedOrigins`(obbligatorio): si tratta di un elenco delle origini che verrà concesso l'accesso all'indice. Ciò significa che il codice Javascript served da tali origini diverse sarà autorizzati a eseguire una query l'indice (presupponendo che fornisce la chiave dell'API corretta). Ogni origine è in genere nella forma `protocol://fully-qualified-domain-name:port` anche se la porta spesso viene omesso. [In questo articolo](http://go.microsoft.com/fwlink/?LinkId=330822) per informazioni dettagliate, vedere.
 - Se si desidera consentire l'accesso a tutte le origini diverse, inclusi `*` come un singolo elemento di `allowedOrigins` in forma di matrice. Si noti che **non è consigliabile esercitazione per servizi di ricerca di produzione.** Tuttavia, può essere utile per lo sviluppo o il debug.
- `maxAgeInSeconds`(facoltativo): browser usare questo valore per determinare la durata (in secondi) per le risposte di verifica preliminare CORS cache. Deve trattarsi di un numero intero non negativo. È il valore più grande, saranno le prestazioni migliori, ma più tempo richiederà modifiche criteri CORS abbiano effetto. Se non è impostata, verrà utilizzata una durata predefinita di 5 minuti.

<a name="CreateUpdateIndexExample"></a>
**Esempio di corpo richiesta**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Risposta**

Per una richiesta riuscita: "201 creato".

Per impostazione predefinita, il corpo della risposta conterrà JSON per la definizione di indice che è stata creata. Se il `Prefer` richiesta intestazione è impostata su `return=minimal`, il corpo della risposta sarà vuoto e il codice di stato di successo sarà "204 Nessun contenuto" anziché "201 creato". Ciò avviene anche se inserire o POST utilizzato per creare l'indice.

**Note**

Attualmente non esiste un supporto limitato per gli aggiornamenti dello schema di indice. Tutti gli aggiornamenti dello schema che richiedono la reindicizzazione ad esempio la modifica di tipi di campo non sono attualmente supportati. Anche se non è possibile modificare o eliminare i campi esistenti, i nuovi campi possono essere aggiunti a un indice esistente in qualsiasi momento. Quando viene aggiunto un nuovo campo, tutti i documenti esistenti nell'indice analitico avrà automaticamente un valore null per quel campo. Verrà utilizzato nessuno spazio di archiviazione aggiuntivo fino a quando non vengono aggiunti nuovi documenti per l'indice.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

La caratteristica per i suggerimenti di ricerca Azure è una funzionalità di query di ricerca incrementale o completamento automatico, fornendo un elenco di potenziali termini di ricerca in risposta a input stringa parziale immessi in una casella di ricerca. Si sarà notato i suggerimenti di query quando si usa commerciale motori di ricerca: digitare ".NET" in Bing produce un elenco di condizioni di licenza per ".NET 4.5", ".NET Framework 3,5" e così via. Quando si usa il servizio di ricerca API REST, l'implementazione di suggerimenti in un'applicazione di ricerca di Azure personalizzata richiede le operazioni seguenti:

- Attivare i suggerimenti aggiungendo una costruzione **suggester** dell'indice di fornisce il nome, la modalità di ricerca e un elenco di campi per il quale viene richiamato incrementale. Ad esempio, se si specifica "cityName" come un campo di origine, digitando una stringa di ricerca parziale di "Mare" genereranno "Seattle", "Spiaggia" e "Seatac" (tutti e tre sono i nomi di città effettivo) come i suggerimenti di query a disposizione dell'utente.

- Richiamare suggerimenti chiamando l' [API suggerimenti](#Suggestions) nel codice dell'applicazione. In genere stringhe di ricerca parziale vengono inviate al servizio mentre l'utente digita una query di ricerca e l'API restituisce un set di frasi suggerite.

In questo articolo viene spiegato come configurare un **suggester**. Inoltre, è necessario esaminare [i suggerimenti di API](#Suggestions) per informazioni dettagliate sull'utilizzo di un suggester.

**Uso**

`Suggesters`vengono create nell'indice e lavoro migliore quando viene utilizzata per suggerire documenti specifici anziché separati parole o frasi specifiche. I campi candidati migliori sono titoli, nomi e gli altri relativamente brevi frasi che è possono identificare un elemento. Meno efficaci sono campi ripetitivi, ad esempio categorie e tag, o campi molto lunghi, ad esempio campi descrizioni o commenti.

Come parte della definizione di indice, è possibile aggiungere un singolo suggester per il `suggesters` insieme. Proprietà che definiscono un suggester sono i seguenti:

- `name`: Il nome della suggester. Utilizza il nome della suggester quando si chiama la `suggest` API.
- `searchMode`: La strategia utilizzata per la ricerca di candidati. Modalità di sola attualmente supportate è `analyzingInfixMatching`, che esegue la ricerca flessibile di frasi all'inizio o al centro frasi.
- `sourceFields`: Un elenco di uno o più campi che sono l'origine di contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. Solo i campi che non dispone di un analizzatore lingue personalizzate impostare possono essere utilizzati.

**Esempio di suggester**

Un suggester fa parte dell'indice. Sola suggester possono essere presenti nel `suggesters` insieme nella versione corrente, insieme all'insieme di campi e `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Se è stata utilizzata la versione di anteprima pubblica di ricerca di Azure, `suggesters` sostituisce una proprietà booleana precedenti (`"suggestions": false`) che supportato solo prefisso suggerimenti per stringhe brevi (3-25 caratteri). La sostituzione, `suggesters`, supporta infisso corrispondenti che consente di trovare termini corrispondenti all'inizio o al centro di contenuto del campo, con tolleranza migliore per gli errori nelle stringhe di ricerca. A partire dalla versione disponibile in genere, questa è l'unica implementazione di suggerimenti API. Le versioni precedenti `suggestions` proprietà che è stata introdotta in `api-version=2014-07-31-Preview` continua a funzionare nella versione precedente, ma non funziona nel `2015-02-28` o versioni successive di ricerca di Azure.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Aggiorna indice

È possibile aggiornare un indice esistente all'interno di ricerca di Azure usando una richiesta HTTP inserire. Gli aggiornamenti possono includere l'aggiunta di nuovi campi allo schema esistente, la modifica delle opzioni CORS e la modifica dei profili punteggio. Per ulteriori informazioni, vedere [aggiungere i profili di classificazione](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Specificare il nome dell'indice per l'aggiornamento su richiesta URI:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importanti:** Supporto per gli aggiornamenti dello schema di indice è limitato alle operazioni che non richiedono ricostruire l'indice di ricerca. Tutti gli aggiornamenti dello schema che richiedono la reindicizzazione, ad esempio la modifica di tipi di campi, non sono attualmente supportati. Nuovi campi possono essere aggiunti in qualsiasi momento, anche se non è possibile modificare o eliminare i campi esistenti. Lo stesso vale per `suggesters`. I nuovi campi possono essere aggiunti a un suggester al momento vengono aggiunti i campi, ma non è possibile rimuovere i campi da `suggesters` e campi esistenti non possono essere aggiunti a `suggesters`.

Quando si aggiunge un nuovo campo a un indice, tutti i documenti esistenti nell'indice analitico avrà automaticamente un valore null per quel campo. Verrà utilizzato nessuno spazio di archiviazione aggiuntivo fino a quando non vengono aggiunti nuovi documenti per l'indice.

**Richiesta**

HTTPS è necessario per tutte le richieste di assistenza. La richiesta **Aggiorna indice** viene creata utilizzando HTTP inserire. Con caricamento, il nome dell'indice fa parte dell'URL. Se non è presente l'indice, viene creato. Se l'indice esiste già, viene aggiornato alla nuova definizione.

Il nome dell'indice deve essere minuscola, iniziare con una lettera o un numero, ha senza barre o punti e inferiore a 128 caratteri. Dopo il nome dell'indice a partire da una lettera o un numero, il resto del nome è possibile includere qualsiasi lettera, numero e trattini, purché i trattini non sono consecutivi.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `Content-Type`: Obbligatorio. Impostare questa opzione`application/json`
- `api-key`: Obbligatorio. Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta **Aggiorna indice** deve includere un `api-key` intestazione impostare la chiave di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Nella sintassi corpo della convocazione**

Quando si aggiorna un indice esistente, corpo deve includere la definizione dello schema originale, più i nuovi campi che si stanno aggiungendo, nonché i profili punteggio modificati, suggesters e CORS opzioni, se presenti. Se non si desidera modificare le opzioni CORS e il punteggio profili, è necessario includere originali dalla creazione dell'indice. In generale il modello migliore da utilizzare per gli aggiornamenti sia per recuperare la definizione di indice con un GET, modificarlo e quindi aggiornare con caricamento.

La sintassi di schema utilizzata per creare un indice riprodotta qui per semplicità di utilizzo. Per ulteriori informazioni, vedere [Create Index](#CreateIndex) .

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Risposta**

Per una richiesta riuscita: "204 Nessun contenuto".

Per impostazione predefinita la risposta sarà vuoto. Tuttavia, se la `Prefer` richiesta intestazione è impostata su `return=representation`, il corpo della risposta conterrà JSON per la definizione di indice che è stata aggiornata. In questo caso, il codice di stato di successo sarà "200 OK".

**Aggiornamento della definizione di indice con analisi personalizzati**

Dopo aver definito un analizzatore, un tokenizer, un token filtro o un carattere, non possono essere modificata. Nuovi file possono essere aggiunti a un indice esistente solo se la `allowIndexDowntime` è impostato su true nella richiesta di aggiornamento indice: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Si noti che questa operazione inserirà l'indice per almeno alcuni secondi, causa l'indicizzazione e le richieste di query esito negativo. La disponibilità di scrittura e le prestazioni dell'indice può essere vista per alcuni minuti dopo l'aggiornamento dell'indice o allungare per gli indici di dimensioni molto grandi.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Indici di elenco

L'operazione **Elenco indici** restituisce un elenco degli indici attualmente nel servizio di ricerca di Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

HTTPS è necessario per tutte le richieste di assistenza. È possibile creare la richiesta di **Elenco indici** utilizzando il metodo GET.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Obbligatorio. Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Indici elenco** deve includere un `api-key` impostato su un tasto di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

Ecco un corpo di risposta di esempio:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Si noti che è possibile filtrare la risposta per difetto a solo le proprietà che interessa. Ad esempio, se si desidera solo un elenco di nomi di indice, utilizzare OData `$select` opzione di query:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

In questo caso, la risposta dell'esempio precedente risulta come segue:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Questa è una tecnica utile per salvare la larghezza di banda se si dispone di un numero eccessivo di indici nel servizio di ricerca.

<a name="GetIndex"></a>
## <a name="get-index"></a>Ottenere indice

L'operazione di **Ottenere indice** Ottiene la definizione di indice dalla ricerca Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

HTTPS è richiesto per le richieste di servizi. È possibile creare la richiesta di **Ottenere indice** utilizzando il metodo GET.

[Nome indice] nella richiesta URI specifica indice per restituire l'insieme di indici.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Ottenere indice** deve includere un `api-key` impostato su un tasto di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

Vedere l'esempio JSON nella [creazione e aggiornamento di un indice](#CreateUpdateIndexExample) per un esempio di payload risposta.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Eliminare indice

L'operazione di **Eliminazione indice** rimuove un indice e documenti associati dal servizio di ricerca di Azure. È possibile ottenere il nome dell'indice nel dashboard di servizio nel portale di Azure o dall'API. Per informazioni dettagliate, vedere [Elenco indici](#ListIndexes) .

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

HTTPS è richiesto per le richieste di servizi. È possibile creare la richiesta di **Eliminare indice** utilizzando il metodo di eliminazione.

[Nome indice] nella richiesta URI specifica indice da eliminare dalla raccolta indici.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Obbligatorio. Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **Eliminare indice** deve includere un `api-key` intestazione impostare la chiave di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 204 Nessun contenuto viene restituito per ottenere una risposta corretta.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Ottenere le statistiche di indice analitico

L'operazione di **Ottenere statistiche dell'indice** restituisce dalla ricerca Azure un numero di documenti per indice corrente e l'uso di spazio di archiviazione.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Statistiche sulla dimensione conta. numeri e lo spazio di archiviazione di documenti sono stati raccolti alcuni minuti, non in tempo reale. Di conseguenza, le statistiche restituite da questa API potrebbero non riflettere modifiche dovuti operazioni di indicizzazione di recente.

**Richiesta**

HTTPS è necessario per tutte le richieste di servizi. Utilizzando il metodo GET, è possibile creare la richiesta di **Ottenere statistiche dell'indice** .

[Nome indice] nella richiesta URI indica il servizio per restituire le statistiche di indice per l'indice specificato.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Ottenere statistiche dell'indice** deve includere un `api-key` impostato su un tasto di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

Corpo della risposta è nel formato seguente:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Analizzatore test

**Analizzare API** Mostra come un analizzatore suddivide il testo in token.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta**

HTTPS è necessario per tutte le richieste di servizi. È possibile creare la richiesta di **Analizzare API** utilizzando il metodo POST.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Analizzare API** deve includere un `api-key` impostato su un tasto di amministratore (anziché una chiave di query).

È necessario anche il nome dell'indice e il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

o

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

Il `analyzer_name`, `tokenizer_name`, `token_filter_name` e `char_filter_name` devono essere nomi validi di analisi predefiniti o personalizzati, tokenizer, token filtri e filtri char per l'indice. Per ulteriori informazioni sul processo di analisi lessicale, vedere [analisi dei risultati di ricerca di Azure](https://aka.ms/azsanalysis).

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

Corpo della risposta è nel formato seguente:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analizzare esempio API**

**Richiesta**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Risposta**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Operazioni di documento

Risultati di ricerca di Azure, un indice archiviato nel cloud e compilato utilizzando i documenti JSON caricare al servizio. Tutti i documenti che si carica comprendono corpus dei dati di ricerca. I documenti conterranno campi, alcune delle quali sono suddiviso in token in termini di ricerca come vengono caricati. Il `/docs` segmento URL dell'API di ricerca di Azure rappresenta l'insieme di documenti in un indice. Tutte le operazioni eseguite nella raccolta, ad esempio durante il caricamento, l'unione, l'eliminazione o query documenti Scrivi posizionare nel contesto di un indice analitico, pertanto gli URL per queste operazioni inizia sempre con `/indexes/[index name]/docs` un nome di indice specificato.

Il codice dell'applicazione deve generare documenti JSON per caricare in Azure ricerca oppure è possibile utilizzare un [indicizzatore](https://msdn.microsoft.com/library/dn946891.aspx) per caricare documenti, se l'origine dati Database SQL Azure o DocumentDB. In genere, gli indici vengano popolati da un singolo set di dati che forniscono.

È consigliabile sulla disponibilità di un documento per ogni elemento che si desidera cercare. Un'applicazione di noleggio filmato potrebbe avere un documento per filmato, un'applicazione vetrina potrebbe avere un documento per SKU, un'applicazione di materiale didattico online può avere un documento per corso, una società di ricerca possono hanno un documento per ogni documento accademico in loro repository e così via.

I documenti sono costituiti da uno o più campi. Campi possono contenere valori di testo che viene suddiviso in token dal servizio di ricerca di Azure in termini di ricerca, nonché non suddiviso in token o non di testo che possono essere usati in filtri o profili punteggio. I nomi, tipi di dati e funzionalità di ricerca supportate per ogni campo dipendono dallo schema di indice. Uno dei campi di ogni schema di indice deve essere designato come un ID e ogni documento deve essere presente un valore per il campo ID che identifica il documento nell'indice analitico. Tutti gli altri campi di documento sono facoltativi e per impostazione predefinita un valore null se non viene specificato a sinistra. Si noti che i valori null non occupano spazio nell'indice di ricerca.

È possibile caricare documenti, è necessario creato l'indice sul servizio. Per informazioni dettagliate su questo primo passaggio, vedere [Create Index](#CreateIndex) .

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Aggiunta, aggiornamento o eliminare documenti

È possibile caricare, documenti di stampa unione, unione o carica o Elimina da un indice specificato utilizzando HTTP POST. Per un numero elevato di aggiornamenti, è consigliabile il batch di documenti con un massimo di circa 16 MB per il batch o (1000 documenti per il batch).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta**

HTTPS è necessario per tutte le richieste di assistenza. È possibile caricare, documenti di stampa unione, unione o carica o Elimina da un indice specificato utilizzando HTTP POST.

URI della richiesta include [nome indice], specificando l'indice per registrare i documenti. È possibile registrare solo i documenti da un indice alla volta.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `Content-Type`: Obbligatorio. Impostare questa opzione`application/json`
- `api-key`: Obbligatorio. Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **Aggiungere documenti** deve includere un `api-key` intestazione impostare la chiave di amministratore (anziché una chiave di query).

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Il corpo della convocazione contiene uno o più documenti da indicizzare. I documenti sono identificati da una chiave univoca. Ogni documento è associato a un'azione: upload, stampa unione, mergeOrUpload o Elimina. Le richieste di caricamento devono includere i dati del documento come un insieme di coppie di parole chiave/valore.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Tasti di documento possono contenere solo lettere, numeri, trattini ("-"), caratteri di sottolineatura ("_") e il segno di uguale ("="). Per ulteriori informazioni, vedere [regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Azioni documenti**

- `upload`: Un'azione di caricamento è simile a un "upsert" in cui verrà inserito il documento in caso nuove e aggiornate sostituito se esistente. Si noti che tutti i campi vengono sostituiti nel caso di aggiornamento.
- `merge`: Stampa unione aggiorna un documento esistente con i campi specificati. Se il documento non esiste, l'unione avrà esito negativo. Qualsiasi campo che specificare in una composizione sostituirà il campo esistente nel documento. Sono inclusi i campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo "tag" con valore `["budget"]` e si esegue una stampa unione con valore `["economy", "pool"]` per "tag", il valore del campo "tag" finale sarà `["economy", "pool"]`. Verrà **non** essere `["budget", "economy", "pool"]`.
- `mergeOrUpload`: si comporta come `merge` se esiste già un documento con il tasto specificato nell'indice analitico. Se il documento non esiste si comporta come `upload` con un nuovo documento.
- `delete`: Elimina rimuove il documento specificato dall'indice. Si noti che tutti i campi specificare un `delete` operazione diversa da quella sul campo chiave verrà ignorati. Se si desidera rimuovere un singolo campo da un documento, utilizzare `merge` invece ed è sufficiente impostare il campo in modo esplicito `null`.

**Risposta**

Codice di stato 200 (OK) viene restituito per ottenere una risposta corretta, vale a dire che tutti gli elementi sono stati indicizzati. In questo caso viene il `status` proprietà viene impostata su true per tutti gli elementi, nonché come il `statusCode` impostata su 201 (per i documenti appena caricati) o 200 (per i documenti uniti o eliminati):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Codice di stato 207 (Multi-Status) viene restituito quando almeno un elemento non è stato indicizzato correttamente. Gli elementi che non sono stati indicizzati hanno la `status` campo è impostato su false. Il `errorMessage` e `statusCode` proprietà motivo dell'errore indicizzazione:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

Nella tabella seguente sono descritti i diversi codici di stato di singoli documenti che possono essere restituiti nella risposta. Nota alcune indicare problemi con la richiesta, mentre altre indicano le condizioni di errore temporaneo. Quest'ultimo che si consiglia di riprovare dopo un periodo di tempo.

<table style="font-size:12">
    <tr>
        <th>Codice di stato</th>
        <th>Significato</th>
        <th>Non irreversibile</th>
        <th>Note</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Documento è stato modificato o eliminato.</td>
        <td>n/d</td>
        <td>Le operazioni di eliminazione siano <a href="https://en.wikipedia.org/wiki/Idempotence">idempotenti</a>. Anche se non esiste una chiave di documento nell'indice analitico, vale a dire si tenta di un'operazione di eliminazione con la chiave verrà generato un codice di 200 stato.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Documento è stato creato correttamente.</td>
        <td>n/d</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Si è verificato un errore in un documento che impediscono l'indicizzazione.</td>
        <td>No</td>
        <td>Il messaggio di errore nella risposta indica qual è il problema con il documento.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Il documento non è stato unito perché la chiave specificata non esiste nell'indice analitico.</td>
        <td>No</td>
        <td>Questo messaggio di errore non viene eseguita per i caricamenti dal momento che creano nuovi documenti e non viene generato per le eliminazioni sono <a href="https://en.wikipedia.org/wiki/Idempotence">idempotenti</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Quando si tenta di indicizzare un documento, è stato rilevato un conflitto di versione.</td>
        <td>Sì</td>
        <td>Questo problema può verificarsi quando si sta tentando di indicizzare più di una sola volta contemporaneamente allo stesso documento.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>L'indice è temporaneamente non disponibile perché è stato aggiornato con il flag 'allowIndexDowntime' impostato su "true".</td>
        <td>Sì</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Servizio di ricerca è temporaneamente non disponibile, probabilmente a causa di carico elevato.</td>
        <td>Sì</td>
        <td>Il codice di attesa prima di inviare di nuovo in questo caso o si corre il rischio prolungamento indisponibilità del servizio.</td>
    </tr>
</table> 

**Nota**: se il codice client rileva spesso una risposta 207, è possibile che il sistema ha in condizioni di carico. È possibile verificarlo controllando il `statusCode` proprietà per 503. In questo caso, è consigliabile ***la limitazione delle richieste di indicizzazione***. In caso contrario, se l'indicizzazione il traffico non diminuisce, Impossibile avviare il sistema di rifiutare tutte le richieste con 503 errori.

Codice di stato 429 indica che è stato superato la quota al numero di documenti per indice. Creare un nuovo indice o aggiornare i limiti di capacità superiori.

**Esempio:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Cerca documenti

Un'operazione di **ricerca** viene visualizzata come una richiesta GET o POST e specifica i parametri che forniscono i criteri per la selezione di documenti corrispondenti.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando utilizzare POST anziché GET**

Quando si utilizza HTTP GET per la **ricerca** dell'API di chiamata, è necessario tenere presente che la lunghezza dell'URL della richiesta non può superare 8 KB. Si tratta in genere sufficiente per la maggior parte delle applicazioni. Tuttavia, alcune applicazioni producono query di grandi o le espressioni di filtro OData. Queste applicazioni utilizzando HTTP POST è preferibile perché consente maggiore filtri e query più GET. Con l'opzione Invia, il numero di termini o clausole in una query è il fattore di limitazione, non la dimensione della query non elaborata poiché il limite di dimensioni richieste per POST è di circa 16 MB.

> [AZURE.NOTE] Anche se il limite di dimensioni richieste POST è molto grande, le query di ricerca e le espressioni di filtro non possono essere arbitrariamente complesse. Per ulteriori informazioni sulle limitazioni di complessità query e filtro di ricerca, vedere [Lucene la sintassi della query](https://msdn.microsoft.com/library/mt589323.aspx) e [sintassi delle espressioni OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Richiesta**

HTTPS è richiesto per le richieste di servizi. È possibile creare la richiesta di **ricerca** utilizzando i metodi GET o POST.

URI della richiesta specifica indice a query per tutti i documenti che corrispondono ai parametri. I parametri vengono specificati nella stringa di query in caso di richieste GET e nella richiesta richiede corpo nel caso dei POST.

Come ottimale quando si creano le richieste GET, ricordarsi di [codifica URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) parametri query specifica quando si chiama API REST direttamente. Per le operazioni di **ricerca** , inclusi:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

La codifica URL è consigliata solo sui parametri di query indicati. Se si inavvertitamente codifica URL stringa di query completa (tutti gli elementi dopo il?), le richieste verranno interrotte.

Inoltre, la codifica URL è necessaria solo quando si chiama API REST direttamente mediante GET. La codifica URL non è necessaria quando si chiama **ricerca** usando POST o quando si utilizza la [libreria client .NET](https://msdn.microsoft.com/library/dn951165.aspx), che consente di gestire la codifica URL dell'utente.

<a name="SearchQueryParameters"></a>
**Parametri query**

**Ricerca** accetta diversi parametri che forniscono i criteri di query e anche specificare il comportamento di ricerca. Fornire che questi parametri nell'URL stringa di query quando si chiama **ricerca** tramite GET e come proprietà JSON nel corpo della richiesta quando si chiama **ricerca** tramite POST. La sintassi per alcuni parametri è leggermente diversa tra GET e POST. Queste differenze sono indicate come applicabili sotto:

`search=[string]`(facoltativo) - il testo da cercare. Tutti `searchable` campi vengono eseguita la ricerca per impostazione predefinita, a meno che non `searchFields` specificato. Durante la ricerca `searchable` viene suddiviso in token campi, il testo di ricerca, in modo più termini possono essere separate da uno spazio vuoto (ad esempio: `search=hello world`). Per trovare qualsiasi termine, utilizzare `*` (può essere utile per le query di filtro booleano). Se si omette questo parametro ha lo stesso effetto dell'impostazione per `*`. Per informazioni sulla sintassi di ricerca, vedere [Semplice sintassi della Query](https://msdn.microsoft.com/library/dn798920.aspx) .

  - **Nota**: I risultati in alcuni casi possono essere sorprendenti quando eseguire una query sul `searchable` campi. I tokenizer include la logica per gestire i casi comuni per il testo inglese come apostrofi, virgole di numeri e così via. Ad esempio `search=123,456` corrisponderanno a un singolo termine 123,456 anziché i singoli termini 123 e 456, dal momento che le virgole vengono utilizzate come separatori delle migliaia per i numeri di grandi dimensioni in lingua inglese. Per questo motivo, è consigliabile usare uno spazio vuoto anziché punteggiatura per separare i termini nel `search` parametro.

`searchMode=any|all`(facoltativo, per impostazione predefinita `any`) - se uno o tutti i termini di ricerca deve corrispondere per contare il documento come una corrispondenza.

`searchFields=[string]`(facoltativo) - l'elenco di nomi di campo separati da virgola per cercare il testo specificato. Campi di destinazione devono essere contrassegnati come `searchable`.

`queryType=simple|full`(facoltativo, per impostazione predefinita `simple`): quando è impostata su testo da cercare "semplice" viene interpretato utilizzando un linguaggio di query semplice che consente di simboli, ad esempio +, * e "". Le query vengono valutate in tutti i campi che supportano le ricerche (o campi indicati `searchFields`) in ogni documento per impostazione predefinita. Quando il tipo di query è impostato su `full` eseguire ricerche nel testo verrà interpretato mediante il linguaggio di query Lucene che consente di ricerche specifiche del campo e ponderate. Per informazioni dettagliate sulla sintassi di ricerca, vedere [Sintassi della Query semplice](https://msdn.microsoft.com/library/dn798920.aspx) e [Lucene la sintassi della Query](https://msdn.microsoft.com/library/mt589323.aspx) . 
 
> [AZURE.NOTE] Intervallo di ricerca in Lucene linguaggio di query non è supportato a favore $filter che offre una funzionalità simile.

`moreLikeThis=[key]`(facoltativo) **Importanti:** Questa caratteristica è disponibile solo in `2015-02-28-Preview`. Questa opzione non è possibile utilizzare in una query che contiene il parametro di ricerca di testo, `search=[string]`. Il `moreLikeThis` parametro consente di trovare documenti che sono simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generato un elenco dei termini di ricerca in base alla frequenza e dei termini nel documento di origine. Tali termini vengono quindi utilizzati per eseguire la richiesta. Per impostazione predefinita, il contenuto di tutte `searchable` campi sono considerati a meno che non `searchFields` viene utilizzato per limitare la ricerca vengono eseguiti i campi.  

`$skip=#`(facoltativo) - il numero di risultati di ricerca da ignorare; Non può essere maggiore di 100.000. Se è necessario digitalizzare i documenti in sequenza ma non è possibile usare `$skip` a causa di limitazioni, si consiglia di utilizzare `$orderby` su una chiave completamente ordinati e `$filter` con un intervallo di query alternativa.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `skip` anziché `$skip`.

`$top=#`(facoltativo) - il numero di risultati di ricerca per recuperare. Può essere utilizzato in combinazione con `$skip` per implementare lato client suddivisione in pagine dei risultati della ricerca.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `top` anziché `$top`.

`$count=true|false`(facoltativo, per impostazione predefinita `false`)-specifica se recuperare il numero totale di risultati. Indica il conteggio di tutti i documenti che soddisfano la `search` e `$filter` parametri, ignorando `$top` e `$skip`. Impostare questo valore su `true` abbia un impatto sulle prestazioni. Si noti che il conteggio restituito un'approssimazione.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `count` anziché `$count`.

`$orderby=[string]`(facoltativo) - un elenco di espressioni separati da virgola per ordinare i risultati in base. Ogni espressione può essere un nome di campo o una chiamata per il `geo.distance()` funzione. Ogni espressione può essere seguito da `asc` indicare crescente, e `desc` per indicare decrescente. Il valore predefinito è crescente. Verrà suddiviso TIES per i punteggi corrispondenza dei documenti. Se non `$orderby` specificato, l'ordinamento predefinito è decrescente per punteggio Confronta documenti. È previsto un limite di 32 clausole per `$orderby`.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `orderby` anziché `$orderby`.

`$select=[string]`(facoltativo) - un elenco di campi separati da virgola da recuperare. Se non viene specificato, sono inclusi tutti i campi contrassegnati come recuperabili nello schema. È possibile richiedere esplicitamente tutti i campi da impostandolo su `*`.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `select` anziché `$select`.

`facet=[string]`(zero o più) - un campo a facet da. Se lo si desidera la stringa potrebbe contenere i parametri per personalizzare faceting espresso come separati da virgola `name:value` coppie di parole. Parametri validi sono:

- `count`(max numerosi termini facet; il valore predefinito è 10). Non esiste alcun valore massimo, ma i valori più alti una riduzione corrispondente prestazioni, in particolare se il campo in base a facet contiene un numero elevato di termini univoci.
  - Ad esempio: `facet=category,count:5` Ottiene le categorie di cinque nei risultati facet.  
  - **Nota**: se il `count` parametro è inferiore al numero di termini univoci, i risultati potrebbero non essere accurati. Ciò è dovuto la modalità query faceting sono distribuite in shards. Aumento `count` generalmente aumenta la precisione dei termini conteggi, ma al costo delle prestazioni.
- `sort`(uno dei `count` di ordinamento *decrescente* in base a numero, `-count` di ordinamento *crescente* dal conteggio, `value` di ordinamento *crescente* per valore, o `-value` di ordinamento *decrescente* per valore)
  - Ad esempio: `facet=category,count:3,sort:count` Ottiene le categorie le prime tre nei risultati facet in ordine decrescente per il numero di documenti con ogni nome di città. Ad esempio, se le prime tre categorie sono preventivo, modelli e lusso e Budget ha 5 accessi, modelli deve 6 e 4 sono previsti lusso, quindi di intervalli sarà nell'ordine modelli, Budget, lusso.
  - Ad esempio: `facet=rating,sort:-value` produce gli intervalli per tutte le classificazioni possibili, in ordine decrescente per valore. Ad esempio, se le valutazioni sono da 1 a 5, gli intervalli verranno ordinati 5, 4, 3, 2, 1, indipendentemente dal numero di documenti corrisponda a ogni valutazione.
- `values`(delimitato da barra verticale numerico o `Edm.DateTimeOffset` valori che specifica un set dinamico di valori di voce faccia)
  - Ad esempio: `facet=baseRate,values:10|20` produce gli intervalli di tre: uno per tasso base 0 fino a ma non inclusi fino a 10, uno per 10, ma non inclusi 20 e uno per 20 o versione successiva.
  - Ad esempio: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produce gli intervalli di due: uno per alberghi ristrutturati prima di febbraio 2010 e uno per alberghi ristrutturati febbraio 1o, 2010 o versione successiva.
- `interval`(numero intero intervallo maggiore di 0 per i numeri, o `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` valori di data ora)
  - Ad esempio: `facet=baseRate,interval:100` produce gli intervalli in base a intervalli di frequenza base delle dimensioni 100. Ad esempio, se tassi di base sono tra $60 e $600, verrà gli intervalli per 0-100, 100 e 200, 200-300, 300-400, 400-500 e 500 600.
  - Ad esempio: `facet=lastRenovationDate,interval:year` produce un intervallo per ogni anno quando alberghi sono state ristrutturati.
- `timeoffset`([+-] hh.mm, [+-] hhmm, o [+-] hh) `timeoffset` è facoltativo. Solo può essere combinato con la `interval` opzione e solo se vengono applicati a un campo di tipo `Edm.DateTimeOffset`. Il valore specifica la differenza di orario UTC account nell'impostazione limiti di tempo.
  - Ad esempio: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` utilizza il limite di giorno che inizia in UTC 01:00:00 (mezzanotte nel fuso orario di destinazione)
- **Nota**: `count` e `sort` può essere combinato nella stessa specifica facet, ma non può essere combinati con `interval` o `values`, e `interval` e `values` non è possibile combinare.
- **Nota**: vengono calcolati facet intervallo entro i tempi di data in base a ora UTC se `timeoffset` non viene specificato. Ad esempio: per `facet=lastRenovationDate,interval:day`, il giorno limite compreso 00:00:00 UTC. 

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `facets` anziché `facet`. Inoltre, è specificare come matrice JSON di stringhe in cui ogni stringa è un'espressione facet separata.

`$filter=[string]`(facoltativo) - un'espressione di ricerca strutturati in sintassi OData standard. Per informazioni su sottoinsieme della grammatica espressione OData che supporta la ricerca di Azure, vedere [La sintassi delle espressioni OData](#ODataExpressionSyntax) .

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `filter` anziché `$filter`.

`highlight=[string]`(facoltativo) - un set di nomi dei campi separati da virgola utilizzati riscontro evidenzia. Solo `searchable` campi possono essere utilizzati per l'evidenziazione.

`highlightPreTag=[string]`(facoltativo, per impostazione predefinita `<em>`) - un tag che consente di anteporre per raggiunto dei punti essenziali di stringa. Devono essere impostate con `highlightPostTag`.

> [AZURE.NOTE] Quando si chiama **ricerca** usando GET, caratteri riservati nell'URL deve essere codifica percentuale (ad esempio % 23 anziché #).

`highlightPostTag=[string]`(facoltativo, per impostazione predefinita `</em>`)-un tag di stringa che aggiunge per raggiunto dei punti essenziali. Devono essere impostate con `highlightPreTag`.

> [AZURE.NOTE] Quando si chiama **ricerca** usando GET, caratteri riservati nell'URL deve essere codifica percentuale (ad esempio % 23 anziché #).

`scoringProfile=[string]`(facoltativo) - il nome di un profilo punteggio da valutare corrispondere punteggi per abbinare i documenti per ordinare i risultati.

`scoringParameter=[string]`(zero o più) - indica i valori per ogni parametro definito in una funzione punteggio (ad esempio `referencePointParameter`) utilizzando il formato `name-value1,value2,...`.

- Ad esempio, se il profilo punteggio definisce una funzione con un parametro denominato "percorso" l'opzione stringa di query è `&scoringParameter=mylocation--122.2,44.8`. Il trattino prima separa il nome dell'elenco di valori, mentre il secondo trattino fa parte del primo valore (longitudine in questo esempio).
- Punteggio dei parametri come tag incrementare che possono contenere punti e virgola, è possibile escape qualsiasi tali valori nell'elenco utilizzando tra virgolette singole. Se i valori contengono tra virgolette singole, è possibile utilizzare caratteri di escape raddoppiare.
  - Ad esempio, se si dispone di un contrassegno incrementare parametro denominato "mytag" e si desidera promuovere il tag valori "Salve, Brien" e "Rossi", la query stringa opzione potrebbe essere `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Si noti che le offerte sono necessari per i valori che contengono le virgole.

> [AZURE.NOTE] Quando si chiama **ricerca** usando POST, questo parametro è denominato `scoringParameters` anziché `scoringParameter`. Inoltre, specificare come matrice JSON di stringhe in cui ogni stringa è un separato `name-values` eseguire l'associazione.

`minimumCoverage`(facoltativo, il valore predefinito è 100)-un numero compreso tra 0 e 100 che indica la percentuale dell'indice deve essere coperta da una query di ricerca in ordine per la query per essere segnalato come successo. Per impostazione predefinita, l'intera indice deve essere disponibile o `Search` restituirà il codice di stato HTTP 503. Se si imposta `minimumCoverage` e `Search` ha avuto esito positivo, verrà restituito HTTP 200 e includere un `@search.coverage` valore nella risposta che indica la percentuale dell'indice che è stato incluso nella query.

> [AZURE.NOTE] Se questo parametro su un valore inferiore a 100 può essere utile per garantire la disponibilità di ricerca anche per i servizi con un solo duplicata. Tuttavia, non tutti i documenti corrispondenti vengono garantiti per essere presenti nei risultati della ricerca. Se il richiamo di ricerca è più importante a un'applicazione di disponibilità, quindi è consigliabile lasciare `minimumCoverage` il valore predefinito di 100.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Nota: per questa operazione, il `api-version` specificato come un parametro di query nell'URL indipendentemente dal fatto che si chiama **ricerca** con GET o POST.

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per l'URL del servizio. Richiesta di **ricerca** è possibile specificare un amministratore chiave o una chiave di query per `api-key`.

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Per ottenere: Nessuno.

Per i POST:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuazione delle risposte di ricerca parziale**

In alcuni casi Azure ricerca non può restituire i risultati della richieste in una singola risposta di ricerca. Questo problema può verificarsi per diversi motivi, ad esempio quando la query richiede troppi documenti non specificando `$top` o specificando un valore per `$top` è troppo grande. In tal caso, Azure ricerca includerà le `@odata.nextLink` annotazione nel corpo della risposta, nonché `@search.nextPageParameters` se si trattasse di una richiesta POST. È possibile utilizzare i valori di tali annotazioni per formulare un'altra richiesta di ricerca per ottenere la parte successiva della risposta della ricerca. Questo comando si chiama una ***continuazione*** della richiesta di ricerca originale e le annotazioni vengono in genere chiamate ***i token di continuazione***. Vedere [nell'esempio seguente](#SearchResponse) per informazioni dettagliate sulla sintassi di tali annotazioni e in cui sono visualizzati nel corpo della risposta. 

I motivi perché Azure ricerca potrebbe restituire i token di continuazione sono specifico dell'implementazione e soggetti a modifiche. Client affidabile deve sempre essere pronti a gestire i casi in cui vengono restituiti i documenti di un numero minore del previsto e un token di continuazione viene incluso per continuare il recupero dei documenti. Si noti inoltre che è necessario utilizzare lo stesso metodo HTTP della richiesta originale per continuare. Ad esempio, se è stata inviata una richiesta GET, tutte le richieste di continuazione inviati necessario utilizzare anche GET (e allo stesso modo per POST).

<a name="SearchResponse"></a>
**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Esempi:**

Nella pagina [OData sintassi delle espressioni per la ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) , è possibile trovare ulteriori esempi.

1)  Ricerca dell'indice ordinamento decrescente in base alla data.


    GET /indexes/hotels/docs? ricerca = * & $orderby = lastRenovationDate desc & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "*", "orderby": "lastRenovationDate desc"}

2)  In una ricerca in base a facet l'indice di ricerca e recuperare facet per categorie, classificazione, tag, nonché gli elementi con baseRate in intervalli specifici:


    GET /indexes/hotels/docs? ricerca = test & facet = categoria & facet = valutazione & facet = tag & facet = baseRate, i valori: 80 | 150 | 220 & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "test", "facet": ["categoria", "valutazione", "tag", "baseRate, i valori: 80 | 150 | 220"]}

3)  Utilizzando un filtro, restringere i risultati della query in base a facet precedente dopo l'utente fa clic su classificazione 3 e categoria "Modelli":


    GET /indexes/hotels/docs? ricerca = test & facet = tag & facet = baseRate, i valori: 80 | 150 | 220 & $filter = valutazione eq 3 e categoria eq 'modelli' & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "test", "facet": ["tag", "baseRate, i valori: 80 | 150 | 220"], "filtro": "valutazione eq 3 e categoria eq 'Modelli'"}

4) Risultati di una ricerca in base a facet, impostare un limite superiore termini univoci restituiti in una query. Il valore predefinito è 10, ma è possibile aumentare o diminuire questo valore utilizzando il `count` parametro nella `facet` attributo:


    GET /indexes/hotels/docs? ricerca = test & facet = città, conteggio: 5 e versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "test", "facet": ["città, conteggio: 5"]}

5)  Cercare l'indice all'interno di campi specifici; Ad esempio un campo specifiche della lingua:


    GET /indexes/hotels/docs? ricerca = hôtel & searchFields = description_fr & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "hôtel", "searchFields": "description_fr"}

6) Ricerca dell'indice in più campi. Ad esempio, è possibile archiviare e campi che supportano le ricerche in più lingue, tutto all'interno dello stesso indice delle query.  Se le descrizioni francese e inglese coesistano nello stesso documento, è possibile tornare a una o tutte nei risultati della query:


    GET /indexes/hotels/docs? ricerca = albergo & searchFields = descrizione, description_fr & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "albergo", "searchFields": "descrizione, description_fr"}

Si noti che è possibile richiedere solo un indice alla volta. Creare più indici per ogni lingua, a meno che non si prevede di eseguire una query una alla volta.

7)  Spostamento - ottenere la pagina 1 ° di elementi (dimensioni della pagina sono 10):


    GET /indexes/hotels/docs? ricerca = * & $skip = 0 & $top = 10 & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "*", "ignorare": 0, "superiore": 10}

8)  Spostamento - ottenere la pagina 2a di elementi (dimensioni della pagina sono 10):


    GET /indexes/hotels/docs? ricerca = * & $skip = 10 & $top = 10 & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "*", "ignorare": 10, "superiore": 10}

9)  Recuperare un determinato set di campi:


    GET /indexes/hotels/docs? ricerca = * & $select = hotelName, descrizione e versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "*", "select": "hotelName, descrizione"}

10)  Recuperare i documenti che soddisfano un'espressione di filtro specifici:


    GET /indexes/hotels/docs? $filter = (baseRate grandi 60 e baseRate lt 300) o eq hotelName 'Rimanere speciale' & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"filtro": "(baseRate grandi 60 e baseRate lt 300) hotelName eq"Restare speciale"o"}

11) Cercare i frammenti di indice e ritorno con evidenziazioni


    GET /indexes/hotels/docs? ricerca = qualcosa & evidenziare = descrizione & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "qualcosa", "evidenziare": "descrizione"}

12) L'indice di ricerca e restituire i documenti ordinati dalla posizione più vicina a più lontano da un riferimento


    GET /indexes/hotels/docs? ricerca = qualcosa & $orderby=geo.distance (percorso, geography'POINT(-122.12315 47.88121) ") & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "qualcosa", "orderby": "geo.distance (percorso, geography'POINT(-122.12315 47.88121)')"}

13) Eseguire una ricerca indice presupponendo che non esiste un profilo di punteggio denominato "geografico" con due funzioni punteggio distanza, che definisce un parametro denominato "currentLocation" e una definizione di un parametro denominato "lastLocation"


    GET /indexes/hotels/docs? ricerca = qualcosa & scoringProfile = geografico & scoringParameter = currentLocation - 122.123,44.77233 & scoringParameter = lastLocation - 121.499,44.2113 & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "qualcosa", "scoringProfile": "geografico", "scoringParameters": ["currentLocation - 122.123,44.77233", "lastLocation - 121.499,44.2113"]}

14) Trovare i documenti nell'indice analitico utilizzando [la sintassi della query semplice](https://msdn.microsoft.com/library/dn798920.aspx). La query seguente restituisce alberghi in campi che supportano le ricerche contengono le condizioni "comodamente" e "posizione" ma non "modelli":


    GET /indexes/hotels/docs? ricerca = comodamente + percorso-modelli & searchMode = all & versione api = anteprima 28 di 02 2015

    POST /indexes/hotels/docs/search? versione api = anteprima 28 di 02 2015 {"search": "comodamente + percorso-modelli", "searchMode": "tutti"}

Si noti l'utilizzo di `searchMode=all` sopra. Inclusione di questo parametro sostituisce il valore predefinito di `searchMode=any`, per garantire che `-motel` significa "AND NOT" anziché "OR NOT". Senza `searchMode=all`, viene visualizzato "OR NOT" che si espande anziché limita i risultati di ricerca e può essere poco intuitivo per alcuni utenti.

15) Trovare i documenti nell'indice analitico utilizzando [la sintassi della query lucene](https://msdn.microsoft.com/library/mt589323.aspx). Questa query restituisce alberghi nel punto in cui il campo categoria contenente il termine "budget" e tutti i campi che supportano le ricerche che contiene la frase "recente ristrutturata". Documenti che contiene la frase "recente ristrutturata" un livello superiore in seguito il valore di incremento termini (3)

    GET /indexes/hotels/docs?search = categoria: budget e \"recente ristrutturati\"^ 3 & searchMode = all & versione api = anteprima 28 di 02 2015 & querytype = completo

    POST /indexes/hotels/docs/search?api-version = anteprima 28 di 02 2015 {"search": "categoria: budget e \"recente ristrutturati\"^ 3", "queryType": "completo", "searchMode": "tutti"}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Documento di ricerca

L'operazione di **Ricerca documento** recupera un documento dalla ricerca Azure. Questo è utile quando si fa clic su un risultato di ricerca specifici e si desidera cercare dettagli specifici sul documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Richiesta**

HTTPS è richiesto per le richieste di servizi. Richiesta del **Documento di ricerca** può essere creata come indicato di seguito.

    GET /indexes/[index name]/docs/key?[query parameters]

In alternativa, è possibile utilizzare la sintassi di OData tradizionale per la ricerca chiave:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

URI della richiesta include [nome di un indice] e [chiave], che specifica il documento da recuperare dall'indice. È possibile ottenere solo un documento contemporaneamente. Usare la **ricerca** per ottenere più documenti in un'unica richiesta.

**Parametri query**

`$select=[string]`(facoltativo) - un elenco di campi separati da virgola da recuperare. Se non viene specificato o impostato su `*`, tutti i campi contrassegnati come recuperabili nello schema sono incluse nella proiezione.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Nota: per questa operazione, il `api-version` specificato come un parametro di query.

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per l'URL del servizio. Richiesta del **Documento di ricerca** è possibile specificare un amministratore chiave o una chiave di query per `api-key`.

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Esempio**

Ricerca documento dotata di tasto "2"

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Ricerca documento dotata di tasto "3" si sta usando OData sintassi:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Numero di documenti

L'operazione di **Conteggio documenti** recupera un conteggio del numero di documenti in un indice di ricerca. Il `$count` sintassi fa parte del protocollo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Richiesta**

HTTPS è richiesto per le richieste di servizi. È possibile creare la richiesta di **Conteggio documenti** utilizzando il metodo GET.

[Nome indice] nella richiesta URI indica il servizio per restituire il numero di tutti gli elementi nella raccolta documenti dell'indice specificato.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**Le intestazioni di richiesta**

L'elenco seguente descrive le intestazioni di richiesta obbligatori e facoltativi.

- `Accept`: Questo valore deve essere impostato su `text/plain`.
- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **Conteggio documenti** è possibile specificare un amministratore chiave o una chiave di query per `api-key`.

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Nessuno.

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

Corpo della risposta contiene il valore di conteggio come numero intero in formato testo normale.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Suggerimenti

L'operazione di **suggerimenti** recupera i suggerimenti in base all'input ricerca parziale. In genere utilizzato nelle caselle di ricerca per fornire i suggerimenti di ricerca incrementale come utenti immettere termini di ricerca.

Le richieste di suggerimento finalizzata che suggerisce documenti di destinazione, in modo che il testo può essere ripetuto se la stessa ricerca input corrispondono a più documenti candidati. È possibile utilizzare `$select` per recuperare gli altri campi di documento (inclusa la chiave di documento) in modo che è possibile stabilire quale documento è l'origine per ciascuno di essi.

Un'operazione di **suggerimenti** viene generata come una richiesta GET o POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando utilizzare POST anziché GET**

Quando si utilizza HTTP GET per chiamare i **suggerimenti** API, è necessario tenere presente che la lunghezza dell'URL della richiesta non può superare 8 KB. Si tratta in genere sufficiente per la maggior parte delle applicazioni. Tuttavia, alcune applicazioni producono query di grandi, in particolare le espressioni di filtro OData. Queste applicazioni utilizzando HTTP POST è preferibile ammette filtri maggiori rispetto a GET. Con l'opzione Invia, il numero di clausole in un filtro è il fattore di limitazione, non la dimensione della stringa di filtro non elaborato poiché il limite di dimensioni richieste per POST è di circa 16 MB.

> [AZURE.NOTE] Anche se il limite di dimensioni richieste POST è molto grande, le espressioni di filtro non possono essere arbitrariamente complesse. Per ulteriori informazioni sui limiti di complessità filtro, vedere [la sintassi delle espressioni OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Richiesta**

HTTPS è richiesto per le richieste di servizi. È possibile creare la richiesta di **suggerimenti** utilizzando i metodi GET o POST.

URI della richiesta specifica il nome dell'indice a query. Parametri, ad esempio il termine di ricerca parziale input, vengono specificati nella stringa di query in caso di richieste GET e nella richiesta richiede corpo nel caso dei POST.

Come ottimale quando si creano le richieste GET, ricordarsi di [codifica URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) parametri query specifica quando si chiama API REST direttamente. Per **i suggerimenti per le** operazioni, sono inclusi:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

La codifica URL è consigliata solo sui parametri di query indicati. Se si inavvertitamente codifica URL stringa di query completa (tutti gli elementi dopo il?), le richieste verranno interrotte.

Inoltre, la codifica URL è necessaria solo quando si chiama API REST direttamente mediante GET. La codifica URL non è necessaria quando si chiama **i suggerimenti** tramite POST o quando si utilizza la [libreria client .NET](https://msdn.microsoft.com/library/dn951165.aspx), che consente di gestire la codifica URL dell'utente.

**Parametri query**

**Suggerimenti** accetta diversi parametri che forniscono i criteri di query e anche specificare il comportamento di ricerca. Fornire che questi parametri nell'URL stringa di query quando si chiama **i suggerimenti** tramite GET e come proprietà JSON nel corpo della richiesta quando si chiama **i suggerimenti** tramite POST. La sintassi per alcuni parametri è leggermente diversa tra GET e POST. Queste differenze sono indicate come applicabili sotto:

`search=[string]`-il testo di ricerca da utilizzare per suggerire query. Devono essere almeno 1 carattere e non più di 100 caratteri.

`highlightPreTag=[string]`tag (facoltativo) - una stringa che consente di anteporre per risultati di ricerca. Devono essere impostate con `highlightPostTag`.

> [AZURE.NOTE] Quando si chiama **suggerimenti** utilizzando GET, caratteri riservati nell'URL deve essere codifica percentuale (ad esempio % 23 anziché #).

`highlightPostTag=[string]`tag (facoltativo) - una stringa che aggiunge per risultati di ricerca. Devono essere impostate con `highlightPreTag`.

> [AZURE.NOTE] Quando si chiama **suggerimenti** utilizzando GET, caratteri riservati nell'URL deve essere codifica percentuale (ad esempio % 23 anziché #).

`suggesterName=[string]`-il nome del suggester come specificato nel `suggesters` insieme che fa parte della definizione di indice. A `suggester` determina quali campi vengono analizzati per i termini di query suggerito. Per informazioni dettagliate, vedere [Suggesters](#Suggesters) .

`fuzzy=[boolean]`(facoltativo, predefinito = false)-se impostato su true questa API troverà suggerimenti anche se non esiste un carattere sostitutivo o mancante nel testo da cercare. Anche se fornisce un'esperienza migliore in alcuni casi si tratta sulle prestazioni come suggerimento sfocata ricerche sono lente e utilizzano altre risorse.

`searchFields=[string]`(facoltativo) - l'elenco di nomi di campo separati da virgola per cercare il testo di ricerca specificati. Campi di destinazione devono essere abilitati per i suggerimenti.

`$top=#`(facoltativo, predefinito = 5)-il numero di suggerimenti per recuperare. Deve essere un numero compreso tra 1 e 100.

> [AZURE.NOTE] Quando si chiama **i suggerimenti** tramite POST, questo parametro è denominato `top` anziché `$top`.

`$filter=[string]`(facoltativo) - un'espressione che filtra i documenti considerati per i suggerimenti.

> [AZURE.NOTE] Quando si chiama **i suggerimenti** tramite POST, questo parametro è denominato `filter` anziché `$filter`.

`$orderby=[string]`(facoltativo) - un elenco di espressioni separati da virgola per ordinare i risultati in base. Ogni espressione può essere un nome di campo o una chiamata per il `geo.distance()` funzione. Ogni espressione può essere seguito da `asc` indicare crescente, e `desc` per indicare decrescente. Il valore predefinito è crescente. È previsto un limite di 32 clausole per `$orderby`.

> [AZURE.NOTE] Quando si chiama **i suggerimenti** tramite POST, questo parametro è denominato `orderby` anziché `$orderby`.

`$select=[string]`(facoltativo) - un elenco di campi separati da virgola da recuperare. Se non viene specificato, viene restituito solo il testo del documento chiave e suggerimenti. È possibile richiedere esplicitamente tutti i campi da impostandolo su `*`.

> [AZURE.NOTE] Quando si chiama **i suggerimenti** tramite POST, questo parametro è denominato `select` anziché `$select`.

`minimumCoverage`(facoltativo, il valore predefinito è 80)-un numero compreso tra 0 e 100 che indica la percentuale dell'indice deve essere coperta da una query di suggerimenti in ordine per la query per essere segnalato come successo. Per impostazione predefinita, all'80% dell'indice deve essere disponibile o `Suggest` restituirà il codice di stato HTTP 503. Se si imposta `minimumCoverage` e `Suggest` ha avuto esito positivo, verrà restituito HTTP 200 e includere un `@search.coverage` valore nella risposta che indica la percentuale dell'indice che è stato incluso nella query.

> [AZURE.NOTE] Se questo parametro su un valore inferiore a 100 può essere utile per garantire la disponibilità di ricerca anche per i servizi con un solo duplicata. Tuttavia, non tutti i suggerimenti corrispondenti sono necessariamente presenti nei risultati. Se richiamo è più importante a un'applicazione di disponibilità, quindi è consigliabile non abbassare `minimumCoverage` sotto il valore predefinito 80.

`api-version=[string]`(obbligatorio). La versione di anteprima `api-version=2015-02-28-Preview`. Per informazioni dettagliate e versioni alternative, vedere [Controllo delle versioni del servizio di ricerca](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Nota: per questa operazione, il `api-version` specificato come un parametro di query nell'URL indipendentemente dal fatto che si chiama **suggerimenti** con GET o POST.

**Le intestazioni di richiesta**

Di seguito sono riportate le intestazioni di richiesta obbligatori e facoltativi

- `api-key`: Il `api-key` viene utilizzato per eseguire l'autenticazione la richiesta di servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **suggerimenti** è possibile specificare un amministratore chiave o una chiave di query come la `api-key`.

Occorre inoltre il nome del servizio per creare l'URL della richiesta. È possibile ottenere il nome del servizio e `api-key` dal dashboard del servizio nel portale di Azure. Vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) per gli spostamenti pagina Guida.

**Corpo della richiesta**

Per ottenere: Nessuno.

Per i POST:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Risposta**

Codice di stato: 200 OK viene restituito per ottenere una risposta corretta.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se l'opzione di proiezione viene utilizzata per recuperare i campi che sono incluse in ogni elemento della matrice:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Esempio**

Recuperare i suggerimenti di 5 in cui l'input di ricerca parziale è 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
