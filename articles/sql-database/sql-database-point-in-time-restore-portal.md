<properties
    pageTitle="Ripristinare un database SQL Azure in un punto precedente nel tempo (portal Azure) | Microsoft Azure"
    description="Ripristinare un database SQL Azure in un punto precedente nel tempo."
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


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Ripristinare un database SQL Azure in un punto precedente nel tempo con il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [Ripristinare In un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

In questo articolo viene illustrato come ripristinare il database in un momento precedente nel tempo da [che database SQL di automatizzato di backup](sql-database-automated-backups.md) tramite il portale di Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Ripristinare un database SQL in un punto precedente nel tempo

Selezionare un database per ripristinare nel portale di Azure:

1.  Aprire il [portale di Azure](https://portal.azure.com).
2.  Sul lato sinistro dello schermo, selezionare **altri servizi** > **database SQL**.
3.  Fare clic su database che si desidera ripristinare.
4.  Nella parte superiore della pagina del database, selezionare **Ripristina**:

    ![Ripristinare un database SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Nella pagina **ripristinare** , selezionare la data e ora (in formato UTC) per ripristinare il database a e quindi fare clic su **OK**:

    ![Ripristinare un database SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Monitorare l'operazione di ripristino

1. Dopo aver fatto clic su **OK** nel passaggio precedente, fare clic sull'icona di notifica nell'angolo superiore destro della pagina e fare clic sulla notifica di **database SQL di ripristino** per informazioni dettagliate.

    ![Ripristinare un database SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. Con le informazioni relative allo stato dell'operazione di ripristino verrà visualizzata la pagina di database SQL di ripristino. È possibile fare clic sulla voce per altri dettagli:

    ![Ripristinare un database SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
