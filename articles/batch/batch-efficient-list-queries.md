<properties
    pageTitle="Query elenco efficiente in Batch Azure | Microsoft Azure"
    description="Migliorare le prestazioni filtrando le query durante la richiesta di informazioni sulle risorse Batch come pool, processi, attività e nodi di calcolo."
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
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Il servizio di Azure Batch della query in modo efficiente

Qui si apprenderà come migliorare le prestazioni dell'applicazione Azure Batch riducendo la quantità di dati che viene restituiti dal servizio quando processi, attività, una query e nodi con [.NET Batch] [ api_net] raccolta.

Quasi tutte le applicazioni di Batch necessario eseguire alcuni tipi di monitoraggio o un altro operazione che spesso una query nel servizio di Batch a intervalli regolari. Per determinare se sono presenti le attività in coda rimanente in un processo, ad esempio, è necessario ottenere dati per ogni attività nel processo. Per determinare lo stato dei nodi nel pool di, è necessario ottenere dati su ogni nodo del pool. In questo articolo viene descritto come eseguire tali query in modo più efficiente.

## <a name="meet-the-detaillevel"></a>Soddisfano i DetailLevel

In un applicazione Batch di produzione, è possibile numerare entità come processi, attività e nodi di calcolo in migliaia. Quando si richiedono informazioni su queste risorse, una grande quantità di dati deve "cross rete" dal servizio di Batch all'applicazione ogni query. Per limitare il numero di elementi e tipo di informazioni che restituite da una query, è possibile aumentare la velocità delle query e pertanto le prestazioni dell'applicazione.

In questo [Batch.NET] [ api_net] frammento di codice API Elenca *tutte le* attività che è associata a un processo, insieme a *tutte* le proprietà di ogni attività:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

È possibile eseguire una query elenco molto più efficace, tuttavia, tramite l'applicazione di un livello di dettaglio"" alla query. A tale scopo, fornire un [ODATADetailLevel] [ odata] oggetto [JobOperations.ListTasks] [ net_list_tasks] metodo. Questo frammento di codice restituisce solo l'ID riga di comando proprietà ed elaborazione nodo informazioni delle attività completate:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

In questo scenario di esempio, se sono disponibili migliaia di attività del processo, i risultati della query secondo verranno in genere restituiti molto più rapido rispetto alla prima riga. Ulteriori informazioni sull'utilizzo ODATADetailLevel quando si Elenca gli elementi con l'API di .NET Batch sono incluso [sotto](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> È consigliabile che è *sempre* fornire un oggetto ODATADetailLevel alle API .NET elenco chiamate per garantire la massima efficienza e prestazioni dell'applicazione. Se si specifica un livello di dettaglio, è possibile evitare per ridurre i tempi di risposta servizio Batch, migliorare l'utilizzo della rete e ridurre al minimo l'utilizzo di memoria dalle applicazioni client.

## <a name="filter-select-and-expand"></a>Filtrare, selezionare ed espandere

[.NET Batch] [ api_net] e [Resto Batch] [ api_rest] API offrono la possibilità di ridurre sia il numero di elementi restituiti in un elenco, oltre che la quantità di informazioni viene restituite per ognuno. Non specificando ** **filtro**, quindi **espandere stringhe** **quando si esegue la query elenco.

### <a name="filter"></a>Filtro
La stringa di filtro è un'espressione che consente di ridurre il numero di elementi restituiti. Ad esempio elenca solo le attività in esecuzione per un processo o solo i nodi di calcolo che sono possibile eseguire le attività dell'elenco.

- La stringa di filtro è costituito da una o più espressioni, con un'espressione che include un nome di proprietà, l'operatore e valore. Le proprietà che è possono specificare sono specifiche per ogni tipo di entità che esegue una query, come gli operatori che sono supportati per ogni proprietà.
- Combinare più espressioni utilizzando gli operatori logici `and` e `or`.
- In questo esempio filtrare gli elenchi di stringa solo l'esecuzione di attività "rendering": `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selezionare
Selezionare stringa limita i valori di proprietà che vengono restituiti per ogni elemento. Specificare un elenco di nomi di proprietà e vengono restituiti solo i valori di proprietà per gli elementi nei risultati della query.

- Seleziona stringa è costituita da un elenco di nomi di proprietà separati da virgola. È possibile specificare le proprietà per il tipo di entità che si esegue la query.
- Selezionare stringa in questo esempio specifica che devono essere restituiti solo tre valori di proprietà per ogni attività: `id, state, stateTransitionTime`.

### <a name="expand"></a>Espandere
Stringa di espansione consente di ridurre il numero di chiamate API che sono richieste per ottenere informazioni specifiche. Quando si utilizza una stringa di espansione, è possono ottenere ulteriori informazioni su ogni elemento con un'unica chiamata API. Invece necessario ottenere l'elenco di entità, quindi la richiesta di informazioni per ogni elemento nell'elenco, utilizzare una stringa di espansione per ottenere le stesse informazioni in un'unica chiamata API. Meno chiamate API significa migliorare le prestazioni.

- Come selezionare stringa, la stringa di espansione controlla se alcuni dati sono incluso nei risultati della query di elenco.
- Stringa di espansione è supportata solo quando viene usato nell'elenco processi, pianificazioni, attività e pool. Attualmente, supporta solo le informazioni sulle statistiche.
- Quando tutte le proprietà e non viene specificato alcun selezionare stringa, Espandi stringa *deve* essere utilizzato per ottenere informazioni statistiche. Se una stringa di selezione viene utilizzata per ottenere un sottoinsieme di proprietà, quindi `stats` può essere specificato nella stringa di selezione e la stringa di espansione non è necessario specificare.
- In questo esempio espandere stringa specifica che le informazioni sulle statistiche devono essere restituite per ogni elemento nell'elenco: `stats`.

> [AZURE.NOTE] Durante la creazione di uno dei tipi di stringa tre query (filtrare, selezionare ed espandere), è necessario assicurarsi che i nomi di proprietà e maiuscole/minuscole corrisponda a quella di controparti elemento API REST. Ad esempio, quando si utilizza la classe .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , è necessario specificare **lo stato** invece di **stato**, anche se la proprietà .NET [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Vedere le tabelle sotto per i mapping di proprietà tra le .NET e le API REST.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regole per il filtro, selezionare ed espandere stringhe

- Nomi di proprietà nel filtro, selezionare ed espandere stringhe dovrebbero essere visualizzate come il [Resto Batch] [ api_rest] API, anche quando si utilizza [.NET Batch] [ api_net] o uno degli altri SDK Batch.
- Tutti i nomi di proprietà sono maiuscole e minuscole, ma i valori di proprietà sono maiuscole e minuscole.
- Data/ora stringhe possono essere uno dei due formati e deve essere precedute `DateTime`.

  - Esempio di formato DTF W3C:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Esempio di formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Booleane stringhe possono essere `true` o `false`.
- Se si specifica una proprietà non valida o un operatore, un `400 (Bad Request)` errore verrà generato.

## <a name="efficient-querying-in-batch-net"></a>Efficiente le query in Batch .NET

All'interno di [.NET Batch] [ api_net] API, [ODATADetailLevel] [ odata] classe viene utilizzata per fornire filtro, selezionare ed espandere stringhe alle operazioni di elenco. La classe ODataDetailLevel ha tre proprietà stringa pubblico che può essere specificata nel costruttore o impostare direttamente sull'oggetto. È quindi passare all'oggetto ODataDetailLevel come parametro le varie operazioni di elenco, ad esempio [ListPools][net_list_pools], [ListJobs][net_list_jobs]e [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limitare il numero di elementi restituiti.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Specificare quali valori di proprietà vengono restituiti con ogni elemento.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Recuperare i dati per tutti gli elementi in una singola API chiamare anziché chiamate distinte per ogni elemento.

Frammento di codice riportato utilizza l'API di .NET Batch per eseguire query in modo efficiente il servizio di Batch per le statistiche di un determinato set di pool. In questo scenario, l'utente Batch ha pool di prova e di produzione. Pool di test ID sono preceduti da "test" e del pool di produzione ID sono preceduti da "produzione". Nel frammento di codice, *myBatchClient* è un'istanza della classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inizializzata correttamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Un'istanza di [ODATADetailLevel] [ odata] configurati con selezionare ed Espandi clausole è anche possibile passare per a seconda delle necessità metodi Get, ad esempio [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)per limitare la quantità di dati restituiti.

## <a name="batch-rest-to-net-api-mappings"></a>Batch resto a mapping API .NET

I nomi delle proprietà nel filtro, selezionare ed espandere stringhe *deve* riflettere controparti API REST, in nome e maiuscole/minuscole. Nella tabella seguente sono disponibili i mapping tra controparti .NET e API REST.

### <a name="mappings-for-filter-strings"></a>Mapping stringhe dei filtri

- **Metodi di .NET elenco**: ogni metodo API .NET nella colonna accetta un [ODATADetailLevel] [ odata] oggetto come parametro.
- **Richieste di elenco REST**: pagina ogni API REST collegata a questa colonna contiene una tabella che specifica la proprietà e operazioni consentite in stringhe di *filtro* . Utilizzare i nomi di proprietà e operazioni quando si crea un [ODATADetailLevel.FilterClause] [ odata_filter] stringa.

| Metodi di elenco .NET | Richieste di elenco REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Elencare i certificati in un account][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Elencare i file associati a un'attività][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Stato del processo preparazione e mansioni di rilascio per un processo][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Elencare i processi presenti in un account][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Elencare i file su un nodo][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Elenco di attività associata a un processo][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Elencare le pianificazioni dei processi in un account][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Elencare i processi associati a una pianificazione di processo][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Elencare i nodi di calcolo di un pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Elencare i pool di un account][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Mapping per selezionare stringhe

- **Tipi di .NET batch**: tipi di API .NET Batch.
- **API REST entità**: ogni pagina in questo articolo contiene una o più tabelle in cui sono elencati i nomi delle proprietà API REST per il tipo. I nomi di proprietà vengono utilizzati quando si creano *Selezionare* stringhe. Utilizzare gli stessi nomi di proprietà quando si crea un [ODATADetailLevel.SelectClause] [ odata_select] stringa.

| Tipi di .NET batch | Entità API REST |
|---|---|
| [Certificato][net_cert] | [Ottenere informazioni su un certificato][rest_get_cert] |
| [CloudJob][net_job] | [Ottenere informazioni su un processo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Ottenere informazioni su una pianificazione di processo][rest_get_schedule] |
| [ComputeNode][net_node] | [Ottenere informazioni su un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Ottenere informazioni su un pool][rest_get_pool] |
| [CloudTask][net_task] | [Ottenere informazioni su un'attività][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Esempio: creare una stringa di filtro

Quando si crea una stringa di filtro per [ODATADetailLevel.FilterClause][odata_filter], consultare la tabella sopra in "I mapping di stringhe dei filtri" alla pagina documentazione trova API REST che corrisponde all'operazione elenco che si desidera eseguire. Sono disponibili gli operatori supportati e le proprietà della prima tabella multirow nella pagina. Se si desidera recuperare tutte le attività di cui codice di uscita è diverso da zero, ad esempio, la riga corrente [nell'elenco di attività associata a un processo] [ rest_list_tasks] specifica la stringa di proprietà applicabili e operatori consentiti:

| Proprietà | Operazioni consentite | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

In questo modo, la stringa di filtro per tutte le attività con un codice di uscita diverso da zero di elenco sarebbe:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Esempio: creare una stringa di selezione

Per creare [ODATADetailLevel.SelectClause][odata_select], consultare la tabella sopra in "I mapping delle stringhe selezionare" e passare alla pagina API REST corrispondente al tipo di entità in vendita. Sono disponibili le proprietà di selezionabile e loro operatori supportati nella prima tabella multirow nella pagina. Se si desidera recuperare solo l'ID e la riga di comando per ogni attività in un elenco, ad esempio, si possono trovare le righe nella tabella applicabile a [ottenere informazioni su un'attività][rest_get_task]:

| Proprietà | Tipo | Note |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

Selezionare stringa per l'inclusione solo l'ID e la riga di comando di ogni attività nell'elenco sarà:

`id, commandLine`

## <a name="code-samples"></a>Esempi di codice

### <a name="efficient-list-queries-code-sample"></a>Esempio di elenco efficiente query

Estrarre [EfficientListQueries] [ efficient_query_sample] progetto di esempio in GitHub per vedere come elenco esecuzione di query può influire sulle prestazioni in un'applicazione. Questa applicazione console c# Crea e aggiunge un numero elevato di attività a un processo. Quindi, rende più chiamate a [JobOperations.ListTasks] [ net_list_tasks] passando [ODATADetailLevel] [ odata] gli oggetti configurati con valori di proprietà diversi per modificare la quantità di dati da recuperare. Produce output simile al seguente:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Come mostrato nei tempi trascorsi, è possibile ridurre sensibilmente i tempi di risposta query limitando le proprietà e il numero di elementi restituiti. È possibile trovare questo e altri progetti di esempio negli [esempi di batch azure] [ github_samples] archivio in GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Esempio di raccolta e codice BatchMetrics

Oltre all'esempio di codice EfficientListQueries sopra, è possibile trovare [BatchMetrics] [ batch_metrics] progetto negli [esempi di batch azure] [ github_samples] GitHub repository. Il progetto di esempio BatchMetrics viene illustrato come in modo efficiente monitorare l'avanzamento del processo Batch Azure tramite l'API Batch.

[BatchMetrics] [ batch_metrics] esempio include un progetto di libreria di classi .NET che è possibile incorporare i propri progetti e un semplice programma della riga di comando per esercitare e viene illustrato l'utilizzo della raccolta.

L'applicazione di esempio all'interno del progetto vengono illustrate le operazioni seguenti:

1. Selezione degli attributi specifici in modo da scaricare solo le proprietà che necessarie
2. Applicazione del filtro a tempi di transizione di stato in modo da scaricare solo le modifiche successivamente all'ultima query

Ad esempio, il metodo seguente viene visualizzato nella raccolta BatchMetrics. Viene restituito un ODATADetailLevel che specifica che solo i `id` e `state` dovrebbero ottenere le proprietà per l'entità che vengono eseguite query. Specifica inoltre che solo entità il cui stato è cambiato dopo specificato `DateTime` parametro deve essere restituito.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="parallel-node-tasks"></a>Attività parallele nodo

[Ingrandire Azure Batch calcolare l'utilizzo delle risorse alle attività simultanee nodo](batch-parallel-node-tasks.md) è un altro articolo relative alle prestazioni di Batch dell'applicazione. Alcuni tipi di carichi di lavoro possono trarre vantaggio da eseguire attività in parallelo su più grande, ma meno - nodi di calcolo. Estrarre lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) in questo articolo per informazioni dettagliate su questo scenario.

### <a name="batch-forum"></a>Forum di batch

[Forum di Azure Batch] [ forum] su MSDN è ideale per discutere in Batch e porre domande relative al servizio. Testa sulla sopra per post "a" utili e rivolgere le domande mentre si verificano durante la creazione di soluzioni Batch.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
