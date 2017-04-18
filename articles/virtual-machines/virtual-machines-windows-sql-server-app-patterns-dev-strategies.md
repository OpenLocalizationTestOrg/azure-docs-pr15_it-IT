<properties
    pageTitle="Modelli di applicazione di SQL Server in macchine virtuali | Microsoft Azure"
    description="Questo articolo descrive i modelli di applicazione per SQL Server in macchine virtuali di Azure. Fornisce progettisti di soluzioni e gli sviluppatori di base per la progettazione e architettura ottimale dell'applicazione."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modelli di applicazione e strategie di sviluppo per SQL Server in macchine virtuali di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Riepilogo:
Determinare il motivo dell'applicazione o modelli da utilizzare per le applicazioni basate su SQL Server in Azure ambiente è un'importante decisione e richiede l'esecuzione di un'interazione SQL Server e ogni componente dell'infrastruttura di Azure approfondita. Con SQL Server in servizi di infrastruttura di Windows Azure, è possibile facilmente eseguire la migrazione, gestire e monitorare le applicazioni di SQL Server esistenti integrate in Windows Server in macchine virtuali in Azure.

L'obiettivo di questo articolo è fornire progettisti di soluzioni e sviluppatori di base per la progettazione, è possibile eseguire durante la migrazione di applicazioni esistenti di Azure e di sviluppo di applicazioni di nuove in Azure e architettura ottimale dell'applicazione.

Per ogni modello di applicazione, si noterà uno scenario locale, i rispettivi soluzione basate su cloud e i consigli tecnici correlati. Inoltre, l'articolo vengono illustrate strategie di sviluppo di Azure specifiche in modo che è possibile progettare correttamente le applicazioni. A causa di numerosi modelli di applicazione possibili, è consigliabile che progettisti e gli sviluppatori devono scegliere il modello più appropriato per le applicazioni e gli utenti.

**Collaboratori tecnici:** Agostino Luis Vargas aringhe, Madhan Arumugam Ramakrishnan

**Revisori tecnici:** Corey sabbiatrici, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Raffaella Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Jin offre Xin

## <a name="introduction"></a>Introduzione

È possibile sviluppare numerosi tipi di applicazioni a più livelli, separando i componenti dei livelli applicazione diverso sui diversi computer anche come componenti separati. Ad esempio, è possibile posizionare l'applicazione client e business regole componenti in un computer, livello web front-end e componenti di livello di accesso ai dati in un altro computer e un livello di database back-end in un altro computer. Questo tipo di strutturazione consente di isolare ogni livello tra loro. Se si cambia la provenienza dei dati, non è necessario modificare l'applicazione client o web, ma solo i componenti livello di accesso ai dati.

Un'applicazione tipica *livelli* include il livello di presentazione, il livello business e il livello di dati:


| Livello              | Descrizione                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Presentazione** | Il *livello di presentazione* (livello web, livello front-end) è il livello in cui gli utenti interagiscono con un'applicazione.                                                                      |
| **Business**     | Il *livello aziendale* (livello intermedio) è il livello di livello la presentazione e il livello di dati consente di comunicare con loro e include le funzionalità di base del sistema. |
| **Dati**         | Il *livello di dati* è il server che contiene dati di un'applicazione (ad esempio un server che esegue SQL Server).                                                             |

Livelli di applicazione descrivono raggruppamenti logici delle funzionalità e componenti di un'applicazione; mentre i livelli descrivono la distribuzione fisica dei componenti e funzionalità nel server fisici separati, computer, reti o postazioni remote. I livelli di un'applicazione possono trovarsi nello stesso computer fisico (stesso livello) o possono essere distribuiti in computer separati (livelli) e i componenti di ogni livello di comunicano con componenti altri livelli tramite le interfacce ben definite. È possibile pensare il livello di termini come riferimento a schemi di distribuzione fisici, ad esempio due livelli, tre livelli e livelli. Un **modello di applicazione di livello 2** contiene due livelli di applicazione: server applicazioni e server di database. Comunicazione diretta avviene tra il server di applicazioni e il server di database. Il server di applicazione contiene componenti a livello web e livello aziendale. Nel **modello di applicazione di livello 3**, sono disponibili tre livelli di applicazione: server, server applicazioni, che contiene il livello logica aziendale e/o componenti di accesso ai dati di business livello e il server di database web. Le comunicazioni tra il server web che dal server di database accade del server dell'applicazione. Per informazioni dettagliate sui livelli di applicazione e livelli, vedere [Guida all'architettura di applicazione di Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Prima di iniziare, leggere l'articolo, è necessario disporre knowledge sui concetti fondamentali di SQL Server e Azure. Per informazioni, vedere [La documentazione Online di SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

In questo articolo vengono illustrati i diversi modelli di applicazione che possono essere adatti per le applicazioni semplici, nonché le applicazioni aziendali altamente complesse. Prima di ogni modello di dettaglio, è consigliabile che è necessario acquisire familiarità con i servizi di archiviazione dati disponibili in Azure, ad esempio [Lo spazio di archiviazione di Azure](../storage/storage-introduction.md), [Database SQL Azure](../sql-database/sql-database-technical-overview.md)e [SQL Server in una macchina virtuale Azure](virtual-machines-windows-sql-server-iaas-overview.md). Per rendere le decisioni migliori per le applicazioni, capire quando utilizzare il servizio di archiviazione di dati in modo chiaro.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Scegliere SQL Server in una macchina virtuale Azure, quando:

- È necessario controllo su SQL Server e Windows. Ad esempio, può includere la versione di SQL Server, correzioni speciale, la configurazione delle prestazioni e così via.

- È necessario un piena compatibilità con SQL Server in locale, spostare le applicazioni esistenti in Azure come-è.

- Si desidera sfruttare le funzionalità dell'ambiente di Azure ma non supporta tutte le caratteristiche che richiede l'applicazione di Database SQL Azure. Si potrebbero includere aree seguenti:

    - **Dimensioni database**: in fase di questo articolo è stato aggiornato, Database SQL supporta un database di 1 TB di dati. Se l'applicazione richiede più di 1 TB di dati e non si desidera implementare soluzioni sharding personalizzato, è consigliabile usare SQL Server in una macchina virtuale Azure. Per informazioni aggiornate, vedere [Ridimensionamento fuori database di SQL Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx) e [livelli di servizio di Database SQL Azure e livelli di prestazioni](../sql-database/sql-database-service-tiers.md).
    - **Conformità HIPAA**: sanitari clienti e fornitori di Software indipendenti (indipendenti) potrebbero scegliere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md) invece di [Database SQL Azure](../sql-database/sql-database-technical-overview.md) poiché SQL Server in una macchina virtuale Azure è coperto da HIPAA Business associare contratto (BAA). Per informazioni sulla conformità, vedere [Microsoft Azure Trust Center: conformità](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Caratteristiche di livello istanza**: in questa fase, Database SQL non supporta le caratteristiche che live all'esterno del database (ad esempio server collegati, agente dei processi, FileStream, esso, ecc.). Per ulteriori informazioni, vedere [linee guida di Database SQL Azure e limitazioni](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>livello 1 (semplice): singola macchina virtuale

In questo modello di applicazione, si distribuisce l'applicazione di SQL Server e database in un computer virtuale autonoma in Azure. La stessa macchina virtuale contiene l'applicazione client/web, componenti aziendali, livello di accesso ai dati e il server di database. La presentazione, business e codice di accesso ai dati in modo logico separati ma fisica risiede in un computer server singolo. La maggior parte dei clienti iniziano con questo modello di applicazione e quindi la scalabilità mediante l'aggiunta di più ruoli web o macchine virtuali di sistema.

Questo modello di applicazione è utile quando:

- Si desidera eseguire una migrazione semplice in piattaforma Azure per valutare se la piattaforma vengono fornite le risposte requisiti dell'applicazione o meno.

- Si desidera mantenere tutti i livelli dell'applicazione ospitati sullo stesso computer di virtuale dello stesso Azure data center per ridurre la latenza tra livelli.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

Nel diagramma seguente viene illustrata una semplice locale scenario e come è possibile distribuire la soluzione cloud abilitate in un unico computer virtuale in Azure.

![modello di applicazione di livello 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Distribuzione di livello aziendale (logica aziendale e componenti di accesso ai dati) sullo stesso livello fisico come livello di presentazione, è possibile ottimizzare le prestazioni dell'applicazione, a meno che non è necessario usare un livello separato per motivi di sicurezza o scalabilità.

Poiché si tratta per iniziare con un motivo molto comune, si può trovare il seguente articolo su migrazione utile per spostare i dati in macchine Virtuali di Server il SQL: [la migrazione di un Database di SQL Server in macchine Virtuali un Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>livello 3 (semplice): più macchine virtuali

In questo modello di applicazione, si distribuisce un'applicazione di livello 3 in Azure inserendo ogni livello di applicazione in una macchina virtuale diversa. Questo offre un ambiente flessibile per scenari di scalabilità e scalabilità con un semplice. Quando una macchina virtuale contiene l'applicazione client/web, un'altra include i componenti aziendali e l'altra ospita il server di database.

Questo modello di applicazione è utile quando:

- Si desidera eseguire la migrazione delle applicazioni di database complessi in macchine virtuali di Azure.

- Si desidera diversi livelli dell'applicazione possa essere contenuta in aree geografiche diverse. Ad esempio, è stata condivisa database distribuiti in più aree per la creazione di report.

- Si desidera spostare le applicazioni aziendali da piattaforme locale virtualizzati in macchine virtuali di Azure. Per informazioni dettagliate sulle applicazioni aziendali, vedere [che cos'è un'applicazione Enterprise](https://msdn.microsoft.com/library/aa267045.aspx).

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

Nel diagramma seguente viene illustrato come è possibile inserire una semplice applicazione di livello 3 in Azure inserendo ogni livello di applicazione in una macchina virtuale diversa.

![modello di applicazione di livello 3](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

In questo modello di applicazione contiene un solo macchine () in ogni livello. Se si dispone di più macchine virtuali di Azure, è consigliabile impostare una rete virtuale. [Azure virtuali](../virtual-network/virtual-networks-overview.md) crea un limite di protezione attendibile e consente inoltre alle macchine virtuali di comunicare tra loro attraverso l'indirizzo IP privato. Inoltre, sempre assicurarsi che tutte le connessioni Internet passare solo a livello di presentazione. Quando si segue questo modello di applicazione, gestire le regole gruppo di sicurezza di rete per controllare l'accesso. Per ulteriori informazioni, vedere [Consenti accesso esterno di una macchina virtuale tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Nel diagramma, protocolli Internet può essere TCP, UDP, HTTP o HTTPS.

>[AZURE.NOTE] Impostazione di una rete virtuale in Azure è gratuito. Tuttavia, addebitate per il gateway VPN che si connette a locale. Questo addebito dipende la quantità di tempo che connessione è stato eseguito il provisioning e disponibile.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>livello 2 e 3 con scalabilità presentazione di livello

In questo modello di applicazione, si distribuisce applicazione di database di livello 2 o 3 in macchine virtuali di Azure inserendo ogni livello di applicazione in una macchina virtuale diversa. Inoltre, scalare il livello di presentazione a causa di aumento delle richieste di client.

Questo modello di applicazione è utile quando:

- Si desidera spostare le applicazioni aziendali da piattaforme locale virtualizzati in macchine virtuali di Azure.

- Si desidera ridimensionare il livello di presentazione a causa di aumento delle richieste di client.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

- Si desidera essere proprietari di un ambiente di infrastruttura in cui è possibile ridimensionare alto e verso il basso su richiesta.

Nel diagramma seguente viene illustrato come è possibile inserire i livelli dell'applicazione di più macchine virtuali di Azure facendo scalabilità il livello di presentazione a causa di aumento delle richieste di client. Come illustrato nella figura, bilanciamento del carico Azure è responsabile per la distribuzione del traffico più macchine virtuali e anche per determinare il server web a cui connettersi. La presenza di più istanze di server web dietro un bilanciamento del carico garantisce elevata disponibilità del livello di presentazione.

![Modello di applicazione - presentazione livello scalabilità](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Procedure consigliate per motivi di livello 2, 3 livelli o livelli che dispone di più macchine virtuali in un livello

È consigliabile posizionare le macchine virtuali che appartengono allo stesso livello nel servizio cloud stesso e nella stessa disponibilità impostare. Ad esempio, inserire un insieme di server web in **CloudService1** e **AvailabilitySet1** e un set di server di database in **CloudService2** e **AvailabilitySet2**. Una disponibilità imposta in Azure consente di inserire i nodi di disponibilità in domini separati guasto ed eseguire l'aggiornamento di domini.

Per utilizzare più istanze di macchine Virtuali di un livello, è necessario configurare di bilanciamento del carico Azure tra livelli dell'applicazione. Per configurare Bilanciamento del carico in ogni livello, creare un endpoint di bilanciamento del carico in macchine virtuali di ogni livello separatamente. Per un livello specifico, è necessario creare macchine virtuali nel servizio cloud stesso. In questo modo che hanno lo stesso indirizzo IP virtuale pubblico. Creare un endpoint una macchine virtuali di tale livello. Per altre macchine virtuali di tale livello per il bilanciamento del carico, assegnare lo stesso endpoint. Tramite la creazione di un set di bilanciamento del carico, distribuire il traffico su più macchine virtuali e consentire bilanciamento del carico determinare il nodo per la connessione quando si verifica un errore di un nodo macchine Virtuali di back-end. Ad esempio, la presenza di più istanze di server web dietro un bilanciamento del carico garantisce elevata disponibilità del livello di presentazione.

Come ottimale, è sempre accertarsi che tutte le connessioni internet passare prima al livello di presentazione. Il livello di presentazione accede a livello aziendale e quindi il livello business accede a livello di dati. Per ulteriori informazioni su come consentire l'accesso a livello di presentazione, vedere [Consenti accesso esterno di una macchina virtuale tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Si noti che il bilanciamento del carico in Azure è simile a caricare bilanciamento del carico in un ambiente locale. Per ulteriori informazioni, vedere [il bilanciamento del carico per i servizi di infrastruttura Azure](virtual-machines-windows-load-balance.md).

Inoltre, è consigliabile che di configurare una rete privata per macchine virtuali mediante virtuali Azure. In questo modo possono comunicare tra loro tramite l'indirizzo IP privato. Per ulteriori informazioni, vedere [Virtuali Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>livello 2 e 3 con scalabilità business di livello

In questo modello di applicazione, si distribuisce applicazione di database di livello 2 o 3 in macchine virtuali di Azure inserendo ogni livello di applicazione in una macchina virtuale diversa. Inoltre, è consigliabile distribuire i componenti del server di applicazioni a più macchine virtuali a causa di complessità dell'applicazione.

Questo modello di applicazione è utile quando:

- Si desidera spostare le applicazioni aziendali da piattaforme locale virtualizzati in macchine virtuali di Azure.

- Si desidera distribuire i componenti del server di applicazioni a più macchine virtuali a causa di complessità dell'applicazione.

- Si desidera spostare Incolla logica business locale le applicazioni line (line-of-business) in macchine virtuali di Azure. Le applicazioni line sono un insieme di applicazioni per computer critici che sono importanti per l'esecuzione di un'organizzazione, ad esempio contabilità, risorse umane (h), retribuzioni, gestione della catena di fornitura e le applicazioni di pianificazione delle risorse.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

- Si desidera essere proprietari di un ambiente di infrastruttura in cui è possibile ridimensionare alto e verso il basso su richiesta.

Nel diagramma seguente viene illustrato uno scenario locale e la relativa soluzione cloud abilitato. In questo scenario, inserire i livelli dell'applicazione in più macchine virtuali di Azure da scalabilità a livello aziendale, che contiene il livello di logica business e componenti di accesso ai dati. Come illustrato nella figura, bilanciamento del carico Azure è responsabile per la distribuzione del traffico più macchine virtuali e anche per determinare il server web a cui connettersi. La presenza di più istanze di server applicazioni bilanciamento garantisce la disponibilità di livello aziendale. Per ulteriori informazioni, vedere [procedure consigliate per livello 2, 3 livelli o motivi di applicazione a più livelli che dispongono di più macchine virtuali in un livello](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Modello di applicazione con scala a livello aziendale indietro](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>livello 2 e 3 con presentazione e business livelli scalabilità e HADR

In questo modello di applicazione, si distribuisce applicazione di database di livello 2 o 3 in macchine virtuali di Azure distribuendo il livello di presentazione (server web) e i componenti (server di applicazioni) a livello aziendale a più macchine virtuali. È inoltre implementare disponibilità ed emergenza soluzioni di ripristino per i database in macchine virtuali di Azure.

Questo modello di applicazione è utile quando:

- Si desidera spostare le applicazioni aziendali piattaforme virtualizzato locali in Azure mediante l'implementazione di disponibilità di SQL Server e ripristino di emergenza.

- Si desidera ridimensionare il livello di presentazione e il livello di business a causa di aumento delle richieste in arrivo e alla complessità dell'applicazione.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

- Si desidera essere proprietari di un ambiente di infrastruttura in cui è possibile ridimensionare alto e verso il basso su richiesta.

Nel diagramma seguente viene illustrato uno scenario locale e la relativa soluzione cloud abilitato. In questo scenario di scalare il livello di presentazione e i componenti a livello aziendale in più macchine virtuali in Azure. È inoltre implementare tecniche elevate di ripristino (HADR) di disponibilità e per i database di SQL Server in Azure.

Esecuzione di più copie di un'applicazione in diversi macchine virtuali assicurarsi che siano bilanciamento del carico richieste tra loro. Quando si dispone di più macchine virtuali, è necessario assicurarsi che tutte le macchine virtuali siano accessibili e in esecuzione in un determinato momento. Se si configura il bilanciamento del carico, servizio di bilanciamento del carico Azure consente di tenere traccia dello stato delle macchine virtuali e indirizza le chiamate in arrivo per i nodi macchine Virtuali funzionanti integri correttamente. Per informazioni su come configurare il bilanciamento del carico macchine virtuali, vedere [il bilanciamento del carico per i servizi di infrastruttura Azure](virtual-machines-windows-load-balance.md). La presenza di più istanze di un server web e applicazione dietro un bilanciamento del carico garantisce disponibilità dei livelli di presentazione e business.

![Scalabilità ed elevata disponibilità](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Procedure consigliate per i modelli di applicazione che richiedono SQL HADR

Quando si imposta la disponibilità di SQL Server e le soluzioni di ripristino di emergenza in macchine virtuali di Azure, configurando una rete virtuale per macchine virtuali con [Azure virtuali](../virtual-network/virtual-networks-overview.md) è obbligatorio.  Macchine virtuali all'interno di una rete virtuale avrà un indirizzo IP privato stabile anche dopo un tempi di inattività, provocano l'ora di aggiornamento necessario per la risoluzione dei nomi DNS. Inoltre, la rete virtuale consente di estendere la rete locale in Azure e crea un limite di protezione attendibile. Ad esempio, se l'applicazione è associate le restrizioni dominio aziendale (ad esempio autenticazione di Windows, Active Directory), la configurazione [Azure virtuali](../virtual-network/virtual-networks-overview.md) è necessaria.

La maggior parte dei clienti che eseguono codice di produzione su Azure, sono abbreviando primari e secondari riproduzioni in Azure.

Per informazioni complete ed esercitazioni su disponibilità e tecniche di ripristino di emergenza, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>livello 2 e 3-livello tramite macchine virtuali di Azure e servizi Cloud

In questo modello di applicazione, si distribuisce un'applicazione di livello 2 o 3 di Azure con entrambi i [Servizi Cloud Windows Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (ruoli web e lavoro - piattaforma come servizio (PaaS)) e [macchine virtuali di Azure](virtual-machines-windows-about.md) (infrastruttura come servizio (IaaS)). Uso di [Servizi Cloud Windows Azure](https://azure.microsoft.com/documentation/services/cloud-services/) per il livello di presentazione/business di livello e SQL Server in [macchine virtuali di Azure](virtual-machines-windows-about.md) per il livello di dati è utile per la maggior parte delle applicazioni in esecuzione in Azure. Il motivo è che con un'istanza di calcolo in esecuzione su Cloud Services fornisce un facile gestione, distribuzione, monitoraggio e scalabilità.

Con i servizi Cloud, Azure mantiene l'infrastruttura di necessità, esegue la manutenzione ordinaria, patch i sistemi operativi e un tentativo di recuperare dal servizio e gli errori hardware. Quando l'applicazione necessita scalabilità, opzioni di scalabilità automatiche e manuale sono disponibili per il progetto di servizio cloud per aumentare o ridurre il numero di istanze o macchine virtuali utilizzati dall'applicazione in uso. Inoltre, è possibile utilizzare locale Visual Studio per distribuire l'applicazione di un progetto di servizio cloud in Azure.

In conclusione, se non si vuole proprietari grazie a più livelli le attività amministrative per la presentazione/business e l'applicazione non richiede alcuna configurazione avanzata del software o il sistema operativo, utilizzare i servizi Cloud Windows Azure. Se il Database di SQL Azure non supporta tutte le caratteristiche di cui che si sta cercando, usare SQL Server in una macchina virtuale Azure per il livello di dati. Esecuzione di un'applicazione in servizi Cloud Windows Azure e la memorizzazione dei dati in macchine virtuali di Azure combina i vantaggi di entrambi i servizi. Per un confronto dettagliato, vedere la sezione in questo argomento sulle [strategie di sviluppo di confronto in Azure](#comparing-web-development-strategies-in-azure).

In questo modello di applicazione il livello di presentazione include un ruolo web che esegue un componente di servizi Cloud nell'ambiente di esecuzione Azure ed è personalizzata per la programmazione di applicazioni web come supportati da IIS e ASP.NET. Il livello di business o back-end include un ruolo di lavoro, che è un componente di servizi Cloud in esecuzione nell'ambiente di esecuzione Azure e può essere utile per lo sviluppo GRG e può eseguire l'elaborazione di sfondo per un ruolo web. Il livello di database si trova in una macchina virtuale di SQL Server Azure. Le comunicazioni tra i livelli di presentazione e il database si verifica direttamente o sopra il livello di business-componenti del ruolo di lavoro.

Questo modello di applicazione è utile quando:

- Si desidera spostare le applicazioni aziendali piattaforme virtualizzato locali in Azure mediante l'implementazione di disponibilità di SQL Server e ripristino di emergenza.

- Si desidera essere proprietari di un ambiente di infrastruttura in cui è possibile ridimensionare alto e verso il basso su richiesta.

- Database SQL Azure non supporta tutte le funzionalità necessarie per l'applicazione o il database.

- Si desidera eseguire il test per diversi livelli di carico di lavoro sottolineare ma allo stesso tempo si desidera proprietari e la manutenzione molti computer fisici sempre questa impostazione.

Nel diagramma seguente viene illustrato uno scenario locale e la relativa soluzione cloud abilitato. In questo scenario, inserire il livello di presentazione in ruoli web, il livello di business in ruoli di lavoro, ma il livello di dati in macchine virtuali in Azure. Esecuzione di più copie del livello di presentazione in ruoli diversi web garantisce per carico tra di loro. Quando si combinano servizi Cloud Windows Azure con macchine virtuali di Azure, è consigliabile impostare la [Rete virtuale Azure](../virtual-network/virtual-networks-overview.md) anche. Con [Azure virtuali](../virtual-network/virtual-networks-overview.md), è possibile impostare stabili e persistenti indirizzi IP privati all'interno del servizio cloud stesso nel cloud. Quando si definisce una rete virtuale per le macchine virtuali e servizi cloud, questi possono avviare le comunicazioni tra loro sull'indirizzo IP privato. Inoltre, verificati macchine virtuali di ruoli e di Azure web/lavoro della stessa [Rete virtuale Azure](../virtual-network/virtual-networks-overview.md) fornisce bassa latenza e connettività più sicura. Per ulteriori informazioni, vedere [che cos'è un servizio cloud](../cloud-services/cloud-services-choose-me.md).

Come illustrato nella figura, bilanciamento del carico Azure distribuisce il traffico tra più macchine virtuali e determina quale server web o server applicazione a cui connettersi. La presenza di più istanze di server web e applicazione dietro un bilanciamento del carico garantisce disponibilità del livello di presentazione e del livello aziendale. Per ulteriori informazioni, vedere [procedure consigliate per i modelli di applicazione che richiedono HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Modelli di applicazione ai servizi Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Un altro approccio per implementare questo modello di applicazione consiste nell'usare un ruolo consolidati web che contiene il livello di presentazione e componenti a livello aziendale come illustrato nella figura seguente. Questo modello di applicazione è utile per applicazioni che richiedono informazioni sullo stato progettazione. Poiché Azure fornisce nodi di calcolo senza informazioni sullo stato sui ruoli web e di lavoro, si consiglia di implementare una logica per la memorizzazione di sessione utilizzando uno dei seguenti tecnologie: [La memorizzazione nella cache di Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Archivio tabelle Azure](../storage/storage-dotnet-how-to-use-tables.md) o [Database SQL Azure](../sql-database/sql-database-technical-overview.md).

![Modelli di applicazione ai servizi Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Modello con macchine virtuali di Azure, Database SQL Azure e Azure servizio App (applicazioni Web)

Lo scopo principale di questo modello di applicazione è sufficiente visualizzare come combinare infrastruttura di Azure come componenti di servizio (IaaS) con Azure componenti di piattaforma come servizio (PaaS) della soluzione. Questo modello si impegna a Database SQL Azure per lo spazio di archiviazione di dati relazionali. Non include SQL Server in una macchina virtuale Azure, che fa parte dell'infrastruttura di Azure offerte di servizio.

In questo modello di applicazione, si distribuisce un'applicazione di database in Azure inserendo i livelli di presentazione e business nella stessa macchina virtuale e accesso a un database nel server di Database SQL Azure (Database di SQL). È possibile implementare il livello di presentazione tramite soluzioni web basate su IIS tradizionale. In alternativa, è possibile implementare una presentazione combinata e livello aziendale con [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Questo modello di applicazione è utile quando:

- Si dispone già di un server di Database SQL esistente configurato in Azure e si desidera verificare rapidamente l'applicazione.

- Si desidera verificare le funzionalità dell'ambiente Azure.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- I componenti di access logica e i dati aziendali possono essere autonomo all'interno di un'applicazione web.

Nel diagramma seguente viene illustrato uno scenario locale e la relativa soluzione cloud abilitato. In questo scenario, inserire i livelli dell'applicazione in una singola macchina virtuale in Azure e ai dati nel Database di SQL Azure.

![Modello di applicazione misti](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se si sceglie di implementare un web combinati e livello applicazione tramite Azure Web Apps, è consigliabile mantenere il livello intermedio o dell'applicazione come librerie di collegamento dinamico (DLL) nel contesto di un'applicazione web.

Inoltre, esaminare i consigli forniti nella sezione [confronto web strategie di sviluppo Azure](#comparing-web-development-strategies-in-azure) alla fine di questo articolo per ulteriori informazioni tecniche di programmazione.

## <a name="n-tier-hybrid-application-pattern"></a>Modello di applicazione livelli ibrido

Nel modello di applicazione ibrido a più livelli, è implementare l'applicazione in più livelli distribuito tra locale e Azure. Di conseguenza, si crea un sistema ibrido flessibile e riutilizzabile, che è possibile modificare o aggiungere un livello specifico senza modificare altri livelli. Per estendere la rete aziendale nel cloud, viene utilizzato il servizio di [Azure virtuali](../virtual-network/virtual-networks-overview.md) .

Questo modello di applicazione ibrida è utile quando:

- Si desidera creare applicazioni che vengono eseguite in parte nel cloud e in parte locale.

- Si desidera eseguire la migrazione di alcuni o tutti gli elementi di un'applicazione locale esistente nel cloud.

- Si desidera spostare le applicazioni aziendali da piattaforme locale virtualizzati in Azure.

- Si desidera essere proprietari di un ambiente di infrastruttura in cui è possibile ridimensionare alto e verso il basso su richiesta.

- Si vuole effettuare il provisioning di sviluppo e ambienti di prova per brevi periodi di tempo rapidamente.

- Si desidera conveniente per eseguire backup per le applicazioni di database dell'organizzazione.

Nel diagramma seguente viene illustrato un modello di applicazione ibrido livelli che si estende in locale e Azure. Come mostrato nella figura, infrastruttura locale include controller di dominio di [Servizi di dominio Active Directory](https://technet.microsoft.com/library/hh831484.aspx) per supportare l'autenticazione degli utenti e autorizzazioni. Si noti che il diagramma illustrato uno scenario, in alcune parti del livello dati dinamica in un data center locale che alcune parti del livello dati dinamica in Azure. In base alle esigenze dell'applicazione, è possibile implementare diversi altri scenari di distribuzione ibrida. Ad esempio, è possibile mantenere il livello di presentazione e il livello di business in un ambiente locale, ma il livello di dati di Azure.

![Modello di applicazione a più livelli](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

In Azure, è possibile utilizzare Active Directory come directory cloud autonoma per l'organizzazione oppure è inoltre possibile integrare Active Directory esistente locali con [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Come illustrato nella figura, i componenti a livello aziendale possono accedere a più origini dati, ad esempio a [SQL Server in Azure](virtual-machines-windows-sql-server-iaas-overview.md) tramite un indirizzo IP interno privato, in locale SQL Server tramite [Azure virtuali](../virtual-network/virtual-networks-overview.md)o a un [Database SQL](../sql-database/sql-database-technical-overview.md) utilizzando le tecnologie di provider di dati .NET Framework. In questo diagramma Database SQL Azure è un servizio di archiviazione dati facoltativi.

Nel modello di applicazione ibrido a più livelli, è possibile implementare il flusso di lavoro seguente nell'ordine specificato:

1. Identificare le applicazioni di database dell'organizzazione che devono essere spostato in alto nel cloud tramite la [valutazione di Microsoft e Planning (MAP) Toolkit](http://microsoft.com/map). Toolkit MAP raccoglie dati di inventario e prestazioni da computer che si intende per la virtualizzazione e vengono fornite indicazioni sulla capacità e pianificazione della valutazione.

1. Pianificare le risorse e configurazione necessaria la piattaforma Azure, ad esempio gli account di archiviazione e macchine virtuali.

1. Configurare la connettività di rete tra la rete aziendale in locale e [Virtuali Azure](../virtual-network/virtual-networks-overview.md). Per configurare la connessione tra la rete aziendale in locale e una macchina virtuale in Azure, usare uno dei due metodi seguenti:

    1. Stabilire una connessione tra locale e Azure tramite i punti finali pubblici in una macchina virtuale in Azure. Questo metodo fornisce un'installazione semplice e consente di utilizzare l'autenticazione di SQL Server in computer virtuale. Inoltre, configurare le regole di gruppo di sicurezza di rete per controllare il traffico pubblico per la macchina virtuale. Per ulteriori informazioni, vedere [Consenti accesso esterno di una macchina virtuale tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Stabilire una connessione tra locale e Azure tramite tunnel di Azure Virtual Private network (VPN). Questo metodo consente di estendere Criteri di dominio per una macchina virtuale in Azure. Inoltre, è possibile impostare le regole firewall e utilizzare l'autenticazione di Windows nel computer virtuale. Azure supporta al momento, protetto da sito VPN e connessioni VPN punto da sito:

        - Con connessione sicura al sito, è possibile stabilire la connettività di rete tra la rete locale e la rete virtuale in Azure. È consigliabile per la connessione l'ambiente del centro di dati in locale in Azure.

        - Con connessione punto a sito sicura, è possibile stabilire la connettività di rete tra la rete virtuale in Azure e i singoli computer che eseguono in un punto qualsiasi. È consigliabile prevalentemente scopi di sviluppo e test.

    Per informazioni su come connettersi a SQL Server in Azure, vedere [connettersi a un SQL Server virtuale computer Azure](virtual-machines-windows-classic-sql-connect.md).

1. Impostare avvisi che il backup dei dati in locale in un disco macchina virtuale in Azure e programmate. Per ulteriori informazioni, vedere [SQL Server Backup e ripristino con il servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) ed [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

1. In base alle esigenze dell'applicazione, è possibile implementare uno dei tre scenari seguenti:

    1. È possibile mantenere il server web, server applicazioni e indipendenti dalle dati in un server di database in Azure mentre è mantenere i dati sensibili locale.

    1. È possibile mantenere il server web e applicazione server in locale mentre il server di database in un computer virtuale in Azure.

    1. È possibile mantenere i server di database, un server web e applicazione server in locale mentre è mantenere le repliche di database in macchine virtuali in Azure. Questa impostazione consente il server web locale o applicazioni di reporting per accedere alle repliche di database in Azure. Di conseguenza, è possibile ottenere per ridurre il carico di lavoro in un database locale. È consigliabile implementare questo scenario per auto leggere carichi di lavoro e scopi sviluppo. Per informazioni sulla creazione di repliche di database in Azure, vedere disponibilità AlwaysOn [disponibilità](virtual-machines-windows-sql-high-availability-dr.md)e ripristino per SQL Server in macchine virtuali di Azure.

## <a name="comparing-web-development-strategies-in-azure"></a>Confronto tra strategie di sviluppo web di Azure

Per implementare e distribuire un'applicazione basata su SQL Server in Azure a più livelli, è possibile usare uno dei due metodi di programmazione seguenti:

- Configurare un server web tradizionale (IIS - Internet Information Services) in Azure e access i database di SQL Server in macchine virtuali di Azure.

- Per implementare e distribuire un servizio cloud in Azure. Accertarsi che questo servizio cloud possa accedere a database di SQL Server in macchine virtuali di Azure. Un servizio cloud può includere più ruoli web e di lavoro.

La tabella seguente contiene un confronto di sviluppo web tradizionale con servizi Cloud Windows Azure e Azure Web Apps per SQL Server in macchine virtuali di Azure. La tabella include Azure Web Apps in quanto è possibile utilizzare SQL Server in macchine Virtuali di Azure come origine dati per le applicazioni Web Azure tramite il relativo indirizzo IP virtuale pubblico o il nome DNS.

||Sviluppo di applicazioni web tradizionale in macchine virtuali di Azure|Servizi cloud di Azure|Web Hosting con Azure Web Apps|
|---|---|---|---|
|**Migrazione delle applicazioni locali**|Le applicazioni esistenti come-è.|Le applicazioni devono ruoli web e lavoro.|Le applicazioni esistenti come-ma adatto per applicazioni web indipendente e servizi web che richiedono scalabilità rapida.|
|**Sviluppo e la distribuzione**|Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gestione IIS, PowerShell.|Visual Studio SDK Azure, TFS, PowerShell. Per ogni servizio cloud sia entrambi gli ambienti a cui è possibile distribuire il pacchetto di servizio e configurazione: produzione e gestione temporanea. È possibile distribuire un servizio cloud all'ambiente di gestione temporanea per eseguire il test prima si promuove di produzione.|Visual Studio, WebMatrix, Visual Web Developer, FTP, fra, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web distribuire, PowerShell.|
|**Amministrazione e configurazione**|Sei responsabile per le attività amministrative su applicazione, dati, le regole del firewall, virtuali e sistema operativo.|Sei responsabile per le attività amministrative su applicazione, dati, le regole firewall e virtuali.|Sei responsabile per le attività amministrative dell'applicazione e solo i dati.|
|**Disponibilità e il ripristino di emergenza (HADR)**|È consigliabile posizionare macchine virtuali nello stesso set di disponibilità e lo stesso servizio cloud. Mantenere le macchine virtuali nello stesso set di disponibilità consente Azure per inserire i nodi di disponibilità in domini separati guasto ed eseguire l'aggiornamento di domini. Analogamente, mantenere nelle macchine virtuali nel servizio cloud stesso consente il bilanciamento del carico e macchine virtuali possono comunicare direttamente tra loro tramite la rete locale all'interno di un centro di dati di Azure.<br/><br/>Sei responsabile per l'implementazione di una disponibilità e soluzione di ripristino completa per SQL Server in macchine virtuali di Azure per evitare di inattività. Per tecnologie HADR supportate, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Sei responsabile per il backup dei dati e applicazione.<br/><br/>Azure possibile spostare le macchine virtuali se il computer host nell'interfaccia di dati non riesce a causa di problemi hardware. Inoltre, potrebbero essersi di inattività della macchina virtuale quando il computer host viene aggiornato per software o protezione aggiornamenti. Di conseguenza, è consigliabile mantenere almeno due macchine virtuali in ogni livello di applicazione per garantire la disponibilità continua. Azure non fornisce contratto di servizio per una singola macchina virtuale. Per ulteriori informazioni, vedere [indicazioni tecniche sulla resilienza Azure](../resiliency/resiliency-technical-guidance.md).|Azure consente di gestire gli errori risultanti dal software di hardware o del sistema operativo sottostante. È consigliabile implementare più istanze di un ruolo web o lavoro per verificare la disponibilità dell'applicazione. Per informazioni, vedere [servizi Cloud, macchine virtuali, virtuale contratto del livello di servizio di rete](http://www.microsoft.com/download/details.aspx?id=38427) e [il ripristino di emergenza e disponibilità per applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Sei responsabile per il backup dei dati e applicazione.<br/><br/>Per i database che si trovano in un database di SQL Server in macchine Virtuali un Azure, è responsabile per l'implementazione di una soluzione di ripristino di alta disponibilità ed evitare qualsiasi tempo di inattività. Per tecnologie HDAR supportate, vedere disponibilità e ripristino per SQL Server in macchine virtuali di Azure.<br/><br/>**Che rispecchiano Database di SQL Server**: uso con i servizi Cloud Azure (ruoli web/lavoro). Parte della stessa rete virtuale Azure può essere macchine virtuali di SQL Server e un progetto di servizio cloud. Se macchine Virtuali di SQL Server non è presente nella stessa rete virtuale, è necessario creare un Alias di SQL Server per il routing di comunicazione per l'istanza di SQL Server. Inoltre, il nome alias deve corrispondere al nome di SQL Server.|Disponibilità ereditata ruoli di lavoro Azure, archiviazione blob Azure e Database di SQL Azure. Ad esempio, lo spazio di archiviazione di Azure gestisce tre repliche di tutti i blob, tabella e i dati di coda. In qualsiasi momento, Database SQL Azure mantiene tre repliche dei dati in esecuzione, ovvero una replica principale e due replica secondario. Per ulteriori informazioni, vedere [Lo spazio di archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) e [Database di SQL Azure](../sql-database/sql-database-technical-overview.md).<br/><br/>Quando si utilizza SQL Server in macchine Virtuali di Azure come origine dati per le applicazioni Web di Azure, tenere presente che Azure Web App non supporta virtuali Azure. In altre parole, tutte le connessioni da Azure Web Apps in macchine virtuali di SQL Server in Azure devono superare i punti finali pubblici macchine virtuali. Ciò potrebbe causare alcune limitazioni per la disponibilità e ripristino di emergenza. Ad esempio, l'applicazione client in connessione a macchine Virtuali di SQL Server di database che rispecchiano Azure Web Apps non sarebbe possibile connettersi al nuovo server primario come database che rispecchiano è necessario configurare Azure virtuali tra macchine virtuali di host di SQL Server in Azure. Di conseguenza, con **Che rispecchiano Database di SQL Server** Azure Web App non è attualmente.<br/><br/>**Disponibilità di SQL Server AlwaysOn**: È possibile impostare disponibilità AlwaysOn quando si usa Azure Web Apps con macchine virtuali di SQL Server in Azure. Ma è necessario configurare comunicare ascoltatore gruppo di disponibilità AlwaysOn per instradare la comunicazione a replica primaria tramite i punti finali bilanciamento del carico pubblici.|
|**Connettività tra locale**|È possibile utilizzare virtuali Azure a cui connettersi locale.|È possibile utilizzare virtuali Azure a cui connettersi locale.|Azure virtuali è supportato. Per ulteriori informazioni, vedere [Integrazione di rete virtuale Web App](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Scalabilità**|Scalabilità è disponibile aumentando le dimensioni di macchina virtuale o aggiunta di più dischi. Per ulteriori informazioni sulle dimensioni dei macchina virtuale, vedere [Le dimensioni di macchina virtuale per Azure](virtual-machines-windows-sizes.md).<br/><br/>**Per Server di Database**: scalabilità è disponibile tramite le tecniche partizione di database e gruppi di disponibilità AlwaysOn di SQL Server.<br/><br/>Per carichi di lettura, è possibile utilizzare [Disponibilità AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) su più nodi secondari, nonché replica di SQL Server.<br/><br/>Per carichi di lavoro piena di scrittura, è possibile implementare dati partizioni orizzontali più server fisici per fornire scalabilità dell'applicazione.<br/><br/>Inoltre, è possibile implementare un scalabilità con [SQL Server con il Routing dipendente dai dati](https://technet.microsoft.com/library/cc966448.aspx). Con dati dipendenti Routing DDR (), è necessario implementare partizione meccanismo nelle applicazioni client, in genere nel livello business per indirizzare le richieste di database in più nodi di SQL Server. Il livello business contiene i mapping da come sono suddiviso i dati e il nodo che contiene i dati.<br/><br/>È possibile ridimensionare le applicazioni che eseguono macchine virtuali. Per ulteriori informazioni, vedere [come scalare un'applicazione](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Nota importante**: la funzionalità **AutoScale** Azure consente di automaticamente aumentare o diminuire le macchine virtuali utilizzati dall'applicazione in uso. Questa caratteristica garantisce che l'utente finale non viene influenzata negativamente durante i periodi e macchine virtuali si arresta quando la domanda è bassa. È consigliabile che non si imposta l'opzione AutoScale per il servizio cloud se includa macchine virtuali di SQL Server. Il motivo è che la caratteristica AutoScale consente di Azure per attivare una macchina virtuale durante l'utilizzo della CPU in quella macchina virtuale è maggiore di alcuni soglia e disattivare una macchina virtuale durante l'utilizzo della CPU passa inferiore a essa. La caratteristica AutoScale è utile per scalabilità delle applicazioni, ad esempio server web, in cui qualsiasi macchine Virtuali possono gestire il carico di lavoro senza alcun riferimento a uno stato precedente. Tuttavia, la caratteristica AutoScale non è utile per applicazioni, ad esempio SQL Server in cui solo un'istanza consente la scrittura nel database.|Scalabilità è disponibile con più ruoli web e di lavoro. Per ulteriori informazioni sulle dimensioni macchina virtuale per ruoli web ruoli e di lavoro, vedere [Configurare le dimensioni per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Quando si utilizza **Servizi Cloud**, è possibile definire più ruoli per distribuire l'elaborazione e ottenere il ridimensionamento flessibile dell'applicazione. Ogni servizio cloud include uno o più ruoli web e/o ruoli di lavoro, ognuno con il proprio file dell'applicazione e configurazione. È possibile scalabilità un servizio cloud aumentando il numero di istanze del ruolo (macchine virtuali) distribuita per un ruolo e verso il basso scala tra un servizio cloud, riducendo il numero di istanze del ruolo. Per informazioni dettagliate, vedere [Modelli di esecuzione di Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Scalabilità è disponibile tramite il supporto di disponibilità Azure predefiniti tramite [servizi Cloud, macchine virtuali, virtuale contratto del livello di servizio di rete](http://www.microsoft.com/download/details.aspx?id=38427) e di bilanciamento del carico.<br/><br/>Per un'applicazione di più livelli, è consigliabile connettersi applicazione di ruoli web/lavoro al database server macchine virtuali tramite virtuali Azure. Inoltre, Azure offre bilanciamento del carico per macchine virtuali nel servizio cloud stesso diffusione richieste degli utenti tra di loro. Macchine virtuali collegate in questo modo possono comunicare direttamente tra loro tramite la rete locale all'interno di un centro di dati di Azure.<br/><br/>È possibile impostare **AutoScale** nel portale di classica Azure, nonché gli orari di pianificazione. Per ulteriori informazioni, vedere [come scalare un'applicazione](../cloud-services/cloud-services-how-to-scale.md).|**Ridimensionare alto e verso il basso**: È possibile aumentare/diminuire le dimensioni dell'istanza (macchine Virtuali) riservato per il sito web.<br/><br/>Scalabilità: È possibile aggiungere più riservate istanze (macchine virtuali) per il sito web.<br/><br/>È possibile impostare **AutoScale** nel portale di, nonché gli orari di pianificazione. Per ulteriori informazioni, vedere [come scala Web Apps](../app-service-web/web-sites-scale.md).|

Per ulteriori informazioni sulla scelta tra questi metodi di programmazione, vedere [Azure Web Apps, servizi Cloud e macchine virtuali: quando utilizzare che](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere [Panoramica di macchine virtuali di Azure di SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).
