<properties
   pageTitle="Dati gli distribuiti Opzioni tabella per i sistemi market parallela elaborazione (MPP) dell'archivio di dati SQL e Parallel Data Warehouse | Microsoft Azure"
   description="Informazioni su come i dati sono distribuiti per market parallela elaborazione (MPP) e le opzioni per la distribuzione di tabelle in magazzino dati di SQL Azure e Parallel Data Warehouse."
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
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Dati distribuiti e distribuite tabelle per market parallela elaborazione (MPP)

Informazioni su come dati utente viene distribuiti in Data Warehouse di SQL Azure e Parallel Data Warehouse, ovvero i sistemi di Microsoft market parallela elaborazione (MPP). Progettazione dei data warehouse da utilizzare dati distribuiti in modo efficace consente di ottenere i vantaggi dell'architettura MPP di elaborazione delle query. Alcune opzioni di progettazione di database possono avere un impatto per migliorare le prestazioni di query.  

>[AZURE.NOTE] Azure SQL Data Warehouse e Parallel Data Warehouse utilizzare la stessa struttura market parallela elaborazione (MPP), ma hanno alcune differenze a causa di piattaforma sottostante. SQL Data Warehouse è una piattaforma come servizio (PaaS) a cui viene eseguito su Azure. Parallel Data Warehouse viene eseguito su Analitica piattaforma del sistema (punti di accesso) che è un dispositivo locale che esegue Windows Server.

## <a name="what-is-distributed-data"></a>Che cos'è distribuiti dati?

In SQL Data Warehouse e Parallel Data Warehouse dati distribuiti fa riferimento ai dati utente memorizzati in più posizioni nel sistema MPP. Ciascuno di questi percorsi funge da un'archiviazione indipendente e unità di elaborazione che esegue query nella propria parte dei dati. Dati distribuiti sono fondamentali per l'esecuzione di query in parallelo per ottenere prestazioni elevate query.

Per distribuire i dati, data warehouse assegna ogni riga di una tabella utente a una posizione distribuita.  È possibile distribuire le tabelle con un metodo di distribuzione hash o circolari. Il metodo di distribuzione viene specificato nell'istruzione CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Distribuito hash tabelle
  
Il diagramma seguente illustra come un intero (tabella non distribuita) vengono archiviata come tabella hash distribuita. Una funzione deterministica assegna ogni riga in cui inserire una distribuzione. Nella definizione della tabella, una delle colonne viene impostata come la colonna di distribuzione. La funzione hash utilizza il valore nella colonna distribuzione per assegnare ogni riga a una distribuzione.

Vi sono considerazioni sulle prestazioni per la selezione di una colonna di distribuzione, ad esempio distinzione, asimmetria dati e tipi di query eseguiti nel sistema.
  
![Tabella distribuito] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabella distribuito")  
  
-   Ogni riga appartiene a una distribuzione.  
  
-   Algoritmo deterministica assegna ogni riga a una distribuzione.  
  
-   Il numero di righe di tabella per distribuzione varia indicato dalle varie dimensioni delle tabelle.

## <a name="round-robin-distributed-tables"></a>Tabelle distribuite circolari

Una tabella distribuita circolari distribuisce le righe mediante l'assegnazione di ogni riga di una distribuzione in modo sequenziale. È veloce caricare dati in una tabella circolari, ma potrebbe non essere più lenta alle prestazioni delle query.  Partecipare a una tabella circolari in genere richiede reshuffling le righe per consentire alla query per produrre un risultato preciso, che richiede tempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Percorsi di archiviazione distribuito sono denominate distribuzioni

Ogni posizione distribuito viene definita una distribuzione. Durante l'esecuzione di una query in parallelo, ogni distribuzione esegue una query SQL nella propria parte dei dati. Per eseguire la query SQL Data Warehouse utilizzato Database SQL. Parallel Data Warehouse utilizza SQL Server per eseguire la query. Questa architettura di condivisione è fondamentale per raggiungere scalabilità elaborazione in parallelo.

### <a name="can-i-view-the-distributions"></a>È possibile visualizzare le distribuzioni?

Ogni distribuzione dispone di un ID di distribuzione ed è visibile nelle visualizzazioni di sistema relativi a SQL Data Warehouse e Parallel Data Warehouse. È possibile utilizzare l'ID di distribuzione per risolvere i problemi di prestazioni delle query e altri problemi. Per un elenco delle visualizzazioni di sistema, vedere la [visualizzazione di sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Differenza tra una distribuzione e un nodo di calcolo

Una distribuzione è l'unità di base per l'archiviazione di dati distribuiti ed elaborazione delle query in parallelo. Distribuzioni sono raggruppate in nodi di calcolo. Ogni nodo di calcolo tiene traccia di una o più distribuzioni.  

-   Sistema di piattaforma Analitica utilizza nodi di calcolo come componente centrale delle funzionalità di architettura e scalabilità hardware. Utilizza sempre otto distribuzioni per nodo di calcolo, come illustrato nel diagramma per le tabelle hash distribuita. Il numero dei nodi di calcolo e di conseguenza il numero delle distribuzioni, è il numero dei nodi di calcolo che si acquista per il dispositivo. Ad esempio, se si acquistano otto nodi di calcolo, viene visualizzato 64 distribuzioni (8 nodi x 8 distribuzioni/nodo di calcolo). 

-   SQL Data Warehouse dispone di un numero fisso di 60 distribuzioni e un numero flessibile dei nodi di calcolo. I nodi di elaborazione sono implementati con Azure risorse di elaborazione e lo spazio di archiviazione. Il numero dei nodi di calcolo può cambiare in base al carico di lavoro di servizio di back-end e la capacità di elaborazione (DWUs) specificato per l'archivio dati. Quando viene modificato il numero dei nodi di calcolo, viene modificato anche il numero di distribuzioni per nodo di calcolo. 

### <a name="can-i-view-the-compute-nodes"></a>È possibile visualizzare i nodi di elaborazione?

Ogni nodo di calcolo con un ID nodo ed è visibile nelle visualizzazioni di sistema relativi a SQL Data Warehouse e Parallel Data Warehouse.  È possibile osservare il nodo di calcolo per la colonna node_id nelle visualizzazioni di sistema i cui nomi iniziano con sys.pdw_nodes. Per un elenco delle visualizzazioni di sistema, vedere la [visualizzazione di sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replicato tabelle per Parallel Data Warehouse 
  
Si applica a: parallela Data Warehouse

Oltre a utilizzare tabelle distribuite, Parallel Data Warehouse offre la possibilità di replicare tabelle. *Replicato tabella* è una tabella in cui è archiviata interamente su ogni nodo di calcolo. La replica di una tabella rimuove la necessità di trasferire le righe delle tabelle tra nodi di calcolo prima di utilizzare la tabella in un join o aggregazione. Le tabelle replicate sono possibili con le tabelle di piccole dimensioni a causa di spazio di archiviazione necessario per archiviare la tabella completa su ogni nodo di calcolo.  
  
Il diagramma seguente illustra una tabella replicata in cui è archiviata in ogni nodo di calcolo. È archiviata la tabella replicata su tutti i dischi assegnati al nodo di calcolo. Questa strategia disco viene implementata tramite gruppi di file di SQL Server.  
  
![Tabella replicato] (media/sql-data-warehouse-distributed-data/replicated-table.png "Tabella replicato") 
  
## <a name="next-steps"></a>Passaggi successivi
  
Per utilizzare tabelle distribuite in modo efficace, vedere [tabelle relativo alla distribuzione SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)  
  



  
