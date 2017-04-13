
<properties
   pageTitle="Esportare un database SQL Server in un file BACPAC con SQL Server Management Studio | Microsoft Azure"
   description="Database SQL di Microsoft Azure, la migrazione di database, esportare database, Esporta file BACPAC, creazione guidata applicazione esportare dati"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Esportare un database SQL Server in un file BACPAC con SQL Server Management Studio

> [AZURE.SELECTOR]
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
In questo articolo viene illustrato come esportare un database di SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) tramite l'esportazione guidata applicazione di livello dati di SQL Server Management Studio. 

1. Verificare di avere l'ultima versione di SQL Server Management Studio. Nuove versioni di Management Studio vengono aggiornate mensile di rimanere aggiornati con gli aggiornamenti al portale di Azure.

     > [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Destro del mouse sul database di origine in Esplora aree di oggetto, fare clic su **attività**e fare clic su **Esporta applicazione di livello di dati**

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Configurare l'esportazione per salvare il file BACPAC in un percorso sul disco locale o in un Azure blob dell'esportazione guidata. BACPAC esportato sempre include lo schema di database completo e, per impostazione predefinita, i dati da tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È possibile, ad esempio esportare solo i dati per le tabelle di riferimento e non da tutte le tabelle.

***Importante*** Quando si esporta una BACPAC a archiviazione blob Azure, utilizzare lo spazio di archiviazione standard. Importazione di un BACPAC dallo spazio di archiviazione premium non è supportata.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


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
