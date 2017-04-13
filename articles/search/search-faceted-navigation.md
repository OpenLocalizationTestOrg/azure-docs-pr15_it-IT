<properties 
    pageTitle="Come implementare lo spostamento in base a facet ricerca Azure | Microsoft Azure | categorie di navigazione ricerca" 
    description="Aggiungere l'esplorazione in base a facet a applicazioni che si integrano con ricerca Azure, un servizio di ricerca cloud ospitato in Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Come implementare lo spostamento in base a facet ricerca Azure

Spostamento in base a facet è un meccanismo di filtro che consente di spostarsi Attiva drill-down nelle applicazioni di ricerca. Il termine 'esplorazione in base a facet' potrebbe essere poco, ma è quasi un dato che è stata utilizzata una prima. Come illustrato nell'esempio seguente, in base a facet spostamento è semplicemente le categorie utilizzate per filtrare i risultati.

## <a name="what-it-looks-like"></a>L'aspetto

 ![][1]
  
Facet possono aiutare a trovare cosa si sta cercando, assicurandosi che non si riceverà zero risultati. Gli sviluppatori di facet consentono di esporre i criteri di ricerca più utili per lo spostamento tra i corpus di ricerca. Nelle applicazioni di rivendita online, in base a facet spostamento spesso è integrato marchi, i reparti (scarpe del bambino), dimensioni, prezzo, popolarità e valutazioni. 

Spostamento in base a facet l'implementazione varia tra le tecnologie di ricerca e può essere molto complessa. Risultati di ricerca di Azure, in base a facet spostamento integrato in fase di query, utilizzo con gli attributi dei campi precedentemente impostati nello schema. Nelle query che consente di creare l'applicazione, una query deve inviare *parametri query facet* per ricevere i valori di filtro facet disponibili per il set di risultati di documento. Tagliare effettivamente il risultato di documento impostato, l'applicazione è necessario applicare un `$filter` espressione.

In termini di sviluppo di applicazioni, scrivere codice che crea query costituisce la maggior parte del lavoro. Molti dei comportamenti dell'applicazione desiderata dalla struttura di spostamento in base a facet viene fornito dal servizio, incluso il supporto incorporato per la configurazione intervalli e ottenere conteggi per risultati ottimali facet. Il servizio include anche sensibile impostazioni predefinite che consentono di evitare strutture di spostamento difficile da gestire. 

In questo articolo contiene le sezioni seguenti:

- [Modalità di compilazione di esso](#howtobuildit)
- [Creare il livello di presentazione](#presentationlayer)
- [Creare l'indice analitico](#buildindex)
- [Controlla qualità dei dati](#checkdata)
- [Creare la query](#buildquery)
- [Suggerimenti su come personalizzare la navigazione in base a facet](#tips)
- [Spostamento in base a facet in base ai valori di intervallo](#rangefacets)
- [Spostamento in base a facet in base a GeoPoints](#geofacets)
- [Provare](#tryitout)

##<a name="why-use-it"></a>Perché usare
Le applicazioni di ricerca più efficaci sono più disponibili modelli interazione oltre a una casella di ricerca. Spostamento in base a facet è un punto di ingresso alternativo per le ricerche, che offre una comoda alternativa alla digitazione manualmente le espressioni di ricerca complessa.

##<a name="know-the-fundamentals"></a>Conoscere le nozioni di base

Se ha esperienza di sviluppo di ricerca, il modo migliore per valutare della struttura di spostamento in base a facet è che mostra le possibilità di ricerca attiva. È un tipo di esperienza di ricerca di drill-down, in base ai filtri predefiniti, utilizzati per limitare rapidamente verso il basso risultati della ricerca mediante clic e azioni. 

**Modello di interazione**

L'esperienza di ricerca per l'esplorazione in base a facet è iterativo, quindi si vedrà prima di tutto la comprensione come una sequenza di query che unfold in risposta alle azioni dell'utente.

Punto di partenza è una pagina di applicazione che consente di spostarsi in base a facet, in genere sul perimetro. Spostamento in base a facet è spesso una struttura ad albero, con caselle di controllo per ogni valore o testo selezionabile. 

1.  Una query inviata alla ricerca di Azure specifica la struttura di spostamento in base a facet tramite uno o più parametri query facet. Ad esempio, la query potrebbe includere `facet=Rating`, ad esempio con un `:values` o `:sort` opzione per perfezionare ulteriormente la presentazione.
2.  Il livello di presentazione esegue il rendering di una pagina di ricerca che consente di spostarsi in base a facet, usando gli aspetti specificati nella richiesta.
3.  Data una struttura di spostamento in base a facet che include valutazione, l'utente fa clic su "4" per indicare che devono essere visualizzati solo i prodotti con una classificazione di 4 o versione successiva. 
4.  In risposta, l'applicazione invia una query che include`$filter=Rating ge 4` 
5.  Il livello di presentazione Aggiorna la pagina, con un set di risultati ridotta che contiene solo gli elementi che soddisfano i criteri di nuovi (in questo caso, i prodotti classificati 4 e massimo).

Un aspetto è un parametro di query, ma non confondere con input per la query. Non è mai utilizzato come criteri di selezione in una query. Se, tuttavia, considerato facet parametri di query come input per la struttura di spostamento che viene restituita nella risposta. Per ogni parametro di query facet specificati, Azure ricerca restituirà il numero di documenti disponibili nei risultati della parziali per ogni valore facet.

Avviso di `$filter` nel passaggio 4. Si tratta di un aspetto importante della struttura di spostamento in base a facet. Sebbene facet e filtri sono indipendenti dell'API, sarà necessario entrambi per offrire l'esperienza desiderato. 

**Modelli di progettazione**

Nel codice applicazione, è consigliabile utilizzare parametri query facet per restituire la struttura di spostamento in base a facet insieme risultati facet, oltre a un'espressione $filter che gestisce l'evento fare clic su. Si tratta del `$filter` restituita espressione come code-behind limitazione per motivi di effettivo dei risultati della ricerca a livello di presentazione. Dato un aspetto di colori, fare clic su colore rosso implementato tramite un `$filter` espressione che seleziona solo gli articoli che hanno un colore di rosso. 

**Nozioni fondamentali sulle query di ricerca Azure**

Risultati di ricerca di Azure, una richiesta viene specificata tramite uno o più parametri di query (per una descrizione di ciascuno di essi, vedere [I documenti di ricerca](http://msdn.microsoft.com/library/azure/dn798927.aspx) ). Nessuno dei parametri query sono obbligatori, ma è necessario disporre almeno un ordine per una query è valido.

Precisione, in genere interpretata come la possibilità di filtrare gli accessi irrilevante, si ottiene attraverso una o entrambe le espressioni:

- **ricerca =**<br/>
Il valore del parametro costituisce l'espressione di ricerca. Potrebbe essere un'unica stringa di testo o un'espressione di ricerca complessa che include più termini e operatori. Sul server, un'espressione di ricerca viene utilizzata per la ricerca full-text, query campi che supportano le ricerche nell'indice per abbinare i termini, i risultati vengono restituiti nell'ordine di rango. Se si imposta `search` su null query esecuzione supera intero indice (vale a dire `search=*`). In questo caso, altri elementi della query, ad esempio un `$filter` o punteggio profilo, sarà vengono restituiti i principali fattori che influenzano quali documenti `($filter`) e in quale ordine (`scoringProfile` o `$orderb`y).

- **$filter =**<br/>
Il filtro è un metodo efficace per limitare le dimensioni dei risultati della ricerca in base ai valori degli attributi di documento specifico. A `$filter` valutato per primo, seguito da logica faceting che genera i valori disponibili e i conteggi corrispondente per ogni valore

Le espressioni di ricerca complessa ridurrà le prestazioni della query. Se possibile, utilizzare le espressioni di filtro ben creato per aumentare precisione e migliorare le prestazioni delle query.

Per comprendere meglio come un filtro aggiunge maggiore precisione, confrontare un'espressione di ricerca complessa che include un'espressione di filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Anche se entrambe le query sono valide, il secondo è superiore se si sta cercando non motel con parcheggio Seattle. La prima query si basa su tali parole specifiche viene menzionati o non menzionati in campi di stringa ad esempio nome, descrizione e qualsiasi altro campo contenente i dati che supportano le ricerche. La seconda query consente di cercare le corrispondenze precise sui dati strutturati e potrebbero essere molto più accurato.

Nelle applicazioni che includono spostamento in base a facet, verrà desidera assicurarsi che ogni azione dell'utente in una struttura di spostamento in base a facet è accompagnato da un tipo di restrizione dei risultati della ricerca, tramiti un'espressione di filtro.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Modalità di compilazione di esso

Spostamento in base a facet nella ricerca Azure è implementato nel codice dell'applicazione che consente di creare la richiesta ma si basa su elementi predefiniti nello schema.

Predefiniti per la ricerca indice è la `Facetable [true|false]` attributo index, impostare i campi selezionati per attivare o disattivare l'utilizzo in una struttura di spostamento in base a facet. Senza `"Facetable" = true`, un campo non può essere utilizzato nella struttura di spostamento facet.

In fase di query, il codice dell'applicazione crea una richiesta che include `facet=[string]`, un parametro di richiesta che fornisce il campo a facet da. Una query può disporre aspetti multipli, ad esempio `&facet=color&facet=category&facet=rating`, ognuno di essi separati da una e commerciale (&).

Il codice dell'applicazione è necessario creare anche un `$filter` espressione per gestire gli eventi fare clic su nella struttura di spostamento in base a facet. A `$filter` riduce i risultati della ricerca, utilizzando il valore di facet come criteri di filtro.

Ricerca Azure restituisce i risultati di ricerca, per i termini immessi dall'utente, insieme aggiornamenti per la struttura di spostamento in base a facet. Risultati di ricerca di Azure, spostamento in base a facet è una costruzione di livello singolo, con valori di facet e conta quanti risultati vengono trovati per ognuna di esse.

Il livello di presentazione nel codice offre l'esperienza utente. Indicare le parti che costituiscono la struttura di spostamento in base a facet, ad esempio l'etichetta, i valori, caselle di controllo e il conteggio. L'API REST di Azure ricerca è indipendente dalla piattaforma, quindi usare qualsiasi lingua e la piattaforma desiderata. Importante è per includere elementi dell'interfaccia utente che supportano l'aggiornamento incrementale, con lo stato dell'interfaccia utente aggiornato ogni facet aggiuntive è selezionata. 

Nelle sezioni seguenti è necessario esaminare attentamente in modalità di compilazione di ogni parte partendo dal livello di presentazione.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Creare il livello di presentazione

Lavoro indietro dal livello di presentazione consentono di scoprire requisiti che potrebbero essere perse in caso contrario e si conoscono le funzionalità sono essenziali per l'esperienza di ricerca.

In termini di navigazione in base a facet, la pagina web o applicazione viene visualizzata la struttura di spostamento in base a facet, viene rilevato all'input utente nella pagina e inserisce gli elementi modificati. 

Per le applicazioni web, AJAX viene generalmente utilizzata nel livello di presentazione perché consente di aggiornare le modifiche incrementali. È anche possibile utilizzare ASP.NET MVC o qualsiasi altra piattaforma di visualizzazione in grado di connettersi a un servizio di ricerca di Azure su HTTP. L'applicazione di esempio fare riferimento ai - **AdventureWorks catalogo** : questo articolo è un'applicazione Asp.net.

Nell'esempio seguente, tratto dal file **index.cshtml** dell'applicazione di esempio, viene creata una struttura HTML dinamica per la visualizzazione struttura di spostamento in base a facet nella pagina dei risultati di ricerca. Nell'esempio riportato di spostamento in base a facet è incorporata la pagina dei risultati di ricerca e viene visualizzata dopo l'utente ha inviato un termine di ricerca.

Si noti che ogni facet ha un'etichetta (colori, categorie, prezzi), un'associazione a un campo in base a facet (colore, categoryName, listPrice) e un `.count` parametro, utilizzato per restituire il numero di oggetti trovati per il risultato facet.

  ![][2]
 

> [AZURE.TIP] Quando si progetta la pagina dei risultati di ricerca, ricordarsi di aggiungere un meccanismo per la cancellazione facet. Se si usa le caselle di controllo, gli utenti possono facilmente intuit come cancellare i filtri. Per altri layout, potrebbe essere necessario un modello di sito o un altro approccio creativo. Ad esempio nell'applicazione di esempio AdventureWorks catalogo, è possibile fare clic sul titolo catalogo AdventureWorks, per reimpostare la pagina di ricerca.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Creare l'indice analitico

Faceting è abilitata in base a campo dal campo nell'indice analitico, tramite l'attributo indice: `"Facetable": true`.  
Tutti i tipi di campo che potrebbero essere eventualmente utilizzati nella struttura di spostamento in base a facet sono `Facetable` per impostazione predefinita. Includono questi tipi di campo `Edm.String`, `Edm.DateTimeOffset`e tutti i tipi di campo numerico (in pratica, tutti i tipi di campo sono facetable ad eccezione di `Edm.GeographyPoint`, che non può essere utilizzato nella struttura di spostamento in base a facet). 

Durante la creazione di un indice analitico, consigliata per l'esplorazione in base a facet è disattivata esplicitamente faceting per i campi che non devono essere mai utilizzati come un aspetto.  In particolare, i campi di stringa per i valori singleton, ad esempio un nome ID o prodotto devono essere impostati su `"Facetable": false` per impedirne l'utilizzo accidentale (e inefficace) in una struttura di spostamento in base a facet.

Di seguito è riportato lo schema per l'applicazione di esempio AdventureWorks catalogo (tagliato di alcuni attributi per ridurre dimensioni complessive):

 ![][3]
 
Si noti che `Facetable` è disattivata per i campi stringa che non devono essere utilizzati come facet, ad esempio un ID o il nome. Attivazione faceting disattivare nel punto in cui non è necessario consente di ridurre le dimensioni dell'indice e in genere migliora le prestazioni.

> [AZURE.TIP] Come si consiglia di includere l'insieme completo di attributi di indice per ogni campo. Sebbene `Facetable` è per impostazione predefinita per quasi tutti i campi, deliberatamente impostazione ogni attributo consentono di valutare tramite le implicazioni di ogni decisione schema. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Controlla qualità dei dati 

Quando si sviluppano applicazioni basate sui dati, preparare i dati è spesso una delle parti più grande del processo. Le applicazioni di ricerca non sono alcuna eccezione. La qualità dei dati incide diretto sul se la struttura di spostamento in base a facet materializza come previsto impostare, nonché l'efficacia in consentono di creare filtri per ridurre il risultato.

Risultati di ricerca di Azure, corpus ricerca viene creato da documenti di compilare un indice. Documenti forniscono i valori che vengono utilizzati per calcolare gli aspetti. Se si vuole facet marchio o prezzo, ogni documento deve contenere valori per *BrandName* e *ProductPrice* valide, coerenti e produttivi come opzione di filtro.

Di seguito sono elencati i promemoria di alcuni delle operazioni necessarie per ripulire per:

- Per ogni campo che si desidera facet da, è necessario stabilire se contiene valori che sono appropriati come filtri in modalità self-diretto ricerca. I valori devono essere breve, descrittivo e sufficientemente distintivo per l'offerta scegliere Cancella tra concorrenti.
- Errori di ortografia o i valori corrispondenti quasi. Se facet colori e i valori dei campi includono arancione e Ornage (un errore di ortografia), un aspetto in base al campo colore da sollevare entrambe.
- Misto è indicata può anche danneggiare nella struttura di spostamento in base a facet, con arancione e arancione visualizzate come due diversi valori. 
- Singole e plurali versioni dello stesso valore possono causare un facet distinto per ogni.

Come si può immaginare, diligenza preparare i dati è un aspetto essenziale della struttura di spostamento in base a facet efficace.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Creare la query

Il codice scritto per la creazione di query deve specificare tutte le parti di una query valida, incluse le espressioni di ricerca, facet, filtri, punteggio profili – nulla utilizzate per formulare una richiesta. In questa sezione, esploreremo nel punto in cui facet adattino in una query e come filtri vengono utilizzati per aspetti per fornire un set di risultati ridotte.

Un esempio è spesso si consiglia di iniziare. Nell'esempio seguente, tratto dal file **CatalogSearch.cs** consente di creare una richiesta di crea spostamento facet in base a colori, Category e prezzi. 

Si noti che facet sono integrali nell'applicazione di esempio. L'esperienza di ricerca in catalogo AdventureWorks si basa su filtri e spostamento in base a facet. Si tratta evidente il posizionamento visibile della struttura di spostamento in base a facet nella pagina. L'applicazione di esempio include parametri URI aspetti (colore, categoria, prezzi) come proprietà il metodo di ricerca (come impostata nell'applicazione di esempio).

  ![][4]
 
Parametro di query facet è impostato su un campo e a seconda del tipo il tipo di dati, può essere ulteriormente parametri da elenco con valori delimitati da virgole include `count:<integer>`, `sort:<>`, `intervals:<integer>`, e `values:<list>`. Un elenco di valori è supportato per i dati numerici quando si configura intervalli. Per dettagli sull'utilizzo, vedere [Ricerca documenti Azure ricerca API ()](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Insieme facet, la richiesta formulata dall'applicazione in uso deve anche creare filtri per limitare il set di documenti candidati in base a una selezione di valore facet. Per un archivio di bicicletta, in base a facet spostamento fornisce indicazioni a domande quali "quali colori, produttori e tipi di biciclette sono disponibili", filtrando risposte a domande quali "quali bici esatte sono rossi, bici mountain, in questo prezzo intervallo".

Quando un utente fa clic su "Rosso" per indicare che devono essere visualizzati solo i prodotti rossi, la query successiva all'invio includerà `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Procedure consigliate per l'esplorazione in base a facet

L'elenco seguente sono riepilogate alcune procedure consigliate.

- **Precisione**<br/>
Utilizzare i filtri. Se si utilizzano normalmente solo i espressioni di ricerca da soli derivanti potrebbero causare un documento deve essere restituito che non dispone del valore del facet precisi in uno dei campi. 

- **Campi di destinazione**<br/>
In base a facet drill-down, è preferibile includono solo i documenti contenenti il valore di facet un determinato campo (in base a facet), non in un punto qualsiasi in tutti i campi che supportano le ricerche. Aggiunta di un filtro consolida il campo di destinazione indirizzando al servizio di ricerca solo nel campo in base a facet per un valore corrispondente.

- **Efficienza dell'indice**<br/>
Se l'applicazione utilizza esclusivamente spostamento sfaccettato (ovvero, senza casella di ricerca), è possibile contrassegnare il campo come `searchable=false`, `facetable=true` per produrre un indice più compatto. Inoltre, l'indicizzazione si verifica solo ai valori di facet intero, con nessuna interruzione di word o l'indicizzazione dei componenti di un valore di più parole.

- **Prestazioni**<br/>
Filtri limitare il set di documenti candidati per la ricerca ed escludere dalla classificazione. Se si dispone di un elevato numero di documenti, con un drill-down facet molto selettiva verso il basso spesso si otterranno prestazioni migliori in modo significativo.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Suggerimenti su come personalizzare la navigazione in base a facet

Di seguito è un foglio di suggerimento con indicazioni sui problemi specifici.

**Aggiungere etichette per ogni campo nella struttura di spostamento facet**

Etichette sono in genere definite nell'HTML o modulo (**index.cshtml** nell'applicazione di esempio). Non esiste alcun API in Azure Cerca etichette spostamento facet o qualsiasi altro tipo di metadati.

**Definire i campi che possono essere utilizzati come facet**

Tenere presente che lo schema dell'indice determina quali campi sono disponibili da utilizzare come un aspetto. Supponendo che un campo è facetable, la query specifica i campi da facet da. Il campo per cui si è faceting fornisce i valori che vengono visualizzati sotto l'etichetta. 

I valori da visualizzare in ogni etichetta vengono recuperati dall'indice. Ad esempio, se il campo facet *colori*, i valori disponibili per altre opzioni di filtro sarà valori per il campo (rosso, nero e così via).

Per valori numerici e DateTime solo, è possibile impostare in modo esplicito i valori in base al campo sfaccettato (ad esempio `facet=Rating,values:1|2|3|4|5`). Un elenco di valori consentito per questi tipi di campo semplificare la separazione dei risultati facet in intervalli contigui (uno dei due intervalli in base a valori numerici o periodi di tempo). 

**Tagliare i risultati facet**

Risultati facet sono documenti che si trovano nei risultati della ricerca che corrispondono a un termine facet. Nell'esempio seguente, nei risultati della ricerca per *il cloud computing*, 254 elementi dovranno *interno specifica* come tipo di contenuto. Elementi non sono necessariamente si escludono a vicenda. Se un elemento soddisfa i criteri di entrambi i filtri, viene conteggiata in ognuna di esse. Questo è possibile quando faceting su `Collection(Edm.String)` campi, che spesso vengono utilizzati per implementare documento tagging.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

In generale, se si trovano che faccia risultati in modo permanente sono troppo grandi, è consigliabile aggiungere più filtri, come spiegato nelle sezioni precedenti, per consentire agli utenti di applicazione altre opzioni per restringere la ricerca.

**Limitare gli elementi nel riquadro di spostamento facet**

Per ogni campo in base a facet nel menu di navigazione, esiste un limite predefinito di 10 valori. Questa impostazione predefinita senso per le strutture di spostamento perché mantiene l'elenco di valori di dimensioni gestibili. È possibile ignorare l'impostazione predefinita mediante l'assegnazione di un valore da contare.

- `&facet=city,count:5`Specifica che le prime 5 città trovate nella parte superiore classificati i risultati vengono restituite come risultato facet. Dato un termine di ricerca di "aeroporto" e 32 corrispondenze, se la query specifica `&facet=city,count:5`, solo le prime cinque univoco le città con più documenti nei risultati della ricerca sono incluse nei risultati della sfaccettatura.

Si noti la differenza tra i risultati facet e risultati della ricerca. Risultati della ricerca sono tutti i documenti che corrispondono alla query. Risultati facet sono le corrispondenze per ogni valore facet. Nell'esempio, i risultati della ricerca includeranno nomi di città che non sono presenti nell'elenco di classificazione sfaccettato (5 in questo esempio). I risultati vengono filtrati in base a facet spostamenti diventano visibili all'utente quando lui Cancella facet o sceglie altri facet oltre città. 

> [AZURE.NOTE] La discussione `count` quando è presente più di un tipo può essere confusione. Nella tabella seguente offre un breve riepilogo della modalità di utilizzo il termine in API di ricerca di Azure, il codice di esempio e documentazione. 

- `@colorFacet.count`<br/>
Nel codice di presentazione, verrà visualizzato un parametro count su facet, consente di visualizzare il numero di risultati facet. Nei risultati facet conteggio indica il numero di documenti che corrispondono a intervallo o termini facet.

- `&facet=City,count:12`<br/>
In una query di facet, è possibile impostare conteggio a un valore.  Il valore predefinito è 10, ma è possibile impostarlo superiori o inferiori. Impostazione `count:12` Ottiene nella parte superiore 12 corrisponde nei risultati facet dal numero di documenti.

- "`@odata.count`"<br/>
In risposta alla query, questo valore indica il numero di elementi corrispondenti nei risultati della ricerca. In Media, supera la somma di tutti i risultati facet, a causa la presenza di elementi che corrispondono al termine di ricerca, ma senza corrispondenze di valore facet.


**Livelli di struttura di spostamento in base a facet** 

Come indicato, non è disponibile alcun supporto diretto per annidare facet in una gerarchia. Dalla casella di spostamento in base a facet supporta solo un livello di filtri. Tuttavia, esistono soluzioni alternative. È possibile codificare una struttura gerarchica facet in un `Collection(Edm.String)` con una voce scegliere singola gerarchia. L'implementazione di questa soluzione alternativa è lo scopo di questo articolo, ma è possibile leggere informazioni sugli insiemi in [OData dall'esempio](http://msdn.microsoft.com/library/ff478141.aspx). 

**Convalidare i campi**

Se si creazione l'elenco di facet dinamicamente in base all'input utente non attendibile, è necessario convalidare uno che i nomi dei campi in base a facet sono validi o i nomi di escape durante la creazione di URL utilizzando `Uri.EscapeDataString()` .NET o nell'equivalente nella piattaforma ideale.

**Conteggi nei risultati facet**

Quando si aggiunge un filtro a una query in base a facet, è consigliabile mantenere conto sfaccettatura (ad esempio `facet=Rating&$filter=Rating ge 4`). Tecnicamente, facet = valutazione non è necessaria, ma mantenendo restituisce i conteggi dei valori di facet per valutazioni 4 e versioni successive. Ad esempio, se un utente fa clic su "4" e la query include un filtro per maggiore o uguale a "4", conteggi vengono restituiti per ogni valore di classificazione è "4" e verso l'alto.  

**Implicazioni sharding conteggi facet**

In alcuni casi, è possibile che faccia non corrisponde al set di risultati (vedere [navigazione in base a facet nella ricerca Azure (forum post)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Facet può essere accurati grazie all'architettura sharding. Ogni indice di ricerca ha più shards ognuno di essi report facet primi N al numero di documento, quindi combinato in un singolo risultato. Se alcuni shards supportano numerose abbinare i valori, mentre gli altri utenti hanno meno, è possibile che alcuni valori di facet mancano o in contata per byte nei risultati.

Anche se questo comportamento può cambiare in qualsiasi momento, se si verifica questo comportamento oggi, è possibile aggirare il problema artificialmente eccessi nella misurazione il conteggio:<number> a un numero elevato per applicare completo dei report ogni condiviso. Se il valore di count: è maggiore o uguale al numero di valori univoci presenti nel campo, vengono garantiti risultati accurati. Tuttavia, quando conteggi dei documenti sono molto elevati, che non esiste una riduzione delle prestazioni, in modo utilizzare questa opzione cautela.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Spostamento di facet in base a un intervallo di valori

Faceting su intervalli è un requisito di applicazione di ricerca comuni. Sono supportati intervalli di dati numerici e valori DateTime. È possibile leggere informazioni su ogni approccio nei [Documenti di ricerca (API di ricerca Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Ricerca Azure semplifica la costruzione di intervallo, consentendo due approcci per il calcolo di un intervallo. Per entrambi gli approcci Azure ricerca crea gli intervalli appropriati in base input che rese disponibili. Ad esempio, se si specificano valori di intervallo di 10 | 20 | 30, viene creato automaticamente gli intervalli di 0 -10, 10-20, 20-30. L'applicazione di esempio rimuove qualsiasi intervalli sono vuoti. 

**Metodo 1: Utilizzare il parametro intervallo**<br/>
Per impostare gli aspetti prezzo incrementi $10, è possibile specificare:`&facet=price,interval:10`


**Metodo 2: Utilizzare un elenco di valori**<br/>
Per i dati numerici, è possibile utilizzare un elenco di valori.  Considerare l'intervallo di facet per listPrice rendering come indicato di seguito:

  ![][5]

L'intervallo è specificato nel file **CatalogSearch.cs** usando un elenco di valori:

    facet=listPrice,values:10|25|100|500|1000|2500

Ogni intervallo integrato con 0 come punto di partenza, un valore nell'elenco come un endpoint e quindi ritagliata dell'intervallo precedente per creare intervalli distinti. Ricerca Azure farlo come parte della struttura di spostamento in base a facet. Non è necessario scrivere codice per la struttura di ogni intervallo.

### <a name="build-a-filter-for-facet-ranges"></a>Creare un filtro per gli intervalli facet ###

Per filtrare i documenti basati su un intervallo selezionato dall'utente, è possibile utilizzare il `"ge"` e `"lt"` filtrare gli operatori di un'espressione di due parti che definisca i punti finali dell'intervallo. Ad esempio, se si sceglie l'intervallo di 10-25, il filtro è `$filter=listPrice ge 10 and listPrice lt 25`.

Nell'applicazione di esempio, l'espressione di filtro utilizza **priceFrom** e **priceTo** per impostare i punti finali. Il metodo **BuildFilter** in **CatalogSearch.cs** contiene l'espressione di filtro che forniscono i documenti in un intervallo.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Spostamento filtrato in base a GeoPoints

È comune per visualizzare i filtri che consentono di si sceglie un archivio, ristorante o destinazione in base a relativa prossimità alla posizione corrente. Spostamento in base a facet simile a questo tipo di filtro, ma è semplicemente un filtro. Abbiamo menzionare crea qui per coloro che specificamente cercando suggerimenti implementazione per il problema di progettazione particolare.

Sono disponibili due funzioni geospaziali in ricerca Azure, **geo.distance** e **geo.intersects**.

- La funzione **geo.distance** restituisce la distanza in chilometri tra due punti, uno un campo e uno da una costante passati come parte del filtro. 

- La funzione **geo.intersects** restituisce vera se un determinato punto all'interno di un poligono specificato, in cui il punto di un campo e poligono viene specificato come un elenco di costante di coordinate passate come parte del filtro.

È possibile trovare esempi di filtro nella [sintassi delle espressioni OData (Azure ricerca)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Provare

Azure ricerca Adventure Works Demo su Codeplex contiene gli esempi riportati in questo articolo. Mentre si lavora con i risultati di ricerca, guardare l'URL per le modifiche in creazione della query. Questa applicazione accade accodare facet all'URI quando si seleziona ognuno.

1.  Configurare l'applicazione di esempio per utilizzare l'URL del servizio e api chiave. 

    Si noti lo schema che viene definito nel file Program.cs del progetto CatalogIndexer. Consente di specificare campi facetable per colore, listPrice, dimensione, lo spessore, categoryName e modelName.  Solo alcuni di questi (colore listPrice, categoryName) sono effettivamente implementate nella struttura di spostamento in base a facet.

3.  Eseguire l'applicazione. 

    Innanzitutto la casella di ricerca è visibile. È possibile fare clic sul pulsante di ricerca per ottenere risultati tutti oppure digitare un termine di ricerca.

    ![][7]
 
4.  Immettere un termine di ricerca, ad esempio bicicletta e fare clic su **Cerca**. La query viene eseguita rapidamente.
 
    Una struttura di spostamento in base a facet viene restituita anche con i risultati della ricerca.  L'URL facet per i colori, categorie e i prezzi sono null. Nella pagina dei risultati della ricerca la struttura di spostamento in base a facet include conteggi per ogni risultato facet.

     ![][8]
 
5.  Fare clic su un colore, una categoria e un intervallo di prezzo. Facet sono null per una ricerca iniziale, ma come assumono valori, i risultati della ricerca vengono rimossi degli elementi che non corrispondono. Si noti che è stata URI visualizzi le modifiche apportate alla query.

    ![][9]
 
6.  Per cancellare la query in base a facet in modo che è possibile provare comportamenti per le query diversa, fare clic su **Catalogo AdventureWorks** nella parte superiore della pagina.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Passaggio successivo

Per testare la conoscenza, è possibile aggiungere un campo facet *modelName*. L'indice è già impostato per il facet, in modo che non all'indice sono necessarie modifiche. Ma è necessario modificare il codice HTML per includere un nuovo aspetto per i modelli e aggiungere il campo facet al costruttore query.

Per informazioni più complete sui principi di progettazione per l'esplorazione in base a facet, è consigliabile i collegamenti seguenti:

- [Progettazione per la ricerca in base a facet](http://www.uie.com/articles/faceted_search/)
- [Modelli di progettazione: Spostamento in base a facet](http://alistapart.com/article/design-patterns-faceted-navigation)

È anche possibile guardare [Azure ricerca approfondita](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). In 45:25, esiste una dimostrazione sull'implementazione facet.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 