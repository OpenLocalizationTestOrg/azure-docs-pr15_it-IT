<properties
   pageTitle="Creare un Data Warehouse SQL nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un Data Warehouse SQL Azure nel portale di Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Creare un Data Warehouse SQL Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In questa esercitazione utilizza il portale di Azure per creare un Data Warehouse SQL che contiene un database di esempio AdventureWorksDW.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- **Account Azure**: visitare [Versione di valutazione gratuita di Azure][] o [MSDN Azure crediti][] per creare un account.
- **Server di SQL Azure**: per ulteriori informazioni, vedere [creazione di un server logico di Database SQL Azure con il portale di Azure][] .

> [AZURE.NOTE] Creazione di un Data Warehouse SQL potrebbe causare un nuovo servizio fatturabile.  Per ulteriori informazioni, vedere [SQL Data Warehouse prezzi][] .

## <a name="create-a-sql-data-warehouse"></a>Creare un Data Warehouse SQL

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **+ nuova** > **dati + spazio di archiviazione** > **SQL Data Warehouse**.

    ![Creare](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. In e il **SQL Data Warehouse** compilare le informazioni necessarie e quindi preme 'Create' per creare.

    ![Creare database](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Server**: è consigliabile selezionare il server prima di tutto.  

    - **Nome del database**: il nome che viene utilizzato come riferimento SQL Data Warehouse.  Devono essere univoco nel server.
    
    - **Prestazioni**: È consigliabile a partire da 400 [DWUs][DWU]. È possibile spostare il dispositivo di scorrimento a sinistra o a destra per regolare le prestazioni dei data warehouse o modificare la scala verso l'alto o verso il basso dopo la creazione.  Per ulteriori informazioni su come DWUs, vedere la documentazione in [proporzioni dei caratteri](./sql-data-warehouse-manage-compute-overview.md) o i [prezzi pagina][SQL Data Warehouse prezzi]. 

    - **Abbonamento**: selezionare l' [abbonamento] da questa Data Warehouse SQL verrà distinta per.

    - **Gruppo risorse**: [gruppi di risorse] [ Resource group] sono contenitori che consentono di gestire una raccolta di risorse Azure. Ulteriori informazioni sui [gruppi di risorse](../azure-resource-manager/resource-group-overview.md).

    - **Selezionare origine**: fare clic su **Seleziona origine** > **campione**. Azure popola automaticamente l'opzione **Seleziona esempio** con AdventureWorksDW.

> [AZURE.NOTE] Le regole di confronto predefiniti per una Data Warehouse SQL è SQL_Latin1_General_CP1_CI_AS. Se è necessario un confronto diverso, [T-SQL][] utilizzabili per creare il database con un confronto diverso.

4. Fare clic su **Crea** per creare il proprio Data Warehouse SQL.

5. Attendere alcuni minuti. Quando il proprio data warehouse è pronta, deve essere restituito al [portale di Azure](https://portal.azure.com). È possibile trovare la Data Warehouse SQL nel dashboard, elencati sotto il database di SQL o nel gruppo di risorse utilizzato per crearlo. 

    ![visualizzazione del portale](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Passaggi successivi

Una volta creato un Data Warehouse SQL, si è pronti per [connettersi](./sql-data-warehouse-connect-overview.md) ed eseguire query su.

Per caricare dati in SQL Data Warehouse, vedere la [Panoramica di caricamento](./sql-data-warehouse-overview-load.md).

Se si sta tentando di eseguire la migrazione di un database esistente nell'archivio di dati SQL, vedere [Panoramica di migrazione](./sql-data-warehouse-overview-migrate.md) o l'utilità di [Migrazione](./sql-data-warehouse-migrate-migration-utility.md).

Le regole firewall possono essere configurate con Transact-SQL. Per ulteriori informazioni, vedere [sp_set_firewall_rule][] e [sp_set_database_firewall_rule][].

È anche utile esaminare le [procedure consigliate][].

<!--Article references-->
[Creare un server di Database SQL Azure logico con il portale di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Procedure consigliate]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[abbonamento]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse prezzi]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versione di valutazione gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crediti Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

