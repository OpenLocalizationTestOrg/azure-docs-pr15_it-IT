<properties
   pageTitle="Eseguire la migrazione a Database SQL di replica transazione | Microsoft Azure"
   description="Database SQL di Microsoft Azure, la migrazione di database, importare database, replica transazione"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Eseguire la migrazione di database SQL Server di Database di SQL Azure con replica transazione

> [AZURE.SELECTOR]
- [SQL Server Management Studio migrazione guidata](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in File BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importa da File BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transazione replica](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

In questo articolo informazioni su come eseguire la migrazione di un database di SQL Server compatibile al Database SQL Azure con tempo di inattività minimo replica transazione SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Introduzione all'architettura di replica transazione

Quando è indispensabile rimuovere il database di SQL Server produzione mentre si sta eseguendo la migrazione, è possibile utilizzare la replica transazione di SQL Server come soluzione di migrazione. Per utilizzare questa soluzione, configurare il Database di SQL Azure come sottoscrittore istanza di SQL Server locale che si desidera eseguire la migrazione. I locale distribuzione transazione Sincronizza i dati da un database locale da sincronizzare (autore) mentre nuove transazioni continuano si verificano. 

È possibile usare anche replica transazione per eseguire la migrazione di un sottoinsieme del database locale. La pubblicazione che si replica di Database SQL Azure può essere limitata a un sottoinsieme di tabelle del database replicato. Per ogni tabella replicato, è possibile limitare i dati da un sottoinsieme delle righe e/o un sottoinsieme delle colonne.

Con la replica snapshot, tutte le modifiche apportate ai dati o schema mostrano nel Database di SQL Azure. Dopo la sincronizzazione è stata completata e si è pronti eseguire la migrazione, modificare la stringa di connessione delle applicazioni in modo che puntino loro al Database SQL Azure. Una volta replica transazione intensivo delle eventuali modifiche a sinistra del database locale e tutte le applicazioni scegliere DB Azure, è possibile disinstallare replica transazione. Il Database di SQL Azure è ora il sistema di produzione.

 ![Diagramma SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisiti di replica transazioni

Transazione replica è una tecnologia predefinita e integrata con SQL Server dopo SQL Server 6.5. È una tecnologia sperimentata e che la maggior parte degli amministratori di database fonti di informazioni con cui hanno esperienza. Con [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server), è ora possibile configurare il Database di SQL Azure come [transazione server di sottoscrizione](https://msdn.microsoft.com/library/mt589530.aspx) alla pubblicazione locale. L'esperienza di iniziare la configurazione da Management Studio è lo stesso come se è stato configurato un server di sottoscrizione di replica transazione su un server locale. Supporto per questo scenario è supportato quando il server di pubblicazione e il distributore sono almeno uno dei seguenti versioni di SQL Server:

 - SQL Server 2016 e versioni successive 
 - SQL Server 2014 SP1 CU3 e versioni successive
 - SQL Server 2014 RTM CU10 e versioni successive
 - SQL Server 2012 SP2 CU8 e versioni successive
 - SQL Server 2012 SP3 e versioni successive


> [AZURE.IMPORTANT] Utilizzare l'ultima versione di SQL Server Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. Le versioni precedenti di SQL Server Management Studio non è possibile configurare Database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Transazione replica](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
