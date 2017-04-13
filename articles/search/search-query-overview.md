<properties
    pageTitle="Query indice di ricerca Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare una query di ricerca nella ricerca Azure e utilizzare i parametri di ricerca per filtrare e ordinare i risultati di ricerca."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>Indice di ricerca di Azure della query
> [AZURE.SELECTOR]
- [Panoramica](search-query-overview.md)
- [Portale](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

Quando si inviano richieste di ricerca alla ricerca di Azure, sono disponibili numerosi parametri che è possibile specificare insieme a parole effettive digitata nella casella di ricerca dell'applicazione. Questi parametri query consentono di ottenere un controllo più approfondito dell'esperienza di ricerca full-text.

Di seguito è riportato un elenco che illustra brevemente comuni di utilizzo di parametri della query di ricerca di Azure. Per esaustive parametri e il comportamento di query, vedere le pagine dettagliate per [l'API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) e [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Tipi di query

Ricerca Azure offre molte opzioni per creare query estremamente efficaci. Due tipi principali di query si userà sono `search` e `filter`. A `search` query consente di cercare uno o più condizioni in tutti i campi _che supportano le ricerche_ nell'indice e funziona in modo che ci si aspetta un motore di ricerca come Google o Bing per l'uso. A `filter` query viene valutata un'espressione booleana tutti _possibile filtrare_ campi in un indice. Diversamente da quanto succede `search` query `filter` query corrispondono il contenuto esatto di un campo, ovvero sono maiuscole e minuscole per i campi stringa.

È possibile utilizzare le ricerche e filtri insieme o separatamente. Se si usa loro insieme, il filtro viene applicato prima di tutto all'intero indice e quindi viene eseguita la ricerca sui risultati del filtro. Filtri possono quindi essere una tecnica utile per migliorare le prestazioni delle query poiché riduce il set di documenti che è necessario elaborare la query di ricerca.

La sintassi per le espressioni di filtro non è un sottoinsieme del [linguaggio di filtro OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Per le query di ricerca è possibile utilizzare la [sintassi semplificata](https://msdn.microsoft.com/library/azure/dn798920.aspx) o la [sintassi della query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) sono descritti di seguito.

### <a name="simple-query-syntax"></a>Nella sintassi della query semplice
[Nella sintassi della query semplice](https://msdn.microsoft.com/library/azure/dn798920.aspx) è il linguaggio di query predefinito utilizzato nella ricerca Azure. Nella sintassi della query semplice supporta un numero di operatori di ricerca comuni inclusi AND, o meno, una frase, suffisso e operatori di precedenza.

### <a name="lucene-query-syntax"></a>Nella sintassi della query Lucene
La [sintassi della query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) consente di utilizzare il linguaggio di query ampiamente adottata ed efficace sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Si sta usando questa sintassi di query consente di ottenere facilmente le funzionalità seguenti: [nell'ambito campo query](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [ricerca sfocata](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [ricerca](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [termini incrementare](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [ricerca di espressioni regolari](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [caratteri jolly](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [le basi della sintassi](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)e [query utilizzando operatori booleani](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono i risultati per una query di ricerca, è possibile richiedere che ricerca Azure serve i risultati ordinati per i valori in un campo specifico. Per impostazione predefinita, Azure ricerca Ordina i risultati di ricerca in base al rango di punteggio di ricerca, ciascun documento derivato dal [TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si desidera ricerca Azure per restituire i risultati ordinati in un valore diverso dal punteggio di ricerca, è possibile utilizzare il `orderby` parametro di ricerca. È possibile specificare il valore della `orderby` parametro per includere i nomi dei campi e le chiamate alla [ `geo.distance()` funzione](https://msdn.microsoft.com/library/azure/dn798921.aspx) per i valori geospaziali. Ogni espressione può essere seguito da `asc` per indicare che i risultati vengono richiesti in ordine crescente, e `desc` per indicare che i risultati vengono richiesti in ordine decrescente. La classificazione predefinita ordine crescente.

## <a name="paging"></a>Spostamento
Ricerca Azure rende più facile suddivisione in pagine dei risultati della ricerca. Tramite il `top` e `skip` parametri, è possibile inviare correttamente le richieste di ricerca che consentono di ricevere il totale set di risultati della ricerca in sottoinsiemi gestibili, ordinati che attivare facilmente consigliate dell'interfaccia utente corretta ricerca. Quando si riceve questi sottoinsiemi di dimensioni inferiori dei risultati, è inoltre possibile ricevere il conteggio dei documenti nel set di totale dei risultati della ricerca.

È possibile ottenere ulteriori informazioni spostamento risultati della ricerca nell'articolo [come pagina risultati di ricerca di Azure](search-pagination-page-layout.md).


## <a name="hit-highlighting"></a>Evidenziazione dei risultati
Risultati di ricerca di Azure, mettere in evidenza la parte esatta dei risultati di ricerca che corrispondono alla query di ricerca è semplice utilizzando il `highlight`, `highlightPreTag`, e `highlightPostTag` parametri. È possibile specificare i campi _che supportano le ricerche_ deve sottolineato e specificando i contrassegni stringa esatta da aggiungere all'inizio e alla fine del testo per la ricerca di Azure restituisce il testo corrispondente.
