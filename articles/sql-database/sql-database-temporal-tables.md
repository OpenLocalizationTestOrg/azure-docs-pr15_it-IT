<properties
   pageTitle="Guida introduttiva a tabelle temporali nel Database SQL Azure | Microsoft Azure"
   description="Informazioni su come iniziare a utilizzare tabelle temporali nel Database di SQL Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Guida introduttiva a tabelle temporali nel Database SQL Azure

Tabelle temporali costituiscono una nuova caratteristica di programmabilità del Database di SQL Azure che consente di rilevare e analizzare la cronologia completa delle modifiche apportate ai dati, senza la necessità di codifica personalizzata. Tabelle temporali mantenere i dati strettamente correlati al contesto temporale in modo che fatti archiviate possono essere interpretati come valido solo all'interno del periodo specifico. Questa proprietà di tabelle temporali consente per analisi efficaci basate sul tempo e ottenere informazioni approfondite da evoluzione dei dati.

##<a name="temporal-scenario"></a>Scenario temporale

In questo articolo illustra i passaggi per utilizzare tabelle temporali in uno scenario di applicazione. Si supponga di voler tenere traccia delle attività dell'utente in un nuovo sito Web sviluppata da zero o in un sito Web esistente che si desidera estendere con analitica attività utente. In questo esempio si presuppone che il numero di pagine web visitate durante un periodo di tempo è un indicatore che deve essere di acquisizione e monitorare nel database di sito Web ospitato nel Database di SQL Azure. Lo scopo dell'analisi nella cronologia delle attività dell'utente è ottenere input riprogettare sito Web e fornire ottimale per i visitatori.

Il modello di database per questo scenario è molto semplice: unità di misura metriche attività utente viene rappresentato con un campo numero intero, **PageVisited**e viene acquisita insieme a informazioni di base nel profilo utente. Inoltre, per l'analisi di tempo in base a, da mantenere una serie di righe per ogni utente, in cui ogni riga rappresenta il numero di pagine di un utente particolare visitato all'interno di un periodo di tempo specifico.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Per fortuna, non è necessario inserire un progetto nell'app per mantenere queste informazioni attività. Con tabelle temporali, questo processo automatizzato - offrendo totale flessibilità durante la progettazione del sito Web e più tempo per concentrarsi sull'analisi dei dati stesso. L'unica cosa è necessario effettuare è per assicurarsi che sia configurata **WebSiteInfo** tabella come [temporale versione del sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). La procedura esatta per utilizzare tabelle temporali in questo scenario è descritti di seguito.

##<a name="step-1-configure-tables-as-temporal"></a>Passaggio 1: Configurare le tabelle come temporale

A seconda che si avvia sviluppo di nuovi o l'aggiornamento di applicazione esistente, si verrà creare tabelle temporale o modificare quelli esistenti aggiungendo attributi temporali. In generale caso proprio scenario può essere una combinazione di queste due opzioni. Eseguire queste operazioni utilizzando [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) o un altro strumento di sviluppo di Transact-SQL.


> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Creare una nuova tabella

Utilizzare i menu di scelta rapida "Nuova versione del sistema tabella" in Esplora oggetti SQL Server Management Studio per aprire l'editor di query con uno script modello temporale tabella e quindi utilizzare "Specificare i valori per parametri modello" (Ctrl + MAIUSC + M) per popolare il modello:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

In SSDT, scelto modello "Temporale tabella (versione del sistema)" durante l'aggiunta di nuovi elementi al progetto di database. Che verranno aprire Progettazione tabelle e consentono di specificare facilmente il layout di tabella:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

È anche possibile creare temporale tabella specificando le istruzioni Transact-SQL direttamente, come illustrato nell'esempio seguente. Si noti che gli elementi obbligatori di ogni tabella temporale la definizione del periodo e la clausola SYSTEM_VERSIONING con un riferimento a un'altra tabella utente che verrà archiviata nella cronologia versioni:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Quando si crea tabella temporale versione del sistema, viene creata automaticamente la tabella Cronologia con la configurazione predefinita. Tabella della cronologia per impostazione predefinita contiene un indice B albero cluster di colonne del periodo (fine, inizio) con attivata la compressione di pagina. Questa configurazione è ideale per la maggior parte dei casi in cui vengono utilizzate tabelle temporali, in particolare per [il controllo dei dati](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

In questo caso particolare, il nostro obiettivo è eseguire analisi delle tendenze basate sul tempo tramite una cronologia dei dati più e più grande set di dati, in modo che la scelta di spazio di archiviazione per la tabella Cronologia è un indice columnstore raggruppate. Un cluster columnstore fornisce buoni risultati molto e prestazioni per le query analitiche. Tabelle temporali offrono la possibilità di configurare gli indici su tabelle corrente e temporali completamente in modo indipendente. 

**Nota**: gli indici Columnstore sono disponibili solo nel livello di servizio premium.

Lo script seguente mostra come indice predefinito nella tabella Cronologia può essere modificato in columnstore cluster:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tabelle temporali sono rappresentate in Esplora aree di oggetto con l'icona specifico per facilitarne l'identificazione, mentre la tabella Cronologia viene visualizzata come un nodo figlio.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Istruzione ALTER table esistente per temporale

Di seguito coprire lo scenario alternativo in cui la tabella WebsiteUserInfo esiste già, ma non è progettata per mantenere una cronologia delle modifiche. In questo caso, è possibile estendere semplicemente la tabella esistente per diventare temporale, come illustrato nell'esempio seguente:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Passaggio 2: Eseguire regolarmente il carico di lavoro

Il vantaggio principale di tabelle temporali è non è necessario modificare oppure modificare il sito Web in alcun modo per eseguire il rilevamento delle modifiche. Una volta creato, tabelle temporali persistono trasparente precedenti versioni di riga, ogni volta che si eseguono modifiche sui dati. 

Per sfruttare rilevamento automatico per questo scenario, si aggiornare solo colonna **PagesVisited** ogni volta che fine proprio sessione nel sito Web dell'utente:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

È importante notare che la query di aggiornamento non è necessario conoscere l'ora esatta quando si è verificato l'operazione di effettivo né come verranno mantenuti dati cronologici per analisi future. Entrambi gli aspetti vengono gestiti automaticamente dal Database di SQL Azure. Il diagramma seguente illustra la modalità di generazione dati della cronologia in tutti gli aggiornamenti.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Passaggio 3: Eseguire l'analisi dei dati cronologici

Quando è attivato il controllo delle versioni sistema temporale, analisi dei dati cronologici sono solo una query all'esterno è. In questo articolo è fornisce alcuni esempi di indirizzo analisi scenari comuni - informazioni tutti i dettagli, esplorare varie opzioni introdotte con la clausola [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Per visualizzare i principali 10 utenti ordinati in base al numero di pagine web visitate in un'ora fa, eseguire la query:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

È possibile modificare facilmente questa query per analizzare le visite al sito per un giorno fa, un mese fa o in qualsiasi momento in passato si desidera.

Per eseguire analisi statistica base per il giorno precedente, utilizzare l'esempio seguente:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Per cercare le attività di un utente specifico, all'interno di un periodo di tempo, utilizzare la clausola contenuti IN:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualizzazione grafica è particolarmente utile per le query temporale come è possibile visualizzare le tendenze e modelli di utilizzo in un intuitivo modo molto semplice:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Schema di tabella in evoluzione

In genere, sarà necessario modificare lo schema della tabella temporale mentre si sta eseguendo lo sviluppo di app. Per tale, è sufficiente eseguire regolari ALTER TABLE istruzioni e il Database di SQL Azure verrà in modo appropriato propagato modifiche alla tabella della cronologia. Lo script seguente mostra come è possibile aggiungere ulteriore attributo per tenerne traccia:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Allo stesso modo, è possibile modificare la definizione di colonna mentre è attivo il carico di lavoro:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Infine, è possibile rimuovere una colonna che non più necessarie.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
In alternativa, è possibile utilizzare più recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) modificare schema tabella temporale mentre si è connessi al database (modalità online) o come parte del progetto di database (modalità non in linea).

##<a name="controlling-retention-of-historical-data"></a>Il controllo di conservazione dei dati cronologici

Con tabelle temporale versione del sistema, la tabella Cronologia potrebbe aumentare le dimensioni del database più valori normali tabelle. Una tabella di grandi dimensioni e crescente cronologia può diventare un problema a causa delle archiviazione pure costi nonché imporre sulle prestazioni sulle imposte sull'esecuzione di query temporale. Di conseguenza, lo sviluppo di un criterio di conservazione dei dati per la gestione dei dati nella tabella della cronologia è importante della pianificazione e la gestione del ciclo di vita di ogni tabella temporale. Con il Database di SQL Azure, sono disponibili i metodi seguenti per la gestione dei dati cronologici descritti nella tabella temporale:

- [Partizione delle tabelle](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script di pulizia personalizzato](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sulle tabelle temporali, vedere [la documentazione di MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visitare Channel 9 per ascoltare una [parte reale implementazione temporale storie di successo](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e guardare un [live dimostrazione temporale](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
