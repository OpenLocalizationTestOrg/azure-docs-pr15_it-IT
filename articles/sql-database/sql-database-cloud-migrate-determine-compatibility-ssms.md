<properties
   pageTitle="Usare SQL Server Management Studio per la compatibilità di Database SQL di determinare prima della migrazione a Database SQL Azure | Microsoft Azure"
   description="Microsoft Database SQL di Azure, la migrazione del database, la compatibilità del Database di SQL, esportazione guidata di applicazione livello dati"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Usare SQL Server Management Studio per la compatibilità di Database SQL di determinare prima della migrazione a Database SQL Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Notifica di aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
In questo articolo informazioni su come determinare se un database SQL Server è compatibile per eseguire la migrazione di Database SQL utilizzando l'esportazione guidata applicazione di livello dati di SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Con SQL Server Management Studio

1. Verificare di avere l'ultima versione di SQL Server Management Studio. Nuove versioni di Management Studio vengono aggiornate mensile di rimanere aggiornati con gli aggiornamenti al portale di Azure.

     > [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
3. Destro del mouse sul database di origine in Esplora aree di oggetto, fare clic su **attività**e fare clic su **Esporta applicazione di livello di dati**

    ![Esportare un'applicazione di livello dati dal menu di attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Esportazione guidata fare clic su **Avanti**e quindi nella scheda **Impostazioni** configurare Esporta per salvare il file BACPAC in un'altra posizione un disco locale o in un archivio blob Azure. Salvataggio di un file BACPAC se si dispone non sono presenti problemi di compatibilità database. Se sono presenti problemi di compatibilità, vengono visualizzati nella console.

    ![Esportare le impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Per ignorare l'esportazione di dati, fare clic su **scheda Avanzate** e deselezionare la casella di controllo **Seleziona tutto** . A questo punto, il nostro obiettivo è solo per verificare la compatibilità.

    ![Esportare le impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Fare clic su **Avanti** e quindi fare clic su **Fine**. Problemi di compatibilità database, se presenti, vengono visualizzati dopo la procedura guidata convalida lo schema.

    ![Esportare le impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se viene visualizzato alcun errore, il database è compatibile e si è pronti eseguire la migrazione. Se si dispone di errori, è necessario per correggerli. Per visualizzare gli errori, fare clic su **errore** per lo **schema di convalida**. 
    ![Esportare le impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Se il *. Correttamente, viene generato BACPAC file, quindi il database è compatibile con Database SQL e si è pronti eseguire la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Risolvere i problemi di compatibilità migrazione del database](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Eseguire la migrazione di un database di SQL Server compatibile al Database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
