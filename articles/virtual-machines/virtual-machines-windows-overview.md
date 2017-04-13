<properties
    pageTitle="Cenni preliminari sulle macchine virtuali di Windows | Microsoft Azure"
    description="Informazioni sulla creazione e gestione macchine virtuali di Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Panoramica delle macchine virtuali di Windows Azure

Macchine virtuali di Azure (macchine Virtuali) corrisponde a uno dei diversi tipi di [risorse di elaborazione scalable, su richiesta](../app-service-web/choose-web-site-cloud-service-vm.md) che offre Azure. In genere si sceglie una macchina virtuale quando è necessario un maggiore controllo dell'ambiente informatico che offrono altre opzioni. In questo articolo fornisce informazioni su cosa è necessario prendere in considerazione prima di creare una macchina virtuale, come si crea e come si gestiscono.

Una macchina virtuale Azure offre flessibilità di virtualizzazione senza che sia necessario acquistare e gestire l'hardware che viene eseguito. Tuttavia, è comunque necessario mantenere la macchina virtuale eseguendo attività, ad esempio la configurazione, patch e installare il software in esecuzione su di esso.

Azure macchine virtuali è utilizzabile in vari modi. Alcuni esempi sono:

- **Sviluppo e test** -macchine virtuali Azure offrono un modo creare un computer con configurazioni specifiche semplice e rapido necessari al codice e testare un'applicazione.
- **Applicazioni nel cloud** , in quanto può variare richiesta per l'applicazione, può essere utile economico per l'esecuzione in una macchina virtuale in Azure. Quando è necessario e li arresta quando non non pagare macchine virtuali aggiuntive.
- **Data Center estesa** -macchine virtuali in una rete virtuale Azure facilmente possono essere connessi alla rete dell'organizzazione.

Il numero di macchine virtuali utilizzate dall'applicazione ridimensionare verticale e orizzontale tutto quello che si necessari per soddisfare le esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Che cos'è necessario considerare prima di creare una macchina virtuale?

Esistono sempre una vasta gamma di [considerazioni di progettazione](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) quando si creano un'infrastruttura di applicazione in Azure. Questi aspetti una macchina virtuale sono importanti da considerare prima di iniziare:

- I nomi delle risorse dell'applicazione
- La posizione in cui sono memorizzate le risorse
- Le dimensioni della macchina virtuale
- Il numero massimo di macchine virtuali che è possibile creare
- Il sistema operativo che viene eseguita la macchina virtuale
- La configurazione di macchine Virtuali dopo l'avvio
- Risorse correlate che deve essere la macchina virtuale

### <a name="naming"></a>Assegnare un nome

Una macchina virtuale ha un [nome](virtual-machines-windows-infrastructure-naming-guidelines.md) assegnato e ha un nome computer configurato come parte del sistema operativo. Il nome di una macchina virtuale può contenere al massimo 15 caratteri.

Se si usa Azure per creare il disco di sistema operativo, il nome del computer e il nome del computer virtuale sono gli stessi. Se si [Carica e usare un'immagine personalizzata](virtual-machines-windows-upload-image.md) che contiene un sistema operativo configurato in precedenza e usarlo per creare una macchina virtuale, i nomi possono essere diversi. È consigliabile quando si carica file immagine, effettuare il nome del computer nel sistema operativo e la macchina virtuale assegnare un nome uguale.

### <a name="locations"></a>Percorsi

Tutte le risorse create in Azure sono distribuite in più [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. In genere, l'area geografica è chiamato **posizione** quando si crea una macchina virtuale. Per una macchina virtuale, la posizione specifica in cui sono archiviati dischi rigidi virtuali.

Questa tabella illustra alcuni modi per ottenere un elenco delle posizioni disponibili.

| Metodo | Descrizione |
| ------ | ----------- |
| Portale di Azure | Selezionare un percorso nell'elenco quando si crea una macchina virtuale. |
| PowerShell Azure | Usare il comando [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| API REST | Utilizzare l'operazione di [percorsi List](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Dimensione memoria virtuale

Le [dimensioni](virtual-machines-windows-sizes.md) di macchine Virtuali in uso è il carico di lavoro che si desidera eseguire. Le dimensioni che si è scelto quindi determinano fattori capacità power, memoria e spazio di archiviazione di elaborazione. Azure offre una vasta gamma di dimensioni per supportare molti tipi di utilizzo.

Azure costi un [prezzo orario](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in base a dimensioni e il sistema operativo la macchina virtuale. Per ore parziale, in base alle tariffe Azure solo per i minuti utilizzati. Spazio di archiviazione è un prezzo e addebitata separatamente.

### <a name="vm-limits"></a>Limiti di macchine Virtuali

L'abbonamento contiene predefiniti [limiti di quota](../azure-subscription-service-limits.md) che potrebbero avere un impatto della distribuzione di più macchine virtuali per il progetto. Il limite corrente alla scala cronologica per abbonamento è 20 macchine virtuali per ogni area geografica. Limiti possono essere generati da un ticket di supporto che richiede un aumento di archiviazione.

### <a name="operating-system-disks-and-images"></a>Immagini e dischi del sistema operativo

Macchine virtuali di utilizzare [virtuali rigido dischi rigidi](virtual-machines-windows-about-disks-vhds.md) per archiviare le sistema operativo (SO) e i dati. Dischi rigidi virtuali utilizzati anche per le immagini che da cui è possibile scegliere di installare un sistema operativo. 

Azure offre molte [immagini marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) per l'uso con diverse versioni e tipi di sistemi operativi Windows Server. Immagini Marketplace sono identificate da publisher immagine, offerta, sku e versione (in genere versione è specificato come più recente). 

Questa tabella illustra alcuni metodi che è possibile trovare informazioni per un'immagine.

| Metodo | Descrizione |
| ------ | ----------- |
| Portale di Azure | I valori vengono specificati automaticamente automaticamente quando si seleziona un'immagine da utilizzare. |
| PowerShell Azure | [Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -"posizione"<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -"posizione"-"publisherName" di Publisher<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -"posizione"-Publisher "publisherName"-offrono "offerName" |
| API REST | [Autori di immagine di elenco](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Immagine elenco offre](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Elenco immagine SKU](https://msdn.microsoft.com/library/mt743701.aspx) |

È possibile scegliere di [caricare e utilizzare la propria immagine](virtual-machines-windows-upload-image.md) e quando si esegue il nome dell'autore, offerta e sku non utilizzati.

### <a name="extensions"></a>Estensioni

Macchine Virtuali [estensioni](virtual-machines-windows-extensions-features.md) fornire le funzionalità aggiuntive macchine Virtuali tramite configurazione di distribuzione post e le operazioni automatiche.

Queste attività comuni possono essere eseguite mediante le estensioni:

- **Esecuzione di script personalizzati** : l' [Estensione di Script personalizzati](virtual-machines-windows-extensions-customscript.md) consente di configurare carichi di lavoro nella macchina virtuale eseguendo lo script quando viene eseguito il provisioning macchina virtuale.
- **Distribuisci e gestire le configurazioni** : l' [estensione di PowerShell bene accolta stato configurazione DSC ()](virtual-machines-windows-extensions-dsc-overview.md) consente di impostare DSC in una macchina virtuale per gestire le configurazioni e ambienti.
- **Raccogliere dati di diagnostica** : l' [Estensione di diagnostica Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) consente di configurare la macchina virtuale per raccogliere dati di diagnostica che possono essere utilizzati per monitorare l'integrità dell'applicazione.

### <a name="related-resources"></a>Risorse correlate

Le risorse in questa tabella vengono utilizzate per la macchina virtuale e si vuole esistere o essere creata quando viene creata la macchina virtuale.

| Risorsa | Obbligatorio | Descrizione |
| -------- | -------- | ----------- |
| [Gruppo di risorse](../azure-resource-manager/resource-group-overview.md) | Sì | La macchina virtuale deve essere contenuta in un gruppo di risorse. |
| [Account di archiviazione](../storage/storage-create-storage-account.md) | Sì | La macchina virtuale è necessario l'account di archiviazione per archiviare i dischi rigidi virtuali. |
| [Rete virtuale](../virtual-network/virtual-networks-overview.md) | Sì | La macchina virtuale deve essere un membro di una rete virtuale. |
| [Indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | No | La macchina virtuale può avere un indirizzo IP pubblico assegnato per accedervi in remoto. |
| [Interfaccia di rete](../virtual-network/virtual-network-network-interface-overview.md) | Sì | La macchina virtuale deve l'interfaccia di rete per comunicare nella rete. |
| [Dischi di dati](virtual-machines-windows-attach-disk-portal.md) | No | La macchina virtuale possa includere dati dischi per espandere le funzionalità di archiviazione. |

## <a name="how-do-i-create-my-first-vm"></a>Creazione di personale macchine Virtuali prima

Sono disponibili diverse opzioni per la creazione di una macchina virtuale. L'opzione dipende dall'ambiente che ci si trova. 

In questa tabella offre informazioni introduttive sulla creazione di una macchina virtuale.

| Metodo | Articolo |
| ------ | ------- |
| Portale di Azure | [Creare una macchina virtuale che esegue Windows tramite il portale](virtual-machines-windows-hero-tutorial.md) |
| Modelli | [Creare una macchina virtuale di Windows con un modello di Manager delle risorse](virtual-machines-windows-ps-template.md) |
| PowerShell Azure | [Creare una macchina virtuale di Windows tramite PowerShell](virtual-machines-windows-ps-create.md) |
| Client SDK | [Distribuire risorse Azure utilizzando c#](virtual-machines-windows-csharp.md) |
| API REST | [Creare o aggiornare una macchina virtuale](https://msdn.microsoft.com/library/mt163591.aspx) |

Si auguriamo che non si verifica mai, ma in alcuni casi di errori. Se si verifica questa situazione all'utente, esaminare le informazioni contenute in [Gestione risorse di risoluzione dei problemi di distribuzione con la creazione di una macchina virtuale di Windows Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Come è possibile gestire macchine Virtuali creato personalmente?

Macchine virtuali possono essere gestite usando un portale basato su browser strumenti della riga di comando con il supporto per lo script o direttamente tramite API. Alcune attività di gestione tipiche che è possibile eseguire sono informazioni su una macchina virtuale, l'accesso a una macchina virtuale, la gestione della disponibilità e backup.

### <a name="get-information-about-a-vm"></a>Ottenere informazioni su una macchina virtuale

Questa tabella illustra alcuni modi che è possibile ottenere informazioni su una macchina virtuale.

| Metodo | Descrizione |
| ------ | ----------- |
| Portale di Azure | Nel menu hub fare clic su **macchine virtuali** e quindi selezionare la macchina virtuale dall'elenco. Della pala per la macchina virtuale, è possibile accedere a informazioni generali, i valori e il monitoraggio metriche. |
| PowerShell Azure | Per informazioni sull'utilizzo di PowerShell per gestire macchine virtuali, vedere [gestire macchine virtuali di Azure tramite Gestione risorse e PowerShell](virtual-machines-windows-ps-manage.md). |
| API REST | Utilizzare l'operazione di [macchine Virtuali di ottenere informazioni](https://msdn.microsoft.com/library/mt163682.aspx) per ottenere informazioni su una macchina virtuale. |
| Client SDK | Per informazioni sull'utilizzo c# per gestire macchine virtuali, vedere [gestire macchine virtuali di Azure mediante Gestione risorse di Azure e c#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Accedere a macchina virtuale

Utilizzare il pulsante Connetti nel portale di Azure per [avviare una sessione remota RDP (Desktop)](virtual-machines-windows-connect-logon.md). In alcuni casi del problema quando si tenta di utilizzare una connessione remota. Se si verifica questa situazione all'utente, vedere le informazioni della Guida a [risolvere i problemi di Desktop remoto connessioni a una macchina virtuale Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gestire la disponibilità

È importante per capire come [verificare la disponibilità elevata](virtual-machines-windows-manage-availability.md) per l'applicazione. Questa configurazione consiste nel creare più macchine virtuali per garantire che almeno un sia in esecuzione.

Affinché la distribuzione ottenere il nostro 99.95 contratto di servizio macchine Virtuali, è necessario distribuire macchine virtuali di due o più in esecuzione il carico di lavoro all'interno di un [set di disponibilità](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Grazie a questa configurazione nelle macchine virtuali distribuite in più domini guasto e distribuite in host con windows manutenzione diversi. Intero [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) viene garantita disponibilità di Azure nel suo insieme.
 
### <a name="back-up-the-vm"></a>Eseguire il backup della macchina virtuale
 
Per proteggere i dati e risorse in servizi di Azure Backup e il ripristino del sito di Azure viene usato un [archivio di servizi di recupero](../backup/backup-introduction-to-azure-backup.md) . È possibile utilizzare un archivio di servizi di recupero di [distribuzione e gestione di backup per macchine virtuali distribuito Manager delle risorse tramite PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Passaggi successivi

- Se si intende utilizzare con macchine virtuali Linux, osservare [Azure e Linux](virtual-machines-linux-azure-overview.md).
- Acquisire familiarità con le indicazioni sulla configurazione dell'infrastruttura di [questa procedura dettagliata dell'infrastruttura di Azure di esempio](virtual-machines-windows-infrastructure-example.md).
- Assicurarsi di che seguire le [Procedure consigliate per l'esecuzione di una macchina virtuale di Windows in Azure](virtual-machines-windows-guidance-compute-single-vm.md).


