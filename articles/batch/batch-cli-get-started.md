<properties
   pageTitle="Guida introduttiva di Azure Batch CLI | Microsoft Azure"
   description="Ottenere una breve introduzione a comandi Batch in Azure CLI per gestire le risorse del servizio di Azure Batch"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Guida introduttiva di Azure Batch CLI

L'interfaccia della riga di comando di Azure multipiattaforma e su (Azure CLI) consente di gestire gli account di Batch e risorse, ad esempio pool, processi e attività in Linux, Mac e Windows Shell dei comandi. CLI Azure consente di eseguire e script molte attività che svolgere con API Batch, portale Azure e i cmdlet di PowerShell Batch.

In questo articolo si basa su Azure CLI versione 0.10.5.

## <a name="prerequisites"></a>Prerequisiti

* [Installare CLI Azure](../xplat-cli-install.md)

* [Connettere CLI Azure all'abbonamento Azure](../xplat-cli-connect.md)

* Passare alla **modalità di gestione risorse**:`azure config mode arm`

>[AZURE.TIP] È consigliabile che si aggiorna l'installazione di Azure CLI frequentemente per usufruire di miglioramenti e aggiornamenti del servizio.

## <a name="command-help"></a>Guida di comando

È possibile visualizzare il testo della Guida per ogni comando in CLI Azure aggiungendo `-h` come l'opzione solo dopo il comando. Per esempio:

* Per ottenere l'assistenza per il `azure` comando, immettere:`azure -h`
* Per ottenere un elenco di tutti i comandi Batch in CLI, usare:`azure batch -h`
* Per ottenere informazioni sulla creazione di un account di Batch, immettere:`azure batch account create -h`

In caso di dubbi, utilizzare la `-h` opzione della riga di comando per visualizzare la Guida relativa ai comandi CLI Azure.

## <a name="create-a-batch-account"></a>Creare un account di Batch

Sintassi:

    azure batch account create [options] <name>

Esempio:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crea un nuovo account di Batch con i parametri specificati. È necessario specificare almeno un percorso, gruppo di risorse e nome dell'account. Se si dispone già di un gruppo di risorse, crearne uno eseguendo `azure group create`e specificare una delle aree Azure (ad esempio "Ovest Usa") per la `--location` opzione. Per esempio:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Il nome dell'account Batch deve essere univoco all'interno dell'area di Azure che l'account viene creato. Possono contenere solo lettere minuscole caratteri alfanumerici e devono essere 3-24 caratteri. Non è possibile usare caratteri speciali come `-` o `_` nei nomi di account Batch.

### <a name="linked-storage-account-autostorage"></a>Account di archiviazione collegato (autostorage)

È possibile, se necessario, collegare un account di archiviazione **Generale** al proprio account Batch quando si crea. La caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch utilizza archiviazione blob in generale collegata account di archiviazione, come la libreria di [.NET convenzioni di File Batch](batch-task-output.md) . Queste funzioni opzionali supporto per la distribuzione delle applicazioni, che eseguire le attività Batch e il mantenimento dei dati che producono.

Per collegare un account di archiviazione Azure esistente a un nuovo account Batch quando si crea, specificare la `--autostorage-account-id` opzione. Questa opzione richiede l'ID di risorse completo dell'account di archiviazione.

Prima di tutto, visualizzare i dettagli del proprio account di archiviazione:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Quindi usare il valore di **Url** per il `--autostorage-account-id` opzione. Il valore dell'Url inizia con "/ abbonamenti /" e contiene il percorso abbonamento ID e delle risorse per l'account di archiviazione:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Eliminare un account di Batch

Sintassi:

    azure batch account delete [options] <name>

Esempio:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Elimina l'account Batch specificato. Quando richiesto, confermare che si desidera rimuovere l'account (rimozione account può richiedere del tempo per completare).

## <a name="manage-account-access-keys"></a>Gestire i tasti di scelta account

È necessario un tasto di scelta per [creare e modificare le risorse](#create-and-modify-batch-resources) nell'account Batch.

### <a name="list-access-keys"></a>Elenco i tasti di scelta

Sintassi:

    azure batch account keys list [options] <name>

Esempio:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Elenca i tasti di account per l'account Batch specificato.

### <a name="generate-a-new-access-key"></a>Generare una nuova chiave di accesso

Sintassi:

    azure batch account keys renew [options] --<primary|secondary> <name>

Esempio:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Rigenera la chiave account specificato per l'account Batch specificato.

## <a name="create-and-modify-batch-resources"></a>Creare e modificare le risorse Batch

È possibile utilizzare CLI Azure per creare, leggere, aggiornare ed eliminazione risorse Batch (CRUD) come pool, calcolare nodi, processi e attività. Queste operazioni CRUD richiedono il nome dell'account Batch, tasto di scelta rapida ed endpoint. È possibile specificarli con la `-a`, `-k`, e `-u` opzioni o set di [variabili di ambiente](#credential-environment-variables) CLI usa automaticamente (se popolato).

### <a name="credential-environment-variables"></a>Credenziali variabili

È possibile impostare `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, e `AZURE_BATCH_ENDPOINT` variabili anziché specificando `-a`, `-k`, e `-u` opzioni della riga di comando per ogni comando che eseguite. Batch utilizza queste variabili (se impostare) in modo che è possibile omettere il `-a`, `-k`, e `-u` opzioni. Il resto di questo articolo si presuppone utilizzo di tali variabili.

>[AZURE.TIP] Elencare le chiavi con `azure batch account keys list`e visualizzare endpoint dell'account con `azure batch account show`.

### <a name="json-files"></a>File JSON

Quando si creano risorse Batch come pool e processi, è possibile specificare un file JSON contenente la configurazione della risorsa nuove anziché passando relativi parametri come opzioni. Per esempio:

`azure batch pool create my_batch_pool.json`

Sebbene sia possibile eseguire molte operazioni di creazione delle risorse solo le opzioni della riga di comando, alcune caratteristiche richiedono un file in formato JSON che contiene i dettagli della risorsa. Ad esempio, è necessario utilizzare un file JSON se si desidera specificare file di risorse per un'attività di inizio.

Per trovare JSON necessari per creare una risorsa, fare riferimento alla [Guida di riferimento all'API REST Batch] [ rest_api] documentazione su MSDN. Ogni argomento "Aggiungi *tipo di risorsa"*contiene esempio JSON per la creazione della risorsa, è possibile utilizzare come modelli per i file JSON. Ad esempio JSON per la creazione del pool può trovarsi in [Aggiungi un pool a un account][rest_add_pool].

>[AZURE.NOTE] Se si specifica un file JSON quando si crea una risorsa, vengono ignorati tutti gli altri parametri che specificano la riga di comando per la risorsa.

## <a name="create-a-pool"></a>Creare un pool

Sintassi:

    azure batch pool create [options] [json-file]

Esempio (configurazione macchina virtuale):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Esempio (configurazione di servizi Cloud):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crea un pool dei nodi di calcolo nel servizio Batch.

Come detto in [Panoramica delle funzionalità Batch](batch-api-basics.md#pool), sono disponibili due opzioni quando si seleziona un sistema operativo per i nodi del pool: **macchina virtuale** e **Configurazione di servizi Cloud**. Utilizzare la `--image-*` opzioni per creare pool di configurazione della macchina virtuale e `--os-family` la creazione di configurazione di servizi Cloud pool. Non è possibile specificare `--os-family` e `--image-*` opzioni.

È possibile specificare pool [pacchetti di applicazioni](batch-application-packages.md) e la riga di comando per [avviare l'operazione](batch-api-basics.md#start-task). Per specificare il file di risorse per l'inizio dell'operazione, tuttavia, è necessario utilizzare invece un [file JSON](#json-files).

Eliminare un pool con:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Controllare l' [elenco delle immagini macchina virtuale](batch-linux-nodes.md#list-of-virtual-machine-images) valori appropriate per la `--image-*` opzioni.

## <a name="create-a-job"></a>Creare un processo

Sintassi:

    azure batch job create [options] [json-file]

Esempio:

    azure batch job create --id "job001" --pool-id "pool001"

Aggiunge un processo per l'account Batch e specifica il pool in cui eseguire le attività.

Eliminare un processo con:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Pool di elenco, processi, attività e altre risorse

Ogni tipo di risorsa Batch supporta un `list` comando che esegue una query account Batch e vengono elencate le risorse di quel tipo. Ad esempio, è possibile elencare i pool nel proprio account e le attività in un processo:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Elenco risorse in modo efficiente

Per velocizzare la ricerca, è possibile specificare le opzioni della clausola **Selezionare**, **filtro**ed **espandere** per `list` operazioni. Utilizzare queste opzioni per limitare la quantità di dati restituiti dal servizio Batch. Poiché tutti i filtri, si verifica sul lato server, solo i dati che si desidera attraversa in rete. Utilizzare le clausole per salvare la larghezza di banda (e pertanto ora) quando si eseguono le operazioni di elenco.

Ad esempio, verrà restituito solo pool cui ID iniziano con "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

CLI Batch supporta tutti i tre clausole è supportate dal servizio Batch:

* `--select-clause [select-clause]`Restituire un sottoinsieme di proprietà per ogni entità
* `--filter-clause [filter-clause]`Restituire solo entità che corrispondono all'espressione OData specificato
* `--expand-clause [expand-clause]`Ottenere informazioni sulle entità in un'unica chiamata resto sottostante. La clausola Espandi supporta solo il `stats` proprietà al momento.

Per informazioni dettagliate sui tre clausole ed eseguendo query elenco con loro, vedere [il servizio di Azure Batch della Query in modo efficiente](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gestione dei pacchetti di applicazione

Pacchetti di applicazioni offrono un modo semplificato per distribuire le applicazioni dei nodi di elaborazione nei pool. Con CLI Azure, è possibile caricare pacchetti di applicazioni, gestire le versioni di pacchetto ed eliminare pacchetti.

Per creare una nuova applicazione e aggiungere una versione del pacchetto:

**Creare** un'applicazione:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Aggiungere** un pacchetto dell'applicazione:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Attiva** il pacchetto:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Impostare la **versione predefinita** per l'applicazione:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Distribuire un pacchetto dell'applicazione

È possibile specificare uno o più pacchetti di applicazione per la distribuzione quando si crea un nuovo pool. Quando si specifica un pacchetto al momento della creazione del pool, viene distribuito a ciascun nodo come il pool di join nodo. Pacchetti vengono distribuiti anche quando un nodo viene riavviato o ricostruire l'immagine.

Specificare la `--app-package-ref` opzione durante la creazione di un pool per distribuire un pacchetto dell'applicazione in nodi del pool che si connettono del pool. Il `--app-package-ref` opzione accetta un elenco delimitato da punto e virgola di ID dell'applicazione per la distribuzione dei nodi di elaborazione.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Quando si crea un pool utilizzando le opzioni della riga di comando, è attualmente non è possibile specificare *quale* versione di pacchetto dell'applicazione per la distribuzione dei nodi di elaborazione, ad esempio "1.10-beta3". Di conseguenza, è innanzitutto necessario specificare una versione predefinita per l'applicazione con `azure batch application set [options] --default-version <version-id>` prima di creare il pool (vedere la sezione precedente). È possibile, tuttavia, specificare una versione del pacchetto per il pool se si utilizza un [file JSON](#json-files) anziché le opzioni della riga di comando quando si crea il pool.

Disponibili ulteriori informazioni sui pacchetti di applicazioni nella [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md).

>[AZURE.IMPORTANT] È necessario [un account di archiviazione Azure collegamento](#linked-storage-account-autostorage) al proprio account Batch per utilizzare i pacchetti di applicazione.

### <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti di applicazioni del pool

Per aggiornare le applicazioni assegnate a un pool esistente, rilasciare il `azure batch pool set` comando con il `--app-package-ref` opzione:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Per distribuire il nuovo pacchetto di applicazione per calcolare già i nodi di un pool esistente, è necessario riavviare o creare una nuova immagine i nodi:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] È possibile ottenere un elenco dei nodi in un pool insieme i relativi ID nodo con `azure batch node list`.

Tenere presente che è necessario che già configurata l'applicazione con una versione predefinita prima della distribuzione (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Suggerimenti sulla risoluzione dei problemi

Questa sezione è destinata a fornire risorse da utilizzare per la risoluzione dei problemi di Azure CLI. Non necessariamente assistere tutti, ma potrebbe essere utile limitare la causa e scegliere è per le risorse.

* Usare `-h` per ottenere **il testo della Guida** per i comandi CLI

* Usare `-v` e `-vv` per visualizzare l'output comando **dettagliato** ; `-vv` "extra" dettagliate e consente di visualizzare le richieste REST e le risposte effettivo. Queste opzioni sono utili per la visualizzazione di output di errore completo.

* È possibile visualizzare l' **output del comando come JSON** con la `--json` opzione. Ad esempio `azure batch pool show "pool001" --json` Visualizza le proprietà del pool001 nel formato JSON. È possibile quindi copiare e modificare l'output da usare in un `--json-file` (vedere [file JSON](#json-files) in precedenza in questo articolo).

* [Forum di Batch su MSDN] [ batch_forum] è una risorsa di grande aiuto e monitorare attentamente dai membri del team Batch. Assicurarsi di rivolgere le domande vi si riscontrino problemi o informazioni relative a un'attività specifica.

* Operazione non tutte le risorse Batch è attualmente supportato da CLI Azure. Ad esempio, attualmente non è possibile specificare un pacchetto dell'applicazione *versione* per un pool, solo l'ID del pacchetto. In questi casi, potrebbe essere necessario fornire un `--json-file` del comando invece di usare le opzioni della riga di comando. Assicurarsi di mantenersi aggiornati con l'ultima versione CLI di occuparsi miglioramenti futuri.

## <a name="next-steps"></a>Passaggi successivi

*  Vedere [distribuzione di applicazioni con pacchetti di applicazioni Azure Batch](batch-application-packages.md) per scoprire come usare questa caratteristica per gestire e distribuire le applicazioni che eseguire su nodi di elaborazione Batch.

* Vedere per la [Query in modo efficiente il servizio Batch](batch-efficient-list-queries.md) ulteriori informazioni sulla riducendo il numero di elementi e il tipo di informazioni che vengono restituiti per le query a Batch.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx