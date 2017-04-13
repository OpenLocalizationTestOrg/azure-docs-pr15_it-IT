<properties
    pageTitle="Allegare un disco a una VM Linux | Microsoft Azure"
    description="Informazioni su come allegare un disco dati a una macchina virtuale Azure che eseguono Linux e inizializzare in modo che sia pronto per l'utilizzo."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Come collegare un disco dati a una macchina virtuale Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vedere come [collegare un disco dati utilizzando il modello di distribuzione di Manager delle risorse](virtual-machines-linux-add-disk.md).

È possibile allegare dischi vuoti e dischi che contengono dati nelle macchine virtuali di Azure. Entrambi i tipi di dischi sono VHD che si trovano in un account di archiviazione Azure. Come con l'aggiunta di qualsiasi disco a un computer Linux dopo aver collegato il disco è necessario inizializzare e formattarlo nel modo che sia pronto per l'utilizzo. Dettagli in questo articolo il collegamento di dischi vuoti e dischi già contenente dati nelle macchine virtuali, nonché come quindi inizializzare e formattare un nuovo disco.

> [AZURE.NOTE]È consigliabile utilizzare uno o più dischi separati per archiviare i dati della macchina virtuale. Quando si crea una macchina virtuale Azure, ha un disco di sistema operativo e un disco temporaneo. **Non utilizzare il disco temporaneo per l'archiviazione dei dati permanenti.** Come indica il nome, fornisce solo lo spazio di archiviazione temporaneo. E non offre ridondanza o copia di backup in quanto che non si trova in archiviazione Azure.
> Il disco temporaneo è in genere gestito dall'agente di Linux Azure e automaticamente installato per **/mnt/resource** (o **/mnt** su Ubuntu immagini). Mano, un disco dati potrebbe essere denominato in base kernel Linux simile al `/dev/sdc`, ed è necessario partizioni, formattare e installare questa risorsa. Vedere la [Guida di Azure Linux agente utente] [ Agent] per informazioni dettagliate.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inizializzare un nuovo disco di dati in Linux

1. SSH per la macchina virtuale. Per ulteriori informazioni, vedere [come effettuare l'accesso a una macchina virtuale che eseguono Linux][Logon].

2. Successivamente è necessario trovare l'identificatore di dispositivo per il disco di dati inizializzare. Esistono due modi per eseguire questa operazione:

    ) Grep per i dispositivi SCSI nei log, ad esempio il comando seguente:

            $sudo grep SCSI /var/log/messages

    Per distribuzioni Ubuntu recenti, è necessario utilizzare `sudo grep SCSI /var/log/syslog` perché la registrazione per `/var/log/messages` potrebbe essere disattivata per impostazione predefinita.

    È possibile trovare l'identificatore dell'ultimo disco di dati che è stato aggiunto nei messaggi che vengono visualizzati.

    ![Ottenere i messaggi su disco](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OR

    b) usare la `lsscsi` comando per individuare l'id del dispositivo. `lsscsi`può essere installato in uno dei `yum install lsscsi` (in rosso Hat basati distribuzioni) o `apt-get install lsscsi` (in Debian basati distribuzioni). È possibile trovare il disco che si sta cercando dalla relativa _lun_ o dal **numero di unità logica**. Ad esempio, _lun_ per dischi allegato possa essere letto facilmente da `azure vm disk list <virtual-machine>` come:

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Confrontare i dati con l'output di `lsscsi` per lo stesso esempio macchina virtuale:

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    L'ultimo numero della tupla in ogni riga è _lun_. Vedere `man lsscsi` per ulteriori informazioni.

3. Al prompt dei comandi, digitare il comando seguente per creare il dispositivo:

        $sudo fdisk /dev/sdc


4. Quando richiesto, digitare **n** per creare una nuova partizione.


    ![Creare dispositivi](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Quando richiesto, digitare **p** per creare una partizione di partizione primaria. Digitare **1** per renderla prima partizione e quindi premere INVIO per accettare il valore predefinito per il cilindro. In alcuni sistemi, è possibile visualizzare i valori predefiniti del primo e l'ultimo sectors, anziché il cilindro. È possibile scegliere di accettare le impostazioni predefinite.


    ![Creare partizione](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Digitare **p** per visualizzare i dettagli relativi al disco che è suddiviso.


    ![Informazioni su disco dell'elenco](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Digitare **w** per scrivere le impostazioni per il disco.


    ![Scrivere le modifiche disco](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. A questo punto è possibile creare il file system nella nuova partizione. Aggiungere il numero di partizione per l'ID di dispositivo (nell'esempio seguente `/dev/sdc1`). Nell'esempio seguente crea una partizione ext4 /dev/sdc1:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Creare file system di](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Sistemi SuSE Linux Enterprise 11 supportano solo accesso in sola lettura per ext4 file System. Per questi sistemi, è consigliabile per formattare il nuovo file system come ext3 anziché ext4.


9. Creare una directory di installare il nuovo file system, come indicato di seguito:

        # sudo mkdir /datadrive


10. Infine è possibile collegare le unità, come indicato di seguito:

        # sudo mount /dev/sdc1 /datadrive

    Il disco di dati è ora pronto per l'utilizzo come **/datadrive**.
    
    ![Creare la directory e installare il disco](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Aggiungere la nuova unità /etc/fstab.:

    Per garantire che l'unità è reinstallarlo automaticamente dopo il riavvio deve essere aggiunta ai file di fstab/ecc /. Inoltre, si consiglia di UUID (universale identificatore univoco) viene utilizzato in /etc/fstab. per fare riferimento all'anziché solo il nome del dispositivo (ad esempio /dev/sdc1). Utilizzo UUID evita il disco non corretto essere installato in una posizione specificata, se il sistema operativo rileva un errore durante l'avvio e di qualsiasi rimanenti dischi dati quindi assegnati tali dispositivi ID. Per trovare UUID della nuova unità, è possibile utilizzare l'utilità di **ID blocco** :

        # sudo -i blkid

    L'output sarà simile al seguente:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] In modo non corretto modificando il file **/etc/fstab.** potrebbe causare un avvio del sistema. Se si ignora, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente il file. È anche consigliabile verrà creata una copia di backup del file /etc/fstab. prima della modifica.

    Successivamente, aprire il file **/etc/fstab.** in un editor di testo:

        # sudo vi /etc/fstab

    In questo esempio viene utilizzato il valore UUID per la nuova periferica **/dev/sdc1** che è stata creata in precedenza e il punto di montaggio **/datadrive**. Aggiungere la riga seguente alla fine del file **/etc/fstab.** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    In alternativa, sui sistemi basati su SuSE Linux potrebbe essere necessario usare un formato leggermente diverso:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] Il `nofail` opzione garantisce che la macchina virtuale inizia anche se il file System è danneggiato o il disco non esiste al momento dell'avvio. Senza questa opzione, è possibile riscontrare comportamento come descritto in [Impossibile SSH in macchine Virtuali Linux a causa errori FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    È ora possibile verificare che il file system sia installato correttamente, rimozione del mount e reinstallazione di vale a dire il file system nel caso dell'esempio punto di montaggio `/datadrive` creato in questa procedura:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Se il `mount` comando genera un errore, archiviare il file o altri oggetti/fstab per la sintassi corretta. Se vengono create nuovi dati unità o partizioni, inserirli/ecc/fstab separatamente anche.

    Rendere l'unità scrivibile utilizzando questo comando:

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Successivamente la rimozione di un disco dati senza modificare fstab potrebbe causare la macchina virtuale per problemi di avvio. Se si tratta di frequente, la maggior parte delle distribuzioni forniscono sia il `nofail` e/o `nobootwait` fstab opzioni che consentono di un sistema per l'avvio anche se il disco non riesce a installare all'avvio. Per ulteriori informazioni su questi parametri, consultare la documentazione della distribuzione.

### <a name="trimunmap-support-for-linux-in-azure"></a>Supporto per tagliare/annullamento del mapping per Linux in Azure
Alcuni vengono Linux supporta le operazioni di TRIM/annullamento del mapping per rimuovere blocchi inutilizzati sul disco. Queste operazioni sono particolarmente utile in archiviazione standard per informare Azure eliminato pagine non sono più valide e può essere eliminata. Eliminazione di pagine, è possibile salvare costo se si creano file di grandi dimensioni e quindi eliminarle.

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
È possibile leggere altre informazioni sull'utilizzo del VM Linux nei seguenti articoli:

- [Come effettuare l'accesso a una macchina virtuale che eseguono Linux][Logon]

- [Come faccio a scollegare un disco da una macchina virtuale Linux](virtual-machines-linux-classic-detach-disk.md)

- [Utilizzo di CLI Azure con il modello di distribuzione classica](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
