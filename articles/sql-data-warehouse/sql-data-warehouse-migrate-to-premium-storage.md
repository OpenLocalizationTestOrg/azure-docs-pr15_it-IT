<properties
   pageTitle="Eseguire la migrazione il Warehouse di dati di SQL Azure esistente allo spazio di archiviazione premium | Microsoft Azure"
   description="Istruzioni per la migrazione di un Data Warehouse SQL esistente allo spazio di archiviazione premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migrazione a dettagli di archiviazione Premium
SQL Data Warehouse introdotto recente [Premium lo spazio di archiviazione per maggiore prevedibilità delle prestazioni][].  È ora sono pronti per eseguire la migrazione esistente Data warehouse attualmente su archiviazione Standard allo spazio di archiviazione Premium.  Continuare a leggere per ulteriori informazioni su come e quando si verificano le migrazioni automatiche e su come eseguire la migrazione automatica se si preferisce per controllare quando si verifica il tempo di inattività.

Se si dispone di più di una Data Warehouse, utilizzare la [programmazione automatica migrazione][] riportata di seguito per determinare quando verranno inoltre migrato.

## <a name="determine-storage-type"></a>Determinare il tipo di spazio di archiviazione
Se è stato creato un DW prima delle date riportata di seguito, al momento si usano lo spazio di archiviazione Standard.  Ogni Data Warehouse su Standard dello spazio di archiviazione è soggetto a migrazione automatica è una notifica nella parte superiore e il Data Warehouse nel [Portale di Azure][] che indica che "aggiornamento*in un programma per lo spazio di archiviazione premium richiede un'interruzione.  Informazioni su altre ->*. "

| **Area geografica**          | **DW creati prima della data**   |
| :------------------ | :-------------------------------- |
| Australia orientale      | Archiviazione Premium non è ancora disponibile |
| Australia sudorientale | 5 agosto 2016                    |
| Brasile sud        | 5 agosto 2016                    |
| Canada centrale      | 25 maggio 2016                      |
| Canada orientale         | 26 maggio 2016                      |
| Centrale USA          | 26 maggio 2016                      |
| Cina est          | Archiviazione Premium non è ancora disponibile |
| America del Nord Cina         | Archiviazione Premium non è ancora disponibile |
| Asia orientale           | 25 maggio 2016                      |
| Stati Uniti orientali             | 26 maggio 2016                      |
| US2 orientale            | 27 maggio 2016                      |
| India centrale       | 27 maggio 2016                      |
| Sud India         | 26 maggio 2016                      |
| India ovest          | Archiviazione Premium non è ancora disponibile |
| Giappone est          | 5 agosto 2016                    |
| Giappone ovest          | Archiviazione Premium non è ancora disponibile |
| America del Nord centrale USA    | Archiviazione Premium non è ancora disponibile |
| Europa Nord America        | 5 agosto 2016                    |
| Sud centrale USA    | 27 maggio 2016                      |
| Area Asia sudorientale      | 24 maggio 2016                      |
| Europa occidentale         | 25 maggio 2016                      |
| Stati Uniti centro ovest     | 26 agosto 2016                   |
| Usa ovest             | 26 maggio 2016                      |
| US2 ovest            | 26 agosto 2016                   |

## <a name="automatic-migration-details"></a>Dettagli sulla migrazione automatica
Per impostazione predefinita, si esegue la migrazione del database dell'utente durante 6 pm e 6 am in ora locale dell'area durante la [pianificazione di migrazione automatica][] sotto.  La Data Warehouse esistente sarà inutilizzabile durante la migrazione.  Si prevede che la migrazione richiederà circa un'ora per TB di spazio di archiviazione per Data Warehouse.  Abbiamo garantisce che non addebitate in qualsiasi parte della migrazione automatica.

> [AZURE.NOTE] Non sarà possibile usare il Data Warehouse esistente durante la migrazione.  Una volta completata la migrazione, la Data Warehouse sarà torna online.

I dettagli seguenti sono i passaggi che Microsoft tenendo conto dell'utente per completare la migrazione e non richiede alcun coinvolgimento da parte dell'utente.  In questo esempio, si supponga che il DW esistente in archiviazione Standard attualmente denominato "MyDW".

1.  Microsoft Rinomina "MyDW" a "MyDW_DO_NOT_USE_ [Timestamp]"
2.  Microsoft sospende "MyDW_DO_NOT_USE_ [Timestamp]".  Durante questo periodo, viene accettata una copia di backup.  Se si riscontrano problemi durante il processo, è possibile riscontrare più pausa/curriculum.
3.  Microsoft crea un nuovo DW denominato "MyDW" allo spazio di memorizzazione Premium dal backup creato nel passaggio 2.  "MyDW" non verrà più visualizzata fino al termine del ripristino.
4.  Una volta completato il ripristino, viene restituito "MyDW" per la stessa DWUs e in pausa o attivo lo stato prima della migrazione.
5.  Una volta completata la migrazione, Microsoft Elimina "MyDW_DO_NOT_USE_ [Timestamp]"
    
> [AZURE.NOTE] Queste impostazioni non vengono trasferiti come parte della migrazione:
> 
>   -  Il controllo a livello di Database deve essere riattivata
>   -  È necessario essere riaggiunto regole del firewall al livello del **Database** .  Non sono interessati regole del firewall a livello di **Server** .

### <a name="automatic-migration-schedule"></a>Pianificare la migrazione automatica
Migrazioni automatiche verificano da pm di 6-6 am (ora locale per l'area geografica) durante la pianificazione di interruzione seguente.

| **Area geografica**          | **Data di inizio prevista**     | **Data di fine prevista**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia orientale      | Non è ancora determinato           | Non è ancora determinato           |
| Australia sudorientale | 10 agosto 2016              | 24 agosto 2016              |
| Brasile sud        | 10 agosto 2016              | 24 agosto 2016              |
| Canada centrale      | 23 giugno 2016                | 1 luglio 2016                 |
| Canada orientale         | 23 giugno 2016                | 1 luglio 2016                 |
| Centrale USA          | 23 giugno 2016                | 4 luglio 2016                 |
| Cina est          | Non è ancora determinato           | Non è ancora determinato           |
| America del Nord Cina         | Non è ancora determinato           | Non è ancora determinato           |
| Asia orientale           | 23 giugno 2016                | 1 luglio 2016                 |
| Stati Uniti orientali             | 23 giugno 2016                | 11 luglio 2016                |
| US2 orientale            | 23 giugno 2016                | 8 luglio 2016                 |
| India centrale       | 23 giugno 2016                | 1 luglio 2016                 |
| Sud India         | 23 giugno 2016                | 1 luglio 2016                 |
| India ovest          | Non è ancora determinato           | Non è ancora determinato           |
| Giappone est          | 10 agosto 2016              | 24 agosto 2016              |
| Giappone ovest          | Non è ancora determinato           | Non è ancora determinato           |
| America del Nord centrale USA    | Non è ancora determinato           | Non è ancora determinato           |
| Europa Nord America        | 10 agosto 2016              | 31 agosto 2016              |
| Sud centrale USA    | 23 giugno 2016                | 2 ° luglio 2016                 |
| Area Asia sudorientale      | 23 giugno 2016                | 1 luglio 2016                 |
| Europa occidentale         | 23 giugno 2016                | 8 luglio 2016                 |
| Stati Uniti centro ovest     | 14 agosto 2016              | 31 agosto 2016              |
| Usa ovest             | 23 giugno 2016                | 7 luglio 2016                 |
| US2 ovest            | 14 agosto 2016              | 31 agosto 2016              |

## <a name="self-migration-to-premium-storage"></a>Self-migrazione allo spazio di archiviazione Premium
Se si desidera controllare quando si verificherà il tempo di inattività, è possibile utilizzare la procedura seguente per eseguire la migrazione di un Data Warehouse esistente in archiviazione Standard allo spazio di archiviazione Premium.  Se si sceglie di eseguire la migrazione di automatica, è necessario completare la self-migrazione prima di inizia la migrazione automatica in quell'area per evitare il rischio di migrazione automatica che causano conflitti (fare riferimento alla [pianificazione di migrazione automatica][]).

### <a name="self-migration-instructions"></a>Istruzioni di migrazione automatica
Se si desidera controllare il tempo di inattività, è possibile eseguire la self-migrazione il Data Warehouse mediante backup e ripristino.  La parte di ripristino della migrazione deve essere necessaria circa un'ora per TB di spazio di archiviazione per DW.  Se si desidera mantenere lo stesso nome una volta completata la migrazione, seguire la procedura per la [procedura per rinominare durante la migrazione][]. 

1.  [Sospendere][] il DW che utilizza un backup automatico
2.  [Ripristinare][] l'ultima istantanea
3.  Eliminare il DW esistente in archiviazione Standard. **Se non si riesce a eseguire questo passaggio, verrà addebitata per entrambi DWs.**

> [AZURE.NOTE] Queste impostazioni non vengono trasferiti come parte della migrazione:
> 
>   -  Il controllo a livello di Database deve essere riattivata
>   -  È necessario essere riaggiunto regole del firewall al livello del **Database** .  Non sono interessati regole del firewall a livello di **Server** .

#### <a name="optional-steps-to-rename-during-migration"></a>Facoltativo: procedura per rinominare durante la migrazione 
Due database nello stesso server logico non è possibile specificare lo stesso nome. SQL Data Warehouse supporta ora la possibilità di rinominare una DW.

In questo esempio, si supponga che il DW esistente in archiviazione Standard attualmente denominato "MyDW".

1.  Rinominare "MyDW" con il comando ALTER DATABASE che segue a un elemento come "MyDW_BeforeMigration".  Questo comando interrompe tutte le transazioni esistenti e deve essere eseguito nel database master venga eseguita correttamente.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Pausa][] "MyDW_BeforeMigration", ovvero un backup automatico
3.  [Ripristinare][] le più recenti di un nuovo database con il nome usato per snapshot (ex: "MyDW")
4.  Eliminare "MyDW_BeforeMigration".  **Se non si riesce a eseguire questo passaggio, verrà addebitata per entrambi DWs.**

> [AZURE.NOTE] Queste impostazioni non vengono trasferiti come parte della migrazione:
> 
>   -  Il controllo a livello di Database deve essere riattivata
>   -  È necessario essere riaggiunto regole del firewall al livello del **Database** .  Non sono interessati regole del firewall a livello di **Server** .

## <a name="next-steps"></a>Passaggi successivi
Con la modifica dello spazio di archiviazione Premium, abbiamo aumentato anche il numero di file di database blob in architettura sottostante della Warehouse dati.  Per ottimizzare le prestazioni di questa modifica, è consigliabile rigenerare gli indici Columnstore raggruppate utilizzando il seguente script.  Script riportato di seguito funziona imposto alcuni dati esistenti di BLOB aggiuntive.  Se nessuna azione, i dati naturalmente ridistribuire nel tempo come caricare più dati delle tabelle Data Warehouse.

**Prerequisiti:**

1.  Data Warehouse deve essere eseguita con 1.000 DWUs o versione successiva (vedere [scala grande potenza][])
2.  Utente che esegue lo script deve essere al [ruolo ricoperto dai partecipanti mediumrc][] o versione successiva
    1.  Per aggiungere un utente a questo ruolo, eseguire le operazioni seguenti: 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Se si riscontrano problemi con il Data Warehouse, [creare un ticket di supporto][] e la Guida di riferimento "Migrazione a Premium lo spazio di archiviazione" cause possibili.

<!--Image references-->

<!--Article references-->
[pianificare la migrazione automatica]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[creare un ticket di supporto]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pausa]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Ripristinare]: sql-data-warehouse-restore-database-portal.md
[procedura per rinominare durante la migrazione]: #optional-steps-to-rename-during-migration
[scala grande potenza]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[ruolo mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Spazio di archiviazione Premium per maggiore prevedibilità delle prestazioni]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portale di Azure]: https://portal.azure.com
