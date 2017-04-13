<properties
    pageTitle="Configurazione di archiviazione per le macchine virtuali di SQL Server | Microsoft Azure"
    description="In questo argomento descrive come Azure consente di configurare lo spazio di archiviazione per le macchine virtuali di SQL Server durante il provisioning (modello di distribuzione Manager delle risorse). Viene inoltre come è possibile configurare lo spazio di archiviazione per le macchine virtuali Server SQL esistente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configurazione di archiviazione per le macchine virtuali di SQL Server

Quando si configura un'immagine di macchina virtuale di SQL Server in Azure, il portale consente di automatizzare la configurazione di archiviazione. Sono inclusi associare lo spazio di archiviazione per la macchina virtuale, rendere accessibile a SQL Server che lo spazio di archiviazione e configurandolo per ottimizzare ai propri requisiti di prestazioni specifici.

In questo argomento viene illustrato come Azure Configura lo spazio di archiviazione per le macchine virtuali Server SQL durante il provisioning e per le macchine virtuali esistenti. Questa configurazione si basa sulle [procedure consigliate delle prestazioni](virtual-machines-windows-sql-performance.md) per macchine virtuali di Azure che esegue SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

## <a name="prerequisites"></a>Prerequisiti
Per usare le impostazioni di configurazione dello spazio di archiviazione automatica, la macchina virtuale richiede le caratteristiche seguenti:

- Il provisioning con un' [immagine della raccolta SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Viene utilizzato il [modello di distribuzione di Manager delle risorse](../resource-manager-deployment-model.md).
- Viene utilizzata [l'archiviazione Premium](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Nuove macchine virtuali
Nelle sezioni seguenti viene descritto come configurare lo spazio di archiviazione per nuove macchine virtuali di SQL Server.

### <a name="azure-portal"></a>Portale di Azure
Durante il provisioning di una macchina virtuale Azure con un'immagine della raccolta di SQL Server, è possibile scegliere di configurare automaticamente lo spazio di archiviazione per la nuova macchina virtuale. Specificare le dimensioni dello spazio di archiviazione, limiti delle prestazioni e carichi di lavoro. La schermata seguente mostra e il configurazione dello spazio di archiviazione usato durante il SQL VM il provisioning.

![Configurazione di spazio di archiviazione SQL Server macchine Virtuali durante il Provisioning](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

In base alle scelte, Azure esegue le attività di configurazione dello spazio di archiviazione seguenti dopo aver creato la macchina virtuale:

- Crea e allega premium archiviazione dati dischi alla macchina virtuale.
- Consente di configurare dischi di dati per essere accessibili a SQL Server.
- Consente di configurare dischi di dati in un pool di archiviazione in base ai requisiti specificati dimensioni e prestazioni (secondo e velocità).
- Associa il pool di archiviazione a una nuova unità del computer virtuale.
- Consente di ottimizzare la nuova unità in base al tipo di carico di lavoro specificato (Data warehouse, transazione elaborazione o generale).

Per ulteriori informazioni su come Azure consente di configurare le impostazioni di archiviazione, vedere la [sezione Configurazione archiviazione](#storage-configuration). Per una procedura dettagliata completa su come creare una macchina virtuale Server SQL nel portale di Azure, vedere [l'esercitazione provisioning](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Gestire i modelli di risorse
Se si utilizzano i seguenti modelli di Manager delle risorse, due dischi dati premium sono collegati per impostazione predefinita, con alcuna configurazione del pool di archiviazione. Tuttavia, è possibile personalizzare questi modelli per modificare il numero di dischi dati premium associati alla macchina virtuale.

- [Creare macchine Virtuali con Backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Creare macchine Virtuali con aggiornamento automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Creare macchine Virtuali con integrazione AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Macchine virtuali esistenti
Per macchine virtuali di Server SQL esistente, è possibile modificare alcune impostazioni di archiviazione nel portale di Azure. Selezionare la macchina virtuale, passare all'area impostazioni e quindi selezionare configurazione SQL Server. E il configurazione SQL Server Mostra l'utilizzo corrente dello spazio di archiviazione delle macchine Virtuali. Tutte le unità presenti le macchine Virtuali vengono visualizzate nel grafico. Per ogni unità, lo spazio di archiviazione vengono visualizzati in quattro sezioni:

- Dati di SQL
- Log SQL
- Altro (spazio di archiviazione non SQL)
- Disponibile

![Configurare lo spazio di archiviazione per SQL Server macchine Virtuali esistente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Per configurare lo spazio di archiviazione per aggiungere una nuova unità o estendere un'unità esistente, fare clic sul collegamento Modifica sopra il grafico.

Le opzioni di configurazione che viene visualizzato varia a seconda di se è stata utilizzata questa caratteristica prima. Quando si usa per la prima volta, è possibile specificare i requisiti di spazio di archiviazione per una nuova unità. Se questa caratteristica è stata utilizzata per creare un'unità, è possibile estendere lo spazio di archiviazione dell'unità.

### <a name="use-for-the-first-time"></a>Usare per la prima volta
Se è la prima volta che questa caratteristica, è possibile specificare i limiti di dimensioni e prestazioni di archiviazione per una nuova unità. L'esperienza è simile a quanto visualizzato in fase di provisioning. La differenza principale è che non consentiti per specificare il tipo di carico di lavoro. Questa limitazione impedisce a interrompere le configurazioni di SQL Server esistente nel computer virtuale.

![Configurare i dispositivi di scorrimento SQL Server lo spazio di archiviazione](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crea una nuova unità in base alle proprie specifiche. In questo scenario, Azure esegue le attività di configurazione dello spazio di archiviazione seguenti:

- Crea e allega premium archiviazione dati dischi alla macchina virtuale.
- Consente di configurare dischi di dati per essere accessibili a SQL Server.
- Consente di configurare dischi di dati in un pool di archiviazione in base ai requisiti specificati dimensioni e prestazioni (secondo e velocità).
- Associa il pool di archiviazione a una nuova unità del computer virtuale.

Per ulteriori informazioni su come Azure consente di configurare le impostazioni di archiviazione, vedere la [sezione Configurazione archiviazione](#storage-configuration).

### <a name="add-a-new-drive"></a>Aggiungere una nuova unità
Se è già stato configurato lo spazio di archiviazione in macchine Virtuali di Server il SQL, espansione della memoria visualizza due nuove opzioni. La prima opzione consiste nell'aggiungere una nuova unità, che può aumentare le prestazioni della macchina virtuale.

![Aggiungere una nuova unità a una VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Dopo aver aggiunto l'unità, tuttavia, è necessario eseguire alcune configurazione manuale aggiuntiva per ottenere l'aumento delle prestazioni.

### <a name="extend-the-drive"></a>Estendere l'unità
Opzione per espandere lo spazio di archiviazione è per estendere l'unità esistente. Questa opzione aumenta dello spazio di archiviazione per l'unità, ma non aumento delle prestazioni. Con i pool di archiviazione, è possibile modificare il numero di colonne dopo aver creato il pool di archiviazione. Il numero di colonne determina il numero di scrittura in parallelo, che possono essere suddivisi in dischi di dati. Di conseguenza, eventuali dischi dati aggiunti non è possibile migliorare le prestazioni. Forniscono solo uno spazio di archiviazione per i dati scritti. Questa limitazione significa anche che, quando si estende l'unità, il numero di colonne determina il numero minimo di dischi di dati che è possibile aggiungere. Pertanto se si crea un pool di archiviazione con dischi di quattro dati, il numero di colonne è quattro. Ogni volta che si estende lo spazio di archiviazione, è necessario aggiungere dischi almeno quattro dati.

![Estendere un'unità per una VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configurazione di archiviazione
In questa sezione fornisce un riferimento le modifiche alla configurazione dello spazio di archiviazione che Azure esegue automaticamente durante la configurazione nel portale di Azure o SQL VM il provisioning.

- Se è stato selezionato meno di due questo servizio di archiviazione per la macchina virtuale, Azure non crea un pool di archiviazione.
- Se è stato selezionato almeno due questo servizio di archiviazione per la macchina virtuale, Azure consente di configurare un pool di archiviazione. La sezione successiva di questo argomento fornisce i dettagli della configurazione del pool di archiviazione.
- Configurazione di archiviazione automatica sempre utilizza dischi di dati di [archiviazione premium](../storage/storage-premium-storage.md) P30. Di conseguenza, non esiste un mapping 1:1 tra il numero selezionato di terabyte e il numero di dischi dati collegati a di una macchina virtuale.

Per informazioni sui prezzi, vedere la pagina [archiviazione prezzi](https://azure.microsoft.com/pricing/details/storage) nella scheda **Archiviazione su disco** .

### <a name="creation-of-the-storage-pool"></a>Creazione del pool di archiviazione
Azure utilizza le impostazioni seguenti per creare il pool di archiviazione in macchine virtuali di SQL Server.

| Impostazione | Valore |
|-----|-----|
| Dimensioni strisce  | 256 KB (Data warehouse); 64 KB (transazione) |
| Dimensioni del disco | 1 TB |
| Cache | Lettura |
| Dimensione allocazione | Dimensione dell'unità di assegnazione NTFS 64 KB |
| Inizializzazione file immediata | Attivato |
| Blocca pagine in memoria | Attivato |
| Ripristino | Recupero semplice (senza adattabilità) |
| Numero di colonne | Numero di dati dischi<sup>1</sup> |
| Posizione di TempDB | Archiviato in dati dischi<sup>2</sup> |

<sup>1</sup> dopo aver creato il pool di archiviazione, è possibile modificare il numero di colonne nel pool di archiviazione.

<sup>2</sup> questa impostazione si applica solo alla prima unità creato tramite la funzionalità di configurazione archiviazione.

## <a name="workload-optimization-settings"></a>Impostazioni di ottimizzazione di carico di lavoro
La tabella seguente descrive le opzioni di tipo tre carico di lavoro disponibili e le ottimizzazioni corrispondente:

| Tipo di carico di lavoro | Descrizione | Ottimizzazioni |
|-----|-----|-----|
| **Generale** | Impostazione predefinita che supporta la maggior parte dei carichi di lavoro | Nessuno |
| **Elaborazione delle transazioni** | Consente di ottimizzare lo spazio di archiviazione per carichi di lavoro OLTP tradizionali database | Contrassegno di analisi 1117<br/>Contrassegno di analisi 1118 |
| **Data warehouse** | Consente di ottimizzare lo spazio di archiviazione per carichi di lavoro di analisi e creazione di report | Contrassegno di analisi 610<br/>Contrassegno di analisi 1117 |

>[AZURE.NOTE] È possibile specificare il tipo di carico di lavoro solo durante il provisioning di una macchina virtuale SQL selezionandola nella schermata di configurazione dello spazio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
