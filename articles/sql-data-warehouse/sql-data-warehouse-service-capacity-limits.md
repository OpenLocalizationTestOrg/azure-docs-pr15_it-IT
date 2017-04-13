<properties
   pageTitle="Limiti di capacità SQL Data Warehouse | Microsoft Azure"
   description="Valori massimi per le connessioni, database, tabelle e query SQL Data warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Limiti di capacità SQL Data Warehouse

Le tabelle seguenti contengono i valori massimo consentiti per diversi componenti di Data Warehouse di SQL Azure.


## <a name="workload-management"></a>Gestione di carico di lavoro

| Categoria            | Descrizione                                       | Massimo            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unità di magazzino dati (DWU)][]| Max DWU per un singolo SQL Data Warehouse | 6000               |
| [Unità di magazzino dati (DWU)][]| Max DWU per un singolo SQL server         | 6000 per impostazione predefinita<br/><br/> Per impostazione predefinita, ogni SQL server (ad esempio myserver.database.windows.net) dispone di una Quota di DTU di 45,000 che consente di DWU 6000. La quota è semplicemente un limite di sicurezza. È possibile aumentare la quota di [creazione di un ticket di supporto][] e selezionando *la Quota* come tipo di richiesta.  Per calcolare le DTU esigenze, moltiplicare il 7.5 totale che DWU necessari. È possibile visualizzare il consumo DTU corrente da e il server SQL nel portale. Database in pausa e riavviate contare verso la quota DTU. |
| Connessione al database | Sessioni aperte                          | 1024<br/><br/>È supportato un massimo di connessioni attive 1024, ognuno dei quali può inviare richieste a un database SQL Data Warehouse nello stesso momento. Si noti che non esistono limiti al numero di query che in realtà è possibile eseguire contemporaneamente. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna nel punto in cui è in attesa di elaborazione.|
| Connessione al database | Memoria massima per le istruzioni preparate            | 20 MB              |
| [Gestione di carico di lavoro][] | Numero massimo query simultanee                    | 32<br/><br/> Per impostazione predefinita, SQL Data Warehouse possono eseguire un massimo di 32 query simultanee e code rimanente query.<br/><br/>Quando gli utenti sono assegnati a una classe di risorse superiore o quando SQL Data Warehouse è configurato con DWU bassa è possibile ridurre il livello di concorrenza. Alcune query, ad esempio query DMV sempre è possibile eseguire.|
| [Tempdb][]          | Dimensione massima di Tempdb                                | 399 GB per DW100. Pertanto in Tempdb DWU1000 dimensioni corrispondenti a 3,99 TB |


## <a name="database-objects"></a>Oggetti di database

| Categoria          | Descrizione                                  | Massimo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Database          | Dimensioni massime                                     | 240 TB compressi su disco<br/><br/>Questo spazio è indipendente spazio tempdb o log e pertanto questo spazio si impegna costantemente per tabelle permanenti.  Compressione columnstore raggruppate viene stimata 5 X.  Questo tipo di compressione consente di aumento di circa 1 il database senza quando tutte le tabelle sono raggruppate columnstore (il tipo di tabella predefinito).|
| tavolo             | Dimensioni massime                                     | 60 TB compressi su disco   |
| tavolo             | Tabelle per database                          | 2 miliardi          |
| tavolo             | Colonne per tabella                            | 1024 colonne       |
| tavolo             | Byte per colonna                             | Dipendente dal [tipo di dati][]di colonna.  Limite è 8000 per i tipi di dati char, 4000 per nvarchar o 2 GB per i tipi di dati MAX.|
| tavolo             | Byte per ogni riga, dimensioni definite                  | 8060 byte<br/><br/>Il numero di byte per ogni riga viene calcolato nello stesso modo, come nel caso di SQL Server con la compressione di pagina. Ad esempio SQL Server, SQL Data Warehouse supporta l'archiviazione di overflow della riga che consente di **colonne di lunghezza variabile** il push disattivare riga. Quando le righe di lunghezza variabile vengono inserite disattivare riga, solo 24 byte radice viene archiviato nel record principale. Per ulteriori informazioni, vedere l'articolo MSDN [riversamento riga dati superiore a 8 KB][] .|
| tavolo             | Partizioni per tabella                    | 15.000<br/><br/>Per garantire prestazioni elevate, è consigliabile riducendo il numero delle partizioni necessarie durante ancora che supportano le esigenze aziendali. Man mano che aumenta il numero delle partizioni, il sovraccarico per le operazioni di definizione lingua DDL (Data) e (il Manipulation Language) che si sviluppa e genera un rallentamento delle prestazioni.|
| tavolo             | Caratteri per valore limite partizione.| 4000 |
| Indice             | Indici non cluster per tabella.        | 999<br/><br/>Si applica a solo tabelle rowstore.|
| Indice             | Indici cluster per tabella.            | 1<br><br/>Si applica alle tabelle rowstore e columnstore.|
| Indice             | Dimensione della chiave di indice.                          | 900 byte.<br/><br/>Si applica a solo gli indici rowstore.<br/><br/>Se i dati nelle colonne esistenti non superano 900 byte quando viene creato l'indice, è possono creare indici in colonne varchar con dimensioni massime di più di 900 byte. Tuttavia, inserire in un secondo momento o azioni di aggiornamento per le colonne che causano la dimensione totale a superare i 900 byte avrà esito negativo.|
| Indice             | Colonne chiave per indice.                   | 16<br/><br/>Si applica a solo gli indici rowstore. Gli indici cluster columnstore includono tutte le colonne.|
| Statistiche        | Dimensioni dei valori della colonna totale.      | 900 byte.         |
| Statistiche        | Colonne per ogni oggetto statistiche.           | 32                 |
| Statistiche        | Statistiche create per le colonne per tabella. | 30.000            |
| Stored procedure | Massimo livelli di annidamento.               | 8                 |
| Visualizzazione              | Colonne per ogni visualizzazione                         | 1.024             |


## <a name="loads"></a>Carica

| Categoria          | Descrizione                                  | Massimo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Carica Polybase    | Byte per ogni riga                                | 32.768<br/><br/>Carica Polybase è limitati per il caricamento di righe entrambe inferiori a 32 KB e non è possibile caricare VARCHR(MAX), nvarchar (max) o varbinary (max).  Durante questo limite esiste oggi, verrà rimosso abbastanza breve.<br/><br/>


## <a name="queries"></a>Query

| Categoria          | Descrizione                                  | Massimo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Query             | In coda query su tabelle utente.               | 1000               |
| Query             | Query simultanee per le viste di sistema.          | 100                |
| Query             | In coda query sulle viste di sistema               | 1000               |
| Query             | Parametri massimo                           | 2098               |
| Batch             | Dimensioni massime                                 | 65, 536 * 4096        |
| Seleziona risultati    | Colonne per riga                              | 4096<br/><br/>Non è possibile avere più di 4096 colonne per ogni riga nel risultato della Seleziona. Non ci sono garanzie che è possibile disporre sempre 4096. Se il piano di query richiede una tabella temporanea, 1024 colonne per tabella massima applicabili.|
| SELEZIONARE            | Sottoquery annidate                            | 32<br/><br/>Non è possibile avere più di 32 sottoquery annidata in un'istruzione SELECT. Non ci sono garanzie che è possibile disporre sempre 32. Ad esempio un JOIN può comportare una sottoquery nel piano della query. Il numero di una sottoquery può anche essere limitato dalla memoria disponibile.|
| SELEZIONARE            | Colonne per ogni JOIN                             | 1024 colonne<br/><br/>Non è possibile avere più di 1024 colonne del join. Non ci sono garanzie che è possibile disporre sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne che il risultato JOIN, il limite di 1024 si applica la tabella temporanea. |
| SELEZIONARE            | Byte per RAGGRUPPA per colonne.                  | 8060<br/><br/>Le colonne all'interno della clausola GROUP BY possono avere un massimo di 8060 byte.|
| SELEZIONARE            | Byte per colonne ORDER BY                   | 8060 byte.<br/><br/>Le colonne all'interno della clausola ORDER BY possono avere un massimo di 8060 byte.|
| Identificatori e costanti per ogni istruzione | Numero di costanti e identificatori di cui si fa riferimento. | 65.535<br/><br/>SQL Data Warehouse limita il numero di identificatori e costanti che possono essere contenute in un'espressione di una query. Questo limite è 65.535. Superiore a questo numero generi errore di SQL Server 8632. Per ulteriori informazioni, vedere [errore interno: è stato raggiunto un limite di servizi di espressione][].|


## <a name="metadata"></a>Metadati

| Visualizzazione di sistema                        | Numero massimo di righe |
| :--------------------------------- | :------------|
| Sys.dm_pdw_component_health_alerts | 10.000       |
| Sys.dm_pdw_dms_cores               | 100          |
| Sys.dm_pdw_dms_workers             | Numero totale di colleghi DMS per 1000 più recente richieste SQL. |
| Sys.dm_pdw_errors                  | 10.000       |
| Sys.dm_pdw_exec_requests           | 10.000       |
| Sys.dm_pdw_exec_sessions           | 10.000       |
| Sys.dm_pdw_request_steps           | Numero totale di passaggi per le richieste SQL 1000 più recenti vengono memorizzati in sys.dm_pdw_exec_requests. |
| Sys.dm_pdw_os_event_logs           | 10.000       |
| Sys.dm_pdw_sql_requests            | Le richieste più recenti 1000 SQL archiviati in sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Panoramica di riferimento SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Unità di magazzino dati (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Panoramica di riferimento SQL Data Warehouse]: ./sql-data-warehouse-overview-reference.md
[Gestione di carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[tipo di dati]: ./sql-data-warehouse-tables-data-types.md
[creazione di un ticket di supporto]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Dati di Overflow delle righe che superano 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Errore interno: è stato raggiunto un limite di servizi di espressioni]: https://support.microsoft.com/kb/913050
