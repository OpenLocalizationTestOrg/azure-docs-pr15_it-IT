<properties 
   pageTitle="Database SQL Azure domande frequenti" 
   description="Risposte ai clienti di domande comuni domande su cloud database e Database di SQL Azure, database relazionali gestione sistema Microsoft (RDBMS) e database come servizio nel cloud." 
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
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Database SQL di domande frequenti

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Come dell'utilizzo del Database di SQL vengono visualizzate in sulla fattura? 
Fatture di Database SQL in una tariffa oraria prevedibile in base a livello del servizio + livello delle prestazioni per database singoli o eDTUs al pool di database flessibile. Utilizzo effettivo viene calcolato e proporzionale ogni ora, in modo che la fattura risulti frazioni di un'ora. Ad esempio, se esiste un database per 12 ore in un mese, la fattura Mostra l'utilizzo di giorni che vanno 0,5. Inoltre, livelli di servizio + livello di prestazioni ed eDTUs al pool non vengono visualizzati nella fattura per rendere più semplice visualizzare il numero di giorni che vanno database utilizzato per ogni in un solo mese.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Cosa fare se un solo database è attivo per meno di un'ora o utilizza un livello superiore di servizio per meno di un'ora?
Vengono addebitate per ogni ora esiste un database utilizzando il livello di servizio massimo + livello di prestazioni che applicati in quell'ora, indipendentemente dall'utilizzo o se il database è stato attivo per meno di un'ora. Ad esempio, se si crea un database ed eliminarla cinque minuti dopo la fattura riflette un costo per ora di un database. 

Esempi
    
- Se si crea un database di base e quindi aggiornare immediatamente a S1 Standard, addebitate alla tariffa Standard S1 per la prima ora.

- Se si aggiorna un database da Basic a Premium alle 10:00 e al termine dell'aggiornamento ore 1:35. il giorno seguente addebitate alla tariffa Premium iniziando 01:00. 

- Se si eseguire il downgrade un database da Premium in base alle 11:00 e che venga completato 2:15 PM, quindi il database viene calcolato il tasso di Premium fino a 3:00 p.m., trascorso il quale viene addebitata ai tassi di base.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Come utilizzo del pool di database flessibile vengono visualizzate nella fattura e cosa succede quando si cambia il eDTUs al pool?
In base alle tariffe pool database flessibile visualizzata nella fattura come DTUs flessibile (eDTUs) in incrementi indicati in eDTUs al pool nella [pagina prezzo](https://azure.microsoft.com/pricing/details/sql-database/). È gratuito per database per i pool di database flessibile. Vengono addebitate per ogni ora che un pool di esiste nel eDTU più alti, indipendentemente dall'utilizzo o se il pool era attivo per meno di un'ora. 

Esempi

- Se si crea un pool di database flessibile Standard con 200 eDTUs ore 11:18, aggiunta di cinque database al pool di addebitate per 200 eDTUs ora intera, a partire dalle 10.00 tramite il resto del giorno.
- In 2 giorni, ore 5:05, 1 Database inizia a utilizzare 50 eDTUs e resta bloccato all'interno della giornata. Database 2-5 variare compreso tra 0 ed 80 eDTUs. Durante la giornata, aggiungere cinque altri database che utilizzano eDTUs variabile nell'intera giornata. Giorno 2 è un'intera giornata fatturata eDTU 200. 
- Il giorno 3, 5 ore aggiungere un altro database 15. Utilizzo del database aumenta al giorno per il punto in cui si decide di aumentare eDTUs per il pool da 200 a 400 ore 8:05. In base alle tariffe a livello di 200 eDTU attive fino a 8 pm e aumentare a 400 eDTUs per 4 ore rimanenti. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Come l'utilizzo di Active geografico replica in un pool di database flessibile vengono visualizzate in sulla fattura?
A differenza dei singoli database, con [La replica di geografico Active](sql-database-geo-replication-overview.md) flessibile database non è disponibile un impatto fatturazione diretto.  Solo addebitate per eDTUs viene completato il provisioning per ogni pool (pool primario e secondario pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Come l'utilizzo della caratteristica di controllo influisce sulla fattura? 
Il controllo è incorporato nel servizio di Database SQL senza aggiuntivi costo ed è disponibile per i database di base, Standard e Premium. Tuttavia, per archiviare i registri di controllo, il controllo viene utilizzato che un account di archiviazione Azure e le tariffe per tabelle e code di archiviazione Azure applicano in base alle dimensioni del Registro di controllo.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Come è possibile trovare il livello di livello e le prestazioni di servizi più adatto per singoli database e database flessibile pool? 
Sono disponibili alcuni strumenti per l'utente. 

- Per i database locale, utilizzare [advisor ridimensionamento DTU](http://dtucalculator.azurewebsites.net/) consigliare il database e DTUs necessari e valutare più database per i pool di database flessibile.
- Se un solo database da trarre vantaggio da parte di un pool, motore intelligente di Azure consiglia un pool di database flessibile se rileva un modello di utilizzo cronologici che lo richiedono. Vedere [monitorare e gestire un pool di database flessibile con il portale di Azure](sql-database-elastic-pool-manage-portal.md). Per informazioni dettagliate su come eseguire le operazioni matematiche personalmente, vedere [considerazioni di prezzo e prestazioni di un pool di database flessibile](sql-database-elastic-pool-guidance.md)
- Per vedere se si ha bisogno di comporre un solo database verso l'alto o verso il basso, vedere [prestazioni indicazioni per singoli database](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Con quale frequenza è possibile modificare il livello di livello o le prestazioni del servizio di un solo database? 
I database V12, è possibile modificare ogni volta che desidera il livello di servizio (tra Basic, Standard e Premium) o il livello di prestazioni all'interno di un livello di servizio (ad esempio S1 su S2). Per database di versioni precedenti, è possibile modificare il livello di livello o prestazioni servizio un totale di quattro volte in un periodo di 24 ore.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Con quale frequenza è possibile regolare il eDTUs al pool? 
Ogni volta che desidera.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Quanto tempo possibile modificare il livello di livello o le prestazioni del servizio di un solo database o spostare un database e uscire da un pool di database flessibile? 
Modificare il livello di servizio di un database e lo spostamento e uscire da un pool richiede il database da copiare nella piattaforma in background. Modificare il livello di servizio può richiedere da alcuni minuti a diverse ore a seconda delle dimensioni dei database. In entrambi i casi, i database rimangono in linea e disponibile durante lo spostamento. Per informazioni dettagliate su come modificare singoli database, vedere [modificare il livello di servizio di un database](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando utilizzare un solo database e database flessibile 
In generale, pool di database flessibile sono concepiti per un tipico [modello di applicazione software come servizio (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), dove non c'è un database per clienti o tenant. Acquisto di singoli database e overprovisioning per soddisfare la variabile e picco richiesta per ogni database è spesso non costo efficiente. Con pool, si gestiscono le prestazioni collettive del pool e i database alto e verso il basso ridimensionamento automatico. 

Motore intelligente di Azure consiglia un pool per i database quando lo richiedono un modello di utilizzo. Per informazioni dettagliate, vedere [Database SQL di prezzi consigli livello](sql-database-service-tier-advisor.md). Per istruzioni dettagliate sulla scelta tra database singoli e flessibile, vedere [Considerazioni prezzo e prestazioni per i pool di database flessibile](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Che cosa significa disporre di un massimo di 200% dello spazio di archiviazione di database di provisioning massimo per l'archiviazione backup? 
Archiviazione di backup è lo spazio di archiviazione associato il backup del database automatizzati utilizzati per [punto-In-ora-ripristino](sql-database-recovery-using-backups.md#-point-in-time-restore) e il [Ripristino geografico](sql-database-recovery-using-backups.md#geo-restore). Database SQL di Microsoft Azure fornisce un massimo di 200% dello spazio di archiviazione massime database provisioning archiviazione di backup senza costi aggiuntivi. Ad esempio, se si dispone di un'istanza di DB Standard con dimensioni massime DB provisioning di 250 GB, vengono fornite con 500 GB di spazio di archiviazione backup senza costi aggiuntivi. Se il database supera fornito backup dello spazio di archiviazione, è possibile ridurre il periodo di conservazione contattando il supporto di Azure o il pagamento di archiviazione aggiuntivo backup fatturato in base alla tariffa standard di accesso in lettura limitazioni geografiche ridondanti dello spazio di archiviazione (RA GRS). Per ulteriori informazioni sulla fatturazione RA GRS, vedere lo spazio di archiviazione prezzi dettagli.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Sto passando da Web/Business al nuovo livelli di servizio, cosa è necessario sapere?
A questo punto vengano ritirati Azure SQL Web nei database aziendali. I livelli Basic, Standard, Premium ed elastici sostituiranno i database Web e Business ritirare. Sono state altre domande frequenti che facilitano questo periodo di durata della transizione. [Domande frequenti su tramonto Web e Business Edition](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Che cos'è un ritardo di replica prevista durante la replica geografico un database tra due aree all'interno dell'area geografica Azure stesso?  
Supporto attualmente un rilasciato di cinque secondi e ritardo replica è stato minore quando secondario geografico è ospitato nel Azure consiglia accoppiata area geografica e a livello di servizio stesso.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Che cos'è un ritardo di replica prevista quando geografico secondario viene creato nella stessa regione database primario?  
In base a dati empirici, non c'è troppo differenza tra area geografica all'interno e di ritardo tra l'area geografica replica quando il Azure consigliati paese accoppiate verrà utilizzato. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se è presente un errore di rete tra due aree, come la logica di ripetizione funziona quando geografico replica è configurata l'archiviazione?  
Se c'è una disconnessione, si Riprova ogni 10 secondi per ristabilire le connessioni.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Cosa può fare per garantire che una modifica critica nel database primaria viene replicata?
Secondario geografico è una replica asincrono e non provare a utilizzare la sincronizzazione completa con primario. Ma sono disponibili un metodo per forzare la sincronizzazione per assicurarsi che la replica di modifiche importanti (ad esempio gli aggiornamenti delle password). Sincronizzazione forzata influisce sulle prestazioni perché blocca il thread chiamante fino a quando non vengono replicate tutte le transazioni completate. Per informazioni dettagliate, vedere [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quali strumenti sono disponibili per monitorare l'intervallo di replica tra il database primario e secondario geografico?
Abbiamo esporre il ritardo di replica in tempo reale tra il database primario e secondario geografico tramite un DMV. Per informazioni dettagliate, vedere [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
