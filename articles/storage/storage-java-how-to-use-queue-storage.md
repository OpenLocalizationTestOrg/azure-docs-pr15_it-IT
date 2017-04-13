<properties
    pageTitle="Come utilizzare lo spazio di archiviazione coda da Java | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di coda di Azure per creare ed eliminare code e inserire, ottenere ed eliminare i messaggi. Esempi scritti in linguaggio."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Come utilizzare lo spazio di archiviazione coda da Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

Questa guida illustra come eseguire gli scenari comuni utilizzando il servizio di archiviazione di Azure coda. Gli esempi scritto in linguaggio e usare [Azure archiviazione SDK per Java][]. Gli scenari coperti includono i messaggi di coda di **inserimento**, **la visualizzazione**, **visualizzazione**ed **eliminazione** , nonché **creazione** ed **eliminazione di** code. Per ulteriori informazioni sulle code, vedere la sezione [passaggi successivi](#Next-Steps) .

Nota: Un SDK è disponibile per gli sviluppatori che utilizzano lo spazio di archiviazione di Azure nei dispositivi Android. Per ulteriori informazioni, vedere [Azure SDK lo spazio di archiviazione per Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java

In questa Guida, si utilizzeranno le funzionalità di archiviazione che possono essere eseguite all'interno di un'applicazione Java locale o nel codice in esecuzione all'interno di un ruolo web o un ruolo di lavoro in Azure.

A tale scopo, è necessario installare Java Development Kit (JDK) e creare un account di archiviazione Azure nell'abbonamento Azure. Dopo avere fatto, è necessario verificare che il sistema di sviluppo soddisfi i requisiti minimi e le dipendenze elencate nell'archivio di [Azure archiviazione SDK per Java][] su GitHub. Se il sistema soddisfi questi requisiti, è possibile seguire le istruzioni per scaricare e installare le librerie di spazio di archiviazione di Azure per Java nel sistema da tale archivio. Dopo aver completato le attività, sarà possibile creare un'applicazione Java che utilizza gli esempi riportati in questo articolo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurare l'applicazione per accedere all'archiviazione coda

Aggiungere le seguenti istruzioni di importazione nella parte superiore del file di linguaggio in cui si desidera utilizzare lo spazio di archiviazione di Azure API per accedere alle code:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configurazione di una stringa di connessione di archiviazione Azure

Un client di archiviazione Azure utilizza una stringa di connessione di spazio di archiviazione per archiviare i punti finali e le credenziali per accedere ai servizi di gestione dati. Durante l'esecuzione di un'applicazione client, è necessario specificare la stringa di connessione di spazio di archiviazione nel formato seguente, utilizzando il nome del proprio account di archiviazione e la chiave primaria accesso per l'account di archiviazione riportata nel [Portale di Azure](https://portal.azure.com) per i valori di *nome account* e *AccountKey* . In questo esempio viene illustrato come dichiarare un campo statico per contenere la stringa di connessione:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

In un'applicazione in esecuzione all'interno di un ruolo di Microsoft Azure, questa stringa può essere archiviata nel file di configurazione del servizio, *ServiceConfiguration. cscfg*ed è possibile accedervi con una chiamata al metodo **RoleEnvironment.getConfigurationSettings** . Ecco un esempio di recupero la stringa di connessione da un elemento di **impostazione** denominato *StorageConnectionString* nel file di configurazione del servizio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Negli esempi seguenti presuppongono che sia stata utilizzata uno di questi due metodi per ottenere la stringa di connessione di spazio di archiviazione.

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda

Un oggetto **CloudQueueClient** consente di ottenere gli oggetti di riferimento per le code. Il codice seguente viene creato un oggetto **CloudQueueClient** . (Nota: esistono altri modi per creare gli oggetti **CloudStorageAccount** ; per ulteriori informazioni, vedere **CloudStorageAccount** nel [Riferimento SDK Client lo spazio di archiviazione di Azure].)

Utilizzare l'oggetto **CloudQueueClient** per ottenere un riferimento alla coda che si desidera utilizzare. È possibile creare coda se non esiste.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Procedura: aggiungere un messaggio a una coda

Per inserire un messaggio in una coda esistente, è necessario creare un nuovo **CloudQueueMessage**. Successivamente, chiamare il metodo **addMessage** . Un **CloudQueueMessage** possono essere create da una stringa (in formato UTF-8) o una matrice di byte. Ecco codice che crea una coda (se non esiste) e viene inserito il messaggio "Hello, World".

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Procedura: leggere il messaggio successivo

È possibile anche visualizzare il messaggio che precede una coda senza rimuoverlo dalla coda chiamando **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: modificare il contenuto di un messaggio in coda

È possibile modificare il contenuto di un messaggio sul posto nella coda. Se il messaggio rappresenta un'operazione di lavoro, è possibile utilizzare questa caratteristica per aggiornare lo stato dell'attività di lavoro. Il codice seguente aggiorna il messaggio coda con nuovo contenuto e imposta il timeout di visibilità per estendere un'altra 60 secondi. Salva lo stato del lavoro associato al messaggio e offre un'altra minuti per continuare a lavorare sul messaggio sul quale il client. È possibile utilizzare questa tecnica per tenere traccia di più passaggi flussi di lavoro nella coda messaggi senza dover ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware e software. In genere si preferisce mantenere anche un numero di tentativi e se il messaggio è ripetuto più volte *n* , l'eliminazione di esso. Ciò protegge da un messaggio che genera un errore di applicazione ogni volta che viene elaborato.

Le ricerche di esempio di codice seguenti attraverso la coda di messaggi, individua il primo messaggio che corrisponde a "Hello, World" per il contenuto, quindi modifica il contenuto del messaggio ed esce.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

In alternativa, nell'esempio seguente viene aggiornato solo il primo messaggio visibile nella coda.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Procedura: ottenere la lunghezza della coda

È possibile ottenere una stima del numero di messaggi in una coda. Il metodo **downloadAttributes** richiede il servizio di coda per diversi valori correnti, incluso un conteggio del numero di messaggi in una coda. Il numero è approssimativo solo perché i messaggi possono essere aggiunti o rimossi dopo il servizio di coda risponde a una convocazione. Il metodo **getApproximateMessageCount** restituisce l'ultimo valore recuperato dalla chiamata a **downloadAttributes**, senza la chiamata al servizio di coda.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Procedura: rimuovere il messaggio successivo

Il codice rimuove un messaggio da una coda in due passaggi. Quando si chiama **retrieveMessage**, viene visualizzato il messaggio successivo in una coda. Un messaggio restituito da **retrieveMessage** diventa invisibile da qualsiasi altro codice leggere i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **deleteMessage**. Questo processo in due fasi di rimozione di un messaggio garantisce che se il codice ha esito negativo per l'elaborazione di un messaggio a causa di errori hardware e software, un'altra istanza di codice possa ottenere lo stesso messaggio e riprovare. Il codice chiama **deleteMessage** destra dopo l'elaborazione di messaggio.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Altre opzioni per i messaggi di annullamento dell'accodamento

Esistono due modi per personalizzare il recupero dei messaggi da una coda. Prima di tutto, è possibile ottenere un batch di messaggi (fino a 32). In secondo luogo, è possibile impostare un timeout invisibility o allungare, consentendo al codice più o meno tempo per l'elaborazione di ogni messaggio completamente.

Nell'esempio seguente viene utilizzato il metodo **retrieveMessages** per ottenere 20 messaggi in una chiamata. Quindi l'elaborazione di ogni messaggio utilizzando un ciclo **for** . Imposta inoltre il timeout invisibility a cinque minuti (300 secondi) per ogni messaggio. Si noti che viene avviato cinque minuti per tutti i messaggi contemporaneamente, pertanto quando cinque minuti superamento dopo la chiamata a **retrieveMessages**, tutti i messaggi che non sono stati eliminati verrà visualizzati nuovamente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Procedura: elencare le code

Per ottenere un elenco delle code correnti, chiamare il metodo **CloudQueueClient.listQueues()** , che verrà restituito un insieme di oggetti **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **deleteIfExists** sull'oggetto **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione coda, seguire questi collegamenti a informazioni sulle attività di archiviazione più complesse.

- [Spazio di archiviazione di Azure SDK per Java][]
- [Spazio di archiviazione Azure Client SDK riferimento][]
- [Servizi di archiviazione Azure API REST][]
- [Blog del Team di archiviazione Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Spazio di archiviazione di Azure SDK per Java]: https://github.com/azure/azure-storage-java
[Spazio di archiviazione di Azure SDK per Android]: https://github.com/azure/azure-storage-android
[Spazio di archiviazione Azure Client SDK riferimento]: http://dl.windowsazure.com/storage/javadoc/
[Servizi di archiviazione Azure API REST]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del Team di archiviazione Azure]: http://blogs.msdn.com/b/windowsazurestorage/
