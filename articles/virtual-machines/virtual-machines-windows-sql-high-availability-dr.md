<properties
    pageTitle="Disponibilità e ripristino per SQL Server | Microsoft Azure"
    description="Una discussione dei vari tipi di strategie HADR per SQL Server in macchine virtuali di Azure in esecuzione."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Alto disponibilità e ripristino di emergenza per SQL Server in macchine virtuali di Azure

## <a name="overview"></a>Panoramica

Microsoft Azure macchine () con SQL Server consente di ridurre i costi di un'elevata disponibilità e ripristino (HADR) soluzione di database. La maggior parte delle soluzioni di SQL Server HADR sono supportate in Azure macchine virtuali, come solo Azure e come le soluzioni ibride. In una soluzione solo Azure l'intero sistema HADR viene eseguito in Azure. In una configurazione ibrida, parte della soluzione viene eseguito in Azure e le altre parte verrà eseguito in locale all'interno dell'organizzazione. Flessibilità dell'ambiente di Azure consente di spostare parzialmente o completamente in Azure per soddisfare i requisiti di HADR dei sistemi di database SQL Server e il budget.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Informazioni sulla necessità di una soluzione HADR

È possibile assicurarsi che il sistema di database possiede le funzionalità HADR che richiede il contratto di servizio (contratto di servizio). Il fatto che Azure fornisce meccanismi di disponibilità, ad esempio servizio di correzione per servizi cloud e rilevamento di ripristino di errore per macchine virtuali non garantisce Nessun che è possibile soddisfare il contratto di servizio desiderato. Questi meccanismi di proteggono la disponibilità delle macchine virtuali ma non la disponibilità di SQL Server in esecuzione in macchine virtuali. È possibile che l'istanza di SQL Server mentre la macchina virtuale è in linea e integro. Inoltre, anche la disponibilità meccanismi forniti da Azure non consentano i tempi di inattività di macchine virtuali a causa di eventi, ad esempio il ripristino da software o errori hardware e aggiornamenti del sistema operativo.

Inoltre, geografico ridondante lo spazio di archiviazione (GRS) in Azure, che sarà implementata con la funzione replica geografico, potrebbe non essere una soluzione di ripristino di emergenza adeguata per i database. Poiché la replica geografico invia dati in modo asincrono, gli aggiornamenti recenti possono essere persi in caso di emergenza. Ulteriori informazioni relative ai limiti della replica geografico sono descritte nella sezione [geografico replica non è supportata per i file di dati e log su dischi separati](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Architetture di distribuzione HADR

Tecnologie SQL Server HADR supportate in Azure includono:

- [Sempre in gruppi di disponibilità](https://technet.microsoft.com/library/hh510230.aspx)
- [Database che rispecchiano](https://technet.microsoft.com/library/ms189852.aspx)
- [Distribuzione dei log](https://technet.microsoft.com/library/ms187103.aspx)
- [Eseguire il backup e ripristino con il servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Sempre in istanze Cluster di Failover](https://technet.microsoft.com/library/ms189134.aspx)

È possibile combinare le tecnologie per implementare una soluzione di SQL Server con disponibilità e ripristino di emergenza. A seconda della tecnologia in uso, una distribuzione ibrida può richiedere un tunnel VPN con la rete virtuale Azure. Nelle sezioni seguenti vengono illustrano alcuni dei architetture di implementazione di esempio.

## <a name="azure-only-high-availability-solutions"></a>Solo Azure: le soluzioni di disponibilità

È possibile trovare una soluzione di disponibilità per i database di SQL Server in Azure sempre sulla disponibilità a gruppi o database che rispecchiano.

| Tecnologia                               | Architetture di esempio                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre in gruppi di disponibilità**        | Ogni replica disponibilità in esecuzione in macchine virtuali di Azure disponibilità elevata all'interno dell'area stessa. È necessario configurare un controller di dominio macchine Virtuali, perché Windows Server Failover cluster (WSFC) richiede un dominio Active Directory.<br/> ![Sempre in gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Per ulteriori informazioni, vedere [Configurare sempre nella disponibilità gruppi in Azure grafica](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Sempre in istanze Cluster di Failover** | Failover Cluster istanze (cliente) che richiedono archiviazione condivisa, possono essere create in modi diversi 2.<br/><br/>1. un cliente in un WSFC due nodi in esecuzione in macchine virtuali di Azure con lo spazio di archiviazione supportato da una soluzione cluster di terze parti. Per un esempio specifico che utilizza SIOS DataKeeper, vedere [disponibilità di una condivisione file utilizzando WSFC e software di terze parti 3 ° SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. un cliente in un WSFC due nodi in esecuzione in macchine virtuali di Azure con destinazione iSCSI remoto condiviso blocco dello spazio di archiviazione tramite ExpressRoute. NetApp privata dello spazio di archiviazione dei criteri, ad esempio, esporre una destinazione iSCSI tramite ExpressRoute con Equinix alle macchine virtuali di Azure.<br/><br/>Per lo spazio di archiviazione condiviso di terze parti e soluzioni di replica di dati, contattare il fornitore per eventuali problemi relativi all'accesso ai dati in caso di failover.<br/><br/>Nota utilizzando cliente nella parte superiore di [spazio di archiviazione File Azure](https://azure.microsoft.com/services/storage/files/) non è supportato ancora, poiché questa soluzione non utilizza lo spazio di archiviazione Premium. Stiamo lavorando per supportare questa breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Solo Azure: soluzioni di ripristino di emergenza

È disponibile una soluzione di ripristino di emergenza per il database di SQL Server in Azure utilizzo sempre sulla disponibilità dei gruppi, database che rispecchiano o copia di backup e ripristino con BLOB di spazio di archiviazione.

| Tecnologia                               | Architetture di esempio                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre in gruppi di disponibilità**        | Repliche disponibilità in esecuzione in più Data Center in macchine virtuali di Azure di emergenza. Questa soluzione tra area consente di proteggere interruzione completo del sito. <br/> ![Sempre in gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>In un'area, ogni replica deve essere compreso tra lo stesso servizio cloud e VNet stesso. Poiché a ogni area avrà un VNet separato, queste soluzioni richiedono VNet alla connettività VNet. Per ulteriori informazioni, vedere [configurare una connessione VPN da sito nel portale di classica Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **Database che rispecchiano**                   | Capitale e simmetrici e i server eseguono nei data center diverso per il ripristino di emergenza. È necessario distribuire utilizzando i certificati server perché non è più Data Center dell'intervallo di dominio active directory.<br/>![Database che rispecchiano](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Eseguire il backup e ripristino con il servizio di archiviazione Blob Azure** | Backup database di produzione direttamente a archiviazione blob in un Data Center diverso per il ripristino di emergenza.<br/>![Eseguire il backup e ripristino](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Per ulteriori informazioni, vedere [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>Ibrido IT: Soluzioni di ripristino di emergenza

Dispone di una soluzione di ripristino di emergenza per i database di SQL Server in un ambiente ibrido IT utilizzando sempre in gruppi disponibilità, che rispecchiano database, la distribuzione dei log e copia di backup e ripristino con lo spazio di archiviazione di Azure blog.

| Tecnologia                               | Architetture di esempio                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre in gruppi di disponibilità**        | Alcune repliche disponibilità in esecuzione in macchine virtuali di Azure e sé esecuzione locale di emergenza intersito. Il sito di produzione può essere in locale o in un Data Center di Azure.<br/>![Sempre in gruppi di disponibilità](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Poiché ogni replica disponibilità deve essere dello stesso cluster WSFC, cluster WSFC deve essere esteso entrambe le reti (un cluster WSFC più subnet). Questa configurazione richiede una connessione VPN tra Azure e la rete locale.<br/><br/>Per il ripristino di emergenza ha esito positivo dei database, installare anche un controller di dominio replica nel sito di ripristino di emergenza.<br/><br/>È possibile utilizzare la creazione guidata Replica aggiungere in SQL Server Management Studio per aggiungere una replica Azure a un sempre nella disponibilità gruppo esistente. Per ulteriori informazioni, vedere Esercitazione: estendere le sempre nel gruppo di disponibilità in Azure. |
| **Database che rispecchiano**                   | Un partner in esecuzione in una macchina virtuale Azure e altri esecuzione locale di emergenza intersito i certificati server. Non è necessario essere nello stesso dominio di Active Directory partner e non è richiesta alcuna connessione VPN.<br/>![Database che rispecchiano](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Un altro database che rispecchiano scenario prevede un partner in esecuzione in una macchina virtuale Azure e le altre in esecuzione in locale nello stesso dominio di Active Directory per il ripristino di emergenza intersito. È necessaria una [connessione VPN tra la rete virtuale Azure e la rete locale](../vpn-gateway/vpn-gateway-site-to-site-create.md) .<br/><br/>Per il ripristino di emergenza ha esito positivo dei database, installare anche un controller di dominio replica nel sito di ripristino di emergenza. |
| **Distribuzione dei log**                         | Un server che esegue una macchina virtuale Azure e altri esecuzione locale di emergenza intersito. La distribuzione dei log dipende dalla condivisione file di Windows, in modo che è necessaria una connessione VPN tra la rete virtuale Azure e la rete locale.<br/>![Distribuzione dei log](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Per il ripristino di emergenza ha esito positivo dei database, installare anche un controller di dominio replica nel sito di ripristino di emergenza. |
| **Eseguire il backup e ripristino con il servizio di archiviazione Blob Azure** | Database di produzione backup direttamente a archiviazione blob Azure di emergenza in locale.<br/>![Eseguire il backup e ripristino](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Per ulteriori informazioni, vedere [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerazioni importanti per SQL Server HADR in Azure

Azure macchine virtuali, lo spazio di archiviazione e reti hanno caratteristiche operative diverse rispetto a un'infrastruttura IT non virtualizzato in locale. Una corretta implementazione di una soluzione HADR SQL Server in Azure, è necessario comprendere le differenze e progettare una soluzione di includerli.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nodi di disponibilità di un set di disponibilità

Set di disponibilità in Azure consente di inserire i nodi di disponibilità separata guasto (FDs) e i domini aggiornamento (UDs). Per le macchine virtuali di Azure collocare nello stesso set di disponibilità, è necessario distribuire loro lo stesso servizio cloud. Solo i nodi lo stesso servizio cloud partecipare lo stesso set di disponibilità. Per ulteriori informazioni, vedere [gestire la disponibilità di macchine virtuali](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportamento di cluster WSFC in rete Azure

Il servizio DHCP non compatibili con RFC in Azure può causare la creazione di alcune configurazioni cluster WSFC a causa il nome della rete cluster viene assegnato un indirizzo IP duplicato, ad esempio lo stesso indirizzo IP di uno dei nodi cluster. Si tratta di un problema durante l'implementazione di sempre nella disponibilità gruppi, che dipende la caratteristica WSFC.

Quando un cluster di due nodi viene creato e in linea, considerare lo scenario:

1. Il cluster è disponibile online, quindi il nodo 1 richiede un indirizzo IP assegnato dinamicamente il cluster nome di rete.

2. Nessun indirizzo IP diverso da indirizzo IP del nodo 1 applicando il servizio DHCP, poiché il servizio DHCP riconosce che la richiesta proviene da nodo 1 stesso.

3. Windows rileva che viene assegnato un indirizzo duplicato per il nodo 1 e il nome di rete cluster e il gruppo di cluster predefinito non è possibile connettere.

4. Gruppo di cluster predefinito sposta nodo 2, che considera indirizzo IP del nodo 1 come indirizzo IP del cluster e porta in linea il gruppo cluster predefinito.

5. Quando il nodo 2 tenta di stabilire una connessione con il nodo 1, pacchetti indirizzati al nodo 1 mai lasciare il nodo 2 perché risolve indirizzo IP del nodo 1 della stessa. Il nodo 2 non è possibile stabilire una connessione con il nodo 1, perde base e chiude il cluster.

6. Nel frattempo, il nodo 1 possono inviare pacchetti a nodo 2, ma non è possibile rispondere nodo 2. Il nodo 1 perde base e chiude il cluster.

Assegnando un inutilizzato indirizzo IP statico, ad esempio un indirizzo IP locale come 169.254.1.1, il nome di rete cluster per visualizzare il nome della rete cluster online, è possibile evitare questo scenario. Per semplificare il processo, vedere [Configurazione di Cluster di Failover Windows in Azure per sempre nella disponibilità i gruppi](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Per ulteriori informazioni, vedere [Configurare sempre nella disponibilità gruppi in Azure grafica](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Supporto per comunicare ascoltatore gruppo disponibilità

Listener gruppo di disponibilità sono supportati in macchine virtuali di Azure che eseguono Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Il supporto è possibile per l'uso di bilanciamento del carico endpoint attivata macchine virtuali di Azure i nodi di gruppo di disponibilità. È necessario seguire speciali passaggi di configurazione per i listener per entrambe le applicazioni client in esecuzione in Azure, nonché quelli che eseguono locale.

Sono disponibili due opzioni principali per la configurazione del comunicare ascoltatore: esterno (pubblico) o interno. Comunicare ascoltatore (pubblico) esterni utilizza internet affiancate di bilanciamento del carico ed è associato a un pubblico virtuale indirizzi IP che sono accessibili via internet. Un comunicare ascoltatore interno utilizza un servizio di bilanciamento del carico interno e supportano solo client all'interno della stessa rete virtuale. Per specificare se caricare il tipo di bilanciamento del carico, è necessario attivare l'accesso diretto Server restituito. 

Se il gruppo di disponibilità si estende su più subnet Azure (ad esempio una distribuzione che attraversa aree Azure), la stringa di connessione client deve includere "**MultisubnetFailover = True**". Il risultato tentativi di connessione in parallelo alle repliche in subnet diverse. Per istruzioni sulla configurazione di comunicare un ascoltatore, vedere

- [Configurare un comunicare ascoltatore ILB sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurare un comunicare esterni ascoltatore sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

È ancora possibile connettersi a ogni replica disponibilità separatamente connettendosi direttamente all'istanza del servizio. Inoltre, poiché sempre in gruppi disponibilità sono compatibili con database che rispecchiano client, è possibile connettersi alle repliche disponibilità come database che rispecchiano partner come repliche sono configurate in modo simile a database che rispecchiano:

- Una replica principale e una secondaria

- La replica secondaria è configurata come non leggibile (**Leggibile secondario** opzione impostata su **No**)

Di seguito viene una stringa di connessione client di esempio che corrisponde alla configurazione mi piace che rispecchiano database utilizzando ADO.NET o SQL Server Native Client:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Per ulteriori informazioni sulla connettività dei client, vedere:

- [Uso di parole chiave di stringa di connessione con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Connettere i client a un Database che rispecchiano sessione (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connessione a comunicare ascoltatore gruppo di disponibilità ibride IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Gruppo di disponibilità listener, connettività dei Client e applicazione Failover (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Con stringhe di connessione che rispecchiano Database con i gruppi di disponibilità](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latenza di rete ibride IT

Distribuire la soluzione HADR con sul presupposto che possono essere periodi di tempo con latenza elevata rete tra la rete locale e Azure. Quando si distribuisce repliche in Azure, è necessario utilizzare commit asincrono anziché icona del commit per la modalità di sincronizzazione. Quando la distribuzione di database che rispecchiano server locale sia in Azure, utilizzare la modalità di prestazioni anziché la modalità di protezione alta.

### <a name="geo-replication-support"></a>Supporto della replica geografico

La replica geografico in Azure dischi non supporta i file di dati e file di log dello stesso database per l'archiviazione in dischi separati. GRS replica le modifiche in ogni disco in modo indipendente e asincrono. Questo meccanismo garantisce l'ordine di scrittura all'interno di un singolo disco sulla copia replicato geografico, ma non attraverso replicato geografico copie di più dischi. Se si configura un database per archiviare il file di dati e il file di log su dischi separati, dischi recuperati dopo un'emergenza potrebbero contenere una copia del file di dati più aggiornata rispetto al file di log, che le interruzioni di scrittura in avanti log di SQL Server e le proprietà ACID delle transazioni. Se non è l'opzione per disabilitare la replica geografico per l'account di archiviazione, è consigliabile mantenere tutti i file registro e dati per un determinato database sullo stesso disco. Se è necessario utilizzare più di un disco a causa delle dimensioni del database, è necessario installare una delle soluzioni di ripristino di emergenza elencate sopra per garantire la ridondanza dei dati.

## <a name="next-steps"></a>Passaggi successivi

Se è necessario creare una macchina virtuale Azure con SQL Server, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per ottenere le massime prestazioni da SQL Server in esecuzione in una macchina virtuale Azure, vedere la Guida in [Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Altre risorse

- [Installare una nuova foresta di Active Directory in Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Creare Cluster WSFC per sempre in gruppi di disponibilità in macchine Virtuali di Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
