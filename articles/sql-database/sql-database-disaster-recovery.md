<properties
   pageTitle="Ripristino di Database SQL | Microsoft Azure"
   description="Informazioni su come ripristinare un database da un'interruzione del data center locale con Azure SQL attiva geografico-replica di Database e funzionalità di ripristino geografico."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Ripristinare un Database SQL Azure o failover secondario

Database SQL Azure offre le seguenti funzionalità per il ripristino da un'interruzione del servizio:

- [Replica di geografico Active](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-recovery-using-backups.md#point-in-time-restore)

Per informazioni sugli scenari di continuità aziendale e le caratteristiche di questi scenari di supporto, vedere [la continuità aziendale](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Preparazione per l'evento di un'interruzione

Per il successo con il ripristino per un'altra area dati utilizzando la replica di geografico Active o geografico ridondanti backup, è necessario preparare un server di un'altra data center interruzione per imparare a nuovo server primario in caso di necessità si verificano come definiti anche i passaggi descritti e testati per garantire un ripristino smussato. Questa procedura di preparazione include:

- Identificare il server logico in un'altra area per imparare a nuovo server primario. Con Active geografico replica, questo saranno almeno e forse ogni server secondario. Per geografico ripristino, in genere corrisponderà a un server nell' [area accoppiata](../best-practices-availability-paired-regions.md) per l'area geografica in cui si trova il database.
- Identificare e definire, le regole del firewall a livello di server necessari in per gli utenti di accedere al nuovo database principale.
- Stabilire come si prevede di reindirizzare la chiamata agli utenti di nuovo server primario, ad esempio modificando le stringhe di connessione o modificando le voci DNS.
- Identificare e facoltativamente creare gli account di accesso che deve essere presente nel database master nel nuovo server primario e assicurarsi che tali account di accesso dispone delle autorizzazioni appropriate nel database master, se presenti. Per ulteriori informazioni, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md)
- Identificare le regole di avviso che dovranno essere aggiornati per eseguire il mapping per il nuovo database primario.
- Configurazione del controllo sul database primaria corrente del documento
- Eseguire un [ripristino di emergenza eseguire il drill](sql-database-disaster-recovery-drills.md). Per simulare un'interruzione del servizio per il ripristino geografico, è possibile eliminare o rinominare i database di origine per causare un errore di connettività di applicazione. Per simulare un'interruzione del servizio per Active geografico replica, è possibile disattivare l'applicazione web o macchina virtuale connessi al database o failover il database per provocare errori connectity applicazione.

## <a name="when-to-initiate-recovery"></a>Quando avviare un ripristino

L'operazione di ripristino influisce su dell'applicazione. Richiede la modifica della stringa di connessione SQL o il reindirizzamento utilizzando il DNS e potrebbe causare la perdita di dati permanenti. Di conseguenza, deve essere eseguita solo quando l'interruzione è soggetto a durata non superiore a obiettivo ora ripristino dell'applicazione. Quando viene distribuita nell'ambiente di produzione è necessario controllare periodicamente dello stato delle applicazioni e usare i seguenti punti di dati dichiarare che è necessaria l'esecuzione di ripristino:

1.  Errore di connettività permanente dal livello applicazione al database.
2.  Portale di Azure viene visualizzato un avviso relativo a un evento imprevisto nell'area con un ampio impatto.
3.  Il server di Database SQL Azure viene contrassegnato come ridotto.

A seconda del tolleranza applicazione per il tempo di inattività e i possibili responsabilità di business è possibile prendere in considerazione le seguenti opzioni di ripristino.

Usare il [Database ripristinabili ottenere](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) per ottenere il punto di ripristino replicato geografico più recente.

## <a name="wait-for-service-recovery"></a>Attendere il ripristino del servizio

Il lavoro team Azure attentamente per ripristinare la disponibilità dei servizi esempio rapidamente possibili ma a seconda radice può causare può richiedere ore o giorni.  Se l'applicazione è in grado di tollerare tempi di inattività significativi che semplicemente è possibile attendere il ripristino completare. In questo caso è richiesta alcuna azione da parte dell'utente. È possibile visualizzare lo stato corrente dei servizi sul nostro [Azure nel Dashboard integrità dei servizi](https://azure.microsoft.com/status/). Dopo il ripristino dell'area della disponibilità dell'applicazione verranno ripristinate.

## <a name="failover-to-geo-replicated-secondary-database"></a>Il controllo a replicato geografico database secondario

Se il tempo di inattività dell'applicazione causando responsabilità di business dovrebbe essere uso database replicato geografico nell'applicazione. Verrà attivata l'applicazione ripristinare rapidamente disponibilità in un'area diversa in caso di un'interruzione. Informazioni su come [configurare la replica geografico](sql-database-geo-replication-portal.md).

Per ripristinare la disponibilità dei database è necessario avviare il failover secondario replicato geografico utilizzando uno dei metodi supportati.

Utilizzare uno dei seguenti guide per il controllo a un database secondario replicato geografico:

- [Il controllo a secondario geografico replicato nel portale di Azure](sql-database-geo-replication-portal.md)
- [Il controllo a secondario replicato geografico tramite PowerShell](sql-database-geo-replication-powershell.md)
- [Il controllo a secondario replicato geografico mediante T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Recuperare utilizzando geografico Ripristina

Se il tempo di inattività dell'applicazione non comporta la responsabilità di business è possibile utilizzare geografico ripristino di un metodo per recuperare i database dell'applicazione. Crea una copia del database della copia di backup ridondanti geografico più recente.

Utilizzare uno dei seguenti guide per geografico Ripristina un database in una nuova area:

- [Geografico-Ripristina un database a una nuova area nel portale di Azure](sql-database-geo-restore-portal.md)
- [Geografico-Ripristina un database a una nuova area tramite PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurare il database dopo il ripristino

Se si usa failover geografico replica o Ripristina geografico per recuperare un'interruzione del servizio, è necessario assicurarsi che la connessione ai nuovi database sia configurata correttamente in modo che è possibile riprendere la funzione applicazione normale. Si tratta di un elenco di controllo delle attività per preparare il produzione database ripristinato.

### <a name="update-connection-strings"></a>Aggiornare le stringhe di connessione

Poiché il database recuperato si trova in un server diverso, è necessario aggiornare la stringa di connessione dell'applicazione in modo che punti al server.

Per ulteriori informazioni sulla modifica delle stringhe di connessione, vedere il linguaggio di sviluppo appropriata per la [raccolta di connessioni](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurare le regole Firewall

È necessario assicurarsi che le regole del firewall configurate sul server e sul database corrispondano a quelli che sono stati configurati nel server primario e nel database principale. Per ulteriori informazioni, vedere [How to: configurare le impostazioni di un Firewall (Database di SQL Azure)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurare gli account di accesso e gli utenti del Database

È necessario assicurarsi che tutti gli account utilizzati da un'applicazione esistano nel server che ospita i database ripristinato. Per ulteriori informazioni, vedere [Configurazione di sicurezza per la replica geografico](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Configurare e testare le regole firewall server e gli account di accesso (e le relative autorizzazioni) durante il drill-un ripristino di emergenza. Questi oggetti a livello di server e la configurazione potrebbe non essere disponibili durante il periodo di inattività.

### <a name="setup-telemetry-alerts"></a>Avvisi di telemetria di configurazione

È necessario assicurarsi che le impostazioni di regola di avviso esistenti vengono aggiornate per eseguire il mapping al database recuperato e altro server.

Per ulteriori informazioni sulle regole di avviso di database, vedere [Le notifiche di avviso visualizzato](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [L'integrità dei servizi di rilevamento](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Attivare il controllo

Se il controllo è necessaria per accedere al database, è necessario attivare il controllo dopo il ripristino del database. Un buon indicatore che sia necessario un controllo è applicazioni client di utilizzare le stringhe di connessione sicura in una serie di *. database.secure.windows.net. Per ulteriori informazioni, vedere [iniziare a utilizzare il controllo dei database SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e il ripristino di continuità aziendale, vedere [scenari di continuità](sql-database-business-continuity.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
