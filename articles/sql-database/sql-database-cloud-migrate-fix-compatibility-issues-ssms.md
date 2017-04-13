<properties
   pageTitle="Risolvere i problemi di compatibilità database di SQL Server con SQL Server Management Studio prima della migrazione al Database SQL | Microsoft Azure"
   description="Microsoft Database SQL di Azure, la migrazione del database, compatibilità, migrazione guidata di SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Risolvere i problemi di compatibilità database di SQL Server con SQL Server Management Studio prima della migrazione al Database SQL

> [AZURE.SELECTOR]
- Utilizzare [SQL Azure migrazione guidata](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilizzare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usare [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Per gli utenti avanzati possono risolvere i problemi di compatibilità database SQL Server con SQL Server Management Studio prima della migrazione a Database SQL Azure.


> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Con SQL Server Management Studio

Consente di risolvere i problemi di compatibilità con diversi comandi Transact-SQL, ad esempio **ALTER DATABASE**SQL Server Management Studio. Questo metodo è principalmente per gli utenti avanzati che sono in grado di lavorando Transact-SQL a un database attivo. In caso contrario, si consiglia di utilizzare SSDT. 



## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Eseguire la migrazione di un database di SQL Server compatibile al Database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
