<properties
    pageTitle="Ottimizzare le prestazioni di MySQL in macchine virtuali Linux | Microsoft Azure"
    description="Informazioni su come ottimizzare MySQL in esecuzione su una Azure macchine () che eseguono Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Ottimizzazione delle prestazioni MySQL in macchine virtuali Linux Azure

Dipende da diversi fattori che influiscono su prestazioni MySQL su Azure, sia in configurazione di selezione e software hardware virtuale. In questo articolo è incentrata su ottimizzazione delle prestazioni tramite lo spazio di archiviazione, sistema e configurazioni del database.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Utilizzo di RAID in un computer virtuale Azure
Spazio di archiviazione è fondamentale che impatto sulle prestazioni di database in ambienti cloud.  Rispetto a un singolo disco, RAID possono accedere più rapidamente tramite concorrenza.  Per altri dettagli, consultare [Livelli RAID Standard](http://en.wikipedia.org/wiki/Standard_RAID_levels) .   

Velocità disco e l'ora di risposta i/o in Azure possono essere migliorate in modo significativo tramite RAID. Il test di laboratorio Mostra i/o velocità possibile raddoppiare e i/o risposta possibile ridurre i tempi della metà mediamente quando il numero di dischi RAID è doppia (da 2 a 4, 4 a 8, ecc.). Per informazioni dettagliate, vedere [Appendice](#AppendixA) .  

Oltre a disco, MySQL miglioreranno quando si aumenta il livello RAID.  Per informazioni dettagliate, vedere [Appendice B](#AppendixB) .  

Può essere anche necessario prendere in considerazione le dimensioni del blocco. In generale quando si dispone di una dimensione maggiore per il blocco, si otterrà inferiore generale, in particolare per scrittura di grandi dimensioni. Tuttavia, quando le dimensioni del blocco sono troppo grande, è possibile aggiungere un ulteriore sovraccarico e non è possibile trarre vantaggio del RAID. La dimensione predefinita corrente è 512KB, che sia ottimale per ambienti di produzione più generali. Per informazioni dettagliate, vedere [Appendice C](#AppendixC) .   

Si noti che esistono limiti dischi quanti è possibile aggiungere per i tipi di macchina virtuale diversa. Questi limiti sono riportati in [macchine virtuali e le dimensioni di servizio Cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 4 dischi dati collegata a seguire l'esempio RAID in questo articolo, sarà necessario anche se è possibile scegliere di configurare RAID con dischi meno.  

In questo articolo si presuppone una macchina virtuale Linux è già stato creato e MYSQL installato e configurato. Per ulteriori informazioni sulla Guida introduttiva, vedere come installare MySQL in Azure.  

###<a name="setting-up-raid-on-azure"></a>La configurazione RAID su Azure
La procedura seguente viene illustrato come creare RAID su Azure tramite il portale classico Azure. Possono anche configurare RAID usando gli script di Windows PowerShell.
In questo esempio abbiamo configurerà RAID 0 con 4 dischi.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Passaggio 1: Aggiungere un disco dati alla macchina virtuale  

Nella pagina di macchine virtuali del portale di classica Azure, fare clic su macchina virtuale a cui si desidera aggiungere un disco dati. In questo esempio, la macchina virtuale è mysqlnode1.  

![][1]

Nella pagina per la macchina virtuale, fare clic su **Dashboard**.  

![][2]


Nella barra delle applicazioni fare clic su **Connetti**.

![][3]

E quindi fare clic su **Allega disco vuoto**.  

![][4]

Per dischi di dati, la **Preferenza di Cache Host** deve essere impostata su **Nessuno**.  

Un disco vuoto verrà aggiunto al computer virtuale. Ripetere questo passaggio tre volte in modo da avere 4 dischi di dati per RAID.  

È possibile visualizzare le unità aggiunte nella macchina virtuale esaminando il Registro di messaggi kernel. Ad esempio, per visualizzare questa Ubuntu, utilizzare il comando seguente:  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Passaggio 2: Creare RAID con dischi aggiuntivi
Procedere in questo articolo per istruzioni dettagliate su configurazione RAID:  

[Configurare il software RAID su Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Se si utilizza il file system XFS, seguire la procedura seguente dopo aver creato RAID.

Per installare XFS Debian, Ubuntu o Linux menta, usare il comando seguente:  

    apt-get -y install xfsprogs  

Per installare XFS su Fedora, CentOS o RHEL, usare il comando seguente:  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Passaggio 3: Configurare un nuovo percorso di archiviazione
Usare il comando seguente:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Passaggio 4: Copiare i dati originali nel nuovo percorso di archiviazione
Usare il comando seguente:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Passaggio 5: Modificare le autorizzazioni in modo che possono accedere MySQL (lettura e scrittura) il disco di dati
Usare il comando seguente:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Regolare l'algoritmo di pianificazione i/o su disco
Linux consente di quattro tipi dei / o pianificazione algoritmi:  

-   Algoritmo NOOP (operazione n)
-   Algoritmo di scadenza (scadenza)
-   Algoritmo di Accodamento completamente giusto (CFQ)
-   Algoritmo periodo budget (Anticipatory)  

È possibile selezionare diverse i/o le utilità di pianificazione in scenari diversi per ottimizzare le prestazioni. In un ambiente di accesso completamente casuale, non c'è la differenza tra algoritmi CFQ e scadenza per prestazioni ottimali. In genere è consigliabile impostare l'ambiente di database MySQL alla scadenza di stabilità. Se esistono molte i/o sequenziale, CFQ può ridurre le prestazioni dei / o del disco.   

Per SSD e altri dispositivi, usando NOOP o scadenza può ottenere prestazioni migliori rispetto a utilità di pianificazione predefinita.   

Dal kernel 2,5, algoritmo di programmazione i/o predefinito è scadenza. A partire dal kernel 2.6.18, CFQ diventato algoritmo di programmazione i/o predefinito.  È possibile specificare questa impostazione al momento dell'avvio del Kernel o dinamicamente modificare questa impostazione durante l'esecuzione di sistema.  

Nell'esempio seguente viene illustrato come controllare e imposta utilità di pianificazione predefinita all'algoritmo NOOP.  

Per la famiglia Debian distribuzione:

###<a name="step-1view-the-current-io-scheduler"></a>Utilità di pianificazione visualizzazione dei / o passaggio 1.
Usare il comando seguente:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Verrà visualizzata in seguito output, che indica l'utilità di pianificazione corrente.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Passaggio 2. Cambiare il dispositivo corrente (dev/sda) operazioni pianificazione algoritmo di
Usare i comandi seguenti:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Impostare questa proprietà per /dev/sda solo non è utile. Deve essere impostata su tutti i dischi di dati in cui si trova il database.  

Verrà visualizzato il seguente output, che indica che grub.cfg è stata ricreata correttamente e che l'utilità di pianificazione predefinito è stato aggiornato a NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Per il gruppo di distribuzione Red Hat, è necessario solo il comando seguente:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Configurare le impostazioni operazioni di file di sistema
Una procedura consigliata consiste nel disattivare la caratteristica di registrazione per volta nel file system. Per volta è ora dell'ultimo accesso file. Ogni volta che si accede a un file, il file system registra il timestamp nel registro. Vengono utilizzate raramente queste informazioni. È possibile disabilitarlo se non è necessario, che consente di ridurre tempo di accesso complessivo del disco.  

Per disattivare la registrazione per volta, è necessario modificare il file system configurazione file /etc/. fstab e aggiungere l'opzione **noatime** .  

Ad esempio modificare il file /etc/fstab. vim aggiungendo noatime come illustrato di seguito.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Quindi, reinstallare il file system con il comando seguente:  

    mount -o remount /RAID0

Verificare il risultato modificato. Si noti che quando si modifica il file di test, l'ora dell'accesso non viene aggiornato.  

Prima di esempio:     

![][5]

Dopo l'esempio:

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumentare il numero massimo di punti di manipolazione di sistema per la concorrenza elevata
MySQL è elevato concorrenza database. Il numero di punti di manipolazione di simultanee predefinito è 1024 per Linux, non è sufficiente. **Utilizzare la procedura seguente per aumentare i quadratini simultanei massimo del sistema per il supporto della concorrenza elevata di MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Passaggio 1: Modificare il file limits.conf
Aggiungere quattro righe seguenti nel file /etc/security/limits.conf per aumentare i massimo consentiti simultanee punti di manipolazione di. Si noti che 65536 il numero massimo supportati dal sistema.   

    * contorni nofile 65536
    * disco rigido nofile 65536
    * contorni nproc 65536
    * disco rigido nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Passaggio 2: Aggiornare il sistema per i nuovi limiti
Eseguire i comandi seguenti:  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Passaggio 3: Assicurarsi che i limiti vengano aggiornati all'avvio
Inserire i comandi di avvio seguenti nel file /etc/rc.local in modo che abbia effetto ogni fase di avvio.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Ottimizzazione del database MySQL
È possibile utilizzare la stessa strategia di ottimizzazione delle prestazioni per configurare MySQL su Azure come in un computer locale.  

Le regole di ottimizzazione i/o principale sono:   

-   Aumentare la dimensione della cache.
-   Ridurre i tempi di risposta i/o.  

Per ottimizzare le impostazioni del server MySQL, è possibile aggiornare il file my.cnf, ovvero il file di configurazione predefinito per server e computer client.  

I seguenti elementi di configurazione sono i principali fattori che influiscono sulle prestazioni MySQL:  

-   **innodb_buffer_pool_size**: pool di buffer contiene dati memorizzati e l'indice. Questo è impostato in genere 70% di memoria fisica.
-   **innodb_log_file_size**: questa è la dimensione del registro Ripeti. Utilizzare Ripeti registri per fare in modo che le operazioni di scrittura siano veloce, affidabile e recuperati dopo un arresto anomalo. Questo è impostato su 512MB, in modo da ampio spazio per registrare le operazioni di scrittura.
-   **max_connections**: in alcuni casi le applicazioni non chiudere le connessioni in modo corretto. Un valore maggiore otterranno server Cestino inattivo connessioni più tempo. Numero massimo di connessioni è 10000, ma il massimo consigliato è 5000.
-   **Innodb_file_per_table**: questa impostazione abilitare o disabilitare la possibilità di InnoDB per l'archiviazione tabelle nei file separati. Attivare l'opzione garantisce che è possono applicare in modo efficiente diverse operazioni di amministrazione avanzate. Dal punto di vista delle prestazioni, può accelerare la trasmissione di spazio di tabella e ottimizzare le prestazioni di gestione sporco. L'impostazione consigliata per questa è così via.</br>
    Da 5.6 MySQL, l'impostazione predefinita è Sì. Di conseguenza, è richiesta alcuna azione. Per altre versioni, è precedente alla 5.6, le impostazioni predefinite è disattivata. È necessario attivare questa via. E devono applicarlo prima di caricamento dei dati, poiché è state modificate solo le tabelle appena creato.
-   **innodb_flush_log_at_trx_commit**: valore predefinito è 1, con l'ambito impostata su 0 ~ 2. Il valore predefinito è l'opzione più adatta per autonoma DB MySQL. L'impostazione di 2 consente la maggior parte dell'integrità dei dati ed è adatto per schema nel MySQL cluster. L'impostazione di 0 consente la perdita di dati, che può influire sulla affidabilità, in alcuni casi con prestazioni migliori ed è adatto per secondario cluster MySQL.
-   **Innodb_log_buffer_size**: il buffer di registro consente transazioni per l'esecuzione senza dover svuotamento del log su disco prima di eseguire il commit le transazioni. Tuttavia, se c'è oggetto binario di grandi dimensioni o un campo di testo, la cache verrà utilizzata molto rapidamente e i/o frequenti verrà attivata. È meglio aumentare le dimensioni di buffer se variabile di stato Innodb_log_waits non è 0.
-   **query_cache_size**: l'opzione ottimale consiste nel disabilitare dall'inizio. Impostare query_cache_size su 0 (si tratta ora l'impostazione di MySQL 5.6) e utilizzare altri metodi per velocizzare le query.  

Vedere [Appendice D](#AppendixD) per il confronto delle prestazioni dopo l'ottimizzazione.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Attivare il log di query lenta MySQL per analizzare la riduzione delle prestazioni
Log di query lenta MySQL consentono di individuare le query lente per MySQL. Dopo aver attivato il log di query lenta MySQL, è possibile utilizzare strumenti MySQL come **mysqldumpslow** per identificare la riduzione delle prestazioni.  

Si noti che per impostazione predefinita non è attivato. Attivare il log di query lenta potrebbe utilizzare alcune risorse CPU. Di conseguenza, è consigliabile attivare questa temporaneamente per la risoluzione dei problemi di prestazioni.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Passaggio 1: Modificare file my.cnf aggiungendo le seguenti righe alla fine   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Passaggio 2: Riavviare server mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Passaggio 3: Controllare se l'impostazione sta impiegando effetto utilizzando il comando "Mostra"

![][7]   

![][8]

In questo esempio, è possibile vedere che attivata la caratteristica di query lenta. È quindi possibile utilizzare lo strumento **mysqldumpslow** per determinare i problemi di prestazioni e ottimizzare le prestazioni, ad esempio aggiungere indici.





##<a name="appendix"></a>Appendice

Di seguito sono riportati dati di test sulle prestazioni di esempio prodotti in laboratorio di destinazione, offrono generali sull'andamento delle prestazioni dati diversi ottimizzazione delle prestazioni approcci, tuttavia i risultati possono variare in versioni diverse di ambiente o prodotto.

<a name="AppendixA"></a>Appendice a:  
**Prestazioni del disco (IOPS) con livelli RAID diversi**


![][9]

**Comandi di test:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Nota: Il carico di lavoro di questo test utilizza 64 thread, si tenta di raggiungere il limite massimo di RAID.

<a name="AppendixB"></a>Combinazioni  
**Confronto delle prestazioni (velocità) MySQL con livelli RAID diversi**   
(File system di XFS)


![][10]  
![][11]

**Comandi di test:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Confronto delle prestazioni (OLTP) MySQL con livelli RAID diversi**  
![][12]

**Comandi di test:**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Appendice c:   
**Confronto delle prestazioni (IOPS) disco di dimensioni blocco diversi**  
(File system di XFS)


![][13]

**Comandi di test:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Nota le dimensioni del file utilizzata per questo test sono 30 e 1GB rispettivamente, con RAID XFS 0(4 disks) file system.


<a name="AppendixD"></a>Appendice d:  
**Confronto delle prestazioni (velocità) MySQL prima e dopo l'ottimizzazione**  
(XFS File System)


![][14]

**Comandi di test:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Impostazione di configurazione per impostazione predefinita e optmization è il seguente:**

|Parametri |Impostazione predefinita    |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Nessuno   |7G
|**innodb_log_file_size**   |5M |512M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16 MILIONI    |0


Parametri di configurazione ottimizzazione più dettagliati, vedere le istruzioni ufficiale mysql.  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Ambiente di testing**  

|Hardware   |Dettagli
|-----------|-------
|CPU    |AMD Opteron(tm) processore 4171 HE / 4 core
|Memoria |G 14
|disco rigido   |10G/disco
|sistema operativo |Ubuntu 14.04.1 risultati



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
