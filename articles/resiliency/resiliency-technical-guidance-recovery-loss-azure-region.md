<properties
   pageTitle="Flessibilità per il ripristino da perdita di indicazioni tecniche un'area geografica Azure | Microsoft Azure"
   description="Articolo su informazioni e la progettazione di applicazioni tolleranza di errore flessibili, disponibilità, nonché di pianificazione di emergenza"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Indicazioni tecniche sulla resilienza Azure: ripristino di un'interruzione del servizio a livello di area

Azure viene suddivisa fisico e logico in unità denominate aree geografiche. Un'area costituita da Data Center di uno o più vicino. Al momento della loro Azure aree 24 tutto il mondo.

In alcuni casi è possibile che strutture di un'intera regione possono diventare inaccessibili, ad esempio a causa di errori di rete. O strutture possono essere persi completamente, ad esempio a causa di emergenza naturale. In questa sezione vengono illustrate le funzionalità di Azure per la creazione di applicazioni distribuiti in aree geografiche. Ad esempio distribuzione consente di ridurre al minimo la probabilità che un errore in un'area potrebbe influire sulle altre aree.

##<a name="cloud-services"></a>Servizi cloud

###<a name="resource-management"></a>Gestione delle risorse

Creazione di un servizio cloud separata in ogni paese di destinazione e quindi pubblicando il pacchetto di distribuzione per ogni servizio cloud, è possibile distribuire le istanze di calcolo tra le aree. Tuttavia, si noti che la distribuzione del traffico servizi cloud in diverse aree geografiche deve essere implementata lo sviluppatore dell'applicazione o con un servizio di gestione del traffico.

Determinazione del numero di istanze del ruolo riserva per distribuire in anticipo di emergenza è importante per la pianificazione della capacità. La presenza di una distribuzione secondaria completa garantisce che la capacità è già disponibile quando necessario; Tuttavia, si raddoppia il costo. Un modello comune consiste nel dispone di una distribuzione small, secondaria appena grande per l'esecuzione di servizi critici. Distribuzione secondaria small è consigliabile, sia per prenotare capacità e per verificare la configurazione dell'ambiente secondario.

>[AZURE.NOTE]La quota di abbonamento non è una garanzia capacità. La quota è semplicemente un limite di credito. Per garantire la capacità, è necessario definire il numero richiesto di ruoli nel modello di servizio e i ruoli devono essere distribuiti.

###<a name="load-balancing"></a>Il bilanciamento del carico

Per il bilanciamento del carico il traffico attraverso aree richiede una soluzione di gestione del traffico. Azure offre [Azure il traffico Manager](https://azure.microsoft.com/services/traffic-manager/). È possibile usufruire dei servizi di terze parti che offrono funzionalità di gestione il traffico simile.

###<a name="strategies"></a>Strategie

Molte strategie alternative sono disponibili per l'implementazione di elaborazione distribuito in aree geografiche. Questi devono essere adattati alle specifiche esigenze e circostanze dell'applicazione. Un alto livello, gli approcci possono essere suddivisi nelle categorie seguenti:

  * __Ridistribuire su emergenza__: questo approccio l'applicazione viene ridistribuito da zero in fase di emergenza. Si tratta appropriata per le applicazioni non critici che non richiedono un tempo di recupero garantito.

  * __Caldo riserva (attivo/passivo)__: un servizio di hosting secondario verrà creato in un'area alternativa e i ruoli sono distribuiti per garantire la capacità minima; Tuttavia, i ruoli non ricevano il traffico di produzione. Questo approccio è utile per le applicazioni che non sono state progettate per distribuire il traffico in aree geografiche.

  * __Riserva hot (attivo)__: l'applicazione è progettata per ricevere carico di produzione in più aree. Servizi cloud in ogni area potrebbero essere configurati per maggiore capacità maggiore di quello necessario per il ripristino. In alternativa, è possibile che in servizi cloud out in base alle esigenze al momento di emergenza e failover. Questo approccio richiede investito nella finestra di progettazione di applicazioni, ma presenta vantaggi significativi. Ad esempio tempi di ripristino bassa e garantito, continuo test di tutte le posizioni di ripristino e l'utilizzo efficiente della capacità.

Una descrizione completa della struttura distribuito non rispetta l'ambito di questo documento. Per ulteriori informazioni, vedere [ripristino di emergenza e disponibilità per le applicazioni di Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Macchine virtuali

Ripristino dell'infrastruttura come un servizio (IaaS) macchine () è simile alla piattaforma come servizio PaaS () calcolare ripristino alcune differenze. Vi sono differenze importanti, tuttavia, che una VM IaaS è costituito da macchina virtuale e il disco di macchine Virtuali.

  * __Usare Azure Backup per creare l'area geografica cross backup che sono applicazione coerente__.
  [Copia di Backup di Azure](https://azure.microsoft.com/services/backup/) consente agli utenti di creare backup coerenti applicazione su più dischi macchine Virtuali e supportare la replica di backup in aree geografiche. È possibile eseguire questa operazione scegliendo da replicare geografico archivio di backup al momento della creazione. Si noti che la replica del backup archivio deve essere configurato al momento della creazione. Non è possibile impostare in un secondo momento. Se un'area viene persa, verranno resi i backup disponibili per i clienti. I clienti saranno in grado di ripristinare uno qualsiasi dei loro punti di ripristino configurato.

  * __Separare il disco di dati dal disco di sistema operativo__. Un fattore importante per macchine virtuali IaaS è non è possibile modificare il disco di sistema operativo senza ricreare la macchina virtuale. Questa operazione non è un problema se strategia di ripristino è necessario ridistribuire dopo emergenza. Tuttavia, può essere un problema se si utilizza l'approccio caldo riserva di prenotare la capacità. A questo scopo correttamente, è necessario disporre il disco di sistema operativo corretto distribuito in entrambe le posizioni principali e secondarie e i dati dell'applicazione devono venire archiviati in un'unità separata. Se possibile, utilizzare una configurazione standard del sistema operativo che può essere fornita in entrambe le posizioni. Dopo aver caso di errore, è necessario collegare quindi l'unità di dati nelle macchine virtuali IaaS esistente nel contesto di periferica secondario. Utilizzare AzCopy per copiare snapshot di dischi dati in un sito remoto.

  * __Prestare particolare attenzione gli eventuali problemi di coerenza dopo geografico-caso di errore di più dischi macchine Virtuali__. Macchine Virtuali dischi implementati come BLOB Azure lo spazio di archiviazione e avere le stesse caratteristiche della replica geografico. A meno che non viene utilizzato [Il Backup di Azure](https://azure.microsoft.com/services/backup/) , non esistono garanzie di coerenza dischi, poiché geografico replica è asincrona e replica in modo indipendente. Singoli dischi macchine Virtuali sono necessariamente un arresto anomalo coerente lo stato dopo geografico-caso di errore, ma non coerente su dischi. Ciò potrebbe causare problemi in alcuni casi (ad esempio nel caso striping del disco).

##<a name="storage"></a>Spazio di archiviazione

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Ripristino utilizzando geografico ridondanti archiviazione blob, tabella, coda e lo spazio di archiviazione di macchine Virtuali su disco

In Azure, BLOB, tabelle, code e macchine Virtuali dischi sono tutti geografico replicato per impostazione predefinita. In questo caso per lo spazio di archiviazione ridondanti geografico (GRS). GRS replica lo spazio di archiviazione dati un Data Center accoppiate centinaia di miglia distanza all'interno di una specifica località geografica. GRS è progettato per fornire ulteriore durata in caso di emergenza Data Center principali. Controlli di Microsoft in caso di failover e failover è limitato al emergenza principali in cui nella posizione originale principale è considerata irreversibile certo tempo. In alcuni casi può trattarsi diversi giorni. Dati sono in genere replicati nell'arco di qualche minuto, anche se l'intervallo di sincronizzazione non è ancora coperto da un contratto di servizio.

Invariato geografico-, non ci sarà alcuna modifica per la modalità di accesso di account (la chiave account e URL, non cambiano). Account di archiviazione, tuttavia, sarà in un'area diversa dopo il failover. Questa operazione può influire sulle applicazioni che richiedono internazionali affinità con il proprio account di archiviazione. Anche per i servizi e applicazioni che non richiedono un account di archiviazione in Data Center del stesso, la data center tra latenza e in base alle tariffe della larghezza di banda potrebbero essere buoni motivi per spostare temporaneamente il traffico nell'area failover. Questo potrebbe suddividere in una strategia di ripristino di emergenza globale.

Oltre a failover automatico fornito da GRS, Azure è stato introdotto un servizio che offre accesso in lettura alla copia dei dati nella posizione di archiviazione secondario. Si tratta di accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS).

Per ulteriori informazioni sull'archiviazione GRS e RA GRS, vedere [replica di archiviazione Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Mapping area geografico replica:

È importante sapere dove i dati si replicato geografico, per sapere dove distribuire altre istanze dei dati che richiedono internazionali affinità con lo spazio di archiviazione. Nella tabella seguente mostra le associazioni percorso primario e secondario:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>La replica geografico prezzi:

La replica geografico è incluso in prezzi correnti per l'archiviazione di Azure. Si tratta dello spazio di archiviazione ridondanti geografico (GRS). Se non si desidera i dati replicato geografico è possibile disattivare la replica geografico per il proprio account. Questo comando si chiama localmente lo spazio di archiviazione ridondanti e viene addebitata un prezzo confrontate con quelle di GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Stabilire se si è verificato geografico-caso di errore

Se si verifica geografico-caso di errore, questo verrà registrato al [Dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/). Le applicazioni di implementare un modo automatizzato di rilevamento, tuttavia, controllando l'area geografico per il proprio account di archiviazione. Questo può essere utilizzato per avviare altre operazioni di ripristino, ad esempio l'attivazione di risorse di elaborazione nell'area geografico dove spostato di archiviazione. È possibile eseguire una query per questa dal servizio Gestione API, utilizzando [Ottenere proprietà Account di archiviazione](https://msdn.microsoft.com/library/ee460802.aspx). Le relative proprietà sono:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Dischi macchine Virtuali e geografico failover

Come illustrato nella sezione dischi macchine Virtuali, non esistono garanzie per la coerenza dei dati su dischi macchine Virtuali dopo caso di errore. Per garantire la correttezza di backup, un prodotto di backup, ad esempio Data Protection Manager deve essere utilizzato per eseguire il backup e ripristino dei dati dell'applicazione.

##<a name="database"></a>Database

###<a name="sql-database"></a>Database SQL

Database SQL Azure offre due tipi di ripristino: geografico Ripristina e replica di geografico Active.

####<a name="geo-restore"></a>Ripristino geografico

[Ripristino geografico](../sql-database/sql-database-recovery-using-backups.md#geo-restore) disponibile anche con database di base, Standard e Premium. Fornisce l'opzione di ripristino predefinito quando il database è disponibile a causa di un evento imprevisto nell'area in cui si trova il database. Come ripristinare In un momento, Ripristina geografico si basa su Backup database in geografico ridondanti dello spazio di archiviazione Azure. Ripristina dalla copia di backup replicate geografico e pertanto è adattabile alle interruzioni lo spazio di archiviazione nell'area principale. Per ulteriori informazioni, vedere [ripristinare un Database SQL Azure o il controllo a secondario](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Replica di geografico Active

[La replica di geografico Active](../sql-database/sql-database-geo-replication-overview.md) è disponibile per tutti i livelli di database. Progettato per le applicazioni che hanno requisiti di ripristino maggiormente rispetto a può offrire geografico Ripristina. Usa la replica di geografico Active, è possibile creare fino a quattro dei secondari leggibili su server in diverse aree geografiche. È possibile avviare il controllo a uno dei database secondario. Inoltre, la replica di geografico Active utilizzabile per supportare gli scenari di aggiornamento o il trasferimento di applicazioni, come il bilanciamento del carico per carichi di lavoro di sola lettura. Per informazioni dettagliate, vedere [configurare la replica geografico](../sql-database/sql-database-geo-replication-portal.md) e di [non riuscire sopra al database secondario](../sql-database/sql-database-geo-replication-failover-portal.md). Per informazioni dettagliate su come progettare e implementare applicazioni e aggiornamento di applicazioni senza tempo di inattività, fare riferimento alla [struttura un'applicazione di emergenza cloud utilizzando la replica geografico attivo nel Database di SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [gestione degli aggiornamenti di applicazioni cloud SQL attiva geografico-replica di Database](../sql-database/sql-database-manage-application-rolling-upgrade.md) .

###<a name="sql-server-on-virtual-machines"></a>SQL Server in macchine virtuali

Una vasta gamma di opzioni sono disponibili per il ripristino e la disponibilità di SQL Server 2012 (e versioni successive) in esecuzione in macchine virtuali di Azure. Per ulteriori informazioni, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Altri servizi di piattaforma Azure

Quando si tenta di eseguire il servizio cloud in più aree di Azure, è necessario considerare le implicazioni per ognuna delle dipendenze. Nelle sezioni seguenti, le indicazioni specifiche del servizio presuppone che è necessario utilizzare lo stesso servizio Azure in un Data Center Azure alternativo. Questo comporta la configurazione e attività di replica di dati.

>[AZURE.NOTE]In alcuni casi, la procedura seguente consente di misure per limitarli tramite un'interruzione del servizio specifici invece di un evento intero Data Center. Dal punto di vista dell'applicazione, potrebbe essere un'interruzione del servizio specifici semplicemente come limitare e richiedono temporaneamente la migrazione del servizio a un'area di Azure alternativa.

###<a name="service-bus"></a>Bus di servizio

Azure servizio Bus utilizza uno spazio dei nomi univoco che si estendono su più aree Azure. Pertanto prima è necessario installare gli spazi dei nomi di servizio necessario bus nell'area alternativo. Tuttavia, sono disponibili anche considerazioni per la durata dei messaggi in coda. Esistono diverse strategie per replica di messaggi tra le regioni Azure. Per informazioni dettagliate su queste strategie di replica e altre strategie di ripristino di emergenza, vedere [procedure consigliate per le applicazioni isolanti contro interruzioni Bus di servizio e guasti](../service-bus-messaging/service-bus-outages-disasters.md). Per ulteriori informazioni sulla disponibilità, vedere [Bus di servizio (disponibilità)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Servizio di App

Per eseguire la migrazione di un'applicazione di servizio App Azure, ad esempio Web Apps o App Mobile, a un'area di Azure secondaria, è necessario avere una copia di backup del sito Web disponibili per la pubblicazione. Se l'interruzione non comporta l'intero centro dati di Azure, potrebbe essere possibile utilizzare FTP per scaricare una copia di backup recente del contenuto del sito. Creare una nuova app nell'area alternativo, a meno che non è già stato fatto per prenotare la capacità. Pubblicare il sito per la nuova area e apportare le modifiche necessarie. Tali modifiche possono includere le stringhe di connessione di database o altre impostazioni specifiche. Se necessario, aggiungere il certificato SSL del sito e modificare il record CNAME DNS in modo che il nome di dominio personalizzato fa riferimento a URL ridistribuita di Azure Web App.

###<a name="hdinsight"></a>HDInsight

Per impostazione predefinita in archiviazione Blob Azure vengono archiviati i dati associati HDInsight. HDInsight è necessario che un cluster di Hadoop elaborazione dei processi MapReduce deve essere situato nella stessa regione come account di archiviazione che contiene i dati da analizzare. Se si utilizza la funzionalità di replica geografico disponibile per lo spazio di archiviazione di Azure, è possibile accedere ai dati presenti nell'area secondario in cui i dati è stati replicati se per qualche motivo l'area principale non è più disponibile. È possibile creare un nuovo cluster Hadoop nell'area in cui i dati replicati e continuano l'elaborazione. Per ulteriori informazioni sulla disponibilità, vedere [HDInsight (disponibilità)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Creazione di report di SQL

In questo momento il ripristino da perdita di un'area di Azure richiede più istanze di SQL Reporting in diverse aree geografiche Azure. Queste istanze di SQL Reporting devono accedere agli stessi dati, e che devono avere dati proprio ripristino piano in caso di emergenza. È anche possibile mantenere esterne copie di backup del file RDL per ogni rapporto.

###<a name="media-services"></a>Servizi multimediali di Microsoft

Servizi multimediali di Azure ha un approccio ripristino diversi per la codifica e trasmissione. In genere, streaming è più importante durante un periodo di inattività internazionali. Per eseguire questa operazione, è un account di servizi multimediali in due aree di Azure diverse. Contenuto codificato deve trovarsi in entrambe le aree geografiche. In caso di errore, è possibile reindirizzare il traffico di trasmissione all'area alternativo. Codifica può essere eseguita in un'area di Azure. Se la codifica è urgenti, ad esempio durante l'elaborazione di eventi live, è necessario essere pronti a inviare processi a un Data Center alternativo in caso di errore.

###<a name="virtual-network"></a>Rete virtuale

File di configurazione offrono il modo più rapido per impostare una rete virtuale in un'area di Azure alternativa. Dopo aver configurato la rete virtuale nell'area di Azure principale, [esportare le impostazioni di rete virtuale](../virtual-network/virtual-networks-create-vnet-classic-portal.md) per la rete corrente in un file di configurazione della rete. In caso di un'interruzione nell'area principale, [ripristinare la rete virtuale](../virtual-network/virtual-networks-create-vnet-classic-portal.md) dal file di configurazione archiviata. Configurare altri servizi cloud, macchine virtuali o impostazioni locali croce per l'uso con la nuova rete virtuale.

##<a name="checklists-for-disaster-recovery"></a>Elenchi di controllo per il ripristino di emergenza

##<a name="cloud-services-checklist"></a>Elenco di controllo servizi cloud

  1. Nella sezione servizi Cloud di questo documento.
  2. Creare una strategia di ripristino di emergenza tra regione.
  3. Comprendere compromessi nella prenotazione di capacità in aree geografiche alternative.
  4. Utilizzare gli strumenti del ciclo di traffico, ad esempio Azure il traffico Manager.

##<a name="virtual-machines-checklist"></a>Elenco di controllo macchine virtuali

  1. Nella sezione macchine virtuali di questo documento.
  2. Utilizzare [Backup Azure](https://azure.microsoft.com/services/backup/) per creare backup coerente delle applicazioni in aree geografiche.

##<a name="storage-checklist"></a>Elenco di controllo per lo spazio di archiviazione

  1. Nella sezione dello spazio di archiviazione di questo documento.
  2. Non disabilitare la replica geografico delle risorse di archiviazione.
  3. Comprendere l'area geografica alternativo per la replica geografico in caso di failover.
  4. Creare personalizzate strategie di backup per strategie di failover controllata dall'utente.

##<a name="sql-database-checklist"></a>Elenco di controllo di Database SQL

  1. Nella sezione Database SQL di questo documento.
  2. Utilizzare [La replica geografico](../sql-database/sql-database-geo-replication-overview.md) o [Ripristina geografico](../sql-database/sql-database-recovery-using-backups.md#geo-restore) in base alle esigenze.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server in macchine virtuali elenco di controllo

  1. Esaminare SQL Server in macchine virtuali di questo documento.
  2. Utilizzare gruppi di disponibilità AlwaysOn area tra o database che rispecchiano.
  3. In alternativa utilizzare backup e ripristino nell'archiviazione blob.

##<a name="service-bus-checklist"></a>Elenco di controllo Bus di servizio

  1. Nella sezione servizio Bus di questo documento.
  2. Configurare uno spazio dei nomi Bus di servizio in un'area alternativa.
  3. Valutare la possibilità di strategie di replica personalizzate per i messaggi in aree geografiche.

##<a name="app-service-checklist"></a>Elenco di controllo servizio App

  1. Nella sezione servizio App di questo documento.
  2. Mantenere il backup del sito di fuori area principale.
  3. Se interruzione parziale, provare a recuperare sito corrente con FTP.
  4. Pianificare la distribuzione del sito al sito nuovo o esistente in un'area alternativo.
  5. Pianificare le modifiche alla configurazione per l'applicazione e i record DNS CNAME.

##<a name="hdinsight-checklist"></a>Elenco di controllo HDInsight

  1. Nella sezione HDInsight di questo documento.
  2. Creare un nuovo cluster Hadoop nell'area dati replicato.

##<a name="sql-reporting-checklist"></a>Elenco di controllo dei report di SQL

  1. Nella sezione SQL Reporting di questo documento.
  2. Gestire un'istanza di SQL Reporting alternativa in un'area diversa.
  3. Mantenere un piano separato per replicare la destinazione di tale area.

##<a name="media-services-checklist"></a>Elenco di controllo servizi multimediali

  1. Nella sezione Servizi multimediali di questo documento.
  2. Creare un account di servizi multimediali in un'area alternativa.
  3. Codificare lo stesso contenuto in entrambe le aree di cedere il flusso.
  4. Inviare processi di codifica a un'area alternativo in caso di un'interruzione del servizio.

##<a name="virtual-network-checklist"></a>Elenco di controllo di rete virtuale

  1. Nella sezione virtuali di questo documento.
  2. Usare esportata le impostazioni di rete virtuale per ricreare in un'altra area.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie basata su [indicazioni tecniche sulla resilienza Azure](./resiliency-technical-guidance.md). Articolo successivo in questa serie è incentrata su [ripristino da un data center locale in Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
