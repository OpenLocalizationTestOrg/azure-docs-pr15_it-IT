<properties
 pageTitle="Eseguire OpenFOAM con Service Pack HPC in macchine virtuali Linux | Microsoft Azure"
 description="Distribuire un cluster di Microsoft HPC Pack su Azure ed eseguire un processo OpenFOAM su più nodi di calcolo di Linux in una rete RDMA."
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
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Eseguire OpenFoam con Microsoft HPC Pack in un cluster Linux RDMA in Azure

Questo articolo descrive un modo per eseguire OpenFoam in macchine virtuali di Azure. In questo caso, si distribuisce un cluster di Microsoft HPC Pack con nodi di calcolo Linux su Azure ed eseguire un [OpenFoam](http://openfoam.com/) processo con Intel MPI. È possibile usare in grado di RDMA macchine virtuali di Azure i nodi di calcolo, in modo che i nodi di elaborazione comunicano tramite la rete RDMA Azure. Altre opzioni per l'esecuzione OpenFoam in Azure includono completamente configurato le immagini commerciale disponibile sul mercato, ad esempio [2.3 OpenFoam su CentOS 6 di UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)ed eseguendo nel [Batch di Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (per aprire un'operazione su campo e la modifica) è un pacchetto di software Apri origine calcolo di dinamica fluida (CFD) che è ampiamente utilizzato in Progettazione e scientifiche in commerciale e accademica organizzazioni. Include strumenti per mesh, in particolare snappyHexMesh, un mesher parallelizzata per geometrie CAD complesse e per pre- e post-elaborazione. Quasi tutti i processi eseguiti in parallelo, per consentire agli utenti di usufruire di hardware disposizione.  

Microsoft HPC Pack offre funzionalità per eseguire applicazioni in parallelo, incluse le applicazioni MPI, nei cluster di macchine virtuali di Microsoft Azure e HPC su larga scala. Pacchetto HPC supporta anche HPC Linux esecuzione delle applicazioni in Linux nodo macchine virtuali distribuite in un cluster HPC Pack calcolano. Per un'introduzione all'utilizzo di nodi di calcolo Linux con HPC Pack, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

>[AZURE.NOTE] In questo articolo viene illustrato come eseguire un carico di lavoro Linux MPI con HPC Pack. Presuppone che l'utente abbia familiarità con amministrazione del sistema Linux e all'esecuzione di carichi di lavoro di MPI nei cluster Linux. Se si utilizzano versioni di MPI e OpenFOAM diversi da quelli riportati in questo articolo, è necessario modificare alcuni passaggi di installazione e configurazione. 

## <a name="prerequisites"></a>Prerequisiti di

*   **Nodi cluster HPC Pack con Linux dotati di RDMA** - distribuzione di un cluster HPC Pack con dimensioni A8, A9, H16r o Linux H16rm nodi di calcolo utilizzando un [modello di gestione risorse di Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o uno [script di PowerShell Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Per i prerequisiti e le procedure per entrambe le opzioni, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) . Se si sceglie l'opzione di distribuzione di script di PowerShell, vedere il file di configurazione di esempio nei file di esempio alla fine di questo articolo. Utilizzare questa configurazione per distribuire un cluster di basate su Azure HPC Pack costituita da un nodo principale A8 Windows Server 2012 R2 dimensioni e 2 nodi di calcolo di dimensioni A8 SUSE Linux Enterprise Server 12. Sostituire i valori appropriati per i nomi di abbonamento e servizio. 

    **Altre informazioni da conoscere**

    *   Per i prerequisiti reti Linux RDMA in Azure, vedere [informazioni sulla serie di H e complesse macchine virtuali di serie](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Se si usa l'opzione di distribuzione di script di Powershell, distribuire tutti i nodi di elaborazione Linux all'interno del servizio di un'area di utilizzare la connessione di rete RDMA.

    *   Dopo aver distribuito i nodi Linux, connettersi tramite SSH per eseguire attività amministrative aggiuntive. Trovare i dettagli della connessione SSH per ogni VM Linux nel portale di Azure.  
        
*   **Intel MPI** - eseguire OpenFOAM su SLES 12 HPC nodi in Azure, è necessario installare il runtime Intel MPI raccolta 5 dal [sito Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 è preinstallato in immagini basate su CentOS HPC).  In un passaggio successivo, se necessario, installare Intel MPI nei nodi del calcolo Linux. Per eseguire questo passaggio, dopo aver registrato con Intel, seguire il collegamento nel messaggio di posta elettronica di conferma alla pagina web correlata. Copiare il collegamento di download per il file .tgz appropriata per la versione di Intel MPI. In questo articolo è basato su Intel MPI versione 5.0.3.048.

*   **OpenFOAM Source Pack** - Download il software OpenFOAM Source Pack per Linux dal [sito OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). In questo articolo alla versione di Source Pack 2.3.1, disponibile per il download come OpenFOAM 2.3.1.tgz. Seguire le istruzioni più avanti in questo articolo per decomprimere e compilare OpenFOAM sui nodi di calcolo Linux.

*   **EnSight** (facoltativo) - per visualizzare i risultati della simulazione OpenFOAM, scaricare e installare il programma di analisi e visualizzazione [EnSight](https://www.ceisoftware.com/download/) . Gestione delle licenze e scaricare le informazioni sono nel sito EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurare il trust reciproco tra nodi di calcolo

Esecuzione di un processo tra nodo in più nodi Linux richiede i nodi per considerare attendibile il loro (da **rsh** o **ssh**). Quando si crea cluster HPC Pack con lo script di distribuzione Microsoft HPC Pack IaaS, lo script imposta automaticamente permanente trust reciproco per specificare l'account di amministratore. Per gli utenti non amministratori creati nel dominio del cluster, è necessario impostare temporaneo trust reciproco tra i nodi quando un processo è assegnato a tali ed elimina la relazione dopo che il processo è stato completo. Per stabilire l'attendibilità per ogni utente, specificare una coppia di chiavi RSA al cluster HPC Pack viene utilizzato per la relazione di attendibilità.

### <a name="generate-an-rsa-key-pair"></a>Generare una coppia di chiavi RSA

È facile generare una coppia di chiavi RSA, che contiene una chiave pubblica e una chiave privata eseguendo il comando **ssh keygen** Linux.

1.  Accedere a un computer Linux.

2.  Eseguire il comando seguente:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Premere **INVIO** per usare le impostazioni predefinite fino al completamento il comando. Non si immette una frase. Quando viene richiesto di immettere una password, premere **INVIO**.

    ![Generare una coppia di chiavi RSA][keygen]

3.  Passare alla directory ~/.ssh directory. La chiave privata verrà archiviata in id_rsa e la chiave pubblica in id_rsa.pub.

    ![Chiavi pubbliche e private][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Aggiungere la coppia di chiavi al cluster HPC Pack
1.  Creare una connessione Desktop remoto per il nodo principale con l'account di amministratore HPC Pack (l'account di amministratore configurare durante l'esecuzione di script di distribuzione).

2. Usare le procedure standard di Windows Server per creare un account utente nel dominio di Active Directory del cluster. Sul nodo principale, ad esempio, utilizzare lo strumento utente di Active Directory e computer. Gli esempi riportati in questo articolo presuppongono che si crea un utente di dominio denominato hpclab\hpcuser.

3.  Creare un file denominato C:\cred.xml e copiare i dati della chiave RSA. File di esempio cred.xml sia posizionato alla fine di questo articolo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Aprire un prompt dei comandi e immettere il seguente comando per impostare i dati delle credenziali per l'account hpclab\hpcuser. Utilizzare il parametro **extendeddata** per passare il nome del file C:\cred.xml che è stato creato per i dati della chiave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Questo comando viene completata correttamente senza output. Dopo aver impostato le credenziali per gli account utente che è necessario eseguire processi, archiviare il file cred.xml in un percorso sicuro o eliminarla.

5.  Se si ha generato la coppia di chiavi RSA su uno dei nodi Linux, ricordarsi di eliminare le chiavi dopo aver loro utilizzo. Se HPC Pack rileva un file id_rsa esistente o id_rsa.pub, non viene impostato su trust reciproco.

>[AZURE.IMPORTANT] Non è consigliabile esecuzione di un processo Linux come amministratore cluster in un cluster condiviso, poiché viene eseguito un processo inviato da un amministratore con l'account radice nei nodi Linux. Tuttavia, un processo inviato da un utente amministratore non viene eseguito in un account utente Linux locale con lo stesso nome utente processo. In questo caso, HPC Pack configura automaticamente trust reciproco per questo utente Linux tra i nodi allocati per il processo. È possibile impostare l'utente Linux manualmente nei nodi Linux prima di eseguire il processo o HPC Pack crea automaticamente l'utente durante il processo viene inviato. Se HPC Pack crea l'utente, HPC Pack eliminato termine del processo. Per ridurre i rischi di sicurezza, HPC Pack rimuove le chiavi dopo il completamento del processo.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurare una condivisione file per i nodi Linux

A questo punto consente di impostare una condivisione di piccole e medie imprese standard per una cartella sul nodo principale. Per consentire i nodi Linux accedere a file dell'applicazione con un percorso comune, installare la cartella condivisa nei nodi Linux. Se si desidera, è possibile utilizzare un'altra opzione, ad esempio una condivisione file Azure - consigliata per molti scenari - o una condivisione NFS di condivisione file. Visualizzare file di condivisione di informazioni e procedure dettagliate per [iniziare a utilizzare Linux nodi di calcolo in un Cluster di Pack HPC in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Creare una cartella sul nodo principale e condividerlo a tutti gli utenti mediante l'impostazione dei privilegi di lettura/scrittura. Ad esempio condividere C:\OpenFOAM sul nodo principale come \\ \\SUSE12RDMA HN\OpenFOAM. In questo caso, *HN SUSE12RDMA* è il nome host del nodo principale.

2.  Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti:

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Il primo comando crea una cartella denominata /openfoam su tutti i nodi nel gruppo LinuxNodes. Il secondo comando montaggio //SUSE12RDMA-HN/OpenFOAM cartella condivisa nel nodi Linux con dir_mode e file_mode bit impostati su 777. Il *nome utente* e la *password* nel comando dovrebbe essere le credenziali dell'utente sul nodo principale.

>[AZURE.NOTE]La "\`" simbolo nel secondo comando è un simbolo di escape per PowerShell. "\`," indica che il "," (carattere virgola) fa parte del comando.

## <a name="install-mpi-and-openfoam"></a>Installare MPI e OpenFOAM

Per eseguire OpenFOAM come un processo MPI sulla rete RDMA, è necessario compilare OpenFOAM con le raccolte Intel MPI. 

Eseguire innanzitutto diversi comandi **clusrun** per installare le librerie MPI Intel (se non è già installato) e OpenFOAM nei nodi del Linux. Utilizzare la condivisione di nodo principale configurata in precedenza per condividere i file di installazione tra i nodi Linux.

>[AZURE.IMPORTANT]Questa procedura di installazione e la compilazione è illustrati esempi. È necessario conoscenza Linux dell'amministrazione del sistema per assicurarsi che le raccolte e compilatori dipendenti siano installate correttamente. Potrebbe essere necessario modificare determinate variabili di ambiente o altre impostazioni per le versioni di Intel MPI e OpenFOAM. Per informazioni dettagliate, vedere [Intel MPI raccolta della Guida all'installazione di Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) e [OpenFOAM origine Pack installazione](http://openfoam.org/download/2-3-1-source/) per l'ambiente.


### <a name="install-intel-mpi"></a>Installare Intel MPI

Salvare il pacchetto di installazione scaricato per MPI Intel (l_mpi_p_5.0.3.048.tgz in questo esempio) in C:\OpenFoam sul nodo principale in modo che i nodi Linux possono accedere al file da /openfoam. Eseguire quindi **clusrun** per installare Intel MPI library in tutti i nodi di Linux.

1.  I comandi seguenti copiare il pacchetto di installazione ed estraggono i file in /opt/intel su ciascun nodo.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Per installare Intel MPI raccolta automaticamente, utilizzare un file silent.cfg. È possibile trovare un esempio nei file di esempio alla fine di questo articolo. Posizionare il file in /openfoam cartella condivisa. Per informazioni dettagliate relative al file silent.cfg, vedere [Intel MPI raccolta della Guida all'installazione di Linux - tipo di installazione](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Verificare che si salva il file silent.cfg come file di testo con Linux riga terminazioni (solo nuova riga, non CR nuova riga). In questo modo che venga eseguita correttamente nei nodi Linux.

3.  Installare Intel MPI raccolta invisibile.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurare MPI

Per la verifica, è necessario aggiungere le seguenti linee per /etc/security/limits.conf su ognuno dei nodi Linux:


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Riavviare i nodi Linux dopo aver aggiornato il file limits.conf. Ad esempio, utilizzare il comando **clusrun** seguente:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Dopo il riavvio, assicurarsi che la cartella condivisa sia installata come /openfoam.

### <a name="compile-and-install-openfoam"></a>Compilare e installare OpenFOAM

Salvare il pacchetto di installazione scaricato per il pacchetto di origine di OpenFOAM (OpenFOAM 2.3.1.tgz in questo esempio) a C:\OpenFoam sul nodo principale in modo che i nodi Linux possono accedere al file da /openfoam. Eseguire quindi **clusrun** comandi per la compilazione OpenFOAM in tutti i nodi di Linux.


1.  Creare una cartella /opt/OpenFOAM su ciascun nodo Linux, copiare il pacchetto di origine in questa cartella ed estrarre non esiste.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Per compilare OpenFOAM con la raccolta di MPI Intel, occorre prima configurare alcuni variabili per MPI Intel e OpenFOAM. Utilizzare uno script bash chiamato settings.sh per impostare le variabili. È possibile trovare un esempio nei file di esempio alla fine di questo articolo. Posizionare il file (salvato con terminazioni Linux) in /openfoam cartella condivisa. Il file contiene anche impostazioni per i runtime MPI e OpenFOAM utilizzati in un secondo momento per eseguire un processo OpenFOAM.

3. Installare pacchetti dipendenti necessari per la compilazione di OpenFOAM. A seconda del distribuzione Linux, è prima di tutto potrebbe essere necessario aggiungere un archivio. Eseguire comandi **clusrun** simili al seguente:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Se necessario, SSH a ciascun nodo Linux per eseguire i comandi per confermare che consentono di eseguire in modo corretto.

4.  Eseguire il seguente comando per la compilazione OpenFOAM. Il processo di compilazione richiede un certo tempo per completare e genera una grande quantità di informazioni del log di output standard, quindi usare l'opzione **/ alternati** per visualizzare l'output alternato.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]La "\`" symbol nel comando è un simbolo di escape per PowerShell. "\`&" indica che il "&" è una parte del comando.

## <a name="prepare-to-run-an-openfoam-job"></a>Preparare l'esecuzione di un processo OpenFOAM

Ora come configurare pronto per l'esecuzione di un processo MPI denominato sloshingTank3D, che corrisponde a uno degli esempi OpenFoam, su due nodi Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurare l'ambiente di runtime

Per configurare gli ambienti di runtime per MPI e OpenFOAM nei nodi Linux, eseguire il comando seguente in una finestra di Windows PowerShell sul nodo principale. (Questo comando è valido per SUSE Linux solo.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Preparare i dati di esempio

Utilizzare la condivisione di nodo principale che è configurato in precedenza per condividere i file tra i nodi Linux (mount come /openfoam).

1.  SSH a uno del Linux nodi di calcolo.

2.  Eseguire il seguente comando per impostare l'ambiente di runtime OpenFOAM, se non è già fatto.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiare il codice di esempio sloshingTank3D alla cartella condivisa e spostarsi su di esso.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Quando si utilizzano i parametri predefiniti di questo esempio, può essere necessaria decine di minuti per l'esecuzione, pertanto è consigliabile modificare alcuni parametri per renderlo di velocità di esecuzione. Una soluzione semplice consiste nel modificare il tempo passaggio variabili deltaT e writeInterval nel file system/controlDict. Questo file contiene tutti i dati di input relativi al controllo del tempo e leggere e scrivere dati di soluzioni. Ad esempio, è possibile modificare il valore di deltaT da 0.05 allo 0,5 e il valore di writeInterval da 0.05 a 0,5.

    ![Modificare le variabili di passaggio][step_variables]

5.  Specificare i valori desiderati per le variabili nel file system/decomposeParDict. In questo esempio vengono utilizzati due Linux nodi ogni con 8 core, pertanto impostare numberOfSubdomains 16 e n di hierarchicalCoeffs a (1 1 16), ovvero eseguita OpenFOAM in parallelo con i processi di 16. Per ulteriori informazioni, vedere [OpenFOAM manuale dell'utente: 3,4 applicazioni in esecuzione in parallelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Scomporre processi][decompose]

6.  Eseguire i comandi seguenti dalla directory sloshingTank3D per preparare i dati di esempio.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Sul nodo principale, verranno visualizzati che i file di dati di esempio vengono copiati in C:\OpenFoam\sloshingTank3D. (C:\OpenFoam è la cartella condivisa sul nodo principale).

    ![File di dati sul nodo principale][data_files]

### <a name="host-file-for-mpirun"></a>File di host per mpirun

In questo passaggio si crea un file di host (un elenco dei nodi di calcolo) che utilizza il comando **mpirun** .

1.  In uno dei nodi Linux, creare un file denominato file host in /openfoam, in modo che il file raggiungibili /openfoam/hostfile su tutti i nodi di Linux.

2.  Scrivere i nomi dei nodi Linux in questo file. In questo esempio, il file contiene i nomi seguenti:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]È anche possibile creare il file in C:\OpenFoam\hostfile sul nodo principale. Se si sceglie questa opzione, salvarlo come file di testo con Linux terminazioni riga (solo nuova riga, non CR nuova riga). In questo modo che venga eseguita correttamente nei nodi Linux.

    **Bash script spaziale**

    Se si dispongano di molti nodi Linux e si desidera che il processo per l'esecuzione in solo alcuni di essi, non è consigliabile utilizzare un file fisso host, perché non si sa quale nodi da allocare per il lavoro. In questo caso, scrivere un spaziale script bash per **mpirun** creare il file di host automaticamente. È possibile trovare un spaziale di script bash esempio denominato hpcimpirun.sh alla fine di questo articolo e salvarlo come /openfoam/hpcimpirun.sh. Questo script di esempio vengono eseguite le operazioni seguenti:

    1.  Configura automaticamente le variabili di ambiente per **mpirun**e alcuni parametri di comando di addizione per eseguire il processo MPI tramite la rete RDMA. In questo caso, imposta le variabili seguenti:

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = ib0 di v2 necessarie di un
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Crea un file host secondo l'ambiente variabile $CCP_NODES_CORES, l'impostazione è il nodo principale HPC durante il processo è attivato.

        Il formato di $CCP_NODES_CORES segue questo modello:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        dove

        * `<Number of nodes>`-il numero dei nodi assegnata a questo processo.  
        
        * `<Name of node_n_...>`-il nome di ogni nodo assegnata a questo processo.
        
        * `<Cores of node_n_...>`-il numero di core nel nodo assegnata a questo processo.

        Se, ad esempio, il processo richiede due nodi per l'esecuzione, $CCP_NODES_CORES è simile a
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Chiama il comando **mpirun** e aggiunge due parametri alla riga di comando.

        * `--hostfile <hostfilepath>: <hostfilepath>`-il percorso del file Hosts lo script crea

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-una variabile di ambiente l'impostazione di nodo principale HPC Pack, in cui sono archiviati il numero di core totale assegnata a questo processo. In questo caso, specifica il numero di processi per **mpirun**.


## <a name="submit-an-openfoam-job"></a>Inviare un processo OpenFOAM

A questo punto è possibile inviare un processo HPC Cluster Manager. È necessario passare hpcimpirun.sh script nelle righe di comandi per alcune attività processo.

1. Connettere il nodo principale cluster e avviare HPC Cluster Manager.

2. **In Gestione risorse**, assicurarsi che i nodi di elaborazione Linux lo stato **Online** . Se non si trovano, selezionarle e fare clic su **Connetti**.

3.  In **Gestione del processo**, fare clic su **Nuovo processo**.

4.  Immettere un nome per il processo, ad esempio _sloshingTank3D_.

    ![Dettagli dei processi][job_details]

5.  In **risorse di processo**, scegliere il tipo di risorsa come "Nodo" e impostare il valore minimo a 2. Questa configurazione viene eseguito il processo in due nodi di Linux, ognuno con otto core in questo esempio.

    ![Risorse di processo][job_resources]

6. Fare clic su **Modifica attività** nella barra di spostamento a sinistra e quindi fare clic su **Aggiungi** per aggiungere un'attività per il processo. Aggiungere quattro attività per il processo con il comando righe e le impostazioni seguenti.

    >[AZURE.NOTE]Esecuzione di `source /openfoam/settings.sh` configura automaticamente gli ambienti di runtime OpenFOAM e MPI, in modo che tutte le operazioni seguenti chiami prima del comando OpenFOAM.

    *   **Attività 1**. Eseguire **decomposePar** per generare i file di dati per l'esecuzione di **interDyMFoam** in parallelo.
    
        *   Assegnare un nodo all'attività

        *   **Riga di comando** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Cartella di lavoro** - / openfoam/sloshingTank3D
        
        Vedere la figura seguente. Configurare le attività rimanenti in modo analogo.

        ![Dettagli attività 1][task_details1]

    *   **Attività 2**. Eseguire **interDyMFoam** in parallelo per calcolare il campione.

        *   Due nodi per assegnare all'attività

        *   **Riga di comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Cartella di lavoro** - / openfoam/sloshingTank3D

    *   **Attività 3**. Eseguire **reconstructPar** per unire la serie di directory ora da ciascuna directory processor_N_ in un'unica serie.

        *   Assegnare un nodo all'attività

        *   **Riga di comando** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Cartella di lavoro** - / openfoam/sloshingTank3D

    *   **Attività 4**. Eseguire **foamToEnsight** in parallelo per convertire i file di risultato OpenFOAM nel formato EnSight e i file EnSight nella directory Ensight nella directory di maiuscole e minuscole.

        *   Due nodi per assegnare all'attività

        *   **Riga di comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Cartella di lavoro** - / openfoam/sloshingTank3D

6.  Aggiungere le dipendenze a queste attività in attività ordine crescente.

    ![Relazioni tra attività][task_dependencies]

7.  Fare clic su **Invia** per eseguire il processo.

    Per impostazione predefinita, HPC Pack invia il processo come account connesso corrente. Dopo aver fatto clic **Invia**, che appare una finestra di dialogo che richiede di immettere il nome utente e la password.

    ![Credenziali di processo][creds]

    In alcune condizioni HPC Pack memorizza le informazioni utente prima di input e non visualizzare questa finestra di dialogo. Per rendere HPC Pack visualizzarla nuovamente, immettere il comando seguente al prompt dei comandi e quindi inviare il processo.

    ```
    hpccred delcreds
    ```

8.  Il processo consente di accedere da decine di minuti a diverse ore in base ai parametri che per il campione è stata impostata. In mappa termica, viene visualizzato il processo in esecuzione su nodi Linux. 

    ![Mappa termica][heat_map]

    In ogni nodo vengono avviati otto processi.

    ![Processi Linux][linux_processes]

9.  Al termine del processo, è possibile trovare i risultati di processo in cartelle C:\OpenFoam\sloshingTank3D e i file di log in C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Visualizzare i risultati nel EnSight

Se si desidera utilizzare [EnSight](https://www.ceisoftware.com/) per visualizzare e analizzare i risultati del processo di OpenFOAM. Per ulteriori informazioni sulla visualizzazione e animazione in EnSight, vedere questa [Guida video](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Dopo aver installato EnSight sul nodo principale, avviarlo.

2.  Aprire C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Viene visualizzato un serbatoio della benzina nel Visualizzatore.

    ![Serbatoio della benzina in EnSight][tank]

3.  Creare un **Isosurface** da **internalMesh**e quindi scegliere la variabile **alpha_water**.

    ![Creare un isosurface][isosurface]

4.  Impostare il colore di **Isosurface_part** creato nel passaggio precedente. Ad esempio, impostarlo su acqua blu.

    ![Modificare il colore di isosurface][isosurface_color]

5.  Creare un **volume Iso** da **muri** selezionando **pareti** nel riquadro **parti** e fare clic sul pulsante **Isosurfaces** sulla barra degli strumenti.

6.  Nella finestra di dialogo selezionare **tipo** come **Isovolume** e impostare il valore minimo **dell'intervallo Isovolume** allo 0,5. Per creare il isovolume, fare clic su **Crea le parti selezionate**.

7.  Impostare il colore di **Iso_volume_part** creato nel passaggio precedente. Ad esempio, impostarlo su acqua completa blu.

8.  Impostare il colore di **muri**. Ad esempio, impostarlo su nero trasparente.

9. Fare clic su **Riproduci** per visualizzare i risultati della simulazione.

    ![Risultato serbatoio della benzina][tank_result]

## <a name="sample-files"></a>File di esempio

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>File di configurazione XML di esempio per la distribuzione di cluster da script di PowerShell

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>File di silent.cfg di esempio per installare MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Script di esempio settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Script di esempio hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
