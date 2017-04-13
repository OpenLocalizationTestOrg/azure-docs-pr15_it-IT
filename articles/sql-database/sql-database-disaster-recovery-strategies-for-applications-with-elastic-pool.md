<properties 
   pageTitle="Progettazione di soluzioni basate su Cloud di emergenza usando replica con SQL Database geografico | Microsoft Azure"
   description="Informazioni su come progettare una soluzione di cloud di emergenza scegliendo motivo failover destra."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Strategie di ripristino di emergenza per applicazioni che utilizzano Pool flessibile Database SQL 

Nel corso degli anni di che aver acquisito che servizi cloud non sono operazioni non consentite infallibile e irreversibile possibile e si verificherà. Database SQL di fornisce un numero di funzionalità per fornire per la continuità aziendale dell'applicazione quando si verificano questi problemi. Database [pool flessibile](sql-database-elastic-pool.md) e autonomo supportano lo stesso tipo di ripristino di emergenza. Questo articolo vengono illustrati diverse DR strategie per pool di elastici sfruttare queste caratteristiche di continuità aziendale di Database SQL.

Ai fini di questo articolo verrà utilizzato il modello di applicazione ISV SaaS canonico:

<i>Web applicazione disposizioni unico database basati su un cloud moderna, ideale per ogni utente finale. ISV ha un numero elevato di clienti e pertanto utilizza numerosi database, anche detto database tenant. Poiché i database tenant sono in genere modelli di attività imprevisti, ISV utilizza un pool flessibile rendere il database costi molto prevedibili "Extended" periodi di tempo. Pool flessibile anche semplifica la gestione delle prestazioni quando picchi di attività dell'utente. Oltre ai database tenant l'applicazione utilizza diversi database per gestire i profili utente, sicurezza, raccolta modelli di utilizzo e così via. Disponibilità di singoli tenant non ha effetti disponibilità dell'applicazione nel suo insieme. Tuttavia, verificare la disponibilità e prestazioni del database di gestione è fondamentale per la funzione dell'applicazione e se i database di gestione in modalità offline tutta l'applicazione è offline.</i>  

Il resto del documento verranno illustrati strategie DR illustrano una serie di scenari dalle applicazioni di avvio sensibili costo a quelli con requisiti di disponibilità restrittivi.  

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Costo iniziale riservate

<i>Sono un'azienda di avvio e sta estremamente costo riservata.  Per semplificare la distribuzione e gestione dell'applicazione e desidero dispone di un contratto di servizio limitato per i singoli clienti Ma si vuole garantire l'applicazione nel suo mai è offline.</i>

Per soddisfare i requisiti di semplicità, devono distribuire tutti i database tenant in un pool flessibile nell'area Azure di propria scelta e i database di gestione come database replicato geografico autonomo. Il ripristino di emergenza di tenant, utilizzare il ripristino geografico, in è disponibile senza costi aggiuntivi. Per assicurare la disponibilità dei database di gestione, dovrebbero essere replicate geografico in un'altra area (passaggio 1). Il costo in corso della configurazione di ripristino di emergenza in questo scenario è uguale al costo totale dei database secondari. Questa configurazione è illustrata nel diagramma seguente.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

In caso di un'interruzione nell'area principale, la procedura di ripristino per visualizzare l'applicazione online è illustrata nel diagramma successivo.

- Immediatamente failover la gestione del database (2) all'area DR. 
- Modificare la stringa di connessione dell'applicazione in modo che puntino all'area DR. Database di tenant e tutti i nuovi account verranno creati nell'area DR. I clienti esistenti vedranno i dati temporaneamente non disponibili.
- Creare il pool flessibile con la stessa configurazione del pool originale (3). 
- Utilizzare Ripristina geografico per creare copie del tenant di database (4). È possibile valutare la possibilità di attivazione Ripristina singoli dalle connessioni per l'utente finale o utilizzare alcune altre combinazioni di priorità specifica applicazione.

A questo punto l'applicazione è in linea nell'area DR, ma alcune verificherà ritardo quando si accede ai propri dati.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se l'interruzione era temporaneo, è possibile che l'area principale sarà recuperato da Azure prima del completamento tutte le operazioni di ripristino nell'area DR. In questo caso, è necessario progettare spostando l'applicazione tornare all'area principale. Il processo avrà la procedura illustrata nella diagramma seguente.
 
- Annullare tutte le richieste di ripristino di geografico in sospeso.   
- Failover i database di gestione all'area principale (5). Nota: Dopo il ripristino dell'area colori precedenti hanno diventa automaticamente dei secondari. A questo punto si passerà ruoli nuovamente. 
- Modificare la stringa di connessione dell'applicazione in modo che puntino tornare all'area principale. A questo punto tenant database e tutti i nuovi account verranno creati nell'area principale. Alcuni clienti esistenti vedranno i dati temporaneamente non disponibili.   
- Impostare tutti i database nel pool di DR in sola lettura per assicurarsi che non possono essere modificati nell'area DR (6). 
- Per ogni database nel pool di DR che sono stati modificati dopo il ripristino, rinominare o eliminare i database corrispondenti del pool principale (7). 
- Copiare i database aggiornati dal pool di DR al pool principale (8). 
- Eliminare il pool di DR (9)

A questo punto l'applicazione sarà online nell'area principale con tutti tenant database disponibili nel pool di principale.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

La chiave **trarre vantaggio** di questa strategia è in corso costo ridondanza dei dati livello. Backup automaticamente dal servizio di Database SQL con nessuna applicazione di riscrittura e senza costi aggiuntivi.  Il costo è sostenuto solo quando i database flessibile vengono ripristinati. Il **compromesso** è che il ripristino completo di tutti i database tenant richiederà molto tempo. Dipende il totale complessivo dimensioni dei database tenant e numero di ripristini verrà avviata nell'area DR. Anche se è la priorità Ripristina alcuni tenant rispetto alle altre, sarà in competizione con tutte le altri ripristini avviati nella stessa regione come il servizio compatibilità e limitare per ridurre al minimo l'impatto sulle database i clienti esistenti. Inoltre, il ripristino del database tenant non può iniziare fino a quando non viene creato il nuovo pool flessibile nell'area DR.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Applicazione consolidato con servizio su più livelli 

<i>Si è un'applicazione di SaaS consolidata con offerte di servizi a più livelli e contratti di servizio diversi per i clienti di valutazione e a pagamento. Per i clienti di valutazione, è necessario ridurre i costi più possibile. Versione di valutazione clienti possono richiedere del tempo di inattività ma desidera ridurre la probabilità. Per i clienti di pagamenti, qualsiasi tempo di inattività è il rischio di volo. Pertanto si desidera verificare che il pagamento clienti sempre comunque in grado di accedere ai dati.</i> 

Per supportare questo scenario, si deve suddivisione in pool flessibile distinti per separare il tenant di valutazione dalla tenant a pagamento. I clienti di valutazione avrebbe eDTU inferiore per tenant e contratto di servizio inferiore con un tempo di ripristino. I clienti di pagamenti può essere in un pool con maggiore eDTU per tenant e un contratto di servizio superiore. Per garantire il tempo di ripristino più basso, database tenant chi acquista dovrebbero essere replicate geografico. Questa configurazione è illustrata nel diagramma seguente. 

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Come primo scenario, i database di gestione sarà molto attivi in modo che si utilizza un database replicato geografico autonoma per renderla (1). In questo modo le prestazioni prevedibili per i nuovi abbonamenti cliente, aggiornamenti dei profili e altre operazioni di gestione. L'area geografica in cui risiedono i colori del database Gestione sarà l'area principale e l'area geografica in cui risiedono i database secondari di uno o più database Gestione sarà l'area geografica DR.

Database tenant chi acquista avrà database attivi nel pool di "a" viene completato il provisioning nell'area principale. È consigliabile eseguire il provisioning di un pool secondario con lo stesso nome nell'area DR. Ogni tenant è replicato geografico al pool secondario (2). In questo modo un ripristino rapido di tutti i database tenant tramite failover. 

Se si verifica un'interruzione nell'area principale, la procedura di ripristino per visualizzare l'applicazione online è illustrata nel diagramma seguente:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Eseguire immediatamente il database Gestione all'area DR (3).
- Modificare la stringa di connessione dell'applicazione in modo che puntino all'area DR. A questo punto tenant database e tutti i nuovi account verranno creati nell'area DR. I clienti di valutazione esistenti vedranno i dati temporaneamente non disponibili.
- Failover database del tenant a pagamento al pool nell'area DR immediatamente ripristinare la disponibilità (4). Poiché il failover è una modifica del livello di metadati rapide che è possibile prendere in considerazione l'ottimizzazione in failover singoli sono attivate su richiesta tramite le connessioni degli utenti finali. 
- Se le dimensioni di eDTU pool secondario erano inferiore a quello principale in quanto i database secondari richiesta solo la capacità di elaborare i registri di modifica mentre si trattasse di database secondari, è necessario aumentare immediatamente la capacità di pool ora a contenere il carico di lavoro completo di tutti i tenant (5). 
- Creare il nuovo pool flessibile con lo stesso nome e la stessa configurazione nell'area DR per i database dei clienti valutazione (6). 
- Dopo aver creato il pool dei clienti versione di valutazione, utilizzare geografico Ripristina per ripristinare i database di singoli tenant di valutazione nel pool di nuovo (7). È possibile valutare la possibilità di attivazione Ripristina singoli dalle connessioni per l'utente finale o utilizzare alcune altre combinazioni di priorità specifica applicazione.

A questo punto l'applicazione è in linea nell'area DR. Tutti i clienti di pagamenti è possibile accedere ai propri dati durante la valutazione si verificherà ritardo quando si accede ai dati.

Quando l'area principale verrà recuperata da Azure *dopo* avere ripristinato l'applicazione nell'area DR che è possibile continuare a eseguire l'applicazione in quell'area oppure è possibile decidere di non riuscire tornare all'area principale. Se l'area principale è recuperato *prima* viene completato il processo di failover, è necessario prendere in considerazione in mancanza indietro immediatamente. Il failback richiederà i passaggi illustrati nel diagramma seguente: 
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annullare tutte le richieste di ripristino di geografico in sospeso.   
- Failover la gestione del database (8). Dopo il ripristino dell'area principale precedente diventa automaticamente secondaria. A questo punto diventa primario nuovamente.  
- Failover tenant a pagamento database (9). Analogamente, dopo il ripristino dell'area colori precedenti diventano automaticamente database secondario. A questo punto sono sarà nuovamente i colori. 
- Impostare i database di valutazione ripristinati che sono stati modificati nell'area DR in sola lettura (10).
- Per ogni database nel pool di clienti valutazione DR che sono stati modificati dopo il ripristino, rinominare o eliminare il database corrispondente del pool principale clienti valutazione (11). 
- Copiare i database aggiornati dal pool di DR al pool principale (12). 
- Eliminare il pool di DR (13) 

> [AZURE.NOTE] L'operazione di failover è asincrono. Per ridurre al minimo il tempo di ripristino è importante eseguire il comando di failover dei database tenant in batch con almeno 20 database. 

La chiave **trarre vantaggio** di questa strategia è che fornisce il contratto di servizio più elevato per i clienti di pagamenti. Garantisce inoltre che le nuove versioni di valutazione vengono sbloccati non appena viene creato il pool DR versione di valutazione. Il **compromesso** è che il programma di installazione aumenta il costo totale dei database tenant in base al costo del pool di DR secondario per i clienti a pagamento. Inoltre, se pool secondario include una dimensione diversa, il pagamento verificherà prestazioni ridotte dopo il failover finché non viene completata l'aggiornamento del pool nell'area DR. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Applicazione geografiche con servizio su più livelli

<i>Si dispone di un'applicazione di SaaS consolidata con offerte di servizi a più livelli. Si desidera offrire un contratto di servizio molto aggressiva per i clienti a pagamento e ridurre i rischi di impatto quando si verificano interruzioni perché anche breve interruzione può causare un problema relativo cliente. È fondamentale che i clienti pagamenti sempre possano accedere ai dati. Le versioni di valutazione sono gratuite e un contratto di servizio non è disponibile durante il periodo di valutazione.</i> 

Per supportare questo scenario, è necessario disporre tre pool flessibile separato. Due pool di dimensioni uguali con eDTUs alta per ogni database deve eseguire il provisioning in due aree geografiche diverse per contenere database tenant dei clienti a pagamento. Pool terzo che contiene il versione di valutazione tenant avrebbe un eDTUs inferiore per ogni database ed eseguire il provisioning in uno dei due area.

Per garantire il tempo di ripristino più basso durante le interruzioni tenant chi acquista database devono essere replicato geografico con 50% dei database principali in ognuna delle due aree. Analogamente, ogni area avrebbe 50% dei database secondari. In questo modo se un'area è offline solo 50% del database dei clienti a pagamento da interessati e avrebbero dovuto failover. Altri database rimarrà invariate. Questa configurazione è illustrata nella figura seguente:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Come illustrato negli scenari precedenti, i database di gestione sarà molto attivi in modo che è necessario configurare loro come database replicato geografico autonomo (1). In questo modo le prestazioni prevedibili delle nuove sottoscrizioni di assistenza clienti, aggiornamenti dei profili e altre operazioni di gestione. Area sarebbe l'area principale per i database di gestione e l'area geografica B verrà utilizzato per il ripristino del database Gestione.

Database tenant dei clienti pagamento sarà anche replicato geografico ma con i colori e dividere il database secondari tra le opzioni internazionali e opzioni internazionali B (2). In questo modo i database primaria tenant subisca il periodo di inattività consente il controllo all'area geografica di altri e diventano disponibili. L'altra che metà dei database tenant non subiranno tutto. 

Diagramma seguente illustra i passaggi di ripristino da eseguire se si verifica un'interruzione nell'area risposte.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Immediatamente esito negativo sui database di gestione all'area B (3).
- Modificare la stringa di connessione dell'applicazione in modo che puntino ai database di gestione in B. modificare la gestione del database per verificare che i nuovi account e i database tenant verranno creati nell'area B e i database esistenti tenant troverà sono anche l'area geografica. I clienti di valutazione esistenti vedranno i dati temporaneamente non disponibili.
- Failover database del tenant a pagamento al pool di 2 nell'area B immediatamente ripristinare la disponibilità (4). Poiché il failover è una modifica del livello di metadati rapide che è possibile prendere in considerazione l'ottimizzazione in failover singoli sono attivate su richiesta tramite le connessioni degli utenti finali. 
- Dall'ora pool 2 contiene solo i database principali che il carico di lavoro totale nel pool di ne aumenterà in modo che sia immediatamente deve aumentare la dimensione di eDTU (5). 
- Creare il nuovo pool flessibile con lo stesso nome e la stessa configurazione nell'area B per i database dei clienti valutazione (6). 
- Dopo aver creato il pool di utilizzare geografico Ripristina per ripristinare il database di singoli tenant di valutazione nel pool (7). È possibile valutare la possibilità di attivazione Ripristina singoli dalle connessioni per l'utente finale o utilizzare alcune altre combinazioni di priorità specifica applicazione.


> [AZURE.NOTE] L'operazione di failover è asincrono. Per ridurre al minimo il tempo di ripristino è importante eseguire il comando di failover dei database tenant in batch con almeno 20 database. 

A questo punto l'applicazione è in linea nell'area B. Tutti i clienti di pagamenti è possibile accedere ai propri dati durante la valutazione si verificherà ritardo quando si accede ai dati.

Quando viene ripristinato paese è necessario decidere se si desidera utilizzare l'area geografica B per i clienti di valutazione o failback all'uso del pool di valutazione clienti nell'area risposte. Un criterio potrebbe essere % del tenant di valutazione database modificati dopo il ripristino. Indipendentemente da questa decisione sarà necessario bilanciare il pagamento tenant tra due pool. diagramma seguente è illustrato il processo quando i database tenant di valutazione hanno esito negativo tornare all'area risposte.  
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annullare tutte le richieste di ripristino di geografico in attesa al pool DR versione di valutazione.   
- Failover il database di gestione (8). Dopo il ripristino dell'area principale precedente era diventato automaticamente secondario. A questo punto diventa primario nuovamente.  
- Selezionare quali tenant a pagamento database avrà esito negativo al pool 1 e avviare il controllo a loro dei secondari (9). Dopo il ripristino dell'area tutti i database nel pool 1 ha automaticamente dei secondari. A questo punto 50% di essi sarà nuovamente i colori. 
- Ridurre le dimensioni del pool 2 eDTU originale (10).
- Impostare tutte ripristinati valutazione database nell'area B di sola lettura (11).
- Per ogni database nel pool di DR versione di valutazione che sono stati modificati dopo il ripristino rinominare o eliminare il database corrispondente del pool principale versione di valutazione (12). 
- Copiare i database aggiornati dal pool di DR al pool principale (13). 
- Eliminare il pool di DR (14) 

I principali **vantaggi** di questa strategia sono:

- Supporta il contratto di servizio più efficiente per i clienti di pagamenti perché garantisce che un'interruzione del servizio non è possibile impatto più di 50% dei database tenant. 
- Garantisce che prove nuove vengono sbloccate come trail DR pool viene creato durante il recupero. 
- Consente di uso più efficiente delle capacità del pool come 50% del database secondari in pool 1 e il 2 sono necessariamente meno attivo quindi i database principali.

Di seguito sono elencati i principali **compromessi** :

- Le operazioni CRUD contro i database di gestione avrà latenza inferiore per gli utenti finali connessi all'area A rispetto per gli utenti finali connessi al paese B che verranno eseguite contro primario del database Gestione.
- Richiede l'esecuzione di più complessa struttura del database di gestione. Ad esempio, ogni record tenant avrebbe disporre di un tag di posizione che deve essere modificata durante failover e failback.  
- Il pagamento potrebbero verificarsi prestazioni ridotte rispetto alla visualizzazione consueta finché non viene completata l'aggiornamento del pool nell'area B. 

## <a name="summary"></a>Riepilogo

In questo articolo è incentrata su strategie di ripristino di emergenza per il livello di database utilizzato da un'applicazione multi-tenant SaaS ISV. Basare la strategia che scelta delle esigenze dell'applicazione, ad esempio il modello aziendale, il contratto di servizio che si desidera offrire ai clienti, definire il budget vincolo e così via... Ogni strategia descritto illustra i vantaggi e compromesso in modo che è possibile apportare la decisione appropriata. Inoltre, l'applicazione specifica conterrà probabilmente altri componenti Azure. Per esaminare le indicazioni di continuità aziendale e progettare il ripristino del livello di database con loro. Per ulteriori informazioni sulla gestione di ripristino delle applicazioni di database in Azure, fare riferimento alla [Progettazione soluzioni basate su cloud di emergenza](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
