<properties
   pageTitle="Utilizzare contatori delle prestazioni in diagnostica Windows Azure | Microsoft Azure"
   description="Utilizzare contatori in servizi cloud Azure o una macchina virtuale per trovare bottiglia e ottimizzare le prestazioni."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Creare e utilizzare contatori delle prestazioni in un'applicazione di Azure

In questo articolo vengono illustrati i vantaggi dei e su come inserire contatori nella propria applicazione Azure. Usarli per raccogliere dati, trovare bottiglia e ottimizzare le prestazioni di sistema e delle applicazioni.

Contatori disponibili per Windows Server, IIS e ASP.NET possono anche essere raccolte e utilizzati per determinare lo stato di ruoli web Azure, ruoli di lavoro e macchine virtuali. È anche possibile creare e utilizzare contatori personalizzati.  

È possibile esaminare i dati del contatore delle prestazioni
1. Direttamente nell'host applicazione con lo strumento Performance Monitor accesso utilizzando Desktop remoto
2. Con System Center Operations Manager mediante Azure Management Pack
3. Con altri strumenti di monitoraggio di accedere ai dati di diagnostici trasferiti allo spazio di archiviazione Azure. Per ulteriori informazioni, vedere [archiviare e visualizzare i dati di diagnostica in archiviazione Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Per ulteriori informazioni sul monitoraggio delle prestazioni dell'applicazione nel [portale classica Azure](http://manage.azure.com/), vedere [come monitorare i servizi Cloud](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Per ulteriori istruzioni dettagliate sulla creazione di una registrazione e strategia di analisi e l'utilizzo di diagnostica e altre tecniche per risolvere i problemi e ottimizzare le applicazioni di Azure, vedere [Risoluzione dei problemi di procedure consigliate per lo sviluppo di applicazioni Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Abilitare il monitoraggio delle prestazioni contatore

Prestazioni non sono abilitati per impostazione predefinita. Applicazione o un'attività di avvio necessario modificare la configurazione dell'agente di diagnostica predefinito per includere i contatori specifici che si desiderano verificare la presenza di ogni istanza del ruolo.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contatori disponibili per Microsoft Azure

Azure fornisce un sottoinsieme dei contatori disponibili per Windows Server, IIS e stack di ASP.NET. Nella tabella seguente sono elencati alcuni contatori particolarmente utili per le applicazioni Azure.

|Categoria del contatore: Oggetto (istanza)|Nome del contatore      |Guida di riferimento|
|---|---|---|
|Eccezioni CLR .NET (_globale_)|# Eccezioni generata / sec   |Per prestazioni eccezioni|
|Memoria CLR .NET (_globale_)    |% Tempo nel catalogo globale            |Contatori delle prestazioni della memoria|
|ASP.NET                      |Riavvio dell'applicazione    |Contatori delle prestazioni per ASP.NET|
|ASP.NET                      |Tempo di esecuzione richiesta  |Contatori delle prestazioni per ASP.NET|
|ASP.NET                      |Richieste disconnesse   |Contatori delle prestazioni per ASP.NET|
|ASP.NET                      |Riavvio del processo di lavoro |Contatori delle prestazioni per ASP.NET|
|Applicazioni ASP.NET (__totale__)|Richieste totali        |Contatori delle prestazioni per ASP.NET|
|Applicazioni ASP.NET (__totale__)|Richieste/Sec          |Contatori delle prestazioni per ASP.NET|
|V 4.0.30319 ASP.NET           |Tempo di esecuzione richiesta  |Contatori delle prestazioni per ASP.NET|
|V 4.0.30319 ASP.NET           |Tempo di attesa richiesta       |Contatori delle prestazioni per ASP.NET|
|V 4.0.30319 ASP.NET           |Richieste correnti        |Contatori delle prestazioni per ASP.NET|
|V 4.0.30319 ASP.NET           |Richieste in coda         |Contatori delle prestazioni per ASP.NET|
|V 4.0.30319 ASP.NET           |Richieste rifiutate       |Contatori delle prestazioni per ASP.NET|
|Memoria                       |MB disponibili        |Contatori delle prestazioni della memoria|
|Memoria                       |Byte approvate         |Contatori delle prestazioni della memoria|
|Processore (totale)            |% Tempo processore        |Contatori delle prestazioni per ASP.NET|
|TCPv4                        |Problemi di connessione     |Oggetto TCP|
|TCPv4                        |Connessioni |Oggetto TCP|
|TCPv4                        |Reimpostare le connessioni       |Oggetto TCP|
|TCPv4                        |Segmenti inviati/sec       |Oggetto TCP|
|Interface(*) di rete         |Ricevuto/sec      |Oggetto interfaccia di rete|
|Interface(*) di rete         |Byte inviati/sec          |Oggetto interfaccia di rete|
|Interfaccia di rete (Microsoft Virtual Machine Bus rete scheda 2)|Ricevuto/sec|Oggetto interfaccia di rete|
|Interfaccia di rete (Microsoft Virtual Machine Bus rete scheda 2)|Byte inviati/sec|Oggetto interfaccia di rete|
|Interfaccia di rete (Microsoft Virtual Machine Bus rete scheda 2)|Totale byte/sec|Oggetto interfaccia di rete|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Creare e aggiungere contatori personalizzati all'applicazione

Azure è il supporto per creare e modificare contatori personalizzati per ruoli web ruoli e di lavoro. I contatori utilizzabili per rilevare e monitorare comportamento specifico dell'applicazione. È possibile creare ed eliminare categorie contatori delle prestazioni personalizzato e specificatori di un'attività di avvio, un ruolo web o un ruolo di lavoro con autorizzazioni elevate.

>[AZURE.NOTE] Autorizzazioni per l'esecuzione deve elevate codice che apporta modifiche ai contatori delle prestazioni personalizzato. Se il codice in un ruolo web o un ruolo di lavoro, il ruolo deve includere il contrassegno <Runtime executionContext="elevated" /> nel file ServiceDefinition.csdef per il ruolo correttamente.

È possibile inviare dati del contatore delle prestazioni personalizzati allo spazio di archiviazione Azure tramite l'agente di diagnostica.

I dati del contatore prestazioni standard viene generati dai processi di Azure. Dati del contatore delle prestazioni personalizzato devono essere creati tramite l'applicazione web ruolo o lavoro ruolo. Per informazioni sui tipi di dati memorizzati in contatori personalizzati, vedere [Tipi di contatore delle prestazioni](https://msdn.microsoft.com/library/z573042h.aspx) . Per un esempio che crea e set di dati personalizzati in un ruolo web, vedere [Esempio contatori di prestazioni](http://code.msdn.microsoft.com/azure/) .

## <a name="store-and-view-performance-counter-data"></a>Archiviare e visualizzazione dati

Azure memorizza i dati di contatore prestazioni con altre informazioni di diagnostica. Questi dati sono disponibili per il monitoraggio remoto, mentre l'istanza di ruolo è in esecuzione tramite accesso remoto al desktop per visualizzare strumenti, ad esempio Performance Monitor. Per mantenere i dati all'esterno dell'istanza di ruolo, l'agente di diagnostica deve trasferire i dati allo spazio di archiviazione Azure. Il limite di dimensioni dei dati contatore prestazioni memorizzati nella cache può essere configurato in agente di diagnostica o può essere configurato per far parte di un limite condiviso per tutti i dati di diagnostici. Per ulteriori informazioni sull'impostazione della dimensione di buffer, vedere [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Per una panoramica della configurazione di agente di diagnostica per trasferire i dati in un account di archiviazione, vedere [archiviare e visualizzare i dati di diagnostica in archiviazione Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .

Ogni istanza del contatore delle prestazioni configurato è registrato un tasso di campioni specificato e i dati campionati vengono trasferiti all'account di archiviazione per una richiesta di trasferimento pianificata o una richiesta di trasferimento su richiesta. Trasferimento automatico può pianificato come spesso come una volta al minuto. Dati trasferiti dall'agente di diagnostica vengono archiviati in una tabella, WADPerformanceCountersTable, nell'account di archiviazione. In questa tabella può accedere ed eseguire una query con i metodi di archiviazione Azure standard API. Vedere [Esempio contatori di prestazioni di Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) per un esempio di eseguire query e visualizzare dati dalla tabella WADPerformanceCountersTable contatori delle prestazioni.

>[AZURE.NOTE] A seconda della frequenza di trasferimento agente di diagnostica e latenza coda, i dati del contatore prestazioni più recenti in account di archiviazione potrebbero essere aggiornato alcuni minuti.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Abilitare contatori utilizzando il file di configurazione di diagnostica

Utilizzare la procedura seguente per abilitare contatori nell'applicazione Azure.

## <a name="prerequisites"></a>Prerequisiti

In questa sezione presuppone che aver importato il monitor diagnostica nell'applicazione e il file di configurazione diagnostica aggiunto alla soluzione Visual Studio (diagnostics.wadcfg 2,4 SDK in seguito o diagnostics.wadcfgx in SDK 2.5 e versioni successive). Vedere i passaggi 1 e 2 [per consentire](./cloud-services-dotnet-diagnostics.md)di diagnostica in servizi Cloud Windows Azure e macchine virtuali) per altre informazioni.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Passaggio 1: Raccogliere e archiviare i dati dai contatori delle prestazioni

Dopo aver aggiunto il file di diagnostica per la soluzione Visual Studio è possibile configurare la raccolta e l'archiviazione dei dati in un'applicazione Azure. Aggiungendo contatori delle prestazioni per il file di diagnostica. Diagnostica, inclusi contatori, prima di tutto rilevazione sull'istanza. I dati vengono mantenuti alla tabella WADPerformanceCountersTable nel servizio di Azure tabella, quindi in modo che sarà anche necessario specificare l'account di archiviazione dell'applicazione. Se si sta verificando l'applicazione localmente nell'emulatore di calcolo, è anche possibile archiviare dati di diagnostica a localmente nell'emulatore di spazio di archiviazione. Prima archiviare i dati di diagnostica è necessario accedere al [portale classica Azure](http://manage.windowsazure.com/) e creare un account di archiviazione. Procedura consigliata è individuare l'account di archiviazione nella stessa posizione geografico come applicazione Azure per evitare il pagamento dei costi esterni della larghezza di banda e per ridurre la latenza.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Aggiungere contatori delle prestazioni per il file di diagnostica

Sono disponibili molti contatori che è possibile utilizzare. Nell'esempio seguente mostra diversi contatori consigliato per web e il monitoraggio ruolo di lavoro.

Aprire il file di diagnostica (diagnostics.wadcfg 2,4 SDK in seguito o diagnostics.wadcfgx in SDK 2.5 e versioni successive) e aggiungere l'elemento DiagnosticMonitorConfiguration quanto segue:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

L'attributo bufferQuotaInMB, che specifica la maggior quantità di archiviazione nel file system che è disponibile per il tipo di raccolta dati (registri Azure, i registri di IIS e così via). Il valore predefinito è uguale a 0. Quando viene raggiunta la quota, i dati dal meno recente vengono eliminati quando si aggiungono nuovi dati. La somma di tutte le proprietà di bufferQuotaInMB deve essere maggiore del valore dell'attributo OverallQuotaInMB. Per informazioni più dettagliate che consente di determinare quanto spazio di archiviazione sarà necessario per la raccolta di dati di diagnostica, vedere la sezione Configurazione tampone di [Risoluzione dei problemi di procedure consigliate per lo sviluppo di applicazioni Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

L'attributo scheduledTransferPeriod che specifica l'intervallo tra programmati trasferimenti di dati, arrotondato per eccesso al minuto più vicino. Negli esempi seguenti viene impostata su PT30M (30 minuti). L'impostazione del periodo di trasferimento su un valore basso, ad esempio 1 minuto, verrà negativamente sulle prestazioni dell'applicazione di produzione ma può essere utile per vedere diagnostica uso rapidamente durante la verifica. Il periodo previsto trasferimento dovrebbe essere rimpicciolisce abbastanza per assicurarsi che i dati di diagnostica non vengano sovrascritti sull'istanza, ma sufficientemente elevato che non ostacoli le prestazioni dell'applicazione.

L'attributo counterSpecifier specifica il contatore delle prestazioni per la raccolta. L'attributo sampleRate specifica la frequenza con cui il contatore delle prestazioni da esaminare, in questo caso 30 secondi.

Dopo aver aggiunto i contatori che si desidera raccogliere, salvare le modifiche al file di diagnostica. È necessario specificare l'account di archiviazione che verranno mantenuti per i dati di diagnostica.

### <a name="specify-the-storage-account"></a>Specificare l'account di archiviazione

Per mantenere le informazioni di diagnostica al proprio account di archiviazione Azure, è necessario specificare una stringa di connessione nel file di configurazione (ServiceConfiguration. cscfg) del servizio.

Per Azure SDK 2.5 è possibile specificare l'Account di archiviazione nel file diagnostics.wadcfgx.

>[AZURE.NOTE] Queste istruzioni vengono applicate solo a Azure SDK 2,4 e sotto. Per Azure SDK 2.5 è possibile specificare l'Account di archiviazione nel file diagnostics.wadcfgx.

Per impostare le stringhe di connessione:

1. Aprire il file ServiceConfiguration.Cloud.cscfg utilizzando un editor di testo e impostare la stringa di connessione per lo spazio di archiviazione. I valori *nome account* e *AccountKey* si trovano nel portale di classica Azure il dashboard di account di archiviazione in Gestione delle chiavi.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Salvare il file ServiceConfiguration.Cloud.cscfg.

3. Aprire il file ServiceConfiguration.Local.cscfg e verificare che UseDevelopmentStorage sia impostato su true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Una volta configurati le stringhe di connessione, quando l'applicazione viene distribuita l'applicazione viene mantenuto dati di diagnostica al proprio account di archiviazione.
4. Salvare e compilare il progetto, quindi distribuire l'applicazione.

## <a name="step-2-optional-create-custom-performance-counters"></a>Passaggio 2: (Facoltativo) creare contatori personalizzati

Oltre a contatori prestazioni predefiniti, è possibile aggiungere contatori delle prestazioni personalizzati per monitorare web o lavoro ruoli. Contatori personalizzati possono essere usati per tenere traccia e monitorare comportamento specifico dell'applicazione e possono essere creati o eliminati in un'attività di avvio, un ruolo web o un ruolo di lavoro con autorizzazioni elevate.

Agente di diagnostica Windows Azure consente di aggiornare la configurazione di contatore prestazioni dal file .wadcfg un minuto dopo l'avvio.  Se si creano contatori personalizzati nel metodo OnStart e richiedono più di un minuto per eseguire le attività di avvio, contatori delle prestazioni personalizzato verranno non sia stati creati quando l'agente di Azure diagnostica tenta di caricarli.  In questo scenario si noterà che diagnostica Azure acquisisce correttamente tutti i dati di diagnostica tranne i contatori personalizzati.  Per risolvere il problema, creare i contatori delle prestazioni in un'attività di avvio o spostare alcune delle attività avvio lavorare al metodo OnStart dopo la creazione di contatori delle prestazioni.

Per creare una semplice personalizzata contatore delle prestazioni "\MyCustomCounterCategory\MyButton1Counter", procedere come segue:

1. Aprire il file di definizione di servizio (CSDEF) per l'applicazione.
2. Aggiungere l'elemento di Runtime per la WebRole o l'elemento WorkerRole per consentire l'esecuzione con privilegi elevati:

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Salvare il file.
4. Aprire il file di diagnostica (diagnostics.wadcfg 2,4 SDK in seguito o diagnostics.wadcfgx in SDK 2.5 e versioni successive) e aggiungere quanto segue il DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Salvare il file.
6. Creare la categoria personalizzata prestazioni contatore nel metodo OnStart del proprio ruolo, prima di chiamare base. OnStart. Nell'esempio c# seguente viene creata una categoria personalizzata, se non esiste già:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Aggiornare i contatori all'interno dell'applicazione. Nell'esempio seguente viene aggiornato un contatore delle prestazioni personalizzato gli eventi Button1_Click:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Salvare il file.  

Dati del contatore delle prestazioni personalizzato verranno ora raccolti dal monitor diagnostica Windows Azure.

## <a name="step-3-query-performance-counter-data"></a>Passaggio 3: Query contatore di prestazioni

Dopo l'applicazione viene distribuita e l'esecuzione di monitor diagnostica viene avviata la raccolta dei contatori prestazioni e mantenere i dati allo spazio di archiviazione Azure. Utilizzare strumenti, ad esempio Esplora Server in Visual Studio, [Esplora archivi Azure](http://azurestorageexplorer.codeplex.com/)o [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) di Cerebrata per visualizzare i dati di contatori prestazioni descritti nella tabella WADPerformanceCountersTable. Anche a livello di programmazione, è possibile richiedere il servizio di tabella utilizzando [c#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Trascrizione](../storage/storage-ruby-how-to-use-table-storage.md)o [PHP](../storage/storage-php-how-to-use-table-storage.md).

Nell'esempio c# seguente viene illustrata una query semplice su tabella WADPerformanceCountersTable e consente di salvare i dati di diagnostica in un file CSV. Una volta contatori delle prestazioni vengono salvati in un file CSV è possibile utilizzare la funzionalità di grafica in Microsoft Excel o un altro strumento per visualizzare i dati. Assicurarsi di aggiungere un riferimento a Microsoft.WindowsAzure.Storage.dll, che è inclusa in Azure SDK per .NET ottobre 2012 e versioni successive. Installazione dell'assembly nella directory di programma Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ %.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Eseguire il mapping entità agli oggetti c# utilizzando una classe personalizzata derivata dalla **TableEntity**. Il codice seguente definisce una classe di entità che rappresenta un contatore delle prestazioni descritti nella tabella **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Passaggi successivi
[Altri articoli sulla diagnostica Azure] (.. / diagnostics.md azure)
