<properties
 pageTitle="Cluster Linux RDMA per l'esecuzione di applicazioni MPI | Microsoft Azure"
 description="Creare un cluster Linux di dimensioni H16r, H16mr, A8 o A9 macchine virtuali di usare la rete RDMA Azure per eseguire le applicazioni MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI


Informazioni su come configurare un cluster Linux RDMA in Azure con [macchine virtuali di serie di una serie di H o complesse](virtual-machines-linux-a8-a9-a10-a11-specs.md) per l'esecuzione di applicazioni parallele messaggio passando Interface (MPI). In questo articolo vengono illustrati i passaggi per preparare un'immagine da eseguire MPI Intel in un cluster di HPC Linux. Sarà quindi possibile distribuire un cluster di macchine virtuali mediante questa immagine e uno dei formati di macchine Virtuali di Azure dotati di RDMA (attualmente H16r, H16mr, A8 o A9). Utilizzo del cluster per l'esecuzione di applicazioni MPI che comunicano in modo efficiente in una latenza bassa, rete alta velocità basata su remoto accesso diretto alla memoria tecnologia (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Opzioni di distribuzione cluster

Di seguito sono metodi utilizzabili per creare un cluster Linux RDMA con o senza un'utilità di pianificazione del processo.


* **Script di azure CLI** - come illustrato in seguito in questo articolo utilizzare l' [interfaccia della riga di Azure](../xplat-cli-install.md) (CLI) per la distribuzione di un cluster di macchine virtuali RDMA dotati di script. I nodi cluster CLI in modalità di gestione dei servizi crea seriale nel modello di distribuzione classica, in modo che la distribuzione di molti nodi di calcolo potrebbe richiedere alcuni minuti. Per attivare la connessione di rete RDMA quando si usa il modello di distribuzione classico, distribuirle nel servizio cloud stesso.

* **Modelli di gestione risorse di azure** , è anche possibile utilizzare il modello di distribuzione di Manager delle risorse per distribuire un cluster di macchine virtuali in grado di RDMA che si connette alla rete RDMA. È possibile [creare un modello personalizzato](../resource-group-authoring-templates.md), o archiviare i [modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/) per i modelli fornito da Microsoft o alla community di distribuire la soluzione desiderata. Modelli di Manager delle risorse possono fornire un modo veloce e affidabile per distribuire un cluster Linux. Per attivare la connessione di rete RDMA quando si usa il modello di distribuzione di Manager delle risorse, distribuirle nello stesso set di disponibilità.

* **HPC Pack** - creare un cluster di Microsoft HPC Pack in Azure e aggiungere i nodi di calcolo in grado di RDMA che eseguono una distribuzione Linux supportata per accedere alla rete RDMA. Vedere [Guida introduttiva a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Passaggi di distribuzione di esempio nel modello classico

La procedura seguente viene illustrato come utilizzare CLI Azure per distribuire una macchina virtuale HPC di SP1 SUSE Linux Enterprise Server (SLES) 12 da Azure Marketplace, personalizzarlo e creare un'immagine personalizzata macchine Virtuali. Quindi, utilizzare l'immagine per la distribuzione di un cluster di macchine virtuali RDMA dotati di script. 

>[AZURE.TIP]Usare una procedura simile per distribuire un cluster di macchine virtuali in grado di RDMA in base alle altre immagini HPC supportati in Azure Marketplace. Alcuni passaggi possono variare leggermente, come indicato. Ad esempio MPI Intel è incluso e configurato in solo alcune di queste immagini. E se si distribuisce una macchina virtuale HPC di 12 SLES anziché una macchina virtuale HPC di SLES 12 SP1, è necessario aggiornare il driver RDMA. Per informazioni dettagliate, vedere [le istanze di complesse sulle A8, A9, A10 e A11](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Prerequisiti

*   **Computer client** , è necessario un Mac, Linux o client basato su Windows per comunicare con Azure. Per eseguire questi passaggi che si utilizza un client Linux.

*   **Abbonamento azure** , se non si dispone di una sottoscrizione, è possibile creare un [account gratuiti](https://azure.microsoft.com/free/) in solo un paio di minuti. Per cluster di grandi dimensioni, valutare la possibilità di una sottoscrizione in o altre opzioni di acquisto. 

*   **Disponibilità di dimensioni macchine Virtuali** - attualmente le dimensioni di istanza seguenti sono in grado di RDMA: H16r, H16mr, A8 e A9. Controllare [i prodotti disponibili per l'area geografica](https://azure.microsoft.com/regions/services/) disponibilità in aree geografiche Azure. 

*   **Quota core** - potrebbe essere necessario aumentare la quota di core per distribuire un cluster di macchine virtuali complesse. Ad esempio, è necessario almeno 128 core se si desidera distribuirle 8 A9 come illustrato in questo articolo. L'abbonamento potrebbe anche limitare il numero di core che è possibile distribuire in determinate famiglie di dimensioni macchine Virtuali, inclusa la serie di H. Per richiedere una quota di aumentare, [aprire una richiesta di assistenza clienti online](../azure-supportability/how-to-create-azure-support-request.md) senza costi aggiuntivi. 

*   **Azure CLI** - [installare](../xplat-cli-install.md) CLI Azure e [connettersi al proprio abbonamento Azure](../xplat-cli-connect.md) dal computer client.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Passaggio 1. Effettuare il provisioning di una macchina virtuale HPC SP1 12 SLES

Dopo avere effettuato l'accesso a Azure con CLI Azure, eseguire `azure config list` per verificare che l'output mostra la modalità di gestione dei servizi. In caso contrario, impostare la modalità eseguendo questo comando:


    azure config mode asm


Digitare quanto segue per elencare tutte le sottoscrizioni che è autorizzato a utilizzare:


    azure account list

L'abbonamento corrente viene identificato con `Current` impostato su `true`. Se l'abbonamento non è quello che si desidera utilizzare per creare il cluster, impostare la sottoscrizione appropriata Id come l'abbonamento:

    azure account set <subscription-Id>

Per visualizzare le immagini SLES 12 SP1 HPC disponibile al pubblico in Azure, eseguire un comando simile al seguente, presupponendo che le supporta l'ambiente di shell **grep**:


    azure vm image list | grep "suse.*hpc"

Eseguire il provisioning ora una macchina virtuale in grado di RDMA con un'immagine SLES 12 SP1 HPC eseguendo un comando simile al seguente:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

dove

* Le dimensioni (A9 in questo esempio) è uno dei formati di macchine Virtuali in grado di RDMA.

* Il numero di porta SSH esterno (22 in questo esempio, per impostazione predefinita SSH) è un numero di porta valido. Il numero di porta SSH interno è impostato su 22.

* Nell'area Azure specificato dalla posizione viene creato un nuovo servizio cloud. Specificare un percorso in cui è disponibile la dimensione di memoria virtuale che scelto.

* Il nome dell'immagine SLES 12 SP1 al momento può essere `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` o `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` per il supporto di priorità SUSE (applicano costi aggiuntivi).

### <a name="step-2-customize-the-vm"></a>Passaggio 2. Personalizzare la macchina virtuale

Dopo la macchina virtuale viene completato il provisioning, SSH in macchine Virtuali utilizzando la macchina virtuale indirizzo IP esterno (o nome DNS) e la porta esterna numero è configurato e personalizzarlo. Per ulteriori informazioni di connessione, vedere [come accedere a macchina virtuale in esecuzione Linux](virtual-machines-linux-mac-create-ssh-keys.md). Eseguire i comandi dell'utente che è stato configurato in macchine Virtuali, a meno che non è necessario accedere come principale per completare un'azione.

>[AZURE.IMPORTANT]Microsoft Azure non consente l'accesso principale in macchine virtuali Linux. Per ottenere accesso amministrativo quando la connessione con un account utente per la macchina virtuale, eseguire i comandi utilizzando `sudo`.

* **Aggiornamenti** - Installa aggiornamenti utilizzando **zypper**. È anche consigliabile installare l'utilità NFS. 

    >[AZURE.IMPORTANT]In una macchina di HPC virtuale SLES 12 SP1, è consigliabile non applicare gli aggiornamenti kernel, che possono causare problemi con RDMA Linux driver.

* **Intel MPI** - completare l'installazione di Intel MPI nella macchina virtuale HPC SP1 12 SLES eseguendo il comando seguente:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Memoria blocchi** - codici MPI per bloccare la memoria disponibile per RDMA, aggiungere o modificare le impostazioni seguenti nel file /etc/security/limits.conf. (È necessario l'accesso principale per modificare il file). 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Scopo di verifica, è inoltre possibile impostare memlock su illimitato. Ad esempio: `<User or group name>    hard    memlock unlimited`. Per ulteriori informazioni, vedere [Metodi ottimale per l'impostazione Dimensione memoria bloccato](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Chiavi SSH per macchine virtuali SLES** - generare SSH chiavi per stabilire l'attendibilità per l'account utente tra i nodi di elaborazione del cluster SLES durante l'esecuzione di processi MPI. (Se è stata distribuita una VM HPC basate su CentOS, non eseguire questo passaggio. Vedere le istruzioni in seguito in questo articolo per configurare la protezione SSH passwordless tra i nodi cluster dopo aver acquisire un'immagine e distribuire il cluster.) 

    Eseguire il comando seguente per creare le chiavi SSH. Quando viene chiesto di input, premere INVIO per generare le chiavi nella posizione predefinita senza impostare una frase.

        ssh-keygen

    Aggiungere la chiave pubblica al file authorized_keys per chiavi pubbliche note.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Nella directory ~/.ssh, modificare o creare il file "config". Specificare l'intervallo di indirizzi IP di rete privata che si intende utilizzare in Azure (10.32.0.0/16 in questo esempio):

        host 10.32.0.*
        StrictHostKeyChecking no

    Elencare in alternativa, l'indirizzo IP di rete privata di ogni macchina virtuale del cluster come indicato di seguito:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configurazione di `StrictHostKeyChecking no` può creare potenzialmente dannosi quando non viene specificato un indirizzo IP specifico o un intervallo.

* **Applicazioni** - installare le applicazioni è necessario in questa macchina virtuale o eseguire altre personalizzazioni prima di acquisire l'immagine.

### <a name="step-3-capture-the-image"></a>Passaggio 3. Acquisire un'immagine

Per acquisire un'immagine, eseguire il comando seguente VM Linux. Questo comando di annullamento del provisioning macchina virtuale ma mantiene gli account utente e chiavi SSH che ha configurato.

```
sudo waagent -deprovision
```

Eseguire i comandi di Azure CLI seguenti per acquisire un'immagine dal computer client. Per informazioni dettagliate, vedere [come acquisire una macchina virtuale Linux classica come immagine](virtual-machines-linux-classic-capture-image.md) .  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Dopo avere eseguito questi comandi, l'immagine di macchine Virtuali verrà acquisita per l'uso e la macchina virtuale viene eliminata. A questo punto si dispone di un'immagine personalizzata pronta per distribuire un cluster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Passaggio 4. Distribuire un cluster con l'immagine

Modificare il seguente script Bash con i valori appropriati per l'ambiente ed esecuzione dal computer client. Poiché Azure distribuisce macchine virtuali seriale nel modello di distribuzione classico, bastano pochi minuti per distribuirle l'8 A9 suggerite in questo script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerazioni per un cluster HPC CentOS

Se si desidera configurare un cluster in base a una delle immagini basate su CentOS HPC di Azure Marketplace anziché SLES 12 per HPC, seguire la procedura generale nella sezione precedente. Quando il provisioning e configurare la macchina virtuale, tenere presente le differenze seguenti:

1. Intel MPI è già installato in una macchina virtuale viene completato il provisioning da un'immagine in base CentOS HPC. 

2. Impostazioni di blocco della memoria già aggiunti nel file /etc/security/limits.conf della macchina virtuale.

2. Non generare chiavi SSH in macchine Virtuali viene effettuato il provisioning per l'acquisizione. Se, tuttavia, è consigliabile configurare l'autenticazione basata su utente dopo la distribuzione di cluser. Vedere la sezione seguente.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurare la protezione SSH passwordless sul cluster

In un cluster HPC basati su CentOS, esistono due metodi per stabilire l'attendibilità tra i nodi di elaborazione: autenticazione basata su host e l'autenticazione basata su utente. Autenticazione basata su host non rientra nell'ambito di questo articolo e in genere deve essere eseguita tramite uno script di estensione durante la distribuzione. Autenticazione basata su utente è utile per stabilire l'attendibilità dopo la distribuzione e richiede la creazione e condivisione delle chiavi SSH tra i nodi di elaborazione del cluster. Questo metodo è comunemente noto come passwordless SSH accesso ed è necessario quando MPI processi in esecuzione. 

Contribuito dalla community di uno script di esempio è disponibile in [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) per abilitare l'autenticazione utente di semplice utilizzo in un cluster HPC basate su CentOS. Scaricare e usare questo script procedendo come segue. È anche possibile modificare questo script o utilizzare qualsiasi altro metodo per stabilire passwordless autenticazione SSH tra i nodi cluster di elaborazione.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Per eseguire lo script, è necessario conoscere il prefisso per gli indirizzi IP di subnet. È possibile ottenere il prefisso eseguendo il seguente comando in uno dei nodi cluster. L'output dovrebbe essere simile alla 10.1.3.5 e il prefisso è il 10.1.3 parte.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Eseguire lo script con tre parametri: il nome utente comuni sui nodi di calcolo, la password comune per l'utente nodi di calcolo e il prefisso subnet restituite rispetto al comando precedente.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Questo script esegue le operazioni seguenti:

* Crea una directory sul nodo host denominato .ssh, necessario per l'accesso passwordless. 
* Crea un file di configurazione nella directory .ssh che indica passwordless accesso per consentire l'accesso da qualsiasi nodo del cluster. 
* Crea file contenente i nomi di nodo e indirizzi IP di nodo per tutti i nodi del cluster. Questi file vengono lasciati dopo l'esecuzione di script per riferimento futuro. 
* Crea una coppia di chiavi pubblica e privata per ogni nodo cluster incluso il nodo host e crea voci nel file authorized_keys.

>[AZURE.WARNING]Eseguire questo script, è possibile creare un potenziale rischio. Assicurarsi che le informazioni di chiave pubbliche di ~/.ssh non è state distribuite.


## <a name="configure-intel-mpi"></a>Configurare Intel MPI

Per eseguire applicazioni MPI su Azure Linux RDMA, è necessario configurare determinate variabili di ambiente specifiche per Intel MPI. Di seguito è riportato Bash uno script di configurare le variabili necessari per eseguire un'applicazione. Modificare il percorso di mpivars.sh in base alle esigenze per l'installazione di Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Il formato del file Hosts è come indicato di seguito. Aggiungere una riga per ogni nodo del cluster. Specificare gli indirizzi IP privati dal VNet definita in precedenza, non nomi DNS. Ad esempio, per due host con gli indirizzi IP 10.32.0.1 e 10.32.0.2 il file contiene le operazioni seguenti:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Eseguire MPI in un cluster di due nodi base

Se non è già stato fatto, occorre prima configurare l'ambiente per Intel MPI. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Eseguire un comando MPI semplice

Eseguire un comando MPI semplice su uno dei nodi di calcolo per mostrare che MPI sia installato correttamente e possono comunicare tra almeno che due nodi di calcolo. Il seguente comando **mpirun** esegue il comando **hostname** su due nodi.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
L'output deve elencare i nomi di tutti i nodi passato come input per `-hosts`. Ad esempio un comando **mpirun** con due nodi restituisce output simile al seguente:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Eseguire un benchmark MPI

Il comando seguente Intel MPI viene eseguita una prova comparativa pingpong per verificare la configurazione del cluster e connessione alla rete RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

In un cluster di lavoro con due nodi, è necessario vedere output simile al seguente. In rete RDMA Azure aspettarsi latenza o inferiori 3 microsecondi messaggio dimensioni fino a 512 byte.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Passaggi successivi

* Provare la distribuzione e l'esecuzione di applicazioni il MPI Linux il cluster Linux.

* Vedere la [documentazione relativa alla libreria di MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) per indicazioni su Intel MPI.

* Provare un [modello di Guida introduttiva](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) a creare un cluster di lucentezza Intel utilizzando un'immagine in base CentOS HPC. Per informazioni dettagliate, vedere questo [post di blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
