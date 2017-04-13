<properties
   pageTitle="Cloud continuità aziendale - ripristinare un database eliminato - Database SQL | Microsoft Azure"
   description="Le informazioni in un momento ripristino, che consente di ripristinare un Database di SQL Azure un punto precedente nel tempo (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Ripristinare un database di SQL Azure con backup automatici

Database SQL sono disponibili tre opzioni per il ripristino del database con [che database SQL di automatizzato di backup](sql-database-automated-backups.md). È possibile ripristinare un database da un backup servizio avviato durante il [periodo di conservazione](sql-database-service-tiers.md) per:

- Un nuovo database nello stesso server logico salvato in un punto specifico nel tempo entro il periodo di conservazione. 
- Un database nello stesso server logici di ripristinare il tempo di eliminazione di un database eliminato.
- Un nuovo database in qualsiasi server logico in un'area di ripristinare il backup giornalieri più recenti in archiviazione blob replicato geografico (RA GRS).

È possibile utilizzare [automatizzato di Database SQL di backup](sql-database-automated-backups.md) anche per creare una [copia del database](sql-database-copy.md) in qualsiasi server logico in qualsiasi area di coerente a con il Database di SQL corrente. È possibile usare copia del database ed [esportare in un BACPAC](sql-database-export.md) per archiviare una copia di un database per lo spazio di archiviazione a lungo termine oltre il periodo di conservazione coerente o trasferire una copia del database in un locale o in macchine Virtuali di Azure istanza di SQL Server.

## <a name="recovery-time"></a>Tempo di ripristino

Il tempo di ripristino per ripristinare un database utilizzando il backup del database automatizzato è interessato da diversi fattori: 
 - Le dimensioni del database
 - Le prestazioni del database
 - Il numero dei log delle transazioni correlate
 - La quantità di attività che deve essere riprodotti per recuperare il punto di ripristino
 - La larghezza di banda di rete se il ripristino si trova in un'area diversa 
 - Il numero di richieste simultanee Ripristina elaborate nell'area di destinazione. 
 
 Per un database molto grande e/o attivo il ripristino può richiedere alcune ore. Se c'è interruzione prolungata in un'area, è possibile che sia un numero elevato di richieste di ripristino geografico viene elaborate da altre aree. Se sono disponibili numerose richieste questo potrebbe aumentare il tempo di ripristino per i database in quell'area. La maggior parte dei database Ripristina completata entro 12 ore.

 Non esiste nessuna funzionalità incorporata di massa Ripristina. Il [Database SQL Azure: ripristino completo](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script è illustrato un esempio di un modo per eseguire questa operazione.

> [AZURE.IMPORTANT] Per recuperare utilizzando backup automatici, è necessario essere un membro del ruolo di collaboratore di SQL Server nella sottoscrizione o essere il proprietario della sottoscrizione. È possibile recuperare tramite il portale di Azure, PowerShell o API REST. Non è possibile utilizzare Transact-SQL. 

## <a name="point-in-time-restore"></a>Ripristinare In un momento

Ripristinare In un momento consente di ripristinare un database esistente come nuovo database in un momento precedente nel tempo nello stesso server logici utilizzando [automatizzato di Database SQL di backup](sql-database-automated-backups.md). Non è possibile sovrascrivere il database esistente. È possibile ripristinare in un momento precedente nel tempo tramite il [portale di Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) o l' [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Ripristinare In un momento: Portale di Azure](sql-database-point-in-time-restore-portal.md)
- [Ripristinare In un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

È possibile ripristinare il database a qualsiasi livello di prestazioni o elastici pool. È necessario avere a disposizione una quota DTU sufficiente sul server logico o pool flessibile. Tenere presente che il ripristino crea un nuovo database e che il livello di livello e le prestazioni di servizio del database ripristinato potrebbe essere diverso dallo stato corrente del database attivo. Al termine, il database ripristinato è un normale database online completamente accessibile alle tariffe normali in base al livello di livello e le prestazioni del servizio. Non si comportano in base alle tariffe fino al ripristino del database.

In genere si ripristina un database in un punto di earler ai fini del ripristino. In tal caso, è possibile gestire il database ripristinato come sostituzione per il database originale o utilizzare per recuperare i dati e quindi aggiornare il database originale. 

- ***Sostituzione del database:*** Se il database ripristinato viene usato come sostituzione per il database originale, è necessario verificare le prestazioni e/o il livello di servizio appropriati e ridimensionare il database, se necessario. È possibile rinominare il database originale e quindi assegnare il database ripristinato il nome originale con il comando ALTER DATABASE in Transact-SQL. 
- ***Il recupero dei dati:*** Se si prevede di recuperare dati da database ripristinato di correggere un errore di utente o un'applicazione, sarà necessario separatamente scrivere ed eseguire qualsiasi script di recupero dati è necessario estrarre i dati dal database ripristinato al database originale. Anche se l'operazione di ripristino può richiedere molto tempo, il ripristino del database sarà visibile nell'elenco database in tutto. Se si elimina il database durante il ripristino, esso verrà annullare l'operazione e non viene addebitata per il database che non è stato completato il ripristino. 

Per informazioni dettagliate sull'utilizzo di ripristinare in un momento per correggere gli errori utente e applicazione, vedere ripristino [momento](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Ripristino del database eliminati

Ripristino del database eliminati consente di ripristinare un database eliminato al tempo l'eliminazione di un database eliminato nello stesso server logici utilizzando [automatizzato di Database SQL di backup](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Se si elimina un'istanza server Database SQL Azure, tutti i database vengono eliminati anche e non può essere recuperati. Non è disponibile alcun supporto per il ripristino di un server eliminato al momento.

È possibile utilizzare la stessa o un nuovo nome di database per il database ripristinato. È possibile utilizzare il [portale di Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o [resto (createMode = ripristino)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Eliminare il ripristino del database: portale Azure](sql-database-restore-deleted-database-portal.md)
- [Eliminare il ripristino del database: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Ripristino geografico

Ripristino geografico consente di ripristinare un database SQL in qualsiasi server in un'area Azure dal più recente replicato geografico [automatizzato backup giornaliero](sql-database-automated-backups.md). Ripristino geografico utilizza una copia di backup ridondanti geografico come origine e può essere utilizzato per ripristinare un database, anche se il database o Data Center non è accessibile a causa di un'interruzione. È possibile utilizzare il [portale di Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), o [resto (createMode = ripristino)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Geografico Ripristina: Portale di Azure](sql-database-geo-restore-portal.md)
- [Ripristino geografico: PowerShell](sql-database-geo-restore-powershell.md)

Ripristino geografico è l'opzione di ripristino predefinito quando il database non è disponibile a causa di un evento imprevisto nell'area in cui il database è ospitato. Se un incidente su larga scala nei risultati di area geografica in indisponibilità dell'applicazione di database, è possibile utilizzare geografico Ripristina per ripristinare un database di backup più recente a un server in un'altra regione. Tutti i backup sono replicate geografico e può avere un ritardo tra quando il backup sia presa e geografico replicate nel Azure blob in un'area diversa. Il ritardo può essere fino a un'ora in modo che in caso di emergenza possono essere presenti su 1 ora la perdita di dati, ad esempio, rilasciato di fino a 1 ora. Di seguito viene illustrato il ripristino del database dall'ultimo giorno backup.

![ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)

Per informazioni dettagliate sull'utilizzo geografico Ripristina per ripristinare un'interruzione del servizio, vedere [recuperare da un'interruzione del servizio](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Ripristino geografico è disponibile con tutti i livelli di servizio, ma è più semplice le soluzioni di ripristino di emergenza disponibile nei Database di SQL con la più lunga rilasciato e stima ripristino ora (Inserisci). Per i database di base con dimensioni massime di 2 GB geografico-ripristino fornisce una soluzione DR accettabile con un Inserisci di 12 ore. Per i database Standard o Premium più grandi, se in modo significativo più breve tempi di ripristino sono desiderate o per ridurre il rischio di perdita di dati è consigliabile utilizzare la replica di geografico Active. La replica di geografico Active offre molto basso rilasciato e Inserisci come richiede solo per avviare un il controllo a continuamente replicato secondario. Per informazioni dettagliate, vedere [La replica di geografico Active](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Livello di programmazione ripristino tramite backup automatici

Come descritto in precedenza, addiition al portale di Azure, il ripristino del database può essere eseguito mediante un'applicazione in Azure PowerShell e le API REST. Nella tabella seguente sono descritti l'insieme di comandi disponibili.

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrizione|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Ottiene uno o più database.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Ottiene un database eliminate che è possibile ripristinare.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Ottiene una copia di backup ridondanti geografico di un database.|
|[Ripristino AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Ripristina un database SQL.|
||||

### <a name="rest-api"></a>API REST

|API|Descrizione|
|---|-----------|
|[RESTO (createMode = ripristino)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Ripristina un database|
|[Ottenere creare o aggiornare lo stato del Database](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Restituisce lo stato durante un'operazione di ripristino|
||||



## <a name="summary"></a>Riepilogo

Backup automatici proteggere i database da utente e gli errori dell'applicazione, l'eliminazione del database accidentali e prolungate interruzioni. Questa soluzione zero costo zero-amministrazione è disponibile con tutti i database SQL. 

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
