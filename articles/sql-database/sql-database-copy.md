<properties
    pageTitle="Copiare un database SQL Azure | Microsoft Azure"
    description="Creare una copia di un database di SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copiare un Database SQL Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

È possibile utilizzare Azure [automatizzato di Database SQL di backup](sql-database-automated-backups.md) per creare una copia del database SQL. La copia del database utilizza la stessa tecnologia come la funzionalità di replica geografico. Ma a differenza della replica geografico termina il collegamento di replica come al termine della fase di seeding. Di conseguenza, il database di copia è uno snapshot del database di origine al momento della richiesta di copia.  
È possibile creare la copia del database nel server stesso o un server diverso. Livello di servizio livello e le prestazioni (livello prezzo) della copia del database sono gli stessi del database di origine per impostazione predefinita. Quando si utilizza l'API, è possibile selezionare un livello di prestazioni diversi all'interno di stesso livello di servizio (edition). Una volta completata la copia, la copia diventa un database completamente funziona, indipendente. A questo punto è possibile eseguire l'aggiornamento o downgrade a qualsiasi edizione. Account di accesso, utenti e autorizzazioni possono essere gestite in modo indipendente.  

Quando si copia un database nello stesso server logici, gli account di accesso stesso può essere usata su entrambi i database. Consente di copiare il database di identità di protezione diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti di database, le relative autorizzazioni e il sicurezza gli identificatori di vengono copiati per la copia del database.  

Quando si copia un database in un altro server logico, la sicurezza dell'entità nel nuovo server diventa il proprietario del database nel nuovo database. Se si usa [contenuto gli utenti del database](sql-database-manage-logins.md) per l'accesso ai dati assicura database primario e secondario sempre le stesse credenziali utente per consentire al termine copia è possibile accedervi immediatamente con le stesse credenziali. Se si usa [Azure Active Directory](../active-directory/active-directory-whatis.md), è possibile eliminare completamente la necessità di gestione delle credenziali nella copia. Tuttavia, quando si copia il database in un nuovo server, in base a accesso in genere non funziona perché gli account di accesso non esiste nel nuovo server. Informazioni su [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di account di accesso durante la copia di un database in un altro server logico. 

Per copiare un database SQL, è necessario quanto segue:

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **Versione di valutazione gratuita** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Un database SQL per copiare. Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Passaggi successivi

- Vedere [copiare un database di SQL Azure tramite il portale di Azure](sql-database-copy-portal.md) per copiare un database tramite il portale di Azure.
- Vedere [Copia un database SQL Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
- Vedere [Copia un database SQL Azure mediante T-SQL](sql-database-copy-transact-sql.md) per copiare un database utilizzando Transact-SQL.
- Informazioni su [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e gli account di accesso durante la copia di un database in un altro server logico.



## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestire gli account di accesso](sql-database-manage-logins.md)
- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
