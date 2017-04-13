<properties 
    pageTitle="Note sulla versione di Gateway di gestione dati | Dati di Azure Factory" 
    description="Note sulla versione brano di Gateway di gestione dati" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Note sulla versione di Gateway di gestione dati
Uno dei problemi per l'integrazione di dati moderna è perfettamente spostare dati da e verso locale nel cloud. Dati Factory rende questa integrazione perfetta con Gateway di gestione dati, è un agente che è possibile installare locale per abilitare lo spostamento dei dati ibrida.

Vedere gli articoli seguenti per informazioni dettagliate sui Gateway di gestione dati e come utilizzarlo: 

- [Gateway di gestione dati](data-factory-data-management-gateway.md)
- [Spostare i dati tra locali e cloud usando Factory di dati di Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Versione corrente (2.2.6072.1)

- Supporta l'impostazione proxy HTTP per il gateway usando Gestione configurazione di Gateway. Se configurato, Blob Azure, tabella Azure, Lake di dati di Azure e DB documento sono accessibili tramite proxy HTTP.
- Intestazione supporta la gestione di formato testo quando si copiano dati da/verso Blob Azure, archivio Lake dati di Azure, File System locale e HDFS locale.
- Supporta la copia di dati da accodare Blob e Blob di pagine insieme già supportati Blob di blocco.
- Introduce un nuovo stato del gateway **Online (limitato)**, che indica che la funzionalità principale del gateway funziona tranne il supporto di operazione interattiva per Copia guidata.
- Migliora l'affidabilità della registrazione del gateway tramite chiave di registrazione.

## <a name="earlier-versions"></a>Versioni precedenti

## <a name="2160401"></a>2.1.6040.1

- DB2 driver è incluso nel pacchetto di installazione di gateway ora. Non è necessario installare separatamente. 
- Driver DB2 supporta ora z/OS e DB2 per si (AS / 400) insieme a piattaforme già supportate (Linux, Unix e Windows). 
- Supporta l'utilizzo DocumentDB come origine o destinazione per gli archivi dati locali
- Supporta la copia di dati da/a fredda/hot blob archiviazione insieme account di archiviazione generico già supportati. 
- Consente di connettersi a SQL Server locale tramite gateway con diritti di accesso remoto.  

## <a name="2060131"></a>2.0.6013.1

- È possibile selezionare la lingua da utilizzare durante l'installazione manuale da un gateway.
- Quando gateway non funziona come previsto, è possibile scegliere di inviare i registri di gateway di ultimi sette giorni a Microsoft per facilitare la risoluzione dei problemi del problema. Se gateway non è connesso al servizio cloud, è possibile scegliere di salvare e archiviare i registri di gateway.  
- Miglioramenti apportati all'interfaccia utente per Gestione configurazione di gateway:
    - Rendere più visibili stato del gateway nella scheda Home.
    - Controlli riorganizzati e semplificati.
- È possibile copiare dati da un archivio uso [dello strumento anteprima di copia senza codice](data-factory-copy-data-wizard-tutorial.md). In generale, vedere [Copia scalare](data-factory-copy-activity-performance.md#staged-copy) per informazioni dettagliate su questa caratteristica. 
- È possibile usare Gateway di gestione dati ai dati di ingresso direttamente da un database di SQL Server locale in Azure apprendimento.
- Miglioramenti delle prestazioni
    - Migliorare le prestazioni in visualizzazione Schema/anteprima in SQL Server nello strumento di anteprima senza codice copia.



## <a name="11259531"></a>1.12.5953.1
- Correzioni di bug

## <a name="11159181"></a>1.11.5918.1

- Dimensione massima del log gateway è state aumentate da 1 MB a 40 MB.
- Nel caso in cui è necessario riavviare durante l'aggiornamento automatico di gateway, viene visualizzata una finestra di dialogo di avviso. È possibile scegliere di riavviare destra quindi o in un secondo momento. 
- In caso di errore di aggiornamento automatico, programma di installazione di gateway tentativi di aggiornamento automatico tre volte al massimo.
- Miglioramenti delle prestazioni
    - Migliorare le prestazioni di caricamento delle tabelle di grandi dimensioni dal server in locale in uno scenario di copia senza codice.
- Correzioni di bug

## <a name="11058921"></a>1.10.5892.1

- Miglioramenti delle prestazioni
- Correzioni di bug

## <a name="1958652"></a>1.9.5865.2

- Funzionalità di aggiornamento automatico zero touch
- Icona Nuovo gateway sugli indicatori di stato
- Possibilità di "Aggiorna adesso" dal client
- Possibilità di impostare l'ora di pianificazione di aggiornamento
- Script di PowerShell per attivare e disattivare l'aggiornamento automatico attivazione/disattivazione
- Supporto per formato JSON  
- Miglioramenti delle prestazioni
- Correzioni di bug

## <a name="1858221"></a>1.8.5822.1

- Migliorare la risoluzione dei problemi
- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1757951"></a>1.7.5795.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1757641"></a>1.7.5764.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1657351"></a>1.6.5735.1

- Supporto locale HDFS origine/Sink
- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1656961"></a>1.6.5696.1

- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1656761"></a>1.6.5676.1

- Strumenti di diagnostica supporto su Configuration Manager
- Supporta le colonne della tabella per le origini dati in formato tabulare per Azure Data Factory
- Supporto DW SQL Azure Data Factory
- Supporto Reclusive in BlobSource e FileSource per Factory dati Azure
- Supporto CopyBehavior-MergeFiles, PreserveHierarchy e FlattenHierarchy BlobSink e FileSink con copia binario per Factory dati Azure
- Supporto attività Copia segnalare l'avanzamento di Azure Data Factory
- Supporto origine connettività per la convalida dei dati Factory dati Azure
- Correzioni di bug


### <a name="1656721"></a>1.6.5672.1

- Nome della tabella di origine dati ODBC di supporto per Azure Data Factory
- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1656581"></a>1.6.5658.1

- Elaborare File di supporto per dati Azure Factory
- Supporto conservando gerarchia nella copia binario per Azure Data Factory
- Supporto Copia attività idempotenza per Factory dati Azure
- Correzioni di bug

### <a name="1656401"></a>1.6.5640.1

- Supporto 3 più origini dati per Azure Data Factory (ODBC, OData, HDFS)
- Supporto di virgolette in csv parser per Azure Data Factory
- Supporto per la compressione (BZip2)
- Correzioni di bug

### <a name="1556121"></a>1.5.5612.1

- Supporto cinque database relazionali per Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
- Supporto per la compressione (Gzip e Deflate)
- Miglioramenti delle prestazioni
- Correzioni di bug


### <a name="1455491"></a>1.4.5549.1

- Aggiungere il supporto di origine dati Oracle per Azure Data Factory
- Miglioramenti delle prestazioni
- Correzioni di bug

### <a name="1454921"></a>1.4.5492.1

- Binario unificata che supporta i servizi Microsoft Azure Data Factory e Office 365 Power BI
- Perfezionare il processo di configurazione dell'interfaccia utente e la registrazione
- Azure Data Factory – Azure ingresso e in uscita di supporto per l'origine dati SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Risolvere il problema di timeout per supportare più connessioni all'origine dati richiede molto tempo. 
    
### <a name="1155268"></a>1.1.5526.8

- Richiede .NET Framework 4.5.1 come prerequisito durante l'installazione.

### <a name="1051442"></a>1.0.5144.2

- Nessuna modifica che interessano gli scenari di Azure Data Factory. 
