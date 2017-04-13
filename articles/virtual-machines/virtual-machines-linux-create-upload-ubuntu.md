<properties
    pageTitle="Creare e caricare un disco rigido virtuale Linux Ubuntu in Azure"
    description="Informazioni su come creare e caricare un Azure disco rigido virtuale (disco rigido virtuale) che contiene un sistema operativo Ubuntu Linux."
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
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparare una macchina virtuale Ubuntu per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Immagini di cloud Ubuntu ufficiale
A questo punto, Ubuntu pubblica ufficiale dischi rigidi virtuali Azure per il download in [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Se è necessario creare la propria immagine Ubuntu speciali per Azure, piuttosto che manuale alla procedura descritta di seguito è consigliabile iniziare con queste noti utilizzo dischi rigidi virtuali e personalizzare in base alle esigenze. Le più recenti immagine sempre sono disponibili nei seguenti percorsi:

 - Ubuntu 12.04/preciso: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/affidabile: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Prerequisiti di

In questo articolo si presuppone che già stato installato un sistema operativo Ubuntu Linux a un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione, ad esempio Hyper-V. Per ulteriori informazioni, vedere [installare il ruolo di Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione Ubuntu**

- Vedere anche [Le note di installazione Linux generali](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione Linux per Azure.
- Il formato VHDX non è supportato in Azure, solo **fissa disco rigido virtuale**.  È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet disco rigido virtuale Converti.
- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.
- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Linux configurabili per creare un file di scambio sul disco temporaneo di risorse.  Sono disponibili ulteriori informazioni nella procedura seguente.
- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.


## <a name="manual-steps"></a>Procedure manuali

> [AZURE.NOTE] Prima di creare un'immagine personalizzata di Ubuntu per Azure, è consigliabile utilizzare le immagini da [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) invece.


1. Nel riquadro centrale di Hyper-V Manager, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire la finestra per la macchina virtuale.

3.  Sostituire i repository correnti dell'immagine da usare pronti contro termine Azure del Ubuntu. La procedura variare leggermente a seconda della versione Ubuntu.

    Prima di modificare /etc/apt/sources.list, è consigliabile eseguire un backup:

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Le immagini di Ubuntu Azure sono ora seguendo kernel *Attivazione hardware* (HWE). Aggiornare il sistema operativo al più recente kernel eseguendo i comandi seguenti:

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modificare la riga di avvio kernel per Grub includere i parametri kernel aggiuntive per Azure. Per eseguire questa operazione Apri "/ e così via, predefinito/grub" in un editor di testo, trovare la variabile denominata `GRUB_CMDLINE_LINUX_DEFAULT` (o aggiungerla se necessario) e modificare in modo da includere i parametri seguenti:

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Salvare e chiudere il file "`sudo update-grub`'. In questo modo che tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di supporto tecnico Azure con il debug dei problemi.

6.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

7.  Installare l'agente Linux Azure:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Si noti che l'installazione del `walinuxagent` pacchetto comporta la rimozione di `NetworkManager` e `NetworkManager-gnome` pacchetti, se sono installati.

8.  Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Fare clic su **azione -> chiusura verso il basso** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.


## <a name="next-steps"></a>Passaggi successivi
A questo punto si è pronti a utilizzare il disco rigido virtuale Ubuntu Linux per creare nuove macchine virtuali in Azure. Se questa è la prima volta che si sta caricando file con estensione vhd in Azure, vedere i passaggi 2 e 3 nella [creazione e il caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Riferimenti ##

Kernel abilitazione (HWE) hardware Ubuntu:

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-Images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-Images-Now-using-Hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
