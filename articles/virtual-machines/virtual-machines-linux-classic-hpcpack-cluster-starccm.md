<properties
 pageTitle="Eseguire STELLA-CCM + con HPC Pack in macchine virtuali Linux | Microsoft Azure"
 description="Distribuire un cluster di Microsoft HPC Pack su Azure ed eseguire una STELLA-CCM + processo su Linux più nodi di calcolo in una rete RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Eseguire STELLA-CCM + con Microsoft HPC Pack su un RDMA Linux cluster Azure
In questo articolo viene illustrato come distribuire un cluster di Microsoft HPC Pack su Azure ed eseguire una [STELLA adapco CD-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) processo su più nodi di calcolo Linux interconnessi con InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack offre funzionalità per eseguire un'ampia gamma di applicazioni in parallelo, incluse le applicazioni MPI, nei cluster di macchine virtuali di Microsoft Azure e HPC su larga scala. Pacchetto HPC supporta anche applicazioni HPC Linux in esecuzione in macchine virtuali di nodo di calcolo Linux distribuiti in un cluster HPC Pack. Per un'introduzione all'utilizzo Linux nodi con HPC Pack, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurare un cluster HPC Pack
Scaricare gli script di distribuzione HPC Pack IaaS dall' [Area Download](https://www.microsoft.com/en-us/download/details.aspx?id=44949) ed estrarre loro in locale.

PowerShell Azure è un prerequisito. Se PowerShell non è configurato nel computer locale, leggere l'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Al momento della loro immagini Linux da Azure Marketplace, che contiene i driver InfiniBand per Azure, si applicano SLES 12, 6.5 CentOS e CentOS 7.1. In questo articolo si basa sull'utilizzo dei SLES 12. Per recuperare il nome di tutte le immagini di Linux che supportano HPC sul mercato, è possibile eseguire il comando PowerShell seguente:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

L'output sono elencati nella posizione in cui sono disponibili le immagini e il nome dell'immagine (**ImageName**) da utilizzare nel modello di distribuzione in un secondo momento.

Prima di distribuire il cluster, è necessario creare un file di modello di distribuzione HPC Pack. Perché la destinazione è un piccolo cluster, il nodo principale verrà controller di dominio e ospitare un database SQL locale.

Il modello seguente verrà distribuire ad esempio un nodo principale, creare un file XML denominato **MyCluster.xml**e sostituire i valori delle **SubscriptionId**, **StorageAccount**, **luogo**, **VMName**e **nome** con i propri.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Avviare la creazione di testa nodo eseguendo il comando PowerShell in una finestra del prompt:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Dopo 20-30 minuti, il nodo principale dovrebbe essere pronto. È possibile connettersi a tale dal portale di Azure facendo clic sull'icona **Connetti** della macchina virtuale.

Infine, è necessario risolvere il server d'inoltro DNS. A tale scopo, avviare Gestione DNS.

1.  Pulsante destro del mouse sul nome del server in Gestore DNS, scegliere **proprietà**e quindi fare clic sulla scheda **server d'inoltro** .

2.  Fare clic sul pulsante **Modifica** per rimuovere tutti i server d'inoltro e quindi fare clic su **OK**.

3.  Assicurarsi che sia selezionata la casella di controllo **Usa principali se nessun server d'inoltro sono disponibili** e quindi fare clic su **OK**.

## <a name="set-up-linux-compute-nodes"></a>Impostare i nodi di calcolo Linux
Distribuire i nodi di elaborazione Linux con lo stesso modello di distribuzione utilizzati per creare il nodo principale.

Copiare il file **MyCluster.xml** dal computer locale di nodo principale e aggiornare il contrassegno **NodeCount** con il numero dei nodi che si desidera distribuire (< = 20). Prestare attenzione che abbastanza core disponibili nella quota di Azure, poiché ogni istanza A9 utilizzerà 16 core nell'abbonamento. Se si desidera utilizzare più macchine virtuali nello stesso budget, è possibile utilizzare le istanze di A8 (8 core) anziché A9.

Copiare gli script di distribuzione HPC Pack IaaS sul nodo principale.

Eseguire i comandi di Azure PowerShell seguenti in un prompt dei comandi con privilegi elevato:

1.  Eseguire **Aggiungi AzureAccount** per connettersi al proprio abbonamento Azure.

2.  Se si hanno più abbonamenti, eseguire **Get-AzureSubscription** per visualizzarne l'elenco.

3.  Impostare un abbonamento predefinito eseguendo la **xxxx selezionare AzureSubscription - SubscriptionName-predefinito** comando.

4.  Eseguire **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** per avviare l'installazione Linux nodi di calcolo.

    ![Distribuzione di testa nodo in azione][hndeploy]

Aprire lo strumento di gestione di Cluster HPC Pack. Dopo alcuni minuti, nodi di calcolo Linux regolarmente verranno visualizzato nell'elenco cluster dei nodi di calcolo. Con la modalità classica distribuzione macchine virtuali IaaS vengono create in sequenza. Pertanto se il numero dei nodi è importante, il recupero loro tutti distribuito può richiedere un periodo di tempo significativo.

![Nodi Linux HPC Pack Cluster Manager][clustermanager]

Ora che tutti i nodi sono in esecuzione nel cluster, sono disponibili le impostazioni di infrastruttura aggiuntiva per.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurare una condivisione File di Azure per i nodi di Windows e Linux
È possibile utilizzare il servizio di Azure File per archiviare gli script, pacchetti di applicazioni e i file di dati. File Azure offre funzionalità CIFS nella parte superiore di archiviazione Blob Azure come archivio persistente. Tenere presente che questa non è la soluzione più scalable, ma è quello più semplice e non richiede macchine virtuali dedicate.

Creare una condivisione File Azure seguendo le istruzioni vedere l'articolo [Introduzione a spazio di archiviazione di Azure File in Windows](..\storage\storage-dotnet-how-to-use-files.md).

Mantenere il nome del proprio account di archiviazione come **saname**, il nome di condivisione file come **condivisione**e la chiave account lo spazio di archiviazione come **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Installare la condivisione di File di Azure sul nodo principale
Aprire un prompt dei comandi con privilegi elevato ed eseguire il seguente comando per archiviare le credenziali nell'archivio di computer locale:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Quindi, per installare la condivisione di File di Azure, eseguire:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Installare la condivisione di File di Azure su Linux nodi di calcolo
Uno strumento utile che viene fornito con HPC Pack è lo strumento clusrun. È possibile utilizzare questo strumento della riga di comando per eseguire il comando stesso contemporaneamente su un set di nodi di calcolo. In questo caso, viene utilizzato per installare la condivisione di File di Azure e archiviarlo per superare il riavvio.
In una finestra del prompt sul nodo principale, eseguire i comandi seguenti.

Per creare directory di montaggio:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Per installare la condivisione di File di Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Per mantenere la condivisione di montaggio:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Installare STELLA-CCM +
Azure macchine Virtuali le istanze A8 A9 forniscono supporto InfiniBand e funzionalità RDMA. I driver di kernel che consentono di queste funzionalità sono disponibili per Windows Server 2012 R2, SUSE 12, CentOS 6.5 e immagini CentOS 7.1 in Azure Marketplace. Microsoft MPI e Intel MPI (versione 5. x) sono due librerie di MPI che supportano i driver di Azure.

STELLA adapco CD-CCM + rilasciare 11 e versioni successive viene fornito con versione Intel MPI 5. x, in modo che è incluso il supporto di InfiniBand per Azure.

Ottenere il Linux64 STELLA-CCM + pacchetto dal [portale adapco CD](https://steve.cd-adapco.com). In questo caso è stato usato versione 11.02.010 precisione misto.

In condivisione File Azure **/hpcdata** , il nodo principale, creare uno script di shell denominato **setupstarccm.sh** con il seguente contenuto. Questo script viene eseguito su ogni nodo di calcolo per configurare STELLA-CCM + in locale.

#### <a name="sample-setupstarcmsh-script"></a>Script di esempio setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Ora, per configurare la STELLA-CCM + su tutti i Linux nodi di calcolo, aprire un prompt dei comandi con privilegi elevato ed eseguire il comando seguente:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Mentre il comando è in esecuzione, è possibile monitorare l'utilizzo della CPU utilizzando la mappa termica di gestione Cluster. Dopo alcuni minuti, tutti i nodi devono essere impostati correttamente.

## <a name="run-star-ccm-jobs"></a>Eseguire STELLA-CCM + processi
HPC Pack viene utilizzato per le funzionalità di utilità di pianificazione del processo per l'esecuzione STELLA-CCM + processi. A tale scopo, è necessario il supporto di alcuni script utilizzati per avviare il processo ed eseguire STELLA-CCM +. I dati di input viene mantenuti per la condivisione File Azure primo per semplicità.

Viene utilizzato il seguente script di PowerShell per accodare una STELLA-CCM + processo. Bastano tre argomenti:

*  Il nome del modello

*  Il numero dei nodi da utilizzare

*  Il numero di core su ciascun nodo da utilizzare

Poiché STELLA-CCM + possibile riempire la larghezza di banda della memoria, è preferibile usare meno core per nodi di calcolo e aggiungere nuovi nodi. Il numero esatto di core per nodo dipenderà la famiglia di processori e la velocità di interconnessione.

I nodi destinati esclusivamente il processo e non possono essere condiviso con altri processi. Il processo non è avviato come un processo MPI direttamente. Script di shell **runstarccm.sh** inizierà sul pulsante di visualizzazione MPI.

Il modello di input e lo script **runstarccm.sh** sono archiviate nella condivisione di **/hpcdata** che è stata installata in precedenza.

File di log denominati con l'ID di processo e sono archiviati in **/hpcdata condividere**, insieme a STELLA-CCM + i file di output.


#### <a name="sample-submitstarccmjobps1-script"></a>Script di esempio SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Sostituire **runner.java** con la tua STELLA preferita-pulsante di visualizzazione modello CCM + Java e il codice di registrazione.

#### <a name="sample-runstarccmsh-script"></a>Script di esempio runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Per il test è stato usato un token di licenza di Power su richiesta. Per tale token, è necessario impostare la variabile di ambiente **CDLMD_LICENSE_FILE $** **1999@flex.cd-adapco.com** e il tasto opzione **- podkey** della riga di comando.

Dopo alcuni inizializzazione, lo script estrae - dalle **CCP_NODES_CORES $** variabili di ambiente impostate HPC Pack, ossia l'elenco dei nodi per creare un file host che utilizza l'icona di avvio delle MPI. In questo file host conterrà l'elenco dei nomi dei nodi di calcolo utilizzati per il processo, un nome per riga.

Il formato di **$CCP_NODES_CORES** segue questo modello:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Dove:

* `<Number of nodes>`è il numero dei nodi assegnata a questo processo.

* `<Name of node_n_...>`è il nome di ogni nodo assegnata a questo processo.

* `<Cores of node_n_...>`è il numero di core nel nodo assegnata a questo processo.

Anche il numero di core (**$NBCORES**) viene calcolato in base al numero dei nodi (**$NBNODES**) e il numero di core per nodo (fornito come parametro **$NBCORESPERNODE**).

Per le opzioni di MPI, quelli utilizzati con MPI Intel su Azure sono:

*   `-mpi intel`Per specificare Intel MPI.

*   `-fabric UDAPL`Per utilizzare verbi InfiniBand Azure.

*   `-cpubind bandwidth,v`Per ottimizzare la larghezza di banda per MPI con STELLA-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`affinché funzioni con Azure InfiniBand Intel MPI e impostare il numero di core per nodo necessari.

*   `-batch`Per iniziare a STELLA-CCM + in modalità batch senza interfaccia utente.


Infine per avviare un processo, verificare che i nodi siano in esecuzione e online in Gestione Cluster. Da un prompt dei comandi di PowerShell eseguire questa operazione:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Interrompere nodi
In un secondo momento dopo averne completato con i test, è possibile utilizzare i comandi seguenti HPC Pack PowerShell per interrompere e avviare nodi:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Passaggi successivi
Provare a eseguire altri carichi di lavoro Linux. Ad esempio, vedere:

* [Eseguire NAMD con Microsoft HPC Pack su Linux nodi di calcolo in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
