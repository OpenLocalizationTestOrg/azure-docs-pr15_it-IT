<properties
    pageTitle="Creare e caricare un VHD Linux in Azure"
    description="Informazioni su come creare e caricare un Azure disco rigido virtuale (disco rigido virtuale) che contiene un sistema operativo Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Informazioni per le distribuzioni Non approvato #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: contratto di servizio di Azure la piattaforma si applica a macchine virtuali in esecuzione il sistema operativo Linux solo quando viene utilizzato uno dei [approvato distribuzioni](virtual-machines-linux-endorsed-distros.md) . Tutte le distribuzioni di Linux illustrate nella raccolta di immagini Azure sono avallate distribuzioni con la configurazione necessaria.

- [Linux su Azure - approvato distribuzioni](virtual-machines-linux-endorsed-distros.md)
- [Supporto per le immagini di Linux in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Tutte le distribuzioni in esecuzione in Azure saranno necessario soddisfare alcuni prerequisiti per poter eseguire correttamente sulla piattaforma.  In questo articolo non è esaustivo in ogni distribuzione è diversa; è molto probabile che anche se è necessario soddisfare i criteri seguenti sarà comunque necessario modificare in modo significativo il sistema Linux per garantire che venga eseguita correttamente sulla piattaforma.

È per questo motivo è consigliabile iniziare con uno dei nostro [Linux su Azure approvato distribuzioni](virtual-machines-linux-endorsed-distros.md) laddove possibile. Gli articoli seguenti consentirà come preparare le varie distribuzioni Linux avallate supportate in Azure:

- **[Distribuzioni basate su centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Il resto di questo articolo descrive come indicazioni generali per l'esecuzione della distribuzione Linux su Azure.


## <a name="general-linux-installation-notes"></a>Note sull'installazione Linux generale ##

- Il formato VHDX non è supportato in Azure, solo **fissa disco rigido virtuale**.  È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet disco rigido virtuale Converti. Se si utilizza VirtualBox significa che la selezione di **dimensioni fisse** anziché il valore predefinito in modo dinamico allocato durante la creazione del disco.

- Quando si installa il sistema Linux è *consigliabile* utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo mai deve essere collegato a un'altra macchine Virtuali identici per la risoluzione dei problemi. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi di dati.

- È necessario un supporto kernel per il montaggio sistemi file utente. Al primo avvio in Azure la configurazione di provisioning viene passata per VM Linux tramite media formattati come utente che è collegato alle guest. Agente di Azure Linux deve essere in grado di installare il file system utente per leggere la configurazione e la macchina virtuale di provisioning.

- Versioni di kernel Linux sotto 2.6.37 non supportano NUMA su Hyper-V con dimensioni maggiori di macchine Virtuali. Questo problema principalmente impatto di distribuzioni precedenti utilizzando il padre kernel Red Hat 2.6.32 ed è stato risolto nel RHEL 6.6 (504 di 2.6.32 kernel). Sistemi con personalizzati vengono antecedenti 2.6.37 o. x in base RHEL precedenti rispetto a 2.6.32-504 necessario impostare il parametro di avvio `numa=off` sul kernel della riga di comando in grub.conf. Per ulteriori informazioni vedere Red Hat [436883 KB](https://access.redhat.com/solutions/436883).

- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Linux configurabili per creare un file di scambio sul disco temporaneo di risorse.  Sono disponibili ulteriori informazioni nella procedura seguente.

- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.


### <a name="installing-linux-without-hyper-v"></a>L'installazione di Linux senza Hyper-V ###

In alcuni casi, i programmi di installazione di Linux potrebbe non includere i driver per Hyper-V iniziale su disco RAM (initrd o initramfs) a meno che non vengono rilevati che sia in esecuzione un ambiente di Hyper-V.  Quando si usa un sistema di virtualizzazione diverso (ad esempio Virtualbox, KVM, ecc.) per preparare l'immagine di Linux, potrebbe essere necessario ricostruire initrd per garantire che almeno il `hv_vmbus` e `hv_storvsc` moduli kernel sono disponibili nel disco di RAM iniziale.  Si tratta di almeno un problema noto nei sistemi in base alla distribuzione Red Hat padre.

Meccanismo per ricostruire l'immagine initrd o initramfs può variare a seconda della distribuzione. Consultare la documentazione della distribuzione o di supporto per la procedura appropriata.  Ecco un esempio per la procedura ricreare il initrd utilizzando il `mkinitrd` utility:

Per eseguire il backup immagine initrd esistente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Successivamente, ricostruire initrd con la `hv_vmbus` e `hv_storvsc` moduli kernel:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Ridimensionamento di dischi rigidi virtuali ###

Immagini disco rigido virtuale in Azure devono avere una dimensione virtuale allineati rispetto a 1MB.  In genere, dischi rigidi virtuali creati con Hyper-V devono già essere allineati correttamente.  Se il disco rigido virtuale non è allineato correttamente si potrebbe ricevere un messaggio di errore analogo al seguente quando si tenta di creare un' *immagine* dal disco rigido virtuale:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Per risolvere questo problema è possibile ridimensionare la macchina virtuale tramite la console di gestione di Hyper-V o il cmdlet di Powershell [Disco rigido virtuale di ridimensionamento](http://technet.microsoft.com/library/hh848535.aspx) .  Se non è in esecuzione in un ambiente di Windows è consigliabile usare qemu img convertire, se necessario, e ridimensionare il disco rigido virtuale.

> [AZURE.NOTE] Esiste un errore noto nelle versioni qemu img > = 2.2.1 che genera un disco rigido virtuale formattato in modo errato. Il problema è stato risolto QEMU 2.6. Si consiglia di utilizzare qemu-img 2.2.0 o inferiore o aggiornare a 2.6 o versione successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Ridimensionare il disco rigido virtuale direttamente utilizzare strumenti come `qemu-img` o `vbox-manage` potrebbe causare un disco rigido virtuale avvio.  Pertanto è consigliabile prima Converti il disco rigido virtuale a un'immagine disco non FORMATTATO.  Se è già stato creato l'immagine di macchine Virtuali come immagine disco non FORMATTATO (impostazione predefinita per alcuni hypervisor, ad esempio KVM) è possibile ignorare questo passaggio:

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcolare le dimensioni dell'immagine disco per garantire che le dimensioni virtuali sono allineata a 1MB necessari.  Il seguente script di shell bash possono essere utili per questa operazione.  Lo script utilizza "`qemu-img info`" per determinare le dimensioni dell'immagine disco virtuale e quindi viene calcolata la dimensione a 1MB successiva:

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Ridimensionare il disco non elaborato usando rounded_size $ come set di script precedente:

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. A questo punto, convertire il disco non ELABORATO in un disco rigido virtuale dimensioni fisse:

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Requisiti di Kernel Linux ##

I driver di servizi di integrazione Linux (elenco) per Hyper-V e Azure vengono forniti direttamente al kernel Linux padre. Molte distribuzioni che includono una versione di kernel Linux recente (ad esempio 3. x) saranno già questi driver disponibili o fornire in caso contrario backported versioni di questi driver loro vengono.  Questi driver vengono costantemente vengano aggiornati nel kernel padre con nuovi aggiornamenti e sulle funzionalità, laddove possibile è consigliabile eseguire una [distribuzione approvato](virtual-machines-linux-endorsed-distros.md) contenenti le correzioni e gli aggiornamenti.

Se si esegue una variante di Red Hat Enterprise Linux versioni **6.0 6.3**, sarà necessario installare i driver più recenti di elenco per Hyper-V. I driver sono disponibili [in questa posizione](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). RHEL **6.4 +** (e derivate) i driver di elenco sono già inclusi in kernel e pertanto non pacchetti di installazione aggiuntive sono necessarie per creare sistemi in Azure.

Se è necessario un kernel personalizzato, è consigliabile usare una versione più recente di kernel (ad esempio **3.8 +**). Per le distribuzioni o fornitori che gestiscono le proprie kernel, alcune attività saranno necessarie per regolarmente backport i driver di elenco da kernel padre per il kernel personalizzato.  Anche se sono già in esecuzione una versione kernel relativamente recente, è consigliabile tenere traccia di tutte le correzioni padre il driver di elenco e backport quelle in base alle esigenze. Il percorso del file di origine di elenco driver è disponibile nel file [provvedono](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) nella struttura di origine kernel Linux:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Diagnostiche, assenza di seguenti patch è noto che si verificano in Azure e pertanto questi deve essere incluso nel kernel. In questo elenco è non completo o completo per tutte le distribuzioni:

- [ata_piix: rinviare dischi per i driver di Hyper-V per impostazione predefinita](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Account per i pacchetti in transito nel percorso Ripristina](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: evitare l'utilizzo della WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: disattivare scrivere stesso per RAID e driver di schede host virtuale](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: puntatore NULL risolvere fix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: bloccare i/o potrebbero causare errori di buffer ad anello](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs: proteggere doppie esecuzione di __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>Agente di Linux Azure ##

[Azure Linux agente](virtual-machines-linux-agent-user-guide.md) (waagent) è necessario eseguire il provisioning in modo corretto una macchina virtuale Linux in Azure. È possibile ottenere la versione più recente, i problemi relativi ai file o inviare richieste pull [repo GitHub agente Linux](https://github.com/Azure/WALinuxAgent).

- Agente di Linux viene rilasciato in base alla licenza Apache 2.0. Distribuzioni molti forniscono già RPM o deb pacchetti per l'agente e pertanto in alcuni casi questo può essere installato e aggiornato con facilmente.

- L'agente di Linux Azure richiede Python v 2.6 +.

- L'agente richiede anche il modulo pyasn1 python. La maggior parte delle distribuzioni forniscono questo come un pacchetto distinto che può essere installato.

- In alcuni casi l'agente di Linux Azure potrebbe non essere compatibile con NetworkManager. Molti dei pacchetti RPM/Deb forniti da distribuzioni configurare NetworkManager come un conflitto per il pacchetto di waagent e pertanto verrà disinstallato NetworkManager quando si installa il pacchetto dell'agente Linux.


## <a name="general-linux-system-requirements"></a>Requisiti di sistema Linux generale ##

- Modificare la riga di avvio kernel GRUB o GRUB2 per includere i parametri seguenti. In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi:

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi.

    Oltre a quanto sopra menzionato, è consigliabile *rimuovere* i parametri seguenti se presenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    Il `crashkernel` opzione potrebbe essere sinistra se lo si desidera configurare, ma nota che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128MB o più, che può essere un problema in formati di macchine Virtuali più piccoli.

- Installazione dell'agente Linux Azure

    L'agente di Linux Azure è necessario per il provisioning di un'immagine di Linux su Azure.  Distribuzioni molti forniscono l'agente come un pacchetto RPM o Deb (il pacchetto in genere è denominato 'WALinuxAgent' o 'walinuxagent').  L'agente può essere installato anche manualmente seguendo i passaggi indicati nella [Guida agente Linux](virtual-machines-linux-agent-user-guide.md).

- Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

- Non creare Scambia spazio su disco del sistema operativo

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Come ultimo passaggio, eseguire i comandi seguenti per eseguire la macchina virtuale:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] In Virtualbox venga visualizzato l'errore seguente dopo l'esecuzione ' waagent-forzare - annullare l'implementazione ': `[Errno 5] Input/output error`. Questo messaggio di errore non è critico e può essere ignorato.

- Sarà quindi necessario arrestare la macchina virtuale e caricare il disco rigido virtuale in Azure.
