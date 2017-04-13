<properties
   pageTitle="Backup SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sui backup di database incorporati SQL Data Warehouse che consentono di ripristinare un Data Warehouse SQL Azure in un punto di ripristino o un'area geografica diversa."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Backup SQL Data Warehouse

SQL Data Warehouse offre backup locali e geografico come parte delle funzionalità di backup warehouse dati. Ad esempio snapshot Azure lo spazio di archiviazione Blob e lo spazio di archiviazione ridondanti geografico. Utilizzare i dati warehouse backup per ripristinare il data warehouse in un punto di ripristino nell'area principale o ripristinare un'area geografica diversa. Questo articolo illustra le specifiche del backup in SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Che cos'è una copia di backup warehouse dati?

Una copia di backup warehouse dati contiene i dati che è possibile utilizzare per ripristinare un data warehouse in un'ora specifica.  Poiché SQL Data Warehouse è un sistema distribuito, una copia di backup warehouse dati è costituito da molti file archiviati in BLOB Azure. 

Il backup del database sono un elemento essenziale di qualsiasi strategia di ripristino emergenza e continuità aziendale perché sono proteggere i dati da danni accidentali o l'eliminazione. Per ulteriori informazioni, vedere [Panoramica di continuità aziendale](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Ridondanza dei dati

SQL Data Warehouse consente di proteggere i dati per la memorizzazione dei dati in locale ridondanti (LRS) Azure Premium archiviazione. Questa caratteristica di archiviazione Azure archivia icona del copie dei dati nell'interfaccia di dati locali per garantire la protezione dei dati trasparente se sono presenti errori localizzati. La ridondanza dei dati garantisce che i dati possono superare i problemi dell'infrastruttura, ad esempio errori del disco. La ridondanza dei dati garantisce continuità aziendale con una tolleranza e infrastruttura di disponibilità.

Per ulteriori informazioni su:

- Archiviazione Premium Azure, vedere [Introduzione all'archiviazione Premium Azure](../storage/storage-premium-storage.md).
- Spazio di archiviazione ridondante in locale, vedere [replica di archiviazione Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Snapshot di spazio di archiviazione Blob Azure

Come dei vantaggi dell'utilizzo dello spazio di archiviazione di Azure Premium, SQL Data Warehouse utilizza istantanee Azure lo spazio di archiviazione Blob per eseguire il backup del data warehouse in locale. È possibile ripristinare un data warehouse a un punto di ripristino snapshot. Snapshot avviare un minimo di ogni otto ore e sono disponibili per sette giorni.  

Per ulteriori informazioni su:

- Snapshot blob Azure, vedere [creare uno snapshot blob](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Backup geografico ridondanti

Ogni 24 ore, SQL Data Warehouse archivia completo data warehouse in archiviazione Standard. Intero data warehouse viene creata in modo che corrisponda l'ora dell'ultima istantanea. Lo spazio di archiviazione standard appartiene a un account di archiviazione ridondanti geografico con accesso in lettura (RA GRS). La caratteristica di Azure archiviazione RA-GRS replica della copia di backup in un [centro dati accoppiate](../best-practices-availability-paired-regions.md). La replica geografico garantisce che sarà possibile ripristinarli in magazzino dati nel caso in cui non è possibile accedere agli snapshot nella propria area geografica principale. 

Questa funzione è attivata per impostazione predefinita. Se non si desidera utilizzare i backup geografico ridondanti, è possibile rifiutare esplicitamente di partecipare. 

>[AZURE.NOTE] Nel sistema di archiviazione Azure, il termine *replica* fa riferimento alla copia di file da una posizione a un'altra. SQL *replica di database* fa riferimento a tenere a più database secondari sincronizzati con un database principale. 

Per ulteriori informazioni su:
- Spazio di archiviazione ridondanti geografico, vedere [replica di archiviazione Azure](../storage/storage-redundancy.md).
- Spazio di archiviazione RA GRS, vedere [lo spazio di archiviazione ridondanti geografico di accesso in lettura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Data warehouse di pianificazione di backup e il periodo di conservazione

SQL Data Warehouse crea istantanee in warehouse dati online ogni quattro a 8 ore e mantiene ogni snapshot per sette giorni. È possibile ripristinare il database online a uno dei punti di ripristino negli ultimi sette giorni. 

Per visualizzare all'avvio l'ultima istantanea, eseguire la query in SQL Data Warehouse online. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se è necessario mantenere uno snapshot per più di sette giorni, è possibile ripristinare un punto di ripristino di un nuovo data warehouse. Al termine dell'operazione di ripristino, SQL Data Warehouse inizia a creare istantanee nel nuovo data warehouse. Se si non apportare modifiche al nuovo data warehouse, le istantanee rimangono vuote e pertanto il costo snapshot minimo. È inoltre possibile sospendere il database per impedire l'esecuzione di istantanee SQL Data Warehouse.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Cosa succede ai criteri di conservazione backup mentre è in pausa la warehouse dati?

SQL Data Warehouse non crea snapshot e non scada mai istantanee mentre un data warehouse è sospeso. L'età snapshot non viene modificato mentre è sospeso data warehouse. Conservazione snapshot è in base al numero di giorni che vanno che data warehouse è online, non i giorni di calendario.

Ad esempio, se uno snapshot inizia il 1 ° ottobre 4 ore e data warehouse è sospeso 3 ottobre PM 4, immagine da due giorni. Ogni volta che il data warehouse ritorna in linea snapshot da due giorni. Se data warehouse online 5 ottobre PM 4, immagine da due giorni e rimane per cinque più giorni.

Se si torna online data warehouse, SQL Data Warehouse riprende nuovi snapshot e scadenza istantanee che hanno più di sette giorni di dati.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Quanto tempo è il periodo di conservazione per un deposito di rilascio di dati?
Quando si elimina un data warehouse, data warehouse e le istantanee sono salvate per sette giorni e rimosse. È possibile ripristinare data warehouse a uno dei punti di ripristino salvata.

> [AZURE.IMPORTANT] Se si elimina un'istanza di SQL server logica, tutti i database che appartengono all'istanza vengono eliminati anche e non possono essere recuperati. Non è possibile ripristinare un server eliminato.

## <a name="data-warehouse-backup-costs"></a>Costi di backup warehouse di dati

Il costo totale per il warehouse dati principale e sette giorni di snapshot Blob Azure viene arrotondato al più vicino TB. Ad esempio, se la data warehouse è 1,5 TB e gli snapshot utilizzano 100 GB, vengono addebitate per 2 TB di dati in base ai tassi lo spazio di archiviazione di Azure Premium. 

>[AZURE.NOTE] Ogni snapshot non contiene alcun dato inizialmente e cresce quando si apportano modifiche a primaria data warehouse. Tutte le istantanee aumentano le dimensioni come le modifiche di warehouse di dati. Di conseguenza, i costi di spazio di archiviazione per le istantanee adatta in base alla frequenza di modifica.

Se si usa lo spazio di archiviazione ridondanti geografico, si riceve un addebito archiviazione separata. Lo spazio di archiviazione ridondanti geografico viene effettuata alla tariffa standard di accesso in lettura limitazioni geografiche ridondanti dello spazio di archiviazione (RA GRS).

Per ulteriori informazioni sui prezzi SQL Data Warehouse, vedere [SQL dati Warehouse prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Utilizzo di backup del database

Lo scopo principale per i backup warehouse di dati SQL è possibile ripristinare data warehouse a uno dei punti di ripristino entro il periodo di conservazione.  

- Per ripristinare un data warehouse SQL, vedere [ripristinare un data warehouse SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Argomenti correlati

### <a name="scenarios"></a>Scenari

- Per una panoramica di continuità aziendale, vedere [Panoramica di continuità aziendale](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Per ripristinare un data warehouse, vedere [ripristinare un data warehouse SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

