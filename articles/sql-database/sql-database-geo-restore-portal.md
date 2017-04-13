<properties
    pageTitle="Ripristinare un database SQL Azure da un backup automatico (portal Azure) | Microsoft Azure"
    description="Ripristinare un database SQL Azure da un backup automatico (portal Azure)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Ripristinare un database SQL Azure da un backup automatico tramite il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md#geo-restore)
- [Ripristino geografico: PowerShell](sql-database-geo-restore-powershell.md)

In questo articolo viene illustrato come ripristinare il database da un [backup automatico](sql-database-automated-backups.md) in un nuovo server con [Geografico Ripristina](sql-database-recovery-using-backups/.md#geo-restore) tramite il portale di Azure.

## <a name="select-a-database-to-restore"></a>Selezionare il database da ripristinare

Per ripristinare un database nel portale di Azure, eseguire la procedura seguente:

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro dello schermo selezionare **+ nuova** > **database** > **Database SQL**:

    ![Ripristinare un database SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Selezionare **Backup** come origine e quindi selezionare il backup da ripristinare. Specificare un nome di database, un server che si desidera ripristinare il database, e quindi fare clic su **Crea**:
  
    ![Ripristinare un database SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Controllare lo stato dell'operazione di ripristino facendo clic sull'icona di notifica nella parte superiore destra della pagina. 


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
