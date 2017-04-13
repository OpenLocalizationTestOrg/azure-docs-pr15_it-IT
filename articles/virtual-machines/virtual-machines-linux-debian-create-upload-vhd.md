<properties
    pageTitle="Preparare il disco rigido virtuale Linux Debian | Microsoft Azure"
    description="Informazioni su come creare Debian 7 e 8 file disco rigido virtuale per la distribuzione di Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Preparare un disco rigido virtuale Debian per Azure

## <a name="prerequisites"></a>Prerequisiti
In questa sezione presuppone che è già stato installato un sistema operativo Linux Debian da un file ISO scaricato dal [sito Web Debian](https://www.debian.org/distrib/) a un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd; Hyper-V è solo un esempio. Per istruzioni sull'utilizzo di Hyper-V, vedere [installare il ruolo di Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Note sull'installazione

- Vedere anche [Le note di installazione Linux generali](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione Linux per Azure.
- Nel nuovo formato VHDX non è supportato in Azure. È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet **disco rigido virtuale Converti** .
- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.
- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Azure Linux configurabili per creare un file di scambio sul disco temporaneo di risorse. Sono disponibili ulteriori informazioni nella procedura seguente.
- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Consente di gestire Azure per creare Debian dischi rigidi virtuali

Sono disponibili strumenti per la generazione Debian dischi rigidi virtuali per Azure, ad esempio il [azure-gestire](https://github.com/credativ/azure-manage) script da [credativ](http://www.credativ.com/). Questo è consigliabile rispetto alla creazione di un'immagine da zero. Ad esempio, per creare un disco rigido virtuale 8 Debian eseguire i comandi seguenti per scaricare azure gestire (e le dipendenze) ed eseguire lo script azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparare manualmente un disco rigido virtuale Debian

1. In Gestione di Hyper-V, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

3. Commento la riga per **CD-ROM deb** in `/etc/apt/source.list` se si configura la macchina virtuale rispetto a un file ISO.

4. Modificare il `/etc/default/grub` file e modificare il parametro **GRUB_CMDLINE_LINUX** come indicato di seguito per includere i parametri kernel aggiuntive per Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Rigenerare il grub ed eseguire:

        # sudo update-grub

6. Aggiungere archivi Azure del Debian /etc/apt/sources.list per Debian 7 o 8:

    **7. x Debian "Wheezy"**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **8 Debian "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Installare l'agente Linux Azure:

        # sudo apt-get update
        # sudo apt-get install waagent

8. Per Debian 7, è necessario eseguire kernel basate su 3.16 dal repository wheezy backports. Prima di tutto creare un file denominato /etc/apt/preferences.d/linux.pref con il contenuto seguente:

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    Quindi eseguire "sudo installa apt get. linux-immagine-amd64" per installare il nuovo kernel.

8. Eseguire la macchina virtuale e prepararli per il provisioning su Azure ed eseguire:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Fare clic su **azione** -> chiusura verso il basso nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.


## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti a utilizzare il disco rigido virtuale Debian per creare nuove macchine virtuali in Azure. Se questa è la prima volta che si sta caricando file con estensione vhd in Azure, vedere i passaggi 2 e 3 nella [creazione e il caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
