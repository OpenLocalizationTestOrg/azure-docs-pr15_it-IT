<properties
pageTitle="Preparare una macchina virtuale di Linux Oracle per Azure | Microsoft Azure"
description="Passo dopo passo la configurazione di una macchina virtuale Oracle in esecuzione Linux in Microsoft Azure."
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparare una macchina virtuale Linux Oracle per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Preparare una macchina virtuale Linux Oracle 6.4 + per Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [Preparare una macchina virtuale Linux Oracle 7.0 + per Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che già stato installato un sistema operativo Linux Oracle a un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione, ad esempio Hyper-V. Per istruzioni, vedere [installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione Linux Oracle**

- Kernel compatibile Red Hat Oracle e il relativo UEK3 (estremamente affidabile e Enterprise Kernel) sono supportate in Hyper-V e Azure. Per ottenere risultati ottimali, assicurarsi di aggiornare al più recente kernel durante la preparazione del disco rigido virtuale Linux Oracle.

- Oracle UEK2 non è supportata in Hyper-V e Azure come non include i driver necessari.

- Nel nuovo formato VHDX non è supportato in Azure. È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il cmdlet disco rigido virtuale Converti.

- Quando si installa il sistema Linux, è consigliabile utilizzare partizioni standard anziché LVM (spesso l'impostazione predefinita per molte installazioni). Evitare a LVM nome in conflitto con macchine virtuali duplicate, in particolare se un disco del sistema operativo deve mai essere collegati a un'altra macchina virtuale per la risoluzione dei problemi. LVM o [RAID](virtual-machines-linux-configure-raid.md) può essere usata su dischi dati se si preferisce.

- NUMA non è supportata per dimensioni maggiori di macchine Virtuali a causa di un bug nelle versioni di kernel Linux sotto 2.6.37. Questo problema influisce principalmente distribuzioni che utilizzano il padre kernel Red Hat 2.6.32. Installazione manuale dell'agente di Azure Linux (waagent) verrà automaticamente disabilitata NUMA nella configurazione GRUB kernel Linux. Sono disponibili ulteriori informazioni nella procedura seguente.

- Non si configura una partizione di scambio su disco del sistema operativo. Agente di Linux configurabili per creare un file di scambio sul disco temporaneo di risorse. Sono disponibili ulteriori informazioni nella procedura seguente.

- Tutti i dischi rigidi virtuali deve avere dimensioni multipli di 1 MB.

- Assicurarsi che la `Addons` repository è abilitato. Scegliere di modificare il file `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e modificare la riga `enabled=0` a `enabled=1` in **[ol6_addons]** o **[ol7_addons]** in questo file.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
È necessario completare i passaggi di configurazione specifiche nel sistema operativo per la macchina virtuale per l'esecuzione in Azure.

1. Nel riquadro centrale di Hyper-V Manager, selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire la finestra per la macchina virtuale.

3. Disinstallare NetworkManager eseguendo il comando seguente:

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE] Se non è già stato installato il pacchetto, questo comando non riesce con un messaggio di errore. È previsto.

4. Creare un file denominato **rete** nella directory /etc/sysconfig/che contiene il testo seguente:

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  Creare un file denominato **ifcfg eth0** nel /etc/sysconfig/network-scripts / directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  Spostare o rimuovere udev regole per evitare di generare statiche regole per l'interfaccia Ethernet. Queste regole provocare problemi se si intende duplicare una macchina virtuale in Azure o Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # chkconfig network on

8.  Installare pyasn1 python eseguendo il comando seguente:

        # sudo yum install python-pyasn1

9.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel predefinito contiene i parametri seguenti:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Verranno disattivate NUMA a causa di un errore di kernel compatibile Red Hat Oracle.

    Oltre a quanto sopra menzionato, si consiglia di *rimuovere* i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    Il `crashkernel` opzione potrebbe essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi in formati di macchine Virtuali più piccoli.

10.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita.

11.  Installare l'agente di Linux Azure eseguendo il comando seguente:

        # <a name="sudo-yum-install-walinuxagent"></a>installazione di slurp Sudo WALinuxAgent

    Si noti che l'installazione del pacchetto WALinuxAgent comporta la rimozione di NetworkManager e pacchetti NetworkManager gnome se non sono stati rimossi già come descritto nel passaggio 2.

12.  Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorsa locale è *temporaneo* e può essere svuotata quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 # # Nota: impostare questa proprietà su qualsiasi elemento è necessario essere.

13.  Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # <a name="sudo-waagent--force--deprovision"></a>Sudo waagent-forzare - annullare l'implementazione
        # <a name="export-histsize0"></a>esportare HISTSIZE = 0
        # <a name="logout"></a>disconnessione

14.  Fare clic su **azione -\> arresto** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.

## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Modifiche in Linux Oracle 7**

Preparazione di una macchina virtuale Oracle Linux 7 di Azure è molto simile a quello per Oracle Linux 6. Esistono tuttavia alcune differenze importanti notare:

-   Il kernel compatibile Red Hat e Oracle UEK3 sono supportati in Azure. È consigliabile kernel UEK3.

-   Il pacchetto di NetworkManager non è più in conflitto con l'agente di Azure Linux. Viene installato per impostazione predefinita ed è consigliabile non viene rimosso.

-   GRUB2 a questo punto viene utilizzato come caricatore di avvio predefinita in modo che la procedura per la modifica di parametri del kernel è cambiata (vedere di seguito).

-   XFS è ora il sistema di file predefinito. Il file system ext4 ancora utilizzabili se lo si desidera.

**Passaggi di configurazione**

1.  In Gestione di Hyper-V, selezionare la macchina virtuale.

2.  Fare clic su **Connetti** per aprire una finestra console per la macchina virtuale.

3.  Creare un file denominato **rete** nella directory /etc/sysconfig/che contiene il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Creare un file denominato **ifcfg eth0** nel /etc/sysconfig/network-scripts / directory che contiene il testo seguente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  Spostare o rimuovere udev regole per evitare di generare statiche regole per l'interfaccia Ethernet. Queste regole provocare problemi se si intende duplicare una macchina virtuale in Microsoft Azure o Hyper-V.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Assicurarsi che il servizio di rete verrà avviato al momento dell'avvio eseguendo il comando seguente:

        # sudo chkconfig network on

7.  Installare il pacchetto di pyasn1 python eseguendo il comando seguente:

        # sudo yum install python-pyasn1

8.  Eseguire il seguente comando per cancellare i metadati slurp correnti e installare gli aggiornamenti:

        # sudo yum clean all
        # sudo yum -y update

9.  Modificare la riga di avvio kernel nella configurazione grub per includere i parametri kernel aggiuntive per Azure. A tale scopo, aprire "/ e così via, predefinito/grub" in un editor di testo e modificare il GRUB\_riga di comando\_parametro LINUX, ad esempio:

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    In questo modo anche tutti i messaggi di console vengono inviati alla porta seriale prima che consentono di Azure supporto con il debug dei problemi. Oltre a quanto sopra menzionato, si consiglia di *rimuovere* i parametri seguenti:

        rhgb quiet crashkernel=auto

    Avvio di grafica e interattiva non sono utili per un ambiente basato su cloud in cui tutti i log per essere inviato alla porta seriale.

    Il `crashkernel` opzione potrebbe essere sinistra se lo si desidera configurare, ma che questo parametro consente di ridurre la quantità di memoria disponibile in macchine Virtuali da 128 MB o più note. È possibile che presentano problemi in formati di macchine Virtuali più piccoli.

10.  Dopo aver modifica "/ e così via, predefinito/grub", eseguire il seguente comando per ricreare la configurazione di grub:

        # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>Sudo grub2 mkconfig - o /boot/grub2/grub.cfg

11.  Verificare che il server SSH sia installato e configurato per l'avvio all'avvio. Si tratta in genere l'impostazione predefinita.

12.  Installare l'agente di Linux Azure eseguendo il comando seguente:

        # <a name="sudo-yum-install-walinuxagent"></a>installazione di slurp Sudo WALinuxAgent

13.  Non creare Scambia spazio su disco del sistema operativo.

    L'agente di Linux Azure può configurare automaticamente lo spazio di scambio utilizzando il disco di risorse locale collegata alla macchina virtuale dopo il provisioning su Azure. Si noti che il disco di risorsa locale è *temporaneo* e può essere svuotata quando la macchina virtuale viene rimosso con deprovisioning. Dopo aver installato l'agente di Linux Azure (vedere il passaggio precedente), modificare in modo appropriato ai parametri seguenti disponibili in /etc/waagent.conf:

        ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 # # Nota: impostare questa proprietà su qualsiasi elemento è necessario essere.

14.  Eseguire i comandi seguenti per eseguire la macchina virtuale e prepararlo per il provisioning su Azure:

        # <a name="sudo-waagent--force--deprovision"></a>Sudo waagent-forzare - annullare l'implementazione
        # <a name="export-histsize0"></a>esportare HISTSIZE = 0
        # <a name="logout"></a>disconnessione

15.  Fare clic su **azione -\> arresto** nella gestione di Hyper-V. Il VHD Linux è ora pronto per essere caricato in Azure.
