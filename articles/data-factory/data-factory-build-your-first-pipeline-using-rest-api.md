<properties
    pageTitle="Creare la prima factory di dati (REST) | Microsoft Azure"
    description="In questa esercitazione, si crea una pipeline di Azure Data Factory esempio utilizzando API REST Factory dati."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Esercitazione: Compilare il prima factory Azure dati utilizzando API REST Factory dati
> [AZURE.SELECTOR]
- [Panoramica e i prerequisiti](data-factory-build-your-first-pipeline.md)
- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Manager delle risorse](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

In questo articolo è utilizzare API REST Factory dati per creare il prima factory dati Azure.

## <a name="prerequisites"></a>Prerequisiti
- Leggere l'articolo [Esercitazione Panoramica](data-factory-build-your-first-pipeline.md) e completare i passaggi **prerequisito** .
- Installare [voltare](https://curl.haxx.se/dlwiz/) nel computer in uso. Lo strumento la curvatura con i comandi resto per creare una factory di dati. 
- Seguire le istruzioni di [questo articolo](../resource-group-create-service-principal-portal.md) : 
    1. Creare un'applicazione Web denominata **ADFGetStartedApp** di Azure Active Directory.
    2. È possibile ottenere **ID client** e la **chiave privata**. 
    3. È possibile ottenere **ID tenant**. 
    4. Assegnare l'applicazione **ADFGetStartedApp** per il ruolo di **Collaboratore Factory dati** .  
- Installare [PowerShell Azure](../powershell-install-configure.md).  
- Avviare **PowerShell** ed eseguire il comando seguente. Tenere aperta Azure PowerShell fino alla fine dell'esercitazione. Se chiuso e riaperto, è necessario eseguire di nuovo i comandi.
    1. Eseguire **l'Accesso AzureRmAccount** e immettere il nome utente e password utilizzati per accedere al portale di Azure.  
    2. Eseguire **Get-AzureRmSubscription** per visualizzare tutte le sottoscrizioni per l'account.
    3. Eseguire **AzureRmSubscription Get - SubscriptionName NameOfAzureSubscription | Set AzureRmContext** per selezionare l'abbonamento a cui si desidera utilizzare. Sostituire **NameOfAzureSubscription** con il nome del proprio abbonamento Azure. 
3. Creare un gruppo di risorse Azure denominato **ADFTutorialResourceGroup** eseguendo il seguente comando di PowerShell:  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Alcune delle procedure in questa esercitazione si supponga di utilizza il gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, è necessario utilizzare il nome del gruppo di risorse al posto di ADFTutorialResourceGroup in questa esercitazione.

## <a name="create-json-definitions"></a>Creare definizioni di JSON
Creare i seguenti file JSON presenti nella cartella in cui si trova curl.exe. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Nome deve essere univoco globale, in modo che potrebbe essere necessario prefisso/suffisso ADFCopyTutorialDF per renderlo un nome univoco. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Sostituire **nome account** e **accountkey** con nome e la chiave del proprio account di archiviazione Azure. Per informazioni su come ottenere il codice di accesso di spazio di archiviazione, vedere [visualizzare, copia e lo spazio di archiviazione rigenera i tasti di scelta](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

Nella tabella seguente vengono descritte le proprietà JSON utilizzata il frammento di codice:

| Proprietà | Descrizione |
| :------- | :---------- |
| Versione | Specifica che la versione di HDInsight creata da 3,2. | 
| ClusterSize | Dimensioni del cluster HDInsight. | 
| TimeToLive | Specifica che il tempo di inattività per cluster HDInsight prima dell'eliminazione. |
| linkedServiceName | Specifica l'account di archiviazione che viene usata per archiviare i registri generati da HDInsight |

Noti quanto segue: 

- Data Factory crea automaticamente un cluster di HDInsight **basato su Windows** con JSON sopra. È possibile che anche creare un cluster di HDInsight **basato su Linux** . Per informazioni dettagliate, vedere [Servizi collegati HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- È possibile utilizzare **il proprio cluster HDInsight** invece di usare un cluster di HDInsight su richiesta. Per informazioni dettagliate, vedere [Servizi collegati HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- Cluster HDInsight crea un **contenitore predefinito** nell'archiviazione blob specificato nel JSON (**linkedServiceName**). HDInsight non viene eliminato il contenitore quando il cluster viene eliminato. Questo comportamento risulta in base alla progettazione. Con un servizio su richiesta HDInsight collegati, un HDInsight cluster viene creato ogni volta che viene elaborata una sezione, a meno che non esiste un esistente live cluster (**timeToLive**) e viene eliminato al termine dell'elaborazione.

    Vengono elaborate più sezioni, molti contenitori vengono visualizzate in archiviazione blob Azure. Se si ha bisogno per la risoluzione dei problemi dei processi, da eliminare in modo da ridurre i costi di spazio di archiviazione. I nomi dei contenitori seguono un motivo: "alimentatore automatico**yourdatafactoryname**-**linkedservicename**- datetimestamp". Usare strumenti, ad esempio [Esplora risorse di archiviazione](http://storageexplorer.com/) per eliminare i contenitori di spazio di archiviazione blob Azure.

Per informazioni dettagliate, vedere [Servizi collegati HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


Il JSON definisce un set di dati denominato **AzureBlobInput**, che rappresenta i dati di input per un'attività nella pipeline. Viene inoltre specificata che i dati di input si trovano nel contenitore di blob denominato **adfgetstarted** e la cartella denominata **inputdata**.

Nella tabella seguente vengono descritte le proprietà JSON utilizzata il frammento di codice:

| Proprietà | Descrizione |
| :------- | :---------- |
| tipo | La proprietà di tipo sia impostata su AzureBlob perché dati si trovano in archiviazione blob Azure. |  
| linkedServiceName | fa riferimento a StorageLinkedService creata in precedenza. |
| fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, tutti i file dal folderPath sono scelto. In questo caso, viene elaborato solo il input.log. |
| tipo | I file di log sono in formato testo, in modo che serve formato testo. | 
| columnDelimiter | colonne nei file di log sono delimitate da una virgola () |
| Frequenza/intervallo | frequenza impostata su mese e l'intervallo è 1, in modo che le sezioni inpue disponibili mensile. | 
| esterno | Questa proprietà è impostata su true se i dati di input non viene generati dal servizio dati Factory. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

Il JSON definisce un set di dati denominato **AzureBlobOutput**, che rappresenta i dati di output per un'attività nella pipeline. Viene inoltre specificata che i risultati vengono memorizzati in un contenitore di blob chiamato **adfgetstarted** e la cartella denominata **partitioneddata**. La sezione **disponibilità** specifica che il set di dati di output viene visualizzato su base mensile.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Sostituire **storageaccountname** con nome del proprio account di archiviazione Azure. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

Frammento di JSON, si sta creando una pipeline è costituita da una singola attività che utilizza Hive per elaborare i dati in un cluster di HDInsight.

Nella finestra account di archiviazione Azure (specificata da scriptLinkedService, denominata **StorageLinkedService**) e nella cartella **script** il contenitore **adfgetstarted**, è archiviato il file di script Hive, **partitionweblogs.hql**.

Sezione **definisce** specifica le impostazioni di runtime passati allo script hive come valori di configurazione Hive (ad esempio ${hiveconf: inputtable}, {hiveconf:partitionedtable} $).

Le proprietà di **inizio** e **Fine** della pipeline specifica del periodo attivo della pipeline.

Attività JSON, specificare che lo script Hive viene eseguita nel calcolo specificato per il **linkedServiceName** - **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Per ulteriori informazioni sulle proprietà JSON utilizzata nell'esempio precedente, vedere [Anatomia di una Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 

## <a name="set-global-variables"></a>Impostare le variabili globali

In Azure PowerShell, eseguire i comandi seguenti dopo la sostituzione dei valori con uno personalizzato:

> [AZURE.IMPORTANT] Per istruzioni su come ottenere ID client, segreto del client, vedere la sezione [Prerequisiti](#prerequisites) tenant ID e ID abbonamento.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Eseguire l'autenticazione con AAD

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Creare factory di dati

In questo passaggio si crea una Factory di dati di Azure denominato **FirstDataFactoryREST**. Una factory dati può avere una o più tubazioni. Una pipeline può contenere uno o più attività. Ad esempio, un'attività di copia per copiare i dati da una fonte a un archivio di dati di destinazione e un'attività di HDInsight Hive per eseguire script Hive per trasformare i dati. Eseguire i comandi seguenti per creare factory dati: 

1. Il comando Assegna a variabile denominata **cmd**. 

    Verificare che il nome del produttore dati specificate (ADFCopyTutorialDF) corrisponda al nome specificato in **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se la factory di dati è stata creata correttamente, viene visualizzato JSON per factory dati nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.  

        Write-Host $results

Noti quanto segue:
 
- Il nome di Azure Data Factory deve essere univoco globale. Se viene visualizzato l'errore nei risultati: **dati factory nome "FirstDataFactoryREST" è non disponibile**, eseguire la procedura seguente:  
    1. Modificare il nome (ad esempio yournameFirstDataFactoryREST) nel file **datafactory.json** . Vedere l'argomento relativo a [Dati Factory - regole di denominazione](data-factory-naming-rules.md) per le regole di denominazione per gli elementi di dati Factory.
    2. Nel primo comando nel punto in cui la variabile **$cmd** viene assegnata un valore, sostituire FirstDataFactoryREST con il nuovo nome ed eseguire il comando. 
    3. Eseguire i due comandi per richiamare l'API REST per creare la factory di dati e stampare i risultati dell'operazione. 
- Per creare le istanze di Factory dati, è necessario essere un amministratore o un collaboratore della sottoscrizione Azure
- Il nome del produttore dati può essere registrato come un nome DNS in futuro e quindi diventano visibile al pubblico.
- Se viene visualizzato l'errore: "**l'abbonamento non è registrato per utilizzare spazio dei nomi Microsoft.DataFactory**", eseguire una delle operazioni seguenti e riprovare: 

    - In Azure PowerShell eseguire il seguente comando per registrare il provider di dati Factory: 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        È possibile eseguire il seguente comando per verificare che la Data Factory provider registrato: 
    
            Get-AzureRmResourceProvider
    - Accedere usando l'abbonamento Azure al [portale di Azure](https://portal.azure.com) e passare a una pala Factory dati (o) creare una factory di dati nel portale di Azure. Questa azione registra automaticamente il provider di necessità.

Prima di creare una pipeline, è necessario creare alcuni entità Factory dati prima di tutto. Creare innanzitutto servizi collegati per creare un collegamento dati archivi/calcola all'archivio dati, definire input e output set di dati per rappresentare i dati negli archivi dati collegati. 

## <a name="create-linked-services"></a>Creazione di servizi collegati 
In questo passaggio si collegare l'account di archiviazione Azure e un cluster di Azure HDInsight su richiesta dell'ambiente di produzione di dati. L'account di archiviazione Azure contiene i dati di input e di output per la pipeline in questo esempio. Per eseguire script Hive specificato nell'attività di pipeline in questo esempio viene usato il servizio di HDInsight collegato. 

### <a name="create-azure-storage-linked-service"></a>Creare il servizio di archiviazione di Azure collegato
In questo passaggio si collega l'account di archiviazione Azure a factory i dati. Con questa esercitazione, utilizzare lo stesso account Azure lo spazio di archiviazione per archiviare i dati di ingresso/uscita e il file di script HQL.

1. Il comando Assegna a variabile denominata **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il servizio collegato è stato completato, viene visualizzato JSON per il servizio collegato nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Creare il servizio di Azure HDInsight collegato
In questo passaggio è collegare un cluster di HDInsight su richiesta su factory i dati. Cluster HDInsight automaticamente creato in fase di esecuzione ed eliminato al termine elaborazione e inattive per il periodo di tempo specificato. È possibile utilizzare il proprio cluster HDInsight invece di usare un cluster di HDInsight su richiesta. Per informazioni dettagliate, vedere [Calcolare servizi collegati](data-factory-compute-linked-services.md) .  

1. Il comando Assegna a variabile denominata **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il servizio collegato è stato completato, viene visualizzato JSON per il servizio collegato nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.  

        Write-Host $results

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio si crea set di dati per rappresentare l'input e generare l'output dei dati per l'elaborazione di Hive. Questi set di dati fanno riferimento a **StorageLinkedService** creata precedentemente in questa esercitazione. Punti di servizio collegato a un account di archiviazione di Azure e set di dati specificano contenitore, cartella, nome del file in archiviazione contenente input e di output dei dati.   

### <a name="create-input-dataset"></a>Creare set di dati input
In questo passaggio è creare il set di dati di input per rappresentare inpui dati archiviati in archiviazione Blob Azure.

1. Il comando Assegna a variabile denominata **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, viene visualizzato JSON per il set di dati nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Creare set di dati di output
In questo passaggio è creare il set di dati di output per rappresentare dati di output archiviati in archiviazione Blob Azure.

1. Il comando Assegna a variabile denominata **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, viene visualizzato JSON per il set di dati nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Creare pipeline
In questo passaggio si crea il prima pipeline con un'attività **HDInsightHive** . Sezione input è disponibile mensile (frequenza: mese, intervallo: 1), sezione output prodotto mensile e utilità di pianificazione per l'attività viene inoltre impostata su mensile. Le impostazioni per il set di dati di output e l'utilità di pianificazione attività devono corrispondere. Set di dati di output è al momento, quali unità della programmazione, è necessario creare un set di dati di output anche se l'attività non produce alcun output. Se l'attività non richiede qualsiasi tipo di input, è possibile ignorare la creazione di set di dati di input.  

Verificare che il file **input.log** nella cartella **adfgetstarted/inputdata** in archiviazione blob Azure visualizzato e il seguente comando per distribuire la pipeline. Poiché l'ora di **inizio** e **Fine** è impostata in passato e **isPaused** è impostato su false, viene eseguita la pipeline (attività nella pipeline) immediatamente dopo la distribuzione. 

1. Il comando Assegna a variabile denominata **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Eseguire il comando tramite **Comando richiama**.

        $results = Invoke-Command -scriptblock $cmd;
3. Visualizzare i risultati. Se il set di dati è stato creato correttamente, viene visualizzato JSON per il set di dati nei **risultati**; in caso contrario, viene visualizzato un messaggio di errore.  

        Write-Host $results
5. Congratulazioni, creato il prima pipeline tramite PowerShell Azure!

## <a name="monitor-pipeline"></a>Pipeline di monitor
In questo passaggio, utilizzare API REST Factory dati per eseguire il monitoraggio delle sezioni realizzate tramite la pipeline.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Creazione di un cluster di HDInsight su richiesta in genere accetta un intervallo di tempo (circa 20 minuti). Prevedere il gadget pipeline di impieghi **30 minuti** per elaborare la sezione.  

Eseguire il comando richiama e quello successivo finché non viene visualizzata la sezione in stato **Pronto** o **non riuscito** . Quando la sezione è pronta, selezionare la cartella **partitioneddata** nel contenitore di **adfgetstarted** in archiviazione blob per i dati di output.  La creazione di un cluster di HDInsight su richiesta in genere richiedono tempo.

![dati di output](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] Il file di input viene eliminato quando la sezione viene elaborata correttamente. Pertanto, se si desidera eseguire di nuovo la sezione o eseguire nuovamente l'esercitazione, caricare il file di input (input.log) nella cartella inputdata del contenitore adfgetstarted.

Utilizzare il portale di Azure per eseguire il monitoraggio delle sezioni e risolvere eventuali problemi. Visualizzare i dettagli [monitorare pipeline Azure nel portale](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) .  

## <a name="summary"></a>Riepilogo 
Per creare una factory di dati di Azure per elaborare i dati in questa esercitazione, eseguendo Hive script in un cluster di hadoop HDInsight. È utilizzato l'Editor di Factory dei dati nel portale di Azure per eseguire la procedura seguente:  

1.  Creare una **factory di dati**di Azure.
2.  Creare due **servizi collegati**:
    1.  Servizio di **Archiviazione azure** collegati per collegare l'archiviazione blob Azure contenente i file di ingresso/uscita su factory dati.
    2.  Servizi collegati nella richiesta di **Azure HDInsight** collegare un cluster di HDInsight Hadoop su richiesta la factory di dati. Azure Data Factory crea un HDInsight Hadoop cluster just-in-time per elaborare dati di input e produrre dati di output. 
3.  Creare due **set di dati**, che descrivono i dati di input e di output per l'attività HDInsight Hive nella pipeline. 
4.  Una **pipeline** con un'attività **HDInsight Hive** . 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster di Azure HDInsight su richiesta. Per informazioni sull'uso di un'attività di copia per copiare i dati da un archivio Blob Azure SQL Azure, vedere [Esercitazione: copiare i dati da un archivio Blob Azure SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Vedere anche
| Argomento | Descrizione |
| :---- | :---- |
| [Dati di riferimento all'API REST Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Vedere la documentazione completa su Factory dati cmdlet |
| [Attività di trasformazione dati](data-factory-data-transformation-activities.md) | In questo articolo fornisce un elenco di attività di trasformazione dati (ad esempio trasformazione HDInsight Hive è utilizzata in questa esercitazione) supportati da Azure Data Factory. |
| [Pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo illustra gli aspetti pianificazione e l'esecuzione del modello di applicazione Azure Data Factory. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo è utile comprendere tubazioni e le attività in Azure Data Factory e sul loro utilizzo per costruire-to-end basati sui dati flussi di lavoro per la scenario o azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo è utile comprendere set di dati di Azure Data Factory.
| [Monitorare e gestire pipeline con Azure pale portale](data-factory-monitor-manage-pipelines.md) | In questo articolo viene descritto come monitorare, gestire ed eseguire il debug la pipeline utilizzando Azure pale portale. |
| [Monitorare e gestire mediante il monitoraggio App](data-factory-monitor-manage-app.md) | In questo articolo viene descritto come monitorare, gestire ed eseguire il debug mediante il monitoraggio e gestione delle App. 

