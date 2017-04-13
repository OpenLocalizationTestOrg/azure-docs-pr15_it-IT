<properties
   pageTitle="Utilizzare le etichette alle query strumento SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di etichette alle query strumento Azure SQL Data warehouse per lo sviluppo di soluzioni."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utilizzare le etichette per le query strumento SQL Data warehouse
SQL Data Warehouse supporta l'etichette di query. Prima di passare in qualsiasi modo approfondito in seguito esaminare un esempio:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ultima riga selezionata tag la stringa 'Etichetta Query personali' alla query. Questa funzionalità è particolarmente utile come l'etichetta è in grado query tramite le viste. In questo modo ci un meccanismo per tenere traccia dei query problematiche e anche per identificare lo stato di avanzamento tramite un'esecuzione ETL.

Una buona convenzione di denominazione è estremamente utile qui. Ad esempio un nome simile ' progetto: procedura: istruzione: commento ' consentono di identificare la query in tra tutto il codice di controllo del codice sorgente.

Per eseguire la ricerca in base all'etichetta è possibile utilizzare la query seguente che utilizza le viste a gestione dinamica:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] È necessario disporre racchiusa tra parentesi quadre o virgolette doppie l'etichetta di word durante la ricerca. Etichetta di una parola riservata e verrà generato un errore se non è stata delimitato.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
