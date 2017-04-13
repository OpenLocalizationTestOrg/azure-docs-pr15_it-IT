<properties
    pageTitle="Aggiungere un disco Linux VM | Microsoft Azure"
    description="Informazioni su come aggiungere un disco persistente il VM Linux"
    keywords="macchina virtuale Linux, aggiungere risorse disco"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Aggiungere un disco una VM Linux

In questo articolo viene illustrato come allegare un disco persistente alle macchine Virtuali in modo da conservare i dati - anche se la macchina virtuale viene di nuovo a causa di manutenzione o ridimensionandoli. Per aggiungere un disco, è necessario [CLI Azure](../xplat-cli-install.md) configurato in modalità di gestione risorse (`azure config mode arm`).  

## <a name="quick-commands"></a>Comandi rapidi

Nell'esempio seguente comando, sostituire i valori compresi tra &lt; e &gt; con i valori al proprio ambiente.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Collegare un disco

Associare un nuovo disco è veloce. Tipo di `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` per creare e collegare un nuovo disco GB per la macchina virtuale. Se non si individua un account di archiviazione in modo esplicito, qualsiasi disco create viene inserito lo stesso account di archiviazione in cui si trova il disco rigido del sistema operativo.  Che dovrebbe essere simile al seguente:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Output

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Connettersi a VM Linux per installare il nuovo disco

> [AZURE.NOTE] In questo argomento si connette a una macchina virtuale utilizzando nomi utente e password. Per utilizzare pubblici e privati coppie di parole chiave per comunicare con le macchine Virtuali, vedere [come utilizzare SSH con Linux in Azure](virtual-machines-linux-mac-create-ssh-keys.md). È possibile modificare la connessione **SSH** di macchine virtuali creati con il `azure vm quick-create` comando utilizzando il `azure vm reset-access` comando per reimpostare l'accesso **SSH** completamente, aggiungere o rimuovere utenti o aggiungere i file di chiave pubblici per proteggere l'accesso.

È necessario per SSH nella macchina virtuale Azure per partizione, formattare e installare il disco di nuovo in modo che le VM Linux usarla. Se non si ha familiarità con la connessione con **ssh**, il comando assume la forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`e ha un aspetto simile al seguente:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Output

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

Ora che è connessi alle macchine Virtuali, si è pronti per allegare un disco.  Il disco, è prima necessario individuare utilizzando `dmesg | grep SCSI` (il metodo da utilizzare per individuare il nuovo disco può variare). In questo caso simile al seguente:

```bash
dmesg | grep SCSI
```

Output

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

e in caso di questo argomento, il `sdc` disco è quello che si desidera. A questo punto suddividere il disco con `sudo fdisk /dev/sdc` - presupponendo che nel caso il disco era `sdc`e impostarlo come un disco principale in partizione 1 e accettare le altre impostazioni predefinite.

```bash
sudo fdisk /dev/sdc
```

Output

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Creare la partizione digitando `p` quando viene richiesto:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

E la scrittura di un file system alla partizione tramite il comando **mkfs** che specifica il tipo di file System e il nome del dispositivo. In questo argomento viene utilizzata la `ext4` e `/dev/sdc1` dall'alto:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Output

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

A questo punto viene creata una directory per installare il file system utilizzando `mkdir`:

```bash
sudo mkdir /datadrive
```

E si montaggio directory utilizzando `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Il disco di dati è ora possibile utilizzare come `/datadrive`.

```bash
ls
```

Output

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Per garantire che l'unità è reinstallarlo automaticamente dopo il riavvio deve essere aggiunta ai file di fstab/ecc /. Inoltre, si consiglia di UUID (universale identificatore univoco) viene utilizzato in/ecc/fstab per fare riferimento all'anziché solo il nome del dispositivo (ad esempio `/dev/sdc1`). Se il sistema operativo rileva un errore durante l'avvio, l'utilizzo di UUID evita il disco non corretto essere installato in un percorso specificato. Rimanente dischi di dati vengono quindi assegnato tali ID dispositivo stesso. Per trovare UUID della nuova unità, utilizzare l'utilità di **ID blocco** :

```bash
sudo -i blkid
```

L'output sarà simile al seguente:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] In modo non corretto modificando il file **/etc/fstab.** potrebbe causare un avvio del sistema. Se si ignora, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente il file. È anche consigliabile verrà creata una copia di backup del file /etc/fstab. prima della modifica.

Successivamente, aprire il file **/etc/fstab.** in un editor di testo:

```bash
sudo vi /etc/fstab
```

In questo esempio viene utilizzato il valore UUID per la nuova periferica **/dev/sdc1** che è stata creata in precedenza e il punto di montaggio **/datadrive**. Aggiungere la riga seguente alla fine del file **/etc/fstab.** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] In un secondo momento la rimozione di un disco dati senza modificare fstab potrebbe causare la macchina virtuale per problemi di avvio. La maggior parte delle distribuzioni forniscono sia il `nofail` e/o `nobootwait` fstab opzioni. Queste opzioni consentono di un sistema per l'avvio anche se il disco non riesce a installare all'avvio. Per ulteriori informazioni su questi parametri, consultare la documentazione della distribuzione.

>[AZURE.NOTE] L'opzione **nofail** garantisce che la macchina virtuale inizia anche se il file System è danneggiato o il disco non esiste al momento dell'avvio. Senza questa opzione, è possibile riscontrare comportamento come descritto in [Impossibile SSH in macchine Virtuali Linux a causa errori FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Supporto per tagliare/annullamento del mapping per Linux in Azure
Alcuni vengono Linux supporta le operazioni di TRIM/annullamento del mapping per rimuovere blocchi inutilizzati sul disco. Questo è utile principalmente in archiviazione standard per informare Azure eliminato pagine non sono più valide e può essere eliminata. In questo modo risparmiare costo se si creano file di grandi dimensioni e quindi eliminarle.

Esistono due modi per attivare TRIM supportano le VM Linux. Per l'approccio consigliato come di consueto, consultare la distribuzione:

- Utilizzare la `discard` montaggio opzione in `/etc/fstab`, ad esempio:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- In alternativa, è possibile eseguire il `fstrim` comando manualmente dalla riga di comando o aggiungerla al crontab eseguire regolarmente:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Risoluzione dei problemi
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passaggi successivi

- Tenere presente che il nuovo disco non è disponibile per la macchina virtuale se il riavvio a meno che non è scrivere tali informazioni al file [fstab](http://en.wikipedia.org/wiki/Fstab) .
- Per garantire che la VM Linux è configurato correttamente, esaminare i suggerimenti di [ottimizzare le prestazioni di computer Linux](virtual-machines-linux-optimization.md) .
- Espandere la capacità di archiviazione mediante l'aggiunta di dischi aggiuntivi e [configurare RAID](virtual-machines-linux-configure-raid.md) per prestazioni ottimali aggiuntive.
