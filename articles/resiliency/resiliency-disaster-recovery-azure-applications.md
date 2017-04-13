<properties
   pageTitle="Ripristino di emergenza per applicazioni Azure | Microsoft Azure"
   description="Panoramica tecnica e informazioni approfondite informazioni sulla creazione di applicazioni per il ripristino di emergenza in Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Ripristino delle applicazioni sviluppate in Microsoft Azure

Mentre disponibilità sulla gestione degli errori temporanei, il ripristino di emergenza (DR) è la grave perdita di funzionalità dell'applicazione. Si consideri ad esempio lo scenario in un'area non è raggiungibile. In questo caso, è necessario disporre di un piano per eseguire l'applicazione o accedere ai dati all'esterno dell'area Azure. Esecuzione del piano a altro comporta il persone, processi e le applicazioni di supporto consentono al sistema di funzione. I proprietari aziendali e tecnologici che definiscono la modalità del sistema operativo per emergenza determinano il livello di funzionalità per il servizio durante un danno. Il livello di funzionalità può richiedere alcuni moduli: non è completamente disponibile, parzialmente disponibile (ridotto di funzionalità o ritardare elaborazione) o completamente disponibile.

##<a name="azure-disaster-recovery-features"></a>Funzionalità di ripristino di emergenza Azure

Come considerazioni sulla disponibilità, Azure con [indicazioni tecniche sulla resilienza](./resiliency-technical-guidance.md) sono progettati per supportare il ripristino di emergenza. È inoltre disponibile una relazione tra alcune delle caratteristiche di disponibilità di Azure e ripristino di emergenza. Ad esempio, la gestione dei ruoli domini guasto aumenta la disponibilità di un'applicazione. Senza che la gestione errori hardware non gestita diventerà uno scenario di "emergenza". Per consentire la corretta applicazione della disponibilità e strategie è una parte importante di emergenza di correzione dell'applicazione. Tuttavia, in questo articolo supera i problemi di disponibilità generale agli eventi di emergenza più gravi (e rari).

##<a name="multiple-datacenter-regions"></a>Più aree del Data Center

Azure mantiene i Data Center in numero di aree in tutto il mondo. Questa infrastruttura supporta diversi scenari di ripristino di emergenza, ad esempio il forniti dal sistema geografico replica di spazio di archiviazione di Azure ad aree secondarie. Inoltre, significa che è possibile semplice e conveniente distribuire un servizio cloud in più posizioni in tutto il mondo. Confronto tra questa e il costo e difficoltà di esecuzione propri centri dati in più aree. Distribuzione di dati e servizi per più paesi consente di proteggere l'applicazione dai principali interruzioni un'area specifica.

##<a name="azure-traffic-manager"></a>Gestione traffico Azure

Quando si verifica un errore specifico del paese, è necessario reindirizzare il traffico a servizi o distribuzioni in un'altra area. È possibile eseguire manualmente il routing, ma è preferibile usare un processo automatico. Azure il traffico Manager è progettato per questa attività. È possibile utilizzare per gestire automaticamente il failover del traffico di utenti a un'altra area geografica in caso di errore area principale. Poiché la gestione del traffico è una parte importante della strategia globale, è importante conoscere le nozioni di base di gestione del traffico.

Nel diagramma seguente, gli utenti si connettono a un URL che ha specificato per il traffico Manager (__http://myATMURL.trafficmanager.net__) e che estrae l'URL del sito effettivo (__http://app1URL.cloudapp.net__ e __http://app2URL.cloudapp.net__). In base a come configurare i criteri per quando agli utenti la distribuzione, gli utenti verranno inviati al sito effettivo corretto quando il criterio impone. Le opzioni dei criteri sono circolari, prestazioni o failover. Per motivi di questo articolo si sarà in questione con l'opzione solo failover.

![Routing tramite Gestore del traffico Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Quando si sta configurando il traffico Manager, fornire un nuovo prefisso il traffico di gestione DNS. Questo è il prefisso URL che è necessario fornire agli utenti di accedere al servizio. Gestore del traffico estrae ora su e non a livello di area di un livello di bilanciamento del carico. Il DNS Manager il traffico associato a un record CNAME per tutte le distribuzioni che gestisce.

All'interno di gestore del traffico, specificare la priorità delle distribuzioni che gli utenti verranno distribuiti in caso di errore. Gestore del traffico controlla i punti finali delle distribuzioni e le note quando si verifica un errore di distribuzione primario. In caso di errore, il traffico Manager analizza l'elenco delle distribuzioni priorità e indirizza gli utenti a quello successivo nell'elenco.

Sebbene il traffico Manager decide da eseguire in caso di errore, è possibile decidere se il dominio failover è attivo o inattivo mentre non si è in modalità di failover. Questa funzionalità non ha nulla a con Azure il traffico Manager. Il traffico Manager rileva un errore nel sito principale e attraversa al sito failover. Gestore del traffico attraversa indipendentemente dal fatto che tale sito attualmente serve utenti o meno.

Per ulteriori informazioni sul funzionamento di Azure il traffico Manager, vedere:

 * [Panoramica di gestione del traffico](../traffic-manager/traffic-manager-overview.md)
 * [Configurare failover metodo di routing](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Scenari di emergenza Azure

Nelle sezioni seguenti vengono illustrate diversi tipi di scenari di emergenza. Interruzioni del servizio a livello di area geografica non sono la sola causa errori a livello di applicazione. Errori di progettazione o amministrazione scarsa possono causare interruzioni. È importante considerare le possibili cause dell'errore durante la progettazione e di fasi di testing del piano di ripristino. Un buon piano consente di sfruttare le funzionalità di Azure e li integra con strategie specifiche dell'applicazione. Risposta scelta è dovuta la priorità dell'applicazione, l'obiettivo di punto di ripristino (rilasciato) e l'obiettivo di tempo di ripristino (RTO).

###<a name="application-failure"></a>Errore dell'applicazione

Azure gestore del traffico gestisce automaticamente gli errori che causano il software sottostante hardware o del sistema operativo nella macchina virtuale host. Azure crea una nuova istanza del ruolo su un server funziona e lo aggiunge alla rotazione di bilanciamento del carico. Se il numero di istanze del ruolo è maggiore di uno, sostituendo il nodo Azure Sposta elaborazione alle altre istanze ruolo in esecuzione.

Sono disponibili gli errori dell'applicazione gravi che si verificano indipendentemente da eventuali errori hardware o del sistema operativo. L'applicazione può avere esito negativo a causa di eccezioni irreversibile causate dalla logica errata o problemi di integrità dei dati. È necessario incorporare abbastanza telemetria nel codice in modo che un sistema di monitoraggio di rilevare le condizioni di errore e comunicare un amministratore dell'applicazione. Un amministratore ha una conoscenza completa di processi di ripristino di emergenza possibile prendere una decisione per richiamare un processo di failover. In alternativa, un amministratore può accettare semplicemente un'interruzione di disponibilità per risolvere gli errori critici.

###<a name="data-corruption"></a>Possibilità di danneggiamento dei dati

Azure memorizza automaticamente i dati di Database SQL Azure e lo spazio di archiviazione di Azure tre volte in modo ridondante all'interno dei domini di errore diverso nella stessa regione. Se si utilizza la replica geografico, i dati vengono archiviati tre volte aggiuntive in un'area diversa. Tuttavia, se gli utenti o applicazione danneggiamento tali dati nella copia primaria, i dati replica rapidamente in altre copie. Purtroppo, causando tre copie dei dati danneggiati.

Per gestire potenziale danneggiamento dei dati, sono disponibili due opzioni. Prima di tutto, è possibile gestire una strategia di backup personalizzata. È possibile memorizzare le copie di backup in Azure o in locale, a seconda delle esigenze aziendali o norme governance. Un'altra opzione consiste nell'usare la nuova opzione in un momento Ripristina per ripristinare un database SQL. Per ulteriori informazioni, vedere la sezione sulle [strategie di dati per il ripristino di emergenza](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Interruzione della rete

Dopo aver configurato le parti della rete Azure inaccessibili, potrebbe non essere possibile accedere all'applicazione o dati. Se uno o più istanze del ruolo non sono disponibili a causa di problemi di rete, Azure utilizza le istanze disponibili rimanente dell'applicazione. Se l'applicazione non è possibile accedere ai dati a causa di un'interruzione di rete Azure, è possibile eseguire in modalità danneggiata potenzialmente localmente utilizzando i dati memorizzati nella cache. È necessario progettare la strategia di ripristino di emergenza per l'esecuzione in modalità danneggiata nell'applicazione. Per alcune applicazioni, questa potrebbe non essere applicabili.

È anche possibile memorizzare i dati in un percorso alternativo finché non viene ripristinata la connettività. Se modalità danneggiata non è un'opzione, le altre opzioni sono i tempi di inattività applicazione o il controllo a un'area alternativo. La struttura di un'applicazione in esecuzione in modalità danneggiata è quante decisioni aziendali quello tecnico. Questo argomento viene trattato ulteriormente la sezione relativa al [ridotto funzionalità dell'applicazione](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Errore di un servizio dipendente

Azure offre molti servizi che possono verificarsi i tempi di inattività periodici. Valutare la possibilità di [Azure Redis Cache](https://azure.microsoft.com/services/cache/) come esempio. Questo servizio multi-tenant funzionalità di memorizzazione nella cache per l'applicazione. È importante considerare cosa accade nell'applicazione se il servizio dipendente non è disponibile. In molti modi, questo scenario è simile allo scenario di interruzione di rete. Tuttavia, prendere in considerazione ogni servizio in modo indipendente genera potenziali miglioramenti al piano generale.

Cache Redis Azure consente la memorizzazione nella cache per l'applicazione tramite all'interno della distribuzione di servizio cloud, quali vantaggi ripristino di emergenza. Prima di tutto, a questo punto viene eseguito il servizio sui ruoli locali rispetto alla distribuzione. Di conseguenza, si è meglio in grado di monitorare e gestire lo stato della cache come parte dei processi di gestione complessivo per il servizio cloud. Questo tipo di memorizzazione nella cache esposto anche nuove caratteristiche. Una delle nuove funzionalità è disponibilità per i dati memorizzati nella cache. In questo modo di mantenere i dati memorizzati nella cache se si verifica un errore di un singolo nodo mantenendo duplicati su altri nodi.

Si noti che disponibilità diminuisce velocità e aumenta latenza a causa l'aggiornamento della copia secondaria scrittura. Pianificare in modo che funge anche la quantità di memoria utilizzata per ogni elemento. In questo esempio specifico viene che ogni servizio dipendente potrebbe avere funzionalità che consentono di migliorare la disponibilità complessiva e vulnerabilità gravi errori.

Con ogni servizio dipendente, è necessario comprendere le implicazioni di un'interruzione del servizio. Nell'esempio memorizzazione nella cache, potrebbe essere possibile accedere ai dati direttamente da un database fino a quando non si ripristina la cache. Sarebbe compromesse in termini di prestazioni, ma fornisce tutte le funzionalità per quanto riguarda i dati.

###<a name="region-wide-service-disruption"></a>Interruzione del servizio a livello di area

Errori precedenti sono state principalmente errori che possono essere gestiti all'interno dell'area di Azure stesso. Tuttavia, è necessario preparare la possibilità che sia presente un'interruzione del servizio dell'intera area. Se si verifica un'interruzione del servizio a livello di area, le copie in locale ridondanti dei dati non sono disponibili. Se è stata attivata la replica geografico, sono disponibili tre copie aggiuntive delle tabelle in un'area diversa BLOB. Se Microsoft dichiara l'area geografica perdito, Azure esegue la correlazione tutte le voci DNS all'area replicato geografico.

>[AZURE.NOTE] Tenere presente che non si dispone di controllare questo processo e si verifica solo per interruzione del servizio a livello di area geografica. Per questi motivi, è necessario utilizzare altre strategie di backup specifiche di un'applicazione per ottenere il massimo livello di disponibilità. Per ulteriori informazioni, vedere la sezione sulle [strategie di dati per il ripristino di emergenza](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interruzione del servizio a livello di Azure

Pianificazione di emergenza, è necessario considerare l'intero intervallo di emergenza. Uno dei più gravi interruzioni di servizio implica contemporaneamente tutte le aree Azure. Come con altre interruzioni del servizio, è possibile decidere è necessario eseguire il rischio di inattività temporaneo in tal caso. Interruzioni del servizio diffuso che controllano più aree geografiche dovrebbero essere molto più interruzioni del servizio isolato che includono i servizi dipendenti o le aree geografiche singole rari.

Per alcune applicazioni critiche, è possibile che deve essere un piano di backup per anche in questo scenario. Il piano per l'evento potrebbe contenere sopra la mancata services in un [cloud alternativo](#alternative-cloud) o un [ibrido locale e cloud, soluzione](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Funzionalità dell'applicazione peggiore

In genere, un'applicazione ben progettata utilizza una raccolta moduli di comunicare con loro attraverso l'implementazione di modelli di scambio di informazioni accoppiamento. Un'applicazione orientata DR richiede la separazione delle attività livello di modulo. In modo da impedire all'interruzione di un servizio dipendente di arrestare l'intera applicazione. Si consideri ad esempio un'applicazione web commerciale per la società Y. I moduli seguenti possono costituire l'applicazione:

 * __Catalogo di prodotti__ consente agli utenti di visualizzare i prodotti.
 * __Carrello__ consente agli utenti di aggiungere o rimuovere prodotti nel carrello.
 * __Stato dell'ordine__ Mostra lo stato di spedizione degli ordini di utente.
 * __Invio degli ordini__ completa degli acquisti sessione inviando l'ordine con pagamento.
 * __Ordine di elaborazione__ convalida affinché l'integrità dei dati e verifica la disponibilità di quantità.

Quando un dipendente di un modulo in questa applicazione non è raggiungibile, come il modulo funzionamento fino a quando non viene ripristinata la parte? Un sistema ben progettato implementata limiti di isolamento tramite la separazione delle attività in fase di progettazione e in fase di esecuzione. È possibile classificare ogni errore come recuperabili e non è reversibile. Errori irreversibile richiederà verso il basso il modulo, ma è possibile ridurre un errore reversibile tramite alternative. Come illustrato nella sezione disponibilità, è possibile nascondere alcuni problemi relativi agli utenti tramite la gestione di errori e intraprendere azioni alternative. Durante una più grave interruzione del servizio, l'applicazione potrebbe essere completamente disponibile. Una terza opzione invece continuare a gestire gli utenti in modalità danneggiata.

Ad esempio, se il database per l'hosting ordini viene interrotto, l'ordine di elaborazione modulo viene interrotta la possibilità di elaborare le transazioni di vendita. A seconda dell'architettura, può risultare difficile o addirittura impossibile per le parti invio degli ordini e ordine di elaborazione dell'applicazione per continuare. Se l'applicazione non è progettato per gestire questo scenario, l'intera applicazione può passare alla modalità offline.

In questo scenario stesso, tuttavia, è possibile che i dati dei prodotti archiviate in un percorso diverso. In tal caso, il modulo di catalogo di prodotti ancora può essere utilizzato per la visualizzazione dei prodotti. In modalità danneggiata, l'applicazione continua a essere disponibile per gli utenti per funzionalità disponibili come visualizzare il catalogo di prodotti. Altre parti dell'applicazione, tuttavia, sono disponibili, ad esempio l'ordinamento o inventario query.

Un'altra variazione della modalità danneggiata Centra su prestazioni piuttosto che funzionalità. Si consideri ad esempio uno scenario in cui il catalogo di prodotti nella cache tramite Azure Redis Cache. Se la memorizzazione nella cache non è disponibile, l'applicazione può passare direttamente all'archiviazione server per recuperare le informazioni del catalogo di prodotti. Ma l'accesso potrebbe non essere più lento rispetto alla versione memorizzati nella cache. Per questi motivi, le prestazioni dell'applicazione sono ridotto fino a quando non viene completamente ripristinato il servizio cache.

Decidere la quantità di un'applicazione continuerà a funzione in modalità danneggiata è decisioni aziendali e decisioni tecniche. L'applicazione deve decidere come informare gli utenti di risolvere il problema temporaneo. In questo esempio, l'applicazione potrebbe consentire la visualizzazione di prodotti e persino aggiungendoli al carrello. Tuttavia, quando si tenta di acquisto, l'applicazione informa l'utente che il modulo cliente disponibile al momento. Non è ideale per il cliente, ma impedisce un'interruzione del servizio a livello di applicazione.

##<a name="data-strategies-for-disaster-recovery"></a>Strategie di dati per il ripristino di emergenza

La gestione dei dati in modo corretto è illustrata l'area più difficile ottenere destra in qualsiasi piano di recupero. Ripristino dei dati è anche la parte del processo di ripristino che in genere richiede più tempo. Diverse opzioni in modalità di riduzione delle risultato difficile sfide per il recupero dei dati da errore e la coerenza dopo l'errore.

Uno dei fattori è necessario ripristinare o mantenere una copia dei dati dell'applicazione. Utilizzare questi dati per riferimento e transazioni in un sito secondario. Un locale impostazione richiede un processo di pianificazione costoso e lungo per implementare una strategia di ripristino di emergenza in più paesi. La maggior parte dei provider di cloud, inclusi Azure, in modo appropriato, consentire facilmente la distribuzione delle applicazioni a più aree. Queste aree aree geografiche sono distribuite in modo che i disagi in più paesi devono essere estremamente rari. La strategia per la gestione dei dati tra le regioni è uno dei fattori principali per il successo di un piano di ripristino di emergenza.

Nelle sezioni seguenti vengono illustrano tecniche di ripristino di emergenza relativi a backup dei dati, dati di riferimento e dati delle transazioni.

###<a name="backup-and-restore"></a>Eseguire il backup e ripristino

Esecuzione di backup regolari dei dati dell'applicazione supporta alcuni scenari di ripristino di emergenza. Risorse di archiviazione diversi richiedono diverse tecniche.

Per i livelli Basic, Standard e Database di SQL Premium, è possibile sfruttare in momento Ripristina per ripristinare il database. Per ulteriori informazioni, vedere [Panoramica: Cloud business continuità e database di emergenza Database SQL di](../sql-database/sql-database-business-continuity.md). Un'altra opzione consiste nell'utilizzare la replica di geografico Active per Database SQL. Questa replica automaticamente le modifiche del database di database secondari nella stessa regione Azure o anche in un'area di Azure diversa. Fornisce un'alternativa potenziale ad alcune delle altre manuale tecniche di sincronizzazione di dati illustrate in questo articolo. Per ulteriori informazioni, vedere [Panoramica: SQL Database attiva geografico replica](../sql-database/sql-database-geo-replication-overview.md).

È anche possibile utilizzare un approccio più manuale per il backup e ripristino. Usare il comando copia del DATABASE per creare una copia del database. È necessario utilizzare questo comando per eseguire un backup con la coerenza. È anche possibile usare il servizio di importazione/esportazione del Database di SQL Azure. Supporta l'esportazione dei database file BACPAC archiviati in archiviazione Blob Azure.

La ridondanza incorporata di spazio di archiviazione di Azure crea due repliche del file di backup nella stessa regione. Tuttavia, la frequenza di esecuzione del processo di backup determina l'ordine di produzione rilasciato, quale la quantità di dati che potrebbero perdere in scenari di emergenza. Si supponga ad esempio, che si esegue una copia di backup nella parte superiore dell'ora e guasto due minuti prima di inizio dell'ora. Si perdono 58 minuti dei dati che si sono verificati dopo l'ultimo backup è stato eseguito. Inoltre, per evitare un'interruzione del servizio a livello di area geografica, è necessario copiare i file BACPAC a un'area alternativa. Quindi è la possibilità di ripristinare tali backup nell'area alternativo. Per ulteriori informazioni, vedere [Panoramica: Cloud business continuità e database di emergenza Database SQL di](../sql-database/sql-database-business-continuity.md).

Per l'archiviazione di Azure, è possibile sviluppare processi di backup personalizzato o usare uno dei numerosi strumenti di backup di terze parti. Si noti che la maggior parte delle strutture di applicazione ulteriori complessità nel punto in cui le risorse di archiviazione fanno riferimento a altro. Si consideri ad esempio un database SQL che contiene una colonna collegata a un blob in archiviazione Azure. Se i backup non viene eseguita contemporaneamente, il database può avere il puntatore a un blob che non è stato backup prima dell'errore. L'applicazione al emergenza deve implementare procedure per la gestione di questo incoerenze dopo il ripristino.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Serie di dati di riferimento per il ripristino di emergenza

Dati di riferimento sono i dati di sola lettura che supporta la funzionalità dell'applicazione. In genere non vengono modificati spesso. Eseguire il backup e ripristino è un metodo per gestire le interruzioni dei servizi a livello di area, il RTO è relativamente lungo. Quando si distribuisce un'applicazione di un'area secondaria, alcune strategie migliorare RTO dei dati di riferimento.

Dato che fanno riferimento a modifiche ai dati in alcuni casi, è possibile migliorare la RTO mantenere una copia permanente dei dati di riferimento nell'area secondario. Per evitare il tempo necessario per ripristinare i backup in caso di emergenza. Per soddisfare i requisiti di ripristino di emergenza in più paesi, è necessario distribuire l'applicazione e i dati di riferimento insieme in più aree. Come indicato nella [serie di dati di riferimento per la disponibilità elevata](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), è possibile distribuire i dati di riferimento al ruolo, allo spazio di archiviazione esterno o a una combinazione di entrambi.

Il modello di distribuzione di dati di riferimento all'interno di nodi di calcolo in modo implicito soddisfa i requisiti di ripristino di emergenza. Distribuzione di dati di riferimento al Database SQL richiede distribuire una copia dei dati di riferimento per ogni area. Si applica la stessa strategia allo spazio di archiviazione Azure. Distribuire una copia dei dati di riferimento archiviati in archiviazione Azure alle aree principali e secondarie.

![Pubblicazione dei dati di riferimento alle aree principali e secondarie](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

È necessario implementare routine di backup specifiche di un'applicazione per tutti i dati, inclusi i dati di riferimento. Copie replicate geografico tra le aree vengono utilizzate solo in un'interruzione del servizio a livello di area geografica. Per evitare che i tempi di inattività "Extended", distribuire le parti essenziali di dati dell'applicazione all'area secondario. Per un esempio di questa topologia, vedere il [modello attivo passivo](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Modello di dati delle transazioni per il ripristino di emergenza

Implementazione di una strategia di modalità di emergenza pienamente richiede replica asincrono dei dati transazione all'area secondario. Windows ora pratica entro il quale la replica consente di verificare le caratteristiche di rilasciato dell'applicazione. È comunque possibile ripristinare i dati che si sono interrotta dall'area principale durante la finestra di replica. Potrebbe anche essere possibile unire con l'area secondaria in un secondo momento.

Di seguito architettura sono riportati alcuni suggerimenti in diversi modi per gestire i dati delle transazioni in uno scenario di failover. È importante tenere presente che questi esempi non sono completi. Ad esempio, è possibile che sostituite percorsi di archiviazione intermedia, ad esempio code con Database di SQL Azure. Code stesse potrebbero essere lo spazio di archiviazione di Azure o Bus di servizio Azure code (vedere [code Azure e Bus di servizio: confronto e rispetto](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Possono variare anche le destinazioni dello spazio di archiviazione di server, ad esempio le tabelle Azure invece di Database SQL. Inoltre, è possibile che venga inseriti ruoli di lavoro come intermediari in vari passaggi. Importante non consiste nell'emulare queste architetture esattamente, ma per prendere in considerazione varie alternative per il recupero dei dati delle transazioni e dei moduli correlati.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Replica di dati delle transazioni in preparazione per il ripristino di emergenza

Valutare la possibilità di un'applicazione che utilizza lo spazio di archiviazione di Azure code per contenere dati delle transazioni. In questo modo ruoli di lavoro elaborare i dati transazioni nel database di server in un'architettura separata. È necessario le transazioni da utilizzare sorta di memorizzazione temporaneo se i ruoli front-end richiedono la query immediata dei dati. A seconda del livello di tolleranza perdite di dati, è possibile replicare code, il database o tutte le risorse di archiviazione. Con solo la replica di database, se l'area principale non è attivo, è comunque possibile ripristinare i dati nelle code quando si torna area principale.

Il diagramma seguente illustra un'architettura nel punto in cui il database server viene sincronizzato in aree geografiche.

![Replica di dati delle transazioni in preparazione per il ripristino di emergenza](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

La difficoltà principale per l'implementazione di questa architettura è la strategia di replica tra le aree. Il servizio di sincronizzazione di dati di SQL Azure consente questo tipo di replica. Tuttavia, il servizio è ancora in anteprima e scelta non consigliato per gli ambienti di produzione. Per ulteriori informazioni, vedere [Panoramica: Cloud business continuità e database di emergenza Database SQL di](../sql-database/sql-database-business-continuity.md). Per le applicazioni di produzione, è necessario acquistare una soluzione di terze parti o creare la propria logica di replica nel codice. A seconda dell'architettura, la replica potrebbe essere bidirezionale, anch ' esso più complessa.

Implementazione potenziali potrebbe rendere usare della coda intermedia dell'esempio precedente. Il ruolo di lavoro che elabora i dati per la destinazione di archiviazione finale potrebbe apportare le modifiche nell'area principale e secondaria area. Non sono attività semplice e indicazioni per codice della replica sono lo scopo di questo articolo. Il punto importante che molte del tempo e il test deve concentrarsi su come si replicano i dati all'area secondario. L'elaborazione di altre e verifica possono contribuire a garantire che i processi di failover e ripristino gestiscono correttamente eventuali incoerenze dati possibili o transazioni duplicate.

>[AZURE.NOTE] La maggior parte di questo documento è incentrata su piattaforma come servizio (PaaS). Tuttavia, altre opzioni di replica e la disponibilità per applicazioni ibride utilizzano macchine virtuali di Azure. Queste applicazioni ibride consente di infrastruttura come servizio (IaaS) ospitato da SQL Server in macchine virtuali in Azure. In questo modo approcci disponibilità tradizionale in SQL Server, ad esempio disponibilità AlwaysOn o la distribuzione dei Log. Alcune tecniche, ad esempio AlwaysOn, funzionano solo da istanze di SQL Server locale e macchine virtuali di Azure. Per ulteriori informazioni, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Modalità di applicazione peggiore per l'acquisizione di transazione

Valutare la possibilità di un'architettura a seconda che opera in modalità danneggiata. L'applicazione sull'area di secondario Disattiva tutte le funzionalità, ad esempio creazione di report, funzionalità di business intelligence (BI) o svuotamento code. Accetta solo i tipi principali di transazione flussi di lavoro, come definito da esigenze aziendali. Il sistema acquisisce le transazioni e li scrive code. Il sistema è possibile posticipare l'elaborazione dei dati durante la fase iniziale dell'interruzione del servizio. Se il sistema area principale viene riattivato nell'intervallo di tempo previsto, i ruoli di lavoro nell'area principale possono distogliere code. Questo processo non è necessario per l'unione di database. Se l'interruzione del servizio area principale supera la finestra ai, l'applicazione può avviare l'elaborazione di code.

In questo scenario, il database nel database secondario contiene dati transazioni incrementali che devono essere uniti dopo primario viene riattivato. Il diagramma seguente illustra la strategia per temporaneamente l'archiviazione dei dati delle transazioni fino a quando non viene ripristinata l'area principale.

![Modalità di applicazione peggiore per l'acquisizione di transazione](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Per ulteriori informazioni tecniche di gestione dei dati per applicazioni Azure flessibili, vedere [sicura: indicazioni per architetture Cloud flessibili](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologie di distribuzione di emergenza

È necessario preparare applicazioni critiche la possibilità di un'interruzione del servizio a livello di area geografica. Eseguire questa operazione incorporare una strategia di distribuzione in più paesi di pianificazione operativa.

Distribuzioni in più paesi prevede processi IT pro per pubblicare i dati dell'applicazione e materiale di riferimento all'area secondario dopo un'emergenza. Se l'applicazione richiede failover immediato, il processo di distribuzione prevede un'installazione attivo/passivo o un'installazione attivo/attivo. Questo tipo di distribuzione è le istanze esistenti dell'applicazione in esecuzione nell'area alternativo. Uno strumento di distribuzione, ad esempio Azure il traffico gestione offre servizi di bilanciamento del carico a livello di DNS. È possibile rilevare interruzioni del servizio e instradare gli utenti a diverse aree quando necessario.

Parte di un ripristino di emergenza Azure completato è architettura che il ripristino in soluzione dall'inizio. Cloud fornisce opzioni aggiuntive per il ripristino da errori durante un danno che non sono disponibili in un provider di hosting tradizionale. In particolare, è possibile in modo dinamico e rapidamente assegnare risorse a un'area diversa. Di conseguenza, non è possibile pagando molto per le risorse inattive mentre si sta attendendo per un errore.

Nelle sezioni seguenti vengono illustrate diverse topologie di distribuzione di emergenza. In genere è un compromesso aumento dei costi o complessità della disponibilità aggiuntive.

###<a name="single-region-deployment"></a>Distribuzione di sola area geografica

Una distribuzione singolo paese non è una topologia di ripristino di emergenza, ma è utilizzata per il contrasto di altre architetture. Distribuzioni singolo paese sono comuni per le applicazioni di Azure. Tuttavia, questo tipo di distribuzione non è un contendente gravi per un piano di recupero.

Il diagramma seguente illustra un'applicazione in esecuzione in una singola regione Azure. Gestore del traffico Azure e l'utilizzo di guasto e aggiornamento dei domini aumentare la disponibilità dell'applicazione all'interno dell'area.

![Distribuzione di sola area geografica](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

In questo caso, è evidente che il database sia un singolo punto di errore. Anche se Azure replica i dati in domini diversi guasto alle repliche interne, questa operazione si verifica nella stessa regione. L'applicazione non è possibile contrastare grave. Se l'area geografica viene interrotto, tutti i domini guasto scendere, incluse tutte le istanze del servizio e le risorse di archiviazione.

Per tutti tranne le applicazioni meno critiche, è necessario applicare un piano per distribuire le applicazioni su più aree. Occorre valutare la possibilità di RTO e costi vincoli nel valutare quale topologia di distribuzione da utilizzare.

Diamo un'occhiata ora modelli specifici di cedere il controllo nelle diverse aree. In questi esempi tutti usare due aree per descrivere il processo.

###<a name="redeployment-to-a-secondary-azure-region"></a>Ridistribuzione su un'area di Azure secondaria

Nel modello di ridistribuzione a un'area secondaria, solo l'area principale dispone di applicazioni e database in esecuzione. L'area secondario non sia configurato per un failover automatico. Quando si verifica un errore, pertanto devono ruotare su tutte le parti del servizio nella nuova area. Sono inclusi il caricamento di un servizio cloud di Azure, la distribuzione del servizio cloud, ripristino dei dati e modifica di DNS per reindirizzare il traffico.

Anche se si tratta di più conveniente delle opzioni in più paesi, sono le caratteristiche RTO peggiore. In questo modello, il servizio pacchetto e database di backup archiviati in locale o dell'istanza di spazio di archiviazione Blob Azure dell'area secondario. Tuttavia, è necessario distribuire un nuovo servizio e ripristinare i dati prima di riprendere l'operazione. Anche se è automatizzare completamente il trasferimento di dati dallo spazio di archiviazione backup, utilizzare il nuovo ambiente di database utilizza molto tempo. Spostamento dei dati dallo spazio di archiviazione disco di backup per il database vuoto sull'area di secondario è costosa parte del processo di ripristino. È necessario ripetere l'operazione, tuttavia, per visualizzare il nuovo database allo stato operativo perché non è replicato.

L'approccio ottimale è per archiviare i pacchetti di servizio di archiviazione Blob nell'area secondario. Non è più necessario caricare il pacchetto di Azure, ecco cosa si verifica quando si distribuisce da un computer di sviluppo locale. È possibile distribuire rapidamente i pacchetti di servizio a un nuovo servizio cloud dallo spazio di archiviazione Blob usando gli script di PowerShell.

Questa opzione è utile solo per le applicazioni non critici tollerabile un RTO elevato. Ad esempio, si potrebbero funzionare per un'applicazione che può essere verso il basso per alcune ore ma dovrebbe essere eseguito nuovamente entro 24 ore.

![Ridistribuzione su un'area di Azure secondaria](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Attiva passiva

Il modello attivo passivo è la scelta Ottimizza per molte società. Questo modello consente di miglioramenti alle RTO con un aumento relativamente costo sulla serie ridistribuzione.
In questo scenario è nuovamente un principale e un'area di Azure secondaria. Tutto il traffico passa alla distribuzione attiva sull'area principale. L'area secondario meglio preparato per il ripristino di emergenza perché il database è in esecuzione su entrambe aree geografiche. Inoltre, un meccanismo di sincronizzazione è tra di esse. Questo approccio standby può comportare due varianti: un approccio solo database o una distribuzione completa nell'area secondario.

####<a name="database-only"></a>Solo database

Nella prima variante del modello attivo passivo, solo l'area principale ha un'applicazione di servizio cloud distribuito. Tuttavia, diversamente da quanto succede motivo ridistribuzione entrambe aree geografiche vengono sincronizzati con il contenuto del database. (Per ulteriori informazioni, vedere la sezione sul [modello di dati delle transazioni di emergenza](#transactional-data-pattern-for-disaster-recovery)). Quando si verifica un danno, esistono meno i requisiti di attivazione. Avviare l'applicazione nell'area secondario, modificare le stringhe di connessione per il nuovo database e modificare le voci DNS per reindirizzare il traffico.

Simile ridistribuzione è necessario sono già stati memorizzati i pacchetti di servizio in archiviazione Blob Azure nell'area secondaria per la distribuzione più veloce. Diversamente da quanto succede motivo ridistribuzione si non si verifichi la maggior parte dei costi generali che richiede l'operazione di ripristino del database. Il database è pronto e in esecuzione. Consente di salvare una quantità significativa di tempo, questo criterio DR accessibile. È anche il modello DR più popolari.

![Solo database attivo-passivo](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Replica completa

Nella seconda variante del modello attivo passivo area principale e secondaria area dispone di una distribuzione completa. La distribuzione include i servizi cloud e un database sincronizzato. Tuttavia, solo l'area principale attivamente gestisce le richieste di rete dagli utenti. L'area secondario diventa attiva solo quando l'area principale si verifica un'interruzione del servizio. In questo caso, tutte le nuove richieste di rete instradata all'area secondario. Gestore del traffico Azure possono gestire questa failover automaticamente.

Failover dovuto più velocemente di quanto variante solo database già distribuiti i servizi. Questo modello fornisce un RTO molto basso. L'area geografica failover secondario deve essere pronto per l'invio immediatamente dopo errore dell'area principale.

Insieme a un tempi di risposta, questo modello ha il vantaggio di pre-allocazione e distribuzione di servizi di backup. Non è necessario preoccuparsi di un'area senza spazio da allocare nuove istanze di emergenza. È importante se l'area di Azure secondario si avvicina capacità. Non ci sono garanzie (contratto di servizio) che istantaneamente sarà possibile distribuire un numero di nuovi servizi cloud in un'area.

Per il tempo di risposta più veloce con questo modello, è necessario disporre simile scala (numero di istanze del ruolo) nelle aree principali e secondarie. Nonostante i vantaggi acquistati istanze di elaborazione inutilizzati è costosa e potrebbe non essere la scelta finanziaria più prudente. Per questi motivi, è in genere viene utilizzata una versione leggermente ridotta di servizi cloud sull'area di secondario. Quindi esito negativo su e rapidamente scalabilità distribuzione secondaria se necessario. È consigliabile automatizzare il processo di failover dopo l'area principale non è accessibile, istanze aggiuntive, a seconda del carico per l'attivazione. Si prevede l'uso di un meccanismo di adattamento automatico come [scala macchina virtuale imposta](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Il diagramma seguente illustra il modello in cui le aree principali e secondarie contengono un servizio cloud distribuito in un modello attivo passivo.

![Replica attiva passiva, completo](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Attivo attivo

A questo punto, sta probabilmente informazioni generali sulle evoluzione dei modelli: riducendo il RTO aumenta i costi e complessità. Soluzione attivo attivo non viene effettivamente divisa questa tendenza con riguardo ai costi.

In un modello di attivo attivo, i servizi cloud e database vengono completamente distribuiti in entrambe le aree geografiche. Diversamente da quanto succede modello attivo passivo entrambe aree geografiche riceveranno il traffico utente. Questa opzione produce i tempi di ripristino rapido. I servizi sono già ridimensionati e per gestire una parte del caricamento in ogni area. DNS è già abilitato per usare l'area secondario. Esiste un ulteriore complessità per determinare in che modo inviare agli utenti all'area appropriata. Pianificazione circolari potrebbe essere possibile. È probabile che alcuni utenti utilizzare un'area specifica in cui si trova la copia primaria dei dati.

In caso di failover, è sufficiente disabilitare DNS all'area principale. Consente di indirizzare tutto il traffico all'area secondario.

Anche in questo modello, esistono alcune varianti. Ad esempio, nel diagramma seguente mostra un modello in cui l'area principale possiede la copia master del database. Servizi cloud in entrambe le aree geografiche scrittura per tale database primario. La distribuzione secondaria può leggere dal database principale o replicato. Replica in questo esempio si verifica uno dei modi.

![Attivo attivo](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Esiste un svantaggio all'architettura attivo attivo nel diagramma precedente. La seconda area necessario accedere al database nella prima regione perché la copia master vi risiede. Prestazioni Elimina in modo significativo disattivata quando si accede a dati dall'esterno un'area. In chiamate area tra database, è necessario considerare un tipo di batch strategia per migliorare le prestazioni delle chiamate. Per ulteriori informazioni, vedere [come utilizzare il batch per migliorare le prestazioni dell'applicazione di Database SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Un'architettura alternativa prevede ogni regione accedere direttamente il proprio database. Nel modello, è necessario un tipo di replica bidirezionale per sincronizzare i database in ogni area.

Nel modello attivo attivo, potrebbe essere necessario non le istanze dell'area di principale a quanto avviene in motivo attivo passivo. Se si dispone di 10 istanze area geografica principale in un'architettura attivo passivo, potrebbe essere necessario solo 5 in ogni area geografica in un'architettura attivo attivo. Entrambe aree geografiche condividono ora il carico di lavoro. È possibile sui costi sulla serie attivo passivo se si mantiene un caldo standby sull'area del passivo con 10 istanze in attesa di failover.

Tenere presente che fino a quando non si ripristina l'area principale, secondario area potrebbe ricevere un aumento inaspettato di nuovi utenti. Se sono presenti 10.000 utenti ogni server quando l'area principale si verifica un'interruzione del servizio, l'area secondario improvvisamente deve gestire 20.000 utenti. Monitoraggio delle regole sull'area di secondario deve rilevare in modo la doppia le istanze presenti nell'area secondario. Per ulteriori informazioni, vedere la sezione sul [rilevamento degli errori](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Ibrido locale e cloud, soluzione

Una strategia aggiuntiva per il ripristino di emergenza consiste nell'architettura di un'applicazione ibrida che viene eseguita in locale e nel cloud. A seconda dell'applicazione, l'area principale potrebbe essere un'altra posizione. Valutare la possibilità di architetture precedente e immaginare area primario o secondario come una posizione locale.

Esistono alcuni problemi in queste architetture ibrida. Prima di tutto, la maggior parte di questo articolo sono trattate modelli di architettura PaaS. Applicazioni PaaS tipiche in Azure si basano su costrutti di Azure specifico, ad esempio ruoli, servizi cloud e il traffico Manager. Per creare una soluzione locale di questo tipo di applicazione PaaS richiede un'architettura molto differente. Potrebbe non essere possibile da una gestione o prospettiva di costo.

Tuttavia, una soluzione ibrida di emergenza ha meno sfide per architetture tradizionale che sono state spostate semplicemente nel cloud. Ciò avviene architetture che utilizzano IaaS. Le applicazioni IaaS utilizzare macchine virtuali nel cloud che può avere equivalenti diretto locale. È anche possibile utilizzare reti virtuali per connettersi macchine nel cloud con risorse di rete locale. Verrà aperta la diverse possibilità che non sono possibili con le applicazioni solo PaaS. Ad esempio SQL Server possibile sfruttare le soluzioni di ripristino di emergenza, ad esempio disponibilità AlwaysOn e che rispecchiano database. Per informazioni dettagliate, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Soluzioni IaaS offrono un percorso più semplice per le applicazioni in locale da utilizzare Azure come l'opzione di failover. Se si dispone un'applicazione completa in un'area locale esistente. Tuttavia, cosa fare se non si dispongono delle risorse per gestire un'area geografica separata per il failover? È possibile decidere di utilizzare macchine virtuali e reti virtuali per ottenere l'applicazione in esecuzione in Azure. In questo caso, definire i processi che la sincronizzazione dei dati nel cloud. La distribuzione di Azure quindi diventa l'area secondaria da utilizzare per failover. L'area principale rimane l'applicazione locale. Per ulteriori informazioni sulle architetture IaaS e funzionalità, vedere la [documentazione macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Cloud alternativi

Ci sono situazioni in cui anche l'affidabilità del Cloud Microsoft potrebbe non soddisfare regole di conformità interni o i criteri che richiede l'organizzazione. Anche la preparazione e progettazione per implementare sistemi di backup durante un danno migliore inferiori se è presente un'interruzione del servizio globale di un provider di servizi cloud.

Verrà si desidera confrontare i requisiti di disponibilità con il costo e le complessità della disponibilità migliorata. Eseguire un'analisi dei rischi e definire la RTO e rilasciato per la soluzione. Se l'applicazione non è possibile tollerare qualsiasi tempo di inattività, può essere utile in cui si consiglia di utilizzare un'altra soluzione cloud. A meno che non si blocca l'intera Internet, un'altra soluzione cloud potrebbe comunque essere disponibile se Azure diventa accessibile a livello globale.

Come per lo scenario ibrido distribuzioni failover precedente architetture di ripristino di emergenza possono esistere all'interno di un'altra soluzione cloud. Siti cloud alternativo DR dovrebbero essere usati solo per le soluzioni cui RTO consente molto piccolo, se presenti, i tempi di inattività. Si noti che una soluzione che utilizza un sito DR all'esterno di Azure richiederà carico di lavoro da configurare, sviluppare, distribuire e gestire. È inoltre più difficile implementare le procedure consigliate in un'architettura di croce cloud. Anche se piattaforme cloud sono simili concetti principali, le API e architetture sono diverse.

Se si decide di dividere il DR tra piattaforme diverse, rende opportuno architettura abstraction layer nella finestra di progettazione della soluzione. In questo caso, non sarà necessario sviluppare e gestire due versioni della stessa applicazione per le piattaforme cloud diverso in caso di emergenza. Come con scenario ibrido l'utilizzo di macchine virtuali di Azure o servizio contenitore Azure potrebbe essere più semplice in questi casi rispetto all'utilizzo dei modelli disponibili in PaaS cloud specifiche.

##<a name="automation"></a>Automazione

Alcuni dei motivi che abbiamo parlato richiedono attivazione rapida delle distribuzioni offline e il ripristino di parti specifiche di un sistema. Automazione o script, supporta la possibilità di attivare le risorse su richiesta e distribuire soluzioni rapidamente. In questo articolo correlati DR automazione è equivalente con [PowerShell di Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx), ma l' [API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) , è possibile.

Sviluppare script consente di gestire le parti della DR Azure non consente di gestire in modo trasparente. Il vantaggio di produrre risultati coerenti ogni volta che viene ridotto a icona la possibilità di errore umano. Visto predefiniti DR script riduce il tempo necessario per ricreare un sistema e delle relative parti costituenti in corrispondenza di emergenza. Non si vuole risalire alla causa manualmente come ripristinare il sito mentre è in giù e perdere denaro minuto.

Dopo aver creato gli script, eseguirne il test più volte dall'inizio alla fine. Dopo aver verificato le funzionalità di base, assicurarsi che testarli in [simulazione di emergenza](#disaster-simulation). In questo modo scoprire difetti negli script o processi.

Si consiglia di automazione consiste nel creare un archivio di script di PowerShell o interfaccia riga di comando (CLI) per il ripristino di emergenza Azure. Chiaramente contrassegnare e categorizzare loro per la ricerca semplice. Specificare una persona per gestire l'archivio e controllo delle versioni degli script. Documento che loro bene con spiegazioni dei parametri ed esempi di uso di uno script. Assicurarsi che è mantenere questa documentazione aggiornati con le distribuzioni Azure. Caratteri di sottolineatura lo scopo di avere una sola persona responsabile tutte le parti dell'archivio.

##<a name="failure-detection"></a>Rilevamento degli errori

Per gestire correttamente i problemi di disponibilità e ripristino di emergenza, è necessario essere grado di rilevare e diagnosticare gli errori. È necessario farlo server avanzate e monitoraggio della distribuzione in modo che è possibile conoscere rapidamente quando le sue parti o un sistema sono improvvisamente verso il basso. Strumenti di monitoraggio che esaminare lo stato generale del servizio cloud e le dipendenze, è possono eseguire parte del lavoro. Uno strumento di Microsoft è [System Center 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Strumenti di terze parti possono inoltre fornire le funzionalità di monitoraggio. La maggior parte delle soluzioni di monitoraggio di tenere traccia delle contatori di prestazioni chiave e la disponibilità dei servizi.

Anche se questi strumenti sono importanti, non sostituiscono la necessità di pianificare l'individuazione degli errori e dei report all'interno di un servizio cloud. È necessario pianificare per il corretto funzionamento Azure diagnostica. Contatori personalizzati o voci del registro eventi possono anche essere parte della strategia complessiva. In questo modo altri dati in caso di errore rapidamente diagnosi e ripristino funzionalità complete. Inoltre, sono disponibili statistiche aggiuntive che gli strumenti di monitoraggio consente di determinare dell'integrità. Per ulteriori informazioni, vedere [Attivazione di Azure diagnostica in servizi Cloud Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Per informazioni su come pianificare un' generale "modello dello stato", vedere [sicura: indicazioni per architetture Cloud flessibili](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulazione di emergenza

Test di simulazione comporta la creazione di piccole dimensioni reali situazioni al piano di lavoro per osservare come rispondere ai membri del team. Simulazioni visualizzati anche l'efficacia le soluzioni sono nel piano del ripristino. Eseguire simulazioni in modo che gli scenari creati non interrompano business effettivi mentre si ancora automatizzare come situazioni reali.

Valutare la possibilità di progettazione di un tipo di "Pannello di controllo" nell'applicazione per simulare manualmente i problemi di disponibilità. Ad esempio, tramite un parametro di contorni, impostare un trigger eccezioni access database per un modulo di ordinamento, in modo che venga malfunzionante. È possibile adottare simili leggeri approcci per altri moduli livello interfaccia di rete.

La simulazione evidenzia gli eventuali problemi che si è lavorati sufficientemente. Gli scenari simulati devono essere completamente controllare. Questo errore indica che, anche se il piano di ripristino sembra malfunzionante, è possibile ripristinare la situazione normale senza causare danni rilevanti. È anche importante informare dirigenti su quando e come gli esercizi di simulazione verranno eseguiti. È necessario pianificare le informazioni nel tempo o risorse che potrebbero verificarsi produttivo durante l'esecuzione di test di simulazione. Quando si sta sottoporre il piano di recupero a un test, è importante definire come verrà misurato successo.

Esistono diverse altre tecniche che è possibile utilizzare per testare i piani di ripristino. Tuttavia, la maggior parte delle loro sono semplicemente modificate versioni di queste tecniche di base. La principale motivazione alla base testing consiste nel valutare come sia possibile e come servono il piano di ripristino. Test di ripristino di emergenza è incentrata su Dettagli per individuare holes nel piano del ripristino di base.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie di articoli basato sul [ripristino di emergenza e disponibilità per applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L'articolo precedente in questa serie è [disponibilità per applicazioni basate su Microsoft Azure](./resiliency-high-availability-azure-applications.md).
