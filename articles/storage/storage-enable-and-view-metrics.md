<properties
    pageTitle="Attivazione metriche di archiviazione nel portale di Azure | Microsoft Azure"
    description="Come abilitare metriche di archiviazione per i servizi di Blob, code, tabella e File"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Attivazione metriche di archiviazione di Azure e la visualizzazione di dati di metrica

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Panoramica

Per impostazione predefinita, metriche di archiviazione non è abilitata per i servizi di archiviazione. È possibile attivare il monitoraggio tramite il [Portale di Azure](https://portal.azure.com) e Windows PowerShell o a livello di programmazione tramite la libreria di client lo spazio di archiviazione.

Quando si abilita metriche di archiviazione, è necessario scegliere un periodo di conservazione per i dati: questo periodo determina per quanto tempo lo spazio di archiviazione servizio mantiene le metriche e in base alle tariffe può per lo spazio necessario archiviarli. In genere, utilizzare un indirizzo più breve periodo di conservazione per le metriche minute più metriche orarie a causa di significativo aggiungere altro spazio necessario per metriche minute. È consigliabile scegliere un periodo di conservazione in modo da avere un tempo sufficiente per analizzare i dati e scaricare metriche che si desidera mantenere per l'analisi non in linea o la gestione di report. Tenere presente che verrà anche fatturato per il download dei dati di metrica dal proprio account di archiviazione.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Come abilitare metriche tramite il portale di Azure

Seguire questa procedura per attivare metriche nel [Portale di Azure](https://portal.azure.com):

1. Passare al proprio account di archiviazione.
1. Aprire e **l'Impostazioni** e selezionare **diagnostica**.
1. Assicurarsi che **lo stato** è impostato su **On**.
1. Selezionare le metriche per i servizi che si desidera eseguire il monitoraggio.
2. Specificare un criterio di conservazione per indicare il periodo di tempo per mantenere metriche e registrare i dati.

Si noti che il [Portale di Azure](https://portal.azure.com) non attualmente consentono di configurare metriche minute nell'account di archiviazione; è necessario abilitare le metriche minute tramite PowerShell o a livello di programmazione.

## <a name="how-to-enable-metrics-using-powershell"></a>Come abilitare metriche tramite PowerShell

Per configurare metriche di archiviazione nell'account di archiviazione utilizzando il cmdlet di Azure PowerShell Get-AzureStorageServiceMetricsProperty per recuperare le impostazioni correnti e il cmdlet Set-AzureStorageServiceMetricsProperty per modificare le impostazioni correnti, è possibile usare PowerShell sul computer locale.

I cmdlet che controllano metriche di archiviazione utilizzano i parametri seguenti:

- MetricsType: i valori possibili sono ore e minuti.

- ServiceType: i valori possibili sono Blob, coda e una tabella.

- MetricsLevel: i valori possibili sono Nessuna, il servizio e ServiceAndApi.

Ad esempio, il comando seguente passa su minute metriche per il servizio Blob nell'account di archiviazione predefinito con il periodo di memorizzazione impostato su cinque giorni:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

Il comando seguente recupera i corrente oraria metriche livello e la conservazione dei giorni per il servizio blob nell'account di archiviazione predefinito:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Per informazioni su come configurare i cmdlet di PowerShell Azure per funzionare con l'abbonamento Azure e come selezionare l'account di archiviazione predefinito da utilizzare, vedere: [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Come abilitare metriche di archiviazione a livello di programmazione

Il frammento c# riportato di seguito viene illustrato come attivare la registrazione per il servizio di Blob mediante la raccolta di client lo spazio di archiviazione per .NET e metriche:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days.
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);


## <a name="viewing-storage-metrics"></a>Visualizzazione metriche di archiviazione

Dopo aver configurato le metriche di archiviazione Analitica per monitorare l'account di archiviazione, lo spazio di archiviazione Analitica registra le metriche in un set di tabelle noto nel proprio account di archiviazione. È possibile configurare i grafici per visualizzare metriche orarie nel [Portale di Azure](https://portal.azure.com):

1. Passare al proprio account di archiviazione nel [Portale di Azure](https://portal.azure.com).
2. Nella sezione **monitoraggio** , fare clic su **Aggiungi i riquadri** per aggiungere un nuovo grafico. Nella **Sezione raccolta**, selezionare l'unità di misura metriche che si desidera visualizzare e trascinare la sezione **monitoraggio** .
3. Per modificare le metriche vengono visualizzate in un grafico, fare clic su **Modifica** collegamento. È possibile aggiungere o rimuovere singoli metriche selezionando o deselezionando loro.
4. Dopo aver completato le modifiche metriche, fare clic su **Salva** .

Se si desidera scaricare le metriche per lo spazio di archiviazione a lungo termine o analizzarli in locale, è necessario:

- Utilizzare uno strumento che è a conoscenza di tali tabelle e consentirà di visualizzare e il relativo download.
- Scrivere un'applicazione personalizzata o uno script per leggere e archiviare le tabelle.

Molti strumenti lo spazio di archiviazione per l'esplorazione di terze parti siano a conoscenza di tali tabelle ed è possibile visualizzarli direttamente.
Per un elenco di strumenti disponibili, vedere [Gli elenchi di cartelle di Azure lo spazio di archiviazione](storage-explorers.md) .

> [AZURE.NOTE] A partire dalla versione 0.8.0 di [Microsoft Azure archiviazione Esplora] (http://storageexplorer.com/), a questo punto sarà possibile visualizzare e scaricare le tabelle di metriche analitica.

Per accedere a livello di programmazione tabelle analitica, tenere presente che le tabelle analitica non vengono visualizzati se vengono elencati tutte le tabelle nell'account di archiviazione. È possibile accedervi direttamente per nome oppure utilizzare l' [API CloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) nella libreria client .NET per i nomi delle tabelle di query.

### <a name="hourly-metrics"></a>Metrica oraria
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Metrica minuta
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacità
- $MetricsCapacityBlob

È possibile trovare tutti i dettagli degli schemi per tali tabelle nel [Schema tabella metriche di archiviazione Analitica](https://msdn.microsoft.com/library/azure/hh343264.aspx). Le righe di esempio seguente mostrano solo un sottoinsieme di colonne disponibili, ma alcune caratteristiche più importanti del modo in cui che metriche di archiviazione consente di salvare queste metriche illustrano:

| PartitionKey  |       RowKey       |                    Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilità | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      utente. Tutti      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | utente. QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  utente. QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | utente. UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

Nei dati metriche minuto in questo esempio, il tasto partizione utilizza l'ora risoluzione minuto. Il tasto riga identifica il tipo di informazioni memorizzate nella riga e questo è composto da due elementi di informazioni, il tipo di accesso e il tipo di richiesta:

- Il tipo di accesso è utente o del sistema, dove utente fa riferimento a tutte le richieste di utente per il servizio di archiviazione e sistema fa riferimento a richieste per lo spazio di archiviazione Analitica.

- Il tipo di richiesta è tutto che non è una riga di riepilogo o identifica l'API specifico, ad esempio QueryEntity o UpdateEntity.


I dati di esempio sopra mostrano tutti i record per un solo minuto (a partire alle 11:00), in modo che il numero di richieste QueryEntities più il numero di QueryEntity richiede più il numero di UpdateEntity le richieste di aggiungere fino a sette, ossia il totale nella riga utente: tutti. Allo stesso modo, è possibile derivano latenza media di-to-end 104.4286 nella riga utente: All calcolando ((143.8 * 5) + 3 + 9) / 7.

È necessario prendere in considerazione la configurazione di avvisi nel [Portale di Azure](https://portal.azure.com) nella pagina Monitor in modo che metriche di archiviazione automatica può comunicare le modifiche più importanti del comportamento dei servizi di archiviazione. Se si usa uno strumento di explorer lo spazio di archiviazione per scaricare questi dati metriche in un formato con valori delimitati da, è possibile utilizzare Microsoft Excel per analizzare i dati. Vedere il blog di registrare [Gli elenchi di cartelle di Microsoft Azure lo spazio di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) per un elenco di strumenti di Esplora risorse di archiviazione disponibile.



## <a name="accessing-metrics-data-programmatically"></a>Accesso ai dati di metrica a livello di programmazione

L'elenco seguente indica c# codice di esempio per accedere a metriche minute per un intervallo di minuti e i risultati vengono visualizzati in una finestra console. Utilizza la raccolta di spazio di archiviazione di Azure versione 4 che include la classe CloudAnalyticsClient che semplifica l'accesso alle tabelle metriche di archiviazione.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
    select entity;

    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }

    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;

    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Quali addebiti verrà eseguita quando si abilita metriche di archiviazione?

Scrivere le richieste di creare entità della tabella per le metriche addebitate a tassi standard applicabili a tutte le operazioni di archiviazione Azure.

Le richieste di lettura ed eliminazione tramite un client ai dati di metrica sono anche fatturabili a tassi standard. Se è stato configurato un criterio di conservazione dei dati, addebitate non quando lo spazio di archiviazione di Azure Elimina dati metriche precedenti. Tuttavia, se si eliminano dati analitica, è possibile che l'account verrà addebitata le operazioni di eliminazione.

La capacità utilizzata dalle tabelle metriche anche è fatturabile: è possibile utilizzare le operazioni seguenti per stimare la quantità di capacità utilizzata per l'archiviazione dei dati di metrica:

- Se ogni ora un servizio di ogni API in ogni servizio, circa 148KB di dati vengono archiviati ogni ora nelle tabelle delle transazioni metriche se è stata attivata servizio e livello API riepilogo.

- Se ogni ora un servizio di ogni API in ogni servizio, circa 12KB di dati vengono archiviati ogni ora nelle tabelle delle transazioni metriche se è stata attivata semplicemente il livello di riepilogo.

- La tabella capacità per BLOB include due righe Totale aggiunte ogni giorno (purché utente ha scelto di registri): ciò significa che ogni giorno le dimensioni della tabella aumentano fino a circa 300 byte.

## <a name="next-steps"></a>Fasi successive:
[Abilitare lo spazio di archiviazione e l'accesso alle dati dei registri](https://msdn.microsoft.com/library/dn782840.aspx)
