<properties
   pageTitle="Ripristinare un SQL Azure Data Warehouse (API REST) | Microsoft Azure"
   description="Attività di API REST per ripristinare un Data Warehouse SQL Azure."
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

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Ripristinare un SQL Azure Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [RESTO][]

In questo articolo si imparerà a ripristinare un Data Warehouse SQL Azure tramite l'API REST.

## <a name="before-you-begin"></a>Prima di iniziare

**Verificare la capacità DTU.** Ogni SQL Data Warehouse è ospitato da SQL server (ad esempio myserver.database.windows.net) che ha una quota DTU predefinita.  Per poter ripristinare un Data Warehouse SQL, verificare che l'istanza di SQL server è abbastanza DTU rimanente per il database da ripristinare. Per informazioni su come calcolare DTU necessari o richiedere ulteriori DTU, vedere [richiedere una modifica di quota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o in pausa

Per ripristinare un database:

1. È possibile ottenere l'elenco dei punti di ripristino del database mediante l'operazione di ottenere i punti di ripristino del Database.
2. Iniziare il ripristino mediante l'operazione [Crea database ripristinare richiesta][] .
3. Tenere traccia dello stato di ripristino del mediante l'operazione di [stato di operazione di Database][] .

>[AZURE.NOTE] Al termine dell'operazione di ripristino, è possibile configurare il database recuperato dal seguenti [configurare il database dopo il ripristino][].

## <a name="restore-a-deleted-database"></a>Ripristinare un database eliminato

Per ripristinare un database eliminato:

1.  Elencare tutti i database eliminati ripristinabili mediante l'operazione di [database ignorati ripristinabili elenco][] .
2.  Visualizzare i dettagli per il database eliminato da ripristinare mediante l'operazione [ottenere ripristinabile database rilasciati][] .
3.  Iniziare il ripristino mediante l'operazione [Crea database ripristinare richiesta][] .
4.  Tenere traccia dello stato di ripristino del mediante l'operazione di [stato di operazione di Database][] .

>[AZURE.NOTE] Per configurare il database dopo il ripristino è stato completato, vedere [configurare il database dopo il ripristino][]. 


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle funzionalità di continuità aziendale delle edizioni di Database SQL Azure, leggere la [Panoramica di continuità aziendale di Database SQL Azure][].

<!--Image references-->

<!--Article references-->
[Panoramica di continuità aziendale Database SQL Azure]: ./sql-database-business-continuity.md
[Richiedere una modifica di quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurare il database dopo il ripristino]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Creare una richiesta di ripristino del database]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Stato operazione di database]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Ottenere database rilasciati ripristinabile]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Elenco ripristinabile eliminati database]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
