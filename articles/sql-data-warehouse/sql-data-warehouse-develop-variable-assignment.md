<properties
   pageTitle="Assegnare le variabili di SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'assegnazione di variabili Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="assign-variables-in-sql-data-warehouse"></a>Assegnare le variabili di SQL Data Warehouse
Le variabili di SQL Data Warehouse siano impostate con la `DECLARE` istruzione o `SET` istruzione.

Tutte le operazioni seguenti modi perfettamente valido per impostare un valore variabile:

## <a name="setting-variables-with-declare"></a>Impostazione delle variabili con una dichiarazione

Inizializzazione di variabili con una dichiarazione corrisponde a uno dei modi più flessibili per impostare un valore variabile SQL Data warehouse.

```sql
DECLARE @v  int = 0
;
```

È anche possibile utilizzare DECLARE per impostare più di una variabile alla volta. Non è possibile utilizzare `SELECT` o `UPDATE` per eseguire questa operazione:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e utilizzare una variabile nella stessa istruzione DECLARE. Per illustrare il punto di esempio riportato di seguito **non** è consentito come @p1 viene sia inizializzato e usati nella stessa istruzione DECLARE. Questa operazione verrà generato un errore.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Impostare valori di SET
Set è un metodo molto comune per l'impostazione di una singola variabile.

Tutti gli esempi riportati di modi valida dell'impostazione di una variabile con impostato:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

È possibile impostare solo una variabile contemporaneamente insieme. Tuttavia, può essere visualizzato sopra operatori composti da gode.

## <a name="limitations"></a>Limitazioni
È possibile utilizzare SELECT o UPDATE per un'assegnazione di variabile.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
