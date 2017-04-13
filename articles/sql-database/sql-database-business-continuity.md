<properties
   pageTitle="Cloud continuità aziendale - database ripristino - Database SQL | Microsoft Azure"
   description="Informazioni su come Database SQL Azure supporta la continuità aziendale cloud e il ripristino del database e di mantenere le applicazioni critiche cloud in esecuzione."
   keywords="continuità aziendale, continuità aziendale cloud, ripristino del database, il ripristino del database"
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
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Panoramica di continuità aziendale con Database di SQL Azure

In questo argomento descrive le funzionalità che consente di Database SQL Azure la continuità aziendale e ripristino di emergenza. Fornisce opzioni, suggerimenti ed esercitazioni per il ripristino da eventi dannosi che potrebbero causare la perdita di dati o il database e applicazione diventi disponibile. La discussione include operazioni da eseguire quando un utente o errore dell'applicazione viene applicata l'integrità dei dati, un'area di Azure con un'interruzione del servizio o l'applicazione richiede manutenzione. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Caratteristiche di Database SQL che è possibile utilizzare per fornire continuità aziendale

Database SQL include alcune funzionalità di continuità aziendale, inclusi backup automatici e replica di database facoltativo. Ogni ha diverse caratteristiche per il tempo di recupero stimato (Inserisci) e potenziali perdite di dati per le transazioni recenti. Dopo aver stabilito queste opzioni, è possibile scegliere tra di esse - e, nella maggior parte dei casi, utilizzarli insieme per diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo di accettabile prima che l'applicazione viene ripristinata completamente dopo l'evento dannose, nell'obiettivo di tempo di ripristino (RTO). È anche necessario conoscere la maggior quantità di dati gli aggiornamenti recenti (intervallo di tempo) dell'applicazione tollerabile perdere durante il ripristino dopo l'evento dannose - l'obiettivo di punto di ripristino (rilasciato). 

Nella tabella seguente contiene un confronto di Inserisci rilasciato per i tre scenari più comuni.

| Funzionalità |  Livello di base | Livello standard  | Livello Premium |
|---|---|---|---|
| Scegliere in ora ripristinare dal backup | Qualsiasi punto di ripristino entro 7 giorni   | Qualsiasi punto di ripristino all'interno di 35 giorni  | Qualsiasi punto di ripristino all'interno di 35 giorni |
Geografico ripristino da un backup replicate geografico | Inserisci < 12h, rilasciato < 1H   | Inserisci < 12h, rilasciato < 1H   | Inserisci < 12h, rilasciato < 1H |
|Replica di geografico Active | Inserisci < 30s, rilasciato < 5s   | Inserisci < 30s, rilasciato < 5s | Inserisci < 30s, rilasciato < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>Utilizzare il backup del database per ripristinare un database

Database SQL esegue automaticamente una combinazione di backup completi settimanali, differenziale del database di backup ogni ora e log delle transazioni ogni cinque minuti per proteggere l'azienda da perdite di dati. Questi backup sono archiviati in archiviazione localmente ridondanti per 35 giorni per i database nei livelli di servizio Standard e Premium e sette giorni per i database nel livello di base del servizio, vedere [livelli di servizio](sql-database-service-tiers.md) per ulteriori informazioni sui livelli di servizio. Se il periodo di conservazione per il livello di servizio non soddisfa esigenze aziendali, è possibile aumentare il periodo di conservazione modificando [il livello di servizio](sql-database-scale-up.md). Il database completo e differenziale backup sono inoltre replicati in un [centro dati accoppiate](../best-practices-availability-paired-regions.md) di protezione contro un'interruzione del centro di dati, vedere [backup automatici del database](sql-database-automated-backups.md) per altri dettagli.

È possibile usare questi backup automatici del database per ripristinare un database da vari eventi dannosi, all'interno del centro dati e a un'altra data center. Usa backup automatici del database, l'ora di ripristino dipende diversi fattori tra cui il numero totale di database ripristino nella stessa regione contemporaneamente, le dimensioni del database, la dimensione registro delle transazioni e la larghezza di banda di rete. Nella maggior parte dei casi, i tempi di ripristino sono inferiore a 12 ore. Quando si ripristina a un'altra area dati, perdite di dati sono limitata su 1 ora per l'archiviazione geografico ridondanti di oraria differenziali. 

> [AZURE.IMPORTANT] Per recuperare utilizzando backup automatici, è necessario essere un membro del ruolo di collaboratore di SQL Server o il proprietario della sottoscrizione, vedere [RBAC: ruoli incorporati](../active-directory/role-based-access-built-in-roles.md). È possibile ripristinare tramite il portale di Azure, PowerShell o API REST. Non è possibile utilizzare Transact-SQL.

Utilizzare backup automatici come meccanismo di continuità e ripristino business se l'applicazione:

- Non è considerato missione critiche.
- Non è disponibile un contratto di servizio di associazione di conseguenza i tempi di inattività di 24 ore o allungare non darà responsabilità finanziaria.
- Perdere fino a un'ora di modifica è una perdita di dati accettabili ha una bassa percentuale di modifica dei dati (basse transazioni orarie). 
- Costo sono riservati. 

Se è necessario ripristino più rapido, utilizzare [La replica di geografico Active](sql-database-geo-replication-overview.md) (illustrata Avanti). Se è necessario essere in grado di recuperare dati da un periodo antecedente 35 giorni, è consigliabile archiviare il database regolarmente in un file BACPAC (un file contenente allo schema del database e i dati associati) archiviati in archiviazione blob Azure o in un'altra posizione desiderata. Per ulteriori informazioni su come creare un archivio di database coerenti, vedere [creare una copia del database](sql-database-copy.md) ed [esportare la copia del database](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Utilizzare la replica geografico attiva per ridurre i tempi di ripristino e limitare le perdite di dati associata a un ripristino

Oltre a utilizzare il backup del database per il ripristino del database in caso di un'interruzione di business, è possibile utilizzare [La replica di geografico Active](sql-database-geo-replication-overview.md) per configurare un database per database secondari leggibili fino a quattro aree di propria scelta. Questi database secondari mantenuti sincronizzati con il database primario utilizzando un meccanismo di replica asincrono. Questa funzionalità viene utilizzata per evitare interruzioni delle attività aziendali in caso di un'interruzione del centro di dati o durante l'aggiornamento dell'applicazione. La replica di geografico Active utilizzabili per migliorare le prestazioni di query per le query di sola lettura per gli utenti distribuiti in diverse località.

Se il database principale è in linea in modo imprevisto oppure è necessario disconnettere per attività di manutenzione, è possibile convertire rapidamente secondario per diventare principale (denominato anche caso di errore) e configurare le applicazioni per connettersi a appena alzati di livello principale. Con pianificato in caso di errore, non c'è alcun perdite di dati. Con un failover non pianificato, potrebbe essere certa perdita di dati per le transazioni molto recenti per le caratteristiche di replica asincrona. Dopo aver caso di errore, è possibile failback successivo, in base a un piano o l'interfaccia di dati è disponibile in modalità online. In tutti i casi, gli utenti esperienza utente di un periodo di inattività e necessario ristabilire la connessione. 

> [AZURE.IMPORTANT] Per utilizzare la replica di geografico Active, è necessario essere il proprietario della sottoscrizione o disporre delle autorizzazioni amministrative di SQL Server. È possibile configurare e failover tramite il portale di Azure, PowerShell o API REST utilizzando le autorizzazioni dell'abbonamento o Transact-SQL utilizzando le autorizzazioni in SQL Server.

Se l'applicazione soddisfa questi criteri, utilizzare la replica di geografico Active:

- È fondamentale importanza.
- Ha un contratto di servizio (contratto di servizio) che non consentono di 24 ore o più dei tempi di inattività.
- Tempo di inattività determinerà responsabilità finanziaria.
- Con una frequenza elevata di dati sia sufficientemente alto cambia e perdere un'ora di dati non è accettabile.
- Costi supplementari di replica di geografico active sono inferiori alla responsabilità finanziaria potenziali e perdita associata di business.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Ripristinare un database dopo un errore di utente o applicazione

* Nessuno è perfetto! Un utente potrebbe Elimina per sbaglio alcuni dati, inavvertitamente eliminare una tabella di importante o anche rimuovere un intero database. O un'applicazione potrebbe sovrascrivere accidentalmente le dei dati con dati non validi a causa di un problema dell'applicazione. 

In questo scenario, ecco le opzioni di ripristino.

### <a name="perform-a-point-in-time-restore"></a>Eseguire un ripristino in un momento

È possibile utilizzare backup automatici per recuperare una copia del database a un valido punto di noto in tempo, purché sia tempo entro il periodo di conservazione di database. Dopo il ripristino del database, è possibile sostituire il database originale con il database ripristinato o copiare i dati necessari i dati ripristinati nel database originale. Se il database utilizza la replica di geografico Active, è consigliabile copiare i dati necessari da copia ripristinata nel database originale. Se si sostituisce il database originale con il database ripristinato, sarà necessario riconfigurare e ripetere la sincronizzazione geografico replica attiva (che può richiedere molto tempo per un database di grandi dimensioni). 

Per ulteriori informazioni e per istruzioni dettagliate su come ripristinare un database su un punto nel tempo tramite il portale di Azure o tramite PowerShell, vedere [ripristinare in un momento](sql-database-recovery-using-backups.md#point-in-time-restore). È possibile recuperare tramite Transact-SQL.

### <a name="restore-a-deleted-database"></a>Ripristinare un database eliminato

Se il database viene eliminato, ma il server logico non è stato eliminato, è possibile ripristinare il database eliminato e il punto in cui è stato eliminato. Consente di ripristinare il backup del database nello stesso server SQL logici da cui è stato eliminato. È possibile ripristinarlo utilizzando il nome originale o fornire un nuovo nome o il database ripristinato.

Per ulteriori informazioni e per istruzioni dettagliate su come ripristinare un database eliminato tramite il portale di Azure o tramite PowerShell, vedere [ripristinare un database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore). Non è possibile ripristinare tramite Transact-SQL.

> [AZURE.IMPORTANT] Se il server logico viene eliminato, è possibile recuperare un database eliminato. 

### <a name="import-from-a-database-archive"></a>Importare da un archivio di database

Se si è verificato la perdita di dati di fuori del periodo di conservazione corrente per i backup automatici e si dispone di stato dell'archiviazione del database, è possibile [importare un file BACPAC archiviato](sql-database-import.md) in un nuovo database. A questo punto è possibile sostituire il database originale con database importato o copiare i dati necessari dai dati importati nel database originale. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Ripristinare un database a un'altra area dopo un'interruzione di centro dati delle vendite regionali Azure

<!-- Explain this scenario -->

Sebbene rari, un centro di dati di Azure può contenere un'interruzione. Quando si verifica un'interruzione, causa un'interruzione di azienda che potrebbe last solo alcuni minuti oppure potrebbe durare ore. 

- È possibile attendere che il database per riconnessione al termine dell'interruzione di centro dati. Funziona con le applicazioni che possono avere il database offline. Ad esempio un progetto di sviluppo o una versione di valutazione gratuita non è necessario allo svolgimento di costantemente. Quando un data center sono un'interruzione del servizio, non sanno quanto tempo interruzione a lungo termine, in modo che questa opzione è disponibile solo se il database non sono necessari per un po' di tempo.
- È anche possibile uno dei due il controllo a un'altra area dati se si utilizza la replica di geografico Active o recupera utilizzando il backup del database geografico ridondanti (geografico ripristino). Failover richiede solo pochi secondi, durante il recupero da un backup richiede ore.

Quando si richiede un'azione, il tempo necessario per recuperare e quantità perdite di dati si verifica in caso di un'interruzione del centro dati dipendono da come si decide di usare le caratteristiche di continuità aziendale in precedenza nell'applicazione. In effetti, è possibile scegliere di utilizzare una combinazione di backup del database e Attiva replica geografico in base alle esigenze dell'applicazione. Per una descrizione delle considerazioni sulla progettazione di applicazioni per i database autonomi e per i pool flessibile con queste caratteristiche di continuità aziendale, vedere [Progettazione un'applicazione di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Nelle sezioni seguenti offrono una panoramica dei passaggi per recuperare utilizzando il backup del database o la replica di geografico Active. Per istruzioni dettagliate su inclusa la pianificazione requisiti, operazioni di ripristino di post e informazioni su come simulare un'interruzione del servizio per eseguire un drill di ripristino di emergenza, vedere [ripristinare un Database SQL da un'interruzione](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparare l'ambiente per un'interruzione del servizio

Indipendentemente da utilizzare la caratteristica continuità aziendale, è necessario:

- Identificare e preparare il server di destinazione, inclusi le regole del firewall a livello di server, gli account di accesso e le autorizzazioni a livello di database master.
- Determinare la modalità di reindirizzamento client e le applicazioni client al nuovo server
- Altre dipendenze, ad esempio il controllo di avvisi e le impostazioni del documento 
 
Se non si prevede e preparare correttamente, portare le applicazioni online in caso di errore o un ripristino può richiedere più tempo ed è probabile che richiedono anche risoluzione dei problemi relativi alla volta di sottolineare - una combinazione non valida.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Il controllo a un database secondario replicato geografico 

Se si utilizza la replica di geografico Active come meccanismo di ripristino, [forzare un il controllo a secondario replicato geografico](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). In pochi secondi, quello secondario promossi per nuovo principale ed è pronto per registrare le nuove transazioni e rispondere a eventuali quesiti - con pochi secondi perdita di dati per i dati che non ha ancora replicati. Per informazioni su come automatizzare il processo di failover, vedere [Progettazione un'applicazione di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Se si torna online centro dati, è possibile failback su primario originale (o meno).

### <a name="perform-a-geo-restore"></a>Eseguire un ripristino geografico 

Se si usa automatiche backup con replica di spazio di archiviazione ridondanti geografico come meccanismo di ripristino, [avviare il ripristino di un database utilizzando geografico Ripristina](sql-database-disaster-recovery.md#recover-using-geo-restore). Ripristino avviene di solito entro 12 ore, con una perdita di dati di fino a un'ora determinata dalle quando l'ultimo backup differenziale orario con adottate e replicato. Fino a termine del ripristino, il database non riesce a tutte le transazioni o rispondere a eventuali quesiti. 

> [AZURE.NOTE] Se il centro dati ritorna in linea prima di trasferire l'applicazione al database recuperato, è possibile annullare semplicemente il ripristino.  

### <a name="perform-post-failover--recovery-tasks"></a>Eseguire post failover / operazioni di ripristino 

Dopo il recupero da uno dei due meccanismo di recupero, è necessario effettuare le seguenti operazioni aggiuntive prima gli utenti e applicazioni siano nuovamente in esecuzione:

- Reindirizzamento client e le applicazioni client per il nuovo server e database ripristinato
- Assicurarsi che le regole firewall server a livello appropriato sono attive per gli utenti connettere (oppure utilizzare [firewall a livello di database](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Verificare che gli account di accesso appropriato e le autorizzazioni a livello di database master sono attive (o utilizzare [contenuto gli utenti](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurare controllo, in base alle esigenze
- Configurazione di avvisi, in base alle esigenze

## <a name="upgrade-an-application-with-minimal-downtime"></a>Aggiornare un'applicazione con tempo di inattività minimo

A volte un'applicazione per impostare la modalità offline a causa di manutenzione pianificata, ad esempio un aggiornamento dell'applicazione. [Gestire gli aggiornamenti delle applicazioni](sql-database-manage-application-rolling-upgrade.md) viene descritto come utilizzare la replica di geografico attiva per abilitare gli aggiornamenti in sequenza dell'applicazione cloud per ridurre al minimo il tempo di inattività durante l'aggiornamento e specificare un percorso di recupero in caso di errori. In questo articolo esamina due diversi metodi di coordinazione il processo di aggiornamento e vengono illustrati i vantaggi e compromessi delle singole opzioni.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione delle considerazioni sulla progettazione di applicazioni per i database autonomi e per i pool flessibile, vedere [Progettazione un'applicazione di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






