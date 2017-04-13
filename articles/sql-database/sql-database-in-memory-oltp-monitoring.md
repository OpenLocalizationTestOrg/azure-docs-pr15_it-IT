<properties
    pageTitle="Monitorare lo spazio di archiviazione in memoria XTP | Microsoft Azure"
    description="Stima e monitorare lo spazio di archiviazione in memoria XTP utilizzano, capacità; risolvere l'errore capacità 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Spazio di archiviazione In memoria OLTP monitor

Quando si usa [OLTP In memoria](sql-database-in-memory.md), dei dati in tabelle ottimizzata memoria e le variabili di tabella si trovano nello spazio di memorizzazione OLTP In memoria. Ogni livello di servizio Premium ha una dimensione di spazio di archiviazione OLTP In memoria massima, descritte in questo [articolo livelli del servizio di Database SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Una volta superato il limite, inserire e aggiornare le operazioni venga avviato non funziona (con errore 41823). A questo punto si sarà necessario per eliminare i dati per recuperare memoria, o aggiornare il livello di prestazioni del database.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinare se i dati si adatteranno in delimitatore lo spazio di archiviazione in memoria

Determinare l'estremità di spazio di archiviazione: per le terminazioni di spazio di archiviazione dei diversi livelli di servizio Premium, consultare l' [articolo livelli del servizio di Database SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) .

La stima dei requisiti di memoria per works ottimizzata memoria tabella nello stesso modo per SQL Server esegue nel Database di SQL Azure. Richiedere alcuni minuti per esaminare l'argomento su [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Si noti che la tabella e le righe delle variabili tabella, nonché gli indici, contare verso la dimensione dei dati utente max. Inoltre, ALTER TABLE deve sufficiente per creare una nuova versione dell'intera tabella e gli indici.

## <a name="monitoring-and-alerting"></a>Monitoraggio e gestione degli avvisi

È possibile monitorare lo spazio di archiviazione in memoria come percentuale di [spazio di archiviazione cap per il livello di prestazioni](sql-database-service-tiers.md#service-tiers-for-single-databases) nel [portale](https://portal.azure.com/)di Azure: 

- Nella e Database, individuare la casella di utilizzo delle risorse e fare clic su Modifica.
- Quindi selezionare l'unità di misura metriche `In-Memory OLTP Storage percentage`.
- Per aggiungere un avviso, fare clic sulla casella utilizzo delle risorse per aprire e il metrico, quindi fare clic su Aggiungi avviso.

O utilizzare la query seguente per illustrare l'utilizzo di spazio di archiviazione in memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Correggere situazioni di memoria esaurita - errore 41823

Esecuzione di memoria esaurita risultati in Inserisci, aggiornare e creare operazioni non funziona con il messaggio di errore 41823.

Messaggio di errore 41823 indica che la memoria del ottimizzata tabelle e le variabili di tabella hanno superato le dimensioni massime.

Per risolvere questo errore è:


- Eliminare i dati dalle tabelle ottimizzata memoria, potenzialmente possibilità di scaricare i dati da tabelle tradizionale, basata su disco. oppure
- Aggiornare il livello di servizio a uno con spazio sufficiente in memoria per i dati che è necessario mantenere nelle tabelle di memoria ottimizzato.

## <a name="next-steps"></a>Passaggi successivi
Risorse aggiuntive sull'utilizzo di [monitoraggio di Database SQL Azure viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
