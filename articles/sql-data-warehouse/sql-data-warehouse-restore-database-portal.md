<properties
   pageTitle="Ripristinare un SQL Azure Data Warehouse (portale) | Microsoft Azure"
   description="Attività portale Azure per ripristinare un Data Warehouse SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Ripristinare un SQL Azure Data Warehouse (Portal)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [RESTO][]

In questo articolo si imparerà a ripristinare un Data Warehouse SQL Azure tramite il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

**Verificare la capacità DTU.** Ogni SQL Data Warehouse è ospitato da SQL server (ad esempio myserver.database.windows.net) che ha una quota DTU predefinita.  Per poter ripristinare un Data Warehouse SQL, verificare che l'istanza di SQL server è abbastanza DTU rimanente per il database da ripristinare. Per informazioni su come calcolare DTU necessari o richiedere ulteriori DTU, vedere [richiedere una modifica di quota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o in pausa

Per ripristinare un database:

1. Accedere al [portale di Azure][]
2. Sul lato sinistro dello schermo selezionare **Sfoglia** e quindi selezionare **SQL Server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Passare al server e selezionarlo
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Trovare SQL Data Warehouse che si desidera ripristinare da e selezionarlo
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Nella parte superiore e il Data Warehouse, fare clic su **Ripristina**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Specificare un nuovo **nome del Database**
7. Selezionare il **Punto di ripristino** più recente
    1. Verificare che si sceglie il punto di ripristino più recente.  Poiché i punti di ripristino vengono visualizzati in formato UTC, in alcuni casi l'opzione predefinita visualizzata non è il punto di ripristino più recente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Fare clic su **OK**
9. Il processo di ripristino del database, verrà avviata e può essere controllato tramite **le notifiche**

>[AZURE.NOTE] Al termine dell'operazione di ripristino, è possibile configurare il database recuperato dal seguenti [configurare il database dopo il ripristino][].


## <a name="restore-a-deleted-database"></a>Ripristinare un database eliminato

Per ripristinare un database eliminato:

1. Accedere al [portale di Azure][]
2. Sul lato sinistro dello schermo selezionare **Sfoglia** e quindi selezionare **SQL Server**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Passare al server e selezionarlo
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Scorrere fino alla sezione operazioni su blade del server
5. Fare clic sul riquadro **Database eliminati**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Selezionare il database eliminato da ripristinare
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Specificare un nuovo **nome del Database**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Fare clic su **OK**
9. Il processo di ripristino del database, verrà avviata e può essere controllato tramite **le notifiche**

>[AZURE.NOTE] Per configurare il database dopo il ripristino è stato completato, vedere [configurare il database dopo il ripristino][]. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle funzionalità di continuità aziendale delle edizioni di Database SQL Azure, leggere la [Panoramica di continuità aziendale di Database SQL Azure][].

<!--Image references-->

<!--Article references-->
[Panoramica di continuità aziendale Database SQL Azure]: ./sql-database-business-continuity.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurare il database dopo il ripristino]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Richiedere una modifica di quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portale di Azure]: https://portal.azure.com/
