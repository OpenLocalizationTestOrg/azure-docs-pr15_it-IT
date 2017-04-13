<properties
    pageTitle="Creare e caricare un Enterprise Hat rosso Linux disco rigido virtuale per l'utilizzo in Azure"
    description="Informazioni su come creare e caricare un Azure disco rigido virtuale (disco rigido virtuale) che contiene un sistema operativo Red Hat Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparare una macchina virtuale basate su Red Hat per Azure

In questo articolo si imparerà a preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'utilizzo in Azure. Versioni di RHEL coperti in questo articolo sono 6,7, 7.1 e 7.2. Hypervisor per la preparazione che rientrano in questo articolo sono Hyper-V, basata sul Kernel macchina virtuale (KVM) e VMware. Per ulteriori informazioni sui requisiti di idoneità per la partecipazione l'accesso Cloud programma Red Hat, vedere [sito Web di accesso Cloud Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [RHEL in esecuzione in Azure](https://access.redhat.com/articles/1989673).

[Preparare una macchina virtuale RHEL 6,7 dalla gestione di Hyper-V](#rhel67hyperv)

[Preparare una macchina virtuale RHEL 7.1/7.2 dalla gestione di Hyper-V](#rhel7xhyperv)

[Preparare una macchina virtuale RHEL 6,7 da KVM](#rhel67kvm)

[Preparare una macchina virtuale RHEL 7.1/7.2 da KVM](#rhel7xkvm)

[Preparare una macchina virtuale RHEL 6,7 da VMware](#rhel67vmware)

[Preparare una macchina virtuale RHEL 7.1/7.2 da VMware](#rhel7xvmware)

[Preparare una macchina virtuale RHEL 7.1/7.2 da un file di sviluppare un](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale basate su Red Hat dalla gestione di Hyper-V
### <a name="prerequisites"></a>Prerequisiti
In questa sezione presuppone che già stato installato un'immagine RHEL (da un file ISO ottenuto dal sito Web di Red Hat) a un disco rigido virtuale (disco rigido virtuale). Per ulteriori informazioni dettagliate sulla gestione di Hyper-V consente di installare un'immagine del sistema operativo, vedere [installare il ruolo di Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di RHEL**

- Vedere anche [Le note di installazione Linux generali](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione Linux per Azure.

- Nel nuovo formato VHDX non è supportato in Azure. È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet di PowerShell **disco rigido virtuale Converti** .

- Dischi rigidi virtuali devono essere creati come "fissi" - dinamici dischi rigidi virtuali non sono supportati.

- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. LVM o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.

- Non si configura una partizione di scambio su disco del sistema operativo. È possibile configurare l'agente di Linux per creare un file di scambio sul disco temporaneo di risorse. Altre informazioni sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.

- Quando si utilizza **qemu img** convertire immagini disco in formato disco rigido virtuale, si noti che esiste un errore noto nelle versioni qemu img 2.2.1 o versione successiva. Questo errore genera un disco rigido virtuale formattato in modo errato. Il problema viene risolto in una versione futura degli qemu img. Per ora, è consigliabile usare qemu-img versione 2.2.0 o versioni precedenti.

### <a id="rhel67hyperv"> </a>Preparare una macchina virtuale RHEL 6,7 dalla gestione di Hyper-V###


1.  In Gestione di Hyper-V, selezionare la macchina virtuale.

2.  Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

3.  Disinstallare NetworkManager eseguendo il comando seguente:

        # sudo rpm -e --nodeps NetworkManager

    Si noti che se non è già stato installato il pacchetto, questo comando non viene eseguito con un messaggio di errore. È previsto.

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

6.  Spostare o rimuovere udev regole per evitare di generare statiche regole per l'interfaccia Ethernet. Queste regole causano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

8.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository RHEL eseguendo il comando seguente:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire `/boot/grub/menu.lst` in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Verranno disattivate NUMA a causa di un errore nella versione kernel utilizzato da RHEL 6.

    Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    L'opzione crashkernel può essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

11. Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita. Modificare /etc/ssh/sshd_config per includere la riga seguente:

        ClientAliveInterval 180

12. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Si noti che l'installazione del pacchetto WALinuxAgent comporta la rimozione di NetworkManager e pacchetti NetworkManager gnome se non sono stati rimossi già come descritto nel passaggio 2.

13. Non creare Scambia spazio su disco del sistema operativo.
L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Annullare la registrazione della sottoscrizione, se necessario, eseguendo il comando seguente:

        # sudo subscription-manager unregister

15. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Fare clic su **azione > arrestare** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.
 

### <a id="rhel7xhyperv"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 dalla gestione di Hyper-V###

1.  In Gestione di Hyper-V, selezionare la macchina virtuale.

2.  Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

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

5.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

6.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository dei RHEL eseguendo il comando seguente:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB_CMDLINE_LINUX** . Per esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.
    L'opzione crashkernel può essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

8.  Dopo aver modifica `/etc/default/grub`, eseguire il seguente comando per ricreare la configurazione di grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per includere la riga seguente:

        ClientAliveInterval 180

10. Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. Non creare Scambia spazio su disco del sistema operativo. L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare i parametri seguenti nel `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Se si desidera annullare la sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

14. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Fare clic su **azione > arrestare** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparare una macchina virtuale basate su Red Hat da KVM

### <a id="rhel67kvm"> </a>Preparare una macchina virtuale RHEL 6,7 da KVM###


1.  Scaricare l'immagine KVM del 6,7 RHEL dal sito Web di Red Hat.

2.  Impostare una password principale.

    Generare una password crittografata e copiare l'output del comando:

        # openssl passwd -1 changeme

    Impostare una password radice con guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modificare il secondo campo dell'utente root da "!" per la password crittografata.

3.  Creare una macchina virtuale KVM dall'immagine qcow2, impostare il tipo di disco **qcow2**e impostare il modello di dispositivo interfaccia virtuali di **virtio**. Quindi avviare il computer virtuale e accedere come radice.

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

6.  Spostare o rimuovere le regole di udev per evitare di generare statiche regole per l'interfaccia Ethernet. Queste regole causano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-v:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # chkconfig network on

8.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository RHEL eseguendo il comando seguente:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire `/boot/grub/menu.lst` in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Verranno disattivate NUMA a causa di un errore nella versione kernel utilizzato da RHEL 6.

    Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.
    L'opzione crashkernel potrebbe essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

10. Aggiungere i moduli di Hyper-V in initramfs:  

    Modificare `/etc/dracut.conf` e aggiungere contenuto: add_drivers + = "hv_vmbus hv_netvsc hv_storvsc"

    Rigenerare initramfs: # dracut – f - v

11. Disinstallare cloud-inizializzazione:

        # yum remove cloud-init

12. Verificare che il server SSH sia installato e configurato per l'avvio al momento dell'avvio:

        # chkconfig sshd on

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Riavviare sshd:

        # service sshd restart

13. Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # yum install WALinuxAgent
        # chkconfig waagent on

15. L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in **/etc/waagent.conf** :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annullare la registrazione della sottoscrizione, se necessario, eseguendo il comando seguente:

        # subscription-manager unregister

17. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Arrestare la macchina virtuale KVM.

19. Convertire l'immagine qcow2 in formato disco rigido virtuale.
    Innanzitutto convertire l'immagine in formato non elaborato:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Assicurarsi che le dimensioni dell'immagine non elaborato sia allineata con 1 MB. In caso contrario, arrotondare le dimensioni in modo da 1 MB: # MB = $((1024*1024)) dimensioni # = $(qemu img info -f non elaborato - output json "rhel-6.7.raw" | \ gawk ' corrispondono ($0, / "virtuale dimensioni": ([0-9] +), /, val) {stampare val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convertire il disco non elaborato in un disco rigido virtuale spaziatura fissa:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da KVM###


1.  Scaricare l'immagine KVM di RHEL 7.1 (o 7.2) dal sito Web Red Hat. 7.1 RHEL verrà usata come nell'esempio qui.

2.  Impostare una password principale.

    Generare una password crittografata e copiare l'output del comando:

        # openssl passwd -1 changeme

    Impostare una password radice con guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modificare il secondo campo utente radice "!" per la password crittografata.

3.  Creare una macchina virtuale KVM dall'immagine qcow2, impostare il tipo di disco **qcow2**e impostare il modello di dispositivo interfaccia virtuali di **virtio**. Quindi avviare il computer virtuale e accedere come radice.

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

6.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # chkconfig network on

7.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository RHEL eseguendo il comando seguente:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB_CMDLINE_LINUX** . Per esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.
    L'opzione crashkernel può essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

9.  Dopo aver modifica `/etc/default/grub`, eseguire il seguente comando per ricreare la configurazione di grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Aggiungere i moduli di Hyper-V in initramfs:

    Modificare `/etc/dracut.conf` e aggiungere contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Ricreare initramfs:

        # dracut –f -v

11. Disinstallare cloud-inizializzazione:

        # yum remove cloud-init

12. Verificare che il server SSH sia installato e configurato per l'avvio al momento dell'avvio:

        # systemctl enable sshd

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Riavviare sshd:

        systemctl restart sshd

13. Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # yum install WALinuxAgent

    Abilitare il servizio waagent:

        # systemctl enable waagent.service

15. Non creare Scambia spazio su disco del sistema operativo. L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare i parametri seguenti nel `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annullare la registrazione della sottoscrizione, se necessario, eseguendo il comando seguente:

        # subscription-manager unregister

17. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Arrestare la macchina virtuale KVM.

19. Convertire l'immagine qcow2 in formato disco rigido virtuale.

    Innanzitutto convertire l'immagine in formato non elaborato:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Assicurarsi che le dimensioni dell'immagine non elaborato sia allineata con 1 MB. Arrotondare in caso contrario, le dimensioni in modo da 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convertire il disco non elaborato in un disco rigido virtuale spaziatura fissa:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparare una macchina virtuale basate su Red Hat da VMware
### <a name="prerequisites"></a>Prerequisiti
In questa sezione presuppone che sia stato già installato una macchina virtuale RHEL in VMware. Per informazioni dettagliate su come installare un sistema operativo in VMware, vedere [Guida all'installazione di sistema operativo Guest VMware](http://partnerweb.vmware.com/GOSIG/home.html).

- Quando si installa il sistema operativo Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. LVM o RAID può essere usata su dischi dati se si preferisce.

- Non si configura una partizione di scambio su disco del sistema operativo. È possibile configurare l'agente di Linux per creare un file di scambio sul disco temporaneo di risorse. È possibile trovare altre informazioni nei passaggi seguenti.

- Quando si crea il disco rigido virtuale, selezionare **archivio disco virtuale in un unico file**.



### <a id="rhel67vmware"> </a>Preparare una macchina virtuale RHEL 6,7 da VMware###

1.  Disinstallare NetworkManager eseguendo il comando seguente:

         # sudo rpm -e --nodeps NetworkManager

    Si noti che se non è già stato installato il pacchetto, questo comando non viene eseguito con un messaggio di errore. È previsto.

2.  Creare un file denominato **rete** nella directory /etc/sysconfig/che contiene il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Creare un file denominato **ifcfg eth0** nel /etc/sysconfig/network-scripts / directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Spostare o rimuovere le regole di udev per evitare di generare statiche regole per l'interfaccia Ethernet. Queste regole causano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

6.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository RHEL eseguendo il comando seguente:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Verranno disattivate NUMA a causa di un errore nella versione kernel utilizzato da RHEL 6.
    Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.
    L'opzione crashkernel può essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

9.  Aggiungere i moduli di Hyper-V in initramfs:

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per includere la riga seguente:

        ClientAliveInterval 180

11. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. Non creare Scambia spazio su disco del sistema operativo:

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare i parametri seguenti nel `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Annullare la registrazione della sottoscrizione, se necessario, eseguendo il comando seguente:

        # sudo subscription-manager unregister

14. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Arrestare la macchina virtuale e convertire il file VMDK in un file con estensione vhd.

    Innanzitutto convertire l'immagine in formato non elaborato:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Assicurarsi che le dimensioni dell'immagine non elaborato sia allineata con 1 MB. Arrotondare in caso contrario, le dimensioni in modo da 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convertire il disco non elaborato in un disco rigido virtuale spaziatura fissa:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da VMware###

1.  Creare un file denominato **rete** nella directory /etc/sysconfig/che contiene il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Creare un file denominato **ifcfg eth0** nel /etc/sysconfig/network-scripts / directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

4.  Registrare il proprio abbonamento Red Hat per abilitare l'installazione dei pacchetti dal repository RHEL eseguendo il comando seguente:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro **GRUB_CMDLINE_LINUX** . Per esempio:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Oltre l'azione precedente, è consigliabile rimuovere i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.
    L'opzione crashkernel può essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi nel ridurre le dimensioni dei macchine Virtuali.

6.  Dopo aver modifica `/etc/default/grub`, eseguire il seguente comando per ricreare la configurazione di grub:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Aggiungere i moduli di Hyper-V in initramfs:

    Modificare `/etc/dracut.conf`, aggiungere contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Ricreare initramfs:

        # dracut –f -v

8.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per includere la riga seguente:

        ClientAliveInterval 180

9.  Il pacchetto di WALinuxAgent `WALinuxAgent-<version>` è stato inserito all'archivio di extra Red Hat. Abilitare l'archivio extra eseguendo il comando seguente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Installare l'agente di Linux Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. Non creare Scambia spazio su disco del sistema operativo. L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo la macchina virtuale viene eseguito il provisioning su Azure. Si noti che il disco di risorse locale è un disco temporaneo e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare i parametri seguenti nel `/etc/waagent.conf` in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Se si desidera annullare la sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

13. Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Arrestare la macchina virtuale e convertire il file VMDK formato disco rigido virtuale.

    Innanzitutto convertire l'immagine in formato non elaborato:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Assicurarsi che le dimensioni dell'immagine non elaborato sia allineata con 1 MB. Arrotondare in caso contrario, le dimensioni in modo da 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convertire il disco non elaborato in un disco rigido virtuale spaziatura fissa:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparare una macchina virtuale basate su Red Hat da una ISO tramite un file di sviluppare un automaticamente


### <a id="rhel7xkickstart"> </a>Preparare una macchina virtuale RHEL 7.1/7.2 da un file di sviluppare un###


1.  Creare un file di sviluppare un con il contenuto riportata di seguito e salvare il file. Per informazioni dettagliate sull'installazione di sviluppare un, vedere la [Guida all'installazione di sviluppare un](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Posizionare lo sviluppare un file in una posizione accessibile dal sistema di installazione.

3.  In Gestione di Hyper-V, creare una nuova macchina virtuale. Nella pagina **connessione disco rigido virtuale** selezionare **Connetti un disco rigido virtuale in un secondo momento**e completare la creazione guidata macchina virtuale.

4.  Aprire le impostazioni di macchine Virtuali:

    un.  Allegare un nuovo disco rigido virtuale a macchina virtuale. Assicurarsi di selezionare **Dimensioni fisse**e **Formato disco rigido virtuale** .

    b.  Collegare l'installazione ISO nell'unità DVD.

    c.  Impostare il BIOS per l'avvio da CD.

5.  Avviare la macchina virtuale. Quando viene visualizzata la Guida all'installazione, premere **Tab** per configurare le opzioni di avvio.

6.  Invio `inst.ks=<the location of the kickstart file>` alla fine di opzioni di avvio, quindi premere **INVIO**.

7.  Attendere il completamento dell'installazione. Al termine, la macchina virtuale verrà chiuso automaticamente. Il VHD Linux è ora pronto per essere caricato in Azure.

## <a name="known-issues"></a>Problemi noti
Esistono problemi noti quando si usa 7.1 RHEL Hyper-V e Azure.

### <a name="disk-io-freeze"></a>Bloccare i/o su disco

Questo problema può verificarsi durante le attività dei / o disco di archiviazione frequenti con RHEL 7.1 in Hyper-V e Azure.   

Tasso di riprodurre il problema:

Questo problema è intermittente. Tuttavia, si verifica più frequente durante frequenti operazioni dei / o del disco in Hyper-V e Azure.   


[AZURE.NOTE] Questo problema noto è già stato risolto con Red Hat. Per installare le correzioni associate, eseguire il comando seguente:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Il driver di Hyper-V potrebbe non inclusa nel disco di RAM iniziale quando si utilizza un hypervisor Hyper-V

In alcuni casi, i programmi di installazione di Linux potrebbe non includere i driver per Hyper-V nel disco di RAM iniziale (initrd o initramfs) a meno che non vengono rilevati che sia in esecuzione in un ambiente di Hyper-V.

Quando si usa un sistema di virtualizzazione diverso (ad esempio Virtualbox, Xen ecc.) per preparare l'immagine di Linux, potrebbe essere necessario ricostruire initrd per garantire che almeno i moduli kernel hv_vmbus e hv_storvsc sono disponibili sul disco RAM iniziale. Si tratta di almeno un problema noto nei sistemi in base alla distribuzione Red Hat padre.

Per risolvere il problema, è necessario aggiungere i moduli di Hyper-V in initramfs e ricostruirlo:

Modificare `/etc/dracut.conf` e aggiungere contenuto:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Ricreare initramfs:

        # dracut –f -v

Per ulteriori informazioni, vedere le informazioni sulla [ricostruzione initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passaggi successivi
A questo punto si è pronti a utilizzare il disco rigido virtuale Red Hat Enterprise Linux per creare nuove macchine virtuali in Azure. Se questa è la prima volta che si sta caricando file con estensione vhd in Azure, vedere i passaggi 2 e 3 nella [creazione e il caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Per ulteriori informazioni su hypervisor che certificati per l'esecuzione Red Hat Enterprise Linux, vedere [il sito Web Red Hat](https://access.redhat.com/certified-hypervisors).
