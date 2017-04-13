<properties
   pageTitle="Determinare la compatibilità di Database SQL utilizzando SqlPackage.exe | Microsoft Azure"
   description="Microsoft Database SQL di Azure, la migrazione del database, la compatibilità del Database di SQL, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinare la compatibilità di Database SQL utilizzando SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Notifica di aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

In questo articolo informazioni su come determinare se un database SQL Server è compatibile per eseguire la migrazione al Database SQL usando l'utilità di prompt dei comandi [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Utilizzo SqlPackage.exe

1. Aprire un prompt dei comandi e modificare una directory contenente la versione più recente di sqlpackage.exe. Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direttamente dall'area download Microsoft.
2. Eseguire il seguente comando SqlPackage con gli argomenti seguenti per l'ambiente:

    ' sqlpackage.exe /Action:Export /ssn: /sdn < nome_server >: < nome_database > /tf: /p:TableData < target_file > = < schema_name.table_name >>< file di output > 2 > & 1'

  	| Argomento  | Descrizione  |
  	|---|---|
  	| < nome_server >  | nome del server di origine  |
  	| < nome_database >  | Nome database di origine  |
  	| < target_file >  | nome del file e percorso del file BACPAC  |
  	| < schema_name.table_name >  | le tabelle per i quali dati vengono restituiti i file di destinazione  |
  	| < file di output >  | il nome del file e il percorso del file di output con errori, se presenti  |

    Il motivo per l'argomento /p:TableName è che si desidera verificare la compatibilità di database per l'esportazione in V12 DB di SQL Azure invece di esportare i dati da tutte le tabelle. L'argomento di esportazione per sqlpackage.exe Purtroppo non supporta l'estrazione zero tabelle. È necessario specificare almeno una tabella, ad esempio una piccola tabella singola. File di output < > contiene il report degli errori. La "> 2 > & 1" stringa tubi output standard e l'errore standard risultante dall'esecuzione del comando al file di output specificato.

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Aprire il file di output e controllare gli errori di compatibilità, se presenti. 

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Risolvere i problemi di compatibilità migrazione del database](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Eseguire la migrazione di un database di SQL Server compatibile al Database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
