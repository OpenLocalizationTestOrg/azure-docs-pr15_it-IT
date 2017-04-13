<properties
   pageTitle="Eseguire la migrazione di database SQL Server di Database SQL utilizzando distribuire Database alla creazione guidata Database di Microsoft Azure | Microsoft Azure"
   description="Database SQL di Microsoft Azure, la migrazione di database, creazione guidata Database di Microsoft Azure"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Eseguire la migrazione di database SQL Server di Database SQL utilizzando distribuire Database alla creazione guidata Database di Microsoft Azure


> [AZURE.SELECTOR]
- [SQL Server Management Studio migrazione guidata](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in File BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importa da File BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transazione replica](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Il Database di distribuire la creazione guidata Database di Microsoft Azure in SQL Server Management Studio esegue la migrazione di un [database di SQL Server compatibile](sql-database-cloud-migrate.md) direttamente nel server Database SQL Azure.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Usare il Database di distribuzione guidata Database di Microsoft Azure

> [AZURE.NOTE] I passaggi seguenti presuppongono che si dispone di un [viene completato il provisioning di Database SQL server](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Verificare di avere l'ultima versione di SQL Server Management Studio. Nuove versioni di Management Studio vengono aggiornate mensile di rimanere aggiornati con gli aggiornamenti al portale di Azure.

    > [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di SQL Server per eseguire la migrazione in Esplora oggetti.
3. Pulsante destro del mouse del database in Esplora oggetti, fare clic su **attività**e fare clic su **Distribuire Database a Microsoft Azure SQL...**

    ![Distribuire Azure dal menu di attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  Nella distribuzione guidata, fare clic su **Avanti**e quindi fare clic su **Connetti** per configurare la connessione al server di Database SQL.

    ![Distribuire Azure dal menu di attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Nella finestra di dialogo Server Connetti immettere le informazioni di connessione per la connessione al server di Database SQL.

    ![Distribuire Azure dal menu di attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Specificare la procedura seguente per il file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) che consente di creare durante il processo di migrazione:

 - Il **nuovo nome di database** 
 - **Edizione di Microsoft Database SQL Azure** ([livello di servizio](sql-database-service-tiers.md))
 - La **dimensione massima del database**
 - L' **Obiettivo di servizio** (livello di prestazioni)
 - Il **nome del file temporaneo**  

    ![Esportare le impostazioni](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Completare la procedura guidata. In base alle dimensioni e alla complessità del database, la distribuzione può richiedere alcuni minuti al numero di ore. Se la procedura guidata rilevati problemi di compatibilità, gli errori sono visualizzati sullo schermo e non potrà continua la migrazione. Per indicazioni su come risolvere i problemi di compatibilità di database, vedere [risolvere i problemi di compatibilità database](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Usa Esplora oggetti, connettersi al database migrato nel server Database SQL Azure.
8.  Tramite il portale di Azure, consente di visualizzare il database e le relative proprietà.

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
