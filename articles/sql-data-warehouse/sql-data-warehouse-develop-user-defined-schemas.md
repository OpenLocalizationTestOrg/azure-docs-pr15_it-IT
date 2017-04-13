<properties
   pageTitle="Schemi definiti dall'utente in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di schemi Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni."
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

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Schemi definiti dall'utente in SQL Data Warehouse

Tradizionale data warehouse spesso utilizzano database separati per creare i limiti di applicazione in base a carico di lavoro, dominio o sicurezza. Ad esempio un data warehouse di SQL Server tradizionale potrebbe contenere un database di gestione temporanea, un database di data warehouse e alcuni database mart dati. In questa topologia ogni database funziona come un carico di lavoro e un limite di protezione nell'architettura.

Se invece SQL Data Warehouse viene eseguito il carico di lavoro warehouse tutti i dati all'interno di un database. Cross database join non sono consentiti. Pertanto SQL Data Warehouse prevede tutte le tabelle usate dalla warehouse per essere memorizzati all'interno di un database.

> [AZURE.NOTE] SQL Data Warehouse non supporta le query di database cross di qualsiasi tipo. Di conseguenza, saranno necessario implementazione warehouse dei dati che si avvalgono di questo modello di essere modificato.

## <a name="recommendations"></a>Consigli

Si tratta di consigli per il consolidamento dei carichi di lavoro, protezione, dominio e i limiti funzionali tramite gli schemi definiti dall'utente

1. Usare un database di SQL Data Warehouse per eseguire il carico di lavoro di magazzino di tutti i dati
2. Consolidare i dati warehouse ambiente esistente per utilizzare un database di SQL Data Warehouse
3. Sfruttare **schemi definiti dall'utente** per fornire i limiti precedentemente implementata utilizzando i database.

Se gli schemi definiti dall'utente non sono stati utilizzati in precedenza è che zero. Utilizzare il nome del database precedente come base per gli schemi definiti dall'utente nel database di SQL Data Warehouse.

Se sono già stati utilizzati gli schemi sono disponibili alcune opzioni:

1. Rimuovere i nomi di schema legacy e ricominciare dall'inizio
2. Mantenere i nomi di schema legacy anteporre il nome dello schema legacy al nome della tabella
3. Mantenere i nomi di schema legacy mediante l'implementazione di visualizzazioni sopra la tabella in uno schema aggiuntiva per ricreare la struttura dello schema precedente.

> [AZURE.NOTE] Al primo opzione 3 può sembrare l'opzione più interessante. Importante sottolineare è però i dati di dettaglio. Visualizzazioni sono di sola lettura nei SQL Data Warehouse. Qualsiasi modifica apportata ai dati o della tabella dovrebbe essere eseguita tabella di base. Opzione 3 è stato inoltre introdotto un livello di visualizzazioni all'interno del sistema. È consigliabile conferire questo aggiuntive se si utilizza le viste dell'architettura già.


### <a name="examples"></a>Esempi:

Implementare gli schemi definiti dall'utente in base ai nomi di database

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi di schema legacy anteporre loro al nome della tabella. Schemi per il limite di carico di lavoro.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi di schema legacy usando le visualizzazioni

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Le eventuali modifiche apportate strategia schema deve una revisione del modello di sicurezza per il database. In molti casi è possibile semplificare il modello di sicurezza mediante l'assegnazione di autorizzazioni a livello di schema. Se sono necessarie le autorizzazioni più granulare è possibile utilizzare i ruoli del database.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
