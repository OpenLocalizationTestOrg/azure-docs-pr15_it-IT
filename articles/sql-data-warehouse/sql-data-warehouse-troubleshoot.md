<properties
   pageTitle="Risoluzione dei problemi di SQL Azure Data Warehouse | Microsoft Azure"
   description="Risoluzione dei problemi di SQL Azure Data Warehouse."
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
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Risoluzione dei problemi di SQL Azure Data Warehouse

In questo argomento vengono elencate alcune domande sulla risoluzione dei problemi più comuni che è ascoltare dai clienti.

## <a name="connecting"></a>La connessione

| Problema                              | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Impossibile eseguire l'accesso per utente "NT \ accesso". (Microsoft SQL Server, errore: 18456) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente in master.  Per risolvere questo problema specificare SQL Data Warehouse che si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Vedere l'articolo [informazioni generali sulla protezione][] per altri dettagli.|
|Il server principale "nomeutente" non è in grado di accedere al database "master" nel contesto di protezione corrente. Non è possibile aprire il database utente predefinito. Impossibile eseguire l'accesso. Impossibile eseguire l'accesso per utente 'NomeUtente'. (Microsoft SQL Server, errore: 916) | Questo errore si verifica quando un utente AAD tenta di connettersi al database master, ma non dispone di un utente in master.  Per risolvere questo problema specificare SQL Data Warehouse che si desidera connettersi al momento della connessione o aggiungere l'utente al database master.  Vedere l'articolo [informazioni generali sulla protezione][] per altri dettagli.|
| Errore CTAIP                        | Questo errore può verificarsi quando un account di accesso è stato creato il database master di SQL server, ma non nel database di SQL Data Warehouse.  Se si verifica questo errore, consultare l'articolo [informazioni generali sulla protezione][] .  In questo articolo viene illustrato come creare creare un account di accesso e utente nel master e quindi su come creare un utente nel database di SQL Data Warehouse.|
| Bloccato da Firewall                |Database SQL Azure sono protetti da firewall di livello server e database per assicurarsi che solo noti indirizzi IP hanno accesso a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario attivare esplicitamente e indirizzo IP o intervallo di indirizzi per potersi connettere.  Per configurare il firewall per l'accesso, seguire i passaggi di [configurazione dell'accesso firewall server per l'indirizzo IP client][] nelle [istruzioni di Provisioning][].|
| Non è possibile connettersi con strumento o driver | SQL Data Warehouse consiglia [SQL Server Management Studio][], [SSDT per Visual Studio 2015][]o [sqlcmd][] per i dati della query. Per ulteriori informazioni sulle dipendenze e la connessione a SQL Data Warehouse, vedere gli articoli [driver per archivio di dati di SQL Azure][] e [connettere nell'archivio di dati di SQL Azure][] .|


## <a name="tools"></a>Strumenti

| Problema                              | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Risorse di Visual Studio oggetto manca utenti AAD | Si tratta di un problema noto.  In alternativa, visualizzare gli utenti in [sys.database_principals][].  Vedere [autenticazione nell'archivio di dati di SQL Azure][] per ulteriori informazioni sull'utilizzo di Azure Active Directory con SQL Data Warehouse.|

## <a name="performance"></a>Prestazioni

|  Problema                             | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Risoluzione dei problemi di prestazioni di query  | Se si sta tentando di risolvere i problemi di una query specifica, iniziare con [imparare a controllare le query][].|
| Piani e le prestazioni delle query spesso sono il risultato di statistiche mancanti   | Le cause più comuni della lentezza è mancanza di statistiche per le tabelle.  Visualizzare [le statistiche tabella mantenimento] [ Statistics] per informazioni dettagliate su come creare statistiche e perché sono fondamentali per le prestazioni.|
| Bassa concorrenza / accodare query   | Informazioni sulla [gestione di carico di lavoro][] è importante per imparare a saldo totale allocazione di memoria della concorrenza.|
| Come implementare le procedure consigliate    | I migliori posizionare per iniziare a informazioni su come migliorare le prestazioni delle query [SQL Data Warehouse procedure consigliate][] articolo.|
| Come migliorare le prestazioni con proporzioni dei caratteri  | Può succedere che la soluzione ideale per migliorare le prestazioni sia semplicemente aggiungere che altre calcolare power alle query modificando [La Data Warehouse SQL][].|
| Prestazioni delle query in seguito qualità insufficiente della indice | Alcune volte query possibile rallentamento a causa di [columnstore scarsa qualità di indice][].  Vedere l'articolo per ulteriori informazioni e su come [ricostruire gli indici per migliorare la qualità di segmento][].|

## <a name="system-management"></a>Gestione del sistema

|  Problema                             | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Msg 40847: Impossibile eseguire l'operazione perché server sarebbe superamento della quota di unità di transazione Database consentita di 45000. | Ridurre [DWU][] del database che si vuole creare o [richiedere un aumento di quota][].|
| In analisi di utilizzo dello spazio    | Visualizzare [le dimensioni di tabella][] per comprendere l'utilizzo dello spazio del sistema.|
| Informazioni sulla gestione delle tabelle          | Vedere la [tabella Panoramica] [ Overview] articolo per informazioni sulla gestione delle tabelle.  In questo articolo include anche i collegamenti in argomenti più dettagliati, ad esempio [tipi di dati di tabella][Data types], [la distribuzione di una tabella][Distribute], [l'indicizzazione di una tabella][Index], [partizione di una tabella][Partition], [le statistiche delle tabelle mantenimento] [ Statistics] e [tabelle temporanee][Temporary].|

## <a name="polybase"></a>Polybase

|  Problema                             | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Errore UTF-8                        |  Attualmente PolyBase supporta solo il caricamento di file di dati che hanno avuto la codifica UTF-8.  Per indicazioni su come aggirare questa limitazione, vedere [utilizzo intorno requisito PolyBase UTF-8][] .|
| Caricamento non riesce a causa di grandi dimensioni righe   | Supporto di grandi dimensioni riga non è attualmente disponibile per Polybase.  Di conseguenza, se la tabella contiene varchar (max), nvarchar (max) o varbinary (max), tabelle esterne non possono essere utilizzate per caricare i dati.  Caricamento per le righe di grandi dimensioni è attualmente supportato solo tramite Azure Data Factory (con BCP), Azure flusso Analitica, SSIS, BCP o la classe .NET SQLBulkCopy. Supporto PolyBase per le righe di grandi dimensioni verrà aggiunto nelle versioni future.|
| Errore bcp caricamento della tabella con tipo di dati MAX | Esiste un problema noto che richiede che alla fine della tabella in alcuni casi si trovino varchar (max), nvarchar (max) o varbinary (max).  Provare a spostare il numero massimo di colonne alla fine della tabella.|

## <a name="differences-from-sql-database"></a>Differenze rispetto al Database SQL

|  Problema                             | Risoluzione                                      |
| :----------------------------------| :---------------------------------------------- |
| Caratteristiche di Database SQL non supportate  | Vedere [caratteristiche delle tabelle non supportate][].|
| Tipi di dati di Database SQL non supportati  | Vedere [tipi di dati non supportati][].|
| Elimina e limitazioni relative all'aggiornamento      | Vedere [soluzioni di aggiornamento][], [eliminazione soluzioni alternative][] e [Utilizzando CTAS aggirare sintassi non supportata di aggiornamento ed eliminazione][].  |
| Istruzione MERGE non è supportato   | Vedere [Stampa unione soluzioni alternative][].|
| Limitazioni della stored procedure       | Vedere [Stored procedure limitazioni][] conoscere alcune delle limitazioni della stored procedure.|
| Funzioni definite dall'utente non supportano le istruzioni SELECT | Si tratta di un limite corrente del nostro funzioni definite dall'utente.  Vedere [Creazione di funzioni][] per la sintassi che è supportato.   |

## <a name="next-steps"></a>Passaggi successivi

Se si riesce a trovare una soluzione al problema riscontrato sopra, ecco alcune altre risorse è possibile provare.

- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog del team gatto]
- [Creare ticket di supporto]
- [Forum MSDN]
- [Forum di Overflow dello stack]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Cenni preliminari sulla sicurezza]: ./sql-data-warehouse-overview-manage-security.md
[SQL SERVER MANAGEMENT STUDIO]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT per Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Driver di SQL Azure Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connettersi a SQL Azure Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Creare ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Ridimensionamento i Data Warehouse SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[richiedere un aumento di quota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Imparare a controllare le query]: ./sql-data-warehouse-manage-monitor.md
[Istruzioni di provisioning]: ./sql-data-warehouse-get-started-provision.md
[Configurare l'accesso firewall server per l'indirizzo IP client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Procedure consigliate SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Dimensioni tabella]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Caratteristiche delle tabelle non supportate]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipi di dati non supportate]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Columnstore scarsa qualità di indice analitico]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rigenerare gli indici per migliorare la qualità di segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gestione di carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Utilizzo di CTAS per ovviare sintassi non supportata di aggiornamento ed eliminazione]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[AGGIORNARE soluzioni alternative]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ELIMINARE soluzioni alternative]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[UNIRE soluzioni alternative]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitazioni della stored procedure]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticazione di SQL Azure Data warehouse]: ./sql-data-warehouse-authentication.md
[Aggirare requisito PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[Sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREARE UNA FUNZIONE]: https://msdn.microsoft.com/library/mt203952.aspx
[SQLCMD]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team gatto]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum di Overflow dello stack]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

