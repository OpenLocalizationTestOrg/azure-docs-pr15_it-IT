<properties
    pageTitle="Ripristinare un database SQL Azure eliminato (portal Azure) | Microsoft Azure"
    description="Ripristinare un database SQL Azure eliminato (portal Azure)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Ripristinare un database SQL Azure eliminato tramite il portale di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [**Ripristinare eliminate DB: portale**](sql-database-restore-deleted-database-portal.md)
- [Ripristinare eliminate DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Selezionare il database per ripristinare 

Per ripristinare un database eliminato nel portale di Azure:

1.  Nel [portale di Azure](https://portal.azure.com), fare clic su **altri servizi** > **SQL Server**.
3.  Selezionare il server che conteneva il database che si desidera ripristinare.
4.  Scorrere fino alla sezione **operazioni** della stessa e server e selezionare **database eliminati**: ![ripristinare un database SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Selezionare il database da ripristinare.
6.  Specificare un nome di database e fare clic su **OK**:

    ![Ripristinare un database SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
