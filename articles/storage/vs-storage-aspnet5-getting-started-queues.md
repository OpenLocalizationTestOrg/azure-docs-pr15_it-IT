<properties
    pageTitle="Guida introduttiva allo spazio di archiviazione di coda e Visual Studio connessi servizi (ASP.NET 5) | Microsoft Azure"
    description="Come iniziare a utilizzare lo spazio di archiviazione di Azure coda in un progetto ASP.NET 5 in Visual Studio"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>Guida introduttiva allo spazio di archiviazione di coda e Visual Studio connessi servizi (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

##<a name="overview"></a>Panoramica

In questo articolo viene descritto come iniziare a utilizzare lo spazio di archiviazione di Azure coda in Visual Studio dopo aver creato o si desidera fare riferimento a un account di archiviazione Azure in un progetto ASP.NET 5 utilizzando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** . L'operazione di **Aggiungere servizi connessi** installa i pacchetti NuGet appropriati per accedere all'archiviazione Azure del progetto e aggiunge la stringa di connessione per l'account di archiviazione per i file di configurazione del progetto.

Lo spazio di archiviazione di Azure coda è un servizio per l'archiviazione di un numero elevato di messaggi accessibili da qualsiasi punto del mondo tramite chiamate autenticate tramite HTTP o HTTPS. Un messaggio coda singola può contenere fino a 64 kilobyte (KB) le dimensioni e una coda può contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione.

Per iniziare, è innanzitutto necessario creare una coda di Azure nell'account di archiviazione. Si verrà spiegato come creare una coda nel codice. È inoltre verrà spiegato eseguire operazioni di base coda, ad esempio aggiunta, modifica, lettura e la rimozione di messaggi. Gli esempi sono scritti c\# codice e utilizzare la libreria di Client lo spazio di archiviazione di Azure per .NET. Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

**Nota:** Alcune delle API che eseguono chiamate allo spazio di archiviazione Azure ASP.NET 5 sono asincroni. Per ulteriori informazioni, vedere [programmazione asincrono con asincrono e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Il codice seguente presuppone vengono utilizzati i metodi di programmazione asincrono.

- Per ulteriori informazioni su come modificare a livello di programmazione code, vedere [Guida introduttiva di archiviazione di Azure coda mediante .NET](storage-dotnet-how-to-use-queues.md) .
- Per informazioni generali sull'archiviazione di Azure, vedere [la documentazione di spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/) .
- Per informazioni generali sui servizi cloud Azure, vedere [la documentazione di servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) .
- Per ulteriori informazioni sulla programmazione di applicazioni ASP.NET, vedere [ASP.NET](http://www.asp.net) .





##<a name="access-queues-in-code"></a>Code di Access nel codice

Per accedere alle code nei progetti ASP.NET 5, è necessario includere gli elementi seguenti a qualsiasi file di origine c# che accede lo spazio di archiviazione di Azure coda.

1. Assicurarsi che le dichiarazioni di spazio dei nomi nella parte superiore del file c# includono queste istruzioni **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. È possibile ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice riportato di seguito per ottenere il la stringa di connessione di spazio di archiviazione e informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. È possibile ottenere un oggetto **CloudQueueClient** riferimento a oggetti coda nell'account di archiviazione.  

        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. È possibile ottenere un oggetto **CloudQueue** per fare riferimento a una coda specifica.

        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota:** Utilizzare tutto il codice sopra davanti al codice negli esempi seguenti.

###<a name="create-a-queue-in-code"></a>Creare una coda nel codice

Per creare coda Azure nel codice, è sufficiente aggiungere una chiamata a **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

##<a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Per inserire un messaggio in una coda esistente, creare un nuovo oggetto **CloudQueueMessage** , quindi il metodo **AddMessageAsync** .

Un oggetto **CloudQueueMessage** può essere creato da una stringa (in formato UTF-8) o una matrice di byte.

Ecco un esempio che consente di inserire il messaggio "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

##<a name="read-a-message-in-a-queue"></a>Leggere un messaggio in una coda

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda utilizzando il metodo **PeekMessageAsync** .

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


##<a name="read-and-remove-a-message-in-a-queue"></a>Leggere e rimuovere un messaggio in una coda

Il codice è possibile rimuovere (rimuovere) un messaggio da una coda in due passaggi.
1. Chiamare **GetMessageAsync** per ottenere il messaggio successivo in una coda. Un messaggio restituito da **GetMessageAsync** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi.
2.  Per completare la rimozione del messaggio dalla coda, chiamare **DeleteMessageAsync**.

Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice seguente chiama **DeleteMessageAsync** destra dopo il messaggio è stato elaborato.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Sfruttare le opzioni aggiuntive per l'annullamento dell'Accodamento messaggi

Esistono due modi per personalizzare il recupero dei messaggi da una coda.
Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente. Nell'esempio seguente viene utilizzato il metodo **GetMessages** per ottenere 20 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo **foreach** . Inoltre, imposta il timeout invisibility 5 minuti per ogni messaggio. Si noti che tutti i messaggi contemporaneamente, la data di inizio 5 minuti passati dopo avere 5 minuti dopo la chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano visibili.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Ottenere la lunghezza della coda

È possibile ottenere una stima del numero di messaggi in una coda. Il metodo **FetchAttributes** richiede il servizio di coda di recuperare gli attributi di coda, tra cui il numero di messaggi. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato tramite il metodo **FetchAttributes** , senza chiamare il servizio di coda.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizzare il modello attesa asincrono con coda comune API

In questo esempio viene illustrato come utilizzare il modello attesa asincrono con coda comune API. L'esempio chiama la versione asincrono di ogni metodo specificato. Può essere visualizzato per la post-correzione di asincrono di ogni metodo. Quando si utilizza un metodo asincrono, il criterio di attesa asincrono sospende l'esecuzione locale fino al termine della chiamata. Questo comportamento consente di eseguire altre operazioni che è utile per evitare problemi di prestazioni e consente di migliorare le prestazioni complessive dell'applicazione il thread corrente. Per altre informazioni sull'utilizzo del modello asincrono Await in .NET, vedere [asincrono e Await (c# e Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **Delete** sull'oggetto coda.

    // Delete the queue.
    messageQueue.Delete();


##<a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
