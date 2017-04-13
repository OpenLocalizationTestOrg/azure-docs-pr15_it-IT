<properties
    pageTitle="Come modello di tipi di dati complessi in Azure ricerca | Ricerca di Microsoft Azure"
    description="Annidate o possano modellare strutture di dati gerarchici in un indice di ricerca di Azure mediante set di righe bidimensionale e il tipo di dati di raccolte."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Come tipi di dati complessi in Azure ricerca del modello

Set di dati esterni utilizzati per popolare un indice di ricerca di Azure talvolta include sottostrutture gerarchiche o annidate che non suddividere perfettamente in un set di righe in formato tabulare. Esempi di tali strutture potrebbero includere più percorsi e i numeri di telefono per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un libro singolo e così via. Modellazione di termini, è possibile visualizzare queste strutture parla di *tipi di dati complessi*, *tipi di dati composti*, *tipi di dati composti*o *aggregare i tipi di dati*, per citarne alcune.

Tipi di dati complessi non sono supportati in Azure ricerca a livello nativo, ma una possibile soluzione consolidata include fasi di appiattimento la struttura e quindi utilizzando un tipo di **raccolta** dati per ricostituire la struttura interna. Seguendo la tecnica descritta in questo articolo consente il contenuto dovrà essere eseguita la ricerca, in base a facet, filtrato e ordinato.

## <a name="example-of-a-complex-data-structure"></a>Esempio di una struttura di dati complessi

I dati in questione si trovano in genere, come un set di documenti JSON o XML o elementi in un punto vendita NoSQL, ad esempio DocumentDB. La difficoltà quando si proprio strutturali, con più elementi figlio che devono essere eseguita la ricerca e filtrati.  Come punto di partenza per illustrare la soluzione, eseguire il seguente documento JSON contenente un insieme di contatti di un esempio:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Mentre i campi denominato "id", "nome" e 'società' è possibile associare facilmente-a-uno come campi all'interno di un indice di ricerca di Azure, il campo 'posizioni' contiene una matrice di posizioni, sia un set di ID posizione, nonché le descrizioni di posizione. Dato che ricerca Azure non dispone di un tipo di dati che supporta questa caratteristica, è necessario un modo diverso per questo modello di ricerca di Azure. 

> [AZURE.NOTE] Questa tecnica è descritto da Kirk Evans in un post di blog [Indicizzazione DocumentDB con Azure ricerca](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), che mostra una tecnica denominata "trasparenza i dati," quale si dispone di un campo denominato `locationsID` e `locationsDescription` che sono entrambe [le raccolte](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matrice di stringhe).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Trasformare la matrice in singoli campi

Per creare un indice di ricerca di Azure che supporta il set di dati, creare singoli campi per sottostruttura annidata: `locationsID` e `locationsDescription` con un tipo di dati di [raccolte](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matrice di stringhe). In questi campi da indicizzare i valori "1" e "2" nel `locationsID` di campo per John Smith e i valori "3" e "4" nel `locationsID` campo per Jen Campbell.  

I dati all'interno di ricerca di Azure sarebbero simile alla seguente: 

![dati di esempio, 2 righe](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Aggiungere un campo insieme alla definizione dell'indice

Nello schema di indice, le definizioni di campo può essere simile a questo esempio.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Convalidare le funzionalità di ricerca e facoltativamente è possibile estendere l'indice

Supponendo che si è creato l'indice e il caricamento dei dati, è possibile verificare la soluzione per verificare l'esecuzione di query di ricerca con il set di dati. Ogni campo **insieme** deve essere **che supportano le ricerche**, **possibile filtrare** e **facetable**. Dovrebbe essere possibile eseguire query ad esempio:

* Trovare tutte le persone che lavorano "Adventureworks sede centrale".
* È possibile ottenere un conteggio del numero di persone che lavorano in un "casa".  
* Persone che lavorano in un "casa", Mostra quali altri uffici funzionano insieme a un conteggio delle persone in ogni posizione.  

In questa tecnica rientra distanza a cui è quando è necessario eseguire una ricerca che combina l'id di posizione sia, nonché la descrizione della posizione. Per esempio:

* Trovare tutte le persone a cui hanno un ufficio Home è un ID percorso di 4.  

Se si ricorda il contenuto originale simile al seguente:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Tuttavia, dopo che sono stati separati i dati in campi separati, non esiste alcun modo di sapere se Office Home per Jen Campbell si riferisce a `locationsID 3` o `locationsID 4`.  

Per gestire questo caso, definire un altro campo dell'indice che combina tutti i dati in un'unica raccolta.  Nel caso dell'esempio applicherà questo campo `locationsCombined` e si per separare il contenuto con un `||` anche se è possibile scegliere qualsiasi separatore che si ritiene che sarebbe un set di caratteri per il contenuto univoco. Per esempio: 

![dati di esempio, 2 righe con separatori](./media/search-howto-complex-data-types/sample-data-2.png)

Utilizzare questa `locationsCombined` campo, è possibile ora contenere ancora più query, ad esempio:

* Viene visualizzato un conteggio delle persone che lavorano in un 'Home Office' con Id posizione di "4".  
* Cercare persone che lavorano in un "casa" con i passaggi Id "4". 

## <a name="limitations"></a>Limitazioni

Questa tecnica è utile per alcuni scenari, ma non è applicabile in ogni caso.  Per esempio:

1. Se non si dispone un set di campi statico nel tipo di dati complessi e non è possibile eseguire il mapping di tutti i tipi di possibili per un singolo campo. 
2. Aggiornamento di oggetti annidati richiede alcune operazioni aggiuntive per determinare cosa è necessario aggiornare l'indice di ricerca di Azure

## <a name="sample-code"></a>Codice di esempio

È possibile visualizzare un esempio su come indicizzare un set di dati JSON complesso Azure ricerca ed eseguire un numero di query su questo set di dati in questa [GitHub repo](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Passaggio successivo

[Scelta del supporto per i tipi di dati complessi](https://feedback.azure.com/forums/263029-azure-search) in Uservoicehttp di ricerca Azure pagina e fornire qualsiasi input aggiuntivo che si desidera affidare a considerare in relazione alla implementazione delle funzionalità di Microsoft. È anche possibile contattare per uno specifico utente direttamente su Twitter in @liamca.


 