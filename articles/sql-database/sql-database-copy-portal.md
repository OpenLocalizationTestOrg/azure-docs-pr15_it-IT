<properties
    pageTitle="Copiare un database di SQL Azure tramite il portale di Azure | Microsoft Azure"
    description="Creare una copia di un database di SQL Azure"
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



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiare un Database di SQL Azure tramite il portale di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

La procedura seguente mostra come copiare un database SQL [Azure portale](https://portal.azure.com) per lo stesso server o un server diverso.

Per copiare un database SQL, è necessario quanto segue:

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **Versione di valutazione gratuita** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Un database SQL per copiare. Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Copiare il database di SQL

Aprire la pagina di database SQL per il database che si desidera copiare:

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Fare clic su **altri servizi** > **database SQL**e quindi fare clic su database desiderato.
3.  Nella pagina database SQL, fare clic su **Copia**:

    ![Database SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  Nella pagina **Copia** viene fornito un nome di database predefinito. Digitare un nome diverso se si vuole (tutti i database in un server devono avere nomi univoci).
2.  Selezionare un **server di destinazione**. Il server di destinazione è in cui viene creata la copia del database. È possibile copiare il database per lo stesso server o un server diverso. Creare un server o selezionare un server esistente dall'elenco. 
3.  Dopo aver selezionato il **server di destinazione**, il **pool di database flessibile**e **livello di prezzi** verranno attivate opzioni. Se il server dispone di un pool, è possibile copiare il database.
3.  Fare clic su **OK** per avviare il processo di copia.

    ![Database SQL](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorare l'avanzamento dell'operazione di copia

- Dopo l'avvio della copia, fare clic su notifica portale per informazioni dettagliate.

    ![notifica][3]
 
    ![Monitor][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Verificare che il database è disponibile nel server

- Fare clic su **altri servizi** > **database SQL** e verificare che il nuovo database sia **Online**.


## <a name="resolve-logins"></a>Risolvere gli account di accesso

Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica su come copiare un Database di SQL Azure, vedere [copiare un database SQL Azure](sql-database-copy.md) .
- Vedere [Copia un database SQL Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
- Vedere [Copia un database SQL Azure mediante T-SQL](sql-database-copy-transact-sql.md) per copiare un database utilizzando Transact-SQL.
- Informazioni su [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e gli account di accesso durante la copia di un database in un altro server logico.



## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestire gli account di accesso](sql-database-manage-logins.md)
- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

