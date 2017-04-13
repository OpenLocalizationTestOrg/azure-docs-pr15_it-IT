<properties
   pageTitle="Soluzioni di ripristino di emergenza - SQL Database attiva geografico replica cloud | Microsoft Azure"
   description="Informazioni su come progettare soluzioni di ripristino di emergenza cloud per pianificazione continuità aziendale usando replica geografico per il backup dei dati app con Database di SQL Azure."
   keywords="cloud di emergenza, le soluzioni di ripristino di emergenza, il backup dei dati di app, replica di geografico, pianificazione continuità aziendale"
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
   ms.workload="data-management"
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Progettare un'applicazione di emergenza cloud usando la replica di geografico Active in Database SQL


> [AZURE.NOTE] [La replica di geografico Active](sql-database-geo-replication-overview.md) è ora disponibile per tutti i database in tutti i livelli.



Informazioni su come utilizzare [La replica di geografico Active](sql-database-geo-replication-overview.md) in Database SQL di database le applicazioni flessibili internazionali errori e irreversibile interruzioni. Per la pianificazione della continuità aziendale, prendere in considerazione la topologia applicazione di distribuzione, contratto di servizio di destinazione, la latenza di traffico e dei costi. In questo articolo è esaminare i modelli di applicazione comuni e illustrati i vantaggi e compromessi delle singole opzioni. Per informazioni sulle Active geografico replica con i pool flessibile, vedere [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modello di progettazione 1: distribuzione attivo passivo di emergenza cloud con un database collocato

Questa opzione è più adatta per le applicazioni con le caratteristiche seguenti:

+ Istanza attiva in una singola regione Azure
+ Dipendenza sicura ai dati dell'accesso in lettura / scrittura (RW)
+ La connettività tra area tra la logica dell'applicazione e il database non è accettabile a causa di latenza e il traffico costo    

In questo caso, topologia applicazione di distribuzione è ottimizzata per la gestione di emergenza internazionali quando tutti i componenti di applicazione sono interessati ed è necessario failover come unità. Per la ridondanza geografica, la logica dell'applicazione e il database vengono replicate in un'altra area ma non vengono utilizzate per il carico di lavoro dell'applicazione in condizioni normali. Configurare l'applicazione nell'area secondaria per l'utilizzo di una stringa di connessione SQL al database secondario. Gestore del traffico sia configurato per utilizzare [metodo routing failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Gestore del traffico Azure](../traffic-manager/traffic-manager-overview.md) viene utilizzata in questo articolo solo a titolo esemplificativo. È possibile utilizzare qualsiasi soluzione di bilanciamento del carico che supporta metodo routing failover.    

Oltre a istanze principale dell'applicazione è necessario considerare la distribuzione di una piccola [applicazione ruolo di lavoro](cloud-services-choose-me.md#tellmecs) che controlla il database primario inviando periodici comandi (sola lettura) sola lettura T-SQL. È possibile utilizzare per attivare automaticamente il failover, per generare un avviso nella console di amministrazione dell'applicazione o di entrambe. Per assicurarsi che il monitoraggio non subisca interruzioni a livello di area geografica, è necessario distribuire le istanze dell'applicazione monitoraggio in ogni regione e avere ogni istanza connesso al database primario nell'area principale e il database secondario nell'area locale. 

> [AZURE.NOTE] Entrambe le applicazioni di monitoraggio devono essere attivo e la verifica dei database primari e secondari. Quest'ultimo può utilizzato per rilevare un errore nell'area secondario e avviso quando l'applicazione non è protetto.     

Il diagramma seguente illustra la configurazione prima di un'interruzione.

![Configurazione di replica con SQL Database geografico. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Dopo un'interruzione nell'area principale, l'applicazione di monitoraggio rileva che il database primario non è possibile accedere e registra un avviso. A seconda del contratto di servizio dell'applicazione, è possibile decidere quante ricerche monitoraggio errori consecutive avrà esito negativo prima dichiara un'interruzione del database. Per ottenere una coordinata failover del punto finale dell'applicazione e del database, è necessario disporre dell'applicazione monitoraggio eseguire le operazioni seguenti:

1. [Aggiornare lo stato del punto finale principale](https://msdn.microsoft.com/library/hh758250.aspx) per l'attivazione del failover punto finale.
2. Chiamare il database secondario per [avviare failover di database](sql-database-geo-replication-portal.md).

Dopo il failover, l'applicazione elaborerà le richieste di utenti presenti nell'area secondario, ma rimarrà collocato con il database perché il database primario sarà ora nell'area secondario. Questo scenario è illustrato nel diagramma successivo. In tutti i diagrammi, le linee continue indicano connessioni attive, linee tratteggiate indicano connessioni sospese e segni di tabulazione indicano i trigger di azione.


![Geografico replica: Il controllo a database secondario. Backup dei dati di App.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

In caso di un'interruzione nell'area secondario, il collegamento di replica tra principale e il database secondario viene sospesa e applicazione di monitoraggio registra un avviso che il database primario viene esposta. Non rallentare le prestazioni dell'applicazione, ma l'applicazione opera esposti e pertanto rischio maggiore in caso entrambe aree geografiche non in sequenza.

> [AZURE.NOTE] È consigliabile solo le configurazioni di distribuzione con una singola regione DR. In questo modo la maggior parte delle aree geografiche Azure ha due aree. Queste configurazioni verranno non impedisce l'applicazione irreversibile di entrambe aree geografiche. In caso di errore di guasto, è possibile ripristinare i database in un'area di terzo tramite [l'operazione di ripristino geografico](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Dopo l'interruzione è ridotto, database secondario viene sincronizzato automaticamente con primario. Durante la sincronizzazione, le prestazioni dei primario Impossibile interessate leggermente a seconda della quantità di dati che devono essere sincronizzata. Il diagramma seguente illustra un'interruzione nell'area secondario.

![Database secondario sincronizzato con principale. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


I principali **vantaggi** di questo modello di progettazione sono:

+ Stringa di connessione SQL viene impostata durante la distribuzione dell'applicazione in ogni regione e non cambia dopo il failover.
+ Le prestazioni dell'applicazione non subisca failover dell'applicazione e il database si trovano sempre condivisa.

Il principale **svantaggio** è che l'istanza dell'applicazione ridondanti presenti nell'area secondario viene utilizzato solo per il ripristino di emergenza.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modello di progettazione 2: distribuzione attivo attivo bilanciamento del carico applicazioni
Questa opzione di ripristino di emergenza cloud è più adatta per le applicazioni con le caratteristiche seguenti:

+ Rapporto elevato di database legge di scrittura
+ Latenza di scrittura database non ha effetti dell'utente finale  
+ Logica di sola lettura può essere separata dalla logica di lettura e scrittura utilizzando una stringa di connessione diverso
+ Logica di sola lettura non dipendono dai dati che viene sincronizzati con gli aggiornamenti più recenti  

Se le applicazioni dispongono di queste caratteristiche, bilanciamento del carico le connessioni degli utenti finali tra più istanze dell'applicazione in diverse aree geografiche possibile migliorare le prestazioni e l'esperienza utente finale. Per implementare il bilanciamento del carico, ogni area deve essere un'istanza attiva dell'applicazione con la logica di sola lettura (RW) connessa al database primario nell'area principale. La logica di sola lettura (sola lettura) deve essere connesso a un database secondario nella stessa regione istanza dell'applicazione. Gestore del traffico deve essere configurato per utilizzare [circolari routing](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) o [prestazioni routing](../traffic-manager/traffic-manager-configure-performance-routing-method.md) con [endpoint monitoraggio](../traffic-manager/traffic-manager-monitoring.md) abilitato per ogni istanza dell'applicazione.

Come modello #1, è opportuno distribuire un'applicazione di monitoraggio simile. Diversamente da quanto succede motivo #1, l'applicazione di monitoraggio non verrà tuttavia responsabile dell'attivazione failover punto finale.

> [AZURE.NOTE] Durante questo modello Usa più di un database secondario, solo uno dei database secondario verrebbe utilizzato per failover per i motivi indicati in precedenza. Poiché questo modello richiede l'accesso in sola lettura a quello secondario, è necessario replica di geografico Active.

Gestore del traffico deve essere configurato per prestazioni ottimali routing per indirizzare le connessioni utente all'istanza dell'applicazione più vicina alla posizione geografica dell'utente. Il diagramma seguente illustra la configurazione prima di un'interruzione.
![Nessuna interruzione: prestazioni routing alle più vicino di applicazione. Replica geografico.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se un'interruzione del database viene rilevato nell'area principale, si avvia failover del database primario a una delle regioni secondarie, la modifica del percorso del database primario. Gestione di traffico automaticamente esclude endpoint non in linea dalla tabella di routing, ma continua routing il traffico degli utenti finali alle altre varianti online. Poiché il database primario è ora in un'area diversa, tutte le istanze online necessario modificare gli stringa di connessione in lettura / scrittura SQL per connettersi al nuovo principale. È importante avere apportato questa modifica prima di iniziare il failover di database. Le stringhe di connessione SQL di sola lettura devono rimanere invariate come puntano sempre a un database nella stessa regione. La procedura di failover è:  

1. Modificare le stringhe di connessione di SQL in lettura / scrittura in modo che puntino nel nuovo database primario.
2. Chiamare il database secondario designato per poter [avviare database failover](sql-database-geo-replication-portal.md).

Il diagramma seguente illustra la configurazione di nuova dopo il failover.
![Configurazione dopo il failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

In caso di un'interruzione in una delle regioni secondarie, il responsabile di traffico verranno rimosse automaticamente endpoint offline in quell'area dalla tabella di routing. Il canale di replica al database secondario in quell'area è sospesa. Perché le altre aree il traffico di altri utenti in questo scenario, le prestazioni dell'applicazione sono interessata durante il periodo di inattività. Dopo l'interruzione è ridotto, il database secondario nell'area interessata viene immediatamente sincronizzato con primario. Durante la sincronizzazione delle prestazioni dell'istanza primaria Impossibile interessate leggermente a seconda della quantità di dati che devono essere sincronizzata. Il diagramma seguente illustra un'interruzione in una delle regioni secondarie.

![Interruzione del servizio nell'area secondario. Cloud di emergenza - replica geografico.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Il **vantaggio** di questo modello di progettazione è possibile ridimensionare il carico di lavoro di applicazione tra più database secondari per ottenere prestazioni ottimali degli utenti finali. Di seguito sono riportati i **compromessi** di questa opzione:

+ Lettura e scrittura connessioni tra le istanze dell'applicazione e database sono diversi latenza e costo
+ Le prestazioni dell'applicazione impatto durante il periodo di inattività
+ Istanze dell'applicazione sono necessari per modificare in modo dinamico la stringa di connessione SQL dopo il failover di database.  

> [AZURE.NOTE] Trasferire carichi di lavoro specifico, ad esempio segnalazione processi, strumenti di business intelligence o l'esecuzione di backup, è possibile utilizzare un approccio simile. In genere, questi carichi di lavoro utilizzano risorse significativa del database si consiglia di designare uno dei database secondari per poterli con il livello di prestazioni trovata una corrispondenza per il carico di lavoro previsto.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modello di progettazione 3: distribuzione attivo passivo per la conservazione dei dati  
Questa opzione è più adatta per le applicazioni con le caratteristiche seguenti:

+ Le perdite di dati sono aziendale ad alto rischio. Il database può solo essere utilizzato come ultima risorsa se l'interruzione è permanente.
+ L'applicazione può essere utilizzato in "modalità di sola lettura" per un periodo di tempo.

In questo modello, l'applicazione passa alla modalità di sola lettura quando la connessione al database secondario. La logica dell'applicazione nell'area principale è condivisa con il database primario e funziona in modalità di lettura e scrittura (RW). La logica dell'applicazione nell'area secondario è condivisa Trova con il database secondario ed è pronta per l'uso in modalità di sola lettura (sola lettura).  Gestore del traffico deve essere configurato per l'uso del [routing failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) con [endpoint monitoraggio](../traffic-manager/traffic-manager-monitoring.md) abilitato per entrambe le istanze dell'applicazione.

Il diagramma seguente illustra la configurazione prima di un'interruzione.
![Distribuzione di attivo passivo prima del failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando il responsabile del traffico rileva un errore di connettività all'area principale, passa automaticamente il traffico utente all'istanza dell'applicazione nell'area secondario. Con questo modello, è importante che è **non** avviare failover database dopo l'interruzione viene rilevato. L'applicazione nell'area secondario viene attivato e viene eseguito in modalità di sola lettura utilizzando il database secondario. Come illustrato nel diagramma seguente.

![Servizio: Applicazione in modalità di sola lettura. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Dopo l'interruzione dell'area principale è ridotto, il traffico manager rileva il ripristino di connettività nell'area principale e passa il traffico utente tornare all'istanza dell'applicazione nell'area principale. Istanza di tale applicazione curriculum e funziona in modalità di lettura e scrittura utilizzando il database primario.

> [AZURE.NOTE] Poiché questo modello richiede l'accesso in sola lettura a quello secondario richiede l'esecuzione della replica geografico attivo.

In caso di un'interruzione nell'area secondario, il responsabile di traffico endpoint applicazione nell'area principale viene contrassegnato come ridotto e il canale di replica è sospesa. Tuttavia, questa interruzione non influire sulle prestazioni dell'applicazione durante il periodo di inattività. Dopo l'interruzione è ridotto, il database secondario viene immediatamente sincronizzato con primario. Durante la sincronizzazione delle prestazioni dell'istanza primaria Impossibile interessate leggermente a seconda della quantità di dati che devono essere sincronizzata.

![Interruzione del servizio: Database secondario. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Questo modello di progettazione offre molti **vantaggi**:

+ Evitare la perdita di dati durante interruzioni temporanee.
+ Non richiede di distribuire un'applicazione di monitoraggio durante il recupero viene attivato da gestore del traffico.
+ Tempo di inattività solo dipende rapidità gestore del traffico rileva errori connettività, che può essere configurato.

**Compromessi** sono:

+ Applicazione deve essere in grado di funzionare in modalità di sola lettura.
+ È necessario passare a tale dinamicamente quando è connesso a un database di sola lettura.
+ Il ripristino delle operazioni di lettura e scrittura richiede il ripristino dell'area principale, che indica che l'accesso ai dati completo può essere disabilitata per ore o addirittura giorni.

> [AZURE.NOTE] In caso di un'interruzione del servizio permanente nell'area, è necessario attivare database failover manualmente e accettare la perdita di dati. L'applicazione funzioneranno nell'area secondario con l'accesso in lettura / scrittura per il database.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Pianificazione di continuità aziendale: scegliere progettazione di un'applicazione di emergenza cloud

Strategia di ripristino di emergenza cloud specifici possibile combinare o estendere questi modelli di progettazione in base alle esigenze dell'applicazione.  Come detto in precedenza, la strategia che scelta dipende dal contratto di servizio desiderato per l'offerta per i clienti e topologia applicazione di distribuzione. Per facilitare la decisione, nella tabella seguente contiene un confronto di scelte in base alla perdita di dati stimata o ripristino scegliere obiettivo (rilasciato) e il tempo di ripristino (Inserisci) stimato.

| Motivo | RILASCIATO | INSERISCI
| :--- |:--- | :---
| Distribuzione attivo passivo di emergenza con accesso al database collocati | Accesso in lettura / scrittura < 5 sec | I tempi di rilevamento errore + API failover chiamare verifica delle applicazioni di test
| Distribuzione attivo attivo bilanciamento del carico applicazioni | Accesso in lettura / scrittura < 5 sec | I tempi di rilevamento errore failover API chiamata stringa di connessione SQL modificare + verifica delle applicazioni di test
| Distribuzione attivo passivo per la conservazione dei dati | Accesso in sola lettura < 5 sec accesso in lettura / scrittura = zero | Accesso in sola lettura = connettività errore rilevamento tempo + test di verifica dell'applicazione <br>Accesso in lettura / scrittura = tempo per ridurre il periodo di inattività

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
- Per informazioni sull'utilizzo di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md)
- Per informazioni sulle Active geografico replica con i pool flessibile, vedere [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
