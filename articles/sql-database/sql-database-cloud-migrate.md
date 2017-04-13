<properties
   pageTitle="Migrazione del database di SQL Server di Database SQL | Microsoft Azure"
   description="Informazioni su come circa locale SQL Server la migrazione di database al Database di SQL Azure nel cloud. Utilizzare gli strumenti di migrazione di database per verificare la compatibilità prima di migrazione del database."
   keywords="database di migrazione, la migrazione del database di sql server, strumenti di migrazione del database, eseguire la migrazione del database, eseguire la migrazione di database sql"
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

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migrazione del database di SQL Server di Database SQL nel cloud

In questo articolo è illustrato come eseguire la migrazione di un locale SQL Server 2005 database o versioni successive a Database SQL Azure. In questo processo di migrazione di database, eseguire la migrazione dello schema e i dati da database di SQL Server nell'ambiente corrente nel Database di SQL. Per la corretta, il database esistente prima di tutto necessario passare una prova di compatibilità. Con [SQL Database V12](sql-database-v12-whats-new.md), esistono alcuni problemi di compatibilità rimanenti, ad eccezione di problema relativo alle operazioni a livello di server e tra database. Database e applicazioni basate su [parzialmente o non supportate funzioni](sql-database-transact-sql-information.md) necessarie alcune nuovamente l'engineering per risolvere le incompatibilità prima possibile eseguire la migrazione di database di SQL Server.

Per eseguire la migrazione, di seguito sono riportati i passaggi è possibile eseguire:

- **Test per la compatibilità**: convalidare la compatibilità di database con [SQL Database V12](sql-database-v12-whats-new.md). 
- **Risolvere problemi di compatibilità, se presenti**: se la convalida non riesce, è necessario correggere gli errori di convalida.  
- **Eseguire la migrazione** Una volta compatibile del database, è possibile usare uno o più metodi per eseguire la migrazione. 

SQL Server sono disponibili diversi metodi per eseguire ognuna di queste attività. In questo articolo viene fornita una panoramica dei metodi disponibili per ogni attività. Il diagramma seguente illustra i passaggi e i metodi.

  ![Diagramma di migrazione VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Per eseguire la migrazione di un database non SQL Server, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 al Database di SQL Azure, vedere [Migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Strumenti di migrazione del database verificano la compatibilità del database di SQL Server database SQL

Per verificare la compatibilità di Database SQL prima di iniziare il processo di migrazione di database, utilizzare uno dei metodi seguenti:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Notifica di aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SQL Server Data Tools per Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utilizza le regole di compatibilità più recenti per rilevare incompatibilità V12 di Database SQL. Se vengono rilevate incompatibilità, è possibile risolvere i problemi rilevati direttamente in questo strumento. Questo metodo è il metodo consigliato per testare e risolvere i problemi di compatibilità V12 di Database SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage è un'utilità della riga di comando che verifica compatibilità problemi e genera un report contenente i problemi di compatibilità rilevati. Se si usa questo strumento, assicurarsi di che utilizzare la versione più recente per usare le regole di compatibilità più recente. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere eventuali problemi di compatibilità rilevati - è consigliabile SSDT.  
- [Creazione guidata applicazione il livello di dati di esportazione in SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): questa procedura guidata vengono rilevate e segnalati errori sullo schermo. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al Database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere eventuali problemi di compatibilità rilevati - è consigliabile SSDT.
- [Nella casella Microsoft SQL Server 2016 aggiornamento Advisor anteprima](http://www.microsoft.com/download/details.aspx?id=48119): questo strumento autonomo che si trova attualmente in anteprima, rileva e genera un rapporto di incompatibilità V12 di Database SQL. Questo strumento non ha ancora le regole di compatibilità più recente. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al Database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere eventuali problemi di compatibilità rilevati - è consigliabile SSDT. 
- [Migrazione guidata di SQL Azure ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW è uno strumento codeplex che utilizza le regole di compatibilità V11 Database di SQL Azure per rilevare incompatibilità V12 di Database SQL Azure. Se vengono rilevate incompatibilità, è possano fissa alcuni problemi direttamente in questo strumento. Questo strumento potrebbe risultare incompatibilità che non devono essere corretti. È stato il primo Database di SQL Azure assistenza migrazione disponibili e attivamente supportata dalla community di SQL Server. Inoltre, questo strumento può completare la migrazione da all'interno dello strumento. 

## <a name="fix-database-migration-compatibility-issues"></a>Risolvere i problemi di compatibilità migrazione del database

Se vengono rilevati problemi di compatibilità, è necessario correggerli prima di procedere con la migrazione di database SQL Server. Sono disponibili una vasta gamma di problemi di compatibilità che possono verificarsi, a seconda del tipo di entrambi nella versione di SQL Server nel database di origine e la complessità del database si esegue la migrazione. Le versioni precedenti di SQL Server dispongono di più problemi di compatibilità. Utilizzare le seguenti risorse, oltre a una destinazione ricerca Internet utilizzando il motore di ricerca di scelte:

- [Funzioni di database SQL Server non supportate nel Database di SQL Azure](sql-database-transact-sql-information.md)
- [Funzionalità del motore di Database eliminate in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funzionalità del motore di Database eliminate in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funzionalità del motore di Database eliminate SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funzionalità del motore di Database eliminate in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funzionalità del motore di Database eliminate in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oltre alle ricerche su Internet e usare queste risorse, usare il [forum della community MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

Utilizzare uno dei seguenti strumenti di migrazione di database per risolvere i problemi rilevati:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Uso di [SQL Server Data Tools per Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): per usare SSDT, importate schema del database in SQL Server Data Tools per Visual Studio "SSDT") e compilare il progetto per una distribuzione V12 di Database SQL. Quindi possibile risolvere tutti i problemi di compatibilità rilevati in SSDT. Al termine, sincronizzare le modifiche al database di origine (o una copia del database di origine. SSDT è attualmente il metodo consigliato per testare e risolvere i problemi di compatibilità V12 di Database SQL. Fare clic sul collegamento per una [Panoramica utilizzando SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Usare [SQL Server Management Studio ("Express")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): per usare SQL Server Management Studio, si esegue comandi Transact-SQL per correggere gli errori rilevati con un altro strumento. Questo metodo è principalmente per gli utenti avanzati modificare lo schema di database direttamente nel database di origine. 
- Utilizzare [SQL Azure la migrazione guidata ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): per utilizzare SAMW, viene generato uno script Transact-SQL dal database di origine. La procedura guidata Trasforma lo script, se possibile, per rendere lo schema compatibile con V12 di Database SQL. Al termine, SAMW possibile connettersi a V12 di Database SQL per eseguire lo script. Questo strumento analizza anche i file di analisi per determinare i problemi di compatibilità. Lo script può essere generato con solo schema o può includere dati in formato BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Eseguire la migrazione di un database di SQL Server compatibile al Database SQL

Per eseguire la migrazione di un database di SQL Server compatibile, Microsoft offre diversi metodi di migrazione per i diversi scenari. Il metodo che scelto dipende la tolleranza per i tempi di inattività, le dimensioni e alla complessità del database SQL Server e la connessione al cloud Microsoft Azure.  

> [AZURE.SELECTOR]
- [SQL Server Management Studio migrazione guidata](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in File BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importa da File BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transazione replica](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Per scegliere il metodo di migrazione, la prima domanda è se non è possibile facile eseguire il database fuori produzione durante la migrazione. La migrazione di un database mentre si verificano transazioni attive può causare eventuali incoerenze del database e probabile danneggiamento del database. Sono disponibili molti metodi per disattivare gradualmente un database, da disabilitazione della connettività dei client per la creazione di uno [snapshot del database](https://msdn.microsoft.com/library/ms175876.aspx).

Per eseguire la migrazione con tempo di inattività minimo, utilizzare [la replica di SQL Server transazione](sql-database-cloud-migrate-compatible-using-transactional-replication.md) se il database soddisfa i requisiti per la replica transazione. Se si possono fare dei tempi di inattività o si sta eseguendo una migrazione di test di un database di produzione per la migrazione versioni successiva, valutare la possibilità di uno dei tre metodi seguenti:

- [SQL Server Management Studio migrazione guidata](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): per piccole e medie database, la migrazione di un compatibile SQL Server 2005 database o versioni successive è semplice come [Distribuire Database alla creazione guidata Database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) in esecuzione in SQL Server Management Studio.
- [Esportare in File BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e [Importa da File BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): se si hanno problemi di connettività (senza la connettività, larghezza di banda ridotta o problemi di timeout) e per le medie e database di grandi dimensioni, utilizzare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Con questo metodo si esportare lo schema di SQL Server e i dati in un file BACPAC. Quindi possibile importare il file BACPAC in Database SQL utilizzando l'esportazione dati livello applicazione guidata SQL Server Management Studio o l'utilità di prompt dei comandi [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
- Usare BACPAC e BCP insieme: usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per molto più grandi database per ottenere maggiore parallelizzazione per un aumento delle prestazioni, nonostante con più complessi. Con questo metodo, eseguire la migrazione dei dati e lo schema separatamente.
 - È possibile [esportare lo schema solo in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importazione dello schema solo dal File BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) nel Database di SQL.
 - Utilizzare [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per estrarre i dati in file flat e quindi [in parallelo carico](https://technet.microsoft.com/library/dd425070.aspx) questi file nel Database di SQL Azure.

     ![SQL Server database migrazione - eseguire la migrazione di database SQL nel cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Passaggi successivi

- [Anteprima di preparazione aggiornamento di Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119)
- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
[Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
