<properties
   pageTitle="Risolvere i problemi di compatibilità di database di SQL Server prima della migrazione al Database SQL | Microsoft Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Utilizzare SQL Azure migrazione guidata per problemi di compatibilità risolvere SQL Server database prima della migrazione a Database SQL Azure

> [AZURE.SELECTOR]
- Utilizzare [SQL Azure migrazione guidata](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilizzare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usare [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

In questo articolo informazioni su come rilevare e correggere i problemi di compatibilità database di SQL Server tramite la migrazione guidata di SQL Azure prima della migrazione a Database SQL Azure.

## <a name="using-sql-azure-migration-wizard"></a>Utilizzo di SQL Azure migrazione guidata

Usare lo strumento di CodePlex [SQL Azure migrazione guidata](http://sqlazuremw.codeplex.com/) per generare uno script T-SQL da un database di origine non compatibili. Questo script viene quindi trasformato dalla procedura guidata per assicurarsi che sia compatibile con il Database di SQL. È quindi connettersi al Database di SQL Azure per eseguire lo script. Questo strumento analizza anche i file di analisi per determinare i problemi di compatibilità. Lo script può essere generato con solo schema o può includere dati in formato BCP. Documentazione aggiuntiva, tra cui istruzioni dettagliate è disponibile su CodePlex in [SQL Azure migrazione guidata](http://sqlazuremw.codeplex.com/).  

 ![Diagramma di migrazione SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Non tutti dello schema non compatibile con la procedura guidata rileva può essere risolti automaticamente le trasformazioni predefinite. Script compatibile che non possono essere indirizzati vengono segnalati come errori, con i commenti inseriti in script generato. Se vengono rilevati errori, utilizzare Visual Studio o SQL Server Management Studio per esaminare e correggere gli errori che non è possibile correggere utilizzando la migrazione guidata di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Eseguire la migrazione di un database di SQL Server compatibile al Database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
