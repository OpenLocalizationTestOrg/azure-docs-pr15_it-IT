<properties
    pageTitle="Creare e caricare un disco rigido virtuale Linux SUSE in Azure"
    description="Informazioni su come creare e caricare un Azure disco rigido virtuale (disco rigido virtuale) che contiene un sistema operativo SUSE Linux."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparare una macchina virtuale SLES oppure openSUSE per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Prerequisiti di ##

In questo articolo si presuppone che si è già installato un SUSE oppure openSUSE sistema operativo Linux a un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione, ad esempio Hyper-V. Per ulteriori informazioni, vedere [installare il ruolo di Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / note sull'installazione openSUSE

- Vedere anche [Le note di installazione Linux generali](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione Linux per Azure.

- Il formato VHDX non è supportato in Azure, solo **fissa disco rigido virtuale**.  È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet disco rigido virtuale Converti.

- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.

- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Linux configurabili per creare un file di scambio sul disco temporaneo di risorse.  Sono disponibili ulteriori informazioni nella procedura seguente.

- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.


## <a name="use-suse-studio"></a>Utilizzare SUSE Studio
[Studio SUSE](http://www.susestudio.com) facilmente creare e gestire le immagini SLES e openSUSE per Azure e Hyper-V. Questo è l'approccio consigliato per la personalizzazione di immagini SLES e openSUSE.

In alternativa alla creazione di proprio disco rigido virtuale SUSE pubblica immagini BYOS (porta il proprio abbonamento) per SLES in [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparare SUSE Linux Enterprise Server 11 SP4 ##

1. Nel riquadro centrale di Hyper-V Manager, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire la finestra per la macchina virtuale.

3. Registrare il sistema di SUSE Linux Enterprise per consentirne il scaricare gli aggiornamenti e installare pacchetti.

4. Aggiornare il sistema con l'ultima patch:

        # sudo zypper update

5. Installare l'agente di Linux Azure dal repository dei SLES:

        # sudo zypper install WALinuxAgent

6. Controllare se waagent è impostato su "in" in chkconfig e in caso contrario, abilitarla per l'avvio automatico:
               
        # sudo chkconfig waagent on

7. Verificare se waagent servizio è in esecuzione e in caso contrario, avviarlo: 

        # sudo service waagent start
                
8. Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. Per eseguire questa operazione, aprire "/ boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In questo modo tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi.

9. Verificare che /boot/grub/menu.lst e /etc/fstab. entrambe riferimento disco tramite il relativo UUID (da uuid) invece che con l'ID di disco (da id). 

    Ottenere CD UUID
    
        # ls /dev/disk/by-uuid/

    Se /dev/disk/by-id / viene usato, aggiornare fstab /boot/grub/menu.lst e/o altri oggetti/con il valore corretto per uuid

    Prima della modifica
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Dopo la modifica
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modificare le regole di udev per evitare di generare statiche regole per le interfacce Ethernet. Queste regole possono provocare problemi se clonare una macchina virtuale in Microsoft Azure o Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. È consigliabile modificare il file "/ ecc/sysconfig/rete/dhcp" e modificare il `DHCLIENT_SET_HOSTNAME` parametro per le operazioni seguenti:

        DHCLIENT_SET_HOSTNAME="no"

12. In "o altri oggetti/sudoers", commento o rimuovere le righe seguenti, se presenti:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

14. Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Fare clic su **azione -> chiusura verso il basso** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.


----------

## <a name="prepare-opensuse-131"></a>Preparare openSUSE 13.1 + ##

1. Nel riquadro centrale di Hyper-V Manager, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire la finestra per la macchina virtuale.

3. Nella shell, eseguire il comando "`zypper lr`'. Se il comando restituisce output simile al seguente, quindi i repository sono configurati come previsto, ossia non sono necessarie rettifiche (si noti che i numeri di versione possono variare):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Se il comando non restituisce "Nessun archivi definiti..." utilizzare i comandi seguenti per aggiungere questi pronti contro termine:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    È quindi possibile verificare i repository sono stati aggiunti eseguendo il comando "`zypper lr`' nuovamente. In caso di uno degli archivi aggiornamenti appropriati non è attivato, abilitarla con il comando seguente:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Aggiornare il kernel all'ultima versione:

        # sudo zypper up kernel-default

    O aggiornare il sistema con tutte le nuove patch:

        # sudo zypper update

5.  Installare l'agente Linux Azure.

        # sudo zypper install WALinuxAgent

6.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    In questo modo tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Inoltre, rimuovere i parametri seguenti dalla riga di avvio kernel se presenti:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  È consigliabile modificare il file "/ ecc/sysconfig/rete/dhcp" e modificare il `DHCLIENT_SET_HOSTNAME` parametro per le operazioni seguenti:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importanti:** In "o altri oggetti/sudoers", commento o rimuovere le righe seguenti, se presenti:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

10. Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Assicurarsi che l'agente di Linux Azure viene eseguita all'avvio:

        # sudo systemctl enable waagent.service

13. Fare clic su **azione -> chiusura verso il basso** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.

## <a name="next-steps"></a>Passaggi successivi
A questo punto si è pronti a utilizzare il disco rigido virtuale SUSE Linux per creare nuove macchine virtuali in Azure. Se questa è la prima volta che si sta caricando file con estensione vhd in Azure, vedere i passaggi 2 e 3 nella [creazione e il caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
