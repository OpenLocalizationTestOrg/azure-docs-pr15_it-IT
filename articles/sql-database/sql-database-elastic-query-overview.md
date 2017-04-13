<properties
    pageTitle="Panoramica di query di database flessibile Database SQL Azure | Microsoft Azure"
    description="Panoramica della funzione query flessibile"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Panoramica di query di Azure Database SQL database flessibile (preview)

La funzionalità di query di database flessibile (nella versione di anteprima) consente di eseguire una query Transact-SQL che si estende su più database nel Database di SQL Azure (SQLDB). Consente di eseguire query tra database per accedere a tabelle remote e connettersi strumenti Microsoft e terze parti (Excel, ottenere informazioni, Tableau, ecc.) per eseguire query su livelli di dati con più database. Questa caratteristica, è possibile scalare query ai livelli di dati di grandi dimensioni nel Database di SQL e visualizzare i risultati nei report di business intelligence (BI).

## <a name="documentation"></a>Documentazione

* [Guida introduttiva a query tra database](sql-database-elastic-query-getting-started-vertical.md)
* [Rapporto tra il database fuori architetture cloud](sql-database-elastic-query-getting-started.md)
* [Query su database cloud sharded (suddivisi in senso orizzontale)](sql-database-elastic-query-horizontal-partitioning.md)
* [Query su database cloud con schemi diversi (suddivisi in verticale)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_eseguire \_remoto](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Perché usare le query flessibile?

**Database SQL Azure**

Query su database di SQL Azure completamente in Transact-SQL. In questo modo di sola lettura le query di database remoti. È disponibile per i clienti di SQL Server locale correnti eseguire la migrazione di applicazioni che utilizzano nomi tre e four parziale o un server collegato al database SQL.

**Disponibile nel livello standard** Query flessibile è supportato a livello di prestazioni Standard oltre il livello di prestazioni Premium. Vedere la sezione sui limiti di anteprima sotto sui limiti delle prestazioni per i livelli di prestazioni inferiori.

**Push a database remoti**

Flessibile query può ora inserire parametri SQL per i database remoti per l'esecuzione.

**Esecuzione di stored procedure**

Esecuzione di chiamate di remote stored procedure o funzioni remote utilizzando [sp\_eseguire \_remoto](https://msdn.microsoft.com/library/mt703714).

**Flessibilità**

Tabelle esterne con query flessibile ora possono fare riferimento a tabelle remote con un nome di tabella o un altro schema.

## <a name="elastic-database-query-scenarios"></a>Scenari di query di database flessibile

L'obiettivo è agevola la ricerca gli scenari in cui più database fornire le righe in un singolo risultato complessivo. La query può essere composti sia dall'utente o dall'applicazione direttamente o indirettamente tramite strumenti connessi al database. Questa operazione è particolarmente utile quando si creano report utilizzando strumenti di integrazione di BI o dati commerciali, o in qualsiasi applicazione che non possa essere modificate. Con una query di flessibile, è possibile eseguire query su più database utilizzando la familiare esperienza di connettività di SQL Server negli strumenti, ad esempio Excel, ottenere informazioni, Tableau o Cognos.
Una query flessibile consente di accedere facilmente a un'intera raccolta di database tramite query rilasciato da SQL Server Management Studio o Visual Studio e semplifica l'esecuzione di query tra database da Framework entità o in altri ambienti ORM. Figura 1 mostra uno scenario in un'applicazione cloud esistente, che utilizza la [libreria client database flessibile](sql-database-elastic-database-client-library.md), si basa su un livello di dati non di architetture e una query flessibile viene utilizzata per i report tra database.

**Figura 1** Query di database flessibile utilizzata nel livello di dati non in scala

![Query di database flessibile utilizzata nel livello di dati non in scala][1]

Scenari per query flessibile sono caratterizzati dalle seguenti:

* **Partizione verticale: query tra database** (Topologia 1): I dati sono suddiviso in verticale tra un numero di database in un livello di dati. In genere diversi insiemi di tabelle si trovano sul database diversi. Ciò significa che lo schema è diverso sui database diversi. Ad esempio, tutte le tabelle per inventario sono su un database mentre tutte le tabelle correlate contabilità in un secondo database. Casi di utilizzo comune con questa topologia richiedono una per eseguire una query tra o per la compilazione di relazioni tra tabelle in diversi database.
* **Partizione orizzontale: Sharding** (Topologia 2): dati sono suddiviso in senso orizzontale per la distribuzione di righe in una scalate fuori dati livello. Con questo approccio, lo schema è identico su tutti i partecipanti database. Questo approccio è l'acronimo di "sharding". Sharding può essere eseguita e gestiti mediante (1) il database flessibile strumenti raccolte o self-sharding (2). Una query flessibile viene utilizzata per query o la compilazione di relazioni tra molti shards.

> [AZURE.NOTE] Query di database flessibile è ideale per gli scenari di creazione di report occasionali nel punto in cui la maggior parte dell'elaborazione possono essere eseguita il livello di dati. Per carichi di lavoro dei report o scenari di warehouse dati con le query più complesse, anche considerare [Warehouse di dati di SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Flessibile topologie di query di Database

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologia 1: Partizione verticale: query tra database

Per iniziare la codifica, vedere [Guida introduttiva alla query tra database (partizione verticale)](sql-database-elastic-query-getting-started-vertical.md).

Per rendere i dati presenti in un database SQLDB disponibile per altri database SQLDB, è possibile utilizzare una query flessibile. In questo modo le query da un database di fare riferimento a tabelle in qualsiasi altro database SQLDB remoto. Il primo passaggio consiste nel definire un'origine dati esterna per ogni database remoto. L'origine dati esterna è definita nel database locale da cui si desidera accedere alle tabelle presenti nel database remoto. Non sono necessarie modifiche nel database remoto. Per tipici verticali partizioni scenari in cui database diversi schemi diversi, query flessibile possono essere utilizzate per implementare casi di utilizzo comune, ad esempio l'accesso ai dati di riferimento e invio di query tra database.

**Dati di riferimento**: 1 topologia viene utilizzato per la gestione dei dati di riferimento. Nella figura seguente vengono mantenute due tabelle con dati di riferimento (T1 e T2) in un database dedicato. Utilizzo di una query flessibile, è ora possibile accedere tabelle T1 e T2 in modalità remota da altri database, come illustrato nella figura. Utilizzo della topologia 1 se le tabelle di riferimento sono piccole o remote query nella tabella di riferimento hanno predicati selettive.

**Figura 2** Verticale partizione - uso dei dati di riferimento delle query alla query flessibile

![Verticale partizione - uso dei dati di riferimento delle query alla query flessibile][3]

**Invio di query tra database**: elastici query Abilita i casi di utilizzo che richiedono query tra diversi database SQLDB. La figura 3 mostra quattro diversi database: CRM, inventario, risorse Umane e prodotti. Query eseguita in uno dei database anche necessario l'accesso a uno o tutti gli altri database. Utilizzo di una query flessibile, è possibile configurare il database per questo caso, eseguire alcune semplici istruzioni DDL su ciascuna delle quattro database. Dopo questa configurazione tantum, accedere a una tabella remota è semplice come riferimento a una tabella locale da una query SQL T o da strumenti di Business Intelligence. Questo approccio è consigliato se la query remote non vengono restituiti risultati di grandi dimensioni.

**Figura 3** Verticale partizione - utilizzando elastici query alla query su database diversi

![Verticale partizione - utilizzando elastici query alla query su database diversi][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topologia 2: Partizione orizzontale: sharding

Utilizzo di query flessibile per eseguire attività di creazione di report su sharded, vale a dire, orizzontalmente suddiviso, livello dati richiede una [mappa condiviso database flessibile](sql-database-elastic-scale-shard-map-management.md) per rappresentare i database del livello dati. In genere, solo una mappa singolo condiviso viene utilizzata in questo scenario e un database dedicato con funzionalità di query flessibile serve come punto di ingresso per i report di query. Solo questo database dedicato richiede l'accesso alla mappa condiviso. La figura 4 illustra questa topologia e la configurazione con la mappa di database e condiviso query flessibile. I database nel livello di dati possono essere di tutte le versioni di Database SQL Azure o edition. Per ulteriori informazioni sulla raccolta flessibile database client e la creazione di mappe condiviso, vedere [eseguire il mapping di gestione condiviso](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Orizzontale partizione - utilizzo di query flessibile per i report su livelli sharded dati

![Orizzontale partizione - utilizzo di query flessibile per i report su livelli sharded dati][5]

> [AZURE.NOTE] Il database di query di database flessibile dedicato deve essere un database di v12 DB SQL. Non esistono restrizioni in shards se stessi.

Per iniziare la codifica, vedere [Guida introduttiva a una query di database flessibile per orizzontale partizione (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Implementazione delle query di database flessibile

Nelle sezioni seguenti vengono descritti i passaggi per implementare query flessibile per gli scenari partizioni orizzontali e verticali. Sono inoltre fare riferimento alla documentazione più dettagliata per i diversi scenari partizioni.

### <a name="vertical-partitioning---cross-database-queries"></a>Partizione verticale - query tra database

La procedura seguente configura le query di database flessibile scenari partizione verticale che richiedono l'accesso a una tabella nel database SQLDB remoti con lo stesso schema:

*    [Creare chiave MASTER](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Creare DATABASE nell'ambito CREDENZIALI](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [Crea/elenco a discesa origine dati esterna](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource di tipo **RDBMS**
*    [Tabella esterna TRASCINAMENTO crea](https://msdn.microsoft.com/library/dn935021.aspx) tabella

Dopo avere eseguito le istruzioni DDL, è possibile accedere alla tabella remota "tabella" come se si trattasse di una tabella locale. Database SQL Azure automaticamente apre una connessione al database remoto, elaborerà la richiesta nel database remoto e restituisce i risultati.
Sono disponibili ulteriori informazioni sui passaggi necessari per lo scenario partizione verticale nella [query flessibile per partizione verticale](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Partizione orizzontale - sharding

La procedura seguente configura le query di database flessibile per orizzontale partizioni gli scenari che richiedono l'accesso a un insieme di tabella che si trovano su (in genere) database SQLDB remoti diversi:

*    [Creare chiave MASTER](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Creare DATABASE nell'ambito CREDENZIALI](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Creare una [mappa condiviso](sql-database-elastic-scale-shard-map-management.md) che rappresenta il livello di dati tramite la libreria di client database flessibile.   
*    [Crea/elenco a discesa origine dati esterna](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource di tipo **SHARD_MAP_MANAGER**
*    [Tabella esterna TRASCINAMENTO crea](https://msdn.microsoft.com/library/dn935021.aspx) tabella

Dopo aver eseguito questa procedura, è possibile accedere alla tabella partizionata orizzontali "tabella" come se si trattasse di una tabella locale. Database SQL Azure automaticamente apre più connessioni in parallelo a database remoti posizione fisica le tabelle, elabora richieste sui database remoti e restituisce i risultati.
Sono disponibili ulteriori informazioni sui passaggi necessari per lo scenario partizione orizzontale nella [query flessibile per partizione orizzontale](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Invio di query T-SQL
Dopo aver definito le origini dati esterne e le tabelle esterne, è possibile utilizzare normali stringhe di connessione di SQL Server per connettersi ai database in cui è stato definito le tabelle esterne. È possibile eseguire istruzioni T-SQL tramite le tabelle esterne nella connessione con limitazioni descritte di seguito. È possibile trovare ulteriori informazioni ed esempi di query T-SQL negli argomenti della documentazione per [partizione orizzontale](sql-database-elastic-query-horizontal-partitioning.md) dei [verticale](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Connettività di strumenti
È possibile utilizzare normali stringhe di connessione di SQL Server per connettere le applicazioni e strumenti di integrazione di Business Intelligence e dati ai database che dispongono di tabelle esterne. Assicurarsi che sia supportato SQL Server come origine dati per lo strumento. Una volta connessa, fare riferimento al database di query flessibile e le tabelle esterne in tale database come si farebbe con qualsiasi altro database di SQL Server a cui ci si connette con lo strumento.

> [AZURE.IMPORTANT] Autenticazione tramite Azure Active Directory con le query flessibile non è attualmente supportato.

## <a name="cost"></a>Costo

Query flessibile è incluso nel costo del database di SQL Azure. Si noti che nel punto in cui i database remoti sono disponibili in un centro di dati diversi rispetto all'endpoint query flessibile topologie sono supportate ma uscita dati dai database remoti addebitate normali [tariffe Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitazioni relative all'anteprima
* Esecuzione della prima query flessibile può richiedere alcuni minuti a livello di prestazioni Standard. In questo caso è necessario caricare le funzionalità di query flessibile. prestazioni di caricamento migliorano con livelli superiori di prestazioni.
* Script di origini dati esterne o tabelle esterne da SQL Server Management Studio o SSDT non è ancora supportato.
* Importazione/esportazione per SQL DB non supporta ancora le origini dati esterne e tabelle esterne. Se è necessario utilizzare importazione/esportazione, eliminare questi oggetti prima di esportare e quindi ricrearle dopo l'importazione.
* Query di database flessibile supporta attualmente solo accesso in sola lettura di tabelle esterne. Tuttavia, è possibile, utilizzare tutte le funzionalità Transact-SQL sul database in cui è definita la tabella esterna. Può essere utile per mantenere risultati temporanei utilizzando, ad esempio, ad esempio, l'opzione < column_list > < local_table > o per definire stored procedure nel database di query flessibile che fanno riferimento a tabelle esterne.
* Ad eccezione di nvarchar (max), Line tipi non supportati le definizioni di tabelle esterne. In alternativa, è possibile creare una vista nel database remoto che esegue il cast il tipo di line in nvarchar (max), definire le tabelle esterne sulla vista anziché la tabella di base e quindi eseguirne il cast nel tipo line originale nelle query.
* Le statistiche di colonna su tabelle esterne non sono attualmente supportate. Statistiche tabelle sono supportate, ma è necessario creare manualmente.

## <a name="feedback"></a>Commenti e suggerimenti
Per condividere commenti e suggerimenti sull'esperienza dell'utente con query flessibile con noi Disqus riportata di seguito, forum MSDN o Stackoverflow. Si è interessati in tutti i tipi di commenti e suggerimenti sul servizio (difetti, bordi irregolari, spazi vuoti caratteristica).

## <a name="more-information"></a>Ulteriori informazioni

Sono disponibili ulteriori informazioni sull'esecuzione di query tra database e scenari partizioni verticali nei documenti seguenti:

* [Esecuzione di query tra database e panoramica partizione verticale](sql-database-elastic-query-vertical-partitioning.md)
* Provare le esercitazioni pratiche per è un esempio di lavoro in esecuzione in minuti: [Guida introduttiva alla query tra database (partizione verticale)](sql-database-elastic-query-getting-started-vertical.md).


Ulteriori informazioni su orizzontale scenari suddivisione e sharding sono disponibili qui:

* [Panoramica di suddivisione e sharding orizzontale](sql-database-elastic-query-horizontal-partitioning.md)
* Provare le esercitazioni pratiche per è un esempio di lavoro in esecuzione in minuti: [Guida introduttiva a una query di database flessibile per orizzontale partizione (sharding)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
