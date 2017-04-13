<properties
   pageTitle="Creare un Data Warehouse SQL con TSQL | Microsoft Azure"
   description="Informazioni su come creare un Data Warehouse SQL Azure con TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Creare un database SQL Data Warehouse utilizzando Transact-SQL (t)

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In questo articolo viene illustrato come creare un SQL Data Warehouse mediante T-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario: 

- **Account Azure**: visitare [Versione di valutazione gratuita di Azure][] o [MSDN Azure crediti][] per creare un account.
- **Server di SQL Azure**: per ulteriori informazioni, vedere [creare un server logico di Database SQL Azure con il portale di Azure][] o [creare un server logico di Database SQL Azure con PowerShell][] .
- **Gruppo risorse**: utilizzare lo stesso gruppo di risorse del server di SQL Azure o informazioni su [come creare un gruppo di risorse][].
- **Ambiente per eseguire Transact-SQL**: È possibile utilizzare [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]o [SQL Server Management Studio][] per eseguire Transact-SQL.

> [AZURE.NOTE] Creazione di un Data Warehouse SQL potrebbe causare un nuovo servizio fatturabile.  Per maggiori informazioni sui prezzi, vedere [SQL Data Warehouse prezzi][] .

## <a name="create-a-database-with-visual-studio"></a>Creare un database con Visual Studio

Se ha familiarità con Visual Studio, vedere l'articolo [Query SQL Azure Data Warehouse (Visual Studio)][].  Per iniziare, aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che ospita i database di SQL Data Warehouse.  Una volta connessa, è possibile creare un Data Warehouse SQL eseguendo il seguente comando SQL database **master** .  Questo comando consente di creare il database MySqlDwDb con un obiettivo di DW400 servizio e consentire al database di aumento di dimensioni massime di 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Creare un database con sqlcmd

In alternativa, è possibile eseguire il comando stesso con sqlcmd eseguendo le operazioni seguenti al prompt dei comandi.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Il confronto predefinito quando non viene specificato è FASCICOLARE SQL_Latin1_General_CP1_CI_AS.  Il `MAXSIZE` può essere compreso tra 250 GB e 240 TB.  Il `SERVICE_OBJECTIVE` può essere compreso tra DW100 e DW2000 [DWU][].  Per un elenco di tutti i valori validi, vedere la documentazione di MSDN per [Creare il DATABASE][].  MAXSIZE sia SERVICE_OBJECTIVE può essere modificato con un comando T-SQL [ALTER DATABASE][] .  Il confronto di un database non è possibile modificato dopo la creazione.   Attenzione deve essere utilizzato quando la modifica di SERVICE_OBJECTIVE come modifica DWU causa il riavvio di servizi, che consente di annullare tutte le query di volo.  Modifica MAXSIZE non riavviare servizi è un'operazione di metadati semplice.

## <a name="next-steps"></a>Passaggi successivi

Dopo la Data Warehouse SQL provisioning è possibile [caricare dati di esempio][] o estrarre come [sviluppare][], [caricare][]o [eseguire la migrazione][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query SQL Azure Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[eseguire la migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppare]: sql-data-warehouse-overview-develop.md
[caricare]: sql-data-warehouse-overview-load.md
[caricare dati di esempio]: sql-data-warehouse-load-sample-databases.md
[Creare un server di Database SQL Azure logico con il portale di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creare un server logico di Database SQL Azure con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[come creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[SQLCMD]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREARE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[LA MODIFICA DEL DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SQL SERVER MANAGEMENT STUDIO]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse prezzi]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versione di valutazione gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crediti Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
