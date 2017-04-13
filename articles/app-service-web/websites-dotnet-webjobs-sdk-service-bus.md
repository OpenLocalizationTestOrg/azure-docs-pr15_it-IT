<properties 
    pageTitle="Come utilizzare Bus di servizio Azure con SDK WebJobs" 
    description="Informazioni su come utilizzare code Bus di servizio Azure e argomenti con SDK WebJobs." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Come utilizzare Bus di servizio Azure con SDK WebJobs

## <a name="overview"></a>Panoramica

Questa guida include esempi di codice c# che mostrano come avviare un processo quando si riceve un messaggio di Azure servizio Bus. Gli esempi di codice utilizzano [SDK WebJobs](websites-dotnet-webjobs-sdk.md) versione 1. x.

Si presuppone che si sa [come creare un progetto WebJob in Visual Studio con stringhe di connessione che fanno riferimento al proprio account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md).

I frammenti di codice mostrano solo le funzioni, non il codice creato il `JobHost` oggetto come in questo esempio:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Un [esempio di codice servizio Bus completo](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) è repository webjobs di azure-esempi di sdk in GitHub.com.

## <a id="prerequisites"></a>Prerequisiti

Per lavorare con Bus di servizio è necessario installare il pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) oltre ad altri pacchetti WebJobs SDK. 

È anche necessario impostare la stringa di connessione AzureWebJobsServiceBus oltre le stringhe di connessione di spazio di archiviazione.  È possibile eseguire questa operazione nel `connectionStrings` sezione del file App, come illustrato nell'esempio seguente:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Per un progetto di esempio che include l'impostazione di stringa di connessione Bus di servizio nel file App, vedere [esempio Bus di servizio](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Sono anche possibile impostare le stringhe di connessione nell'ambiente di runtime Azure, quindi sostituisce le impostazioni di App quando viene eseguito il WebJob in Azure; Per ulteriori informazioni, vedere [Guida introduttiva a SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Procedura di attivazione di una funzione quando si riceve un messaggio di coda Bus di servizio

Per scrivere una funzione che SDK WebJobs chiama quando si riceve un messaggio di coda, utilizzare la `ServiceBusTrigger` attributo. Costruttore dell'attributo accetta un parametro che specifica il nome della coda al sondaggio.

### <a name="how-servicebustrigger-works"></a>Come funziona il ServiceBusTrigger

SDK riceve un messaggio in `PeekLock` modalità e le chiamate perse `Complete` messaggio se la funzione viene completata correttamente o chiamate `Abandon` in caso contrario. Se la funzione viene eseguita per più di `PeekLock` timeout, il blocco verrà automaticamente rinnovato.

Servizio Bus gestisce le proprie coda danneggiato che non può essere controllato o configurato da SDK WebJobs. 

### <a name="string-queue-message"></a>Messaggio di coda stringa

Nell'esempio seguente legge un messaggio di coda che contiene una stringa e scritta in dashboard WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Nota:** Se si siano creando coda messaggi in un'applicazione che non utilizza SDK WebJobs, assicurarsi che impostare [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) come "testo".

### <a name="poco-queue-message"></a>Messaggio di coda POCO

SDK deserializzerà automaticamente un messaggio di coda contenente JSON per un POCO [(normale oggetto CLR precedente](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) tipo. Nell'esempio seguente legge un messaggio di coda che contiene un `BlobInformation` oggetto che ha un `BlobName` proprietà:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Per esempi di codice in cui viene illustrato come utilizzare le proprietà del POCO per lavorare con BLOB e le tabelle nella stessa funzione, vedere la [versione di code lo spazio di archiviazione di questo articolo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Se il codice che crea messaggio della coda di non usa SDK WebJobs, utilizzare codice simile al seguente:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Tipi ServiceBusTrigger interagisce con

Oltre alle `string` e tipi di POCO, è possibile utilizzare il `ServiceBusTrigger` attributo con una matrice di byte o `BrokeredMessage` oggetto.

## <a id="create"></a>Come creare messaggi coda Bus di servizio

Per scrivere una funzione che crea un nuovo uso messaggio coda di `ServiceBus` dell'attributo e il nome della coda, passare al costruttore dell'attributo. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Creare un messaggio di sola coda in una funzione non asincrono

Nell'esempio seguente viene utilizzato un parametro di output per creare un nuovo messaggio nella coda denominata "outputqueue" con lo stesso contenuto del messaggio ricevuto nella coda denominata "inputqueue".

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

Il parametro di output per la creazione di un messaggio di coda singola può essere uno dei tipi seguenti:

* `string`
* `byte[]`
* `BrokeredMessage`
* Tipo POCO serializzabile definite dall'utente. Viene automaticamente serializzato come JSON.

Per i parametri di tipo POCO, coda viene creato un messaggio sempre quando la funzione termina; Se il parametro è null, SDK crea un messaggio di coda che restituirà null quando il messaggio ricevuto e deserializzato. Per gli altri tipi, se il parametro è null non viene creato alcun messaggio coda.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Creare più messaggi coda o nelle funzioni asincrone

Per creare più messaggi, utilizzare la `ServiceBus` dell'attributo con `ICollector<T>` o `IAsyncCollector<T>`, come illustrato nell'esempio seguente:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Ogni messaggio coda viene creata immediatamente quando il `Add` metodo.

## <a id="topics"></a>Come utilizzare gli argomenti Bus di servizio

Per scrivere una funzione che SDK chiama quando si riceve un messaggio a un argomento Bus di servizio, utilizzare la `ServiceBusTrigger` attributo con il costruttore che accetta nome dell'argomento e nome dell'abbonamento, come illustrato nell'esempio seguente:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Per creare un messaggio a un argomento, utilizzare la `ServiceBus` attributo con un nome argomento allo stesso modo si utilizza con un nome di coda.

## <a name="features-added-in-release-11"></a>Caratteristiche aggiunte nella versione 1.1

Nella versione 1.1 sono state aggiunte le funzionalità seguenti:

* Consentire la completa personalizzazione del messaggio elaborazione tramite `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`supporta la personalizzazione di Service Bus `MessagingFactory` e `NamespaceManager`.
* A `MessageProcessor` motivo strategia consente di specificare un processore per coda/argomento.
* Concorrenza elaborazione messaggio è supportata per impostazione predefinita. 
* Semplicità di personalizzazione `OnMessageOptions` tramite `ServiceBusConfiguration.MessageOptions`.
* Consenti [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) necessario specificarlo nella `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (per i diritti di Gestione scenari in cui non si dispone). 

## <a id="queues"></a>Argomenti correlati rientranti l'articolo sulle procedure relative code di spazio di archiviazione

Per informazioni sugli scenari WebJobs SDK non specifiche di Bus di servizio, vedere [come utilizzare l'archiviazione di Azure coda con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Gli argomenti trattati in questo articolo sono i seguenti:

* Funzioni asincrone
* Più istanze
* Spegnimento
* Utilizzare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice
* Impostare i valori per WebJobs SDK parametri di costruzione nel codice
* Impostare un trigger manualmente una funzione
* Scrivere i registri

## <a id="nextsteps"></a>Passaggi successivi

Questa guida è forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di Azure servizio Bus. Per ulteriori informazioni sull'utilizzo di Azure WebJobs e WebJobs SDK, vedere [Azure WebJobs consigliati risorse](http://go.microsoft.com/fwlink/?linkid=390226).
 
