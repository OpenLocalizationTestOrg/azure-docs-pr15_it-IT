<properties
 pageTitle="I nodi cluster AutoScale HPC Pack | Microsoft Azure"
 description="Ingrandimento e riduzione del numero di HPC Pack cluster nodi di calcolo Azure automaticamente"
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
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automaticamente aumentare e ridurre le risorse cluster HPC Pack di Azure secondo il carico di lavoro cluster




Se si distribuiscono nodi di Azure "burst" nel cluster HPC Pack o si crea un cluster HPC Pack in macchine virtuali di Azure, è un modo per aumentare o ridurre il numero di risorse di elaborazione Azure, ad esempio nodi o core secondo il carico di lavoro corrente nel cluster automaticamente. In questo modo è possibile usare in modo più efficiente delle risorse Azure e controllare i costi.
A tale scopo, impostare la proprietà cluster HPC Pack **AutoGrowShrink**. In alternativa, eseguire lo script di PowerShell HPC **AzureAutoGrowShrink.ps1** installata con HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Inoltre, attualmente è possibile solo automaticamente aumentare e ridurre le nodi di calcolo HPC Pack che eseguono un sistema operativo Windows Server.

## <a name="set-the-autogrowshrink-cluster-property"></a>Impostare la proprietà cluster AutoGrowShrink

### <a name="prerequisites"></a>Prerequisiti di

* **HPC Pack 2012 R2 aggiornamento 2 o versioni successive cluster** - nodo principale cluster può essere distribuito in locale o in una macchina virtuale Azure. Vedere [configurare un cluster di ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo principale locale e i nodi di Azure "burst". Vedere lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) distribuire rapidamente un cluster HPC Pack in macchine virtuali di Azure.


* **Per un cluster con un nodo principale in Azure** - se si usa lo script di distribuzione HPC Pack IaaS per creare il cluster, attivare la proprietà di cluster **AutoGrowShrink** impostando l'opzione AutoGrowShrink nel file di configurazione cluster. Per informazioni dettagliate, vedere la documentazione relativa [al download di script](https://www.microsoft.com/download/details.aspx?id=44949). 

    In alternativa, attivare la proprietà cluster **AutoGrowShrink** dopo la distribuzione del cluster utilizzando i comandi di PowerShell HPC descritti nella sezione seguente. Per eseguire questa operazione, prima di tutto la procedura seguente:
    1. Configurare un certificato di gestione Azure sul nodo principale e in abbonamento Azure. Per una distribuzione di test è possibile utilizzare il certificato autofirmato predefinito Microsoft HPC Azure HPC Pack installata sul nodo principale e caricare semplicemente il certificato all'abbonamento Azure. Per opzioni e le procedure, vedere le [indicazioni per la libreria TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Eseguire **regedit** sul nodo principale, passare a HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e aggiungere un nuovo valore stringa. Impostare il nome del valore di "Identificazione personale" e valore dati per l'identificazione personale del certificato nel passaggio 1.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandi di PowerShell HPC per impostare la proprietà AutoGrowShrink

Comandi di PowerShell HPC per impostare **AutoGrowShrink** e per ottimizzare il comportamento con altri parametri sono esempio riportato di seguito. Vedere [i parametri AutoGrowShrink](#AutoGrowShrink-parameters) più avanti in questo articolo per l'elenco completo delle impostazioni. 

Per eseguire questi comandi, avviare HPC PowerShell sul nodo principale cluster come amministratore.

**Per attivare la proprietà AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Per disattivare la proprietà AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Per modificare l'intervallo di ingrandimento in minuti**

    Set-HpcClusterProperty –GrowInterval <interval>

**Per modificare l'intervallo di riduzione in minuti**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Per visualizzare la configurazione corrente di AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Parametri AutoGrowShrink

Di seguito sono parametri AutoGrowShrink che possono essere modificati tramite il comando **Set HpcClusterProperty** .

* **EnableGrowShrink** - interruttore per attivare o disattivare la proprietà **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - numero di attività Organizza parametro aumenti un core. Il valore predefinito è aumenti un core per attività. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 diventa **ParamSweepTasksPerCore** **TasksPerResourceUnit**. È basato sul tipo di risorsa processo e possono essere nodo, socket o core.
    
* **GrowThreshold** - soglia delle attività in coda attivare aumento automatico. Il valore predefinito è 1, in modo che se sono presenti 1 o più attività nello stato in coda automaticamente Ingrandisci nodi.
* **GrowInterval** - intervallo in minuti per impostare un trigger aumento automatico. Intervallo predefinito è 5 minuti.
* **ShrinkInterval** - intervallo in minuti per attivare la riduzione automatica. Intervallo predefinito è 5 minuti. |
* **ShrinkIdleTimes** - numero di controlli continui compattare per indicare che i nodi siano inattivi. Il valore predefinito è 3 volte. Ad esempio, se **ShrinkInterval** è 5 minuti, HPC Pack controlla se il nodo è inattivo ogni 5 minuti. Se i nodi sono nello stato di inattività al termine 3 continua verifica (15 minuti), HPC Pack riduce il nodo.
* **ExtraNodesGrowRatio** - percentuale aggiuntiva dei nodi aumenti per i processi di messaggio passando interfaccia MPI (). Il valore predefinito è 1, in modo che HPC Pack che si sviluppa nodi 1% per i processi di MPI. 
* **GrowByMin** - parametro per indicare se il criterio di aumento è basato sulle risorse minime necessari per il processo. Il valore predefinito è false, vale a dire che HPC Pack che si sviluppa nodi per i processi basati sulle risorse massime necessari per i processi.
* **SoaJobGrowThreshold** - soglia delle richieste SOA per attivare l'automatica Adatta processo. Il valore predefinito è 50000.  
    
    >[AZURE.NOTE] Questo parametro è supportato a partire da HPC Pack 2012 R2 aggiornamento 3.
    
* **SoaRequestsPerCore** -numero SOA posta in arrivo di richieste di aumentare le dimensioni di un core. Il valore predefinito è 20000.  

    >[AZURE.NOTE] Questo parametro è supportato a partire da HPC Pack 2012 R2 aggiornamento 3.

### <a name="mpi-example"></a>Esempio MPI

Per impostazione predefinita HPC Pack che si sviluppa 1% nodi supplementari per i processi MPI (**ExtraNodesGrowRatio** è impostato su 1). Il motivo è che MPI può richiedere più nodi e il processo può essere eseguito solo quando tutti i nodi pronti. Avvio di Azure nodi, in alcuni casi un nodo potrebbe essere più tempo per avviare rispetto ad altri, causando altri nodi di essere inattivo durante l'attesa per il nodo preparare. In espansione nodi supplementari, HPC Pack riduce il tempo di attesa delle risorse e potenzialmente Salva i costi. Per aumentare la percentuale di nodi supplementari per i processi MPI (ad esempio al 10%), eseguire un comando simile a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Esempio SOA

Per impostazione predefinita, **SoaJobGrowThreshold** è impostata su 50000 e **SoaRequestsPerCore** è impostato su 200000. Se si invia un processo SOA con 70000 richieste, si verificherà un'attività in coda e le richieste in arrivo sono 70000. In questo caso si espande HPC Pack 1 per l'attività in coda e per le richieste in arrivo, di base crescerà (70000 50000) / principali 20000 = 1, in modo che in totale crescerà 2 core per il processo SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Eseguire lo script AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Prerequisiti di

* **HPC Pack 2012 R2 aggiornamento 1 o versioni successive cluster** - lo script **AzureAutoGrowShrink.ps1** viene installato nella cartella bin % CCP_HOME %. Il nodo principale cluster può essere distribuito in locale o in una macchina virtuale Azure. Vedere [configurare un cluster di ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) per iniziare con un nodo principale locale e i nodi di Azure "burst". Visualizzare lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) distribuire rapidamente un cluster HPC Pack in macchine virtuali di Azure o utilizzare un [modello di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - lo script attualmente varia in questa versione specifica di Azure PowerShell. Se si esegue una versione più recente sul nodo principale, è necessario eseguire il downgrade alla [versione 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) per eseguire lo script di PowerShell Azure. 

* **Per un cluster con nodi burst Azure** - eseguire lo script in un computer client in cui è installato HPC Pack o sul nodo principale. Se è in esecuzione in un computer client, assicurarsi che sia impostata la variabile $env: CCP_SCHEDULER correttamente in modo che puntino a nodo principale. I nodi di Azure "burst" già devono essere aggiunta ai cluster, ma potrebbe essere stato distribuito non.


* **Per un cluster distribuito in macchine virtuali di Azure** - eseguire lo script sul nodo principale macchine Virtuali, perché dipende **HpcIaaSNode.ps1 Start** e **Stop HpcIaaSNode.ps1** script che vi sono installati. Gli script inoltre richiedere un certificato di gestione Azure o pubblicare il file di impostazioni (vedere [gestire nodi in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Assicurarsi che tutto il nodo di calcolo macchine virtuali è necessario sono già stati aggiunti al cluster. Possono essere impostato su arrestato.

### <a name="syntax"></a>Sintassi

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Parametri

 * **NodeTemplates** - i nomi dei modelli di nodo per definire l'ambito per i nodi di ingrandimento e riduzione. Se non viene specificato (il valore predefinito è @()), tutti i nodi nel gruppo nodo **AzureNodes** sono nell'ambito quando **nodo** contiene un valore di AzureNodes e tutti i nodi nel gruppo nodo **ComputeNodes** risultano nell'ambito **nodo** ha un valore di ComputeNodes.

 * **JobTemplates** - i nomi dei modelli di processo per definire l'ambito per i nodi aumenti.

 * **Nodo** - il tipo del nodo di ingrandimento e riduzione. Sono supportati i valori:

     * **AzureNodes** -per i nodi di Azure PaaS (burst) in un locale o cluster IaaS Azure.

     * **ComputeNodes** - per nodo macchine virtuali solo in un cluster di Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow** - numero di processi in coda di ampliare un nodo.

* **NumOfQueuedJobsToGrowThreshold** - il numero di soglia dei processi in coda per avviare il processo di ingrandimento.

* **NumOfActiveQueuedTasksPerNodeToGrow** - il numero di attività in coda di ampliare un nodo. Se si specifica **NumOfQueuedJobsPerNodeToGrow** con un valore maggiore di 0, questo parametro viene ignorato.

* **NumOfActiveQueuedTasksToGrowThreshold** - il numero di soglia di attività in coda attive per avviare il processo di ingrandimento.

* **NumOfInitialNodesToGrow** - il numero minimo iniziale dei nodi, specialmente se tutti i nodi di ambito sono **Distribuiti non** o **arrestato (Deallocated)**.

* **GrowCheckIntervalMins** - l'intervallo in minuti tra i controlli per espandere.

* **ShrinkCheckIntervalMins** - l'intervallo in minuti tra i controlli per la riduzione.

* **ShrinkCheckIdleTimes** - il numero di continuo Riduci controlli (separati da **ShrinkCheckIntervalMins**) per indicare i nodi siano inattivi.

* **UseLastConfigurations** - le configurazioni precedenti salvate nel file degli argomenti.

* **ArgFile**- il nome del file argomento utilizzato per salvare e aggiornare le configurazioni per eseguire lo script.

* **LogFilePrefix** - il prefisso del nome del file di log. È possibile specificare un percorso. Per impostazione predefinita i log è scritto nella cartella di lavoro corrente.

### <a name="example-1"></a>Esempio 1

Nell'esempio seguente consente di configurare i nodi di Azure burst distribuiti con il modello di AzureNode predefinita di ingrandimento e riduzione automaticamente. Se tutti i nodi sono inizialmente nello stato **Distribuito non** vengono avviati almeno 3 nodi. Se il numero di processi in coda supera 8, lo script inizia nodi fino a quando il numero supera il rapporto di processi in coda a **NumOfQueuedJobsPerNodeToGrow**. Se viene rilevato un nodo di inattività 3 volte inattivo consecutivi, viene interrotta.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Esempio 2

Nell'esempio seguente consente di configurare il nodo di calcolo Azure macchine virtuali distribuite con il modello di ComputeNode predefinita di ingrandimento e riduzione automaticamente.
I processi configurati per il modello di processo predefinito definiscono l'ambito del carico di lavoro nel cluster. Se inizialmente vengono interrotti tutti i nodi, vengono avviati nodi almeno 5. Se il numero di attività in coda maggiore di 15, lo script avvia nodi fino a quando il numero supera il rapporto tra le attività attive in coda a **NumOfActiveQueuedTasksPerNodeToGrow**. Se viene rilevato un nodo di inattività 10 tempi di inattività consecutivi, viene interrotta.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
