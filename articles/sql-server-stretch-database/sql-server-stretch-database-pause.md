<properties
    pageTitle="Interrompere e riprendere la migrazione dei dati (Database di zoom) | Microsoft Azure"
    description="Informazioni su come sospendere o riprendere la migrazione dei dati di Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Interrompere e riprendere la migrazione dei dati (Database di zoom)

Per sospendere o riprendere la migrazione dei dati di Azure, scegliere **Zoom** per una tabella di SQL Server Management Studio, quindi **posizionare il puntatore** per mettere in pausa la migrazione dei dati o **riprendere** a riprendere la migrazione dei dati. È inoltre possibile utilizzare Transact\-SQL per sospendere o riprendere la migrazione dei dati.

Migrazione dei dati di pausa in singole tabelle quando si desidera per la risoluzione dei problemi nel server locale o la larghezza di banda di rete disponibili.

## <a name="pause-data-migration"></a>Migrazione dei dati di pausa

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Usare SQL Server Management Studio per sospendere la migrazione dei dati

1.  Nella finestra Esplora oggetti di SQL Server Management Studio selezionare l'estensione\-abilitato per il quale si desidera posizionare la migrazione dei dati nella tabella.

2.  Destra\-fare clic su e selezionare **Zoom**e quindi selezionare **Pausa**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Utilizzare Transact\-SQL per mettere in pausa la migrazione dei dati
Eseguire il comando seguente.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Riprendere la migrazione dei dati

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Usare SQL Server Management Studio per riprendere la migrazione dei dati

1.  Nella finestra Esplora oggetti di SQL Server Management Studio selezionare l'estensione\-abilitato per il quale si desidera riprendere la migrazione dei dati nella tabella.

2.  Destra\-fare clic su e selezionare **Zoom**e quindi selezionare **Riprendi**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Utilizzare Transact\-SQL per riprendere la migrazione dei dati
Eseguire il comando seguente.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Verificare che la migrazione sia attivo o in pausa

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Usare SQL Server Management Studio per verificare che la migrazione sia attivo o in pausa
In SQL Server Management Studio aprire **Monitoraggio Database zoom** e controllare il valore della colonna **Lo stato della migrazione** . Per ulteriori informazioni, vedere [Monitor e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utilizzare Transact-SQL per verificare che la migrazione sia attivo o in pausa
La visualizzazione di catalogo **sys.remote_data_archive_tables** della query e controllare il valore della colonna **is_migration_paused** . Per ulteriori informazioni, vedere [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Vedere anche

[Istruzione ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitor e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md)
