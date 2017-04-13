<properties
 pageTitle="Configurare un cluster di Windows RDMA per l'esecuzione di applicazioni MPI | Microsoft Azure"
 description="Informazioni su come creare un cluster di Windows HPC Pack con dimensioni massime H16r, H16mr, A8 o A9 macchine virtuali di usare la rete RDMA Azure per eseguire le applicazioni MPI."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurare un cluster di Windows RDMA con HPC Pack per l'esecuzione di applicazioni MPI

Configurare un cluster di RDMA di Windows Azure con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [serie H o complesse istanze di serie](virtual-machines-windows-a8-a9-a10-a11-specs.md) per l'esecuzione di applicazioni parallele passando MPI (Message Interface). Quando si configura dotati di RDMA, basato su Windows Server nodi in un cluster HPC Pack, applicazioni MPI comunicano in modo efficiente attraverso una latenza bassa, rete alta velocità di esecuzione in Azure che si basa sulla tecnologia di accesso (RDMA) memoria diretto remoto.

Se si desidera eseguire carichi di lavoro MPI in macchine virtuali Linux che accedono alla rete RDMA Azure, vedere [configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Opzioni di distribuzione cluster HPC Pack
Microsoft HPC Pack è uno strumento forniti senza costi aggiuntivi per creare HPC cluster locale o in Azure per eseguire le applicazioni di Windows o Linux HPC. HPC Pack include un ambiente di runtime per l'implementazione Microsoft del messaggio passando Interface per Windows (MS-MPI). Se utilizzata con le istanze in grado di RDMA sistema operativo Windows Server supportati, HPC Pack fornisce un'opzione efficace per eseguire le applicazioni di Windows MPI che accedono alla rete RDMA Azure. 

In questo articolo vengono presentati due scenari e collegamenti a istruzioni dettagliate per configurare un cluster di Winodws RDMA con Microsoft HPC Pack. 

* Scenario 1. Distribuire istanze del ruolo di lavoro complesse PaaS)

* Scenario 2. Distribuire nodi di calcolo in macchine virtuali complesse (IaaS)

Per prerequisiti generali come utilizzare le istanze di complesse con Windows, vedere [informazioni sul macchine virtuali di serie di una serie di H e complesse](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenario 1. Distribuire istanze del ruolo di lavoro complesse PaaS)

Da un cluster HPC Pack esistente, aggiungere le risorse di elaborazione aggiuntive istanze del ruolo di lavoro Azure (nodi Azure) in esecuzione in un servizio cloud (PaaS). Questa caratteristica, detto anche "burst in Azure" da HPC Pack supporta un intervallo di dimensioni per le istanze di ruolo di lavoro. Quando si aggiungono i nodi Azure, è sufficiente specificare una delle dimensioni in grado di RDMA.

Ecco considerazioni e i passaggi per burst a dotati di RDMA Azure istanze da un oggetto esistente (in genere locale) cluster. Consente di aggiungere istanze del ruolo di lavoro a un nodo principale HPC Pack distribuiti in una macchina virtuale Azure procedure analoghe.

>[AZURE.NOTE] Per un'esercitazione a burst Azure con HPC Pack, vedere [configurare un cluster di ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenere presente le considerazioni nei passaggi seguenti che riguardano in particolare in grado di RDMA nodi Azure.

![Burst Azure][burst]

### <a name="steps"></a>Procedura

4. **Distribuire e configurare un nodo principale HPC Pack 2012 R2**

    Scaricare il pacchetto di installazione più recente di HPC Pack dall' [Area Download Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Per requisiti e le istruzioni per preparare l'ambiente per una distribuzione di Azure burst, vedere [HPC Pack Guida introduttiva](https://technet.microsoft.com/library/jj884144.aspx) e [Burst di Azure le istanze di lavoro con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurare un certificato di gestione nella sottoscrizione Azure**

    Configurare un certificato per proteggere la connessione tra il nodo principale e Azure. Per le opzioni e procedure, vedere [scenari per configurare il certificato di gestione di Azure per HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Per distribuzioni di prova, HPC Pack installa predefinito Microsoft HPC Azure certificato di gestione è possibile caricare rapidamente all'abbonamento Azure.

6. **Creare un nuovo servizio cloud e un account di archiviazione**

    Utilizzare il portale classico Azure per creare un servizio cloud e un account di archiviazione per la distribuzione in un'area in cui sono disponibili le istanze in grado di RDMA.

7. **Creare un modello di nodo Azure**

    Usare la creazione guidata modello nodo HPC Cluster Manager. Per istruzioni, vedere [creazione di un modello di nodo Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) in "I passaggi per distribuire Azure nodi con Microsoft HPC Pack".

    Per i test iniziali, si consiglia di configurare i criteri di disponibilità manuale nel modello.

8. **Aggiungere nodi al cluster**

    Usare l'installazione guidata nodo HPC Cluster Manager. Per ulteriori informazioni, vedere [Aggiungere Azure nodi al Cluster HPC Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Quando si specifica la dimensione dei nodi, selezionare una delle dimensioni istanza dotati di RDMA.
    
    >[AZURE.NOTE]In ogni burst alla distribuzione di Azure con le varianti complesse, HPC Pack distribuisce automaticamente un minimo di 2 istanze dotati di RDMA (ad esempio A8) come nodi proxy, oltre alle istanze di ruolo Azure lavoro specificato. I nodi proxy utilizzano core che allocati alla sottoscrizione e sostengono spese insieme le istanze di ruolo lavoro Azure.

9. **Avviare (provisioning) i nodi e visualizzarle in linea per eseguire i processi**

    Selezionare i nodi e usare l'azione **Avvia** HPC Cluster Manager. Al termine di provisioning, selezionare i nodi e usare l'azione **In linea** in HPC Cluster Manager. I nodi sono pronti per l'esecuzione di processi.

10. **Inviare i processi al cluster**

    Usare strumenti di presentazione processo HPC Pack per eseguire i processi di cluster. Vedere [Microsoft HPC Pack: gestione](http://technet.microsoft.com/library/jj899585.aspx).

11. **Interrompi (annullare l'implementazione) i nodi**

    Dopo aver processi in esecuzione, disconnettere i nodi e usare l'azione **Interrompi** HPC Cluster Manager.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenario 2. Distribuire nodi di calcolo in macchine virtuali complesse (IaaS)

In questo scenario, si distribuisce il nodo principale HPC Pack e cluster nodi in macchine virtuali a un dominio di Active Directory in una rete virtuale Azure. HPC Pack offre una gamma di [Opzioni di distribuzione in macchine virtuali di Azure](virtual-machines-linux-hpcpack-cluster-options.md), inclusa la distribuzione automatica script e modelli di Guida introduttiva di Azure. Ad esempio, le considerazioni e i passaggi descritti di seguito consentono di utilizzare lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per automatizzare la maggior parte del processo.

![Raggruppamento in macchine virtuali di Azure][iaas]



### <a name="steps"></a>Procedura

1. **Creare un nodo principale cluster e calcolare macchine virtuali di nodo eseguendo lo script di distribuzione HPC Pack IaaS in un computer client**

    Scaricare il pacchetto di Script di distribuzione IaaS HPC Pack dall' [Area Download Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Per preparare il computer client, creare il file di configurazione di script, eseguire lo script, vedere [creare un Cluster HPC con lo script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Per distribuire i nodi di calcolo in grado di RDMA, tenere presente le considerazioni seguenti:
    
    * **Rete virtuale** - specificare una nuova rete virtuale in un'area in cui è disponibile la dimensione istanza dotati di RDMA che si desidera utilizzare.

    * **Sistema operativo Windows Server** - per supportare la connettività RDMA, specificare un sistema operativo Windows Server 2012 R2 o Windows Server 2012 per il nodo di calcolo macchine virtuali.

    * **Servizi cloud** , è consigliabile distribuire il nodo principale nel servizio di un'area e i nodi di calcolo in un servizio cloud diverso.

    * **Dimensione del nodo testa** - per questo scenario, valutare la possibilità di una dimensione di almeno A4 (molto grande) per il nodo principale.

    * **Estensione HpcVmDrivers** - lo script di distribuzione viene installato l'agente di macchine Virtuali di Azure e l'estensione HpcVmDrivers automaticamente quando si distribuiscono dimensioni A8 o A9 nodi con un sistema operativo Windows Server. HpcVmDrivers installa i driver sul nodo di calcolo macchine virtuali in modo che possono essere connessi alla rete RDMA.

    * **Configurazione della rete cluster** - lo script di distribuzione configura automaticamente il cluster HPC Pack topologia 5 (tutti i nodi nella rete dell'organizzazione). Questa topologia è richiesta per tutte le distribuzioni cluster HPC Pack in macchine virtuali. Non modificare la topologia della rete cluster in un secondo momento.

2. **Visualizzare i nodi di calcolo online per l'esecuzione di processi**

    Selezionare i nodi e usare l'azione **In linea** in HPC Cluster Manager. I nodi sono pronti per l'esecuzione di processi.

3. **Inviare i processi al cluster**

    Connettere il nodo principale l'invio di processi oppure impostare un computer locale per eseguire questa operazione. Per informazioni, vedere [Inviare processi a un cluster HPC in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Disconnettere i nodi e interrompere (rilasciare) loro**

    Dopo aver processi in esecuzione, eseguire i nodi offline HPC Cluster Manager. Quindi, utilizzare gli strumenti di gestione Azure arrestare loro.



## <a name="run-mpi-applications-on-the-cluster"></a>Esecuzione di applicazioni MPI sul cluster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Esempio: Eseguire mpipingpong in un cluster di HPC Pack

Per verificare una distribuzione HPC Pack delle istanze dotati di RDMA, eseguire il comando **mpipingpong** HPC Pack sul cluster. **MPIPingPong** invia pacchetti di dati tra nodi accoppiati più volte per calcolare la latenza e le misure di velocità e le statistiche di rete applicazioni basate su RDMA. In questo esempio mostra una tipica per eseguire un processo MPI (in questo caso, **mpipingpong**) tramite il comando **mpiexec** cluster.

In questo esempio si presuppone che aggiunto Azure nodi in una configurazione "burst in Azure" ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Se è stata distribuita HPC Pack in un cluster di macchine virtuali di Azure, è necessario modificare la sintassi di comando per specificare un gruppo di nodo diverso e impostare le variabili di ambiente aggiuntive per indirizzare il traffico di rete alla rete RDMA.


Per eseguire mpipingpong sul cluster:


1. Il nodo principale o in un computer client configurato correttamente, aprire un prompt dei comandi.

2. Per valutare latenza tra coppie di nodi in una distribuzione di Azure burst di 4 nodi, digitare il seguente comando per inviare un processo per l'esecuzione mpipingpong con dimensioni di un pacchetto piccolo e un numero elevato di iterazioni:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    Il comando restituisce l'ID del processo che venga inviato.

    Se è stato distribuito cluster HPC Pack distribuito in macchine virtuali di Azure, specificare un gruppo di nodo contenente nodo macchine virtuali distribuite in un servizio cloud singolo calcolo e modificare il comando **mpiexec** come indicato di seguito:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Al termine del processo, per visualizzare l'output (in questo caso, l'output dell'attività 1 del processo), digitare quanto segue

    ```
    task view <JobID>.1
    ```

    in &lt; *ID processo* &gt; è l'ID del processo di cui è stato inviato.

    L'output includerà risultati latenza simili al seguente.

    ![Latenza pong ping][pingpong1]

4. Per valutare velocità tra coppie di nodi burst Azure, digitare il seguente comando per inviare un processo per l'esecuzione **mpipingpong** con dimensioni di un pacchetto di grandi dimensioni e un numero limitato di iterazioni:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    Il comando restituisce l'ID del processo che venga inviato.

    In un cluster di HPC Pack distribuito in macchine virtuali di Azure, modificare il comando come indicato nel passaggio 2.

5. Al termine del processo, per visualizzare l'output (in questo caso, l'output dell'attività 1 del processo), digitare quanto segue:

    ```
    task view <JobID>.1
    ```

  L'output includerà risultati di velocità simili al seguente.

  ![Produttività pong ping][pingpong2]


### <a name="mpi-application-considerations"></a>Considerazioni relative alle applicazioni MPI


Di seguito sono considerazioni per l'esecuzione di applicazioni MPI con HPC Pack in Azure. Alcuni validi solo per le distribuzioni di Azure nodi (istanze del ruolo di lavoro aggiunti in una configurazione "burst in Azure").

* Istanze del ruolo di lavoro in un servizio cloud sono periodicamente nuovo senza preavviso da Azure (ad esempio, per la manutenzione del sistema o in caso che si verifica un errore di un'istanza). Se durante l'esecuzione un processo MPI è un'istanza di nuovo, l'istanza perde i dati e ritorna allo stato quando è stata innanzitutto distribuito, che possono causare il processo MPI l'esito negativo. Viene eseguito più nodi utilizzare per un singolo processo MPI e più a lungo il processo, sia in esecuzione più probabile che una delle istanze di nuovo durante un processo. È anche consigliabile seguente se è possibile designare un singolo nodo nella distribuzione come file server.


* Per eseguire processi MPI in Azure, non è necessario utilizzare le istanze in grado di RDMA. È possibile utilizzare qualsiasi dimensione istanza supportate da HPC Pack. Tuttavia, le istanze in grado di RDMA sono consigliate per processi relativamente su larga scala MPI sensibili alla latenza e la larghezza di banda di rete che collega i nodi. Se si usa gli altri formati per eseguire i processi MPI latenza e larghezza di banda dipendente, è consigliabile eseguire piccoli processi, in cui una singola attività viene eseguita su solo alcuni nodi.

* Le applicazioni distribuite in istanze Azure di sono soggette le condizioni di licenza associate all'applicazione. Contattare il produttore di qualsiasi applicazione esterna per le restrizioni di licenze o un altra per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze in.


* Le istanze di Azure richiede una preparazione ulteriormente l'accesso locale nodi, condivisioni e server licenze. Per abilitare i nodi Azure accedere a un server licenze locale, ad esempio, è possibile configurare una rete virtuale Azure da sito.


* Per eseguire applicazioni MPI su istanze Azure, registrare ogni applicazione MPI con Windows Firewall sulle varianti eseguendo il comando **hpcfwutil** . In questo modo le comunicazioni MPI eseguita su una porta che venga assegnata dinamicamente tramite il firewall.

    >[AZURE.NOTE] Per burst alle distribuzioni di Azure, è anche possibile configurare un comando di eccezione firewall per l'esecuzione automatica in tutti i nodi di Azure nuovi che vengono aggiunti al cluster. Dopo avere eseguito il comando **hpcfwutil** e verificare il corretto funzionamento dell'applicazione, aggiungere il comando uno script di avvio per i nodi Azure. Per ulteriori informazioni, vedere [usare uno Script di avvio per i nodi di Azure](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack utilizza la variabile di ambiente cluster CCP_MPI_NETMASK per specificare un intervallo di indirizzi accettabili per la comunicazione MPI. A partire da HPC Pack 2012 R2, la variabile di ambiente cluster CCP_MPI_NETMASK solo influisce sulla MPI comunicazione tra nodi di elaborazione del dominio (in locale o in macchine virtuali di Azure). La variabile viene ignorata dal nodi aggiunti in un burst alla configurazione Azure.


* Processi MPI non possono essere tra più istanze di Azure distribuiti in diversi servizi cloud (ad esempio burst alle distribuzioni Azure con i modelli di nodo diverso o nodi di calcolo macchine Virtuali di Azure distribuiti in più servizi cloud). Se si dispone di più distribuzioni nodo Azure che vengono avviate con i modelli di nodo diverso, è necessario eseguire il processo MPI su un solo set di nodi Azure.


* Quando si aggiungono nodi Azure al cluster e renderli disponibili online, il servizio di utilità di pianificazione processo HPC immediatamente tenta di avviare processi nei nodi. Se solo una parte del carico di lavoro possono essere eseguiti sui Azure, assicurarsi di aggiornare o creare un modello di processo per definire quali tipi di processo può essere eseguito su Azure. Ad esempio, per garantire che processi inviati con un modello di processo viene eseguita solo nei nodi Azure, aggiungere la proprietà nodo gruppi al modello di processo e scegliere AzureNodes come il valore desiderato. Per creare gruppi personalizzati per i nodi Azure, utilizzare il cmdlet di PowerShell HPC HpcGroup Aggiungi.


## <a name="next-steps"></a>Passaggi successivi

* In alternativa all'utilizzo di HPC Pack, sviluppare con il servizio di Azure Batch per l'esecuzione di applicazioni MPI sui pool gestito dei nodi di calcolo in Azure. Vedere [attività di più istanze di utilizzo per eseguire le applicazioni di messaggio passando Interface (MPI) nel Batch di Azure](../batch/batch-mpi.md).

* Se si desidera eseguire Linux MPI applicazioni che accedono alla rete RDMA Azure, vedere [configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png