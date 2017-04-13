<properties
    pageTitle="Abilitare il Database di zoom per un database | Microsoft Azure"
    description="Informazioni su come configurare un database per Database di zoom."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>Abilitare il Database di zoom per un database

Per configurare un database esistente per il Database di zoom, selezionare attività **| Zoom | Abilitare** per un database di SQL Server Management Studio per aprire la creazione guidata **Database abilitare per zoom** . È inoltre possibile utilizzare Transact\-SQL per abilitare il Database di zoom per un database.

Se si seleziona attività **| Zoom | Abilitare** per una singola tabella e si dispone non ancora è abilitato il database per Database zoom, la procedura guidata Configura il database per Database di zoom e consente di selezionare le tabelle come parte del processo. Seguire i passaggi descritti in questo argomento anziché la procedura descritta in [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-database.md).

Per abilitare il Database di zoom in un database o una tabella è necessario db\_delle autorizzazioni di proprietario. Attivazione di zoom Database in un database richiede anche le autorizzazioni di controllo DATABASE.

 >   [AZURE.NOTE] In seguito, se si disabilita il Database di zoom, tenere presente che la disattivazione di Database di zoom per una tabella o per un database non comporta l'eliminazione dell'oggetto remoto. Se si desidera eliminare la tabella remota o il database remoto, è necessario rilasciarla tramite il portale di gestione Azure. Gli oggetti remoti continuano a sostenere costi Azure finché non viene eliminato manualmente.

## <a name="before-you-get-started"></a>Prima di iniziare

-   Prima di configurare un database per zoom, è consigliabile eseguire zoom Database proporrà per identificare i database e le tabelle che sono idonee per zoom. Zoom Database proporrà identifica anche problemi di blocco. Per ulteriori informazioni, vedere [identificare database e le tabelle per Database di zoom](sql-server-stretch-database-identify-databases.md).

-   Esaminare [le limitazioni per allontanare Database](sql-server-stretch-database-limitations.md).

-   Estensione Database esegue la migrazione di dati di Azure. Pertanto è necessario disporre di un account Azure e un abbonamento per la fatturazione. Per ottenere un account Azure, [fare clic qui](http://azure.microsoft.com/pricing/free-trial/).

-   Disporre le informazioni di connessione e accesso è necessario creare un nuovo server Azure o selezionare un server Azure esistente.

## <a name="EnableTSQLServer"></a>Prerequisito: Attivare zoom Database sul server
Affinché sia possibile selezionare Zoom Database in un database o una tabella, è necessario abilitare sul server locale. Questa operazione richiede le autorizzazioni del sistema o serveradmin.

-   Se si dispone delle autorizzazioni amministrative necessarie, la creazione guidata **Database abilitare per zoom** configura il server di zoom.

-   Se non si dispone delle autorizzazioni necessarie, un amministratore deve attivare l'opzione manualmente eseguendo **sp\_configurare** prima di eseguire la procedura guidata o un amministratore deve eseguire la procedura guidata.

Per attivare manualmente il Database di zoom nel server, eseguire **sp\_configurare** e attivare l'opzione di **archivio dati remoto** . Nell'esempio seguente viene attivata l'opzione di **archivio di dati remoti** impostando il relativo valore su 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Per ulteriori informazioni, vedere [configurare i dati remoti archiviare opzione di configurazione Server](https://msdn.microsoft.com/library/mt143175.aspx) e [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Utilizzare la procedura guidata per abilitare l'estensione Database in un database
Per informazioni sul Database di abilitare per guidata zoom, tra cui le informazioni che è necessario immettere e le opzioni che è necessario apportare, vedere [Guida introduttiva eseguendo il Database attivare zoom guidata](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Utilizzare Transact\-SQL per abilitare l'estensione Database in un database
Affinché sia possibile selezionare il Database di zoom in singole tabelle, è necessario abilitare nel database.

Per abilitare il Database di zoom in un database o una tabella è necessario db\_delle autorizzazioni di proprietario. Attivazione di zoom Database in un database richiede anche le autorizzazioni di controllo DATABASE.

1.  Prima di iniziare, scegliere un server Azure esistente per i dati che esegue la migrazione di zoom Database o creare un nuovo server Azure.

2.  In server Azure, creare una regola con l'intervallo di indirizzi IP di SQL Server che consente di SQL Server comunicare con il server remoto.

    È possibile trovare facilmente i valori, è necessario, creare la regola firewall per tentare di connettersi al server Azure da Esplora oggetti in SQL Server Management Studio (SQL Server Management Studio). SQL Server Management Studio consente di creare la regola, aprire la finestra di dialogo che include i valori di indirizzi IP richiesti.

    ![Creare una regola in SQL Server Management Studio][FirewallRule]

3.  Per configurare un database SQL Server per Database zoom, il database deve avere una chiave di schema del database. La chiave di schema del database consente di proteggere le credenziali zoom Database viene utilizzato per la connessione al database remoto. Ecco un esempio per creare una nuova chiave master del database.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Per ulteriori informazioni sulla chiave di schema del database, vedere [creare chiave MASTER (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [creare una chiave di schema del Database](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Quando si configura un database per Database estesa, è necessario fornire le credenziali per il Database di zoom da utilizzare per le comunicazioni tra locale in SQL Server e il server Azure remoto. Sono disponibili due opzioni.

    -   È possibile fornire una credenziali di amministratore.

        -   Se si abilita zoom Database eseguendo la procedura guidata, è possibile creare le credenziali in quel momento.

        -   Se si intende attivare zoom Database eseguendo **ALTER DATABASE**, è necessario creare le credenziali manualmente prima di eseguire **ALTER DATABASE** per abilitare il Database di zoom.

        Ecco un esempio per creare nuove credenziali.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Per ulteriori informazioni sulle credenziali, vedere [Creare DATABASE nell'ambito delle CREDENZIALI (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Creare le credenziali sono necessarie autorizzazioni ALTER ANY CREDENTIAL.

    -   È possibile usare un account di servizio federata per SQL Server per comunicare con il server remoto Azure quando le seguenti condizioni sono vere tutte.

        -   L'account di servizio in cui viene eseguita l'istanza di SQL Server è un account di dominio.

        -   L'account di dominio appartiene a un dominio cui Active Directory è federato con Azure Active Directory.

        -   Il server Azure remoto è configurato per supportare l'autenticazione di Azure Active Directory.

        -   Configurare l'account di servizio in cui viene eseguita l'istanza di SQL Server come account dbmanager o sysadmin nel server Azure remoto.

5.  Per configurare un database per Database zoom, eseguire il comando ALTER DATABASE.

    1.  Per l'argomento SERVER specificare il nome di un server Azure esistente, tra cui la `.database.windows.net` parte del nome \- , ad esempio, `MyStretchDatabaseServer.database.windows.net`.

    2.  Fornire una credenziali di amministratore esistente con l'argomento CREDENZIALI o specificare FEDERATO\_servizio\_ACCOUNT = ON. Nell'esempio seguente offre una credenziale esistente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Passaggi successivi
-   [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-table.md) per abilitare le altre tabelle.

-   [Database di zoom monitor](sql-server-stretch-database-monitor.md) per controllare lo stato della migrazione dei dati.

-   [Interrompere e riprendere Database zoom](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di Database di zoom](sql-server-stretch-database-manage.md)

-   [Backup database abilitato zoom](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Vedere anche

[Identificare i database e le tabelle per Database di zoom](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE impostare le opzioni (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
