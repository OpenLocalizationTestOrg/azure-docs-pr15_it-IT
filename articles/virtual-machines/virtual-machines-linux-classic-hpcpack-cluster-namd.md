<properties
 pageTitle="NAMD con Microsoft HPC Pack in macchine virtuali Linux | Microsoft Azure"
 description="Distribuire un cluster di Microsoft HPC Pack su Azure ed eseguire una simulazione NAMD con charmrun su più nodi di calcolo Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Eseguire NAMD con Microsoft HPC Pack su Linux nodi di calcolo in Azure

Questo articolo descrive un modo per eseguire un carico di lavoro elevato-performance computing (HPC) Linux in macchine virtuali di Azure. Il simbolo si imposta un cluster di [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) su Azure con nodi di calcolo Linux ed eseguire una simulazione [NAMD](http://www.ks.uiuc.edu/Research/namd/) per calcolare e visualizzare la struttura di un sistema di grandi dimensioni biomolecular.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (per il programma di Dynamics molecolare Nanoscale) è un pacchetto in parallelo dynamics molecolare progettato per prestazioni elevate simulazione di sistemi di grandi dimensioni biomolecular contenenti fino a milioni di Atom. Esempi di questi sistemi includono virus, strutture di cella e proteine di grandi dimensioni. NAMD Ridimensiona a centinaia di core per simulazioni tipiche e a più di 500.000 core per simulazioni più grande.

* **Microsoft HPC Pack** offre funzionalità per eseguire HPC su larga scala e applicazioni parallele in cluster di computer locale o macchine virtuali di Azure. Ora è possibile applicazioni HPC Linux su Linux originariamente sviluppato come una soluzione per carichi di lavoro di Windows HPC, HPC Pack calcolano macchine virtuali di nodo distribuite in un cluster HPC Pack. Per un'introduzione, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

Per altre opzioni per l'esecuzione HPC Linux carichi di lavoro in Azure, vedere [risorse tecniche per batch e prestazioni elevate computing](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Prerequisiti di

* **Nodi cluster HPC Pack con Linux** - distribuzione di un cluster di HPC Pack con nodi di calcolo Linux su Azure utilizzando un [modello di gestione risorse di Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o uno [script di PowerShell Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Per i prerequisiti e le procedure per entrambe le opzioni, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Se si sceglie l'opzione di distribuzione di script di PowerShell, vedere il file di configurazione di esempio nei file di esempio alla fine di questo articolo. In questo file configura un cluster di basate su Azure HPC Pack costituita da un nodo principale di Windows Server 2012 R2 e quattro nodi di calcolo 6.6 CentOS grandi dimensioni. Personalizzare questo file in base alle esigenze per l'ambiente.


* **File di software ed esercitazione NAMD** - NAMD scarica software per Linux dal sito [NAMD](http://www.ks.uiuc.edu/Research/namd/) (registrazione necessaria). In questo articolo si basa su NAMD versione 2.10 e viene utilizzata nell'archivio [Linux-x86_64 (64 bit Intel/AMD con Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Scaricare il [file dell'esercitazione NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd)anche. I download sono file con estensione tar sono necessari uno strumento di Windows per estrarre i file sul nodo principale cluster. Per estrarre i file, seguire le istruzioni più avanti in questo articolo. 

* **VMD** (facoltativo) - per visualizzare i risultati del processo di NAMD, scaricare e installare il programma di visualizzazione molecolare [VMD](http://www.ks.uiuc.edu/Research/vmd/) in un computer di propria scelta. La versione corrente non è 1.9.2. Vedere la VMD sito informazioni introduttive per il download.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurare il trust reciproco tra nodi di calcolo
Esecuzione di un processo tra nodo in più nodi Linux richiede i nodi per considerare attendibile il loro (da **rsh** o **ssh**). Quando si crea cluster HPC Pack con lo script di distribuzione Microsoft HPC Pack IaaS, lo script imposta automaticamente permanente trust reciproco per specificare l'account di amministratore. Per gli utenti non amministratori creati nel dominio del cluster, è necessario configurare temporaneo trust reciproco tra i nodi quando un processo allocato ad essi. Quindi, eliminare la relazione dopo il processo è stato completo. Per eseguire queste operazioni per ogni utente, specificare una coppia di chiavi RSA a cluster HPC Pack utilizzato per stabilire la relazione di attendibilità. Seguono le istruzioni.

### <a name="generate-an-rsa-key-pair"></a>Generare una coppia di chiavi RSA
È facile generare una coppia di chiavi RSA, che contiene una chiave pubblica e una chiave privata eseguendo il comando **ssh keygen** Linux.

1.  Accedere a un computer Linux.

2.  Eseguire il comando seguente:

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Premere **INVIO** per usare le impostazioni predefinite fino al completamento il comando. Non si immette una frase. Quando viene richiesto di immettere una password, premere **INVIO**.

    ![Generare una coppia di chiavi RSA][keygen]

3.  Passare alla directory ~/.ssh directory. La chiave privata verrà archiviata in id_rsa e la chiave pubblica in id_rsa.pub.

    ![Chiavi pubbliche e private][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Aggiungere la coppia di chiavi al cluster HPC Pack
1.  [Connetti con Desktop remoto](virtual-machines-windows-connect-logon.md) per il nodo principale macchine Virtuali usata dal dominio delle credenziali forniti quando si distribuisce il cluster (ad esempio hpc\clusteradmin). Gestire il cluster dal nodo principale.

2. Usare le procedure standard di Windows Server per creare un account utente nel dominio di Active Directory del cluster. Sul nodo principale, ad esempio, utilizzare lo strumento utente di Active Directory e computer. Gli esempi riportati in questo articolo presuppongono che si crea un utente di dominio denominato hpcuser nel dominio di hpclab (hpclab\hpcuser).

3. Aggiungere l'utente del dominio al cluster HPC Pack come utente cluster. Per ulteriori informazioni, vedere [aggiungere o rimuovere cluster di utenti](https://technet.microsoft.com/library/ff919330.aspx).

2.  Creare un file denominato C:\cred.xml e copiare i dati della chiave RSA. È possibile trovare un esempio nei file di esempio alla fine di questo articolo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Aprire un prompt dei comandi e immettere il seguente comando per impostare i dati delle credenziali per l'account hpclab\hpcuser. Utilizzare il parametro **extendeddata** per passare il nome del file C:\cred.xml che è stato creato per i dati della chiave.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Questo comando viene completata correttamente senza output. Dopo aver impostato le credenziali per gli account utente che è necessario eseguire processi, archiviare il file cred.xml in un percorso sicuro o eliminarla.

5.  Se si ha generato la coppia di chiavi RSA su uno dei nodi Linux, ricordarsi di eliminare le chiavi dopo aver loro utilizzo. HPC Pack non impostato trust reciproco se viene rilevato un file esistente id_rsa o id_rsa.pub file.

>[AZURE.IMPORTANT] Non è consigliabile esecuzione di un processo Linux come amministratore cluster in un cluster condiviso, poiché viene eseguito un processo inviato da un amministratore con l'account radice nei nodi Linux. Un processo inviato da un utente amministratore non viene eseguito in un account utente Linux locale con lo stesso nome utente processo. In questo caso, HPC Pack configura automaticamente trust reciproco per questo utente Linux in tutti i nodi allocati per il processo. È possibile impostare l'utente Linux manualmente nei nodi Linux prima di eseguire il processo o HPC Pack crea automaticamente l'utente durante il processo viene inviato. Se HPC Pack crea l'utente, HPC Pack eliminato termine del processo. Per ridurre i rischi di sicurezza, le chiavi vengono rimossi termine del processo nei nodi.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurare una condivisione file per i nodi Linux

A questo punto configurare una condivisione file di piccole e medie imprese e collegare la cartella condivisa di tutti i nodi di Linux per consentire ai nodi Linux accesso ai file NAMD con un percorso comune. Ecco i passaggi per collegare una cartella condivisa di nodo principale. Una condivisione è consigliata per distribuzioni, ad esempio CentOS 6.6 che attualmente non supportano il servizio di Azure File. Se i nodi Linux supportano una condivisione File di Azure, vedere [come utilizzare l'archiviazione di File di Azure con Linux](../storage/storage-how-to-use-files-linux.md). Per ulteriore opzioni di condivisione con HPC Pack al file, vedere [Introduzione a Linux nodi di calcolo in un Cluster di Pack HPC in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Creare una cartella sul nodo principale e condividerlo a tutti gli utenti mediante l'impostazione dei privilegi di lettura/scrittura. In questo esempio, \\ \\CentOS66HN\Namd è il nome della cartella in cui CentOS66HN è il nome host del nodo principale.

2. Creare una sottocartella denominata namd2 nella cartella condivisa. In namd2, creare un'altra sottocartella denominata namdsample.

3. Estrarre i file NAMD nella cartella tramite una versione di Windows di **tar** o un'altra utilità di Windows che opera su estensione tar archivi. 
    * Estrarre l'archivio tar NAMD in \\ \\CentOS66HN\Namd\namd2.
    
    * Estrarre i file dell'esercitazione in \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per collegare la cartella condivisa di nodi Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Il primo comando crea una cartella denominata /namd2 su tutti i nodi nel gruppo LinuxNodes. Il secondo comando Collega //CentOS66HN/Namd/namd2 cartella condivisa nella cartella con dir_mode e file_mode bit impostati su 777. Il *nome utente* e la *password* nel comando dovrebbe essere le credenziali dell'utente sul nodo principale.

>[AZURE.NOTE]La "\`" simbolo nel secondo comando è un simbolo di escape per PowerShell. "\`," indica che il "," (carattere virgola) fa parte del comando.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Creare uno script Bash per eseguire un processo NAMD

Il processo NAMD richiede un file *nodelist* per **charmrun** determinare il numero dei nodi da utilizzare all'avvio di processi NAMD. Utilizzare uno script Bash che genera il file nodelist ed esegue **charmrun** con questo file nodelist. È quindi possibile inviare un processo NAMD HPC Cluster Manager che richiama questo script.

Editor di testo di propria scelta, creare uno script Bash nella cartella /namd2 contenente i file di programma NAMD e denominarla hpccharmrun.sh. Per una rapida modello di prova, copiare lo script di esempio hpccharmrun.sh fornito alla fine di questo articolo e passare alla [Invia un processo NAMD](#submit-a-namd-job).

>[AZURE.TIP] Salvare lo script come file di testo con Linux riga terminazioni (solo nuova riga, non CR nuova riga). In questo modo che venga eseguita correttamente nei nodi Linux.

Ecco i dettagli che cosa significa questo script bash. 

1.  Definire alcune variabili.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  È possibile ottenere le informazioni sul nodo dalle variabili di ambiente. $NODESCORES archivia un elenco di parole diviso da $CCP_NODES_CORES. $COUNT è la dimensione della $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    Il formato per la variabile CCP_NODES_CORES $ è come indicato di seguito:

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Questa variabile sono elencati il numero totale di nodi, i nomi dei nodi e numero di core su ciascun nodo che sono assegnate al processo. Ad esempio, se il processo deve 10 core per l'esecuzione, il valore di $CCP_NODES_CORES è simile a:

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Se non è impostata la variabile CCP_NODES_CORES $, iniziare **charmrun** direttamente. (Questo deve solo verificarsi quando si esegue questo script direttamente nei nodi del Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  O creare un file nodelist per **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Eseguire **charmrun** con il file nodelist, ottenerne lo stato restituito e rimuovere il file nodelist alla fine.

    ${CCP_NUMCPUS} è un'altra variabile di ambiente l'impostazione di nodo principale HPC Pack. Archivia il numero di core totale assegnata a questo processo. Abbiamo usarla per specificare il numero dei processi per charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Uscire da con lo stato restituito **charmrun** .

    ```
    exit ${RTNSTS}
    ```



Di seguito è le informazioni nel file di nodelist, lo script genera:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Per esempio:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Inviare un processo NAMD

A questo punto si è pronti a inviare un processo NAMD HPC Cluster Manager.

1.  Connettere il nodo principale cluster e avviare HPC Cluster Manager.

2.  In **Gestione risorse**, assicurarsi che i nodi di elaborazione Linux lo stato **Online** . Se non si trovano, selezionarle e fare clic su **Connetti**.

2.  In **Gestione del processo**, fare clic su **Nuovo processo**.

3.  Immettere un nome per il processo, ad esempio *hpccharmrun*.

    ![Nuovo processo HPC][namd_job]

4.  Nella pagina **Dettagli dei processi** , in **Risorse di processo**, selezionare il tipo di risorsa come **nodo** e impostare il **valore minimo** su 3. , viene eseguito il processo in tre nodi Linux e ogni nodo dispone di quattro core.

    ![Risorse di processo][job_resources]

5. Fare clic su **Modifica attività** nella barra di spostamento a sinistra e quindi fare clic su **Aggiungi** per aggiungere un'attività per il processo.    


6. Nella pagina **Dettagli attività e il reindirizzamento i/o** impostare i valori seguenti:

    * **Riga di comando** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] Riga di comando precedente è un singolo comando senza interruzioni di riga. Viene inviato a capo per visualizzare su più righe in una **riga di comando**.

    * **Cartella di lavoro** - /namd2

    * **Minimo** - 3

    ![Dettagli attività][task_details]

    >[AZURE.NOTE] Impostare la cartella di lavoro qui perché **charmrun** tenta di spostarsi tra la stessa cartella di lavoro su ciascun nodo. Se la cartella di lavoro non è impostata, HPC Pack viene avviato il comando in una cartella denominata in modo casuale creata in uno dei nodi Linux. In questo modo l'errore seguente sugli altri nodi: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` per evitare questo problema, specificare un percorso accessibile da tutti i nodi come la cartella di lavoro.

5.  Fare clic su **OK** e quindi fare clic su **Invia** per eseguire il processo.

    Per impostazione predefinita, HPC Pack invia il processo come account connesso corrente. Finestra di dialogo di essere richiesto di immettere il nome utente e la password dopo che si fa clic su **Invia**.

    ![Credenziali di processo][creds]

    In alcune condizioni HPC Pack memorizza le informazioni utente prima di input e non visualizzare questa finestra di dialogo. Per rendere HPC Pack visualizzarla nuovamente, immettere il comando seguente al Prompt dei comandi e quindi inviare il processo.

    ```command
    hpccred delcreds
    ```

6.  Il processo richiede alcuni minuti alla fine.

7.  Trovare il log di processi in \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log e i file di output in \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Facoltativamente, è possibile avviare VMD per visualizzare i risultati del processo. La procedura per la visualizzazione di NAMD output file (in questo caso un ubiquitin proteine molecola in una sfera acqua) non rientrano nell'ambito di questo articolo. Per informazioni dettagliate, vedere [Esercitazione NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .

    ![Risultati dei processi][vmd_view]

## <a name="sample-files"></a>File di esempio

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>File di configurazione XML di esempio per la distribuzione di cluster da script di PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>File di esempio cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Script di esempio hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
