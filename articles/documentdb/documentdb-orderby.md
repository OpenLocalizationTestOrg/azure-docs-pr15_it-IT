<properties 
    pageTitle="Ordinamento dei dati DocumentDB utilizzando Order By | Microsoft Azure" 
    description="Informazioni su come usare ORDER BY DocumentDB query in SQL e LINQ e come specificare un criterio di indicizzazione per le query ORDER BY." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Ordinamento dei dati DocumentDB utilizzando Order By
Microsoft Azure DocumentDB supporta documenti query utilizzando SQL sui documenti JSON. È possibile ordinare i risultati della query utilizzando la clausola ORDER BY nelle istruzioni di query SQL.

Dopo aver letto in questo articolo, sarà possibile rispondere alle domande seguenti: 

- Come query con Order By?
- Configurazione di un criterio di indicizzazione per Order By
- Novità successiva?

Sono anche disponibili [esempi](#samples) e [domande frequenti](#faq) .

Per un riferimento completo sull'esecuzione di query SQL, vedere l' [esercitazione DocumentDB Query](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Come eseguire una Query con Order By
Ad esempio in SQL ANSI, è ora possibile includere una clausola Order By facoltativa nelle istruzioni SQL durante la ricerca DocumentDB. La clausola può includere un argomento facoltativo ASC/DESC per specificare l'ordine in cui devono essere recuperati i risultati. 

### <a name="ordering-using-sql"></a>Ordinamento mediante SQL
Ecco, ad esempio una query per recuperare le pubblicazioni primi 10 in ordine decrescente dei titoli. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Ordinamento con SQL filtro
È possibile ordinare utilizzando qualsiasi proprietà nidificata all'interno dei documenti come Books.ShippingDetails.Weight ed è possibile specificare filtri aggiuntivi nella clausola WHERE in combinazione con Order By come in questo esempio:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordinamento mediante il Provider LINQ per .NET
Utilizzo di .NET SDK versione 1.2.0 e versioni successive, è inoltre possibile utilizzare la clausola OrderBy() o OrderByDescending() all'interno delle query LINQ come in questo esempio:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB supporta l'ordinamento con un singolo valore numerico, stringa o proprietà booleana per query con tipi di query aggiuntive disponibile a breve. Per ulteriori informazioni, vedere [Novità successivo](#Whats_coming_next) .

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurare una regola di indicizzazione per Order By

Tenere presente che DocumentDB supporta due tipi di indici Hash e intervallo, è possibile impostare le proprietà percorsi specifiche, i tipi di dati (stringhe/numeri) e valori di precisione diversi (precisione massima o un valore di precisione fissa). Poiché DocumentDB utilizza Hash indicizzazione come impostazione predefinita, è necessario creare una nuova raccolta con un criterio di indicizzazione personalizzato con intervallo di numeri, stringhe o entrambe, per poter utilizzare Order By. 

>[AZURE.NOTE] Stringa intervallo indici sono stati introdotti in 7 luglio 2015 con API REST versione 2015-06-03. Per creare i criteri per Order By con stringhe, è necessario utilizzare SDK versione 1.2.0 di .NET SDK o versione 1.1.0 di Python, Node o linguaggio SDK.
>
>Prima di API REST versione 2015-06-03, il criterio di indicizzazione insieme predefinito è stato Hash di stringhe e numeri. Questo è stato modificato eseguire l'hashing per stringhe e intervallo per i numeri. 

Per ulteriori informazioni, vedere [regole di indicizzazione DocumentDB](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>L'indicizzazione per Order By rispetto a tutte le proprietà
Ecco come è possibile creare una raccolta con "Tutti intervallo" l'indicizzazione per Order By rispetto a qualsiasi/tutte numeriche o stringa proprietà che vengono visualizzate nei documenti JSON all'interno di essa. Di seguito è ignorare il tipo di indice predefinito per i valori stringa in intervallo e la precisione massima (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Si noti che Order By solo restituirà risultati dei tipi di dati (stringa e numero) indicizzati con un RangeIndex. Ad esempio, se si dispone predefinito indicizzazione criteri che contiene solo RangeIndex sui numeri, una clausola Order By rispetto a un percorso con valori stringa non restituirà contiene documenti.

### <a name="indexing-for-order-by-for-a-single-property"></a>L'indicizzazione per Order By per una singola proprietà
Ecco come è possibile creare una raccolta con l'indicizzazione per Order By rispetto a solo la proprietà titolo, ovvero una stringa. Esistono due percorsi, uno per la proprietà titolo ("/ titolo /?") con intervallo indicizzazione e l'altra per ogni altra proprietà con lo schema di indicizzazione predefiniti, ovvero Hash per stringhe e intervallo per i numeri.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Esempi
Esaminare il [progetto esempi Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) in cui viene illustrato come utilizzare Order By, inclusi la creazione di regole di indicizzazione e spostamento mediante l'ordine. Gli esempi sono Apri origine e si consiglia di inviare richieste pull con contributi che possono trarre vantaggio altri sviluppatori DocumentDB. Fare riferimento alle [linee guida contributo](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) per indicazioni su come collaborare.  

## <a name="faq"></a>Domande frequenti

**Che cos'è il consumo di richiedere unità (RU) previsto di Order By query?**

Poiché Order By utilizza l'indice DocumentDB per le ricerche, il numero di unità di richiesta utilizzati dalle Order By query sarà simile alla query equivalenti senza Order By. Quali altre operazioni su DocumentDB, il numero di unità richiesta dipende dimensioni/forme dei documenti, nonché la complessità della query. 


**Che cos'è il sovraccarico per Order By dell'indicizzazione previsto?**

Il sovraccarico di spazio di archiviazione indicizzazione sarà proporzionato al numero di proprietà. In caso peggiore, il sovraccarico di indice sarà 100% dei dati. Non c'è alcuna differenza il sovraccarico di velocità (unità richiedere) tra intervallo/Order By indicizzazione e l'indicizzazione Hash predefinito.

**Modalità di query di dati esistenti in DocumentDB utilizzando Order By**

Per ordinare i risultati della query utilizzando Order By, è necessario modificare il criterio di indicizzazione della raccolta di utilizzare un tipo di indice intervallo in base alla proprietà usato per ordinare. Vedere [Modifica di indicizzazione criterio](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quali sono le limitazioni di Order By corrente?**

Order By è possibile specificare solo in base a una proprietà, uno dei due numerico o stringa quando si tratta di intervallo indicizzata con la massima precisione (-1).

È possibile eseguire le operazioni seguenti:
 
- Order By con le proprietà di stringa interno come id, _rid e self (disponibile a breve).
- Order By con proprietà derivate dal risultato di un join all'interno dello stesso documento (disponibile a breve).
- Ordina per più proprietà (disponibile a breve).
- Order By con le query nel database, raccolte, gli utenti, autorizzazioni o allegati (disponibile a breve).
- Order By con proprietà calcolate ad esempio, il risultato di un'espressione o una funzione di utente/incorporata-in.

Order By non è attualmente supportata per le query tra partizione quando si utilizza Esplora Query nel portale di Azure.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si riceve un messaggio di errore Order By non è supportato, verificare che si sta usando una versione di [SDK](documentdb-sdk-dotnet.md) che supporta l'ordine. 

## <a name="next-steps"></a>Passaggi successivi

Divisione del [progetto esempi Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) e iniziare l'ordinamento dei dati! 

## <a name="references"></a>Riferimenti
* [Guida di riferimento DocumentDB Query](documentdb-sql-query.md)
* [Riferimento dei criteri di indicizzazione DocumentDB](documentdb-indexing-policies.md)
* [Riferimento SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Ordine di DocumentDB degli esempi](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

