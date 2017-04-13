<properties
   pageTitle="Indicazioni tecniche: ripristino da errori locali in Azure | Microsoft Azure"
   description="L'articolo sulla comprensione e la progettazione flessibili, disponibilità, applicazioni con tolleranza, nonché strategia di emergenza basato su errori locali all'interno di Azure."
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

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Indicazioni tecniche sulla resilienza Azure: ripristino da errori locali in Azure

Esistono due minacce principale per la disponibilità delle applicazioni:

* Errore di dispositivi, ad esempio unità e server
* Esaurimento delle risorse critiche, ad esempio calcolo in condizioni di carico di picco

Azure offre una combinazione di gestione delle risorse, la flessibilità, bilanciamento del carico e partizioni per abilitare disponibilità in questi casi. Alcune di queste funzionalità vengono eseguite automaticamente per tutti i servizi Azure. Tuttavia, in alcuni casi, lo sviluppatore dell'applicazione deve eseguire alcune operazioni aggiuntive vantaggi offerti da essi.

##<a name="cloud-services"></a>Servizi cloud

Servizi Cloud Azure è costituito da insiemi di uno o più ruoli web o lavoro. Una o più istanze di un ruolo è possono eseguire contemporaneamente. La configurazione determina il numero di istanze. Istanze del ruolo vengono monitorate e gestite tramite un componente denominato controller dell'infrastruttura. Il controller dell'infrastruttura rileva e risponda alla errori hardware e software automaticamente.

Tutte le istanze di ruolo viene eseguito nel propria macchine () e comunica con il relativo controller tessuti tramite un agente di guest. Agente di guest raccoglie metriche di risorse e nodo, inclusi l'utilizzo, stato, i registri, Uso risorse, eccezioni e le condizioni di errore macchine Virtuali. Controller dell'infrastruttura query agente di guest intervalli configurabili e si riavvia la macchina virtuale agente di guest cessa di rispondere. In caso di errore hardware, il controller dell'infrastruttura associato Sposta tutte le istanze di ruolo interessato a un nuovo nodo hardware e consente di riconfigurare la rete per instradare il traffico non esiste.

Per trarre vantaggio da queste funzionalità, gli sviluppatori devono assicurarsi che tutti i ruoli di servizio evitare la memorizzazione di stato su istanze del ruolo. Se, tuttavia, tutti i dati persistenti l'accesso dall'archivio permanente, ad esempio lo spazio di archiviazione di Azure o Database SQL Azure. In questo modo i ruoli gestire le richieste. Inoltre, significa che istanze del ruolo possono accedere verso il basso in qualsiasi momento senza creare incoerenze nello stato temporaneo o persistente del servizio.

La necessità di archiviare lo stato esternamente ai ruoli diversi le implicazioni. Implica, ad esempio, che tutte le modifiche correlate a una tabella di archiviazione Azure devono essere modificate in una sola transazione entità gruppo, se possibile. Naturalmente, non è sempre possibile apportare tutte le modifiche in una sola transazione. È necessario prestare particolare attenzione per garantire che gli errori di istanza di ruolo non problemi quando vengono interrompere le operazioni di esecuzione prolungata che si estendono su due o più aggiornamenti sullo stato permanente del servizio. Se un altro ruolo Ritenta tale operazione, dovrebbe prevedere e gestire il caso in cui è stata parzialmente completato il lavoro.

Si consideri ad esempio un servizio che suddivide dati su più archivi. Se un ruolo di lavoro viene interrotta durante è riposizionamento un condiviso, il trasferimento del condiviso non può terminare. O il trasferimento può essere ripetuto da sin un ruolo di lavoro diverso, causando dati orfani o danneggiamento dei dati. Per evitare problemi, operazioni di lunga devono essere una o entrambe le operazioni seguenti:

 * *Idempotente*: ripetibili senza effetti. Per essere idempotente, un'operazione di esecuzione prolungata deve avere lo stesso effetto indipendentemente dal numero di volte in cui viene eseguito, anche quando viene interrotta durante l'esecuzione.
 * *In modo incrementale riavvio*: continuare dal punto d' più recente di errore. Per fare in modo incrementale riavvio, un'operazione di esecuzione prolungata è costituito da una sequenza di dimensioni minori atomiche operazioni. Inoltre deve registrare lo stato di avanzamento nell'archivio permanente, in modo che ogni chiamata successiva in cui è stata interrotta predecessore.

Infine, tutte le operazioni di lunga devono essere richiamate più volte fino a quando non riescono a. Ad esempio un'operazione di provisioning potrebbe essere inserita in una coda di Azure e quindi rimosso dalla coda da un ruolo di lavoro solo quando l'operazione riesce. Simultanea potrebbe essere necessario creare eliminazione dei dati che è interrotto operazioni.

###<a name="elasticity"></a>Flessibilità

Il numero iniziale delle istanze in esecuzione per ogni ruolo è determinato nella configurazione di ogni ruolo. Gli amministratori di configurare personalmente ogni ruolo per l'esecuzione con due o più istanze in base a carico previsto. Ma possono essere facilmente ridimensionate istanze del ruolo o verso il basso come l'utilizzo motivi di modificare. È possibile eseguire questa operazione manualmente nel portale di Azure oppure è possibile automatizzare il processo mediante Windows PowerShell e le API del servizio di gestione o strumenti di terze parti. Per ulteriori informazioni, vedere [come per scalare automaticamente un'applicazione](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partizione

Il controller dell'infrastruttura di Azure vengono utilizzati due tipi di partizioni:

* Un *aggiornamento dominio* viene utilizzato per aggiornare le istanze di ruolo del servizio in gruppi. Azure distribuisce istanze del servizio in più domini di aggiornamento. Per un aggiornamento sul posto, il controller dell'infrastruttura porta verso il basso tutte le istanze di un aggiornamento di dominio, li aggiorna e riavvia prima di passare al successivo aggiornamento dominio. Questo approccio impedisce l'intero servizio non è disponibile durante il processo di aggiornamento.
* Un *dominio* definisce potenziali punti di errore hardware o della rete. Per qualsiasi ruolo dispone di più istanze, tessuti assicura che le istanze sono distribuite in più domini di errore, per evitare che gli errori sono isolati hardware interrompere il servizio. Domini guasto regolano esposizione tutti server e gli errori del cluster.

[Azure contratto di servizio (contratto di servizio)](https://azure.microsoft.com/support/legal/sla/) garantisce che quando due o più istanze di ruolo web distribuite in diversi guasto ed eseguire l'aggiornamento di domini, avranno connettività esterna almeno 99,95 ricordarsi di questa operazione. A differenza dei domini di aggiornamento, non è possibile controllare il numero di domini guasto. Azure automaticamente assegna domini guasto e distribuite istanze del ruolo. AT almeno le prime due istanze di ogni ruolo vengono inserite nella guasto diversi ed eseguire l'aggiornamento domains per fare in modo che un ruolo con almeno due istanze verrà soddisfino il contratto di servizio. Questo è rappresentato nel diagramma seguente.

![Vista semplificata di isolamento dominio degli errori](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Il bilanciamento del carico

Tutto il traffico a un ruolo web attraversa un bilanciamento del carico senza informazioni sullo stato, che distribuisce richieste dei client tra le istanze di ruolo. Istanze del ruolo singoli non dispone di indirizzi IP pubblici e non sono direttamente utilizzabili da Internet. Ruoli Web sono prive di stato in modo che possano essere instradate delle richieste client all'istanza di un ruolo. Ogni 15 secondi viene generato un evento di [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) . È possibile utilizzare per indicare che il ruolo è pronto per ricevere il traffico o se è occupato e prendere fuori la rotazione di bilanciamento del carico.

##<a name="virtual-machines"></a>Macchine virtuali

Azure macchine virtuali è diverso dal piattaforma come servizio PaaS () calcolare ruoli per diversi motivi in relazione alla disponibilità. In alcuni casi, è necessario eseguire operazioni aggiuntive per garantire disponibilità.

###<a name="disk-durability"></a>Durata disco

A differenza delle istanze del ruolo PaaS, dati archiviati in unità macchina virtuale sono persistenti anche quando la macchina virtuale viene spostata. Azure macchine virtuali di utilizzare dischi macchine Virtuali esistenti come BLOB di archiviazione Azure. Per le caratteristiche di disponibilità di spazio di archiviazione di Azure, sono altamente disponibili anche i dati archiviati in unità del computer virtuale.

Si noti che l'eccezione a questa regola è unità D (in macchine virtuali di Windows). Unità D è effettivamente fisico di archiviazione sul server rack che ospita la macchina virtuale e i relativi dati andranno persi se la macchina virtuale è inserimento Cestino. Unità D è destinato solo lo spazio di archiviazione temporaneo. In Linux, Azure "in genere", ma non sempre, espone il disco temporaneo locale come dispositivo blocco /dev/sdb. È spesso attivata dall'agente Linux Azure come /mnt/resource o /mnt punti di montaggio (configurabili /etc/waagent.conf).

###<a name="partitioning"></a>Partizione

Azure a livello nativo riconosce i livelli in un'applicazione PaaS (ruolo web e lavoro) e pertanto possibile correttamente distribuirle domini guasto e aggiornamento. I livelli in un'infrastruttura come un'applicazione di servizio (IaaS), invece, è necessario definire manualmente tramite set di disponibilità. Set di disponibilità sono necessari per un contratto di servizio in IaaS.

![Set di disponibilità per macchine virtuali di Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

Nella figura precedente, il livello di Internet Information Services (IIS) (che funziona come livello web app) e SQL (che funziona come un livello di dati) vengono assegnati ai set di disponibilità diverso. In questo modo che tutte le istanze di ogni livello dispongono ridondanza hardware distribuendo macchine virtuali di domini guasto e non prese livelli interi durante un aggiornamento.

###<a name="load-balancing"></a>Il bilanciamento del carico

Se le macchine virtuali è necessario applicare il traffico distribuito tra loro, è necessario raggruppare macchine virtuali in un'applicazione e carico il bilanciamento tra un endpoint TCP o UDP specifico. Per ulteriori informazioni, vedere [macchine virtuali di bilanciamento del carico](../virtual-machines/virtual-machines-linux-load-balance.md). Se le macchine virtuali ricevano input da un'altra origine (ad esempio meccanismo di Accodamento), un servizio di bilanciamento del carico non è più necessario. Bilanciamento del carico utilizza una verifica dell'integrità di base per determinare se è necessario inviare il traffico al livello di nodo. È anche possibile creare il proprio ricerche per implementare metriche integrità specifiche di un'applicazione che determinano se la macchina virtuale deve ricevere il traffico.

##<a name="storage"></a>Spazio di archiviazione

Spazio di archiviazione Azure è il servizio di dati permanenti previsto per Azure. Fornisce blob, tabella, coda e lo spazio di archiviazione di macchine Virtuali disco. Utilizza una combinazione di replica e la gestione delle risorse per fornire disponibilità all'interno di una singola Data Center. La disponibilità di spazio di archiviazione di Azure contratto di servizio garantisce che almeno 99,9 ricordarsi di questa operazione:

* Correttamente formattate richieste per l'aggiunta, aggiornamento, leggere ed eliminare dati verranno correttamente e correttamente elaborati.
* Gli account di archiviazione avrà la connettività al gateway Internet.

###<a name="replication"></a>Replica

Archiviazione Azure facilita la durata di dati, gestione di più copie di tutti i dati in unità diverse in sottosistema archiviazione fisica completamente indipendenti l'area geografica. Replica dei dati in modo sincrono e tutte le copie vengono eseguito il commit prima la scrittura sia riconosciuta. Archiviazione di Azure è fortemente coerente, ossia lettura è in modo da rispecchiare scrive più recente. Inoltre, copie dei dati vengono continuamente e analizzate per rilevare e correggere bit rot, un rischio spesso trascurato che si trova all'integrità dei dati memorizzati.

Servizi di trarre vantaggio da replica solo tramite lo spazio di archiviazione di Azure. Sviluppatore del servizio non è necessario eseguire ulteriori operazioni di ripristino da un errore locale.

###<a name="resource-management"></a>Gestione delle risorse

Gli account di archiviazione creati dopo maggio 2014, possono raggiungere fino a 500 TB (il valore massimo precedente era TB 200). Se è necessario ulteriore spazio, applicazioni devono progettate per utilizzare più account di archiviazione.

###<a name="virtual-machine-disks"></a>Dischi macchina virtuale

Disco rigido del computer virtuale archiviata come blob pagina nel sistema di archiviazione Azure, assegnarvi le stesse proprietà durata e scalabilità come archiviazione Blob. Questo schema rende persistente, i dati nel disco rigido del computer virtuale anche se il server che esegue la macchina virtuale ha esito negativo e la macchina virtuale deve essere riavviata in un altro server.

##<a name="database"></a>Database

###<a name="sql-database"></a>Database SQL

Database SQL Azure fornisce database come servizio. Consente alle applicazioni di provisioning rapidamente, inserire i dati e i database relazionali di query. Offre molte delle caratteristiche di SQL Server e funzionalità, familiarità durante astraendo il carico di hardware, configurazione, aggiornamento e adattabilità.

>[AZURE.NOTE] Database SQL Azure non fornisce uniformità caratteristica-a-uno con SQL Server. È destinata a soddisfare un insieme diverso di requisiti, ossia quella in modo univoco sono adatti alle applicazioni cloud (scalabilità flessibile, database come servizio per ridurre i costi di manutenzione e così via). Per ulteriori informazioni, vedere [scegliere un'opzione di SQL Server cloud: Database SQL Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Replica

Database SQL Azure fornisce predefiniti adattabilità agli errori di livello di nodo. Tutte le operazioni di scrittura in un database vengono automaticamente replicate in due o più nodi di sfondo tramite una tecnica di commit di base. (Primario e secondario almeno una è necessario verificare che l'attività è scritto nel log delle transazioni prima della transazione successo e restituisce.) In caso di errore di nodo, il database non riesce automaticamente su una replica secondario. Ciò causa un'interruzione di connessione temporanee per le applicazioni client. Per questo motivo, tutti i client di Database SQL Azure necessario implementare un tipo di gestione connessione temporaneo. Per ulteriori informazioni, vedere [Riprova indicazioni specifiche del servizio](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Gestione delle risorse

Ogni database, creazione, è configurato con un limite superiore. Le dimensioni massime attualmente disponibili sono 1 TB (dimensione limiti variano in base al livello del servizio, vedere [livelli di servizio e livelli di prestazioni del database di SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Quando un database raggiunge il limite di dimensioni superiori, Rifiuta altri comandi di inserimento o aggiornamento. (Query ed eliminazione dei dati è comunque possibile).

All'interno di un database, Database SQL Azure utilizza un'infrastruttura per gestire le risorse. Tuttavia, invece di un controller tessuti utilizza una topologia ad anello per rilevare errori. Ogni replica in un cluster ha due router adiacenti ed è responsabile per il rilevamento delle quando accedono verso il basso. Quando una replica non è raggiungibile, adiacenti attivano un agente di riconfigurazione per ricreare su un altro computer. Limitazione motore è disponibile per garantire che un server logico non utilizzare troppe risorse in un computer o superano i limiti fisici del computer.

###<a name="elasticity"></a>Flessibilità

Se l'applicazione richiede più del limite di database di 1 TB, è necessario implementare un approccio scalabilità. Scalabilità con Database di SQL Azure da manualmente partizioni, noto anche come sharding, dati su più database SQL. Questo approccio scalabilità offre la possibilità di ottenere la crescita del costo quasi lineare con scala. Crescita flessibile o capacità su richiesta possibile adatta ai costi incrementali in base alle esigenze in quanto i database vengono addebitati in base alla media effettiva dimensione usata al giorno, non in base a dimensioni massime consentite.

##<a name="sql-server-on-virtual-machines"></a>SQL Server in macchine virtuali

Mediante l'installazione di SQL Server (versione 2014 o versioni successive) in macchine virtuali di Azure, è possibile sfruttare le caratteristiche di disponibilità tradizionale di SQL Server. Queste caratteristiche includono disponibilità AlwaysOn e che rispecchiano database. Si noti che macchine virtuali di Azure, lo spazio di archiviazione e networking caratteristiche operative diverse rispetto a un'infrastruttura IT non virtualizzato in locale. Una corretta implementazione di un elevato disponibilità/emergenza soluzione (HA/DR) SQL Server in Azure, è necessario comprendere le differenze e progettare una soluzione di includerli.

###<a name="high-availability-nodes-in-an-availability-set"></a>Nodi di disponibilità di un set di disponibilità

Quando viene implementata una soluzione di disponibilità in Azure, è possibile utilizzare la disponibilità di impostare in Azure per inserire i nodi di disponibilità in domini separati guasto ed eseguire l'aggiornamento di domini. Per chiarezza, la disponibilità è un concetto Azure. È una procedura consigliata da seguire per essere certi che i database siano effettivamente disponibili, se si usa disponibilità AlwaysOn, che rispecchiano database o un altro elemento. Se non seguire questa procedura consigliata, potrebbe essere false si presuppone che il sistema è altamente disponibile. Tuttavia, in realtà, i nodi possono tutte contemporaneamente perché si verificano possono essere inseriti nello stesso dominio guasto nell'area di Azure.

Questo suggerimento non è applicabile con la distribuzione dei log. Come una caratteristica di ripristino di emergenza, è necessario assicurarsi che i server eseguono nelle aree di Azure separate. Per definizione, queste aree sono domini guasto separata.

Per Azure Cloud Services macchine virtuali distribuito tramite il portale classico stiano nello stesso set di disponibilità, è necessario distribuirle nel servizio Cloud stesso. Macchine virtuali distribuite tramite Gestione risorse Azure (portal corrente) non dispone di questa limitazione. Per portale classica distribuito macchine virtuali nel servizio Cloud di Azure, solo i nodi lo stesso servizio Cloud possono partecipare lo stesso set di disponibilità. Inoltre, macchine virtuali di servizi Cloud dovrebbe essere nella stessa rete virtuale per garantire la conservazione loro indirizzi IP anche dopo la correzione di servizio. Consente di evitare interruzioni update DNS.

###<a name="azure-only-high-availability-solutions"></a>Solo Azure: le soluzioni di disponibilità

È possibile impostare una soluzione di disponibilità per i database di SQL Server in Azure utilizzando disponibilità AlwaysOn o database che rispecchiano.

Nel diagramma seguente viene illustrata l'architettura di disponibilità AlwaysOn in esecuzione in macchine virtuali di Azure. Il diagramma è stato accettato dall'articolo di approfondimento su questo argomento, [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Disponibilità AlwaysOn in Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

È possibile eseguire automaticamente anche provisioning un disponibilità AlwaysOn distribuzione-to-end in macchine virtuali di Azure utilizzando il modello AlwaysOn nel portale di Azure. Per ulteriori informazioni, vedere [SQL Server AlwaysOn che offre la possibilità di Microsoft Azure portale raccolta](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

Nel diagramma seguente viene illustrato l'utilizzo del database che rispecchiano in macchine virtuali di Azure. Anche intrapresa da un argomento in modo completo [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Database che rispecchiano in Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Entrambe le architetture richiedono un controller di dominio. Tuttavia, con che rispecchiano database, è possibile utilizzare i certificati server per eliminare la necessità di un controller di dominio.

##<a name="other-azure-platform-services"></a>Altri servizi di piattaforma Azure

Applicazioni basate su Azure trarre vantaggio dalle funzionalità di piattaforma per recuperare gli errori locali. In alcuni casi, è possibile eseguire azioni specifiche per aumentare la disponibilità per il proprio scenario specifico.

###<a name="service-bus"></a>Bus di servizio

Per ridurre i rischi temporaneamente fuori del Bus di servizio Azure, è consigliabile creare una coda sul lato client permanente. Temporaneamente utilizza un meccanismo di memorizzazione alternativo, locale per archiviare i messaggi che non possono essere aggiunti a coda Bus di servizio. L'applicazione possa decidere in che modo gestire i messaggi archiviati temporaneamente dopo il servizio ripristinato. Per ulteriori informazioni, vedere [procedure consigliate per miglioramenti delle prestazioni con Bus di servizio negoziate messaggistica](../service-bus-messaging/service-bus-performance-improvements.md) e [Bus di servizio (ripristino)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Per impostazione predefinita in archiviazione Blob Azure dati associato a Azure HDInsight. Spazio di archiviazione Azure specifica le proprietà di disponibilità e durata per l'archiviazione Blob. L'elaborazione di più nodi associato a processi Hadoop MapReduce si verifica in un temporaneo Hadoop Distributed File sistema (HDFS) che viene eseguito il provisioning HDInsight avviarlo quando richiesto. Risultati da un processo MapReduce vengono memorizzati anche per impostazione predefinita in archiviazione Blob Azure, in modo che i dati trasformati diventano permanenti e rimangono disponibili dopo il cluster Hadoop viene rimosso con deprovisioning. Per ulteriori informazioni, vedere [HDInsight (ripristino)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Elenchi di controllo per errori locali

###<a name="cloud-services"></a>Servizi cloud

  1. Nella sezione servizi Cloud di questo documento.
  2. Configurare almeno due istanze per ogni ruolo.
  3. Mantenere stato nell'archivio permanente, non disponibili sulla istanze del ruolo.
  4. Gestire correttamente l'evento StatusCheck.
  5. Disporre le modifiche correlate in transazioni laddove possibile.
  6. Verificare che l'attività di ruoli di lavoro siano idempotenti e riavvio.
  7. Continuare a richiamare operazioni fino a quando non riescono a.
  8. Valutare la possibilità di strategie di adattamento automatico.

###<a name="virtual-machines"></a>Macchine virtuali

  1. Nella sezione macchine virtuali di questo documento.
  2. Non utilizzare unità D per l'archiviazione permanente.
  3. Raggruppare i computer di un livello di servizio in un set di disponibilità.
  4. Configurare il bilanciamento del carico e le ricerche facoltative.

###<a name="storage"></a>Spazio di archiviazione

  1. Nella sezione dello spazio di archiviazione di questo documento.
  2. Usare più account di archiviazione dati o della larghezza di banda supera le quote.

###<a name="sql-database"></a>Database SQL

  1. Nella sezione Database SQL di questo documento.
  2. Implementare un criterio di tentativi per la gestione degli errori temporanei.
  3. Utilizzare partizioni/sharding come una strategia di scalabilità.

###<a name="sql-server-on-virtual-machines"></a>SQL Server in macchine virtuali

  1. Esaminare SQL Server in macchine virtuali di questo documento.
  2. Seguire i consigli precedenti per macchine virtuali.
  3. Utilizzare le funzionalità di disponibilità di SQL Server, ad esempio AlwaysOn.

###<a name="service-bus"></a>Bus di servizio

  1. Nella sezione servizio Bus di questo documento.
  2. È consigliabile creare una coda di lato client permanente come backup.

###<a name="hdinsight"></a>HDInsight

  1. Nella sezione HDInsight di questo documento.
  2. Nessun ulteriore disponibilità sono necessarie operazioni per errori locali.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie basata su [indicazioni tecniche sulla resilienza Azure](./resiliency-technical-guidance.md). Articolo successivo in questa serie è [il ripristino di un'interruzione del servizio a livello di area geografica](./resiliency-technical-guidance-recovery-loss-azure-region.md).
