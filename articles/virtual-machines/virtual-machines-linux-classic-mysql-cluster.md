<properties
    pageTitle="Clusterize MySQL con set di bilanciamento del carico | Microsoft Azure"
    description="Configurazione di una bilanciamento del carico e ad alta disponibilità Linux MySQL cluster creato con il modello di distribuzione classica su Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Utilizzo di set di bilanciamento del carico per clusterize MySQL su Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Lo scopo di questo articolo è per esplorare e illustrare i diversi metodi disponibili per distribuire i servizi basati su Linux altamente disponibili in Microsoft Azure esplorazione disponibilità MySQL Server come nozioni di base. Un video che illustrano questo approccio è disponibile su [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Viene descritta una soluzione condivisione due nodi master singolo MySQL disponibilità in base a DRBD, Corosync e Pacemaker. Solo un nodo esegue MySQL alla volta. Leggere e scrivere dalla risorsa DRBD è limitato a un solo nodo anche alla volta.

Non è necessario per una soluzione VIP come LVS poiché insiemi di bilanciamento del carico di Microsoft Azure vengono utilizzati per fornire funzionalità circolari e rilevamento endpoint, rimozione sia il recupero regolare dell'indirizzo VIP. L'indirizzo VIP è un indirizzo IPv4 globalmente routing assegnato da Microsoft Azure quando si crea il servizio cloud.

Sono disponibili altre architetture possibili per MySQL tra il Cluster, Percona e Galera nonché diverse soluzioni middleware, inclusi almeno come una macchina virtuale in [Macchine Virtuali deposito](http://vmdepot.msopentech.com). A condizione che queste soluzioni possono replicare in unicast e multicast o broadcast e non si basano sulle archiviazione condivisa o più interfacce di rete, gli scenari dovrebbero essere semplici distribuire in Microsoft Azure.

Naturalmente queste architetture cluster possono essere esteso ad altri prodotti come PostgreSQL e OpenLDAP in modo simile. Ad esempio, il bilanciamento del carico procedura con senza condivisione è stata verificata con più master OpenLDAP ed è possibile guardare sul blog Channel 9.

## <a name="getting-ready"></a>Operazioni preliminari

È necessario un account di Microsoft Azure con un abbonamento valido a possibilità di creare macchine virtuali di almeno due (2) (x utilizzati in questo esempio), impostare una rete e una subnet, un gruppo di affinità e una disponibilità, nonché la possibilità di creare nuovi dischi rigidi virtuali nella stessa regione servizio cloud e di collegarli a macchine virtuali Linux.

### <a name="tested-environment"></a>Ambiente testati

- Ubuntu 13.10
  - DRBD
  - Server MySQL
  - Corosync e Pacemaker

### <a name="affinity-group"></a>Gruppo affinità

Registrazione in Azure classica portale allo scorrimento fino a impostazioni e creando un nuovo gruppo di affinità viene creato un gruppo di affinità per la soluzione. A questo gruppo affinità verranno assegnate le risorse assegnate create in un secondo momento.

### <a name="networks"></a>Reti

Viene creata una nuova rete e viene creata una subnet all'interno della rete. Abbiamo scelto una rete 10.10.10.0/24 con un solo /24 subnet all'interno.

### <a name="virtual-machines"></a>Macchine virtuali

Il primo Ubuntu 13.10 viene creato con un'immagine Endorsed Ubuntu raccolta e chiamato `hadb01`. Viene creato un nuovo servizio cloud del processo, denominato hadb. Abbiamo chiamata in questo modo per illustrare la natura condivisa a bilanciamento del carico contenenti il servizio quando si aggiungono altre risorse. La creazione di `hadb01` sarà se tramite il portale. Viene creato automaticamente un endpoint per SSH e la rete creata sia selezionata. È anche scegliere di creare una nuova disponibilità impostare per macchine virtuali.

Dopo aver creata la macchina virtuale prima (tecnicamente, quando viene creato il servizio cloud) procedere per creare la seconda macchina virtuale, `hadb02`. Per la seconda macchina virtuale verrà anche utilizzato macchine Virtuali 13.10 Ubuntu dalla raccolta tramite il portale ma si potrebbe scegliere di utilizzare un servizio cloud esistente, `hadb.cloudapp.net`, invece di crearne uno nuovo. Il set di rete e la disponibilità dovrebbe essere selezionato automaticamente per noi. Un endpoint SSH verrà creato, troppo.

Dopo avere create entrambi macchine virtuali, verrà nota la porta SSH per `hadb01` (TCP 22) e `hadb02` (assegnato automaticamente da Azure)

### <a name="attached-storage"></a>Archiviazione

Si collega un nuovo disco a entrambe le macchine virtuali e creare nuovi dischi 5 GB del processo. I dischi verranno ospitati nel contenitore di disco rigido virtuale in uso per il nostro dischi del sistema operativo principale. Una volta dischi vengono creati e allegati non è necessario per la riavviare Linux come kernel verrà visualizzato il nuovo dispositivo (in genere `/dev/sdc`, è possibile controllare `dmesg` per l'output)

In ogni macchina virtuale procedere per creare una nuova partizione utilizzando `cfdisk` (partizione Linux primaria,) e scrivere la nuova tabella delle partizioni. **Non viene creato un file System in questa partizione** .

## <a name="setting-up-the-cluster"></a>La configurazione del cluster

In entrambi macchine virtuali Ubuntu, è necessario utilizzare APT per installare Corosync, Pacemaker e DRBD. Utilizzo `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Non installare MySQL al momento** . Debian Ubuntu script di installazione e verrà inizializzato una directory di dati MySQL in `/var/lib/mysql`, ma poiché la directory viene sostituita da un file System DRBD, è necessario eseguire questa operazione in un secondo momento.

A questo punto è bisogna anch'esso (utilizzando `/sbin/ifconfig`) sia macchine virtuali usano gli indirizzi di subnet 10.10.10.0/24 e che è possibile effettuare il ping tra loro in base al nome. Se lo si desidera è anche possibile usare `ssh-keygen` e `ssh-copy-id` per assicurarsi che sia macchine virtuali possono comunicare tramite SSH senza richiedere una password.

### <a name="setting-up-drbd"></a>La configurazione di DRBD

Verrà creata una risorsa DRBD che utilizza sottostante `/dev/sdc1` partizione per produrre un `/dev/drbd1` risorse possono essere formattate utilizzando ext3 e utilizzati nei nodi principali e secondari. A tale scopo, aprire `/etc/drbd.d/r0.res` e copiare la definizione di risorse seguenti. Eseguire questa operazione entrambi macchine virtuali.

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Dopo questa operazione, inizializzare risorsa tramite `drbdadm` in entrambe le macchine virtuali:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

E infine su primario (`hadb01`) forzare la proprietà (principale) della risorsa DRBD:

    sudo drbdadm primary --force r0

Se si esamina il contenuto di/Process/drbd (`sudo cat /proc/drbd`) su entrambe macchine virtuali, verrà visualizzato `Primary/Secondary` in `hadb01` e `Secondary/Primary` in `hadb02`, coerenti con la soluzione a questo punto. Il disco di 5 GB verrà sincronizzato tramite la rete 10.10.10.0/24 senza costi aggiuntivi per i clienti.

Una volta che viene sincronizzato il disco è possibile creare il file System in `hadb01`. Scopo di verifica è stato usato ext2 ma le istruzioni seguenti verranno creato un file System ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Montaggio della risorsa DRBD

In `hadb01` ora si è pronti per installare le risorse DRBD. Usare Debian e derivate `/var/lib/mysql` come directory di dati di MySQL. Poiché non è stato installato MySQL, verrà verrà creato nella directory e installare la risorsa DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>La configurazione di MySQL

A questo punto si è pronti per installare MySQL in `hadb01`:

    sudo apt-get install mysql-server

Per `hadb02`, sono disponibili due opzioni. È possibile installare server mysql a questo punto, creare /var/lib/mysql e riempirla con una nuova directory di dati, e quindi procedere per rimuovere il contenuto. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La seconda opzione consiste nel controllo a `hadb02` e quindi installare server mysql sono (script di installazione noterà un'installazione esistente e non è disponibile)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se non si prevede di failover DRBD a questo punto, la prima opzione è più semplice sebbene probabilmente sia meno elegante. Dopo a tale scopo, è possibile iniziare a lavorare sul database MySQL. In `hadb02` (o uno di essi dei server sia attivo, in base alle DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Avviso**: questa ultima istruzione disabilita efficacemente l'autenticazione per l'utente radice in questa tabella. Questa operazione deve essere sostituita per il grado di produzione concedere istruzioni e inclusa solo titolo esemplificativo.

Inoltre, è necessario abilitare la rete per MySQL se si desidera rendere query da esterne macchine virtuali, ovvero lo scopo di questa Guida. In entrambi macchine virtuali, aprire `/etc/mysql/my.cnf` e passare alla `bind-address`, modificandolo da 127.0.0.1 a 0.0.0.0. Dopo aver salvato il file, eseguire una `sudo service mysql restart` nel server principale corrente.

### <a name="creating-the-mysql-load-balanced-set"></a>Creazione di carico MySQL bilanciato Set

Si verrà tornare al portale e individuare il `hadb01` macchine Virtuali e quindi i punti finali. Si verrà creare un nuovo Endpoint, scegliere MySQL (TCP 3306) dal menu a discesa e dei segni di graduazione nella casella *Crea nuovo bilanciato set* . Applicherà l'endpoint di bilanciamento del carico `lb-mysql`. Lasciare la maggior parte delle opzioni solo ad eccezione di tempo che è necessario ridurre a 5 (minimi secondi)

Dopo aver creato l'endpoint è passare a `hadb02`, endpoint e creare un nuovo endpoint ma verrà scelto `lb-mysql`, quindi selezionare MySQL dal menu a discesa. È anche possibile usare CLI Azure per questo passaggio.

In questo momento sono tutto quello che è necessario per un'operazione manuale del cluster.

### <a name="testing-the-load-balanced-set"></a>Test il carico bilanciato set

Test possono essere eseguiti da un computer esterno, con qualsiasi client MySQL, nonché applicazioni (ad esempio, phpMyAdmin in esecuzione come un sito Web Azure) In questo caso è stato usato strumento riga di MySQL di comando in un'altra casella Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manualmente non funziona su

È possibile simulare failover ora arresto MySQL, passando principale del DRBD e avviare nuovamente MySQL.

In hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Scegliere hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Una volta che si failover manualmente è possibile ripetere la query remota ed è deve funziona perfettamente.

## <a name="setting-up-corosync"></a>La configurazione di Corosync

Corosync è infrastruttura cluster necessario per Pacemaker per l'uso. Per gli utenti v1 e v2 Heartbeat (e altri metodi come Ultramonkey) Corosync è una divisione di funzionalità CRM mentre Pacemaker rimane molto simile della trasmissione degli heartbeat funzionalità.

Il vincolo principale per Corosync in Azure è Corosync indica che preferisce multicast per la trasmissione di comunicazioni unicast, ma la rete di Microsoft Azure supporta solo unicast.

L'unico vincolo reale è che, dal momento che tutti i nodi non comunicazione tra loro *automaticamente*, è necessario definire i nodi nei file di configurazione, inclusi gli indirizzi IP per fortuna Corosync ha una modalità unicast di lavoro. È possibile utilizzare i file di esempio Corosync per Unicast e modificare associare indirizzo, elenchi di nodi e directory di registrazione (Ubuntu utilizza `/var/log/corosync` durante l'utilizzo di file nell'esempio `/var/log/cluster`) e attivare strumenti di base.

**Nota il `transport: udpu` direttiva riportata di seguito e gli indirizzi IP definiti manualmente per i nodi**.

In `/etc/corosync/corosync.conf` per entrambi i nodi:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Abbiamo copiare il file di configurazione in entrambe le macchine virtuali e avviare Corosync in entrambi i nodi:

    sudo service start corosync

Subito dopo l'avvio del servizio stabilire il cluster di inoltrare la chiamata corrente e deve essere costituito base. Poter controllare questa funzionalità per l'analisi dei registri o:

    sudo corosync-quorumtool –l

Effettuare un output simile all'immagine riportata di seguito:

![output di esempio corosync quorumtool - l](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>La configurazione Pacemaker

Pacemaker utilizza cluster per verificare la presenza di risorse, definire quando scendere i colori e scegliere le risorse dei secondari. Risorse possono essere definite da un set di script disponibili o da script (inizializzazione like) LSB, tra le altre opzioni.

Vogliamo Pacemaker "proprietario per" la risorsa DRBD, il punto di montaggio e il servizio MySQL. Se il Pacemaker possibile attivare e disattivare DRBD, installarlo / umount it e inizio/fine MySQL nell'ordine corretto quando destinati errate succede con principale, la configurazione è stata completata.

Durante l'installazione di Pacemaker la configurazione deve essere semplice, ad esempio:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Verificare eseguendo `sudo crm configure show`. A questo punto, creare un file (ad esempio, `/tmp/cluster.conf`) con le risorse seguenti:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

E caricarlo a questo punto la configurazione (è sufficiente eseguire questa operazione in un nodo):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Inoltre, assicurarsi che il Pacemaker inizi all'avvio in entrambi i nodi:

    sudo update-rc.d pacemaker defaults

Dopo alcuni secondi e l'utilizzo di `sudo crm_mon –L`, verificare che uno dei nodi è diventato lo schema per il cluster ed esegue tutte le risorse. È possibile utilizzare montaggio e ps per verificare che le risorse sono in esecuzione.

Nella schermata seguente `crm_mon` con un nodo arrestato (Esci usando CTRL + C)

![nodo crm_mon arrestato](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

E questa schermata Mostra entrambi i nodi con uno schema e una secondaria:

![crm_mon operativi principale/secondario](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Test

Si è pronti per una simulazione failover automatico. Esistono due modi per eseguire questa operazione: contorni e disco rigido. Il modo contorni consiste nell'usare la funzione di arresto del cluster: ``crm_standby -U `uname -n` -v on``. Utilizzando questo schema secondaria avranno sulle. Ricordare impostare di nuovo su No (crm_mon che indica un nodo è in standby in caso contrario)

Sul disco rigido modo arrestare la macchina virtuale principale (hadb01) tramite il portale o modifica /RL. nella macchina virtuale (ad esempio, interruzione, arresto), quindi aiutiamo Corosync e Pacemaker mediante la segnalazione corso del master verso il basso. È possibile verificare questo (utile per le finestre di manutenzione), ma è anche possibile imporre lo scenario bloccando solo la macchina virtuale.

## <a name="stonith"></a>STONITH

Dovrebbe essere possibile emettere un arresto macchine Virtuali tramite CLI Azure al posto di uno script STONITH che controlla un dispositivo fisico. È possibile utilizzare `/usr/lib/stonith/plugins/external/ssh` come base e Abilita STONITH nella configurazione del cluster. Azure CLI deve essere installato a livello globale e pubblica impostazioni/profilo devono essere caricato per utente del cluster.

Esempi di codice per la risorsa disponibile in [GitHub](https://github.com/bureado/aztonith). È necessario modificare la configurazione del cluster aggiungendo le operazioni seguenti per `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Nota:** non esegue lo script crescenti/decrescenti controlli. La risorsa SSH originale aveva 15 controlli ping ma tempi di ripristino una macchina virtuale Azure potrebbero non essere più variabile.

## <a name="limitations"></a>Limitazioni

Applicano le limitazioni seguenti:

- Script di risorsa DRBD linbit che gestisce DRBD come risorsa usi Pacemaker `drbdadm down` quando arresto un nodo, anche se il nodo appena agirà standby. Questa operazione è ideale perché secondaria verrà non essere sincronizzati la risorsa DRBD mentre lo schema Ottiene scrive. Se lo schema non hanno esito negativo generosamente, secondaria subentrare stato file System meno recente. Esistono due modi possibili per risolvere questo:
  - Applica una `drbdadm up r0` in tutti i nodi tramite un controllo locale (non clusterized), oppure
  - Modifica linbit DRBD script assicurandosi che `down` non viene chiamato, in `/usr/lib/ocf/resource.d/linbit/drbd`.
- Bilanciamento del carico deve almeno 5 secondi per rispondere, in modo che devono essere abilitate cluster o essere tolleranza di errore di timeout; altre architetture consente inoltre, ad esempio in app code, middlewares query e così via.
- Ottimizzazione MySQL è necessario per assicurare la scrittura viene eseguita a una velocità ha ottenuto moltissime e cache vengono trasferite su disco più spesso possibile per ridurre al minimo la perdita di memoria
- Scrivere le prestazioni saranno dipendenti in macchine Virtuali collegamento nell'opzione virtuale come meccanismo utilizzato da DRBD replicare il dispositivo
