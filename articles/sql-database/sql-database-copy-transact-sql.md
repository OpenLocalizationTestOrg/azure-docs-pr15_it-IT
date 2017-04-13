<properties 
    pageTitle="Copiare un database di SQL Azure con Transact-SQL | Microsoft Azure" 
    description="Creare una copia di un database di SQL Azure con Transact-SQL" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiare un database di SQL Azure con Transact-SQL


> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


Questa procedura seguente viene illustrato come copiare un database SQL con Transact-SQL server stesso o un altro server. Copia del database utilizza l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Per completare la procedura descritta in questo articolo è necessario quanto segue:

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **Versione di valutazione gratuita** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Un Database SQL Azure. Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx). Se non si dispone di SQL Server Management Studio o caratteristiche descritte in questo articolo non sono disponibili, [scaricare la versione più recente](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copiare il database di SQL

Accedere al database master utilizzando l'account di accesso a livello di server principale o l'account di accesso che ha creato il database che si desidera copiare. Account di accesso non capitale a livello di server devono essere membri del ruolo dbmanager per copiare i database. Per ulteriori informazioni sull'account di accesso e la connessione al server, vedere [gestire gli account di accesso](sql-database-manage-logins.md).

Iniziare la copia del database di origine con l'istruzione [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Eseguire l'istruzione avvia il processo di copia del database. Poiché la copia di un database è un processo asincrono, viene restituito l'istruzione CREATE DATABASE prima che il database viene completata la copia.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Accedere al database master utilizzando l'account di accesso a livello di server principale o l'account di accesso che ha creato il database che si desidera copiare. Account di accesso non capitale a livello di server devono essere membri del ruolo dbmanager per copiare i database.

Copie questo comando Database1 a un nuovo database denominato Database2 nello stesso server. A seconda delle dimensioni del database l'operazione di copia può richiedere del tempo per completare.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Accedere al database master del server di destinazione, il server di Database SQL Azure in cui è necessario creare il nuovo database. Usare un account di accesso con lo stesso nome e la password come il proprietario del database (DBO) del database di origine nel server di Database SQL Azure di origine. L'accesso al server di destinazione dovrà inoltre essere membro del ruolo dbmanager oppure essere l'account di accesso a livello di server principale.

Questo comando copia Database1 su server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database l'operazione di copia può richiedere del tempo per completare.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorare l'avanzamento dell'operazione di copia

Monitorare il processo di copia eseguendo le visualizzazioni sys e sys.dm_database_copies. Durante la copia è in corso, la colonna STATE_DESC della visualizzazione Sys. Databases per il nuovo database è impostata su copia.


- Se la copia non riesce, la colonna STATE_DESC della visualizzazione Sys. Databases per il nuovo database è impostata su sospetto. In questo caso, eseguire l'istruzione DROP nel nuovo database e riprovare.
- Se la copia ha esito positivo, la colonna STATE_DESC della visualizzazione Sys. Databases per il nuovo database sia impostata su ONLINE. In questo caso, la copia è stata completata e il nuovo database è un normale database possono essere modificati indipendente del database di origine.

> [AZURE.NOTE] -Se si decide di annullare la copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) nel nuovo database. In alternativa, eseguendo l'istruzione DROP DATABASE sul database di origine anche consente di annullare il processo di copia.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Risolvere gli account di accesso al termine dell'operazione di copia

Dopo avere inserito il nuovo database online nel server di destinazione, utilizzare l'istruzione [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) per riassociare gli utenti dal nuovo database agli account di accesso nel server di destinazione. Per risolvere utenti isolati, vedere [Risolvere i problemi di utenti isolati](https://msdn.microsoft.com/library/ms175475.aspx). Vedere anche [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

Tutti gli utenti nel nuovo database gestire le autorizzazioni che avevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del database del nuovo database e viene assegnato un nuovo ID di sicurezza (SID). Dopo avere stabilito la copia e prima vengono rimappati ad altri utenti, account di accesso che ha avviato la copia, il proprietario del database (DBO), possono accedere al nuovo database.


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica su come copiare un Database di SQL Azure, vedere [copiare un database SQL Azure](sql-database-copy.md) .
- Vedere [copiare un database di SQL Azure tramite il portale di Azure](sql-database-copy-portal.md) per copiare un database tramite il portale di Azure.
- Vedere [Copia un database SQL Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
- Informazioni su [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e gli account di accesso durante la copia di un database in un altro server logico.



## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestire gli account di accesso](sql-database-manage-logins.md)
- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


