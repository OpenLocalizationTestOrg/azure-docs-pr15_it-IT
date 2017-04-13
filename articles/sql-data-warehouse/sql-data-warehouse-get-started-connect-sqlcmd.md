<properties
   pageTitle="Query SQL Azure Data Warehouse (sqlcmd) | Microsoft Azure"
   description="Invio di query Warehouse di dati di SQL Azure con sqlcmd utilità della riga di comando."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Query SQL Azure Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprendimento Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

In questa procedura dettagliata utilizza l'utilità della riga di comando [sqlcmd][] eseguire query su un Data Warehouse SQL Azure.  

## <a name="1-connect"></a>1. la connessione di

Per iniziare a utilizzare [sqlcmd][], aprire il prompt dei comandi e immettere **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione richiede i parametri seguenti:

+ **Server (-S):** Server nel modulo `<`nome del Server`>`. database.windows.net
+ **Database (-d):** Nome del database.
+ **Attiva identificatori tra virgolette (-si):** Identificatori devono essere abilitati per la connessione a un'istanza di SQL Data Warehouse.

Per utilizzare l'autenticazione di SQL Server, è necessario aggiungere i parametri di nome utente/password:

+ **Utente (-U):** Utente server nel modulo `<`utente`>`
+ **Password (-P):** Password associata all'utente.

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Per utilizzare l'autenticazione integrata Azure Active Directory, è necessario aggiungere i parametri di Azure Active Directory:

+ **Autenticazione di azure Active Directory (-G):** utilizzare Azure Active Directory per l'autenticazione

Ad esempio, la stringa di connessione sarà simile alla seguente:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] È necessario [attivare l'autenticazione di Azure Active Directory](sql-data-warehouse-authentication.md) eseguire l'autenticazione tramite Active Directory.

## <a name="2-query"></a>2. query di

Dopo la connessione, è possibile inviare eventuali istruzioni Transact-SQL supportate rispetto all'istanza.  In questo esempio vengono inviate le query in modalità interattiva.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

In questi esempi successivi viene descritto come eseguire la query in modalità batch utilizzando l'opzione -Q o tubazioni la stringa SQL a sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni disponibili per sqlcmd, vedere [documentazione sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[SQLCMD]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
