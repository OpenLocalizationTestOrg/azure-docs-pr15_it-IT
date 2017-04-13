<properties
    pageTitle="Il flusso di dati di Azure diagnostica nel percorso critico con gli hub di evento | Microsoft Azure"
    description="Viene illustrato come configurare diagnostica Azure con gli hub evento to end, tra cui linee guida per gli scenari comuni."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>I dati di Azure diagnostica nel percorso critico di flusso con gli hub di evento

Diagnostica Windows Azure modi flessibile raccogliere metriche e registri dal cloud services macchine () e trasferire risultati allo spazio di archiviazione Azure. A partire da marzo 2016 (SDK 2.9) intervallo di tempo, è possibile elaborare diagnostica alle origini dati completamente personalizzato e trasferire i dati di percorso critico in secondi con [Gli hub evento Azure](https://azure.microsoft.com/services/event-hubs/).

Tipi di dati supportati includono:

- Evento individua eventi per Windows (ETW)
- Contatori delle prestazioni
- Registri eventi di Windows
- Registri delle applicazioni
- Azure registri dell'infrastruttura di diagnostica

In questo articolo viene illustrato come configurare la diagnostica Azure con gli hub evento to end. Viene fornita una Guida per gli scenari comuni seguenti:

- Come personalizzare i log e metriche ottenere sinked a un hub di evento
- Come modificare le configurazioni in ogni ambiente
- Come visualizzare i dati di flusso hub di evento
- Come risolvere i problemi di connessione  

## <a name="prerequisites"></a>Prerequisiti

Hub evento l'elaborazione di Azure diagnostica è supportata in servizi Cloud, macchine virtuali, macchina virtuale scala set e servizio tessuti a partire 2.9 di SDK Azure e il corrispondente di Azure Tools per Visual Studio.

- Estensione di diagnostica Azure 1.6 ([Azure SDK per .NET 2.9 o versioni successive](https://azure.microsoft.com/downloads/) è destinato a questa per impostazione predefinita)
- [Visual Studio 2013 o versione successiva](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configurazioni esistenti di Azure diagnostica in un'applicazione tramite un file *.wadcfgx* e uno dei metodi seguenti:
    - Visual Studio: [configurazione di diagnostica per i servizi Cloud Azure e macchine virtuali](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell: [attivare diagnostica nei servizi Cloud Azure tramite PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Spazio dei nomi hub evento completato il provisioning per l'articolo [Introduzione a hub di evento](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Connettersi Azure diagnostica sink hub di evento

Diagnostica Windows Azure sink sempre i registri e metriche, per impostazione predefinita, a un account di archiviazione Azure. Un'applicazione può inoltre elaborare a un evento hub mediante l'aggiunta di una nuova sezione **sink** all'elemento **WadCfg** nella sezione **PublicConfig** del file *.wadcfgx* . In Visual Studio, è archiviato il file *.wadcfgx* nel percorso seguente: **Progetto di servizio Cloud** > **ruoli** >  **(RoleName)** > **diagnostics.wadcfgx** file.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

In questo esempio l'URL di Hub evento è impostato su spazio dei nomi completo dell'Hub evento: spazio dei nomi evento hub + "/" + nome Hub evento.  

L'URL di Hub evento viene visualizzato nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkID=213885) nel dashboard di hub di evento.  

A condizione che lo stesso valore viene utilizzato in modo coerente in tutto il file di configurazione, è possibile impostare il nome **elaborare** a qualsiasi stringa valida.

> [AZURE.NOTE]  È possibile sink aggiuntive, ad esempio *applicationInsights* configurato in questa sezione. Diagnostica Windows Azure consente ai sink uno o più di definire se ogni sink viene anche dichiarato nella sezione **PrivateConfig** .  

Sink evento hub deve inoltre dichiarato e definito nella sezione **PrivateConfig** del file di configurazione *.wadcfgx* .

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

Il `SharedAccessKeyName` valore deve corrispondere a una chiave di firma di Access condiviso (SA) e un criterio definiti dello spazio dei nomi di **Evento hub** . Passare al dashboard di evento hub nel [portale di Azure](https://manage.windowsazure.com), fare clic sulla scheda **Configura** e impostare un criterio denominato (ad esempio, "SendRule") che dispone delle autorizzazioni *Invia* . **StorageAccount** viene anche dichiarato in **PrivateConfig**. Non è necessario modificare i valori di seguito se stanno lavorando. In questo esempio abbiamo lasciare i valori vuoto, che è un segno di una risorsa downstream è possibile impostare i valori. Del file di configurazione dell'ambiente *ServiceConfiguration.Cloud.cscfg* imposta ad esempio i nomi specifici ambiente e i tasti.  

> [AZURE.WARNING] Il tasto evento hub SA verrà archiviato in formato testo normale nel file *.wadcfgx* . Spesso, questo tasto viene archiviato nel controllo del codice sorgente o è disponibile come una risorsa nel server di generazione, pertanto è necessario proteggere in base alle esigenze. È consigliabile usare una chiave di sa con autorizzazioni *Invia solo* in modo che un utente non autorizzato possibile scrivere hub evento, ma non ascolto a tale o gestirlo.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurare i registri di diagnostica Azure e metriche per elaborare con gli hub di evento

Come descritto in precedenza, tutti i predefiniti e i dati di diagnostica personalizzato, vale a dire metriche e i log, è automaticamente sinked allo spazio di archiviazione di Azure agli intervalli specificati. Con gli hub di eventi e qualsiasi sink aggiuntive, è possibile specificare un nodo principale o foglia nella gerarchia per essere sinked con l'Hub di evento. Sono inclusi gli eventi ETW, contatori, i registri eventi di Windows e registri delle applicazioni.   

È importante tenere presente il numero di punti dati effettivamente deve essere trasferito a un hub di evento. In genere, gli sviluppatori di trasferiscono i dati di percorso hot bassa latenza che devono essere utilizzati ed interpretati rapidamente. Sistemi che controllano avvisi o autoscale regole sono disponibili esempi. Uno sviluppatore può inoltre configurare un archivio di analisi alternativo o cercare store, ad esempio Azure flusso Analitica, Elasticsearch, un sistema di monitoraggio personalizzato o un sistema di monitoraggio preferito da altri utenti.

Di seguito sono alcune configurazioni di esempio.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

Nell'esempio seguente, il sink verrà applicato a tale nodo **contatori di prestazioni** nella gerarchia, ovvero tutto figlio **contatori di prestazioni** verranno inviate all'evento hub.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

Nell'esempio precedente, il sink viene applicato a solo tre contatori: **Richieste accodate**, **Le richieste rifiutate**e **% tempo processore**.  

Nell'esempio seguente viene illustrato come uno sviluppatore può limitare la quantità di dati inviati da parametri fondamentali utilizzati per stato del servizio.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

In questo esempio, il sink viene applicato ai registri e viene filtrato solo per analisi a livello di errore.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Distribuire e aggiornare una configurazione dell'applicazione e diagnostica di servizi Cloud

Visual Studio fornisce il percorso più semplice per distribuire l'applicazione e configurazione di sink hub evento. Per visualizzare e modificare il file, aprire il file *.wadcfgx* in Visual Studio, modificarlo e salvarlo. Il percorso è **Un progetto di servizi Cloud** > **ruoli** > **(RoleName)** > **diagnostics.wadcfgx**.  

A questo punto tutto distribuzione e aggiornare le azioni in Visual Studio, Visual Studio Team System e tutti i comandi o script basato su MSBuild e l'utilizzo di **/t: pubblicare** destinazione includere *.wadcfgx* nel processo di salvataggio di un pacchetto. Inoltre, distribuzioni e aggiornamenti distribuire il file in Azure tramite l'estensione agente Azure diagnostica appropriata nelle macchine virtuali.

Dopo aver distribuito l'applicazione e configurazione di diagnostica di Azure, si vedrà immediatamente attività nel dashboard di Hub evento. Indica che si è pronti per passare alla visualizzazione dei dati percorso hot nello strumento di analisi o client di comunicare ascoltatore preferito.  

Nella figura seguente dashboard evento hub viene visualizzato l'invio di integrità dei dati di diagnostica all'evento Hub a partire dopo 11 PM. Ovvero quando l'applicazione è stata distribuita con un file aggiornato *.wadcfgx* e il sink è stato configurato correttamente.

![][0]  

> [AZURE.NOTE] Quando si apportano aggiornamenti ai file di configurazione di diagnostica Azure (.wadcfgx), si consiglia di inviare gli aggiornamenti di tutta l'applicazione, nonché la configurazione utilizzando la pubblicazione di Visual Studio o uno script di Windows PowerShell.  

## <a name="view-hot-path-data"></a>Visualizzare i dati di percorso caldo

Come descritto in precedenza, sono disponibili molti casi di utilizzo per ascoltare e l'elaborazione dei dati di evento hub.

Un semplice approccio consiste nel creare un'applicazione di console small prova per ascoltare l'Hub di eventi e il flusso di output di stampa. È possibile inserire il codice seguente, illustrato in modo più dettagliato in [Guida introduttiva a evento hub](./event-hubs-csharp-ephcs-getstarted.md)), in un'applicazione console.  

Si noti che l'applicazione console deve includere il [pacchetto evento processore Host Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Ricordarsi di sostituire i valori angolari nella funzione **Main** con i valori per le risorse.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Risolvere i problemi di sink hub di evento

- Hub evento non visualizzare l'attività di evento in ingresso o in uscita come previsto.

    Verificare che l'Hub di evento correttamente effettuato il provisioning. Tutte le informazioni di connessione nella sezione **PrivateConfig** della *.wadcfgx* devono corrispondere ai valori della risorsa visto nel portale. Verificare che si dispone di un criterio SA definito ("SendRule" nell'esempio) nel portale e che venga concessa l'autorizzazione *Invia* .  

- Dopo un aggiornamento, Hub evento non viene più visualizzato in ingresso o in uscita evento attività.

    Assicurarsi che le informazioni di configurazione e Hub di evento siano corretta come descritto in precedenza. A volte **PrivateConfig** viene ripristinata nell'aggiornamento di una distribuzione. Soluzione consigliata consiste nell'apportare tutte le modifiche alla *.wadcfgx* del progetto e quindi inserire un aggiornamento completo dell'applicazione. In caso contrario, verificare che l'aggiornamento di diagnostica inserisce un **PrivateConfig** completo che include la chiave SA.  

- I suggerimenti, dopo aver provato Hub evento continua a non funzionare.

    Provare a cercarla descritti nella tabella di archiviazione Azure che contiene i registri ed errori per diagnostica Azure stesso: **WADDiagnosticInfrastructureLogsTable**. Un'opzione consiste nell'usare uno strumento, ad esempio [Esplora archivi Azure](http://www.storageexplorer.com) per connettersi a questo account di archiviazione, visualizzare questa tabella e aggiungere una query per TimeStamp nelle ultime 24 ore. È possibile utilizzare lo strumento per esportare un file CSV e aprirlo in un'applicazione, ad esempio Microsoft Excel. Excel consente di cercare le stringhe di carta telefonica, ad esempio **EventHubs**, per vedere quali un errore.  

## <a name="next-steps"></a>Passaggi successivi

• [Altre informazioni sull'hub di evento](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Appendice: Completare Azure diagnostica file di configurazione (.wadcfgx) riportato

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Complementare *ServiceConfiguration.Cloud.cscfg* per questo esempio è simile alla seguente.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
