<properties
   pageTitle="Gestire i dati cronologici nelle tabelle temporali con criteri di conservazione | Microsoft Azure"
   description="Informazioni su come usare criteri di conservazione temporale per mantenere i dati cronologici sotto il controllo."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gestire i dati cronologici nelle tabelle temporali con criteri di conservazione

Tabelle temporali potrebbero aumentare dimensioni del database più valori normali tabelle, in particolare se è mantenere i dati cronologici per un periodo di tempo. Criteri di conservazione per i dati cronologici, pertanto sono importante della pianificazione e la gestione del ciclo di vita di ogni tabella temporale. Temporale tabelle nel Database di SQL Azure accompagnato da meccanismo di conservazione di semplice utilizzo che consente di eseguire questa operazione.

Conservazione cronologia temporale può essere configurato a livello singola tabella, che consente agli utenti di creare aging flessibile criteri. L'applicazione di criteri di conservazione temporale è semplice: viene richiesto solo un parametro per essere impostato durante la modifica dello schema o creazione tabella.

Dopo aver definito i criteri di conservazione, verrà avviato il Database di SQL Azure verifica periodicamente se sono presenti righe cronologia che sono idonee per la pulizia automatico dei dati. Identificazione delle righe corrispondenti e la rimozione della tabella Cronologia si verificano trasparente, l'attività in background pianificata, eseguire dal sistema. Condizione di età per le righe della tabella Cronologia sia selezionata in base alla colonna che rappresenta fine del periodo SYSTEM_TIME. Se il periodo di conservazione, ad esempio, è impostato su sei mesi, le righe della tabella idonee per la pulizia soddisfano la condizione seguente:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

Nell'esempio precedente si presuppone che **ValidTo** colonna corrisponde alla fine del periodo SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>Come è possibile configurare i criteri di conservazione?

Prima di configurare criteri di conservazione per una tabella temporale, selezionare prima di tutto se conservazione cronologici temporale è abilitato *livello di database*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Contrassegno di database **is_temporal_history_retention_enabled** è impostata su Sì per impostazione predefinita, ma gli utenti possono modificare con l'istruzione ALTER DATABASE. È anche automaticamente impostata su OFF dopo l'operazione [scegliere Ripristina ora](sql-database-point-in-time-restore-portal.md) . Per abilitare la pulizia di conservazione cronologia temporale per il database, eseguire l'istruzione seguente:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Anche se **is_temporal_history_retention_enabled** è disattivata, ma la pulizia automatica delle righe obsoleti non verrà attivata in questo caso, è possibile configurare criteri di conservazione per le tabelle temporale.


Criteri di conservazione viene configurato durante la creazione di tabelle specificando valore per il parametro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Database SQL Azure consente di specificare il periodo di conservazione utilizzando unità di tempo diversi: giorni, settimane, mesi e anni. Se HISTORY_RETENTION_PERIOD è omesso, verrà utilizzato conservazione infinito. È inoltre possibile utilizzare in modo esplicito infinito parola chiave.

In alcuni casi può essere necessario configurare i criteri di conservazione dopo la creazione di una tabella o per modificare in precedenza valore configurato. In questo caso utilizzare istruzione ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Impostazione SYSTEM_VERSIONING per disattivare la funzione valore periodo di conservazione *non vengono mantenuti* . Impostazione SYSTEM_VERSIONING su ON senza HISTORY_RETENTION_PERIOD specificato in modo esplicito i risultati del periodo di conservazione infinito.

Per esaminare lo stato corrente del criterio di conservazione, utilizzare la query seguente che eseguono l'accesso contrassegno di attivazione di conservazione temporale livello di database con periodi di conservazione per le singole tabelle:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>Come Elimina Database SQL di età righe?

Il processo di pulizia dipende dal layout indice della tabella Cronologia. È importante notare che *solo tabelle di cronologia con un indice cluster (struttura B o columnstore) possono avere criteri di conservazione limitata configurato*. Per eseguire la pulizia dei dati obsoleti per tutte le tabelle temporale periodo di conservazione limitata viene creata un'attività in background.
Logica di pulizia per l'indice cluster rowstore (struttura B) Elimina riga obsoleti piccoli (fino a 10 KB) la riduzione a icona pressione nel registro del database e i/o sottosistema. Sebbene la logica di pulizia utilizza indice B albero richiesto, ordine delle eliminazioni per le righe di versioni precedenti del periodo di conservazione non può essere garantito correttamente. Di conseguenza, *non hanno una dipendenza sull'ordine di pulizia nelle applicazioni*.

L'attività di pulizia per columnstore raggruppate vengono rimossi interi [gruppi di righe](https://msdn.microsoft.com/library/gg492088.aspx) in una sola volta (in genere contengono 1 milione di righe di ogni), che è molto efficiente, soprattutto se i dati cronologici viene generati a una velocità elevata.

![Conservazione columnstore raggruppate](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


La compressione dei dati eccellente e conservazione efficiente pulizia rende columnstore indice cluster ideale per gli scenari quando il carico di lavoro genera rapidamente quantità elevata di dati cronologici. Il motivo è tipica per intensivo [carichi di lavoro di elaborazione delle transazioni che utilizzano tabelle temporali](https://msdn.microsoft.com/library/mt631669.aspx) per il rilevamento delle modifiche e il controllo, analisi delle tendenze o IoT acquisizione di dati.

##<a name="index-considerations"></a>Considerazioni sull'indice

L'attività di pulizia per le tabelle con indice cluster rowstore richiede indice per iniziare con la colonna corrispondente alla fine del periodo SYSTEM_TIME. Se ad esempio indice non esiste, non sarà possibile configurare il periodo di conservazione limitata:

*Msg 13765, livello 16, stato 1 <br> </br> impostazione periodo di conservazione limitata non riuscita nella tabella temporale versione del sistema 'temporalstagetestdb.dbo.WebsiteUserInfo' perché la tabella Cronologia 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' non contiene indice cluster necessari. Valutare la possibilità di creare un cluster columnstore o un indice albero B a partire dalla colonna corrispondente a fondo SYSTEM_TIME periodo, nella tabella della cronologia.*

È importante notare che la tabella di cronologia predefinito creata da Database SQL Azure già è raggruppate indice è conforme per criteri di conservazione. Se si tenta di rimuovere tale indice in una tabella con periodo di conservazione limitata, l'operazione non riesce con l'errore seguente:

*Msg 13766, livello 16, stato 1 <br> </br> non è possibile eliminare l'indice cluster 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' perché utilizzato per la pulizia automatica dei dati obsoleti. Valutare la possibilità di impostare HISTORY_RETENTION_PERIOD infinito nella tabella temporale versione del sistema corrispondente se è necessario eliminare l'indice.*

Pulizia sull'indice columnstore cluster funziona in modo ottimale se vengono inserite righe cronologiche l'ordine crescente (in ordine al termine del periodo colonna), vale a dire le maiuscole/minuscole quando la tabella Cronologia viene popolata esclusivamente dal meccanismo di SYSTEM_VERSIONIOING. Se righe nella tabella della cronologia non vengono ordinate in fondo colonna periodo (che può essere le maiuscole/minuscole se si esegue la migrazione dati cronologici esistenti), è necessario ricreare indice columnstore raggruppato nella parte superiore dell'indice rowstore B albero ordinata correttamente, per ottenere prestazioni ottimali.

Evitare la ricostruzione columnstore raggruppate indice nella tabella della cronologia con il periodo di conservazione limitata, poiché può cambiare l'ordinamento nella riga Raggruppa naturalmente imposti dall'operazione di controllo delle versioni di sistema. Se è necessario ricreare indice columnstore raggruppate nella tabella della cronologia, tale scopo, crearne uno nuovo nella parte superiore di indice B albero conforme, mantenendo l'ordinamento in rowgroups necessarie per la pulizia dei dati regolare. Lo stesso approccio da eseguire quando si crea tabella temporale esistente nella tabella della cronologia che è raggruppate indice di colonna senza ordine garantito dati:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Quando il periodo di conservazione limitata è configurato per la tabella Cronologia con l'indice columnstore raggruppate, non è possibile creare ulteriori B albero indici su tale tabella:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Un tentativo di eseguire prima istruzione avrà esito negativo con l'errore seguente:

*Msg 13772, livello 16, stato 1 <br> </br> non è possibile creare non cluster indice in una tabella di cronologia temporale 'WebsiteUserInfoHistory' perché il relativo periodo di conservazione limitata e indice columnstore cluster definito.*

##<a name="querying-tables-with-retention-policy"></a>Tabelle query con criteri di conservazione

Tutte le query della tabella temporale filtrare automaticamente le righe nella cronologia corrispondenti criteri di conservazione limitata, per evitare risultati imprevisti e non coerenti, poiché è possono eliminare righe obsoleti, l'attività di pulizia, *in qualsiasi punto nel tempo e in ordine non autorizzato*.

Nell'immagine seguente viene illustrato il piano di query per una query semplice:

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

Il piano di query include altre filtro applicato alla fine della colonna periodo (ValidTo) nell'operatore "Raggruppate digitalizzare indice" nella tabella della cronologia (evidenziata). Si presuppone che il periodo di conservazione 1 mese è stata impostata su WebsiteUserInfo tabella.

![Filtro di query di conservazione](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Tuttavia, se si esegue una query tabella Cronologia direttamente, potrebbe apparire righe che risalgono a più criteri di conservazione specificato periodo, ma senza qualsiasi garanzia per i risultati della query ripetibili. L'immagine seguente mostra il piano di esecuzione query per la query nella tabella della cronologia senza filtri aggiuntivi applicati:

![Cronologia delle query senza filtro di conservazione](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Non utilizzare logica aziendale nella tabella Cronologia oltre il periodo di conservazione di lettura come è possibile ottenere risultati imprevisti o incoerenti. È consigliabile utilizzare query temporali con clausola FOR SYSTEM_TIME per l'analisi dei dati in tabelle temporali.

##<a name="point-in-time-restore-considerations"></a>Scegliere in considerazioni Ripristina ora

Quando si crea nuovo database ripristinando [database esistente in un punto specifico nel tempo](sql-database-point-in-time-restore-portal.md), ha conservazione temporale disabilitata al livello del database. (**is_temporal_history_retention_enabled** impostato su). Questa funzionalità consente di esaminare tutte le righe nella cronologia al momento del ripristino, senza doversi che le righe obsoleti vengono rimosse prima di passare alla loro della query. È possibile usarlo per *analizzare i dati cronologici oltre il periodo di conservazione configurati*.

Si supponga che una tabella temporale ha la conservazione di un mese periodo specificata. Se il database è stato creato in livello di servizio Premium, sarà creare la copia del database con lo stato del database di a 35 giorni di nuovo in passato. Che in modo efficace consente di analizzare righe cronologia recapitati da fino a 65 giorni tramite una query la tabella Cronologia diretta.

Se si desidera attivare pulizia temporale criteri di conservazione, eseguire l'istruzione Transact-SQL seguente dopo il punto di ripristino ora:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Passaggi successivi

Per informazioni su come utilizzare tabelle temporali nelle applicazioni estrarre [Guida introduttiva a temporale tabelle nel Database di SQL Azure](sql-database-temporal-tables.md).

Visitare Channel 9 per ascoltare una [parte reale implementazione temporale storie di successo](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e guardare un [live dimostrazione temporale](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Per informazioni dettagliate sulle tabelle temporali, consultare [la documentazione MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
