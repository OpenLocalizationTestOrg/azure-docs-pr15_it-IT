<properties
   pageTitle="Gestire le capacità di calcolo in SQL Azure Data Warehouse (panoramica) | Microsoft Azure"
   description="Scala di prestazioni le funzionalità di Warehouse di dati di SQL Azure. Scalabilità regolando DWUs o interrompere e riprendere risorse informatiche per risparmiare sui costi."
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
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gestire le capacità di calcolo in SQL Azure Data Warehouse (panoramica)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L'architettura di SQL Data Warehouse separa lo spazio di archiviazione e di calcolo, consentendo al scalare in modo indipendente. Di conseguenza, è possibile ridimensionare le prestazioni durante il salvataggio dei costi pagando solo per le prestazioni quando è necessario. 

In questo argomento descrive le seguenti funzionalità di scalabilità delle prestazioni di SQL Data Warehouse e offre suggerimenti su come e quando usarle. 

- Scala calcolare power regolando [dati warehouse unità (DWUs)][]
- Sospendere o riprendere risorse di elaborazione

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Prestazioni di scala

In SQL Data Warehouse, è possibile ridimensionare rapidamente le prestazioni out o indietro di aumentare o diminuire le risorse di calcolo della larghezza di banda i/o CPU e memoria. Per ridimensionare le prestazioni, è sufficiente è modificare il numero di [dati warehouse unità (DWUs)][] allocata SQL Data Warehouse al database. SQL Data Warehouse rapidamente apporta la modifica e gestisce tutte le modifiche sottostante al software o hardware.

Eliminato sono i giorni in cui è necessario cercare il tipo di processori, la quantità di memoria o il tipo di spazio di archiviazione è necessario che le prestazioni ottimali nel data warehouse. Inserendo un Data Warehouse nel cloud, è necessario non è più risolvere i problemi di hardware di livello inferiore. Se, tuttavia, SQL Data Warehouse chiede questa domanda: velocità con cui si desidera analizzare i dati? 

### <a name="how-do-i-scale-performance"></a>Le modalità di scalabilità delle prestazioni

Per illimitate aumentare o diminuire la capacità di calcolo, è sufficiente modificare l'impostazione [del data warehouse unità (DWUs)][] per il database. Prestazioni ne aumenterà lineare mentre si aggiungono ulteriori DWU.  Livelli superiori DWU, è necessario aggiungere più di 100 DWUs per osservare un notevole miglioramento delle prestazioni. Per informazioni su come selezionare ponticelli significativi in DWUs, sono disponibili i livelli DWU che consente di ottenere risultati ottimali.
 
Per regolare DWUs, è possibile usare uno di questi metodi singoli.

- [Scala calcolare power il portale di Azure][]
- [Scala calcolare power con PowerShell][]
- [Scala grande potenza con le API REST][]
- [Scala calcolare power con TSQL][]

### <a name="how-many-dwus-should-i-use"></a>Quanti DWUs è utile usare?
 
Prestazioni ottimali in SQL Data Warehouse scale lineare e la modifica da un computer a scala a un'altra (ad esempio da 100 DWUs a DWUs 2000) viene eseguita in secondi. In questo modo si sperimentare diverse impostazioni DWU fino a quando non si determina adatta dello scenario.

Per capire che cos'è il valore DWU ideale, provare proporzioni dei caratteri alto e verso il basso e l'esecuzione di query alcuni dopo il caricamento dei dati. Poiché il ridimensionamento è veloce, è possibile provare un numero di livelli diversi di prestazioni in un'ora o meno. Tenere presente che SQL Data Warehouse è progettato per elaborare grandi quantità di dati e visualizzare le relative funzionalità true per il ridimensionamento, in particolare in scala maggiore che offerti è consigliabile usare un set di dati di grandi dimensioni che si avvicina o è maggiore di 1 TB.

Suggerimenti per la ricerca DWU migliore per il carico di lavoro:

1. Per una data warehouse in fase di sviluppo, iniziare selezionando un numero limitato di DWUs.  Un valido punto di partenza è DW400 o DW200.
2. Monitorare le prestazioni dell'applicazione, osservare che il numero di DWUs selezionato rispetto alle prestazioni che osservano.
3. Determinare quantità prestazioni velocità superiore o inferiore devono essere per poter raggiungere il livello di prestazioni ottimali per le proprie esigenze, presupponendo scala lineare.
4. Aumentare o ridurre il numero di DWUs proporzionalmente come molto più velocemente o più lentamente desiderato il carico di lavoro da eseguire. Il servizio rispondere rapidamente e regolare le risorse di elaborazione per soddisfare i requisiti di DWU nuovi.
5. Continuare ad apportare modifiche fino a raggiungere un livello di prestazioni ottimali per esigenze aziendali.

### <a name="when-should-i-scale-dwus"></a>Quando è utile scalare DWUs?

Quando è necessario più velocemente dei risultati, aumentare il DWUs e pagare prestazioni più elevate.  Quando è necessario meno calcolare power, ridurre il DWUs, pagare solo per le informazioni necessarie. 

Consigli per scopi scalare DWUs:

1. Se l'applicazione ha un carico di lavoro variabili, scala DWU livelli alto o verso il basso per accogliere picchi e punti bassi. Ad esempio, se il carico di lavoro picchi in genere alla fine del mese, pianificare aggiungere ulteriori DWUs durante i giorni, quindi rimpicciolire dopo il periodo di punta.
2. Prima di eseguire un'operazione di caricamento o una trasformazione dati elevato, ingrandite DWUs in modo che i dati sono disponibili più rapidamente.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcolo pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per mettere in pausa un database, usare uno di questi metodi singoli.

- [Calcolo sospendere il portale di Azure][]
- [Calcolo pausa con PowerShell][]
- [Calcolo pausa con le API REST][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcolo curriculum

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per ripristinare un database, usare uno di questi metodi singoli.

- [Calcolo curriculum con Azure portale][]
- [Calcolo curriculum con PowerShell][]
- [Calcolo curriculum con le API REST][]

## <a name="permissions"></a>Autorizzazioni

Ridimensionamento del database richiederà autorizzazioni descritte nella [ALTER DATABASE][].  Sospendere e riprendere richiederà l'autorizzazione di [Collaboratori DB SQL][] , in particolare Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti consentono di acquisire familiarità con alcuni concetti di prestazioni chiave:

- [Gestione di carico di lavoro e della concorrenza][]
- [Cenni preliminari sulla progettazione di tabella][]
- [Distribuzione di tabelle][]
- [Indice della tabella][]
- [Partizione delle tabelle][]
- [Statistiche delle tabelle][]
- [Procedure consigliate][]

<!--Image reference-->

<!--Article references-->
[unità di magazzino dati (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Scala calcolare power il portale di Azure]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Scala calcolare power con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scala grande potenza con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scala calcolare power con TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Calcolo sospendere il portale di Azure]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Calcolo pausa con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Calcolo pausa con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Calcolo curriculum con Azure portale]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Calcolo curriculum con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Calcolo curriculum con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestione di carico di lavoro e della concorrenza]: ./sql-data-warehouse-develop-concurrency.md
[Cenni preliminari sulla progettazione di tabella]: ./sql-data-warehouse-tables-overview.md
[Distribuzione di tabelle]: ./sql-data-warehouse-tables-distribute.md
[Indice della tabella]: ./sql-data-warehouse-tables-index.md
[Partizione delle tabelle]: ./sql-data-warehouse-tables-partition.md
[Statistiche delle tabelle]: ./sql-data-warehouse-tables-statistics.md
[Procedure consigliate]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB collaboratori]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[LA MODIFICA DEL DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
