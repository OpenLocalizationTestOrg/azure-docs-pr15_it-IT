<properties
    pageTitle="Guida introduttiva di archiviazione di Azure coda mediante .NET | Microsoft Azure"
    description="Code Azure offrono asincrone affidabili messaggistica tra componenti dell'applicazione. Il cloud della messaggistica consentono ai componenti dell'applicazione in modo indipendente le dimensioni."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Guida introduttiva di archiviazione di Azure coda mediante .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Lo spazio di archiviazione di Azure coda fornisce cloud messaggistica tra componenti dell'applicazione. Nella progettazione di applicazioni per scala, componenti dell'applicazione spesso sono disaccoppiati, in modo che sia possibile adattarli in modo indipendente. Spazio di archiviazione coda offre messaggistica asincrono delle comunicazioni tra componenti dell'applicazione, se sono in esecuzione nel cloud, sul desktop, su un server locale o in un dispositivo mobile. Archiviazione coda supporta anche la gestione attività asincrone e creazione di flussi di lavoro di processo.

### <a name="about-this-tutorial"></a>Su questa esercitazione

In questa esercitazione viene illustrato come scrivere codice .NET per alcuni scenari comuni di spazio di archiviazione Azure coda. Scenari includono la creazione e all'eliminazione di code e l'aggiunta, la lettura e l'eliminazione coda messaggi.

**Durata stimata per completare:** 45 minuti

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Raccolta di Client di archiviazione Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [account di archiviazione Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Aggiungere le dichiarazioni di nomi

Aggiungere quanto segue `using` le istruzioni nella parte superiore di `program.cs` file:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Creare il client di servizi di coda

La classe **CloudQueueClient** consente di recuperare code archiviate in archiviazione coda. Ecco un modo per creare il client di servizi:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

A questo punto si è pronti scrivere codice che legge i dati da e scrive i dati allo spazio di archiviazione coda.

## <a name="create-a-queue"></a>Creare una coda

In questo esempio viene illustrato come creare una coda se non esiste già:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

Per inserire un messaggio in una coda esistente, è necessario creare un nuovo **CloudQueueMessage**. Successivamente, chiamare il metodo **AddMessage** . Un **CloudQueueMessage** possono essere create da una stringa (in formato UTF-8) o una matrice di **byte** . Ecco codice che crea una coda (se non esiste) e viene inserito il messaggio "Hello, World":

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Leggere il messaggio successivo

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda utilizzando il metodo **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Modificare il contenuto di un messaggio in coda

È possibile modificare il contenuto di un messaggio sul posto nella coda. Se il messaggio rappresenta un'operazione di lavoro, è possibile utilizzare questa caratteristica per aggiornare lo stato dell'attività di lavoro. Il codice seguente aggiorna il messaggio coda con nuovo contenuto e imposta il timeout di visibilità per estendere un'altra 60 secondi. Salva lo stato del lavoro associato al messaggio e offre un'altra minuti per continuare a lavorare sul messaggio sul quale il client. È possibile utilizzare questa tecnica per tenere traccia di più passaggi flussi di lavoro nella coda messaggi senza dover ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware e software. In genere si preferisce mantenere anche un numero di tentativi e se il messaggio è ripetuto più volte *n* , l'eliminazione di esso. Ciò protegge da un messaggio che genera un errore di applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>Annullare l'accodamento il messaggio successivo

Il codice coda un messaggio da una coda in due passaggi. Quando si chiama **GetMessage**, viene visualizzato il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **DeleteMessage**. Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice chiama **DeleteMessage** destra dopo l'elaborazione di messaggio.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utilizzare motivo attesa asincrono con API di archiviazione coda comuni

In questo esempio viene illustrato come utilizzare il modello attesa asincrono con API di archiviazione coda comuni. L'esempio chiama la versione asincrona di ogni metodo specificato come indicato dal suffisso *asincrono* di ogni metodo. Quando si utilizza un metodo asincrono, l'asincrono-attesa motivo sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente di eseguire altre operazioni che è utile per evitare problemi di prestazioni e consente di migliorare le prestazioni complessive dell'applicazione il thread corrente. Per altre informazioni sull'utilizzo del modello asincrono Await in .NET vedere [asincrono e Await (c# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Sfruttare le opzioni aggiuntive per deselezionare Accodamento messaggi

Esistono due modi per personalizzare il recupero dei messaggi da una coda.
Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente. Nell'esempio seguente viene utilizzato il metodo **GetMessages** per ottenere 20 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo **foreach** . Imposta inoltre il timeout invisibility a cinque minuti per ogni messaggio. Si noti che l'avvio di 5 minuti per tutti i messaggi contemporaneamente, dopo avere 5 minuti trascorsi la chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati verrà visualizzata nuovamente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Ottenere la lunghezza della coda

È possibile ottenere una stima del numero di messaggi in una coda. Il metodo **FetchAttributes** richiede il servizio di coda di recuperare gli attributi di coda, tra cui il numero di messaggi. La proprietà **ApproximateMessageCount** restituisce l'ultimo valore recuperato dal metodo **FetchAttributes** senza la chiamata al servizio di coda.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **Delete** sull'oggetto coda.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione coda, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse.

- Visualizzare la documentazione di riferimento coda del servizio per informazioni dettagliate sulle API disponibili:
    - [Raccolta di Client lo spazio di archiviazione per riferimento .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Guida di riferimento all'API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Informazioni su come semplificare il codice che scritto per gestire lo spazio di archiviazione di Azure mediante [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Consente di visualizzare altre funzionalità Guide per informazioni sulle altre opzioni per l'archiviazione dei dati di Azure.
    - [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md) per archiviare i dati strutturati.
    - [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md) per archiviare i dati non strutturati.
    - [Connetti a Database SQL utilizzando .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) per memorizzare dati relazionali.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
