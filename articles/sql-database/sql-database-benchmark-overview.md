<properties
    pageTitle="Panoramica di benchmark di Database SQL Azure"
    description="Questo argomento illustra i Benchmark di Database SQL Azure utilizzato in misurare le prestazioni del Database di SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Panoramica di benchmark di Database SQL Azure

## <a name="overview"></a>Panoramica
Database SQL di Microsoft Azure offre tre [livelli di servizio](sql-database-service-tiers.md) con più livelli di prestazioni. Ogni livello di prestazioni fornisce un set di aumento delle risorse o "power", progettato per offrire velocità sempre più alti.

È importante quantificare come potenza crescente di ogni livello di prestazioni viene convertito in prestazioni maggiore del database. Per eseguire questo Microsoft ha sviluppato Azure SQL Database comparativa (ASDB). Benchmark esercizi una combinazione di operazioni di base nel carichi di lavoro OLTP tutti. Abbiamo misurare la produttività ottenuta per i database in ogni livello di prestazioni.

Le risorse e la potenza di ogni livello di livello e le prestazioni di servizio sono espressi in termini di [Unità di transazione Database (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs consentono di descrivere la capacità relativa di un livello di prestazioni in base a una misura fusione di CPU e memoria, leggere e scrivere tariffe offerte da ogni livello di prestazioni. Raddoppiare la classificazione di DTU di un database equivale a raddoppiare la potenza di database. Benchmark consente di valutare l'impatto sulle prestazioni di database di power crescente offerte da ogni livello di prestazioni adottando operazioni di database effettivo, durante il ridimensionamento delle dimensioni del database, numero di utenti e le tariffe transazione proporzionalmente le risorse disponibili per il database.

Per esprimere la velocità del livello di base del servizio utilizzando le transazioni per ora, il livello di servizio Standard tramite le transazioni al minuto e il livello di servizio Premium utilizzando le transazioni al secondo, rende più semplice correlare rapidamente le prestazioni potenziali di ogni livello di servizio ai requisiti di un'applicazione.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlare risultati benchmark per le prestazioni del database di scenari reali
È importante tenere presente che ASDB, ad esempio tutti benchmark, sia representative e indicativi solo. La frequenza delle transazioni realizzata con l'applicazione benchmark non saranno equivalenti a quelli che potrebbero essere ottenuti con altre applicazioni. Benchmark comprende un insieme di transazione differenti tipi eseguita su uno schema che contiene un intervallo di tabelle e i tipi di dati. Durante la valutazione vengono eseguite le stesse operazioni di base comuni a tutti i carichi OLTP, non rappresenta una classe specifica del database o applicazione. L'obiettivo della benchmark è fornire una Guida adeguata per le prestazioni relative di un database che si potrebbe ottenere durante il ridimensionamento verso l'alto o verso il basso tra i livelli di prestazioni. In realtà, database sono di complessità e dimensioni diverse, riscontrare mix diversi carichi di lavoro e risponderanno in modi diversi. Ad esempio, un'applicazione che richiede significative attività IO potrebbe raggiunto soglie IO in anticipo oppure un'applicazione che richiede significative attività CPU potrebbe raggiunto limiti CPU in anticipo. Non ci sono garanzie che qualsiasi database particolare nello stesso modo benchmark in crescenti carico.

Benchmark e la metodologia sono descritti di seguito.

## <a name="benchmark-summary"></a>Riepilogo benchmark
ASDB misura le prestazioni di una combinazione di operazioni di base sul database che si verificano più di frequente in carichi di lavoro (OLTP) di elaborazione delle transazioni online. Sebbene il riferimento è progettato con il cloud computing idea, schema di database, popolazione di dati e le transazioni sono state progettate per essere ampiamente representative degli elementi di base più comunemente usati nei carichi di lavoro OLTP.

## <a name="schema"></a>Schema
Lo schema è progettato per diversi e sufficiente complessità per supportare una vasta gamma di operazioni. Il riferimento viene eseguita in un database composto da sei tabelle. Le tabelle possono essere suddivise in tre categorie: dimensioni fisse, proporzioni dei caratteri e cresce. Sono disponibili due tabelle di dimensioni fisse. tre tabelle proporzioni. e una tabella crescente. Tabelle di dimensioni fisse contengono un numero costante di righe. Ridimensionamento tabelle hanno una cardinalità proporzionale alle prestazioni del database, ma non modifica durante le prove di valutazione. La tabella crescente viene ridimensionata come una tabella di ridimensionamento in caricamento iniziale, ma le modifiche di cardinalità durante l'esecuzione benchmark non appena righe vengono inserite o eliminate.

Lo schema include una combinazione di tipi di dati, inclusi numeri interi, valore numerico, caratteri e data/ora. Lo schema include chiavi primarie e secondarie, ma non di tutte le chiavi esterne, vale a dire non sono presenti vincoli di integrità referenziale tra le tabelle.

Un programma di generazione dati genera i dati per il database iniziale. Numero intero e dati numerici viene generati con varie strategie. In alcuni casi, i valori vengono distribuiti in modo casuale in un intervallo. In altri casi, un set di valori in modo casuale è permutato per garantire che una distribuzione specifica. Campi di testo vengono generati da un elenco di parole per produrre realistici dati dall'aspetto professionale.

Il database viene ridimensionato in base a un "fattore di scala". Il fattore di ridimensionamento (abbreviato in If) determina la relazione dell'adattamento e crescita tabelle. Come descritto di seguito nella sezione utenti e combinazione di velocità, le dimensioni del database, numero di utenti e le massime prestazioni tutti in proporzione tra loro.

## <a name="transactions"></a>Transazioni
Il carico di lavoro è costituito da nove tipi di transazione, come illustrato nella tabella seguente. Ogni transazione è progettato per evidenziare un particolare set di caratteristiche del sistema di database engine e sistema hardware, con contrasto elevato rispetto alle altre. Questo approccio è facile valutare l'impatto dei diversi componenti di prestazioni. Ad esempio, transazione "Lettura piena" produce un numero significativo di operazioni di lettura da disco.

| Tipo di transazione | Descrizione |
|---|---|
| Leggere Lite | SELEZIONARE; in memoria. sola lettura |
| Supporto di lettura | SELEZIONARE; prevalentemente in memoria; sola lettura |
| Incolla di lettura | SELEZIONARE; prevalentemente non in memoria; sola lettura |
| Aggiornamento Lite | AGGIORNAMENTO; in memoria. lettura e scrittura |
| Incolla di aggiornamento | AGGIORNAMENTO; prevalentemente non in memoria; lettura e scrittura |
| Inserisci Lite | INSERISCI; in memoria. lettura e scrittura |
| Inserisci Incolla | INSERISCI; prevalentemente non in memoria; lettura e scrittura |
| Elimina | ELIMINA; combinazione di in memoria e non in memoria; lettura e scrittura |
| Incolla CPU | SELEZIONARE; in memoria. carico CPU relativamente eccessivo; sola lettura |

## <a name="workload-mix"></a>Combinazione di carico di lavoro
Transazioni vengono selezionate in modo casuale da una distribuzione ponderata con la combinazione di complessiva seguente. Combinazione completa dispone di un rapporto di lettura/scrittura di circa 2:1.

| Tipo di transazione | % di Mix |
|---|---|
| Leggere Lite | 35 |
| Supporto di lettura | 20 |
| Incolla di lettura | 5 |
| Aggiornamento Lite | 20 |
| Incolla di aggiornamento | 3 |
| Inserisci Lite | 3 |
| Inserisci Incolla | 2 |
| Elimina | 2 |
| Incolla CPU | 10 |

## <a name="users-and-pacing"></a>Utenti e velocità
Il carico di lavoro benchmark viene gestita da uno strumento che invia le transazioni su un set di connessioni per simulare il comportamento di un numero di utenti. Anche se tutte le connessioni e le transazioni sono generata dal computer, per semplicità verrà fatto riferimento a queste connessioni "utenti". Anche se ogni utente opera indipendente tutti gli altri utenti, tutti gli utenti eseguono lo stesso ciclo di procedura riportata di seguito:

1. Stabilire una connessione di database.
2. Ripetere segnalato per uscire:
    - Selezionare una transazione in modo casuale (da una distribuzione ponderata).
    - Eseguire la transazione selezionata e misurare il tempo di risposta.
    - Attendere che un ritardo velocità.
3. Chiudere la connessione al database.
4. Esci.

Il ritardo velocità (nel passaggio 2c) sia selezionato in modo casuale, ma con una distribuzione che include una media di secondo 1.0. In questo modo ogni utente, in Media, generare solo una transazione al secondo.

## <a name="scaling-rules"></a>Ridimensionamento delle regole
Il numero di utenti è la dimensione del database (in unità fattore di scala). Non c'è un utente per ogni cinque unità fattore di ridimensionamento. A causa di ritardo velocità, un utente può generare solo una transazione al secondo, in Media.

Ad esempio, una scala fattori di 500 (if = 500) database avrà 100 utenti e ottenere una velocità massima di 100 indica. Per indirizzare un indica superiore tasso richiede un database di dimensioni maggiori e più utenti.

Nella tabella seguente mostra il numero di utenti in realtà mantenuta per ogni livello di livello e le prestazioni di servizio.

| Livello di servizio (livello di prestazioni) | Utenti | Dimensioni database |
|---|---|---|
| Base | 5 | 720 MB |
| Standard (S0) | 10 | ALMENO 1 GB |
| Standard (S1) | 20 | 2.1 GB |
| Standard (S2) | 50 | 7.1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | GB 114 |

## <a name="measurement-duration"></a>Durata misura
Un riferimento valido eseguito richiede una durata stato stabile misura di almeno un'ora.

## <a name="metrics"></a>Metrica
La chiave nel benchmark sono velocità e tempi di risposta.

- Velocità è la misurazione delle prestazioni essenziali nel benchmark. La velocità di trasmissione viene indicato nella transazioni per ogni unità di tempo, il conteggio di tutti i tipi di transazione.
- Tempo di risposta è una misura della prevedibilità delle prestazioni. Il vincolo di tempo di risposta varia in base alla classe del servizio, con categorie superiori di servizio con un requisito di tempo di risposta più restrittive, come illustrato di seguito.

| Classe di servizio  | Misura la velocità di trasmissione | Requisito di tempo di risposta |
|---|---|---|
| Premium | Transazioni al secondo | percentile 95th 0,5 secondi |
| Standard | Transazioni al minuto | 90 ° percentile secondi 1.0 |
| Base | Transazioni orarie | percentile 80th secondi 2.0 |

## <a name="conclusion"></a>Conclusioni
Il riferimento di Database SQL Azure misura le prestazioni relative di Database di SQL Azure in esecuzione in intervallo di livelli di servizio disponibili e livelli di prestazioni. Benchmark esercizi una combinazione di operazioni di base sul database che si verificano più di frequente in carichi di lavoro (OLTP) di elaborazione delle transazioni online. Per la misurazione delle prestazioni effettiva, benchmark fornisce una valutazione più significativa dell'impatto sulla velocità di modifica del livello di prestazioni rispetto a quanto possibile elencando solo le risorse disponibili per ogni livello, ad esempio velocità CPU, le dimensioni della memoria e IOPS. In futuro, si continuerà ad evolversi benchmark per ampliare l'ambito ed espandere i dati forniti.

## <a name="resources"></a>Risorse
[Introduzione al Database SQL](sql-database-technical-overview.md)

[Livelli di servizio e livelli di prestazioni](sql-database-service-tiers.md)

[Indicazioni delle prestazioni per i database singoli](sql-database-performance-guidance.md)
