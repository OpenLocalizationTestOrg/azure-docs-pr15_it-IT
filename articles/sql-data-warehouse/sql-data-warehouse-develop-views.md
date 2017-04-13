<properties
   pageTitle="Visualizzazioni SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'utilizzo di visualizzazioni Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Visualizzazioni SQL Data warehouse

Visualizzazioni sono particolarmente utili SQL Data warehouse. Possono essere utilizzati in un numero di diversi modi per migliorare la qualità della soluzione.  In questo articolo evidenzia alcuni esempi su come migliorare la soluzione più visualizzazioni, nonché le limitazioni che devono essere considerate.

> [AZURE.NOTE] Sintassi di `CREATE VIEW` non viene descritto in questo articolo. Vedere l'articolo [CREATE VIEW][] su MSDN queste informazioni di riferimento.

## <a name="architectural-abstraction"></a>Architettura astrazione
Un modello di applicazione molto comune consiste nel creare di nuovo le tabelle utilizzando Creazione tabella come selezionare (CTAS) seguita da un oggetto ridenominazione motivo durante il caricamento dei dati.

Nell'esempio seguente aggiunge nuova data record a una dimensione Data. Si noti come un nuovo tabble, DimDate_New, è prima di tutto creata e quindi rinominato per sostituire la versione originale della tabella.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Questo approccio causando nelle tabelle visualizzate e spariscono dalla visualizzazione dell'utente, nonché i messaggi di errore "tabella non esiste". Visualizzazioni possono essere utilizzate per offrire agli utenti con un livello di presentazione coerente mentre gli oggetti sottostanti vengono rinominati. Per fornire agli utenti l'accesso ai dati tramite una visualizzazione, pertanto gli utenti non sono necessario visibilità delle tabelle. Fornisce un'esperienza uniforme mentre garantire che i dati warehouse ai designer dei siti possono evolversi nel modello di dati e ottimizzare le prestazioni utilizzando CTAS durante il processo di caricamento dei dati.    

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni
Visualizzazioni possono anche essere utilizzate per applicare il join di prestazioni tra le tabelle. Una visualizzazione, ad esempio possibile incorporare una chiave di distribuzione ridondanti come parte dei criteri join per ridurre al minimo lo spostamento dei dati.  Un altro vantaggio di una visualizzazione possibile forzare una specifica query o suggerimento join. L'utilizzo di visualizzazioni in questo modo garantisce le linee di join vengono sempre eseguite in modo ottimale senza necessità di utenti di ricordare il costrutto corretto per le linee di join.

## <a name="limitations"></a>Limitazioni
Visualizzazioni SQL Data warehouse sono solo metadati.  Di conseguenza le opzioni seguenti non sono disponibili:

-   Non è schema associazione
-   Tabelle di base non possono essere aggiornate tramite la visualizzazione
-   Non è possibile creare visualizzazioni rispetto alle tabelle temporanee
-   Non è disponibile alcun supporto per ESPANDI / suggerimenti NOEXPAND
-   Sono non disponibili le viste indicizzate SQL Data warehouse


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].
Per `CREATE VIEW` sintassi, vedere [Crea visualizzazione][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREAZIONE DI VISUALIZZAZIONE]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
