<properties
   pageTitle="Indicazioni tecniche: ripristino locali in Azure | Microsoft Azure"
   description="Articolo understanding e ripristino Progettazione sistemi dall'infrastruttura locale in Azure"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Indicazioni tecniche sulla resilienza Azure: ripristino locali in Azure

Azure offre una serie completa di servizi per attivare l'estensione di un data center locale in Azure ai fini della alta disponibilità e ripristino:

* __Rete__: con una rete privata virtuale, si estende in modo sicuro la rete locale nel cloud.
* __Calcolare__: clienti che utilizzano Hyper-V in locale possono "sollevare e MAIUSC" esistente macchine () in Azure.
* __Spazio di archiviazione__: StorSimple estende il file system per lo spazio di archiviazione di Azure. Servizio di Azure Backup fornisce backup per database SQL e i file allo spazio di archiviazione di Azure.
* __Replica di database__: con SQL Server 2014 (o versioni successive) disponibilità gruppi, è possibile implementare alto disponibilità e ripristino di emergenza per i dati in locale.

##<a name="networking"></a>Rete

È possibile utilizzare virtuali Azure per creare una sezione in modo logico isolata in Azure e sicuro connetterlo al data center locale o un computer client singolo utilizzando una connessione IPsec. Con virtuali, è possibile sfruttare l'infrastruttura di scalable, su richiesta in Azure fornendo connettività a dati e le applicazioni in locale, inclusi sistemi che eseguono Windows Server, mainframe e UNIX. Per ulteriori informazioni, vedere [la documentazione di rete di Azure](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Calcolare

Se si usa Hyper-V in locale, è possibile "sollevare e MAIUSC" macchine virtuali di Azure esistenti e ai formati che eseguono Windows Server 2012 (o versioni successive) senza apportare modifiche alla macchina virtuale o la conversione di macchine Virtuali di provider di servizi. Per ulteriori informazioni, vedere [informazioni sulla dischi e dischi rigidi virtuali per macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Ripristino del sito Azure

Se si vuole emergenza come servizio (DRaaS), Azure offre [Il ripristino del sito di Azure](https://azure.microsoft.com/services/site-recovery/). Il ripristino del sito Azure offre una protezione completa per i server di Hyper-V e fisico VMware. Il ripristino del sito di Azure, è possibile usare un altro server locale o Azure del sito di ripristino. Per ulteriori informazioni sul ripristino di siti di Azure, vedere la [documentazione per il ripristino del sito di Azure](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Spazio di archiviazione

Sono disponibili diverse opzioni per l'utilizzo di Azure come un sito di backup per dati locali.

###<a name="storsimple"></a>StorSimple

StorSimple in modo sicuro e trasparente si integra lo spazio di archiviazione cloud per le applicazioni locale. Inoltre, offre un unico accessorio che offre prestazioni elevate a più livelli locale e lo spazio di archiviazione cloud e archiviazione dinamica, la protezione dei dati basate su cloud e il ripristino di emergenza. Per ulteriori informazioni, vedere la [pagina relativa ai prodotti StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Copia di Backup Azure

Backup Azure consente l'esecuzione di backup cloud utilizzando strumenti noti di backup in Windows Server 2012 o versioni successive, Windows Server 2012 Essentials (o versioni successive) e System Center 2012 Data Protection Manager (o versioni successive). Questi strumenti forniscono un flusso di lavoro per la gestione del backup che sia indipendente dalla posizione di archiviazione di backup, se un disco locale o lo spazio di archiviazione di Azure. Dopo il backup dei dati nel cloud, gli utenti autorizzati consente di recuperare facilmente backup in qualsiasi server.

Con incrementali, solo le modifiche apportate ai file vengono trasferite nel cloud. In questo modo utilizzare lo spazio di archiviazione, ridurre il consumo della larghezza di banda e il supporto del ripristino in un momento di più versioni dei dati in modo efficiente. È anche possibile scegliere di utilizzare caratteristiche aggiuntive, ad esempio i criteri di conservazione dei dati, la compressione dei dati e la limitazione per il trasferimento di dati. Utilizzo di Azure come il percorso di backup ha evidente che i backup vengono automaticamente "fuori sede". Per evitare i requisiti aggiuntivi per proteggere il supporto di backup sul posto.

Per ulteriori informazioni, vedere [che cos'è il Backup di Azure?](../backup/backup-introduction-to-azure-backup.md) e [Configurare il Backup di Azure per i dati di Data Protection Manager](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Database

È possibile avere una soluzione di ripristino di emergenza per i database di SQL Server in un ambiente ibrido IT mediante l'utilizzo di disponibilità AlwaysOn, che rispecchiano database, la distribuzione dei log e copia di backup e ripristino con archiviazione Blob Azure. Tutte queste soluzioni usare SQL Server in macchine virtuali di Microsoft Azure in esecuzione.

Disponibilità AlwaysOn possono essere utilizzati in un ambiente ibrido IT in cui le repliche di database sono presenti entrambe locale e nel cloud. Come illustrato nella figura seguente.

![SQL Server di disponibilità AlwaysOn in un'architettura di cloud ibrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Database che rispecchiano può occupare server locali e cloud in un'installazione basata su certificato. Il diagramma seguente illustra il concetto.

![SQL Server database che rispecchiano in un'architettura di cloud ibrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

La distribuzione dei log può essere utilizzato per sincronizzare un database locale con un database di SQL Server in una macchina virtuale Azure.

![Distribuzione in un'architettura di cloud ibrida log di SQL Server](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Infine, è possibile backup automatici di un database locale direttamente a archiviazione Blob Azure.

![Eseguire il backup SQL Server per l'archiviazione Blob Azure in un'architettura di cloud ibrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Per ulteriori informazioni, vedere [alta disponibilità e ripristino di emergenza per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) ed [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Elenchi di controllo per il recupero locale in Microsoft Azure

###<a name="networking"></a>Rete

  1. Nella sezione rete del documento.
  2. Utilizzare virtuali per garantire una connessione locale nel cloud.

###<a name="compute"></a>Calcolare

  1. Nella sezione elaborazione di questo documento.
  2. Riposizionare macchine virtuali tra Hyper-V e Azure.

###<a name="storage"></a>Spazio di archiviazione

  1. Nella sezione dello spazio di archiviazione di questo documento.
  2. Vantaggi offerti da servizi StorSimple per l'uso di spazio di archiviazione cloud.
  3. Usare il servizio di Backup di Azure.

###<a name="database"></a>Database

  1. Nella sezione Database di questo documento.
  2. Valutare la possibilità di utilizzo di SQL Server in macchine virtuali di Azure come backup.
  3. Configurare la disponibilità AlwaysOn.
  4. Configurare che rispecchiano database basata su certificati.
  5. Utilizzare la distribuzione dei log.
  6. Eseguire il backup database locale a archiviazione Blob Azure.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie basata su [indicazioni tecniche sulla resilienza Azure](./resiliency-technical-guidance.md). Articolo successivo in questa serie è [il ripristino da danneggiamento dei dati o eliminazioni accidentali](./resiliency-technical-guidance-recovery-data-corruption.md).