<properties
    pageTitle="Esteso eventi nel Database di SQL | Microsoft Azure"
    description="Vengono descritti gli eventi "Extended" (XEvents) nel Database di SQL Azure e come le sessioni di evento sono leggermente diverse da sessioni di evento in Microsoft SQL Server."
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


# <a name="extended-events-in-sql-database"></a>Eventi estesi in Database SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Questo argomento viene illustrato come l'implementazione di eventi "Extended" nel Database di SQL Azure è leggermente diverso rispetto agli eventi "Extended" in Microsoft SQL Server.


- SQL Database V12 acquisita la caratteristica di eventi estesi nella seconda metà del calendario 2015.
- SQL Server ha eventi estesi da 2008.
- La funzionalità di "Extended" eventi sul Database di SQL è un sottoinsieme affidabile delle caratteristiche di SQL Server.


*XEvents* è un nome alternativo informale talvolta è utilizzata per "" Extended "eventi" nel blog e da altre posizioni informale.


> [AZURE.NOTE] In ottobre 2015, l'evento "Extended" sessione è attivata nel Database di SQL Azure a livello di anteprima. La data di disponibilità generale (GA) non è ancora impostata.
>
> Nella pagina di Azure [Aggiornamenti servizio](https://azure.microsoft.com/updates/?service=sql-database) sono i post quando vengono apportati annunci GA.


Ulteriori informazioni sugli eventi "Extended", per Database SQL Azure e Microsoft SQL Server, sono disponibile in:

- [Guida introduttiva: "Extended" eventi in SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Eventi "Extended"](http://msdn.microsoft.com/library/bb630282.aspx)


## <a name="prerequisites"></a>Prerequisiti


In questo argomento si presuppone che si dispone già di conoscenza di:


- [Servizio di Database SQL azure](https://azure.microsoft.com/services/sql-database/).


- [Eventi estesi](http://msdn.microsoft.com/library/bb630282.aspx) di Microsoft SQL Server.
 - La maggior parte della documentazione sugli eventi "Extended" si applica a SQL Server e Database SQL.


Esposizione precedente per gli elementi seguenti è utile quando si sceglie il File di evento come [destinazione](#AzureXEventsTargets):


- [Servizio di archiviazione di Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](../storage/storage-powershell-guide-full.md) - fornisce informazioni complete sulle PowerShell e sul servizio di archiviazione Azure.


## <a name="code-samples"></a>Esempi di codice


Negli argomenti correlati sono due esempi di codice:


- [Chiama il codice di destinazione di Buffer per eventi "Extended" nel Database di SQL](sql-database-xevent-code-ring-buffer.md)
 - Breve semplice script Transact-SQL.
 - Abbiamo sottolineare nell'argomento dell'esempio codice che, quando vengono eseguite con un valore di destinazione Buffer circolare, è necessario rilasciare le risorse eseguendo una rilascio alter `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` istruzione. In un secondo momento è possibile aggiungere un'altra istanza di Buffer circolare da `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Codice di destinazione File eventi per gli eventi "Extended" nel Database di SQL](sql-database-xevent-code-event-file.md)
 - Fase 1 è PowerShell per creare un contenitore di archiviazione Azure.
 - Fase 2 è Transact-SQL che utilizza il contenitore di archiviazione Azure.


## <a name="transact-sql-differences"></a>Differenze in Transact-SQL


- Quando si esegue il comando [Crea EVENT sessione](http://msdn.microsoft.com/library/bb677289.aspx) in SQL Server, utilizzare la clausola **Via SERVER** . Ma nel Database SQL di utilizzare invece la clausola **Via DATABASE** .


- La clausola **Via DATABASE** riguarda anche la [ALTER EVENT sessione](http://msdn.microsoft.com/library/bb630368.aspx) e comandi Transact-SQL [DROP EVENT sessione](http://msdn.microsoft.com/library/bb630257.aspx) .


- È una buona norma per includere l'opzione della sessione evento di **STARTUP_STATE = via** nelle istruzioni **ALTER EVENT SESSION** o **Creare evento sessione** .
 - Il valore **= ON** supporta un riavvio automatico dopo una riconfigurazione del database logico a causa di caso di errore.


## <a name="new-catalog-views"></a>Nuove viste del catalogo


La caratteristica di eventi estesi è supportata da molte [viste del catalogo](http://msdn.microsoft.com/library/ms174365.aspx). Viste catalogo informare è *basato su metadati o le definizioni* delle sessioni di evento creato dall'utente nel database corrente. Le visualizzazioni non vengono restituiti informazioni sulle istanze di sessioni di evento attivo.


| Nome del<br/>visualizzazione del catalogo | Descrizione |
| :-- | :-- |
| **Sys.database_event_session_actions** | Restituisce una riga per ogni azione di ogni evento di una sessione di evento. |
| **Sys.database_event_session_events** | Restituisce una riga per ogni evento in una sessione evento. |
| **Sys.database_event_session_fields** | Restituisce una riga per ogni colonna possibile personalizzare in modo esplicito è stata impostata su eventi e i siti di destinazione. |
| **Sys.database_event_session_targets** | Restituisce una riga per ogni destinatario dell'evento per una sessione evento. |
| **Sys.database_event_sessions** | Restituisce una riga per ogni sessione di evento in database di SQL. |


In Microsoft SQL Server, viste catalogo simili hanno nomi che includono *server\_ * anziché *.database\_*. Il modello di nome è simile a **sys.server_event_%**.


## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Gestione dinamica nuove visualizzazioni [(viste)](http://msdn.microsoft.com/library/ms188754.aspx)


Database SQL Azure ha [gestione dinamica viste](http://msdn.microsoft.com/library/bb677293.aspx) che supportano eventi "Extended". Viste informare sessioni evento *attive* .


| Nome della DMV | Descrizione |
| :-- | :-- |
| **Sys.dm_xe_database_session_event_actions** | Restituisce informazioni sulle operazioni sessione evento. |
| **Sys.dm_xe_database_session_events** | Restituisce informazioni sugli eventi di sessione. |
| **Sys.dm_xe_database_session_object_columns** | Mostra i valori di configurazione per gli oggetti associati a una sessione. |
| **Sys.dm_xe_database_session_targets** | Restituisce informazioni sulle destinazioni sessione. |
| **Sys.dm_xe_database_sessions** | Restituisce una riga per ogni sessione di evento che l'ambito è impostato per il database corrente. |


In Microsoft SQL Server, simile catalogo visualizzazioni sono denominate senza il * \_database* parte del nome, ad esempio:


- **sys.dm_xe_sessions**anziché nome<br/>**sys.dm_xe_database_sessions**.


### <a name="dmvs-common-to-both"></a>Viste comuni a entrambi


Per eventi estesi sono disponibili ulteriori viste comuni per il Database di SQL Azure e Microsoft SQL Server:


- **Sys.dm_xe_map_values**
- **Sys.dm_xe_object_columns**
- **Sys.dm_xe_objects**
- **Sys.dm_xe_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Trovare disponibili esteso eventi, azioni e i siti di destinazione


È possibile eseguire un semplice SQL **Selezionare** per ottenere un elenco di destinazione, eventi disponibili e azioni.


```
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Siti di destinazione per le sessioni di evento di Database SQL


Ecco i siti di destinazione che consente di acquisire risultati dalle sessioni di evento nel Database di SQL:


- [Destinazione Buffer circolare](http://msdn.microsoft.com/library/ff878182.aspx) : posiziona i dati degli eventi in memoria.
- [Contatore degli eventi destinazione](http://msdn.microsoft.com/library/ff878025.aspx) : conta tutti gli eventi che si verificano durante una sessione di eventi "Extended".
- [Destinazione File evento](http://msdn.microsoft.com/library/ff878115.aspx) : scrive buffer completo a un contenitore di archiviazione Azure.


[Individua eventi di Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API non è disponibile per gli eventi "Extended" sul Database SQL.


## <a name="restrictions"></a>Restrizioni


Esistono un paio di differenze correlate alla sicurezza befitting ambiente basato su cloud di Database SQL:


- Eventi estesi siano fondati sul modello di isolamento singola tenant. Una sessione di evento in un database non è possibile accedere a dati o gli eventi da un altro database.

- Non è possibile eseguire un'istruzione **CREATE EVENT sessione** nel contesto del database **master** .


## <a name="permission-model"></a>Modello di autorizzazioni


È necessario disporre dell'autorizzazione di **controllo** per un'istruzione **CREATE EVENT sessione** nel database. Il proprietario del database (dbo) dispone dell'autorizzazione **controllo** .


### <a name="storage-container-authorizations"></a>Autorizzazioni contenitore dello spazio di archiviazione


Token sa che generare per il contenitore di archiviazione Azure specificare **rwl** per le autorizzazioni. Il valore di **rwl** sono disponibili le autorizzazioni seguenti:


- Lettura
- Scrittura
- Elenco


## <a name="performance-considerations"></a>Considerazioni sulle prestazioni


Esistono scenari in cui utilizzo intensivo di eventi estesi possa accumulare più attiva memoria superiore a quella integro dell'intero sistema. Pertanto in modo dinamico il sistema di Database SQL Azure imposta e viene regolata limiti sulla quantità di memoria attiva che può essere accumulata da una sessione evento. Diversi fattori contribuiscono al calcolo dinamico.


Se si riceve un messaggio di errore che indica che è stata applicata una massimo di memoria, alcune azioni correttive che è possibile eseguire sono:


- Eseguire meno sessioni eventi simultanee.


- Tramite le istruzioni **CREATE** e **ALTER** per le sessioni di evento, ridurre la quantità di memoria è specificare la **MAX\_memoria** clausola.


### <a name="network-latency"></a>Latenza di rete


La destinazione del **File evento** potrebbe verificarsi errori durante il salvataggio dati di Azure archiviazione BLOB o latenza di rete. Altri eventi nel Database di SQL potrebbero essere posticipati durante l'attesa per la comunicazione di rete completare. Il ritardo può rallentare il carico di lavoro.

- Per ridurre il rischio di prestazioni, evitare di impostare l'opzione **EVENT_RETENTION_MODE** su **NO_EVENT_LOSS** in ogni evento definizioni sessione.


## <a name="related-links"></a>Collegamenti correlati


- [Utilizzo di PowerShell Azure con lo spazio di archiviazione Azure](../storage/storage-powershell-guide-full.md).
- [Cmdlet di archiviazione Azure](http://msdn.microsoft.com/library/dn806401.aspx)


- [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](../storage/storage-powershell-guide-full.md) - fornisce informazioni complete sulle PowerShell e sul servizio di archiviazione Azure.
- [Come usare archiviazione Blob da .NET](../storage/storage-dotnet-how-to-use-blobs.md)


- [CREARE CREDENZIALI (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREARE sessione evento (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Post di blog di Jonathan Kehayias avvisi "Extended" in Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Gli altri argomenti di esempio di codice per eventi estesi sono disponibili i collegamenti seguenti. Tuttavia, è necessario controllare regolarmente qualsiasi esempio per verificare se il campione è destinato a Microsoft SQL Server e Database di SQL Azure. È possibile decidere se è necessario eseguire l'esempio modifiche di lieve entità.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
