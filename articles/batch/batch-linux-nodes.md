<properties
    pageTitle="Nodi Linux nel pool di Azure Batch | Microsoft Azure"
    description="Informazioni su come elaborare i carichi di lavoro di calcolo in parallelo sui pool di macchine virtuali Linux nel Batch di Azure."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Effettuare il provisioning di nodi di calcolo Linux nel pool di Azure Batch

È possibile utilizzare Azure Batch eseguire carichi di lavoro di calcolo in parallelo in macchine virtuali Linux e Windows. In questo articolo viene illustrato come creare pool Linux dei nodi di calcolo nel servizio Batch utilizzando entrambi [Python Batch] [ py_batch_package] e [.NET Batch] [ api_net] librerie client.

> [AZURE.NOTE] [Pacchetti di applicazioni](batch-application-packages.md) non sono attualmente supportate su nodi di calcolo Linux.

## <a name="virtual-machine-configuration"></a>Configurazione della macchina virtuale

Quando si crea un pool di nodi di calcolo in Batch, sono disponibili due opzioni da cui selezionare le dimensioni di nodo e il sistema operativo: servizi Cloud e configurazione di macchina virtuale.

**Configurazione di servizi cloud** consente di che calcolare nodi *solo*di Windows. Le dimensioni di nodo di calcolo disponibili sono elencate in [formati per i servizi Cloud](../cloud-services/cloud-services-sizes-specs.md)e sistemi operativi disponibili sono elencati [i sistemi operativi Guest Azure versioni e nella matrice compatibilità SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool che contiene i nodi servizi Cloud Windows Azure, è necessario specificare solo la dimensione del nodo e della relativa "OS famiglia," che si trovano negli articoli indicati in precedenza. Per i pool di Windows nodi di calcolo, viene usato più comunemente Cloud Services.

**Configurazione delle macchine virtuali** fornisce immagini Linux e Windows per nodi di calcolo. Le dimensioni di nodo di calcolo disponibili sono elencate in [dimensioni per macchine virtuali in Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [dimensioni per macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Quando si crea un pool che contiene i nodi di configurazione della macchina virtuale, è necessario specificare le dimensioni dei nodi, il riferimento di immagine macchina virtuale e l'agente di nodo Batch SKU sia installato nei nodi.

### <a name="virtual-machine-image-reference"></a>Riferimento all'immagine di macchina virtuale

Il servizio Batch utilizza [Imposta scala macchina virtuale](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per fornire nodi di calcolo Linux. Immagini del sistema operativo per queste macchine virtuali vengono forniti da [Azure Marketplace][vm_marketplace]. Quando si configura un riferimento di immagine macchina virtuale, specificare le proprietà di un'immagine di macchina virtuale Marketplace. Le proprietà seguenti sono necessari quando si crea un riferimento di immagine macchina virtuale:

| **Proprietà di riferimento dell'immagine** | **Esempio** |
| ----------------- | ------------------------ |
| Publisher         | Canonici                |
| Offerta             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| Versione           | più recente                   |

> [AZURE.TIP] Sono disponibili ulteriori su queste proprietà e su come visualizzare un elenco immagini Marketplace [Naviga e selezionare le immagini della macchina virtuale Linux in Azure con CLI o PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Si noti che non tutte le immagini Marketplace attualmente compatibile con Batch. Per ulteriori informazioni, vedere [nodo agente SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Agente di nodo SKU

L'agente di nodo Batch è un programma che viene eseguito su ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio di Batch. Esistono diverse implementazioni dell'agente di nodo, noto come SKU, per sistemi operativi diversi. In pratica, quando si crea una configurazione macchina virtuale, è necessario specificare il riferimento di immagine macchina virtuale e quindi specificare l'agente di nodo per installare sull'immagine. In genere, ogni agente nodo SKU è compatibile con più immagini di macchina virtuale. Ecco alcuni esempi di agente nodo SKU:

* batch.Node.Ubuntu 14.04
* batch.Node.centos 7
* amd64 batch.Node.Windows

> [AZURE.IMPORTANT] Non tutte le immagini di macchina virtuale che sono disponibili sul mercato sono compatibili con gli agenti nodo Batch attualmente disponibili. È necessario utilizzare SDK Batch per visualizzare un elenco agente di nodo disponibile SKU e le immagini di macchina virtuale a cui sono compatibili. Vedere l' [elenco di Virtual Machine immagini](#list-of-virtual-machine-images) più avanti in questo articolo per ulteriori informazioni.

## <a name="create-a-linux-pool-batch-python"></a>Creare un pool di Linux: Python Batch

Frammento di codice seguente viene illustrato un esempio su come utilizzare la [Libreria di Microsoft Azure Batch Client per Python] [ py_batch_package] per creare un pool di Ubuntu Server nodi di calcolo. Documentazione di riferimento per il modulo Python Batch sono disponibili in [pacchetto azure.batch] [py_batch_docs] in lettura di documenti.

Questo frammento consente di creare un [ImageReference] [ py_imagereference] in modo esplicito e specifica ognuna delle proprietà (autore, offerta, SKU, versione). Nel codice di produzione, tuttavia, si consiglia di utilizzare [list_node_agent_skus] [ py_list_skus] metodo per determinare e selezionare l'immagine e nodo agente SKU combinazioni disponibili in fase di esecuzione.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Come detto in precedenza, è consigliabile invece di creare [ImageReference] [ py_imagereference] in modo esplicito, utilizzare [list_node_agent_skus] [ py_list_skus] metodo per selezionare in modo dinamico dalle combinazioni di immagine agente/Marketplace nodo attualmente supportati. Frammento di Python riportato di seguito mostra l'utilizzo di questo metodo.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Creare un pool di Linux: .NET Batch

Frammento di codice seguente mostra un esempio di come utilizzare [.NET Batch] [ nuget_batch_net] libreria di client per creare un pool di Ubuntu Server nodi di calcolo. È possibile trovare la [documentazione di riferimento .NET Batch] [ api_net] su MSDN.

Frammento di codice riportato utilizza [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] metodo per selezionare l'elenco di attualmente supportato Marketplace immagine e nodo agente SKU combinazioni. Questa tecnica è utile perché l'elenco delle combinazioni supportate può cambiare nel tempo. In genere, vengono aggiunti combinazioni supportate.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Anche se il frammento di codice precedente utilizza [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] metodo per elencare e selezionare da in modo dinamico supportato immagine nodo agente SKU combinazioni e (scelta consigliate), è inoltre possibile configurare un [ImageReference] [ net_imagereference] in modo esplicito:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Elenco immagini del computer virtuale

Nella tabella seguente elenca le immagini di macchina virtuale Marketplace compatibili con gli agenti di nodo Batch disponibili quando è stato aggiornato in questo articolo. È importante tenere presente che questo elenco non definitivo perché le immagini e gli agenti nodo possono essere aggiunti o rimossi in qualsiasi momento. È consigliabile che i servizi e applicazioni di Batch utilizzano sempre [list_node_agent_skus] [ py_list_skus] (Python) e [ListNodeAgentSkus] [ net_list_skus] (Batch .NET) per determinare e selezionare SKU attualmente disponibile.

> [AZURE.WARNING] L'elenco seguente può variare in qualsiasi momento. Utilizzare sempre i metodi di **agente di nodo elenco SKU** disponibili in API Batch per elencare e quindi selezionare la macchina virtuale compatibile e agente nodo SKU quando si eseguono processi Batch.

| **Publisher** | **Offerta** | **Immagine SKU** | **Versione** | **Agente di nodo ID SKU** |
| ------- | ------- | ------- | ------- | ------- |
| Canonici | UbuntuServer | 14.04.0-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 14.04.1-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 14.04.2-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 14.04.3-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 14.04.4-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 14.04.5-LTS | più recente | batch.Node.Ubuntu 14.04 |
| Canonici | UbuntuServer | 16.04.0-LTS | più recente | batch.Node.Ubuntu 16.04 |
| Credativ | Debian | 8 | più recente | batch.Node.Debian 8 |
| OpenLogic | CentOS | 7.0 | più recente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.1 | più recente | batch.Node.centos 7 |
| OpenLogic | HPC centOS | 7.1 | più recente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.2 | più recente | batch.Node.centos 7 |
| Oracle | Linux Oracle | 7.0 | più recente | batch.Node.centos 7 |
| SUSE | openSUSE | 13.2 | più recente | batch.Node.opensuse 13.2 |
| SUSE | openSUSE bisestile | 42.1 | più recente | batch.Node.opensuse 42.1 |
| SUSE | SLES HPC | 12 | più recente | batch.Node.opensuse 42.1 |
| SUSE | SLES | SP1 12 | più recente | batch.Node.opensuse 42.1 |
| annunci di Microsoft | standard-dati-scienza-macchine virtuali | standard-dati-scienza-macchine virtuali | più recente | amd64 batch.Node.Windows |
| annunci di Microsoft | Linux-dati-scienza-macchine virtuali | linuxdsvm | più recente | batch.Node.centos 7 |
| MicrosoftWindowsServer | Windows Server | 2008 R2 SP1 | più recente | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | Data Center 2012 | più recente | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | Data Center di R2 2012 | più recente | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | Anteprima tecnica di Server Windows | più recente | amd64 batch.Node.Windows |

## <a name="connect-to-linux-nodes"></a>Connettersi a nodi Linux

Durante lo sviluppo o durante la risoluzione dei problemi, potrebbe essere necessario accedere a nodi nel pool. A differenza dei nodi di calcolo di Windows, è possibile utilizzare Remote protocollo RDP (Desktop) a cui connettersi nodi Linux. Se, tuttavia, il servizio di Batch consente l'accesso SSH in ogni nodo per la connessione remota.

Frammento di codice Python riportato crea un utente in ogni nodo in un pool, è necessario per la connessione remota. Stampa quindi le informazioni di connessione sicura shell (SSH) per ogni nodo.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Di seguito è riportato un output per il codice precedente di un pool che contiene quattro nodi Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Si noti che invece di una password, è possibile specificare una chiave pubblica SSH quando si crea un utente in un nodo. In SDK Python operazione viene eseguita tramite il parametro **ssh_public_key** su [ComputeNodeUser][py_computenodeuser]. In .NET ciò utilizzando [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] proprietà.

## <a name="pricing"></a>Prezzi

Azure Batch si basa su grazie alla tecnologia di servizi Cloud Windows Azure e macchine virtuali di Azure. Il servizio di Batch è disponibile senza costi, vale a dire addebitate solo per le risorse di elaborazione che utilizzano le soluzioni Batch. Quando si sceglie di **Configurazione di servizi Cloud**, verrà addebitato in base alle [servizi Cloud prezzi] [ cloud_services_pricing] struttura. Quando si sceglie di **Configurazione della macchina virtuale**, verrà addebitato in [macchine virtuali di prezzi] base[ vm_pricing] struttura.

## <a name="next-steps"></a>Passaggi successivi

### <a name="batch-python-tutorial"></a>Esercitazione Python batch

Per un'esercitazione più dettagliata su come lavorare con Batch utilizzando Python, vedere [iniziare a utilizzare il client di Azure Batch Python](batch-python-tutorial.md). Il relativo complementare [esempio] [ github_samples_pyclient] include una funzione di supporto, `get_vm_config_for_distro`, che viene visualizzata un'altra tecnica per ottenere una configurazione macchina virtuale.

### <a name="batch-python-code-samples"></a>Esempi di codice Python batch

Estrarre l'altri [esempi di codice Python] [ github_samples_py] negli [esempi di batch azure] [ github_samples] archivio in GitHub dei vari script che illustrano come eseguire operazioni Batch comuni, ad esempio pool di processo e la creazione di attività. [File Leggimi] [ github_py_readme] che allegata Python esempi sono informazioni dettagliate su come installare i pacchetti necessari.

### <a name="batch-forum"></a>Forum di batch

[Forum di Azure Batch] [ forum] su MSDN è ideale per discutere in Batch e porre domande relative al servizio. Leggere i post utili "stickied" e rivolgere le domande mentre si verificano durante la creazione di soluzioni Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
