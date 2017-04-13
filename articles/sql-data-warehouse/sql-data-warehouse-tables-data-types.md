<properties
   pageTitle="Tipi di dati per le tabelle di SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva di tipi di dati per tabelle Warehouse dati di SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipi di dati per le tabelle di SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

Supporta SQL Data Warehouse quelli più comunemente usati tipi di dati.  Di seguito è riportato un elenco dei tipi di dati supportati da SQL Data Warehouse.  Per informazioni dettagliate sul tipo di dati di supporto, vedere [creazione tabella][].

|**Tipi di dati supportati**|||
|---|---|---|
[bigint][]|[decimale][]|[smallint][]|
[binario][]|[margine di flessibilità][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[caratt][]|[denaro][]|[ora][]|
[Data][]|[nchar][]|[tinyint][]|
[DateTime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[parte reale][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Procedure consigliate del tipo di dati

 Quando si definisce i tipi di colonna, utilizzando il tipo di dati più piccolo supporterà i dati si le prestazioni di query. Questa operazione è particolarmente importante per le colonne CHAR e VARCHAR. Se il valore più lungo in una colonna di 25 caratteri, definire la colonna come VARCHAR(25). Evitare la definizione di tutte le colonne di carattere per una lunghezza predefinita di grandi dimensioni. Inoltre, definire le colonne come VARCHAR quando non è necessaria, piuttosto che utilizzare [NVARCHAR][].  Utilizzare nvarchar (4000) o VARCHAR(8000) laddove possibile invece che tramite nvarchar (max) o varchar (max).

## <a name="polybase-limitation"></a>Limitazione Polybase

Se si utilizza Polybase per caricare le tabelle, definire le tabelle in modo che le dimensioni massime riga possibili, inclusi la lunghezza completa delle colonne di lunghezza variabile, non superi 32.767 byte.  Mentre è possibile definire una riga contenente dati di lunghezza variabile in grado di superano la larghezza e di caricare righe con BCP, non sarà possibile utilizzare Polybase per caricare questi dati.  Presto verrà aggiunti Polybase supporto per le righe larghezza.

## <a name="unsupported-data-types"></a>Tipi di dati non supportate

Se si esegue la migrazione del database da un'altra piattaforma SQL come Database di SQL Azure, come si esegue la migrazione, è possibile riscontrare alcuni tipi di dati che non sono supportati in SQL Data Warehouse.  Di seguito sono i tipi di dati non supportate, nonché alcune alternative che è possibile utilizzare al posto dei tipi di dati non supportate.

|Tipo di dati|Soluzione alternativa|
|---|---|
|[geometria][]|[varbinary][]|
|[area geografica][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4000)|
|[immagine][ntext,text,image]|[varbinary][]|
|[testo][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Dividi colonna in più colonne fortemente.|
|[tavolo][]|Convertire in tabelle temporanee.|
|[timestamp][]|Rielaborare il codice per usare [datetime2][] e `CURRENT_TIMESTAMP` funzione.  Sono supportate solo le costanti come predefinite, pertanto current_timestamp non possono essere definite come vincolo default. Se è necessario eseguire la migrazione di valori di versione di riga da una colonna digitato timestamp utilizzare [binario][](8) o [VARBINARY][binario](8) per i valori di versione di riga non NULL o NULL.|
|[XML][]|[varchar][]|
|[tipi definiti dall'utente][]|convertire in relativi tipi nativi laddove possibile|
|valori predefiniti|valori predefiniti supportano solo costanti e valori letterali.  Espressioni non deterministica o funzioni, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate.|

Di seguito SQL può essere eseguito su corrente database SQL per identificare le colonne non è supportata da Azure SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli in [Tabella Panoramica][Panoramica], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella di][indice], [partizione di una tabella][partizione], [Mantenendo le statistiche delle tabelle][statistiche] e [Tabelle temporanee][temporaneo].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].

<!--Image references-->

<!--Article references-->
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribuire]: ./sql-data-warehouse-tables-distribute.md
[Indice]: ./sql-data-warehouse-tables-index.md
[Partizione]: ./sql-data-warehouse-tables-partition.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporaneo]: ./sql-data-warehouse-tables-temporary.md
[Procedure consigliate Warehouse dati SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[Crea tabella]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binario]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[caratt]: https://msdn.microsoft.com/library/ms176089.aspx
[Data]: https://msdn.microsoft.com/library/bb630352.aspx
[DateTime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimale]: https://msdn.microsoft.com/library/ms187746.aspx
[margine di flessibilità]: https://msdn.microsoft.com/library/ms173773.aspx
[geometria]: https://msdn.microsoft.com/library/cc280487.aspx
[area geografica]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[denaro]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[parte reale]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tavolo]: https://msdn.microsoft.com/library/ms175010.aspx
[ora]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[tipi definiti dall'utente]: https://msdn.microsoft.com/library/ms131694.aspx
