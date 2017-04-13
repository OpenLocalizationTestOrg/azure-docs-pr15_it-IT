<properties
   pageTitle="Ripristino di SQL Data Warehouse | Microsoft Azure"
   description="Panoramica delle opzioni di ripristino del database per ripristinare un database SQL di Azure Data warehouse."
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
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Ripristino di SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [RESTO][]

SQL Data Warehouse offre Ripristina locali e geografica come parte di emergenza relativa warehouse dati funzionalità di ripristino. Utilizzare i dati warehouse backup per ripristinare il data warehouse in un punto di ripristino nell'area principale oppure utilizzare backup ridondanti geografico per ripristinare un'area geografica diversa. Questo articolo illustra le specifiche di ripristino di un data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>Che cos'è un ripristino warehouse dati?

Il ripristino di warehouse dati è un nuovo data warehouse creato da un backup di un oggetto esistente o warehouse dati eliminati. Ripristinato data warehouse ricrea warehouse dati di backup in un momento specifico. Poiché SQL Data Warehouse è un sistema distribuito, un ripristino warehouse dati viene creato da molti file di backup archiviati nei blob Azure. 

Ripristino del database è essenziale di qualsiasi strategia di ripristino emergenza e continuità aziendale perché crea nuovamente i dati dopo danni accidentali o l'eliminazione.

Per ulteriori informazioni, vedere:

-  [Backup SQL Data Warehouse](sql-data-warehouse-backups.md)
-  [Panoramica di continuità aziendale](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Punti di ripristino warehouse dati

Come dei vantaggi dell'utilizzo dello spazio di archiviazione di Azure Premium, SQL Data Warehouse utilizza istantanee Azure lo spazio di archiviazione Blob per eseguire il backup primaria data warehouse. Ogni snapshot ha un punto di ripristino che rappresenta l'ora di inizio dello snapshot. Per ripristinare un data warehouse, scegliere un punto di ripristino ed eseguire il comando Ripristina.  

SQL Data Warehouse Ripristina sempre il backup di un nuovo data warehouse. È possibile mantenere ripristinato data warehouse e quella corrente o eliminare uno di essi. Se si desidera sostituire corrente data warehouse con ripristinato data warehouse, è possibile rinominarla.

Se è necessario ripristinare una warehouse dati eliminati o in pausa, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Ripristino geografico ridondanti

Se si usa lo spazio di archiviazione geografico ridondanti, è possibile ripristinare data warehouse nel [centro dati accoppiate](../best-practices-availability-paired-regions.md) in un'area geografica diversa. Data warehouse verrà ripristinati dall'ultimo giorno backup. 

## <a name="restore-timeline"></a>Ripristinare un indicatore cronologico

È possibile ripristinare un database in qualsiasi punto di ripristino disponibili negli ultimi sette giorni. Snapshot avviare ogni quattro a 8 ore e sono disponibili per sette giorni. Quando uno snapshot è più di sette giorni prima della scadenza e il punto di ripristino non è più disponibile.

## <a name="restore-costs"></a>Ripristinare i costi

Le spese di spazio di archiviazione per l'archivio dati ripristinato sono fatturata alla tariffa lo spazio di archiviazione di Azure Premium. 

Se si sospende un ripristinato data warehouse, addebitate per lo spazio di archiviazione alla tariffa lo spazio di archiviazione di Azure Premium. Il vantaggio di sospensione non è che addebitate per le risorse di elaborazione DWU.

Per ulteriori informazioni sui prezzi SQL Data Warehouse, vedere [SQL dati Warehouse prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Per ripristinare l'uso

Lo scopo principale per il ripristino warehouse dati consiste nel recuperare i dati dopo la perdita di dati accidentali.

Utilizzare dati warehouse ripristino per conservare una copia di backup per più di sette giorni. Dopo il ripristino del backup, in base a cui si dispone del data warehouse online e possibile mettere in pausa tempo indefinito per salvare i costi di elaborazione. Il database in pausa comporta costi dello spazio di archiviazione alla tariffa lo spazio di archiviazione di Azure Premium. 

## <a name="related-topics"></a>Argomenti correlati

### <a name="scenarios"></a>Scenari

- Per una panoramica di continuità aziendale, vedere [Panoramica di continuità aziendale](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Per eseguire un ripristino warehouse di dati, eseguire il ripristino utilizzando:

- Portale di Azure, vedere [ripristinare un data warehouse tramite il portale di Azure](sql-data-warehouse-restore-database-portal.md)
- Cmdlet di PowerShell, vedere [ripristinare un data warehouse uso dei cmdlet di PowerShell](sql-data-warehouse-restore-database-powershell.md)
- POSIZIONARE API, vedere [ripristinare un data warehouse utilizzando le API REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
