<properties
   pageTitle="Cmdlet di PowerShell per archivio di dati di SQL Azure"
   description="Trovare i cmdlet di PowerShell superiore per archivio di dati di SQL Azure ad esempio come interrompere e riprendere un database."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlet di PowerShell e le API REST per SQL Data Warehouse

Molte attività di amministrazione di SQL Data Warehouse può essere gestita usando i cmdlet di PowerShell Azure o API REST.  Di seguito sono riportati alcuni esempi su come utilizzare i comandi di PowerShell per automatizzare attività comuni nel Data Warehouse SQL.  Per altri esempi, vedere l'articolo [scalabilità Gestisci con altri][].

> [AZURE.NOTE]  Per utilizzare PowerShell Azure con SQL Data Warehouse, è necessario Azure PowerShell versione 1.0.3 o versione successiva.  È possibile controllare la versione eseguendo **modulo Get - ListAvailable-nome Azure**.  La versione più recente può essere installata da [Installazione guidata piattaforma Web Microsoft][].  Per ulteriori informazioni sull'installazione della versione più recente, vedere [come installare e configurare Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Iniziare a utilizzare i cmdlet di PowerShell di Azure

1. Aprire Windows PowerShell. 
2. Al prompt dei comandi PowerShell, eseguire questi comandi per accedere a Gestione risorse Azure e selezionare l'abbonamento.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Esempio di magazzino pausa SQL dei dati

Posizionare il puntatore un database denominato "Database02" ospitato su un server denominato "Server01."  Il server appartiene a un gruppo di risorse Azure denominato "ResourceGroup1". 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Una variazione, in questo esempio Invia oggetto recuperato alla [Sospensione AzureRmSqlDatabase][].  Di conseguenza, il database è sospeso. Il comando finale Mostra i risultati.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Avviare SQL dati Warehouse esempio

Riprendere il funzionamento di un database denominato "Database02" ospitato su un server denominato "Server01." Il server è contenuto in un gruppo di risorse denominato "ResourceGroup1".

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Una variazione, in questo esempio recupera un database denominato "Database02" da un server denominato "Server01" contenuto in un gruppo di risorse denominato "ResourceGroup1". Invia l'oggetto restituito per [AzureRmSqlDatabase curriculum][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Si noti che se il server è foo.database.windows.net, utilizzare "foo" come nomeserver - i cmdlet di PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Spesso i cmdlet di PowerShell utilizzato

I cmdlet di PowerShell vengono utilizzati di frequente con Data Warehouse di SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Nuovo AzureRmSqlDatabase][]
- [Rimuovi AzureRmSqlDatabase][]
- [Ripristino AzureRmSqlDatabase][] 
- [Curriculum AzureRmSqlDatabase][]
- [Selezionare AzureRmSubscription][]
- [Set AzureRmSqlDatabase][]
- [Sospendere AzureRmSqlDatabase][]

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi di PowerShell, vedere:

- [Creare un SQL Data Warehouse tramite PowerShell][]
- [Ripristino del database][]

Per un elenco di tutte le attività che possono essere automatizzati con PowerShell, vedere [Cmdlet di Database SQL Azure][].  Per un elenco di attività che possono essere automatizzati con altri, vedere [operazioni per i database di SQL Azure][].

<!--Image references-->

<!--Article references-->
[Come installare e configurare PowerShell Azure]: ./powershell-install-configure.md
[Creare un SQL Data Warehouse tramite PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Ripristino del database]: ./sql-data-warehouse-restore-database-powershell.md
[Gestire scalabilità con altri]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlet di Database SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Operazioni per i database SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Nuovo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Rimuovi AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Ripristino AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Curriculum AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Selezionare AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Sospendere AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
