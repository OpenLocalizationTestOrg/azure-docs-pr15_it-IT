<properties
    pageTitle="Caratteristiche di JSON di Database SQL Azure | Microsoft Azure"
    description="Database SQL Azure consente di analisi, query e formattare i dati in notazione JavaScript Object Notation (JSON)."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Guida introduttiva a caratteristiche JSON nel Database di SQL Azure

Database SQL Azure consente di analizzare e dati rappresentati nel formato JavaScript Object Notation [(JSON)](http://www.json.org/) della query ed esportare i dati relazionali come testo JSON.

JSON è un formato di dati più diffusi utilizzato per lo scambio di dati in una versione recente applicazioni web e dispositivi mobili. JSON viene usato anche per l'archiviazione dei dati semistrutturati nei file di log o nei database NoSQL come [DocumentDB Azure](https://azure.microsoft.com/services/documentdb/). Molti servizi web REST risultati restituiti formattati come testo JSON o accettano i dati formattati come JSON. Azure più servizi, ad esempio [Ricerca Azure](https://azure.microsoft.com/services/search/), [Lo spazio di archiviazione di Azure](https://azure.microsoft.com/services/storage/)e [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) presentano endpoint resto che restituiscono o utilizzare JSON.

Database SQL Azure consente di utilizzare dati JSON facilmente e integrare il database con i servizi moderni.

## <a name="overview"></a>Panoramica

Database SQL Azure fornisce le funzioni seguenti per l'utilizzo di dati JSON:

![Funzioni JSON](./media/sql-database-json-features/image_1.png)

Se si dispone di testo JSON, è possibile estrarre dati JSON o verificare che JSON sia stato formattato correttamente utilizzando le funzioni predefinite [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La funzione [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) consente di aggiornare valore all'interno di testo JSON. Per l'esecuzione di query e analisi più avanzate [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funzione trasformare una matrice di oggetti JSON in un set di righe. Qualsiasi query SQL può essere eseguito sul set di risultati restituiti. Infine, non esiste una clausola [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) che consente di formattare i dati memorizzati nelle tabelle relazionali come testo JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formattazione di dati relazionali in formato JSON
Se si dispone di un servizio web che abbia dati dal database di livello e fornisce una risposta JSON formattazione o framework JavaScript lato client o raccolte che accettano dati formattati come JSON, è possibile formattare il contenuto del database nel formato JSON direttamente in una query SQL. Non sono più scrivere il codice dell'applicazione che formatta i risultati da un Database di SQL Azure come JSON o includere qualche libreria serializzazione JSON per convertire i risultati della query in formato tabulare e quindi serializzare gli oggetti in formato JSON. Se, tuttavia, è possibile utilizzare la clausola JSON per formattare i risultati della query SQL come JSON nel Database di SQL Azure e usare direttamente nell'applicazione.

Nell'esempio seguente, le righe della tabella Sales formattate come JSON utilizzando la clausola JSON per:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

La clausola per JSON PATH formatta i risultati della query come testo JSON. I nomi di colonna vengono utilizzati come chiavi, mentre i valori delle celle vengono creati come valori JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Il set di risultati viene formattato come matrice JSON nel punto in cui ogni riga è formattato come oggetto JSON separato.

PERCORSO indica che è possibile personalizzare il formato di output di visualizzare il risultato JSON utilizzando la notazione di alias di colonna. La query seguente viene modificato il nome della chiave "NomeCliente" nel formato JSON di output e inserisce i numeri di telefono e di fax nell'oggetto secondario "Contatti":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

L'output della query è simile alla seguente:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

In questo esempio viene restituito un singolo oggetto JSON anziché una matrice specificando l'opzione [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . È possibile usare questa opzione se si è sicuri che viene restituito un singolo oggetto risultato query.

Il valore principale della clausola FOR JSON è che consente di restituire dati gerarchici complessi dal database nel formato annidato JSON oggetti o matrici. Nell'esempio seguente viene illustrato come includere gli ordini che appartengono al cliente sotto forma di matrice annidata degli ordini:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Invece di inviare separare le query per recuperare i dati dei clienti e quindi per recuperare un elenco di ordini correlati, è possibile ottenere tutti i dati con una singola query, come illustrato nell'output di esempio seguente:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Utilizzo dei dati JSON

Se non si dispone dei dati strutturati strettamente, se si dispone di oggetti secondari complessi, matrici o dati gerarchici o le strutture dati evolvono nel tempo, il formato JSON può essere utile per rappresentare una struttura di dati complessi.

JSON è un formato di testo che può essere utilizzato come qualsiasi altro tipo stringa nel Database di SQL Azure. È possibile inviare o archiviare dati JSON come un NVARCHAR standard:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

I dati JSON utilizzati in questo esempio viene rappresentati utilizzando il tipo di nvarchar (max). JSON possono essere inseriti in questa tabella o fornito come argomento della stored procedure tramite sintassi Transact-SQL standard, come illustrato nell'esempio seguente:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualsiasi lingua lato client o alla raccolta che funziona con dati di tipo stringa nel Database di SQL Azure funziona anche con dati JSON. JSON possono essere creati in una tabella che supporta il tipo NVARCHAR, ad esempio una tabella ottimizzata memoria o una tabella di versione del sistema. JSON non introduce qualsiasi vincolo nel codice lato client o del livello di database.

## <a name="querying-json-data"></a>Recupero dei dati JSON

Se i dati formattati come JSON archiviati in tabelle di SQL Azure, JSON funzioni consentono di usare questi dati in una query SQL.

Funzioni JSON che sono disponibili in consentono di database SQL Azure è trattare dati formattati come JSON come qualsiasi altro tipo di dati SQL. È possibile estrarre i valori nel testo JSON e usare facilmente dati JSON in una query:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

La funzione JSON_VALUE estrae un valore da testo JSON archiviato nella colonna di dati. Questa funzione utilizza un percorso di tipo mi piace JavaScript per fare riferimento a un valore in testo JSON per estrarre. Valore estratto può essere utilizzato in qualsiasi punto della query SQL.

La funzione JSON_QUERY è simile a JSON_VALUE. Diversamente da quanto succede JSON_VALUE, questa funzione estrae complessa oggetto secondario, ad esempio le matrici o oggetti inseriti nel testo JSON.

La funzione JSON_MODIFY consente di specificare il percorso del valore in testo JSON che deve essere aggiornato, come un nuovo valore che verrà sovrascritta quella precedente. In questo modo è possibile aggiornare facilmente testo JSON senza reparsing l'intera struttura.

Poiché JSON è archiviato in un testo standard, ci sono garanzie che i valori memorizzati nelle colonne di testo sono formattati correttamente. È possibile verificare che testo archiviato nella colonna JSON è formattato correttamente utilizzando i vincoli di controllo standard Database SQL Azure e la funzione ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se il testo di input è formattato correttamente JSON, la funzione ISJSON restituisce il valore 1. In ogni inserimento o aggiornamento della colonna JSON, questo vincolo verrà verificare che il valore di testo nuovo non JSON non valido.

## <a name="transforming-json-into-tabular-format"></a>Trasformazione JSON in formato tabulare

Database SQL Azure consente di trasformare raccolte JSON down dei dati JSON tabulare di formato e carico o la query.

OPENJSON è un valore nella tabella che analizza il testo JSON, individua una matrice di oggetti JSON, scorre gli elementi della matrice e restituisce un'unica riga nel risultato output per ogni elemento della matrice.

![JSON tabulare](./media/sql-database-json-features/image_2.png)

Nell'esempio precedente, è possibile specificare dove collocare la matrice JSON che deve essere aperte (in $. Percorso di ordini), deve essere restituiti come risultato e dove trovare i valori JSON che venga restituiti come celle delle colonne della tabella.

È possibile trasformare una matrice JSON il @orders variabile in una serie di righe, analizzare il set di risultati o inserire righe in una tabella standard:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
L'insieme di ordini formattata come matrice JSON e specificato come un parametro per stored procedure può essere analizzati predefinito e inserito nella tabella ordini.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come integrare JSON nell'applicazione, vedere queste risorse:

- [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentazione MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Canale video 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Per informazioni sui diversi scenari per l'integrazione JSON nell'applicazione, vedere le dimostrazioni in questo [video di Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) o trovare lo scenario che soddisfa il caso di utilizzo in [JSON post di blog](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).
