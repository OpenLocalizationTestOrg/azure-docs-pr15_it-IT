<properties 
    pageTitle="Configurare LVM in un computer virtuale che eseguono Linux | Microsoft Azure" 
    description="Informazioni su come configurare LVM su Linux in Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurare LVM su una macchina virtuale Linux in Azure

In questo documento verrà descritto come configurare logico Volume Manager (LVM) nel computer virtuale Azure. Sebbene sia possibile configurare LVM su qualsiasi disco collegato al computer virtuale, per impostazione predefinita la maggior parte delle immagini cloud non avranno LVM configurato nel disco del sistema operativo. In modo da evitare problemi con i gruppi di volume duplicati se il disco OS mai è collegato a un altro macchine Virtuali dello stesso tipo e distribuzione vale a dire durante uno scenario di ripristino. Pertanto è consigliabile utilizzare solo LVM dischi di dati.


## <a name="linear-vs-striped-logical-volumes"></a>Lineare e strisce volumi logici

LVM può essere utilizzato per combinare un numero di dischi in un volume di archiviazione singola. Per impostazione predefinita LVM in genere crea volumi logici lineari, il che significa che l'archiviazione fisica concatenato tra loro. In questo caso le operazioni di lettura/scrittura verranno in genere solo inviate a un singolo disco. Invece, è possibile anche creare strisce volumi logici in lettura e scrittura è distribuiti in più dischi contenuti nel gruppo di volume (ad esempio simile a RAID0). Per migliorare le prestazioni che è probabile che verranno vuole stripe i volumi logici in modo che lettura e scrittura utilizza tutti i dischi di dati allegato.

In questo documento viene descritto come combinare più dischi di dati in un gruppo singolo volume e quindi si crea un volume logico strisce. La procedura indicata di seguito è leggermente generalizzate per lavorare con la maggior parte delle distribuzioni. Nella maggior parte dei casi le utilità e flussi di lavoro per la gestione dei LVM in Azure non sono sostanzialmente diverse da altri ambienti. Come di consueto, anche contattare il Linux fornitore per la documentazione e procedure consigliate per l'uso di LVM con la distribuzione.


## <a name="attaching-data-disks"></a>Associare dischi di dati
In genere uno desidera iniziare con due o più dischi dati vuota quando si utilizza LVM. In base alle esigenze IO, è possibile scegliere di allegare dischi vengono memorizzati in un archivio il nostro Standard, con fino a 500 IO/ps per disco o lo spazio di archiviazione nostro Premium con un massimo di 5000 IO/ps per disco. In questo articolo non verrà fornire dettagli su come effettuare il provisioning e allegare dischi di dati a una macchina virtuale Linux. Vedere la Microsoft Azure articolo [Collega un disco](virtual-machines-linux-add-disk.md) per istruzioni dettagliate su come collegare un disco dati vuota in un computer virtuale Linux in Azure.

## <a name="install-the-lvm-utilities"></a>Installare l'utilità LVM

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS e Linux Oracle**

        # sudo yum install lvm2

- **12 SLES e openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    In SLES11 è anche necessario modificare /etc/sysconfig/lvm e impostare `LVM_ACTIVATED_ON_DISCOVERED` "abilitare":

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurare LVM
In questa guida si presuppone che è stato collegato tre dischi di dati, è necessario fare riferimento a come `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Si noti che questi potrebbe non essere sempre gli stessi nomi di percorso di una macchina virtuale. È possibile eseguire '`sudo fdisk -l`' o un comando simile per visualizzare un elenco dischi disponibili.

1. Preparare volumi fisici:

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Creare un gruppo di volume. In questo esempio stiamo chiamando il volume gruppo "dati-vg01":

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Creare i volumi logici. Il comando seguente si creerà un singolo volume logico denominato "dati-lv01" per estesi a gruppo intero volume, ma è anche possibile creare più volumi logici nel gruppo volume.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formattare il volume logico

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] A seconda dell'uso di SLES11 "-t ext3" anziché ext4. SLES11 supporta solo l'accesso in sola lettura per quelli ext4.


## <a name="add-the-new-file-system-to-etcfstab"></a>Aggiungere il nuovo file system /etc/fstab.

**Attenzione:** In modo non corretto modificando il file /etc/fstab. potrebbe causare un avvio del sistema. Se si ignora, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente il file. È anche consigliabile verrà creata una copia di backup del file /etc/fstab. prima della modifica.

1. Creare il punto di montaggio desiderata per il nuovo file system, ad esempio:

        # sudo mkdir /data


2. Individuare il percorso del volume logico

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Aprire /etc/fstab. in un editor di testo e aggiungere una voce per il nuovo file system, ad esempio:

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Quindi, salvare e chiudere /etc/fstab..


4. Verificare che la voce fstab e così via/sia corretta:

        # sudo mount -a

    Se questo comando genera un messaggio di errore, controllare la sintassi nel file fstab e così via /.

    Successivamente eseguire il `mount` comando per assicurarsi che il file system è installato:

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Facoltativo) Parametri di avvio alternativa in /etc/fstab.

    Distribuzioni molti includono il `nobootwait` o `nofail` montaggio parametri che possono essere aggiunti al file fstab e così via /. Questi parametri consentano errori quando un particolare file system di montaggio e per consentono il sistema Linux di continuare ad avviare anche se non è in grado di installare correttamente il file system RAID. Fare riferimento alla documentazione della distribuzione per ulteriori informazioni su questi parametri.

    Esempio (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
