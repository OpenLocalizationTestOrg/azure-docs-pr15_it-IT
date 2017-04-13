<properties
   pageTitle="Importare al Database SQL da un file BACPAC utilizzando SqlPackage"
   description="Database SQL di Microsoft Azure, la migrazione di database, importare database, importare file di BACPAC, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importare al Database SQL da un file BACPAC utilizzando SqlPackage

> [AZURE.SELECTOR]
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In questo articolo viene illustrato come importare al database SQL da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizzare l'utilità [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direttamente dall'area download Microsoft.


> [AZURE.NOTE] I passaggi seguenti presuppongono hanno già effettuato il provisioning di un server di Database SQL, avere a portata di mano le informazioni di connessione e aver verificato che il database di origine sia compatibile.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importare da un file BACPAC nel Database di SQL Azure con SqlPackage

Utilizzare la procedura seguente per utilizzare l'utilità della riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database di SQL Server compatibile (o un database SQL Azure) da un file BACPAC.

> [AZURE.NOTE] I passaggi seguenti presuppongono che hanno già effettuato il provisioning di un server di Database SQL Azure e di avere a portata di mano le informazioni di connessione.

1. Aprire un prompt dei comandi e modificare una directory contenente l'utilità di sqlpackage.exe: questa utilità viene fornita con Visual Studio e SQL Server.
2. Eseguire il seguente comando sqlpackage.exe con gli argomenti seguenti per l'ambiente:

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argomento  | Descrizione  |
  	|---|---|
  	| < nome_server >  | nome del server di destinazione  |
  	| < nome_database >  | Nome database di destinazione  |
  	| < nome_utente >  | il nome utente nel server di destinazione |
  	| < password >  | la password dell'utente  |
  	| < file_di >  | il nome del file e il percorso per il file BACPAC importato  |

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
