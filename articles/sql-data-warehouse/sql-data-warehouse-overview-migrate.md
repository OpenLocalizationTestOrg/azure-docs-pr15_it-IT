<properties
   pageTitle="Eseguire la migrazione della soluzione a SQL Data Warehouse | Microsoft Azure"
   description="Guida alla migrazione per trasportando la soluzione piattaforma Warehouse di dati di SQL Azure."
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
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Eseguire la migrazione della soluzione nell'archivio di dati SQL

SQL Data Warehouse è un sistema di database distribuito illimitate scale secondo le proprie esigenze. Per mantenere le proporzioni e le prestazioni, non tutte le caratteristiche di SQL Server sono implementate all'interno di SQL Data Warehouse. I seguenti argomenti di migrazione tocco in alcuni dei fattori principali per la migrazione della soluzione nell'archivio di dati SQL. Progettazione di data warehouse per scala introduce vari modelli e gli approcci tradizionali in modo non sono sempre la migliore. Pertanto è possibile adattare la soluzione esistente assicura che si sfruttare la piattaforma distribuita fornita da SQL Data Warehouse.

È anche importante tenere presente che SQL Data Warehouse è una piattaforma di base in Microsoft Azure. Pertanto parte del processo di migrazione può anche includere trasferire i dati nel cloud. Trasferimento di dati è un oggetto vero e proprio e con cautela; in particolare, volumi aumentano. Trasferimento di dati e caricamento dei dati sono distinti.

## <a name="migration-guidance"></a>Guida alla migrazione

Assicurarsi che è stato consultato l'articolo tramite questi articoli per assicurarsi che si conoscono alcuni dei prodotti differenze e sui concetti fondamentali prima di iniziare la migrazione.

- [Eseguire la migrazione dello schema][]
- [Eseguire la migrazione dei dati][]
- [Eseguire la migrazione del codice][]

## <a name="next-steps"></a>Passaggi successivi

GATTO (Team di consulenza cliente) è installato anche alcune istruzioni SQL Data Warehouse ottima che essi pubblicare tramite blog.  Esaminare le articolo [alla migrazione dei dati nell'archivio di dati di SQL Azure in pratica][] per ulteriori informazioni sulla migrazione.

<!--Image references-->

<!--Article references-->
[Eseguire la migrazione dello schema]: sql-data-warehouse-migrate-schema.md
[Eseguire la migrazione dei dati]: sql-data-warehouse-migrate-data.md
[Eseguire la migrazione del codice]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[La migrazione dei dati nell'archivio di dati di SQL Azure in pratica]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
