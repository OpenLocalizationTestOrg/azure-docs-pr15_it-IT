<properties
   pageTitle="Gestire le capacità di calcolo in Azure SQL dati Warehouse (REST) | Microsoft Azure"
   description="Attività di PowerShell per gestire calcolare power. Risorse di elaborazione scala regolando DWUs. In alternativa, sospendere e riprendere calcolare le risorse per risparmiare sui costi."
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

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Gestire le capacità di calcolo in Azure SQL dati Warehouse (REST)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Scala da scalabilità per calcolare le risorse e la memoria per soddisfare le esigenze del carico di lavoro. Risparmiare sui costi risorse posteriore proporzioni durante l'elaborazione sospensione o non periodi completamente. 

Insieme di attività utilizza il portale Azure per:

- Elaborazione di scala
- Calcolo pausa
- Calcolo curriculum

Per ulteriori informazioni, vedere [gestire calcolare Panoramica][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Scala grande potenza

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare il DWUs, utilizzare l'API REST [creazione o aggiornamento Database][] . Nell'esempio seguente imposta obiettivo del livello di DW1000 per il database MySQLDW ospitato nel server MyServer. Il server è in un gruppo di risorse Azure denominato ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcolo pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database, utilizzare l'API REST [Sospendere Database][] . Nell'esempio seguente sospende un database denominato Database02 ospitato su un server denominato Server01. Il server è in un gruppo di risorse Azure denominato ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcolo curriculum

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per avviare un database, utilizzare l'API REST [Database curriculum][] . Nell'esempio seguente viene avviato un database denominato Database02 ospitato su un server denominato Server01. Il server è in un gruppo di risorse Azure denominato ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Gestire le informazioni generali di calcolo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pausa Database]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Database di curriculum]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Creazione o aggiornamento di Database]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
