<properties 
    pageTitle="Codice di Buffer circolare XEvent per Database SQL | Microsoft Azure" 
    description="Fornisce un esempio di codice Transact-SQL produttrice semplice e veloce tramite la destinazione di Buffer circolare, nel Database di SQL Azure." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Chiama il codice di destinazione di Buffer per eventi "Extended" nel Database di SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Si desidera un esempio di codice completo per un modo rapido più semplice per acquisire e segnalare informazioni per un evento "Extended" durante un test. La destinazione più semplice per i dati di evento "Extended" è la [destinazione di Buffer circolare](http://msdn.microsoft.com/library/ff878182.aspx).


In questo argomento viene presentato un esempio di codice Transact-SQL che:


1. Crea una tabella con i dati per illustrare con.

2. Crea una sessione di un evento esistente "Extended", vale a dire **sqlserver.sql_statement_starting**.
    - L'evento è limitata ai istruzioni SQL che contengono una determinata stringa di aggiornamento: **istruzione come '% aggiornamento tabEmployee %'**.
    - Sceglie di inviare l'output dell'evento a un valore di destinazione del tipo di Buffer circolare, vale a dire **package0.ring_buffer**.

3. Avvia la sessione di evento.

4. Problemi di un paio di semplici istruzioni SQL UPDATE.

5. Problemi di un'istruzione SQL SELECT per recuperare output evento da Buffer circolare.
    - **Sys.dm_xe_database_session_targets** e altri gestione dinamica viste vengono unite.

6. Interrompere la sessione degli eventi.

7. Elimina la destinazione di Buffer circolare, per rilasciare le risorse.

8. Elimina la sessione di eventi e la tabella demo.


## <a name="prerequisites"></a>Prerequisiti


- Un account Azure e sottoscrizione. È possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).


- È possibile creare una tabella in un database.
 - Facoltativamente è possibile [creare un database di dimostrazione **AdventureWorksLT** ](sql-database-get-started.md) in minuti.


- SQL Server Management Studio (ssms.exe), ideale la versione più recente mensile aggiornamento. È possibile scaricare il più recente ssms.exe da:
 - Argomento [Scaricare SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Un collegamento diretto per il download.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Esempio di codice


Con una piccola modifica, è possibile eseguire il Buffer circolare codice seguente nel Database di SQL Azure o Microsoft SQL Server. La differenza è la presenza del nodo database' nome alcune gestione dinamica viste, utilizzata nella clausola FROM nel passaggio 5. Per esempio:

- Sys.dm_xe**database**_session_targets
- Sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Contenuto del Buffer ad anello


È stato usato ssms.exe per eseguire l'esempio di codice.


Per visualizzare i risultati, che facendo clic sulla cella sotto l' intestazione di colonna **target_data_XML**.

Quindi nel riquadro dei risultati che facendo clic sulla cella sotto l' intestazione di colonna **target_data_XML**. Questo fare clic su Creazione di un altro progetto esportati in ssms.exe in cui è stato visualizzato il contenuto della cella risultato come XML.


L'output viene visualizzata nel blocco seguente. Riferisca lunga, ma è solo due **<event>** elementi.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Rilasciare risorse utilizzate dal Buffer circolare


Dopo aver con il Buffer circolare, è possibile rimuoverlo e rilascio delle risorse emissione un' **istruzione ALTER** simile al seguente:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


La definizione della sessione di evento è aggiornata, ma non eliminata. In un secondo momento è possibile aggiungere un'altra istanza di Buffer circolare alla sessione di evento:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Ulteriori informazioni


L'argomento principale per gli eventi "Extended" sul Database di SQL Azure è:


- [Considerazioni sulla evento estese nel Database di SQL](sql-database-xevent-db-diff-from-svr.md), che vengono messi a confronto alcuni aspetti di "Extended" eventi che si differenziano tra il Database di SQL Azure rispetto a Microsoft SQL Server.


Gli altri argomenti di esempio di codice per eventi estesi sono disponibili i collegamenti seguenti. Tuttavia, è necessario controllare regolarmente qualsiasi esempio per verificare se il campione è destinato a Microsoft SQL Server e Database di SQL Azure. È possibile decidere se è necessario eseguire l'esempio modifiche di lieve entità.


- Esempio di codice per il Database di SQL Azure: [il codice di destinazione File degli eventi per eventi "Extended" nel Database di SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
