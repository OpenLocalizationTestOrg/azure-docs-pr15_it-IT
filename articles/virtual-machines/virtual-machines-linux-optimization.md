<properties
    pageTitle="Ottimizzare il supporto virtuale Linux su Azure | Microsoft Azure"
    description="Informazioni su alcuni suggerimenti per l'ottimizzazione per assicurarsi che è stata configurata la VM Linux per ottenere prestazioni ottimali in Azure"
    keywords="Linux virtuale computer, macchina virtuale linux, ubuntu virtuale" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Ottimizzare il supporto virtuale Linux su Azure

Creazione di una Linux macchine () è facile eseguire dalla riga di comando o dal portale. In questa esercitazione viene illustrato come assicurarsi che si dispone di configurarlo per ottimizzare le prestazioni della piattaforma Microsoft Azure. Questo argomento viene utilizzata una macchina virtuale Server Ubuntu, ma è anche possibile creare macchina virtuale Linux usando [le proprie immagini come modelli](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Prerequisiti

In questo argomento si presuppone che è già attiva una sottoscrizione di Azure ([abbonamento di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) [installato CLI Azure](../xplat-cli-install.md) e hanno già effettuato il provisioning una macchina virtuale in abbonamento Azure. Prima di eseguire qualsiasi con Azure - è necessario eseguire l'autenticazione all'abbonamento. Per eseguire questa operazione con CLI Azure, è sufficiente digitarne `azure login` per avviare il processo interattivo. 

## <a name="azure-os-disk"></a>Azure disco rigido del sistema operativo

Dopo aver creato una Linux Vm in Azure, ha due dischi è associati. /dev/sda è il disco rigido del sistema operativo, /dev/sdb è il disco temporaneo.  Non utilizzare il disco del sistema operativo principale (dev/sda) per tutte le operazioni eccetto il sistema operativo ed è ottimizzata per il tempo di avvio veloce macchine Virtuali garantisce prestazioni ottimali per i carichi di lavoro. Si desidera connettere la macchina virtuale per ottenere persistente uno o più dischi e ottimizzato per lo spazio di archiviazione per i dati. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Aggiunta di dischi per i siti di destinazione di dimensioni e prestazioni 

In base alle dimensioni macchine Virtuali, è possibile allegare fino a 16 dischi aggiuntivi in una serie, 32 dischi su una serie di D e lavorare 64 dischi su una serie G - ogni fino a 1 TB dimensioni. Aggiungere dischi aggiuntivi in base alle esigenze per le proprie esigenze IOps e spazio. Ogni disco presenta una destinazione prestazioni di 500 IOps per l'archiviazione Standard e a un massimo di 5000 IOps per disco per l'archiviazione Premium.  Per ulteriori informazioni sull'archiviazione Premium dischi, consultare [archiviazione Premium: High-Performance lo spazio di archiviazione per le macchine virtuali Azure](../storage/storage-premium-storage.md)

Per ottenere la massima IOps dischi Premium lo spazio di archiviazione impostate nel punto in cui le impostazioni della cache in "Sola lettura" o "Nessuno", è necessario disattivare "ostacoli" durante l'installazione nel file system in Linux. Non è necessaria ostacoli sono per tali impostazioni della cache in cui scrive dischi di archiviazione Premium eseguito il backup.

- Se si utilizza **reiserFS**, disattivare ostacoli utilizzando l'opzione di montaggio "barriera = Nessuno" (per l'attivazione ostacoli, utilizzare "barriera = svuotamento")
- Se si usa **ext3/ext4**, disattivare ostacoli utilizzando l'opzione di montaggio "barriera = 0" (per l'attivazione ostacoli, utilizzare "barriera = 1")
- Se si usa **XFS**, disattivare ostacoli utilizzando l'opzione di montaggio "nobarrier" (per abilitare gli ostacoli, utilizzare l'opzione "barriera")

## <a name="storage-account-considerations"></a>Considerazioni sull'Account di archiviazione

Quando si crea la VM Linux in Azure, assicurarsi che si allega dischi dagli account di archiviazione che si trovano nella stessa regione di una macchina virtuale per garantire prossimità e ridurre a icona la latenza di rete.  Ogni account di archiviazione Standard con un massimo di 20 k IOps e una capacità di 500 TB.  Si ottiene il per circa 40 dischi utilizzati in modo intensivo incluso il disco del sistema operativo e qualsiasi disco di dati creati. Per gli account di archiviazione Premium, senza alcun limite massimo IOps ma esiste un limite di dimensioni 32 TB. 

Quando gestire alte IOps carichi di lavoro e si è scelto lo spazio di archiviazione Standard per i dischi, potrebbe essere necessario dividere i dischi tra più account di archiviazione per assicurarsi che sia stato non raggiunto il limite di IOps 20.000 per gli account di archiviazione Standard. La macchina virtuale può contenere una combinazione di dischi da tra gli account di archiviazione diversi e tipi di account di archiviazione per ottenere la configurazione ottimale. 

## <a name="your-vm-temporary-drive"></a>Unità temporanea macchine Virtuali

Per impostazione predefinita quando si crea una macchina virtuale Azure offre un disco del sistema operativo (dev/sda) e un disco temporaneo (dev/sdb).  Tutti i dischi aggiuntivi che si aggiunge visualizzate come /dev/sdc, /dev/sdd, /dev/sde e così via. Tutti i dati sul disco temporaneo (dev/sdb) non è permanenti e possono essere persi se eventi specifici come macchine Virtuali il ridimensionamento, ridistribuzione, o manutenzione forza il riavvio della macchina virtuale.  Dimensioni e il tipo di disco temporaneo corrisponde alla dimensione memoria virtuale si è scelto in fase di distribuzione. In caso di una delle dimensioni premium macchine virtuali (delle serie DS, G e DS_V2) unità temporanea supportato da un SSD locale per prestazioni fino a 48 k IOps. 

## <a name="linux-swap-file"></a>File di scambio di Linux

Se la macchina virtuale Azure da un'immagine Ubuntu o CoreOS, è possibile utilizzare CustomData per inviare una configurazione cloud al cloud-inizializzazione. Se è stata [caricata un'immagine Linux personalizzata](virtual-machines-linux-upload-vhd.md) che utilizza cloud-inizializzazione, è inoltre configurare partizioni Scambia utilizzando cloud-inizializzazione.

Le immagini di Cloud Ubuntu, è necessario utilizzare cloud-inizializzazione per configurare la partizione Scambia. Vedere la pagina wiki Ubuntu per altri dettagli: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Per le immagini senza supporto cloud-inizializzazione, le immagini di macchine Virtuali distribuite da Azure Marketplace hanno un agente di Linux macchine Virtuali integrato con il sistema operativo. Questo agente consente la macchina virtuale interagire con i servizi di Azure diversi. Supponendo che è stata distribuita un'immagine standard di Azure Marketplace, è necessario eseguire le operazioni seguenti per configurare correttamente le impostazioni di Linux scambiare file:

Individuare e modificare sono presenti due voci nel file **/etc/waagent.conf** . È possibile controllare l'esistenza di un file di scambio dedicato e le dimensioni del file di scambio. I parametri desiderati per modificare sono `ResourceDisk.EnableSwap=N` e`ResourceDisk.SwapSizeMB=0` 

È necessario modificarli per le operazioni seguenti:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size in MB per soddisfare le esigenze} 

Dopo aver apportato le modifiche, sarà necessario riavviare il waagent o le VM Linux per applicare le modifiche.  Si conosce l'implementazione delle modifiche e un file di scambio è stato creato quando si utilizza il `free` comando per visualizzare lo spazio disponibile. Nell'esempio seguente con un file di scambio di 512MB creato come risultato della modifica del file waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo di pianificazione i/o per l'archiviazione Premium

Con la 2.6.18 kernel Linux, l'impostazione predefinita i/o pianificazione algoritmo è stato modificato da scadenza a CFQ (algoritmo di Accodamento completamente giusto). Per motivi dei / o accesso casuale, vi è differenza trascurabile in differenze di prestazioni tra CFQ e scadenza.  Per dischi basati su SSD nel punto in cui il modello dei / o disco è prevalentemente sequenza, passare Torna a algoritmo NOOP o scadenza possono ottenere prestazioni dei / o migliori.

### <a name="view-the-current-io-scheduler"></a>Visualizzare l'utilità di pianificazione i/o corrente

Usare il comando seguente:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Verrà visualizzata in seguito output, che indica l'utilità di pianificazione corrente.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Cambiare il dispositivo corrente (dev/sda) operazioni pianificazione algoritmo di

Usare i comandi seguenti:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Impostare questa proprietà per /dev/sda solo non è utile. Deve essere impostata su tutti i dischi di dati in cui i/o sequenziale domina motivo i/o.  

Verrà visualizzato il seguente output, che indica che grub.cfg è stata ricreata correttamente e che l'utilità di pianificazione predefinito è stato aggiornato a NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Per il gruppo di distribuzione Red Hat, è necessario solo il comando seguente:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilizzare RAID Software per ottenere maggiore si / operazioni

Se i carichi di lavoro richiede ulteriori IOps che può fornire un singolo disco, è necessario utilizzare una configurazione RAID software di più dischi. Dal momento Azure esegue già adattabilità disco livello tessuti locale, per ottenere il massimo livello di prestazioni da una configurazione striping RAID 0.  È necessario eseguire il provisioning e creare nuovi dischi nell'ambiente di Azure collegarli alle VM Linux prima partizione, la formattazione e le unità di montaggio.  Sono disponibili ulteriori informazioni sulla configurazione di una configurazione RAID software per la VM Linux in azure nel documento **[Configurazione RAID Software su Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Passaggi successivi

Tenere presente che con tutte le discussioni ottimizzazione, è necessario eseguire test prima e dopo ogni modifica apportata a misurare l'impatto che avrà la modifica.  L'ottimizzazione è un processo passo dopo passo che avrà risultati diversi in computer diversi nel proprio ambiente.  Ciò che funziona per una configurazione potrebbe non funzionare per consentire ad altri.

Collegamenti a risorse aggiuntive: 

- [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md)
- [Guida di Azure Linux agente utente](virtual-machines-linux-agent-user-guide.md)
- [Ottimizzazione delle prestazioni MySQL in macchine virtuali Linux Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurare il Software RAID su Linux](virtual-machines-linux-configure-raid.md)
