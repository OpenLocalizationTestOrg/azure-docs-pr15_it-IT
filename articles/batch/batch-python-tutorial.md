<properties
    pageTitle="Esercitazione - iniziare a utilizzare il client di Azure Batch Python | Microsoft Azure"
    description="Informazioni sui concetti di base di Azure Batch e su come sviluppare il servizio di Batch con un semplice scenario"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Iniziare a utilizzare il client di Azure Batch Python

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Informazioni sulle nozioni fondamentali di [Azure Batch] [ azure_batch] e [Python Batch] [ py_azure_sdk] client come illustrato una piccola applicazione Batch scritta in Python. Si osserva come due usare gli script di esempio il servizio di Batch per elaborare un carico di lavoro in parallelo in macchine virtuali Linux nel cloud e su come interagiscono con [Lo spazio di archiviazione di Azure](./../storage/storage-introduction.md) per il recupero e gestione temporanea file. Si verrà informazioni su un flusso di lavoro di applicazione Batch comune e comprendere base dei componenti principali del Batch, ad esempio processi, attività, pool e nodi di calcolo.

![Flusso di lavoro soluzione batch (basic)][11]<br/>

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone una conoscenza di Python e avere familiarità con Linux. Inoltre, presuppone che è in grado di soddisfare i requisiti di creazione di account specificate di seguito per Azure e i servizi Batch e lo spazio di archiviazione.

### <a name="accounts"></a>Account

- **Account Azure**: se si dispone già di un abbonamento Azure, [creare un account Azure gratuito][azure_free_account].
- **Account batch**: dopo avere inserito una sottoscrizione Azure, [creare un account Azure Batch](batch-account-create-portal.md).
- **Account di archiviazione**: vedere [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [account di archiviazione su Azure](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Esempio di codice

Esercitazioni Python [esempio] [ github_article_samples] uno dei molti esempi di codice Batch trovato negli [esempi di batch azure] [ github_samples] archivio in GitHub. È possibile scaricare tutti gli esempi facendo clic su **duplicato o download > ZIP di Download** nella home page di archivio oppure facendo clic su [azure-batch-esempi-master.zip] [ github_samples_zip] collegamento diretto download. Una volta estratto il contenuto del file ZIP, due script per questa esercitazione si trovano nel `article_samples` directory:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente Python

Per eseguire lo script di esempio *python_tutorial_client.py* sulla workstation locale, è necessario un **interprete Python** compatibile con la versione **2.7** o **3.3 +**. Lo script è stato verificato in Linux e Windows.

### <a name="cryptography-dependencies"></a>dipendenze di crittografia

È necessario installare le dipendenze per la [crittografia] [ crypto] raccolta necessaria affinché la `azure-batch` e `azure-storage` pacchetti Python. Eseguire una delle seguenti operazioni appropriate per la piattaforma o fare riferimento all' [installazione di crittografia] [ crypto_install] dettagli per ulteriori informazioni:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Se l'installazione per Python 3.3 + su Linux, usare gli equivalenti su python3 per le dipendenze Python. Ad esempio su Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Pacchetti di Azure

Successivamente, installare i pacchetti di **Azure Batch** e **Lo spazio di archiviazione di Azure** Python. È possibile eseguire con **pip** e *requirements.txt* sono disponibili qui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problema seguendo comando **pip** per installare i pacchetti Batch e lo spazio di archiviazione:

`pip install -r requirements.txt`

Oppure, è possibile installare il [batch di azure] [ pypi_batch] e [archiviazione di azure] [ pypi_storage] Python pacchetti manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Potrebbe essere necessario prefisso dei comandi con `sudo` se si utilizza un account senza privilegi. Ad esempio `sudo pip install -r requirements.txt`. Per ulteriori informazioni sull'installazione di pacchetti Python, vedere [L'installazione di pacchetti] [ pypi_install] su readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Esempio di codice dell'esercitazione Python batch

Esempio di codice dell'esercitazione Python Batch è costituito da due script Python e alcuni file di dati.

- **python_tutorial_client.py**: interagisce con i servizi Batch e lo spazio di archiviazione per eseguire un carico di lavoro in parallelo su nodi di calcolo (macchine virtuali). Lo script *python_tutorial_client.py* esegue sulla workstation locale.

- **python_tutorial_task.py**: nodi in Azure per eseguire il lavoro effettivo di script che viene eseguita nel calcolo. In questo esempio viene *python_tutorial_task.py* analizza il testo in un file scaricato dallo spazio di archiviazione di Azure (file di input). Quindi viene generato un file di testo (file di output) che contiene un elenco delle prime tre parole presenti nel file di input. Dopo la creazione di file di output, per lo spazio di archiviazione di Azure *python_tutorial_task.py* caricamento del file. Per renderlo disponibile per il download dello script client esecuzione sulla workstation. Lo script *python_tutorial_task.py* viene eseguito in parallelo su più nodi di calcolo nel servizio Batch.

- **./data/taskdata\*txt**: questi file di tre testo forniscono l'input per le attività che eseguono sui nodi di calcolo.

Il diagramma seguente illustra le operazioni primarie vengono eseguiti gli script di client e attività. Questo flusso di lavoro di base è tipico delle molte soluzioni di calcolo creati con Batch. Mentre non vengono visualizzati tutte le funzionalità disponibili nel servizio di Batch, quasi ogni scenario Batch include parti del flusso di lavoro.

![Flusso di lavoro di esempio batch][8]<br/>

[**Passaggio 1.**](#step-1-create-storage-containers) Creare **i contenitori** in archiviazione Blob Azure.<br/>
[**Passaggio 2.**](#step-2-upload-task-script-and-data-files) Caricare file di script e input attività contenitori.<br/>
[**Passaggio 3.**](#step-3-create-batch-pool) Creare un Batch **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Pool **StartTask** Scarica lo script di attività (python_tutorial_task.py) nodi che si connettono del pool.<br/>
[**Passaggio 4.**](#step-4-create-batch-job) Creare un Batch **processo**.<br/>
[**Passaggio 5.**](#step-5-add-tasks-to-job) Aggiungere **attività** al processo.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Le attività vengono programmate per l'esecuzione su nodi.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Ogni attività download relativi dati di input dallo spazio di archiviazione di Azure e quindi inizia l'esecuzione.<br/>
[**Passaggio 6.**](#step-6-monitor-tasks) Monitorare l'attività.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Come le attività vengano completate, caricano i dati di output allo spazio di archiviazione Azure.<br/>
[**Passaggio 7.**](#step-7-download-task-output) Scaricare output attività dallo spazio di archiviazione.

Come spiegato in precedenza, non ogni soluzione Batch esegue questa procedura esatta e possono essere presenti molte altre, ma questo viene illustrata processi comuni trovati una soluzione Batch.

## <a name="prepare-client-script"></a>Preparare l'ambiente di script client

Prima di eseguire l'esempio, aggiungere le credenziali dell'account di archiviazione e Batch *python_tutorial_client.py*. Se non è già stato fatto, aprire il file in un editor e aggiornare le seguenti righe con le credenziali.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

È possibile trovare le credenziali dell'account Batch e lo spazio di archiviazione all'interno e il conto di ogni servizio nel [portale di Azure][azure_portal]:

![Batch credenziali nel portale di][9]
![le credenziali di spazio di archiviazione nel portale][10]<br/>

Nelle sezioni seguenti sono analizzare i passaggi necessari per elaborare un carico di lavoro nel servizio Batch dagli script. È consigliabile consultare regolarmente gli script nell'editor mentre si lavora scorrere il resto dell'articolo.

Individuare la riga seguente nella **python_tutorial_client.py** per iniziare con il passaggio 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Passaggio 1: Creare contenitori di archiviazione

![Creare i contenitori di archiviazione Azure][1]
<br/>

Batch include il supporto per interagire con lo spazio di archiviazione di Azure. I contenitori di account di archiviazione costituiranno file necessari per le attività eseguite nell'account Batch. I contenitori offrono una posizione per archiviare i dati di output che generano le attività. La prima cosa che significa lo script *python_tutorial_client.py* è creare tre contenitori in [Archiviazione Blob Azure](../storage/storage-introduction.md#blob-storage):

- **applicazione**: questo contenitore verrà archiviata script Python eseguire le attività, *python_tutorial_task.py*.
- **input**: attività scaricare i file di dati per l'elaborazione di dal contenitore di *input* .
- **output**: attività completato l'elaborazione di file di input, sarà caricano i risultati al contenitore di *output* .

Per interagire con un account di archiviazione e creare contenitori, viene utilizzato l' [archiviazione di azure] [ pypi_storage] pacchetto per creare un [BlockBlobService] [ py_blockblobservice] oggetto: "client blob". È quindi creare tre contenitori in account di archiviazione utilizzando il client blob.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Dopo avere creati i contenitori, l'applicazione ora può caricare i file che verranno utilizzati per le attività.

> [AZURE.TIP] [Come utilizzare lo spazio di archiviazione Blob Azure da Python](../storage/storage-python-how-to-use-blob-storage.md) offre un'ampia panoramica dell'uso BLOB e contenitori di archiviazione di Azure. Dovrebbe essere nella parte superiore dell'elenco di lettura quando si inizia a lavorare con Batch.

## <a name="step-2-upload-task-script-and-data-files"></a>Passaggio 2: Caricare i file di script e i dati delle attività

![Caricare file di input (dati) e applicazione di attività in contenitori][2]
<br/>

In operazione di caricamento di file, *python_tutorial_client.py* definisce innanzitutto insiemi **applicazione** e **input** percorsi dei file in cui si trovano nel computer locale. Quindi carica i file per i contenitori creato nel passaggio precedente.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Tramite la comprensione di elenco, il `upload_file_to_container` funzione chiamata per ogni file alle raccolte e due [ResourceFile] [ py_resource_file] vengono inseriti dati. Il `upload_file_to_container` funzione viene visualizzata sotto:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ py_resource_file] vengono illustrate le attività in Batch con l'URL per un file di archiviazione di Azure che viene scaricata in un nodo di calcolo prima che venga eseguita l'attività. [ResourceFile][py_resource_file]. proprietà **blob_source** specifica l'URL completo del file presenti nell'archiviazione Azure. L'URL può includere una firma di accesso condiviso (SA) che fornisce l'accesso sicuro al file. La maggior parte dei tipi di attività in Batch includono una proprietà *ResourceFiles* , tra cui:

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

In questo esempio non vengono utilizzati i tipi di attività JobPreparationTask o JobReleaseTask, ma è possibile leggere informazioni relative ad esse in [esecuzione attività di preparazione e completamento mansioni nel Batch di Azure nodi di calcolo](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma di accesso condiviso (SA)

Accesso condiviso firme sono stringhe che forniscono l'accesso sicuro a contenitori e BLOB di archiviazione Azure. Lo script *python_tutorial_client.py* utilizza entrambi blob e contenitore condiviso firme di access e viene illustrato come ottenere le stringhe di firma accesso condiviso dal servizio di archiviazione.

- **BLOB condiviso firme access**: ottimali StartTask del pool blob firme accesso condiviso durante il download dei dati di input e di script di attività dallo spazio di archiviazione (vedere [passaggio 3](#step-3-create-batch-pool) ). Il `upload_file_to_container` funzione in *python_tutorial_client.py* contiene il codice che ottiene firma dell'ogni blob accesso condiviso. Ciò avviene tramite la chiamata [BlockBlobService.make_blob_url] [ py_make_blob_url] nel modulo di archiviazione.

- **Firma di accesso condiviso contenitore**: come ogni attività termina la sua attività sul nodo di calcolo, consente di caricare il file di output il contenitore di *output* in archiviazione Azure. A tale scopo, *python_tutorial_task.py* viene utilizzata una firma di accesso condiviso contenitore che fornisce l'accesso di scrittura per il contenitore. Il `get_container_sas_token` funzione in *python_tutorial_client.py* Ottiene firma accesso condiviso del contenitore, che viene quindi passata come argomento della riga di comando per le attività. Passaggio 5 #, [aggiungere attività a un processo](#step-5-add-tasks-to-job), viene illustrato l'uso della contenitori SA.

> [AZURE.TIP] Estrarre la serie di due parti sulla firme accesso condiviso, [parte 1: informazioni sul modello di SA](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: creare e utilizzare un sa con il servizio Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), per altre informazioni sull'accesso sicuro ai dati nel proprio account di archiviazione.

## <a name="step-3-create-batch-pool"></a>Passaggio 3: Creare pool Batch

![Creare un pool di Batch][3]
<br/>

Un Batch **pool** è una raccolta dei nodi di calcolo (macchine virtuali) in cui Batch esegue le attività del processo.

Dopo che carica i file di script e i dati delle attività per l'account di archiviazione, *python_tutorial_client.py* avvia l'interazione con il servizio Batch utilizzando il modulo Batch Python. A tale scopo, un [BatchServiceClient] [ py_batchserviceclient] viene creato:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Successivamente, viene creato un pool dei nodi di calcolo nella finestra account Batch con una chiamata a `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando si crea un pool, è possibile definire un [PoolAddParameter] [ py_pooladdparam] che specifica diverse proprietà per il pool:

- **ID** del pool (*id* - obbligatorio)<p/>Come per la maggior parte delle entità in Batch, il nuovo pool deve avere un ID univoco all'interno di account Batch. Il codice fa riferimento a questo pool utilizzando il relativo ID e si tratta di come è identificare il pool di Azure [portale][azure_portal].

- **Numero dei nodi di calcolo** (*target_dedicated* - obbligatorio)<p/>Questa proprietà specifica quante macchine virtuali devono essere distribuite nel pool. È importante tenere presente che tutti gli account Batch abbiano predefinito **quota** che limita il numero di **Core** (e pertanto nodi di calcolo) in un account di Batch. In [quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md), è possibile trovare le quote per impostazione predefinita e istruzioni su come per [aumentare la quota](batch-quota-limit.md#increase-a-quota) (ad esempio il numero massimo di core nell'account Batch). Se è necessario richiedere "Perché non è possibile il pool di raggiungere più valori X nodi?" la causa potrebbe essere la quota di base.

- **Sistema operativo** per i nodi (*virtual_machine_configuration* **o** *cloud_service_configuration* - obbligatorio)<p/>In *python_tutorial_client.py*, viene creata un pool di nodi Linux mediante un [VirtualMachineConfiguration][py_vm_config]. Il `select_latest_verified_vm_image_with_node_agent_sku` funzionare in `common.helpers` semplifica l'utilizzo di [Macchine virtuali di Azure Marketplace] [ vm_marketplace] immagini. Per ulteriori informazioni sull'utilizzo di immagini Marketplace, vedere [Disposizione Linux nodi in Batch Azure pool di calcolo](batch-linux-nodes.md) .

- **Dimensioni dei nodi di calcolo** (*vm_size* - obbligatorio)<p/>Poiché viene specificata Linux nodi per il nostro [VirtualMachineConfiguration][py_vm_config], si specifica una dimensione di memoria virtuale (`STANDARD_A1` in questo esempio) da [dimensioni per macchine virtuali in Azure](../virtual-machines/virtual-machines-linux-sizes.md). Per ulteriori informazioni nuovamente, vedere [disposizione Linux nodi in Batch Azure pool](batch-linux-nodes.md) .

- **Avviare attività** (*start_task* - non è necessario)<p/>Con la sopra proprietà nodo fisico, è inoltre possibile specificare un [StartTask] [ py_starttask] per il pool (non è necessario). Il StartTask venga eseguito su ciascun nodo come tale nodo viene aggiunto il pool e ogni volta che si riavvia un nodo. Il StartTask è particolarmente utile per la preparazione nodi di calcolo per l'esecuzione di attività, ad esempio l'installazione di applicazioni che eseguono le attività.<p/>In questa applicazione di esempio, il StartTask copiati i file scaricati dallo spazio di archiviazione (che vengono specificati utilizzando proprietà **resource_files** del StartTask) da StartTask di *cartella di lavoro* alla directory *condivisa* che possono accedere a tutte le attività in esecuzione sul nodo. In pratica, viene copiata `python_tutorial_task.py` alla directory condivisa su ciascun nodo come il nodo viene aggiunto il pool, in modo che tutte le attività da eseguire sul nodo possibilità di accedervi.

È possibile notare la chiamata alla `wrap_commands_in_shell` funzione di supporto. Questa funzione utilizza un insieme di comandi separati per crea una sola riga di comando appropriato per proprietà riga un'attività di comando.

Anche significativo frammento di codice precedente è l'utilizzo di due variabili di ambiente nella **riga di comando** proprietà del StartTask: `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`. Ogni nodo di calcolo di un pool di Batch viene configurato automaticamente con diverse variabili di ambiente specifici di Batch. Qualsiasi processo che viene eseguita da un'attività può accedere a tali variabili.

> [AZURE.TIP] Per ulteriori informazioni sull'ambiente di variabili disponibili su nodi di calcolo in un pool di Batch, nonché informazioni nella directory di lavoro di attività, vedere **impostazioni dell'ambiente per le attività** e **file e directory** nella [Panoramica delle funzionalità di Azure Batch](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Passaggio 4: Creare processo Batch

![Crea processo Batch][4]<br/>

Un Batch **processo** è un insieme di attività ed è associata a un pool di nodi di calcolo. Le attività in un processo eseguire su nodi di calcolo del pool associato.

È possibile utilizzare un processo non solo per l'organizzazione e la verifica delle attività in carichi di lavoro correlati, ma anche per l'imposizione di determinati vincoli, ad esempio il massimo di esecuzione del processo di (e di conseguenza, delle attività) e processi priorità in relazione ad altri processi nell'account Batch. In questo esempio, tuttavia, il processo è associato solo al pool creato nel passaggio 3 #. Nessuna proprietà aggiuntive vengono configurati.

Tutti i processi Batch sono associati a uno specifico gruppo. Questa associazione indica quali nodi attività del processo viene eseguita su. Specificare il pool utilizzando [PoolInformation] [ py_poolinfo] proprietà, come illustrato nel frammento di codice riportata di seguito.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Una volta creato un processo, le attività vengono aggiunti per eseguire il lavoro.

## <a name="step-5-add-tasks-to-job"></a>Passaggio 5: Aggiungere attività al processo

![Aggiungere attività al lavoro][5]<br/>
*(1) le attività vengono aggiunte al processo, (2) le attività vengono programmate per l'esecuzione su nodi e (3) le attività scaricare i file di dati per l'elaborazione*

Batch **attività** sono le singole unità di lavoro da eseguire sui nodi di calcolo. Un'attività è una riga di comando ed esegue gli script o eseguibili specificati in tale riga di comando.

Per eseguire effettivamente lavoro, è necessario aggiungere attività a un processo. Ogni [CloudTask] [ py_task] è configurato con una proprietà riga di comando e [ResourceFiles] [ py_resource_file] (come StartTask del pool) che l'attività Scarica il nodo prima che la riga di comando viene eseguita automaticamente. Nell'esempio di ogni attività elabora un solo file. In questo modo, insieme ResourceFiles contiene un singolo elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Quando accedono a variabili di ambiente come `$AZ_BATCH_NODE_SHARED_DIR` o eseguire un'applicazione non trovata nel nodo `PATH`, le righe di comando attività necessario richiamare di shell in modo esplicito, ad esempio con `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Questo requisito è necessario se un'applicazione di esecuzione delle attività nel nodo `PATH` e non fanno riferimento a tutte le variabili di ambiente.

All'interno di `for` ciclo frammento di codice precedente, è possibile vedere che la riga di comando per l'attività viene creata con cinque gli argomenti della riga di comando vengono passati a *python_tutorial_task.py*:

1. **percorso file**: si tratta del percorso locale al file esistente sul nodo. Quando il ResourceFile oggetto `upload_file_to_container` è stato creato nel passaggio 2 precedente, il nome del file è stato usato per questa proprietà (il `file_path` parametro nel costruttore ResourceFile). Indica che è possibile trovare il file nella stessa directory sul nodo *python_tutorial_task.py*.

2. **NUMWORDS**: le prime parole *N* devono essere scritti nel file di output.

3. **storageaccount**: il nome dell'account di archiviazione che possiede il contenitore in cui deve essere caricato l'output di attività.

4. **storagecontainer**: il nome del contenitore di spazio di archiviazione a cui devono essere caricati i file di output.

5. **sastoken**: la firma di accesso condiviso (SA) che fornisce l'accesso di scrittura per il contenitore di **output** in archiviazione Azure. Lo script *python_tutorial_task.py* utilizza la firma di accesso condiviso quando viene creato il relativo riferimento BlockBlobService. In questo modo scrittura al contenitore senza che richiedono un tasto di scelta per l'account di archiviazione.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Passaggio 6: Attività Monitor

![Controllare le attività][6]<br/>
*Script (1) esegue il monitoraggio dell'attività per lo stato di completamento e (2) le attività caricare dati del risultato allo spazio di archiviazione di Azure*

Quando le attività vengono aggiunti a un processo, vengono automaticamente in coda e pianificare l'esecuzione su nodi di calcolo del pool associate al processo. In base alle impostazioni specificate, Batch gestisce Accodamento tutte le attività, pianificazione, la ripetizione e altre funzioni di amministrazione di attività dell'utente.

Sono disponibili molti approcci alla monitoraggio esecuzione dell'attività. Il `wait_for_tasks_to_complete` funzione in *python_tutorial_client.py* fornisce un semplice esempio di Monitoraggio attività per un determinato stato, in questo caso, [completate] [ py_taskstate] stato.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Passaggio 7: Scaricare output attività

![Scaricare output attività dallo spazio di archiviazione][7]<br/>

Ora che il processo, l'output da attività può essere scaricato dallo spazio di archiviazione Azure. Questa operazione viene eseguita con una chiamata a `download_blobs_from_container` in *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] La chiamata a `download_blobs_from_container` in *python_tutorial_client.py* specifica che i file devono essere scaricati alla home directory. È possibile modificare la posizione per l'output.

## <a name="step-8-delete-containers"></a>Passaggio 8: I contenitori Elimina

Poiché vengono addebitate per i dati che si trovano in archiviazione Azure, è sempre consigliabile rimuovere qualsiasi blob che non sono più necessari per i processi Batch. In *python_tutorial_client.py*, questa operazione viene eseguita con tre chiamate a [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passaggio 9: Eliminare il processo e al pool

Nel passaggio finale, viene chiesto di eliminare il processo e al pool create lo script *python_tutorial_client.py* . Sebbene non addebitate per processi e delle attività, non *vengono* addebitate per nodi di calcolo. In questo modo, è consigliabile allocare nodi solo in base alle esigenze. Eliminazione pool inutilizzati possono far parte del processo di manutenzione.

Del BatchServiceClient [JobOperations] [ py_job] e [PoolOperations] [ py_pool] dispongono di corrispondenti metodi di eliminazione, ossia se confermi l'eliminazione:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Tenere presente che addebitate per risorse di elaborazione - eliminazione pool inutilizzato ridurrà costo. Inoltre, tenere presente che un pool se si elimina tutti i nodi di calcolo all'interno di tale pool e che tutti i dati nei nodi sarà irreversibili dopo l'eliminazione di pool.

## <a name="run-the-sample-script"></a>Eseguire lo script di esempio

Quando si esegue lo script *python_tutorial_client.py* dal esercitazioni [esempio][github_article_samples], l'output sarà simile alla seguente. Esiste una pausa in `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mentre vengono creati nodi di elaborazione del pool, avviato, e vengono eseguiti i comandi nell'attività di inizio del pool. Usare il [portale di Azure] [ azure_portal] per controllare il pool, calcolare nodi, processo e attività durante e dopo l'esecuzione. Usare il [portale di Azure] [ azure_portal] o di [Microsoft Azure archiviazione Explorer] [ storage_explorer] per visualizzare le risorse di archiviazione (contenitori e BLOB) che vengono create dall'applicazione.

>[AZURE.TIP] Eseguire lo script *python_tutorial_client.py* dall'interno di `azure-batch-samples/Python/Batch/article_samples` directory. Viene utilizzato un percorso per il `common.helpers` importazione del modulo, pertanto è possibile visualizzare `ImportError: No module named 'common'` se non si esegue il lo script all'interno di questa directory.

Tipico tempo di esecuzione è di **circa 5 a 7 minuti** quando si esegue l'esempio nella configurazione predefinita.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Passaggi successivi

È possibile apportare modifiche alla *python_tutorial_client.py* e *python_tutorial_task.py* sperimentare calcolo diversi scenari. Ad esempio, provare ad aggiungere un ritardo di esecuzione *python_tutorial_task.py* per simulare attività lunga e monitorare le nel portale. Provare aggiunta di altre attività o regolare il numero dei nodi di calcolo. Aggiungere logica per controllare e consentire l'utilizzo di un pool esistente alla velocità di esecuzione.

Ora che si ha familiarità con il flusso di lavoro di base di una soluzione Batch, è possibile eseguire un'analisi ad altre caratteristiche del servizio di Batch.

- Leggere l'articolo [caratteristiche Panoramica del Batch di Azure](batch-api-basics.md) , è consigliabile se si conosce il servizio.
- Avviare gli altri articoli di sviluppo Batch in **sviluppo in modo completo** per il [percorso formativo: Batch][batch_learning_path].
- Estrarre una diversa implementazione di elaborazione il carico di lavoro di "parole primi N" con Batch di [TopNWords] [ github_topnwords] campione.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Creare i contenitori di archiviazione Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Caricare file di input (dati) e applicazione di attività in contenitori"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Creare pool Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Crea processo Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Aggiungere attività al lavoro"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Controllare le attività"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Scaricare output attività dallo spazio di archiviazione"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flusso di lavoro soluzione batch (diagramma completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenziali batch nel portale"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenziali di spazio di archiviazione nel portale"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flusso di lavoro soluzione batch (diagramma minimo)"
