<properties
    pageTitle="Guida introduttiva a query tra database (partizione verticale) | Microsoft Azure"   
    description="come usare una query di database flessibile con verticalmente suddiviso database"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Guida introduttiva a query tra database (partizione verticale) (versione di anteprima)

Una query di database flessibile (preview) per il Database di SQL Azure consente di eseguire query T-SQL che si estendono su più database che utilizzano un singolo punto di connessione. In questo argomento si applica a [verticalmente suddiviso database](sql-database-elastic-query-vertical-partitioning.md).  

Al termine, sarà necessario: informazioni su come configurare e utilizzare un Database di SQL Azure per eseguire query che si estendono su più database correlati. 

Per ulteriori informazioni sulla funzionalità di query di database flessibile, vedere [Panoramica di query di Database SQL Azure database flessibile](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Creare i database di esempio

Per iniziare, è necessario creare due database, **clienti** e **ordini**, in server logici stesse o in altre.   

Eseguire la query seguente nel database di **ordini** per creare la tabella **OrderInformation** e i dati di esempio di input. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

A questo punto, eseguire le seguenti query sul database **clienti** per creare la tabella **CustomerInformation** e i dati di esempio di input. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Creare gli oggetti di database
### <a name="database-scoped-master-key-and-credentials"></a>Database nell'ambito chiave master e le credenziali

1. Aprire SQL Server Management Studio o SQL Server Data Tools in Visual Studio.
2. Connettersi al database ordini ed eseguire i comandi T-SQL seguenti:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    "Username" e "password" deve essere il nome utente e la password utilizzati per accedere al database clienti.
    Autenticazione tramite Azure Active Directory con le query flessibile non è attualmente supportato.

### <a name="external-data-sources"></a>Origini dati esterne
Per creare un'origine dati esterna, eseguire il comando seguente sul database ordini: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabelle esterne
Creare una tabella esterna nel database di ordini, che corrisponde la definizione della tabella CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Eseguire una query SQL T flessibile database di esempio

Dopo aver definito l'origine dati esterna e le tabelle esterne è ora possibile usare Transact-SQL per le tabelle esterne della query. Eseguire la query sul database ordini: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Costo

Attualmente la funzionalità di query di database flessibile è incluso nel costo del Database SQL Azure.  

Per informazioni sui prezzi vedere [Prezzi Database SQL](/pricing/details/sql-database). 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
