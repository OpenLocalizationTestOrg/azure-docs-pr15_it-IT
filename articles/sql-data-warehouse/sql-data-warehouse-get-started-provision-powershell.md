<properties
   pageTitle="Creare SQL Data Warehouse tramite PowerShell | Microsoft Azure"
   description="Creare SQL Data Warehouse tramite PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Creare SQL Data Warehouse tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In questo articolo viene illustrato come creare un SQL Data Warehouse tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- **Account Azure**: visitare [Versione di valutazione gratuita di Azure][] o [MSDN Azure crediti][] per creare un account.
- **Server di SQL Azure**: per ulteriori informazioni, vedere [creare un server logico di Database SQL Azure con il portale di Azure][] o [creare un server logico di Database SQL Azure con PowerShell][] .
- **Gruppo risorse**: utilizzare lo stesso gruppo di risorse del server di SQL Azure o informazioni su [come creare un gruppo di risorse][].
- **PowerShell versione 1.0.3 o versione successiva**: È possibile controllare la versione eseguendo **modulo Get - ListAvailable-nome Azure**.  La versione più recente può essere installata da [Installazione guidata piattaforma Web Microsoft][].  Per ulteriori informazioni sull'installazione della versione più recente, vedere [come installare e configurare Azure PowerShell][].

> [AZURE.NOTE] Creazione di un Data Warehouse SQL potrebbe causare un nuovo servizio fatturabile.  Per maggiori informazioni sui prezzi, vedere [SQL Data Warehouse prezzi][] .

## <a name="create-a-sql-data-warehouse"></a>Creare un Data Warehouse SQL

1. Aprire Windows PowerShell.
2. Eseguire questo cmdlet per l'accesso a Gestione risorse di Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Selezionare l'abbonamento a cui che si desidera utilizzare per la sessione corrente.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Creare database. In questo esempio viene creato un database denominato "mynewsqldw" con livello obiettivi di servizio "DW400" nel server denominato "sqldwserver1", ovvero nel gruppo di risorse denominato "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Parametri obbligatori sono:

- **RequestedServiceObjectiveName**: la quantità di [DWU][] si richiede.  Sono supportati i valori: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
- **DatabaseName**: il nome del Data Warehouse SQL che si sta creando.
- **Nomeserver**: il nome del server che si sta utilizzando per la creazione (deve essere V12).
- **ResourceGroupName**: gruppo di risorse in uso.  Per trovare risorse disponibili gruppi nell'abbonamento a utilizzano Get-AzureResource.
- **Edizione**: deve essere "Data warehouse" per creare un Data Warehouse SQL.

Parametri facoltativi sono:

- **CollationName**: il confronto predefinita se non viene specificato è SQL_Latin1_General_CP1_CI_AS.  Confronto non può essere modificato in un database.
- **MaxSizeBytes**: la dimensione massima predefinita di un database è 10 GB.


Per ulteriori informazioni sulle opzioni del parametro, vedere [AzureRmSqlDatabase nuovo][] e [Creare Database (SQL Azure, Data Warehouse)][].

## <a name="next-steps"></a>Passaggi successivi

Dopo la Data Warehouse SQL ha completato il provisioning desiderato tentare di [caricare i dati di esempio][] o estrarre come [sviluppare][], [caricare][]o [eseguire la migrazione][].

Se è interessati a ulteriori informazioni su come gestire SQL Data Warehouse a livello di programmazione, consultare l'articolo su come utilizzare [i cmdlet di PowerShell e le API REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[eseguire la migrazione]: ./sql-data-warehouse-overview-migrate.md
[sviluppare]: ./sql-data-warehouse-overview-develop.md
[caricare]: ./sql-data-warehouse-load-with-bcp.md
[il caricamento di dati di esempio]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlet di PowerShell e le API REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Come installare e configurare PowerShell Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Creare un server di Database SQL Azure logico con il portale di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creare un server logico di Database SQL Azure con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[come creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Nuovo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Creare Database (SQL Azure Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
[SQL Data Warehouse prezzi]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versione di valutazione gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crediti Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
