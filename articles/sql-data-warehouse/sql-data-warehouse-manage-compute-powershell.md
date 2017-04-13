<properties
   pageTitle="Gestire le capacità di calcolo in SQL Azure Data Warehouse (PowerShell) | Microsoft Azure"
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
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gestire le capacità di calcolo in SQL Azure Data Warehouse (PowerShell)

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


## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-the-latest-version-of-azure-powershell"></a>Installare la versione più recente di PowerShell di Azure

> [AZURE.NOTE]  Per usare PowerShell Azure con SQL Data Warehouse, è necessario Azure PowerShell versione 1.0.3 o versione successiva.  Per verificare la versione corrente, eseguita il comando **modulo Get - ListAvailable-nome Azure**. È possibile installare la versione più recente di [Installazione guidata piattaforma Web Microsoft][].  Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Iniziare a utilizzare i cmdlet di PowerShell di Azure

Per iniziare:

1. Aprire PowerShell Azure. 
2. Al prompt dei comandi PowerShell, eseguire questi comandi per accedere a Gestione risorse Azure e selezionare l'abbonamento.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Scala grande potenza

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare il DWUs, utilizzare il cmdlet di PowerShell [Set-AzureRmSqlDatabase][] . Nell'esempio seguente imposta obiettivo del livello di DW1000 per il database MySQLDW ospitato nel server MyServer. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcolo pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per mettere in pausa un database, utilizzare il cmdlet [Sospensione AzureRmSqlDatabase][] . Nell'esempio seguente sospende un database denominato Database02 ospitato su un server denominato Server01. Il server è in un gruppo di risorse Azure denominato ResourceGroup1. 

> [AZURE.NOTE] Si noti che se il server è foo.database.windows.net, utilizzare "foo" come nomeserver - i cmdlet di PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Una variazione, in questo esempio successivo recupera il database nell'oggetto $database. Quindi invia l'oggetto [Sospensione AzureRmSqlDatabase][]. I risultati vengono memorizzati in resultDatabase oggetto. Il comando finale Mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcolo curriculum

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per avviare un database, utilizzare il cmdlet [Resume AzureRmSqlDatabase][] . Nell'esempio seguente viene avviato un database denominato Database02 ospitato su un server denominato Server01. Il server è in un gruppo di risorse Azure denominato ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Una variazione, in questo esempio successivo recupera il database nell'oggetto $database. Quindi, tubi l'oggetto [AzureRmSqlDatabase curriculum][] e memorizzare i risultati in $resultDatabase. Il comando finale Mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Come installare e configurare PowerShell Azure]: ./powershell-install-configure.md
[Gestire le informazioni generali di calcolo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Curriculum AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Sospendere AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
