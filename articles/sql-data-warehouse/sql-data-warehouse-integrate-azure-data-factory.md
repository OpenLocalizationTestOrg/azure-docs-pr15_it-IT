<properties
   pageTitle="Utilizzare Factory dati Azure con SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di Azure dati Factory (alimentatore automatico) con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utilizzare Factory dati Azure con SQL Data Warehouse

Azure Data Factory fornisce un metodo completamente gestito per coordinazione il trasferimento di dati e l'esecuzione di stored procedure in SQL Data Warehouse.  In questo modo sarà possibile più facilmente sull'impostazione e pianificazione estrarre trasformazione e caricamento (ETL) procedure complesse con SQL Data Warehouse. Per una panoramica più completa di Azure Data Factory, vedere la [documentazione di Azure Data Factory][].

## <a name="data-movement"></a>Spostamento dei dati

Azure Data Factory Abilita spostamento dei dati tra origini locali e diversi servizi di Azure.  In generale, corrente integrazione con Azure Data Factory supporta lo spostamento dei dati da e verso posizioni seguenti:

+ Archiviazione blob Azure
+ Database SQL Azure
+ SQL Server locale
+ SQL Server in IaaS

Per informazioni su come configurare un dati attività Copia vedere [copiare i dati con Azure Data Factory][]

## <a name="stored-procedures"></a>Stored procedure
 Nello stesso modo che può essere utilizzato per pianificare i trasferimenti di dati, Factory di dati di Azure può essere utilizzata anche per gestire l'esecuzione di stored procedure.  In questo modo pipeline più complesse creare e si estende possibilità di Azure dati Factory di sfruttare la potenza di SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][].
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Copiare i dati con Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Panoramica di sviluppo SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Cenni preliminari sull'integrazione di SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentazione di Azure Data Factory]:https://azure.microsoft.com/documentation/services/data-factory/

