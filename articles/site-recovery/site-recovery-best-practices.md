<properties
    pageTitle="Prepararsi per la distribuzione il ripristino del sito | Microsoft Azure"
    description="In questo articolo viene descritto come preparare distribuire la replica con il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Prepararsi per la distribuzione il ripristino del sito di Azure

Leggere questo articolo per una panoramica dei requisiti di distribuzione per ogni scenario di replica è supportata dal servizio il ripristino del sito di Azure. Dopo aver letto requisiti generali per ogni scenario, è possibile collegare a informazioni dettagliate sulla distribuzione specifiche per ogni distribuzione.

Dopo aver letto in questo articolo inserire commenti o domande nella parte inferiore dell'articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno. 

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Selezionare il modello di distribuzione

Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: il modello di gestione di risorse Azure e il modello di Gestione servizi classica. Azure include inoltre due portali: [portale classica Azure](https://manage.windowsazure.com/) che supporta il modello di distribuzione classica e il [portale di Azure](https://ms.portal.azure.com/) con supporto per entrambi i modelli di distribuzione.

Il ripristino del sito è disponibile nel portale di classica e il portale di Azure. Nel portale di classica Azure è possibile supportare il ripristino del sito con il modello di gestione dei servizi classica. Nel portale di Azure è possibile supportare il modello classico o distribuzioni Manager delle risorse. [Per ulteriori](site-recovery-overview.md#site-recovery-in-the-azure-portal) informazioni con il portale di Azure.

Quando si sceglie una nota del modello di distribuzione che:

- È consigliabile che utilizzare il [portale di Azure](https://portal.azure.com) e il modello di Manager delle risorse se possibile.
- Il ripristino del sito fornisce un percorso più semplice e intuitivo introduzione esperienza nel portale di Azure.
- Tramite il portale di Azure, è possibile replicare macchine classica e archiviazione di Manager delle risorse in Azure. Nel portale di Azure, inoltre, è possibile utilizzare gli account di archiviazione LRS o GRS.
- Portale di Azure combina i servizi di Backup e ripristino del sito in un singolo archivio di servizi di recupero in modo che è possibile configurare e gestire i servizi BCDR da un'unica posizione.
- Gli utenti con abbonamenti Azure viene completato il provisioning con il programma Cloud soluzione Provider (CSP) ora possono gestire operazioni di ripristino del sito nel portale di Azure.
- La replica macchine virtuali VMware o computer fisici in Azure nel portale di Azure offre numerose nuove funzionalità incluso il supporto per lo spazio di archiviazione premium e la possibilità di esclusione dischi specifici dalla replica.


## <a name="select-your-deployment-scenario"></a>Selezionare lo scenario di distribuzione

**Distribuzione** | **Dettagli** | **Portale di Azure** | **Portale classica** | **PowerShell**
---|---|---|---|---
**Macchine virtuali VMware in Azure** | Replicare macchine virtuali VMware allo spazio di archiviazione Azure | Nel Azure potrebbe non riuscire per portale macchine virtuali, su classica o lo spazio di archiviazione di Manager delle risorse<br/><br/> Distribuzione nel [portale di Azure](site-recovery-vmware-to-azure.md) offre un'esperienza di distribuzione semplificata e numerosi vantaggi di funzionalità. | Nel portale di classica Azure è possibile distribuire con il [modello avanzato](site-recovery-vmware-to-azure-classic.md) e non su classico dello spazio di archiviazione Azure.<br/><br/> È inoltre disponibile un modello di legacy che non deve essere usato per le nuove distribuzioni. |  PowerShell non è attualmente supportata.
**Macchine virtuali di Hyper-V in Azure** | Macchine virtuali Hyper-V allo spazio di archiviazione Azure. In host Hyper-V gestite in aree System Center VMM o senza VMM possono essere macchine virtuali. | Nel Azure portale macchine virtuali potrebbe non riuscire su classica o lo spazio di archiviazione di Manager delle risorse.<br/><br/> Portale di Azure offre un'esperienza di distribuzione semplificata. [Altre informazioni](site-recovery-vmm-to-azure.md) sui replicare macchine virtuali di Hyper-V nel cloud VMM. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md) sui replicare macchine virtuali di Hyper-V (senza VMM).| Nel portale di Azure classico si possono non riuscire macchine virtuali allo spazio di archiviazione Azure classica | Distribuzione di PowerShell è supportata.
**Server fisici per Azure** | Replicare server Windows/Linux fisici allo spazio di archiviazione Azure | Nel Azure portale server potrebbe non riuscire su classica o lo spazio di archiviazione di Manager delle risorse.<br/><br/> Distribuzione nel [portale di Azure](site-recovery-vmware-to-azure.md) offre un'esperienza di distribuzione semplificata e numerosi vantaggi di funzionalità. | Nel portale di classica Azure è possibile distribuire con il [modello avanzato](site-recovery-vmware-to-azure-classic.md) e non su classico dello spazio di archiviazione Azure.<br/><br/> È inoltre disponibile un modello di legacy che non deve essere usato per le nuove distribuzioni. | Distribuzione di PowerShell non è attualmente supportata.
**Server VMware macchine virtuali/fisici al sito secondario* | Replicare macchine virtuali VMware o server Windows/Linux fisici in un sito secondario. [Ulteriori informazioni e download](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) manuale dell'utente InMage esploratore. | Non disponibile nel portale di Azure | Nel portale di classica esploratore InMage verrà scaricato da un archivio il ripristino del sito. | Distribuzione di PowerShell non è attualmente supportata
**Macchine virtuali di Hyper-V in un sito secondario** | Replicare macchine virtuali di Hyper-V nel cloud VMM in un'area secondaria | Nel [portale di Azure](site-recovery-vmm-to-vmm.md) è possibile replicare macchine virtuali di Hyper-V nel cloud VMM in un sito secondario usando esclusivamente Replica Hyper-V. SAN non è attualmente supportata. | Nel portale di classica Azure è possibile replicare macchine virtuali di Hyper-V nel cloud VMM in un sito secondario con [Hyper-V Replica](site-recovery-vmm-to-vmm-classic.md) o [replica SAN](site-recovery-vmm-san.md) | Distribuzione di PowerShell è supportata



## <a name="check-what-you-need-for-deployment"></a>Selezionare gli elementi necessari per la distribuzione

### <a name="replicate-to-azure"></a>Replicare Azure

**Requisito** | **Dettagli** 
---|---
**Account Azure** | È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover. Per replicare Azure, è necessario un [account di archiviazione Azure](../storage/storage-introduction.md).<br/><br/>Se si distribuisce il ripristino del sito nel portale di classica è necessario uno o più [account di archiviazione GRS standard](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Se si distribuisce nel portale di Azure è possibile utilizzare lo spazio di archiviazione GRS o LRS.<br/><br/> Se si sta replicare macchine virtuali VMware o server fisici in archiviazione Azure premium portale è supportate. Si noti che se si usa un account di archiviazione premium è anche necessario un account di archiviazione standard per l'archiviazione dei registri di replica che acquisire le modifiche ai dati in locale. [Spazio di archiviazione Premium](../storage/storage-premium-storage.md) viene generalmente utilizzata per macchine virtuali che devono avere un in modo coerente prestazioni IO e latenza ridotta a carichi di lavoro in modo intensivo IO host.<br/><br/> Se si desidera utilizzare un account premium per memorizzare dati replicati, è necessario anche un account di archiviazione standard per l'archiviazione dei registri di replica che acquisire le modifiche ai dati in locale.
**Rete Azure** | Per replicare Azure, è necessario un network Azure macchine virtuali di Azure si connetterà quando vengono creati dopo il failover.<br/><br/> Se si distribuisce nel portale di classica utilizzare una rete classica. Se si distribuisce nel portale di Azure, è possibile utilizzare un classico o in rete Manager delle risorse.<br/><br/> La rete deve essere nella stessa regione come archivio.
**Connessione di rete (VMM in Azure)** | Se si sta replicare da VMM in Azure, [connessione di rete](site-recovery-network-mapping.md) garantisce macchine virtuali di Azure, connessi a reti corrette dopo il failover.<br/><br/> Per configurare la connessione di rete è necessario configurare le reti macchine Virtuali nel portale di VMM.
**Locale** | **Macchine virtuali VMware**: È necessario un computer locale che eseguono componenti il ripristino del sito, VMware vSphere hosts/vCenter server e macchine virtuali da replicare. [Per ulteriori](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Server fisici**: se si sta replicare server fisici è necessario un computer locale che eseguono componenti il ripristino del sito e server fisici da replicare. [Per ulteriori](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Se si desidera eseguire [il nuovo](site-recovery-failback-azure-to-vmware.md) dopo il controllo a Azure occorre un'infrastruttura VMware farlo.<br/><br/> **Macchine virtuali di Hyper-V**: se si desidera replicare macchine virtuali di Hyper-V nel cloud VMM è necessario un server VMM e host Hyper-V in quali macchine virtuali si desidera proteggere si trovano. [Per ulteriori](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Se si desidera replicare macchine virtuali di Hyper-V senza VMM è necessario host Hyper-V in cui si trovano macchine virtuali. [Per ulteriori](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Computer protetto** | Computer protetto in grado di replicare in Azure devono soddisfare i [Prerequisiti Azure](#azure-virtual-machine-requirements) descritto di seguito.


### <a name="replicate-to-a-secondary-site"></a>Replicare in un sito secondario

**Requisito** | **Dettagli** 
---|---
**Account Azure** | È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Locale** | **Macchine virtuali VMware**: nel sito principale è necessario un server di processo per la memorizzazione nella cache, la compressione e la crittografia dei dati di replica prima di inviarlo al sito secondario. Nel sito secondario è necessario installare un server di configurazione per gestire e controllare la distribuzione e un server di destinazione principale. È anche consigliabile un server vContinuum per facilitare la gestione. Inoltre, è necessario uno o più host di VMware vSphere e, facoltativamente, un server vCenter. [Per saperne di più](site-recovery-vmware-to-vmware.md)<br/><br/> **Macchine virtuali di Hyper-V nel cloud VMM**: È necessario configurare i server VMM e host Hyper-V contenente macchine virtuali da replicare. [Per ulteriori](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Connessione di rete (VMM a VMM)** | Se si sta replicare da VMM a VMM, [connessione di rete](site-recovery-network-mapping.md) garantisce che macchine virtuali di replica sono connesse a reti appropriate dopo il failover e vengono inserite in modo ottimale in host Hyper-V. Per configurare la connessione di rete è necessario configurare le reti macchine Virtuali sui server VMM.
**Mapping di spazio di archiviazione** | Se sta replicate da VMM in VMM se lo si desidera impostare [il mapping di spazio di archiviazione](site-recovery-storage-mapping.md) per specificare la destinazione di archiviazione per le macchine virtuali replicate. Mapping di spazio di archiviazione può essere configurato per la replica di Replica Hyper-V e SAN.<br/><br/> Mapping di spazio di archiviazione non è attualmente supportato nel portale di Azure.


## <a name="verify-url-access"></a>Verificare l'accesso URL

Assicurarsi che questi URL sono accessibili dal server.

**URL** | **VMM a VMM** | **VMM in Azure** | **Hyper-V in modo Azure** | **VMware in Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Consenti | Consenti | Consenti | Consenti
*. backup.windowsazure.com | Non è necessario | Consenti | Consenti | Consenti
*. hypervrecoverymanager.windowsazure.com | Consenti | Consenti | Consenti | Consenti
*. store.core.windows.net | Consenti | Consenti | Consenti | Consenti
*. blob.core.windows.net | Non è necessario | Consenti | Consenti | Consenti
https://www.msftncsi.com/ncsi.txt | Consenti | Consenti | Consenti | Consenti
https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Non è necessario | Non è necessario | Non è necessario | Consenti

## <a name="azure-virtual-machine-requirements"></a>Requisiti del computer virtuale Azure

È possibile distribuire il ripristino del sito per replicare macchine virtuali e fisici server che eseguono sistemi operativi supportati da Azure. Sono inclusi la maggior parte delle versioni di Windows e Linux. È necessario assicurarsi che locale macchine virtuali che si desidera proteggere è conforme ai requisiti di Azure.


**Caratteristica** | **Requisiti** | **Dettagli**
---|---|---
Host Hyper-V | Deve essere in esecuzione Windows Server 2012 R2 | Prerequisiti controllo avrà esito negativo se il sistema operativo non supportato
Hypervisor VMware  | Sistemi operativi supportati | [Verificare i requisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo guest | Hyper-V in modo replica Azure: il ripristino del sito supporta tutti i sistemi operativi sono [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per server fisico replica e VMware: controllare i [Prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) di Windows e Linux | Prerequisiti controllo avrà esito negativo se non supportate.
Architettura del sistema operativo guest | 64 bit | Prerequisiti controllo avrà esito negativo se non supportate
Dimensioni del disco rigido del sistema operativo |  Fino a 1023 GB | Prerequisiti controllo avrà esito negativo se non supportate
Sistema operativo dischi | 1 | Prerequisiti controllo avrà esito negativo se non supportate.
Numero di dati su disco | minore o uguale a 16 (valore massimo è una funzione delle dimensioni della macchina virtuale da creare. 16 = XL) | Prerequisiti controllo avrà esito negativo se non supportate
Dimensioni del disco rigido virtuale disco dati | Fino a 1023 GB | Prerequisiti controllo avrà esito negativo se non supportate
Schede di rete | Sono supportate più schede |
Indirizzo IP statico | Supportati | Se la macchina virtuale principale utilizza un indirizzo IP statico è possibile specificare l'indirizzo IP statico per la macchina virtuale creata in Azure. Si noti che l'indirizzo IP statico per una macchina virtuale linux in esecuzione su Hyper-v non è supportato.
disco iSCSI | Non è supportata | Prerequisiti controllo avrà esito negativo se non supportate
Disco rigido virtuale condiviso | Non è supportata | Prerequisiti controllo avrà esito negativo se non supportate
FC disco | Non è supportata | Prerequisiti controllo avrà esito negativo se non supportate
Formato del disco rigido| DISCO RIGIDO VIRTUALE <br/><br/> VHDX | Sebbene VHDX non è attualmente supportato in Azure, il ripristino del sito converte automaticamente VHDX disco rigido virtuale quando non si riesce sopra a Azure. Quando non si riesce a locale macchine virtuali continuare a usare il formato VHDX.
BitLocker | Non è supportata | Prima di proteggere una macchina virtuale, è necessario disattivare BitLocker.
Nome macchina virtuale| Da 1 a 63 caratteri. Solo lettere, numeri e trattini. Deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà macchina virtuale nel ripristino del sito
Tipo di macchina virtuale | <p>Generazione 1</p> <p>Generazione 2 - Windows</p> |  Generazione 2 macchina virtuale con il tipo di disco rigido del sistema operativo del disco di base che include 1 o 2 volumi di dati con formato disco come VHDX che è minore di 300GB è supportato. Macchine virtuali Linux generazione 2 non sono supportate. [Leggere altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Ottimizzare la distribuzione

Utilizzare i suggerimenti seguenti per ottimizzare e scalare una distribuzione.

- **Dimensione del volume del sistema operativo**: quando si replica una macchina virtuale in Azure volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di più volumi più è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensione disco dati**: se sta replicate in Azure avere fino a 32 dischi dati in un computer virtuali, ognuno con un massimo di 1 TB. In modo efficiente, è possibile replicare e avere esito negativo su una macchina virtuale ~ 32 TB.
- **Limiti di piano di ripristino**: il ripristino del sito può adatta per migliaia di macchine virtuali. Piani di ripristino sono progettati come modello per le applicazioni che devono avere esito negativo su insieme in modo che è necessario limitare il numero di computer in un piano di ripristino su 50.
- **Limiti di servizio Azure**: Azure ogni abbonamento include un insieme di limiti predefiniti in core, cloud services e così via. È consigliabile eseguire test caso di errore per convalidare la disponibilità delle risorse in abbonamento. È possibile modificare questi limiti tramite supporto Azure.
- **Pianificazione della capacità**: leggere le informazioni sulla [pianificazione delle capacità](site-recovery-capacity-planner.md) per il ripristino del sito.
- **Larghezza di banda replica**: se si è breve sulla larghezza di banda replica tenere presente che:
    - **ExpressRoute**: il ripristino del sito interagisce con Azure ExpressRoute e WAN si, ad esempio Riverbed. [Per ulteriori](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) informazioni sui ExpressRoute.
    - **Il traffico di replica**: viene utilizzato il ripristino del sito eseguita una replica iniziale smart usando solo i blocchi di dati e non l'intero disco rigido virtuale. Solo le modifiche vengono replicate durante la replica in corso.
    - **Il traffico di rete**: È possibile controllare il traffico di rete utilizzato per la replica installando e configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con criteri in base all'indirizzo IP di destinazione e porta.  Inoltre, se si sta la replica per il ripristino del sito di Azure tramite l'agente di Backup di Azure è possibile configurare la limitazione per tale agente. [Per ulteriori](https://support.microsoft.com/kb/3056159).
- **RTO**: misurare l'obiettivo di tempo di ripristino (RTO) è probabile che con il ripristino del sito, è consigliabile eseguire test caso di errore e visualizzare i processi di ripristino del sito per analizzare quantità temporizzare necessario per completare le operazioni. Se sta mancata su Azure, per RTO migliore è consigliabile automatizzare tutte le azioni manuale mediante l'integrazione con Azure piani di automazione e ripristino.
- **Rilasciato**: il ripristino del sito supporta un obiettivo di punto di ripristino vicino sincronizzata (rilasciato) quando si replica in Azure. Si presuppone larghezza di banda sufficiente tra il Data Center e Azure.


##<a name="service-urls"></a>URL di servizi
Assicurarsi che questi URL sono accessibili dal server


**URL** | **VMM a VMM** | **VMM in Azure** | **Sito di Hyper-V in Azure** | **VMware in Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 \*. backup.windowsazure.com |  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 \*. hypervrecoverymanager.windowsazure.com | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 \*. store.core.windows.net | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 \*. blob.core.windows.net |  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 https://www.msftncsi.com/ncsi.txt | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio  | Accesso obbligatorio
 https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Accesso obbligatorio


## <a name="next-steps"></a>Passaggi successivi

Dopo la formazione e il confronto dei requisiti di distribuzione generale è possibile leggere i prerequisiti dettagliati e avviare la distribuzione di ogni scenario.

- [Replicare macchine virtuali VMware Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare server fisici Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare server Hyper-V in cloud VMM Azure](site-recovery-vmm-to-azure.md)
- [Replicare macchine virtuali di Hyper-V (senza VMM) Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicare macchine virtuali di Hyper-V in un sito secondario](site-recovery-vmm-to-vmm.md)
- [Replicare macchine virtuali di Hyper-V in un sito secondario con SAN](site-recovery-vmm-san.md)
- [Replicare macchine virtuali di Hyper-V con un singolo server VMM](site-recovery-single-vmm.md)
