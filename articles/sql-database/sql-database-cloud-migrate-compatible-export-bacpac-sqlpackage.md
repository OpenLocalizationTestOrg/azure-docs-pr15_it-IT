<properties
   pageTitle="Esportare un database di SQL Server in un file BACPAC utilizzando SqlPackage | Microsoft Azure"
   description="Database SQL di Microsoft Azure, la migrazione di database, esportare database, Esporta file BACPAC, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Esportare un database di SQL Server in un file BACPAC mediante SqlPackage

> [AZURE.SELECTOR]
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

In questo articolo viene illustrato come esportare un database SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizzare l'utilità [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

1. Aprire un prompt dei comandi e modificare una directory contenente l'utilità di sqlpackage.exe: questa utilità viene fornita con Visual Studio e SQL Server. Usare la ricerca nel computer per trovare il percorso nel proprio ambiente.
2. Eseguire il seguente comando sqlpackage.exe con gli argomenti seguenti per l'ambiente:

    ' sqlpackage.exe /Action:Export /ssn: /sdn < nome_server >: < nome_database > /tf: < target_file >

  	| Argomento  | Descrizione  |
  	|---|---|
  	| < nome_server >  | nome del server di origine  |
  	| < nome_database >  | Nome database di origine  |
  	| < target_file >  | nome del file e percorso del file BACPAC  |

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importare un BACPAC al Database di SQL Azure con SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importare un BACPAC SqlPackage Database SQL Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importare un BACPAC al portale di Azure di Database SQL Azure](sql-database-import.md)
- [Importare un BACPAC PowerShell di Database SQL Azure](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
