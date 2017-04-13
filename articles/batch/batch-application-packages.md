<properties
    pageTitle="Installazione dell'applicazione semplice e gestione nel Batch di Azure | Microsoft Azure"
    description="Utilizzare la funzionalità di pacchetti applicazione Azure Batch per gestire facilmente più applicazioni e le versioni per l'installazione nel Batch di nodi di calcolo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Distribuzione di applicazioni con pacchetti di applicazioni Azure Batch

La funzionalità di pacchetti di applicazione del Batch di Azure offre semplicità di gestione delle applicazioni, attività e la distribuzione dei nodi di elaborazione del pool. Con i pacchetti di applicazione, è possibile caricare e gestire più versioni delle applicazioni, che eseguire le attività, inclusi i propri file di supporto. È quindi possibile automaticamente distribuire uno o più di queste applicazioni dei nodi di elaborazione del pool.

In questo articolo si imparerà a caricare e gestire i pacchetti di applicazioni nel portale di Azure. Sarà quindi informazioni su come installarli su nodi di calcolo del pool con [.NET Batch] [ api_net] raccolta.

> [AZURE.NOTE] La funzionalità di pacchetti applicazione qui descritta sostituisce la caratteristica "Batch app" disponibile nelle versioni precedenti del servizio.

## <a name="application-package-requirements"></a>Requisiti di pacchetto dell'applicazione

È necessario [un account di archiviazione Azure collegamento](#link-a-storage-account) al proprio account Batch per utilizzare i pacchetti di applicazione.

La funzione di pacchetti applicazione descritta in questo articolo è compatibile *solo* con i pool Batch che sono stati creati dopo 10 marzo 2016. Pacchetti di applicazioni non verrà distribuiti per calcolare i nodi pool creati prima di tale data.

Questa caratteristica è stata introdotta in [API REST Batch] [ api_rest] versione 2015-12-01.2.2 e il corrispondente [.NET Batch] [ api_net] versione raccolta 3.1.0. È consigliabile utilizzare l'ultima versione dell'API sempre quando si lavora con Batch.

> [AZURE.IMPORTANT] Al momento solo *CloudServiceConfiguration* pool supporta pacchetti di applicazioni. Non è possibile utilizzare i pacchetti di applicazioni in pool creati usando VirtualMachineConfiguration immagini. Vedere la sezione [configurazione delle macchine virtuali](batch-linux-nodes.md#virtual-machine-configuration) di [provisioning Linux nodi in Batch Azure pool](batch-linux-nodes.md) per ulteriori informazioni sulle due diverse configurazioni.

## <a name="about-applications-and-application-packages"></a>Sulle applicazioni e di pacchetti di applicazioni

All'interno di Batch di Azure, un' *applicazione* fa riferimento a un insieme di file binari versione che è possibile scaricare automaticamente dei nodi di elaborazione del pool. Un *pacchetto dell'applicazione* fa riferimento a un *determinato set* di tali file binari e rappresenta una determinata *versione* dell'applicazione.

![Diagramma di alto livello delle applicazioni e di pacchetti di applicazioni][1]

### <a name="applications"></a>Applicazioni

Un'applicazione nel Batch contiene uno o più applicazioni pacchetti e consente di specificare le opzioni di configurazione dell'applicazione. Un'applicazione, ad esempio possibile specificare la versione di pacchetto dell'applicazione predefinita per l'installazione su nodi di calcolo e se i pacchetti possono essere aggiornati o eliminati.

### <a name="application-packages"></a>Pacchetti di applicazioni

Un pacchetto dell'applicazione è un file ZIP che contiene i file binari dell'applicazione e i file di supporto necessari per l'esecuzione, le attività. Ogni pacchetto dell'applicazione rappresenta una versione specifica dell'applicazione.

È possibile specificare i pacchetti di applicazioni a livello di pool e attività. È possibile specificare una o più di questi pacchetti e, facoltativamente, una versione quando si crea un pool o un'attività.

* **Pacchetti di applicazioni pool** vengono distribuiti a *tutti* i nodi del pool. Le applicazioni vengono distribuite quando un nodo viene aggiunto un pool e quando viene riavviato o ricostruire l'immagine.

    Pacchetti di applicazioni pool sono adatti quando tutti i nodi di un pool di esecuzione delle attività del processo. Quando si crea un pool ed è possibile aggiungere o aggiornare i pacchetti del pool esistente, è possibile specificare uno o più pacchetti di applicazioni. Se si aggiornano i pacchetti di applicazioni del pool esistente, è necessario riavviare i nodi per installare il nuovo pacchetto.

* **Pacchetti di applicazioni attività** vengono distribuite solo a un nodo di calcolo pianificato per eseguire un'operazione appena prima di eseguire la riga di comando dell'attività. Se il pacchetto di applicazione specificata e la versione è già incluso il nodo, pertanto non verrà ridistribuito e viene utilizzato il pacchetto esistente.

    Pacchetti di applicazioni attività sono utili in ambienti pool condiviso, in processi diversi vengono eseguiti su un pool e il pool non viene eliminato al termine dell'esecuzione un processo. Se il processo ha meno attività più nodi nel pool, pacchetti di applicazioni attività possono ridurre a icona il trasferimento di dati poiché l'applicazione viene distribuito solo per i nodi che eseguono attività.

    Altri scenari che possono trarre vantaggio da pacchetti di applicazioni attività sono processi che utilizzano un'applicazione di grandi dimensioni in particolare, ma per solo un numero limitato di attività. Ad esempio, una fase pre-elaborazione oppure un'attività di unione, dove l'applicazione pre-elaborazione o unione è ad alta densità.

> [AZURE.IMPORTANT] Esistono limitazioni sul numero di applicazioni e di pacchetti di applicazioni all'interno di un account di Batch, nonché le dimensioni massime pacchetto. Per informazioni su questi limiti, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md) .

### <a name="benefits-of-application-packages"></a>Vantaggi dei pacchetti di applicazioni

Pacchetti di applicazioni consente di semplificare il codice della soluzione Batch e ridurre il sovraccarico necessario per gestire le applicazioni che eseguono le attività.

Attività di inizio del pool non è necessario specificare un lungo elenco di singoli file di risorse per installare nei nodi. Non è necessario gestire manualmente più versioni di file dell'applicazione di archiviazione Azure o nei nodi del. E non occorre preoccuparsi di generazione [Sa URL](../storage/storage-dotnet-shared-access-signature-part-1.md) per accedere al proprio account di archiviazione per il file. Batch funziona in background con Azure lo spazio di archiviazione per archiviare i pacchetti di applicazioni e distribuirle per nodi di calcolo.

## <a name="upload-and-manage-applications"></a>Caricare e gestire l'applicazione

È possibile utilizzare il [portale di Azure] [ portal] o alla raccolta [Batch Management.NET](batch-management-dotnet.md) per gestire i pacchetti di applicazioni nell'account Batch. Nelle sezioni successive, è innanzitutto collegare un account di archiviazione, quindi discutere aggiungendo applicazioni e pacchetti e gestirli con il portale.

### <a name="link-a-storage-account"></a>Collegare un account di archiviazione

Per utilizzare pacchetti di applicazioni, è necessario prima collegare un account di archiviazione Azure all'account Batch. Se non è stato ancora configurato un account di archiviazione per l'account Batch, il portale di Azure verrà visualizzato un avviso per la prima volta si fa clic sul riquadro di **applicazioni** in e il **conto Batch** .

> [AZURE.IMPORTANT] Batch attualmente supporta *solo* il tipo di account di archiviazione **Generale** come descritto nel passaggio 5, [Crea un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account), [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione Azure al proprio account Batch, collegamento *solo* un account di archiviazione **Generale** .

![Alcun avviso account configurato lo spazio di archiviazione nel portale di Azure][9]

Il servizio Batch utilizza l'account associato lo spazio di archiviazione per l'archiviazione e il recupero dei pacchetti di applicazioni. Dopo aver collegato due account, è possibile Batch automaticamente distribuire pacchetti archiviati in account di archiviazione collegate ai nodi di calcolo. Scegliere **le impostazioni di account di archiviazione** e **l'avviso** e quindi scegliere **Account di archiviazione** e il **Conto dello spazio di archiviazione** per collegare un account di archiviazione al proprio account Batch.

![Scegliere blade account lo spazio di archiviazione nel portale di Azure][10]

È consigliabile creare un'archiviazione account *specificamente* per l'utilizzo con l'account di Batch e selezionarlo qui. Per informazioni dettagliate su come creare un account di archiviazione, vedere "Creare un account di archiviazione" in [account di archiviazione su Azure](../storage/storage-create-storage-account.md). Dopo aver creato un account di archiviazione, è possibile quindi collegarlo al proprio account Batch utilizzando e il **Conto dello spazio di archiviazione** .

> [AZURE.WARNING] Poiché Batch utilizza lo spazio di archiviazione di Azure per archiviare i pacchetti di applicazioni, vengono [addebitate come di consueto] [ storage_pricing] per i dati di blob di blocco. Assicurarsi di prendere in considerazione le dimensioni e il numero dei pacchetti di applicazioni e rimuovere periodicamente pacchetti deprecati per ridurre al minimo costo.

### <a name="view-current-applications"></a>Visualizzazione di applicazioni

Per visualizzare le applicazioni nell'account Batch, scegliere la voce di menu **applicazioni** nel menu a sinistra durante la visualizzazione e il **conto Batch** .

![Riquadro di applicazioni][2]

Verrà aperta e **l'applicazioni** :

![Elencare le applicazioni][3]

E **l'applicazioni** Visualizza l'ID di ogni applicazione nel proprio account e le proprietà seguenti:

* **Pacchetti**, ossia il numero di versioni associati all'applicazione.
* **Versione predefinita**- versione installata se non si specifica una versione quando si imposta l'applicazione di un pool. Questa impostazione è facoltativa.
* **Consenti aggiornamenti**, ossia il valore che specifica se pacchetto aggiornamenti, eliminazioni e aggiunte consentiti. Se questo è impostato su **No**, pacchetto aggiornamenti ed eliminazioni sono disabilitate per l'applicazione. È possibile aggiungere solo nuovo pacchetto le versioni delle applicazioni. Il valore predefinito è **Sì**.

### <a name="view-application-details"></a>Visualizzare i dettagli dell'applicazione

Fare clic su un'applicazione di e **l'applicazioni** per aprire e il che include i dettagli per tale applicazione.

![Dettagli dell'applicazione][4]

In e il dettagli dell'applicazione, è possibile configurare le impostazioni seguenti per l'applicazione.

* **Consenti aggiornamenti**, ossia specificare se i pacchetti di applicazioni possono essere aggiornati o eliminati. Vedere "Aggiornare o eliminare un pacchetto dell'applicazione" più avanti in questo articolo.
* **Versione predefinita**- specificare un pacchetto dell'applicazione predefinita per la distribuzione per nodi di calcolo.
* **Nome visualizzato**, ossia specificare un "" nome descrittivo il Batch soluzione è possibile utilizzare durante la visualizzazione di informazioni sull'applicazione, ad esempio nell'interfaccia utente di un servizio che si forniscono ai clienti tramite Batch.

### <a name="add-a-new-application"></a>Aggiungere una nuova applicazione

Per creare una nuova applicazione, aggiungere un pacchetto dell'applicazione e specificare un ID univoco e nuova applicazione. Pacchetto dell'applicazione prima che aggiunta con il nuovo ID applicazione creerà anche la nuova applicazione.

Fare clic su **Aggiungi** nella e **applicazioni** per aprire e il **nuova applicazione** .

![Nuova pala applicazione nel portale di Azure][5]

E il **Nuovo applicazione** fornisce i campi seguenti per specificare le impostazioni dell'applicazione nuova e pacchetto dell'applicazione.

**Id applicazione**

In questo campo specifica l'ID dell'applicazione nuovo è soggette le regole di convalida ID Batch Azure standard:

* Possono contenere qualsiasi combinazione di caratteri alfanumerici, inclusi trattini e caratteri di sottolineatura.
* Non può contenere più di 64 caratteri.
* Deve essere univoco all'interno di account Batch.
* Non conservando maiuscole e minuscole e maiuscole/minuscole.

**Versione**

Specifica la versione del pacchetto dell'applicazione che si sta caricando. Stringhe di versione sono soggette le regole di convalida seguenti:

* Possono contenere qualsiasi combinazione di caratteri alfanumerici, inclusi trattini, caratteri di sottolineatura e periodi.
* Non può contenere più di 64 caratteri.
* Deve essere univoco all'interno dell'applicazione.
* Maiuscole/minuscole mantenimento del e maiuscole/minuscole.

**Pacchetto dell'applicazione**

In questo campo specifica il file con estensione zip contenente i file binari dell'applicazione e i file di supporto necessari per eseguire l'applicazione. Selezionare la casella **Selezionare un file** o l'icona di cartella per individuare e selezionare un file ZIP che contiene il file dell'applicazione.

Dopo aver selezionato un file, fare clic su **OK** per avviare il caricamento allo spazio di archiviazione Azure. Una volta completato l'operazione di caricamento, si riceverà una notifica e verrà chiusa e il. A seconda delle dimensioni del file che si sta caricando e la velocità della connessione di rete, questa operazione può richiedere del tempo.

> [AZURE.WARNING] Prima del completamento dell'operazione di upload, non chiudere e il **nuova applicazione** . In questo modo verrà interrotto il processo di caricamento.

### <a name="add-a-new-application-package"></a>Aggiungere un nuovo pacchetto di applicazione

Per aggiungere una nuova versione di pacchetto di applicazione per un'applicazione esistente, selezionare un'applicazione di e **l'applicazioni** , fare clic su **pacchetti**, quindi fare clic su **Aggiungi** per aprire e il **pacchetto di Aggiungi** .

![Aggiungere blade pacchetto dell'applicazione nel portale di Azure][8]

Come si può notare, i campi corrispondono a quelle della stessa e **nuova applicazione** , ma la casella **id applicazione** è disabilitata. Come per la nuova applicazione, specificare la **versione** per il nuovo pacchetto, passare al file zip del **pacchetto dell'applicazione** e quindi fare clic su **OK** per caricare il pacchetto.

### <a name="update-or-delete-an-application-package"></a>Aggiornare o eliminare un pacchetto dell'applicazione

Per aggiornare o eliminare un pacchetto di applicazione esistente, aprire e il dettagli per l'applicazione, fare clic su **pacchetti** per aprire e il **pacchetti** , fare clic **sui puntini di sospensione** nella riga del pacchetto dell'applicazione che si desidera modificare e selezionare l'azione che si desidera eseguire.

![Aggiornare o eliminare pacchetto nel portale di Azure][7]

**Aggiornamento**

Quando si fa clic su **Aggiorna**, viene visualizzato e il *pacchetto di aggiornamento* . Questo blade è simile a e il *nuovo pacchetto di applicazione* , tuttavia è abilitato solo pacchetto campo per la selezione, che consente di specificare un nuovo file ZIP da caricare.

![Blade pacchetto di aggiornamento nel portale di Azure][11]

**Elimina**

Quando si fa clic su **Elimina**, verrà richiesto di confermare l'eliminazione della versione del pacchetto e Batch Elimina il pacchetto dallo spazio di archiviazione Azure. Se si elimina la versione predefinita di un'applicazione, viene rimosso l'impostazione della **versione predefinita** per l'applicazione.

![Eliminare l'applicazione][12]

## <a name="install-applications-on-compute-nodes"></a>Installare applicazioni su nodi di calcolo

Dopo aver esaminato come gestire i pacchetti di applicazioni con il portale di Azure, è possibile viene illustrato come distribuirle per nodi di calcolo e l'esecuzione con le attività Batch.

### <a name="install-pool-application-packages"></a>Installare pacchetti di applicazioni pool

Per installare un pacchetto dell'applicazione in tutti i nodi di calcolo di un pool, specificare una o più applicazioni del pacchetto *riferimenti* per il pool. Pacchetti di applicazioni che si specifica un pool di sono installati in ogni nodo di calcolo quando si aggiunge il nodo del pool e quando il nodo viene riavviato ricostruire l'immagine.

In .NET Batch, specificare una o più [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] quando si crea un nuovo pool oppure per un pool esistente. [ApplicationPackageReference] [ net_pkgref] classe specifica un ID applicazione e versione installare in un pool nodi di calcolo.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Se una distribuzione di pacchetto dell'applicazione non riesce per qualsiasi motivo, il servizio Batch contrassegna [inutilizzabile]nodo[net_nodestate], e non attività vengono programmate per l'esecuzione su tale nodo. In questo caso, è necessario **riavviare** il nodo a reinizializzare distribuzione del pacchetto. Riavviare il nodo consente anche di programmazione delle attività nuovamente sul nodo.

### <a name="install-task-application-packages"></a>Installare pacchetti di applicazione delle attività

Simile a un pool, specificare applicazione pacchetto *riferimenti* per un'attività. Quando un'attività è programmata per l'esecuzione su un nodo, il pacchetto viene scaricato ed estratto prima riga di comando dell'attività viene eseguito. Se un pacchetto specificato e una versione è già installato sul nodo, il pacchetto non è stato scaricato e viene utilizzato il pacchetto esistente.

Per installare un pacchetto dell'applicazione di attività, configurare dell'attività [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] proprietà:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Eseguire le applicazioni installate

I pacchetti specificata per un'attività o un pool vengono scaricati ed estratti nella directory con nome all'interno di `AZ_BATCH_ROOT_DIR` del nodo. Batch crea anche una variabile di ambiente che contiene il percorso della directory denominato. Le righe di comando attività usare questa variabile di ambiente quando si fa riferimento l'applicazione sul nodo. La variabile è nel formato seguente:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`e `version` sono valori che non corrispondono alla versione dell'applicazione e pacchetto specificate per la distribuzione. Ad esempio, se è specificato che versione 2.7 applicazione *miscelatore* deve essere installato, le righe di comando di attività da usare questa variabile di ambiente per accedere ai file:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se si specifica una versione predefinita per un'applicazione, è possibile omettere il suffisso versione. Ad esempio, se si imposta "2.7" come la versione predefinita per l'applicazione *miscelatore*, le attività possono fare riferimento la variabile di ambiente seguenti e verranno eseguite versione 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Frammento di codice seguente mostra una riga di comando attività di esempio che apre la versione predefinita dell'applicazione *miscelatore* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Informazioni sulle [impostazioni dell'ambiente per le attività](batch-api-basics.md#environment-settings-for-tasks) di una [Panoramica delle funzionalità Batch](batch-api-basics.md) per ulteriori informazioni sulle impostazioni di elaborazione nodo ambiente.

## <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti di applicazioni del pool

Se è già stato configurato un pool esistente con un pacchetto dell'applicazione, è possibile specificare un nuovo pacchetto per il pool. Se si specifica un nuovo riferimento pacchetto per un pool, le condizioni seguenti:

* Tutti i nodi nuovi che partecipano del pool e qualsiasi nodo esistente che viene riavviato o ricostruire l'immagine verrà installato il pacchetto appena specificato.
* Calcolare nodi già presenti nel pool di quando si aggiornano i riferimenti del pacchetto non verrà installato automaticamente il nuovo pacchetto di applicazione. Questi calcolare nodi devono essere riavviati o ricostruire l'immagine per ricevere il nuovo pacchetto.
* Quando un nuovo pacchetto viene distribuito, le variabili di ambiente creato rispecchiare i nuovi riferimenti pacchetto dell'applicazione.

In questo esempio, il pool esistente ha versione 2.7 dell'applicazione *miscelatore* configurato in uno dei relativi [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Per aggiornare i nodi del pool con versione 2.76b, specificare una nuova [ApplicationPackageReference] [ net_pkgref] con la nuova versione e commit la modifica.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ora che è stata configurata la nuova versione, tutti i *nuovi* nodi che eseguono l'accesso del pool avrà versione 2.76b in uso. Per installare i nodi che sono *già* nel pool di 2.76b, riavviare o creare una nuova immagine loro. Si noti che i nodi riavviati manterranno i file da distribuzioni pacchetto precedente.

## <a name="list-the-applications-in-a-batch-account"></a>Elencare le applicazioni in un account di Batch

È possibile elencare le applicazioni e dei relativi pacchetti in un account di Batch utilizzando [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] metodo.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Concludere

Con i pacchetti di applicazione, è possibile evitare i clienti selezionare le applicazioni per il proprio lavoro e specificare la versione da utilizzare durante l'elaborazione di processi con il servizio abilitato Batch. È anche possibile fornire la possibilità per i clienti di caricare e tenere traccia delle applicazioni del servizio.

## <a name="next-steps"></a>Passaggi successivi

* [API REST Batch] [ api_rest] fornisce anche il supporto per l'uso con pacchetti di applicazioni. Ad esempio, vedere [applicationPackageReferences] [ rest_add_pool_with_packages] elemento nella finestra [Aggiungi un pool a un account] [ rest_add_pool] per informazioni su come specificare pacchetti da installare tramite l'API REST. Vedere [applicazioni] [ rest_applications] per informazioni dettagliate su come ottenere informazioni sull'applicazione tramite l'API REST Batch.

* Informazioni su come livello di programmazione [gestione di quote con Batch Management .NET e account Azure Batch](batch-management-dotnet.md). [Batch Management.NET] [ api_net_mgmt] raccolta possibile abilitare la funzionalità di creazione e l'eliminazione di account per l'applicazione Batch o il servizio.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramma di alto livello pacchetti di applicazioni"
[2]: ./media/batch-application-packages/app_pkg_02.png "Riquadro di applicazioni nel portale di Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blade applicazioni nel portale di Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blade i dettagli dell'applicazione nel portale di Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nuova pala di applicazione nel portale di Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aggiornare o eliminare i pacchetti di riepilogo a discesa nel portale di Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nuova pala pacchetto dell'applicazione nel portale di Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alcun avviso account lo spazio di archiviazione collegata"
[10]: ./media/batch-application-packages/app_pkg_10.png "Scegliere blade account lo spazio di archiviazione nel portale di Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Blade pacchetto di aggiornamento nel portale di Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Pacchetto Conferma eliminazione nel portale di Azure"
