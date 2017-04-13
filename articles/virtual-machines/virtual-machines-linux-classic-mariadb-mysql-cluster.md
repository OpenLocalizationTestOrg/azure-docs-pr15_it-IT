<properties
    pageTitle="Eseguire un cluster di MariaDB (MySQL) su Azure"
    description="Creare un MariaDB + Galera MySQL cluster in macchine virtuali di Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>Cluster MariaDB (MySQL) - esercitazione Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Cluster MariaDB Enterprise è ora disponibile in Azure Marketplace.  Nuova offerta verrà distribuito automaticamente un cluster di MariaDB Galera su ARM. È necessario utilizzare la nuova offerta da https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Stiamo creando un cluster di [Galera](http://galeracluster.com/products/) Master multipli di [MariaDBs](https://mariadb.org/en/about/), sostituzione più efficaci, scalable e affidabile per MySQL, per l'uso in un ambiente altamente disponibile in macchine virtuali di Azure.

## <a name="architecture-overview"></a>Cenni preliminari sull'architettura

In questo argomento esegue le operazioni seguenti:

1. Creare un cluster di nodo 3
2. Separare i dischi dati dal disco del sistema operativo
3. Creare dischi di dati RAID-0/strisce con impostazione per ingrandire le dimensioni IOPS
4. Utilizzare il bilanciamento del carico di Azure per distribuire il carico dei nodi 3
5. Per ridurre al minimo operazioni ripetitive, creare un'immagine di macchine Virtuali contenente MariaDB + Galera e usarlo per creare altri cluster macchine virtuali.

![Architettura](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  In questo argomento vengono utilizzati gli strumenti di [Azure CLI](../xplat-cli-install.md) , pertanto fare attenzione a scaricarli e connetterle all'abbonamento Azure seguendo le istruzioni. Se è necessario un riferimento ai comandi disponibili nella CLI Azure, vedere questo collegamento per il [riferimento ai comandi CLI Azure](../virtual-machines-command-line-tools.md). È anche saranno necessarie per [creare una chiave SSH per l'autenticazione] e prendere nota del **percorso del file .pem**.


## <a name="creating-the-template"></a>La creazione del modello

### <a name="infrastructure"></a>Infrastruttura

1. Creare un gruppo di affinità per tenere insieme le risorse

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Creare una rete virtuale

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Creare un Account di archiviazione per ospitare tutti i dischi. Nota che si non devono essere inserendo più di 40 molto utilizzato dischi nello stesso Account di archiviazione per evitare di raggiungere il limite di account di 20.000 IOPS lo spazio di archiviazione. In questo caso, siamo quanto sono fuori da questo numero in modo che è necessario archiviare tutti gli elementi nello stesso account per semplicità

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Individuare il nome dell'immagine CentOS 7 Virtual Machine

        azure vm image list | findstr CentOS
Questo sarà simile al `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Usare un nome nel passaggio successivo.

4. Creare il modello di macchine Virtuali sostituendo **/path/to/key.pem** con il percorso in cui è archiviato il tasto SSH .pem generato

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Connettere la macchina virtuale per l'utilizzo nella configurazione RAID dischi dati 4 x 500GB

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH nel modello di macchina virtuale creata in **mariadbhatemplate.cloudapp.net:22** e connettersi tramite la chiave privata.

### <a name="software"></a>Software

1. Ottenere radice

        sudo su

2. Installare il supporto RAID:

     - Installare mdadm

                yum install mdadm

     - Creare la configurazione RAID0/strisce con un sistema di file EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Creare la directory di montaggio

                mkdir /mnt/data

     - Recuperare UUID del dispositivo RAID appena creato

                blkid | grep /dev/md0

     - Modifica /etc/fstab.

                vi /etc/fstab

     - Aggiungere il dispositivo in tale posizione per abilitare mouting automatico in sostituzione UUID con il valore ottenuto dal comando **ID blocco** prima di riavviare il computer

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Installare la nuova partizione

                mount /mnt/data

3. Installare MariaDB:

     - Creare il file MariaDB.repo:

                vi /etc/yum.repos.d/MariaDB.repo

     - Riempirla con il seguente contenuto

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Rimuovere suffisso esistente e librerie mariadb per impedire un conflitto

            yum remove postfix mariadb-libs-*

     - Installare MariaDB con Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Spostare la directory di dati MySQL il dispositivo di blocco RAID

     - Copiare la directory MySQL corrente nella nuova posizione e rimuovere directory precedente

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Impostare le autorizzazioni per nuova directory conseguenza

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Creare un symlink rivolta directory precedente nella nuova posizione sulla partizione RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Perché [che SELinux interferisce con le operazioni cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), è necessario disattivare per la sessione corrente (finché non viene visualizzata una versione compatibile). Modificare `/etc/selinux/config` per disabilitare per il riavvio successive:

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Convalidare le esecuzioni MySQL

    - Avviare MySQL

            service mysql start

    - Proteggere l'installazione di MySQL, impostare la password principale, rimuovere gli utenti anonimi, la disattivazione di accesso principale remota e la rimozione di database di prova

            mysql_secure_installation

    - Creare un utente nel database per le operazioni cluster e, facoltativamente, le applicazioni

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Interrompere MySQL

            service mysql stop

7. Creare un segnaposto di configurazione

    - Modificare la configurazione di MySQL per creare un segnaposto per le impostazioni di raggruppamento. Non sostituire il **`<Vairables>`** inserire o rimuovere ora. Che verrà eseguita una volta creato una macchina virtuale da questo modello.

            vi /etc/my.cnf.d/server.cnf

    - Modificare la sezione **[galera]** e cancellare i

    - Modificare la sezione **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Aprire necessari porte nel firewall (del computer con FirewallD in CentOS 7)

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - TSI GALERA:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Ricaricare il firewall:`firewall-cmd --reload`

9.  Ottimizzare il sistema per prestazioni ottimali. Fare riferimento al presente articolo sulla [strategia di ottimizzazione delle prestazioni](virtual-machines-linux-classic-optimize-mysql.md) per altri dettagli

    - Modificare il file di configurazione MySQL nuovamente

            vi /etc/my.cnf.d/server.cnf

    - Modificare la sezione **[mariadb]** e aggiungere il sotto

    > [AZURE.NOTE] È consigliabile **innodb\_buffer\_pool_size** 70% di memoria della macchina di virtuale. Di seguito GB 2,45 è stata impostata per la macchina virtuale Azure medio con 3,5 GB di RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Interrompere MySQL, disattivare il servizio MySQL esecuzione all'avvio per evitare di influire cluster quando si aggiunge un nuovo nodo e il deprovisioning il computer.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Acquisire macchine Virtuali tramite il portale. (Al momento strumenti [problema 1268 # in CLI Azure] descrive il fatto che le immagini acquisite con gli strumenti di Azure CLI non acquisiscono dischi di dati allegato)

    - Arrestare la macchina tramite il portale
    - Fare clic su acquisizione e specificare il nome dell'immagine come **immagine di galera mariadb** e fornire una descrizione e controllare "È stato eseguito waagent".
    ![Acquisire la macchina virtuale](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![acquisire la macchina virtuale](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Creare il cluster

Creare 3 macchine virtuali all'esterno del modello appena creato e quindi configurare e avviare il cluster.

1. Creare macchine Virtuali di 7 CentOS prima dall'immagine **mariadb-galera-immagine** è stato creato, fornire il nome di rete virtuale **mariadbvnet** e subnet **mariadb**, lavorare dimensioni **medio**passando il servizio Cloud assegnare un nome da **mariadbha** (o il nome che si desidera accedere tramite mariadbha.cloudapp.net) impostazione del nome di questo computer per essere **mariadb1** e il nome utente sia **azureuser**e consentire l'accesso SSH e passando il SSH certificato .pem file e la sostituzione **/path/to/key.pem** con il percorso in cui si archiviato il tasto SSH .pem generato.

    > [AZURE.NOTE] Comandi riportati di seguito sono suddivisi su più righe per chiarezza, ma è necessario immettere ogni come un'unica riga.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Creare 2 più macchine virtuali mediante _la connessione_ ai attualmente creati **mariadbha** servizio Cloud, modificare il **nome della macchina virtuale** , nonché la **porta SSH** a una porta univoca non in conflitto con altre macchine virtuali nel servizio Cloud stesso.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
e per MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. È necessario ottenere l'indirizzo IP interno di ciascuna delle macchine 3 virtuali per il passaggio successivo:

    ![Recupero dell'indirizzo IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH in macchine 3 virtuali ed e modificare file di configurazione su ogni

        sudo vi /etc/my.cnf.d/server.cnf

    rimuovendo **`wsrep_cluster_name`** e **`wsrep_cluster_address`** rimuovendo il **#** all'inizio e alla convalida sono effettivamente quelli desiderati.
    Inoltre, sostituire **`<ServerIP>`** in **`wsrep_node_address`** e **`<NodeName>`** in **`wsrep_node_name`** con indirizzo IP di Virtual Machine e assegnare un nome rispettivamente e rimuovere il commento per le righe di codice.

5. Avviare il cluster su MariaDB1 e consentire l'esecuzione all'avvio

        sudo service mysql bootstrap
        chkconfig mysql on

6. Avviare MySQL MariaDB2 e MariaDB3 e consentire l'esecuzione all'avvio

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Il bilanciamento del carico cluster
Una volta creato macchine virtuali raggruppate, sono stati aggiunti in una disponibilità Set denominato **clusteravset** per assicurarsi che vengono inseriti in diversi guasto e aggiornare i domini e che Azure mai non manutenzione in tutti i computer alla volta. Questa configurazione soddisfa i requisiti devono essere supportati da tale Azure livello contratto di servizio.

È ora possibile utilizzare il servizio di bilanciamento del carico di Azure per bilanciare le richieste tra i 3 nodi.

Eseguire il sotto i comandi nel computer in uso tramite CLI Azure.
La struttura di parametri comando è:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Infine, poiché CLI imposta l'intervallo di ricerca di bilanciamento del carico su 15 secondi (che può essere un po' troppo lunghi), modificarlo nel portale in **endpoint** per le macchine virtuali

![Modifica endpoint](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

quindi fare clic su Imposta Reconfigure The Load-Balanced e andare avanti

![Riconfigurare caricare Set non bilanciate](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

modificare l'intervallo di presenza su 5 secondi, quindi salvare

![Modifica dell'intervallo di verifica](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Convalida del cluster

Il lavoro viene eseguito. Cluster dovrebbe ora essere accessibile in `mariadbha.cloudapp.net:3306` che verrà raggiunto il bilanciamento del carico e indirizzare le richieste tra macchine 3 virtuali lineare ed efficiente.

Utilizzare il client MySQL preferito per connettersi o solo da una delle macchine virtuali per verificare che il cluster funzioni.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Creare un nuovo database e popolare con alcuni dati

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Verrà generato nella tabella seguente

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un 3 nodo MariaDB + Galera cluster altamente disponibili in macchine virtuali di Azure eseguono CentOS 7. Macchine virtuali sono il bilanciamento del carico con il bilanciamento del carico di Azure.

È consigliabile esaminare [un altro modo per cluster MySQL su Linux](virtual-machines-linux-classic-mysql-cluster.md) e in vari modi per [ottimizzare e testare prestazioni MySQL in macchine virtuali Linux Azure](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[creare una chiave SSH per l'autenticazione]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problema #1268 in CLI Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
