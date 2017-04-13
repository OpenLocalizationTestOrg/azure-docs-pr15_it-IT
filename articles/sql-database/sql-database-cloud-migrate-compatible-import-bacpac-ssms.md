<properties
   pageTitle="Eseguire la migrazione di un database SQL Server di Database SQL Azure | Microsoft Azure"
   description="Database SQL di Microsoft Azure, database distribuire, la migrazione di database, database di importazione, esportazione database, la migrazione guidata"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importare da BACPAC al Database SQL con SQL Server Management Studio

> [AZURE.SELECTOR]
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In questo articolo viene illustrato come importare da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) al Database SQL di utilizzare l'esportazione guidata applicazione di livello dati di SQL Server Management Studio.

> [AZURE.NOTE] I passaggi seguenti presuppongono che si hanno già effettuato il provisioning l'istanza di SQL Azure logico e avere a portata di mano le informazioni di connessione.

1. Verificare di avere l'ultima versione di SQL Server Management Studio. Nuove versioni di Management Studio vengono aggiornate mensile di rimanere aggiornati con gli aggiornamenti al portale di Azure.

     > [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Connettersi al server di Database SQL Azure, fare clic sulla cartella **database** e fare clic su **Importa dati applicazione...**

    ![Voce di menu applicazione livello dati di importazione](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Per creare il database nel Database di SQL Azure, importare un file BACPAC da disco locale o selezionare l'account di archiviazione Azure e contenitore in cui è stato caricato il file BACPAC.

    ![Impostazioni di importazione](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Quando si importa un BACPAC dallo spazio di archiviazione blob Azure, utilizzare lo spazio di archiviazione standard. Importazione di un BACPAC dallo spazio di archiviazione premium non è supportata.

4.  Specificare il **nuovo nome di database** per il database sul database di SQL Azure, impostare l' **Edizione di Microsoft Database SQL Azure** (livello di servizio), **dimensione massima del database**e **Obiettivo di servizio** (livello di prestazioni).

    ![Impostazioni del database](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Fare clic su **Avanti** e quindi fare clic su **Fine** per importare il file BACPAC in un nuovo database nel server di Database SQL Azure.

6. Usa Esplora oggetti, connettersi al database migrato nel server Database SQL Azure.

6.  Tramite il portale di Azure, consente di visualizzare il database e le relative proprietà.

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
