<properties
   pageTitle="Risolvere i problemi di compatibilità di database di SQL Server prima della migrazione al Database SQL | Microsoft Azure"
   description="Microsoft Database SQL di Azure, la migrazione del database, compatibilità, migrazione guidata di SQL Azure, SSDT"
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

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Eseguire la migrazione di un Database SQL Server di Database SQL Azure con SQL Server Data Tools per Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Notifica di aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

In questo articolo informazioni su come rilevare e correggere i problemi di compatibilità database di SQL Server con SQL Server Data Tools per Visual Studio prima della migrazione a Database SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Utilizzo di SQL Server Data Tools per Visual Studio

Usare SQL Server Data Tools per Visual Studio ("SSDT") per importare lo schema di database in un progetto di database di Visual Studio per l'analisi. Per analizzare, specificare la piattaforma di destinazione per il progetto come V12 di Database SQL e quindi compilare il progetto. Se la compilazione ha esito positivo, il database è compatibile. Se la compilazione non riesce, è possibile risolvere gli errori in SSDT (o uno degli strumenti descritte in questo argomento). Una volta generato il progetto, è possibile pubblicare nuovamente come una copia del database di origine. È quindi possibile utilizzare la funzionalità di confronto dei dati in SSDT per copiare i dati dal database di origine al database compatibile V12 SQL Azure. È quindi possibile eseguire la migrazione di questo database aggiornato. Per usare questa opzione, scaricare la [versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagramma di migrazione VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se è necessaria solo allo schema di migrazione, lo schema può essere pubblicato direttamente da Visual Studio direttamente al Database SQL Azure. Utilizzare questo metodo quando lo schema di database richiede ulteriori modifiche che è possibile gestire la migrazione guidata singolarmente.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Rilevare i problemi di compatibilità con SQL Server Data Tools per Visual Studio
   
1.  Aprire **Esplora oggetti SQL Server** in Visual Studio. **Aggiunta di SQL Server** consente di connettersi all'istanza di SQL Server contenente il database di cui eseguire la migrazione. Individuare il database in Esplora oggetti, rapida il database e selezionare **Crea nuovo progetto**     
    
    ![Nuovo progetto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Configurare le impostazioni di importazione per **importare solo oggetti nell'ambito dell'applicazione**. Deselezionare le opzioni per importare le operazioni seguenti: a cui fa riferimento accessi, autorizzazioni e impostazioni del database.    

    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Fare clic su **Avvia** per importare il database e creare il progetto che contiene un file di script T-SQL per tutti gli oggetti nel database. I file di script nidificati nelle cartelle all'interno del progetto.    

    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  In Esplora soluzioni di Visual Studio, fare clic sul progetto database e scegliere Proprietà. Nella pagina **Impostazioni di Project** , configurare la piattaforma di destinazione per V12 di Database SQL di Microsoft Azure.    
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Fare clic sul progetto, quindi selezionare **Compila** per compilare il progetto.    
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  Nell' **Elenco errori** consente di visualizzare ogni problemi di compatibilità. In questo caso, il nome utente NT Authority\Servizio incompatibile. Poiché è incompatibile, è possibile impostarla come commento o rimuoverlo e le implicazioni della rimozione della soluzione di database di questo account di accesso e il ruolo di indirizzi.     
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Risoluzione dei problemi di compatibilità con SQL Server Data Tools per Visual Studio

1.  Fare doppio clic sul primo script per aprire lo script in una finestra query e lo script di commento e quindi eseguire lo script.     
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Ripetere questa procedura per ogni script contenente incompatibilità fino a raggiungere alcun errore.    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Quando il database è privo di errori di, fare clic sul progetto e selezionare **pubblica**. Compilazione e pubblicare una copia del database di origine (si consiglia di utilizzare una copia, almeno inizialmente).     
 - Prima di pubblicare, a seconda della versione di SQL Server di origine (precedenti a SQL Server 2014), potrebbe essere necessario ripristinare la piattaforma del progetto destinazione per consentire la distribuzione.     
 - Se si esegue la migrazione di un database di SQL Server precedente, non introdurre caratteristiche nel progetto che non sono supportati in SQL Server di origine fino a eseguire la migrazione del database in una versione più recente di SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  In SQL Server Esplora oggetti destro del mouse sul database di origine e fare clic su **Confronto dei dati**. Confronto tra il progetto al database originale consente di comprendere le modifiche apportate dalla procedura guidata. Selezionare la versione V12 SQL Azure del database e quindi fare clic su **Fine**.    
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Esaminare le differenze rilevate e quindi fare clic su **Aggiorna destinazione** per eseguire la migrazione dei dati dal database di origine nel database V12 SQL Azure.     
    
    ![testo alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Scegliere un metodo di distribuzione. Vedere [eseguire la migrazione di un database di SQL Server compatibile al Database SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Passaggi successivi

- [Versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive

- [SQL Database V12](sql-database-v12-whats-new.md)
- [In Transact-SQL parzialmente o non supportate funzioni](sql-database-transact-sql-information.md)
- [Eseguire la migrazione di database non SQL Server tramite la migrazione guidata di SQL Server](http://blogs.msdn.com/b/ssma/)
