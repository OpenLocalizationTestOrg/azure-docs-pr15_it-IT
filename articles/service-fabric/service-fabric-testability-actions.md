<properties
   pageTitle="Azione di testabilità | Microsoft Azure"
   description="In questo articolo parla azioni testabilità disponibili in Microsoft Azure servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Azioni testabilità
Per simulare un'infrastruttura origini non affidabile, Azure servizio tessuti fornisce, dello sviluppatore, modi per simulare diversi errori reali e transizioni. Questi sono esposti come azioni testabilità. Le azioni sono API di basso livello che causano un inserimento errore specifica, transizione di stato o convalida. Grazie alla combinazione queste azioni, è possibile scrivere scenari completa di test per i servizi.

Servizio tessuti fornisce che alcuni scenari comuni di test è composto da queste azioni. Si consiglia di utilizzare questi scenari predefiniti, vengono scelti attentamente, per testare transizioni comuni e caso di errore. Tuttavia, azioni possono essere usate per creare scenari di test personalizzato quando si desidera aggiungere la copertura per gli scenari che non rientrano gli scenari predefiniti ancora o che sono personalizzati personalizzate per l'applicazione.

Vedere assembly System.Fabric.dll c# implementazioni delle azioni. Vedere assembly Microsoft.ServiceFabric.Powershell.dll del modulo PowerShell per tessuti sistema. Come parte dell'installazione di runtime, è installato il modulo ServiceFabric PowerShell per consentire la facilità di utilizzo.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Regolare e azioni anomalo del guasto
Azioni testabilità sono classificate nelle due intervalli principali:

* Errori anomalo del: questi errori simulare errori come riavvio del computer e diagrammi di processo anomalo. In tal caso di errori, il contesto di esecuzione del processo si arresta in modo anomalo. Questo errore indica che alcuna operazione di rimozione dello stato può essere eseguito prima dell'applicazione viene riavviata.

* Errori irregolari: questi errori simulare azioni regolare come replica sposta e gocce attivate tramite il bilanciamento del carico. In tal caso, il servizio riceve una notifica di chiusura e consente di pulire lo stato prima di uscire.

Per la convalida qualità migliore, eseguire il carico di lavoro di servizio e per le aziende e provocare errori normale e anomalo del diversi. Errori anomalo del esercitano scenari in cui il processo di servizio in modo anomalo termina nel centro di un flusso di lavoro. Si verifica il percorso di ripristino una volta duplicata servizio ripristinato dal servizio tessuti. In questo modo verificare la coerenza dei dati e se lo stato del servizio viene mantenuto correttamente dopo errori. Gruppo di errori (errori regolare) verificare che il servizio reagisce correttamente alle repliche viene spostate dal servizio tessuti. Ciò consente di verificare la gestione di annullamento del metodo RunAsync. Il servizio deve verificare la presenza di token di annullamento viene impostata, correttamente il salvataggio dello stato e uscire dal metodo RunAsync.

## <a name="testability-actions-list"></a>Elenco di azioni testabilità

| Azione | Descrizione | API gestita | Cmdlet di PowerShell | Regolare/anomalo del errori |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Rimuove tutti lo stato di test da cluster in caso di arresto errato del driver di test. | CleanTestStateAsync | Rimuovi ServiceFabricTestState | Non applicabile |
| InvokeDataLoss | Provoca la perdita di dati in una partizione di servizio. | InvokeDataLossAsync | Richiamare ServiceFabricPartitionDataLoss | Normale |
| InvokeQuorumLoss | Inserisce la perdita di base di una partizione di servizio informazioni sullo stato specificato. | InvokeQuorumLossAsync | Richiamare ServiceFabricQuorumLoss | Normale |
| Spostare principale | Sposta la replica primaria specificata di un servizio informazioni sullo stato del nodo di cluster specificato. | MovePrimaryAsync | Sposta ServiceFabricPrimaryReplica | Normale |
| Spostare secondario | Sposta la replica secondaria corrente di un servizio informazioni sullo stato a un nodo cluster diverso. | MoveSecondaryAsync | Sposta ServiceFabricSecondaryReplica | Normale |
| RemoveReplica | Consente di simulare un errore di replica mediante la rimozione di una replica da un cluster. Questa operazione si chiude la replica e passerà al ruolo 'None', rimuovere il relativo stato dal cluster. | RemoveReplicaAsync | Rimuovi ServiceFabricReplica | Normale |
| RestartDeployedCodePackage | Consente di simulare un errore processo codice riavviando un pacchetto di codice distribuito in un nodo in un cluster. Si interrompe il processo di pacchetto codice, che verrà riavviato ogni la replica di servizi utente ospitato nel processo. | RestartDeployedCodePackageAsync | Riavvio ServiceFabricDeployedCodePackage | Anomalo del |
| RestartNode | Consente di simulare un errore di nodo cluster tessuti servizio riavviando un nodo. | RestartNodeAsync | Riavvio ServiceFabricNode | Anomalo del |
| RestartPartition | Consente di simulare uno scenario di blackout Data Center blackout o cluster riavviando alcune o tutte le repliche di una partizione. | RestartPartitionAsync | Riavvio ServiceFabricPartition | Normale |
| RestartReplica | Consente di simulare un errore di replica riavviando una replica persistente in un cluster, chiudere la replica e quindi riaprire la. | RestartReplicaAsync | Riavvio ServiceFabricReplica | Normale |
| StartNode | Avvia un nodo in un cluster che è già stato interrotto. | StartNodeAsync | Inizio ServiceFabricNode | Non applicabile |
| StopNode | Consente di simulare un errore di nodo si arresta un nodo in un cluster. Il nodo resterà verso il basso fino a quando si chiama StartNode. | StopNodeAsync | Interrompi ServiceFabricNode | Anomalo del |
| ValidateApplication | Consente di verificare la disponibilità e l'integrità di tutti i servizi di infrastruttura di servizio in un'applicazione, in genere dopo indurre alcune guasto nel sistema. | ValidateApplicationAsync | Test ServiceFabricApplication | Non applicabile |
| ValidateService | Consente di verificare la disponibilità e l'integrità del servizio di assistenza tessuti, in genere dopo indurre alcune guasto nel sistema. | ValidateServiceAsync | Test ServiceFabricService | Non applicabile |

## <a name="running-a-testability-action-using-powershell"></a>Esecuzione di un'azione testabilità tramite PowerShell

In questa esercitazione viene illustrato come eseguire un'azione testabilità tramite PowerShell. Viene spiegato eseguire un'azione testabilità in base a un cluster (una casella) locale o un cluster di Azure. Microsoft.Fabric.Powershell.dll - modulo servizio tessuti PowerShell, ovvero viene installato automaticamente durante l'installazione di Microsoft servizio tessuti MSI. Il modulo viene caricato automaticamente quando si apre un prompt dei comandi PowerShell.

Esercitazioni segmenti:

- [Eseguire un'azione su un cluster di una casella](#run-an-action-against-a-one-box-cluster)
- [Eseguire un'azione su un cluster di Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Eseguire un'azione su un cluster di una casella

Per eseguire un'azione testabilità su un cluster locale, connettersi al cluster e aprire il prompt dei comandi PowerShell in modalità di amministratore. Analizzate l'azione **ServiceFabricNode riavvia** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Di seguito viene eseguito l'azione **ServiceFabricNode riavvia** su un nodo denominato "Nodo 1". La modalità di completamento specifica non deve verificare se l'azione riavvio nodo effettivamente completata. Specificare la modalità di completamento come "Verificare" avrà effetto verificare se l'azione riavvia effettivamente completata. Anziché specificare direttamente il nodo in base al nome, è possibile specificarla mediante una chiave di partizione e il tipo di replica, come indicato di seguito:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Riavvio ServiceFabricNode** dovrebbe essere usata per riavviare un nodo servizio tessuti in un cluster. Questa operazione verrà interrotto il processo Fabric.exe, che verrà riavviato tutto i sistema servizio e utente servizio riproduzioni ospitati in tale nodo. Tramite l'API per testare il servizio consente di scoprire bug lungo i percorsi di ripristino failover. Consente di simulare errori di nodo del cluster.

La schermata seguente mostra il comando testabilità **Riavvio ServiceFabricNode** in azione.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

L'output del primo **Get-ServiceFabricNode** (un cmdlet del modulo PowerShell per tessuti servizio) che indica che il cluster locale è cinque nodi: Node.1 a Node.5. Dopo l'esecuzione dell'azione testabilità (cmdlet) **Riavvia ServiceFabricNode** sul nodo denominato Node.4, è possibile vedere che il tempo di attività del nodo è stati opportunamente reimpostato.

### <a name="run-an-action-against-an-azure-cluster"></a>Eseguire un'azione su un cluster di Azure

Esecuzione di un'azione testabilità (tramite PowerShell) in un cluster di Azure è simile a eseguire l'azione su un cluster locale. L'unica differenza è prima di eseguire l'azione, invece di connettersi al cluster locale, è necessario connettersi innanzitutto al cluster Azure.

## <a name="running-a-testability-action-using-c35"></a>Esecuzione di un'azione testabilità mediante C & #35;

Per eseguire un'azione testabilità utilizzando c#, è necessario connettersi al cluster tramite FabricClient. Quindi ottenere i parametri necessari per eseguire l'azione. Parametri diversi possono essere utilizzati per eseguire la stessa operazione.
Esaminando l'azione RestartServiceFabricNode, è possibile eseguirlo in base alle informazioni di nodo (nome e ID istanza nodo) del cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

SPIEGAZIONE parametro:

- **CompleteMode** specifica che la modalità non è necessario verificare se l'azione riavvia effettivamente completata. Specificare la modalità di completamento come "Verificare" avrà effetto verificare se l'azione riavvia effettivamente completata.  
- **OperationTimeout** imposta la quantità di tempo per l'operazione da completare prima che venga generata un'eccezione TimeoutException.
- **CancellationToken** consente una chiamata in attesa deve essere annullato.

Anziché specificare direttamente il nodo in base al nome, è possibile specificarla tramite una chiave di partizione e il tipo di replica.

Per ulteriori informazioni, vedere [PartitionSelector e ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector da supporto esposti nel testabilità e consente di selezionare una partizione specifica su cui eseguire una delle operazioni testabilità. Può essere utilizzato per selezionare una partizione specifica se l'ID di partizione è noto in precedenza. In alternativa, è possibile fornire la chiave di partizione e l'operazione risolverà l'ID di partizione internamente. È inoltre possibile selezionare una partizione casuale.

Per utilizzare questo supporto, creare l'oggetto PartitionSelector e selezionare la partizione utilizzando uno dei metodi selezionare *. Quindi passare all'API che richiede l'oggetto PartitionSelector. Se nessuna opzione è selezionata, l'impostazione predefinita in una partizione casuale.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector da supporto esposti nel testabilità e consente di selezionare una replica su cui eseguire una delle operazioni testabilità. Può essere utilizzato per selezionare una specifica replica se l'ID replica è noto in precedenza. Inoltre, avere la possibilità di selezionare una replica primaria o secondario casuale. ReplicaSelector deriva da PartitionSelector, pertanto è necessario selezionare la replica e partizione sul quale si desidera eseguire l'operazione testabilità.

Per utilizzare questo supporto, creare un oggetto ReplicaSelector e impostare nel modo desiderato selezionare la replica e la partizione. È quindi possibile passare all'API che lo richiede. Se nessuna opzione è selezionata, l'impostazione predefinita a un replica casuale e partizione casuali.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di testabilità](service-fabric-testability-scenarios.md)
- Come verificare il servizio
   - [Consente di simulare errori durante carichi di lavoro di servizio](service-fabric-testability-workload-tests.md)
   - [Errori di comunicazione per servizio](service-fabric-testability-scenarios-service-communication.md)
