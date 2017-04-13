<properties
    pageTitle="Nozioni fondamentali sul servizio di Azure Batch | Microsoft Azure"
    description="Informazioni sull'uso del servizio di Azure Batch parallelo su larga scala e HPC carichi di lavoro"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Nozioni fondamentali di Azure Batch

Batch Azure consente di eseguire le applicazioni di grandi dimensioni in parallelo e prestazioni elevate informatiche (HPC) in modo efficiente nel cloud. Si tratta di un servizio di piattaforma che consente di pianificare lavoro complesse per l'esecuzione in una raccolta gestita macchine virtuali e può automaticamente scala calcolare le risorse per soddisfare le esigenze dei processi.

Il servizio di Batch, è possibile definire risorse di elaborazione Azure per l'esecuzione delle applicazioni in parallelo e scala. È possibile eseguire su richiesta o programmata processi ed è non necessario creare, configurare e gestire un cluster HPC, singole macchine virtuali, reti virtuali o un processo complesso e infrastruttura di programmazione di attività manualmente.

## <a name="use-cases-for-batch"></a>Casi di utilizzo per Batch

Batch è un servizio di Azure gestito utilizzato per *l'elaborazione batch* o *elaborazione batch*in esecuzione un volume elevato di attività simili per ottenere un risultato desiderato. Elaborazione batch più comunemente usati per le organizzazioni che regolarmente elaborano, trasformano e analizzare i volumi elevati di dati.

Batch funziona bene con carichi di lavoro e applicazioni (noto anche come "imbarazzanti in parallelo") per sé parallele. Carichi di lavoro per sé parallele facilmente sono suddivisi come descritto più attività da svolgere un'attività contemporaneamente in più computer.

![Attività parallele][1]<br/>

Alcuni esempi di carichi di lavoro che vengono comunemente elaborati questa tecnica sono:

* Modellazione dei rischi finanziari
* Clima e idrologiche analisi dei dati
* Elaborazione, l'analisi e il rendering delle immagini
* Codifica multimediale e ricorrere
* Analisi di sequenza genetiche
* Analisi sottolineare progettazione
* Test del software

Batch può anche eseguire calcoli in parallelo con un passaggio di ridurre l'estremità ed eseguire più complesse carichi di lavoro di HPC, ad esempio applicazioni [Messaggio passando Interface (MPI)](batch-mpi.md) .

Per un confronto tra Batch e altre opzioni di soluzione HPC in Azure, vedere [soluzioni Batch e HPC](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Sviluppo con Batch

Elaborazione in parallelo carichi di lavoro con Batch in genere viene eseguita a livello di programmazione utilizzando uno dei [Batch API](#batch-development-apis). Con le API Batch, creare e gestire pool dei nodi di calcolo (macchine virtuali) e pianificare processi e attività per l'esecuzione dei nodi. Un'applicazione client o servizio che si crea utilizza API Batch per comunicare con il servizio di Batch.

È possibile in modo efficiente elaborare carichi di lavoro di grandi dimensioni per l'organizzazione o fornire un front end del servizio per i clienti in modo che possano eseguire processi e delle attività, su richiesta o in una pianificazione - su uno, centinaia o migliaia di nodi. È anche possibile usare Batch come parte di un flusso di lavoro più grande gestito con strumenti come [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Quando è pronti per eseguire un'analisi all'API Batch per comprendere in modo più completo di funzionalità che fornisce, vedere la [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Account Azure che sarà necessario

Quando si sviluppano soluzioni Batch, si utilizzeranno i seguenti account in Microsoft Azure.

- **Abbonamento e account azure** - se non si dispone già di un abbonamento a Azure, è possibile attivare i [sottoscrittori MSDN trarre vantaggio][msdn_benefits], o iscriversi a un [account Azure gratuito][free_account]. Quando si crea un account, viene creato un abbonamento predefinito.

- **Account batch** - quando le applicazioni di interagiscono con il servizio di Batch, il nome dell'account, l'URL dell'account e un tasto di scelta vengono utilizzati come credenziali. Tutte le risorse del Batch, ad esempio pool, nodi di calcolo, processi, e le attività associate a un account di Batch. È possibile [creare account Batch](batch-account-create-portal.md) nel portale di Azure.

- **Account di archiviazione** - Batch include il supporto per l'utilizzo dei file in [Archiviazione Azure][azure_storage]. Quasi ogni scenario Batch viene utilizzata l'archiviazione di Azure - per i programmi in esecuzione delle attività e i dati che l'elaborazione di gestione temporanea e per l'archiviazione dei dati di output che generano. Per creare un account di archiviazione, vedere [gli account di archiviazione su Azure](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>API di sviluppo batch

I servizi e applicazioni possono inviare chiamate dirette all'API REST, utilizzare uno o più delle seguenti librerie client o una combinazione di entrambi per gestire calcolare Esegui carichi di lavoro in parallelo scala mediante il servizio di Batch e risorse.

| API    | Riferimento all'API | Download | Esempi di codice |
| ----------------- | ------------- | -------- | ------------ |
| **Batch resto** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **Batch .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python batch**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Node batch** | [github.IO][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Linguaggio batch** (anteprima) | [github.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestione delle risorse batch

Oltre ai client API, è possibile usare anche le operazioni seguenti per gestire le risorse all'interno di account Batch.

- [Batch dei cmdlet di PowerShell][batch_ps]: il Batch di Azure cmdlet nel modulo di [Azure PowerShell](../powershell-install-configure.md) consentono di gestire le risorse Batch con PowerShell.

- [Azure CLI](../xplat-cli-install.md): interfaccia della riga di Azure di (Azure CLI) è un set di strumenti multipiattaforma che fornisce i comandi di shell per interagire con molti servizi Azure, tra cui Batch.

- Libreria client [.NET gestione batch](batch-management-dotnet.md) : disponibile anche tramite [NuGet][api_net_mgmt_nuget], è possibile utilizzare la libreria di client Batch Management .NET per gestire a livello di programmazione account Batch, le quote e pacchetti di applicazioni. Riferimento per la raccolta gestione su [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Strumenti batch

Sebbene non sia necessario per creare soluzioni con Batch, di seguito sono illustrati alcuni strumenti utili da utilizzare durante la compilazione e debug Batch applicazioni e servizi.

 - [Portale di Azure][portal]: È possibile creare, monitorare ed eliminare pool Batch, processi e attività in pale Batch del portale Azure. È possibile visualizzare le informazioni sullo stato per queste e altre risorse mentre si eseguono i processi e persino scaricare i file da nodi di elaborazione nei pool (scaricare un'operazione non riuscita `stderr.txt` durante la risoluzione dei problemi, ad esempio). È inoltre possibile scaricare i file RDP (Remote Desktop) che è possibile usare per accedere a nodi di calcolo.

 - [Azure Explorer Batch][batch_explorer]: Batch Explorer fornisce simili Batch funzionalità di gestione risorse come il portale di Azure, ma in un'applicazione client di Windows Presentation Foundation (WPF) autonoma. Una delle applicazioni di esempio .NET Batch disponibile in [GitHub][github_samples], è possibile compilarlo con Visual Studio 2015 o versione successiva e usarlo per esplorare e gestire le risorse nell'account Batch durante lo sviluppo e debug le soluzioni Batch. Processo di visualizzazione, pool e dettagli attività, scaricare i file da nodi di calcolo e connettersi a nodi in remoto utilizzando file RDP (Remote Desktop) è possibile scaricare con Esplora risorse Batch.

 - [Esplora archivi di Microsoft Azure][storage_explorer]: mentre non sia strettamente uno strumento di Azure Batch Esplora aree di spazio di archiviazione è un altro strumento efficace che siano mentre si sviluppa e il debug di soluzioni Batch.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenario: Scalabilità un carico di lavoro in parallelo

Una soluzione comuni che utilizzano le API Batch per interagire con il servizio Batch prevede scalabilità lavoro per sé parallelo, ad esempio il rendering delle immagini per scene 3D, ossia in un pool di nodi di calcolo. Questo pool dei nodi di calcolo può essere "farm di rendering" che sono disponibili decine, centinaia o migliaia di core per il processo di rendering, ad esempio.

Il diagramma seguente illustra un comune Batch flusso di lavoro, con un'applicazione client o servizio ospitato utilizzando Batch per eseguire un carico di lavoro in parallelo.

![Flusso di lavoro soluzione batch][2]

In questo scenario comune, l'applicazione o servizio elabora un carico di lavoro di calcolo nel Batch di Azure eseguendo la procedura seguente:

1. Caricare i **file di input** e l' **applicazione** che elaborerà questi file al proprio account di archiviazione Azure. I file di input possono essere tutti i dati che elaborerà l'applicazione, ad esempio modelli finanziari dati o i file video siano codifica intermedia. I file dell'applicazione possono essere qualsiasi applicazione che viene usato per l'elaborazione dei dati, ad esempio un'applicazione di rendering 3D o media trascodificatore.

2. Creare un Batch **pool** dei nodi di calcolo nell'account Batch - questi nodi sono le macchine virtuali che verranno eseguite le attività. Specificare le proprietà, ad esempio le [dimensioni del nodo](./../cloud-services/cloud-services-sizes-specs.md), il sistema operativo e il percorso in Azure archiviazione dell'applicazione per installare quando i nodi di partecipare a pool (l'applicazione è stata caricata nel passaggio 1 #). È inoltre possibile configurare in modo dinamico il pool di [adattamento automatico](batch-automatic-scaling.md), modificare il numero dei nodi di calcolo nel pool, ossia in risposta al carico di lavoro che generano le attività.

3. Creare un Batch **processo** per eseguire il carico di lavoro nel pool di nodi di calcolo. Quando si crea un processo, è associare a un pool di Batch.

4. Aggiungere **attività** al processo. Quando si aggiungono attività a un processo, il servizio Batch viene programmata automaticamente le attività per l'esecuzione sui nodi di calcolo nel pool. Ogni attività utilizza l'applicazione è stata caricata per elaborare i file di input.

    - 4. Prima dell'esecuzione di un'attività, è possibile scaricare i dati (file di input) corrispondente al processo per il nodo di calcolo a che viene assegnato. Se l'applicazione non è già installato sul nodo (vedere il passaggio #2), può essere scaricato qui invece. Dopo aver completato il download, l'attività viene eseguita sui nodi assegnati.

5. Come eseguire le attività, è possibile eseguire query Batch per monitorare l'avanzamento del processo e le relative attività. L'applicazione client o servizio comunica con il servizio Batch su HTTPS e in quanto potrebbero monitoraggio migliaia di attività in esecuzione su migliaia di nodi di calcolo, assicurarsi di [query in modo efficiente il servizio di Batch](batch-efficient-list-queries.md).

6. Come attività completata, è possibile caricare i dati del risultato allo spazio di archiviazione di Azure. È inoltre possibile recuperare file direttamente da nodi di calcolo.

7. Quando il monitoraggio rileva che l'attività il processo è stata completata, l'applicazione client o servizio è possibile scaricare i dati di output per ulteriori elaborazioni o valutazione.

Tenere in considerazione questo è solo un modo per utilizzare Batch e questo scenario descrive solo alcune delle funzionalità disponibili. Ad esempio, è possibile eseguire [più attività in parallelo](batch-parallel-node-tasks.md) su ogni nodo di calcolo e utilizzare [mansioni di preparazione e completamento](batch-job-prep-release.md) per preparare i nodi per i processi, quindi pulire successivamente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una panoramica del servizio di Batch, è possibile andare per scoprire come è possibile utilizzare per elaborare i carichi di lavoro in parallelo complesse.

- Leggere la [Panoramica delle funzionalità Batch per gli sviluppatori](batch-api-basics.md), informazioni importanti per chiunque preparazione all'uso Batch. L'articolo contiene informazioni più dettagliate sulle risorse servizio Batch come pool, nodi, processi e delle attività e le caratteristiche di API molti che è possibile usare durante la creazione dell'applicazione Batch.

- [Iniziare a utilizzare la libreria di Azure Batch per .NET](batch-dotnet-get-started.md) per imparare a utilizzare c# e la libreria di .NET Batch per eseguire un carico di lavoro semplice utilizzando un flusso di lavoro Batch comune. In questo articolo deve essere uno del primo si durante imparare a utilizzare il servizio di Batch. È inoltre disponibile una [versione Python](batch-python-tutorial.md) dell'esercitazione.

- Scaricare gli [esempi di codice su GitHub] [ github_samples] per vedere come c# e Python consente di interagire con Batch di pianificazione e processo carichi di lavoro di esempio.

- Estrarre il [Percorso di apprendimento Batch] [ learning_path] per avere un'idea delle risorse disponibili per l'utente man mano che si acquisiscono per l'uso con Batch.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
