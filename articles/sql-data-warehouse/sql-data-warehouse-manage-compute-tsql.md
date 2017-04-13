<properties
   pageTitle="Gestire le capacità di calcolo in Azure SQL dati Warehouse (REST) | Microsoft Azure"
   description="Attività di Transact-SQL (Transact-SQL) per le prestazioni di scalabilità regolando DWUs. Salvare i costi ridimensionando durante non periodi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gestire le capacità di calcolo in SQL Azure Data Warehouse (Transact-SQL)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Scala da scalabilità per calcolare le risorse e la memoria per soddisfare le esigenze del carico di lavoro. Risparmiare sui costi risorse posteriore proporzioni durante l'elaborazione sospensione o non periodi completamente. 

Questo insieme di attività utilizzati T-SQL per:

- Impostazioni di visualizzazione corrente DWU
- Risorse di elaborazione cambia regolando DWUs

Per sospendere o riprendere un database, scegliere una delle altre opzioni di piattaforma nella parte superiore di questo articolo.

Per ulteriori informazioni, vedere [gestire calcolare power Panoramica][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Impostazioni di visualizzazione corrente DWU

Per visualizzare le impostazioni correnti DWU per i database:

1. Aprire Esplora oggetti SQL Server in Visual Studio 2015.
2. Connetti a database master associato al server di Database SQL logico.
2. Selezionare la visualizzazione di gestione dinamica sys.database_service_objectives. Ecco un esempio: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Elaborazione di scala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare il DWUs:


1. Connetti a database master associato con il server di Database SQL logico.
2. Istruzione TSQL [ALTER DATABASE][] . Nell'esempio seguente imposta obiettivo del livello di DW1000 per il database MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Gestire calcolo power Panoramica]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[LA MODIFICA DEL DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
