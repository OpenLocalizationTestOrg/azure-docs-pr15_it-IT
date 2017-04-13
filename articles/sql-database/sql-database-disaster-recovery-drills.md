<properties 
   pageTitle="Esercitazioni di ripristino di emergenza Database SQL | Microsoft Azure" 
   description="Informazioni su Guida e procedure consigliate per l'uso di Database SQL Azure per eseguire esercitazioni di ripristino di emergenza che aiuta a mantengono la missione critiche alle applicazioni aziendali flessibili di errori e interruzioni." 
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
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Esecuzione di drill-down ripristino di emergenza

È consigliabile che viene eseguita periodicamente convalida della conformità delle applicazioni per flusso di lavoro di ripristino. Verificare il comportamento dell'applicazione e le implicazioni di perdita di dati e/o le interruzioni a altro comporta il tale failover è buona norma ingegneristica. È anche un requisito per la maggior parte delle standard come parte della certificazione di continuità aziendale.

Eseguire un drill di ripristino di emergenza include:

- Simulare interruzione del livello di dati
- Il ripristino 
- Convalidare Ripristino applicazioni di integrità dei post

A seconda di come è [progettato l'applicazione per la continuità aziendale](sql-database-business-continuity.md), può variare il flusso di lavoro per eseguire il drill-down. Di seguito vengono descritte le procedure consigliate partecipando a una drill di ripristino di emergenza nel contesto di Database SQL Azure. 

##<a name="geo-restore"></a>Ripristino geografico

Per evitare perdite di dati durante l'esecuzione di un trapano di ripristino di emergenza, è consigliabile eseguire il drill-down in un ambiente di testing mediante la creazione di una copia dell'ambiente di produzione e usarlo per verificare il flusso di lavoro failover dell'applicazione.
 
####<a name="outage-simulation"></a>Simulazione interruzione

Per simulare l'interruzione è possibile eliminare o rinominare i database di origine. In questo modo errore connettività dell'applicazione. 

####<a name="recovery"></a>Ripristino

- Eseguire il ripristino geografico del database in un altro server come descritto [di seguito](sql-database-disaster-recovery.md). 
- Modificare la configurazione dell'applicazione per connettersi al database recuperati e seguire la Guida di [configurare un database dopo il recupero](sql-database-disaster-recovery.md) per completare il recupero.

####<a name="validation"></a>Convalida

- Eseguire il drill-down verificando il ripristino di post di integrità di applicazione (ad esempio stringhe di connessione, gli account di accesso, test della funzionalità di base o in altri convalida parte procedure conclusioni applicazione standard).

##<a name="geo-replication"></a>Replica geografico

Per un database è protetto tramite la replica geografico esercizio drill comporta pianificato il controllo a database secondario. Failover pianificato rimangono che primario e secondari database aggiornati quando i ruoli. Diversamente da quanto succede failover non pianificato questa operazione non comporterà la perdita di dati, in modo che il drill-down può essere eseguita nell'ambiente di produzione. 

####<a name="outage-simulation"></a>Simulazione interruzione

Per simulare l'interruzione che è possibile disattivare l'applicazione web o la macchina virtuale connessi al database. Verrà creato i problemi di connettività per i client web.

####<a name="recovery"></a>Ripristino

- Assicurarsi che la configurazione dell'applicazione nell'area DR punta a secondario ex che diventerà completamente accessibile primaria. 
- Eseguire [pianificato failover](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) per rendere il database secondario un nuovo database primario
- Seguire la Guida di [configurare un database dopo il recupero](sql-database-disaster-recovery.md) per completare il recupero.

####<a name="validation"></a>Convalida

- Eseguire il drill-down verificando il ripristino di post di integrità di applicazione (ad esempio stringhe di connessione, gli account di accesso, test della funzionalità di base o in altri convalida parte procedure conclusioni applicazione standard).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli scenari di continuità aziendale, vedere [scenari di continuità](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [backup automatizzato di Database SQL](sql-database-automated-backups.md)
- Per informazioni sull'utilizzo di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Attivo geografico replica](sql-database-geo-replication-overview.md)  
