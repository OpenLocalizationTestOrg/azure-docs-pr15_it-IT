<properties
    pageTitle="Prestazioni ottimali SQL Server | Microsoft Azure"
    description="Procedure ottimali per ottimizzare le prestazioni di SQL Server in macchine virtuali di Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure

## <a name="overview"></a>Panoramica

In questo argomento vengono fornite le procedure consigliate per l'ottimizzazione delle prestazioni di SQL Server in Microsoft Azure Virtual Machine. Durante l'esecuzione di SQL Server in macchine virtuali di Azure, è consigliabile continuare con le stesse prestazioni database ottimizzazione delle opzioni disponibili per SQL Server nell'ambiente server locale. Tuttavia, le prestazioni di un database relazionale in un'area pubblica dipendono da diversi fattori, ad esempio le dimensioni di una macchina virtuale e la configurazione dei dischi di dati.

Durante la creazione di immagini di SQL Server, [valutare la possibilità di provisioning nelle macchine virtuali nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Macchine virtuali di Server SQL viene completato il provisioning nel portale di gestione risorse implementare tutti questi le procedure consigliate, inclusa la configurazione archiviazione.

In questo articolo è incentrato su come ottenere le *massime* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro richiede un livello inferiore, potrebbe non essere necessaria ogni ottimizzazione elencata di seguito. Durante la valutazione di queste raccomandazioni, prendere in considerazione le esigenze di prestazioni e modelli di carico di lavoro.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Elenco di controllo rapido

Di seguito è un elenco di controllo rapido per ottenere prestazioni ottimali di SQL Server in macchine virtuali di Azure:

|Area|Ottimizzazioni|
|---|---|
|[Dimensione memoria virtuale](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) o versioni successive per SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) o superiore per le edizioni di SQL Standard e Web.|
|[Spazio di archiviazione](#storage-guidance)|Utilizzare [l'archiviazione Premium](../storage/storage-premium-storage.md). Spazio di archiviazione standard è consigliato solo per sviluppo/test.<br/><br/>Mantenere gli [account di archiviazione](../storage/storage-create-storage-account.md) e macchine Virtuali di SQL Server nella stessa regione.<br/><br/>Disattivare Azure [geografico ridondanti dello spazio di archiviazione](../storage/storage-redundancy.md) (geografico replica) per l'account di archiviazione.|
|[Dischi](#disks-guidance)|Usare un minimo di 2 [dischi P30](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 per i file di log; 1 per i file di dati e database temporaneo).<br/><br/>Evitare l'utilizzo del sistema operativo o dischi temporanei per lo spazio di archiviazione di database o per la registrazione.<br/><br/>Abilita leggere la memorizzazione nella cache su disco che ospita i file di dati e database temporaneo.<br/><br/>Non abilitare la memorizzazione nella cache nei dischi hosting il file di log.<br/><br/>Importante: Arrestare il servizio SQL Server quando si modificano le impostazioni della cache per un disco macchine Virtuali di Azure.<br/><br/>Stripe più dischi di dati di Azure per aumentare la velocità effettiva IO.<br/><br/>Formato con descritte le dimensioni di allocazione.|
|[/ O](#io-guidance)|Abilitare la compressione di pagina di database.<br/><br/>Abilitare l'inizializzazione file immediata per i file di dati.<br/><br/>Limitare o disattivare l'aumento del database.<br/><br/>Disabilitare la compattazione automatica sul database.<br/><br/>Spostare tutti i database dischi dati, inclusi i database di sistema.<br/><br/>Spostare Directory file registro e di traccia SQL Server errore dischi di dati.<br/><br/>Programma di installazione posizioni dei file di backup e il database predefinito.<br/><br/>Abilitare pagine bloccate.<br/><br/>Applicare le correzioni per le prestazioni di SQL Server.|
|[Funzionalità specifiche](#feature-specific-guidance)|Eseguire il backup direttamente a archiviazione blob.|

Per ulteriori informazioni su *come* e *perché* apportare queste ottimizzazioni, rivedere i dettagli e le indicazioni fornite nelle sezioni seguenti.

## <a name="vm-size-guidance"></a>Guida di dimensioni macchine Virtuali

Per le applicazioni sensibili prestazioni, si consiglia di utilizzare le seguenti [macchine virtuali di dimensioni](virtual-machines-windows-sizes.md):

- **SQL Server Enterprise Edition**: DS3 o versioni successive

- **SQL Server Standard e le edizioni Web**: DS2 o versioni successive


## <a name="storage-guidance"></a>Indicazioni per lo spazio di archiviazione

Supporto per macchine virtuali di serie DS (insieme DSv2 serie e serie GS) [Premium lo spazio di archiviazione](../storage/storage-premium-storage.md). Spazio di archiviazione Premium è consigliato per tutti i carichi di produzione.

> [AZURE.WARNING] Lo spazio di archiviazione standard ha diverse latenza e larghezza di banda e solo consigliato per carichi di lavoro di sviluppo/test. Carichi di lavoro di produzione necessario utilizzare lo spazio di archiviazione Premium.

Inoltre, è consigliabile creare l'account di archiviazione Azure in stesso data center come macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disattivare la replica geografico come ordine di scrittura coerente su più dischi non è garantito. Se, tuttavia, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due Azure data center. Per ulteriori informazioni, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Indicazioni per dischi

Esistono tre tipi di disco principale in una macchina virtuale Azure:

- **Disco rigido del sistema operativo**: quando si crea una macchina virtuale di Azure, la piattaforma allegherà almeno un disco (etichettato con l'unità **C** ) per la macchina virtuale per il disco di sistema operativo. Il disco è un disco rigido virtuale memorizzato come blob pagina in un archivio.
- **Disco temporaneo**: macchine virtuali di Azure contengono un altro disco chiamato disco temporaneo (etichettata con **D**: unità). Si tratta di un disco del nodo che può essere utilizzato per lo spazio di lavoro.
- **Dischi di dati**: È anche possibile allegare dischi aggiuntivi alla macchina virtuale come dischi di dati e questi verranno archiviati in archiviazione come BLOB di pagine.

Le sezioni seguenti descrivono consigli per l'uso di questi dischi diversi.

### <a name="operating-system-disk"></a>Sistema operativo disco

Un disco di sistema operativo è un disco rigido virtuale che è possibile eseguire l'avvio e montaggio come una versione del sistema operativo e viene segnalato come unità **C** .

Predefinito della cache dei criteri sul disco di sistema operativo è **In lettura/scrittura**. Per le applicazioni sensibili prestazioni, è consigliabile utilizzare dischi dati anziché il disco di sistema operativo. Vedere la sezione sui dischi di dati riportata di seguito.

### <a name="temporary-disk"></a>Disco temporaneo

Unità di archiviazione temporanea, etichettata con **D**: unità, non viene mantenuta archiviazione blob Azure. Non archiviare il file di database utente o un file di registro delle transazioni utente su **D**: unità.

Per D serie Dv2 serie e macchine virtuali di serie G, unità temporanea in queste macchine virtuali è basato su SSD. Se il carico di lavoro utilizza molto TempDB (ad esempio per gli oggetti temporanei o join complessi), l'archiviazione TempDB sull'unità **D** potrebbe causare velocità effettiva TempDB superiore e inferiore latenza TempDB.

Per le macchine virtuali che supportano l'archiviazione Premium (serie DS, serie DSv2 e GS serie), è consigliabile archiviare TempDB e/o le estensioni del Pool di Buffer su un disco che supporta l'archiviazione Premium con lettura cache abilitata. C'è un'eccezione per il suggerimento. Se l'utilizzo di TempDB che richiede significative attività di scrittura, è possibile ottenere prestazioni più elevate archiviando TempDB sull'unità **D** locale, ossia anche SSD basate su questi formati di computer.

### <a name="data-disks"></a>Dischi di dati

- **Utilizzare dischi di dati per i file di log e dati**: almeno, utilizzare 2 Premium archiviazione [dischi P30](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) nel punto in cui un disco contiene file di log e l'altro contenente il file di dati e database temporaneo.

- **Striping del disco**: per altre velocità, è possibile aggiungere nuovi dati dischi e utilizzare striping del disco. Per determinare il numero di dischi di dati, è necessario analizzare il numero di IOPS disponibile per i dischi dati e di log. Per ulteriori informazioni, vedere le tabelle in IOPS per dimensione [dimensione memoria virtuale](virtual-machines-windows-sizes.md) e disco vedere l'articolo seguente: [Uso Premium spazio di archiviazione per dischi](../storage/storage-premium-storage.md). Utilizzare la seguente alle linee guida seguenti:

    - Per Windows 8/Windows Server 2012 o versione successiva, usare [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx). Impostare dimensioni strisce a 64 KB per carichi di lavoro OLTP e 256 KB per carichi di lavoro deposito dati per evitare l'impatto sulle prestazioni a causa di problemi di allineamento partizione. Inoltre, impostare il numero di colonna = numero del disco. Per configurare uno spazio di archiviazione con più di 8 dischi è necessario utilizzare PowerShell (non UI Server Manager) impostare in modo esplicito il numero di colonne in base al numero di dischi. Per ulteriori informazioni su come configurare [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx), vedere [Cmdlet spazi di archiviazione in Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Per Windows 2008 R2 o versioni precedenti, è possibile utilizzare dinamici dischi (OS strisce) e la dimensione strisce è sempre 64 KB. Si noti che questa opzione è sconsigliata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa supporto al [Servizio dischi virtuali transizione all'API di gestione dello spazio di archiviazione di Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Se il carico di lavoro non ha eseguito l'accesso in modo intensivo e non è necessario IOPs dedicato, è possibile configurare un solo pool di archiviazione. In caso contrario, creare due pool di archiviazione, uno per i file di log e un altro pool di archiviazione per i file di dati e database temporaneo. Determinare il numero di dischi associati a ogni pool di archiviazione in base alle aspettative di caricamento. Tenere presente che dimensioni macchine Virtuali diverse consentano numeri diversi di dischi di dati allegato. Per ulteriori informazioni, vedere [le dimensioni per macchine virtuali](virtual-machines-windows-sizes.md).

    - Se non si usa lo spazio di archiviazione Premium (dev/test scenari), il suggerimento consiste nell'aggiungere il numero massimo di dischi di dati supportati per la [dimensione memoria virtuale](virtual-machines-windows-sizes.md) e utilizzare lo striping del disco.

- **Criterio di cache**: dischi di dati per archiviazione Premium, abilitare la lettura del disco dati hosting solo i file di dati e database temporaneo. Se non si usa lo spazio di archiviazione Premium, non attivare qualsiasi cache su qualsiasi disco di dati. Per istruzioni su come configurare la cache del disco, vedere gli argomenti seguenti: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [AzureDataDisk Set](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Arrestare il servizio SQL Server quando si modifica l'impostazione della cache dei dischi macchine Virtuali di Azure per evitare la possibilità di eventuali danneggiamenti al database.

- **Dimensione dell'unità di assegnazione NTFS**: quando si formatta il disco di dati, si consiglia di utilizzare una dimensione di unità di assegnazione 64 KB per i file di dati e log nonché TempDB.

- **Procedure consigliate per Gestione disco**: quando la rimozione di un disco dati o modificarne il tipo di cache, arrestare il servizio SQL Server durante la modifica. Quando si modificano le impostazioni della cache su disco del sistema operativo, Azure interrompe la macchina virtuale, viene modificato il tipo di cache e si riavvia la macchina virtuale. Quando si modificano le impostazioni della cache di un disco dati, la macchina virtuale non viene interrotto, ma il disco di dati è scollegato da macchina virtuale durante la modifica e quindi ricollegato.

    >[AZURE.WARNING] Errore arrestare il servizio SQL Server durante queste operazioni può causare il danneggiamento del database.

## <a name="io-guidance"></a>Indicazioni per i/o

- Quando si eseguire l'applicazione e le richieste, si ottengono risultati ottimali con Premium lo spazio di archiviazione. Spazio di archiviazione Premium è progettato per gli scenari in cui la profondità coda IO è maggiore di 1, in modo che le senza alcuna miglioramenti delle prestazioni per le richieste di seriale thread singolo (anche se si tratta dello spazio di archiviazione in modo intensivo). Ad esempio, si potrebbero avere un impatto risultati di test thread singolo di strumenti di analisi delle prestazioni, ad esempio SQLIO.

- È possibile utilizzare [la compressione page database](https://msdn.microsoft.com/library/cc280449.aspx) come consente di migliorare le prestazioni dei carichi di lavoro in modo intensivo i/o. Tuttavia, la compressione dei dati potrebbero aumentare l'utilizzo della CPU nel server di database.

- È consigliabile attivare l'inizializzazione file immediata per ridurre il tempo necessario per l'allocazione di file iniziale. Per sfruttare inizializzazione file immediata, concedere all'account di servizio SQL Server (MSSQLSERVER) con SE_MANAGE_VOLUME_NAME e aggiungerla ai criteri di sicurezza **Eseguire attività di manutenzione Volume** . Se si usa un'immagine di piattaforma SQL Server per Azure, l'account di servizio predefinito (NT Service\MSSQLSERVER) non verrà aggiunto ai criteri di sicurezza **Eseguire attività di manutenzione Volume** . In altre parole, inizializzazione file immediata non è disponibile in un'immagine di piattaforma SQL Server Azure. Dopo aver aggiunto l'account di servizio SQL Server ai criteri di sicurezza **Eseguire attività di manutenzione Volume** , riavviare il servizio SQL Server. Potrebbero essersi considerazioni sulla protezione per l'uso di questa caratteristica. Per ulteriori informazioni, vedere [Inizializzazione File del Database](https://msdn.microsoft.com/library/ms175935.aspx).

- **aumento** considerati semplicemente emergenza per la crescita imprevista. Non gestire i dati e log la crescita quotidiano con aumento. Se si utilizza l'aumento, pre-aumentare il file con l'opzione dimensioni.

- Assicurarsi che **la compattazione automatica** è disattivata per evitare inutile che possa influire negativamente sulle prestazioni.

- Spostare tutti i database dischi dati, inclusi i database di sistema. Per ulteriori informazioni, vedere [Spostare database di sistema](https://msdn.microsoft.com/library/ms345408.aspx).

- Spostare Directory file registro e di traccia SQL Server errore dischi di dati. Questa operazione può essere eseguita in Gestione configurazione SQL Server facendo l'istanza di SQL Server e scegliere Proprietà. Impostazioni di file log e Individua errore possono essere modificate nella scheda **Parametri di avvio** . Directory Scarica specificata nella scheda **Avanzate** . La schermata seguente mostra dove cercare il parametro di avvio di log degli errori.

    ![Schermata di log degli errori SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Programma di installazione posizioni dei file di backup e il database predefinito. Seguire i consigli riportati in questo argomento e apportare le modifiche nella finestra delle proprietà Server. Per ulteriori informazioni, vedere [visualizzare o modificare i percorsi predefiniti per i dati e file di Log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La schermata seguente illustra dove apportare queste modifiche.

    ![File di registro di dati SQL e copia di Backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Abilitare pagine bloccate per ridurre IO e tutte le attività di suddivisione in pagine. Per ulteriori informazioni, vedere [attivare le pagine di blocco nell'opzione di memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Se si esegue SQL Server 2012, installare il Service Pack 1 Cumulative Update 10. Questo aggiornamento contiene lo strumento Correggi per ridurre le prestazioni dei / o quando si esegue selezionare istruzione tabella temporanea in SQL Server 2012. Per informazioni, vedere l' [articolo della knowledge base](http://support.microsoft.com/kb/2958012).

- Valutare la possibilità di comprimere i file di dati durante il trasferimento all'interno/esterno di Azure.

## <a name="feature-specific-guidance"></a>Indicazioni specifiche caratteristica

Alcune distribuzioni possono ottenere prestazioni aggiuntive utilizzando tecniche di configurazione più avanzate. L'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che può essere utile per ottenere prestazioni migliori:

- **Copia di backup allo spazio di archiviazione Azure**: durante l'esecuzione di backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile utilizzare [Il Backup di SQL Server all'URL](https://msdn.microsoft.com/library/dn435916.aspx). Questa caratteristica è disponibile a partire da SQL Server 2012 SP1 CU2 e consigliati per il backup dischi di dati allegato. Quando si copia di backup e ripristino o dal sistema di archiviazione Azure, seguire le indicazioni fornite alla [Backup di SQL Server a URL procedure consigliate e risoluzione dei problemi e ripristino da backup archiviati in archiviazione Azure](https://msdn.microsoft.com/library/jj919149.aspx). È inoltre possibile automatizzare questi backup con [Backup automatico per SQL Server in macchine virtuali di Azure](virtual-machines-windows-classic-sql-automated-backup.md).

    Prima di SQL Server 2012, è possibile utilizzare [Il Backup di SQL Server Azure allo strumento](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità di backup con più strisce backup.

- **File di dati di SQL Server in Azure**: questa nuova funzione, [File di dati di SQL Server in Azure](https://msdn.microsoft.com/library/dn385720.aspx), è disponibile a partire da SQL Server 2014. Esegue SQL Server con file di dati di Azure, vengono illustrate le caratteristiche di prestazioni con dischi di dati di Azure.

## <a name="next-steps"></a>Passaggi successivi

Se è interessati esplorazione di SQL Server e lo spazio di archiviazione Premium in modo più approfondito, vedere l'articolo [Utilizzo Azure Premium archiviazione con SQL Server in macchine virtuali](virtual-machines-windows-classic-sql-server-premium-storage.md).

Per le procedure consigliate, vedere [Considerazioni sulla protezione per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

Esaminare altri argomenti macchina virtuale di SQL Server in [SQL Server Azure macchine virtuali di panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
