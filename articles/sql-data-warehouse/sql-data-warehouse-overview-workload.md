<properties
   pageTitle="Carico di lavoro warehouse dati"
   description="La flessibilità del SQL Data Warehouse consente di ingrandimento, ridurre o sospendere grande potenza utilizzando una scala di slittamento warehouse delle unità di dati (DWUs). Questo articolo illustra le metriche di magazzino dati e le relazioni DWUs. "
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
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Carico di lavoro warehouse dati
Un carico di lavoro warehouse dati fa riferimento a tutte le operazioni attendere rispetto a un data warehouse. Il carico di lavoro warehouse dati include l'intero processo del caricamento dei dati nel warehouse, eseguire l'analisi e reporting in data warehouse, la gestione di dati data warehouse e l'esportazione di dati data warehouse. Alla profondità e ampiezza di questi componenti sono spesso corrispondente al scad del data warehouse.


## <a name="new-to-data-warehousing"></a>Nuovi utenti di data warehouse?
Un data warehouse è una raccolta di dati che viene caricato da uno o più origini dati e viene utilizzati per eseguire attività di business intelligence, ad esempio analisi dei dati e creazione di report.

Data warehouse sono caratterizzati da query in un numero maggiore di righe, grandi intervalli di dati di analisi e potrebbe restituire risultati relativamente lunghi ai fini di analisi e creazione di report. Data warehouse sono inoltre caratterizzati da caricamenti dati relativamente grandi e piccole viene inserito a livello di transazione, aggiornamento ed eliminazione.

- Un data warehouse offre le prestazioni migliori quando i dati vengono archiviati in modo che consente di ottimizzare le query che è necessario analizzare grandi quantità di righe o grandi intervalli di dati. Questo tipo di analisi funziona meglio se i dati vengono archiviati ed effettuata la ricerca in base a colonne, anziché nelle righe.

>[AZURE.NOTE] Indice columnstore in memoria, che utilizza l'archiviazione di colonna, fornisce fino a 10 x gli utili di compressione e 100 x miglioramenti delle prestazioni query tradizionali alberi binari per le query di creazione di relazioni e analitica. È considerata indici columnstore lo standard per l'archiviazione e l'analisi di dati di grandi dimensioni in un data warehouse.

- Un data warehouse requisiti è diversi rispetto a un sistema che ottimizza per online elaborazione delle transazioni (OLTP). Il sistema OLTP include molti inserire, aggiornare ed eliminare operazioni. Queste operazioni di ricerca per specifiche righe nella tabella. Tabella di ricerca migliori quando i dati vengono archiviati in un modo di riga per riga. I dati possono essere ordinati rapidamente la ricerca con una divisione e impera approccio detta ricerca struttura o btree binaria.


## <a name="data-loading"></a>Caricamento dei dati
Caricamento dei dati è una parte del carico di lavoro warehouse dati. In genere imprese hanno un sistema OLTP occupato che tiene traccia delle modifiche durante la giornata quando i clienti generano transazioni aziendali. Periodicamente, spesso in notte durante una finestra di manutenzione, le transazioni vengono spostate o copiate nell'archivio di dati. Dopo aver aggiunto i dati del data warehouse, gli analisti possono eseguire l'analisi e prendere decisioni aziendali sui dati.

- In genere, il processo di caricamento è denominato ETL di estrazione, trasformazione e caricamento. In genere dati devono essere trasformato in modo che sia uniforme con altri dati nel data warehouse. Aziende usato in precedenza, dedicati ETL per eseguire le trasformazioni. A questo punto, con trattamento rapidamente market in parallelo è possibile caricare dati SQL Data Warehouse prima di tutto, quindi eseguire le trasformazioni. Questo processo viene chiamato estratto, carico e trasformare (ELT) e diventa un nuovo standard per il carico di lavoro warehouse di dati.

> [AZURE.NOTE] Con SQL Server 2016, è possibile eseguire analitica in tempo reale in una tabella OLTP. Questa operazione non sostituisce l'esigenza di un data warehouse archiviare e analizzare i dati, ma consentono di eseguire l'analisi in tempo reale.

### <a name="reporting-and-analysis-queries"></a>Query di creazione di report e analisi
Query di creazione di report e analisi spesso sono suddivise in piccole, medie e grandi dimensioni in base a un numero di criteri, ma in genere si basa sull'orario. Nella maggior parte dei data warehouse non esiste un carico di lavoro misto di esecuzione veloci e query con esecuzione prolungata. In ogni caso, è importante per determinare questa combinazione e per determinare la frequenza (ora, ogni giorno, fine mese, trimestre e fine e così via). È importante tenere presente che il carico di lavoro misti query unitamente concorrenza, lead corretta pianificazione delle capacità per un data warehouse.

- Pianificazione della capacità può essere un'attività complessa per un carico di lavoro di query misti, soprattutto quando è necessario un tempo di anticipo lungo per aggiungere capacità al data warehouse. SQL Data Warehouse rimuove urgente pianificazione della capacità dal momento che è possibile aumentare e ridurre le capacità di calcolo in qualsiasi momento e, poiché lo spazio di archiviazione e la capacità di elaborazione vengono ridimensionati in modo indipendente.

### <a name="data-management"></a>Gestione dei dati
Gestione dei dati è importante, soprattutto quando si conosce che è possibile eseguire nel prossimo futuro spazio su disco. Data warehouse suddividere in genere i dati in intervalli significativi, che vengono archiviati come partizioni in una tabella. Tutti i prodotti basati su SQL Server consentono di spostare le partizioni da e verso la tabella. Questa partizione passaggio consente di spostare i dati meno recenti al meno occupazione di molto spazio di archiviazione e mantenere i dati più recenti disponibili nell'archivio online.

- Gli indici ColumnStore supportano le tabelle partizionate. Per gli indici columnstore, le tabelle partizionate vengono usate per la gestione dei dati e archiviazione. Per tabelle archiviate dalla riga di riga, le partizioni dispongono di un ruolo di prestazioni delle query.  

- PolyBase svolge un ruolo importante nella gestione dei dati. Usa PolyBase, è possibile archiviare i dati nei formati precedenti a archiviazione blob Azure o Hadoop.  In questo modo diverse opzioni poiché i dati sono ancora in linea.  Può richiedere più tempo per recuperare dati da Hadoop, ma il compromesso del tempo di recupero potrebbe inferiore al costo di spazio di archiviazione.

### <a name="exporting-data"></a>Esportazione di dati
Per rendere disponibili per i report e analisi dei dati è possibile inviare dati del data warehouse server dedicato per l'esecuzione di report e analisi. Questi server sono denominati mart. Ad esempio, si potrebbe pre-elaborazione dati del report e quindi esportarlo da data warehouse a molti server tutto il mondo, per renderla reso disponibile per i clienti e analisti.

- Per creare report, ogni notte era possibile popolare sola lettura server di report con uno snapshot dei dati giornaliera. In questo modo la larghezza di banda superiore per i clienti riducendo le esigenze di risorse di calcolo in data warehouse. Da un aspetto sicurezza mart consente di ridurre il numero di utenti che hanno accesso al data warehouse.
- Per analitica, è possibile creare un cubo di analysis Services data warehouse ed eseguire analisi data warehouse o pre-elaborazione dati ed esportare con il server di analisi per ulteriormente analitica.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver scoperto un po' su SQL Data Warehouse, informazioni su come rapidamente [creare SQL Data Warehouse][] e [caricare dati di esempio][].

<!--Image references-->

<!--Article references-->
[caricare dati di esempio]: ./sql-data-warehouse-load-sample-databases.md
[creare un Data Warehouse SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
