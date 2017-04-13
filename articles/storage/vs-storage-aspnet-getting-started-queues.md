<properties
    pageTitle="Guida introduttiva allo spazio di archiviazione di coda e Visual Studio connessi servizi (ASP.NET) | Microsoft Azure"
    description="Come iniziare a utilizzare lo spazio di archiviazione di Azure coda in un progetto ASP.NET in Visual Studio dopo la connessione a un account di archiviazione utilizzando Visual Studio connessi servizi"
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
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Guida introduttiva di Azure coda lo spazio di archiviazione e Visual Studio connessi servizi

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Questo articolo descrive come iniziare a utilizzare lo spazio di archiviazione di Azure coda in Visual Studio dopo aver creato o si desidera fare riferimento a un account di archiviazione Azure in un progetto ASP.NET utilizzando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** .

Si verrà spiegato come creare e accedere a una coda di Azure nel proprio account di archiviazione. È inoltre verrà spiegato eseguire operazioni di base coda, ad esempio aggiunta, modifica, la lettura e la rimozione di messaggi. Gli esempi vengono scritte nel codice c# e utilizzano la [Libreria Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

Lo spazio di archiviazione di Azure coda è un servizio per l'archiviazione di un numero elevato di messaggi accessibili da qualsiasi punto del mondo tramite chiamate autenticate tramite HTTP o HTTPS. Un messaggio di coda singola può contenere fino a 64 KB e una coda può contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione.

## <a name="access-queues-in-code"></a>Code di Access nel codice

Per accedere alle code nei progetti ASP.NET, è necessario includere gli elementi seguenti a qualsiasi file di origine c# cui lo spazio di archiviazione di coda di Azure ad accedere.

1. Assicurarsi che le dichiarazioni di spazio dei nomi nella parte superiore del file c# includono queste istruzioni **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. È possibile ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice riportato di seguito per ottenere il la stringa di connessione di spazio di archiviazione e informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. È possibile ottenere un oggetto **CloudQueueClient** riferimento a oggetti coda nell'account di archiviazione.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. È possibile ottenere un oggetto **CloudQueue** per fare riferimento a una coda specifica.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota** Utilizzare tutto il codice sopra davanti al codice negli esempi seguenti.

## <a name="create-a-queue-in-code"></a>Creare una coda nel codice

Per creare una coda di Azure nel codice, aggiungere una chiamata a **CreateIfNotExists** al codice precedente.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Per inserire un messaggio in una coda esistente, creare un nuovo oggetto **CloudQueueMessage** , quindi chiamare il metodo **AddMessage** .

Un oggetto **CloudQueueMessage** può essere creato da una stringa (in formato UTF-8) o una matrice di byte.

Ecco un esempio che consente di inserire il messaggio "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Leggere un messaggio in una coda

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda utilizzando il metodo PeekMessage().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Leggere e rimuovere un messaggio in una coda

Il codice è possibile rimuovere (annullare l'accodamento) un messaggio da una coda in due passaggi.
1. Chiamare GetMessage () per ottenere il messaggio successivo in una coda. Un messaggio restituito da GetMessage () diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi.
2.  Per completare la rimozione del messaggio dalla coda, chiamare **DeleteMessage**.

Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice seguente chiama **DeleteMessage** destra dopo il messaggio è stato elaborato.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Utilizzare altre opzioni per i messaggi deselezionare Accodamento

Esistono due modi per personalizzare il recupero dei messaggi da una coda.
Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente. Nell'esempio seguente viene utilizzato il metodo **GetMessages** per ottenere 20 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo **foreach** . Imposta inoltre il timeout invisibility a cinque minuti per ogni messaggio. Si noti che l'avvio di 5 minuti per tutti i messaggi contemporaneamente, dopo avere 5 minuti trascorsi la chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati verrà visualizzata nuovamente.

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

È possibile ottenere una stima del numero di messaggi in una coda. Il metodo **FetchAttributes** chiede queueservice per recuperare gli attributi di coda, tra cui il numero di messaggi. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato dal metodo **FetchAttributes** senza chiamare il queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Utilizzare motivo attesa asincrono con queueAPIs comuni

In questo esempio viene illustrato come utilizzare il modello attesa asincrono con queueAPIs comuni. L'esempio chiama la versione asincrono di ogni metodo specificato, può essere considerato per la post-correzione di asincrono di ogni metodo. Quando si utilizza un metodo asincrono l'asincrono-attesa motivo sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente di eseguire altre operazioni che è utile per evitare problemi di prestazioni e consente di migliorare le prestazioni complessive dell'applicazione il thread corrente. Per ulteriori informazioni sull'utilizzo della motivo asincrono Await in .NET vedere [asincrono e Await (c# e Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **Delete** sull'oggetto coda.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]