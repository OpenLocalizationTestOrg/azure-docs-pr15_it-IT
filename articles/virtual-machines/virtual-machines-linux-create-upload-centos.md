<properties
    pageTitle="Creare e caricare un VHD Linux basate su CentOS in Azure"
    description="Informazioni su come creare e caricare un Azure disco rigido virtuale (disco rigido virtuale) che contiene un sistema operativo basato su CentOS Linux."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparare una macchina virtuale basata su CentOS per Azure

- [Preparare una macchina virtuale di CentOS 6. x per Azure](#centos-6x)
- [Preparare una macchina virtuale CentOS 7.0 + per Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Prerequisiti ##

In questo articolo si presuppone che sia stato già installato un CentOS (o derivato simile) sistema operativo Linux da un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione, ad esempio Hyper-V. Per ulteriori informazioni, vedere [installare il ruolo di Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).


**Note sull'installazione centOS**

- Vedere anche [Le note di installazione Linux generali](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione Linux per Azure.

- Il formato VHDX non è supportato in Azure, solo **fissa disco rigido virtuale**.  È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet disco rigido virtuale Converti.

- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi.  [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.

- NUMA non è supportata per dimensioni maggiori di macchine Virtuali a causa di un bug nelle versioni di kernel Linux sotto 2.6.37. Questo problema influisce principalmente distribuzioni utilizzando il padre kernel Red Hat 2.6.32. Installazione manuale dell'agente di Azure Linux (waagent) verrà automaticamente disabilitata NUMA nella configurazione GRUB kernel Linux. Sono disponibili ulteriori informazioni nella procedura seguente.

- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Linux configurabili per creare un file di scambio sul disco temporaneo di risorse.  Sono disponibili ulteriori informazioni nella procedura seguente.

- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.


## <a name="centos-6x"></a>CentOS 6. x ##

1. In Gestione di Hyper-V, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

3. Disinstallare NetworkManager eseguendo il comando seguente:

        # sudo rpm -e --nodeps NetworkManager

    **Nota:** Se non è già stato installato il pacchetto, questo comando non riesce con un messaggio di errore. È previsto.

4.  Creare un file denominato **rete** la `/etc/sysconfig/` directory che contiene il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Creare un file denominato **ifcfg eth0** nel `/etc/sysconfig/network-scripts/` directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificare le regole di udev per evitare di generare statiche regole per le interfacce Ethernet. Queste regole possono provocare problemi se clonare una macchina virtuale in Microsoft Azure o Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on


8. **Solo 6.3 centOS**: installare i driver per i servizi di integrazione Linux (elenco).

    **Importante: Il passaggio è valido per CentOS 6.3 e versioni precedenti.**  In CentOS 6.4 + i servizi di integrazione Linux sono *già disponibili in kernel standard*.

    - Seguire le istruzioni di installazione nella [pagina di download di elenco](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e installare RPM all'immagine.  


9. Installare il pacchetto di pyasn1 python eseguendo il comando seguente:

        # sudo yum install python-pyasn1

10. Se si desidera utilizzare i riflette OpenLogic contenuti nei Data Center di Azure, sostituire il file /etc/yum.repos.d/CentOS-Base.repo con archivi seguenti.  Inoltre, questo verrà aggiunto repository **[openlogic]** che include i pacchetti per l'agente di Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Nota:** Il resto di questa guida assumerà che si usa almeno repo [openlogic], che verrà utilizzato per installare l'agente di Azure Linux riportata di seguito.


11. Aggiungere la riga seguente /etc/yum.conf:

        http_caching=packages

    E **in CentOS 6.3 solo** aggiungere la riga seguente:

        exclude=kernel*

12. Disattivare il modulo slurp "fastestmirror" modificando il file "/ etc/yum/pluginconf.d/fastestmirror.conf" e in `[main]`, digitare quanto segue:

        set enabled=0

13. Eseguire il seguente comando per cancellare i metadati slurp corrente:

        # yum clean all

14. **In CentOS 6.3 solo**aggiornare kernel utilizzando il comando seguente:

        # sudo yum --disableexcludes=all install kernel

15. Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Verranno disattivate NUMA a causa di un errore nella versione kernel utilizzata da CentOS 6.

    Oltre a quanto sopra menzionato, è consigliabile *rimuovere* i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    Il `crashkernel` opzione potrebbe essere sinistra se lo si desidera configurare, ma nota che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128MB o più, che può essere un problema in formati di macchine Virtuali più piccoli.


16. Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

17. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent

    Si noti che l'installazione del pacchetto WALinuxAgent comporta la rimozione di NetworkManager e pacchetti NetworkManager gnome se non sono stati rimossi già come descritto nel passaggio 2.

18. Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Fare clic su **azione -> chiusura verso il basso** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Modifiche in CentOS 7 (e derivate simili)**

Preparazione di una macchina virtuale CentOS 7 di Azure è molto simile a 6 CentOS, esistono tuttavia alcune differenze importanti notare:

 - Il pacchetto di NetworkManager non è più in conflitto con l'agente di Azure Linux. Viene installato per impostazione predefinita ed è consigliabile non viene rimosso.
 - GRUB2 a questo punto viene utilizzato come caricatore di avvio predefinita in modo che la procedura per la modifica di parametri del kernel è cambiata (vedere di seguito).
 - XFS è ora il sistema di file predefinito. Il file system ext4 ancora utilizzabili se lo si desidera.


**Passaggi di configurazione**

1. In Gestione di Hyper-V, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

3.  Creare un file denominato **rete** la `/etc/sysconfig/` directory che contiene il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Creare un file denominato **ifcfg eth0** nel `/etc/sysconfig/network-scripts/` directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificare le regole di udev per evitare di generare statiche regole per le interfacce Ethernet. Queste regole possono provocare problemi se clonare una macchina virtuale in Microsoft Azure o Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

7. Installare il pacchetto di pyasn1 python eseguendo il comando seguente:

        # sudo yum install python-pyasn1

8. Se si desidera utilizzare i riflette OpenLogic contenuti nei Data Center di Azure, sostituire il file /etc/yum.repos.d/CentOS-Base.repo con archivi seguenti.  Inoltre, questo verrà aggiunto repository **[openlogic]** che include i pacchetti per l'agente di Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Nota:** Il resto di questa guida assumerà che si usa almeno repo [openlogic], che verrà utilizzato per installare l'agente di Azure Linux riportata di seguito.

9.  Eseguire il seguente comando per cancellare i metadati slurp correnti e installare gli aggiornamenti:

        # sudo yum clean all
        # sudo yum -y update

10. Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ e così via, predefinito/grub" in un editor di testo e modificare il `GRUB_CMDLINE_LINUX` parametro, ad esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Anche disattivate nuove convenzioni di denominazione CentOS 7 per schede di rete. Oltre a quanto sopra menzionato, è consigliabile *rimuovere* i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    Il `crashkernel` opzione potrebbe essere sinistra se lo si desidera configurare, ma nota che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128MB o più, che può essere un problema in formati di macchine Virtuali più piccoli.

11. Dopo aver modifica "/ e così via, predefinito/grub" per sopra, eseguire il seguente comando per ricreare la configurazione di grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Verificare che il server SSH sia installato e configurato per l'avvio all'avvio.  Si tratta in genere l'impostazione predefinita.

13. **Solo se la creazione dell'immagine da VMWare, VirtualBox o KVM:** Aggiungere i moduli di Hyper-V in initramfs:

    Modificare `/etc/dracut.conf`, aggiungere contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Rigenerare il initramfs:

        # dracut –f -v

14. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio scambiare utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Fare clic su **azione -> chiusura verso il basso** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.

## <a name="next-steps"></a>Passaggi successivi
A questo punto si è pronti a utilizzare il disco rigido virtuale Linux CentOS per creare nuove macchine virtuali in Azure. Se questa è la prima volta che si sta caricando file con estensione vhd in Azure, vedere i passaggi 2 e 3 nella [creazione e il caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
