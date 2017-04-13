<properties 
    pageTitle="Servizio Bus negoziate messaggistica esercitazione .NET | Microsoft Azure"
    description="Esercitazione .NET messaggistica gestito."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Servizio Bus negoziate messaggistica .NET esercitazione

Azure Service Bus fornisce due soluzioni di messaggistica complete – uno, a un servizio centralizzato "relay" in esecuzione nel cloud che supporta diversi diversi protocolli di trasporto e Web servizi standard, tra cui SOAP, WS-* e altre applicazioni. Il client non è necessario una connessione diretta al servizio locale né è necessario sapere dove si trova il servizio e le porte in ingresso non è necessario il servizio locale aperta sul firewall.

La seconda soluzione messaggistica consente funzionalità di messaggistica "gestita". Si possono essere considerati come asincrone o disaccoppiato la funzionalità di messaggistica supporto pubblicazione sottoscrizione separazione temporale e scenari di utilizzo dell'infrastruttura di messaggistica Bus di servizio di bilanciamento del carico. Comunicazione disaccoppiata offre molti vantaggi; ad esempio, client e server possono connettersi in base alle esigenze ed eseguire le operazioni in modo asincrono.

In questa esercitazione viene usata per ottenere una panoramica e familiarità con code, uno dei componenti di base del servizio Bus negoziate messaggistica. Dopo aver lavorato tramite la sequenza di argomenti in questa esercitazione, è un'applicazione che vengono inserite in un elenco dei messaggi, viene creata una coda e invia messaggi a tale coda. Infine, l'applicazione riceve e consente di visualizzare i messaggi dalla coda, quindi pulisce le risorse e uscite. Per un'esercitazione corrispondente che descrive come creare un'applicazione che utilizza l'inoltro di Bus del servizio, vedere [Bus di servizio inoltro dei messaggi esercitazione](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introduzione e i prerequisiti

Code offrono prima In recapito dei messaggi FIFO (First Out) a uno o più consumer concorrenti. FIFO significa che i messaggi in genere devono essere ricevuti ed elaborati per i destinatari nell'ordine temporale in cui si trattasse di accodamento e ogni messaggio verrà ricevuto ed elaborato da consumer solo un messaggio. Dei principali vantaggi dell'utilizzo di code è raggiungere *la separazione temporale* dei componenti dell'applicazione: in altre parole, produttori e consumatori non è necessario inviare e ricevere messaggi contemporaneamente, dal momento che i messaggi archiviati in modo permanente nella coda. Dei vantaggi correlato è *caricare livellamento*, che consente a produttori e consumatori di inviare e ricevere messaggi a tassi diversi.

Di seguito sono alcuni passaggi necessari e amministrativi che è necessario seguire prima di iniziare l'esercitazione. Il primo consiste nel creare uno spazio dei nomi di servizio e per ottenere una chiave di firma (SA) accesso condiviso. Uno spazio dei nomi fornisce un limite di applicazione per ogni applicazione esposto tramite Bus di servizio. Una chiave SA viene generata automaticamente dal sistema quando viene creato un spazio dei nomi di servizio. La combinazione dei nomi di servizio e chiave SA fornisce credenziali con cui Bus di servizio di autenticazione per l'accesso a un'applicazione.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Creare uno spazio dei nomi di servizio e ottenere una chiave SA

Il primo passaggio consiste per creare uno spazio dei nomi di servizio e per ottenere una chiave di [Firma di Access condiviso](service-bus-sas-overview.md) (SA). Uno spazio dei nomi fornisce un limite di applicazione per ogni applicazione esposto tramite Bus di servizio. Una chiave SA viene generata automaticamente dal sistema quando viene creato un spazio dei nomi di servizio. La combinazione dei nomi di servizio e chiave SA fornisce le credenziali per Bus di servizio per l'accesso a un'applicazione di autenticazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Il passaggio successivo consiste nel creare un progetto di Visual Studio e creare due funzioni di supporto di caricare un elenco delimitato da dei messaggi in un oggetto di .NET [elenco](https://msdn.microsoft.com/library/6sh2ey19.aspx) [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) fortemente tipizzata.

### <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Aprire Visual Studio come amministratore facendo clic su programma nel menu di avvio e facendo clic su **Esegui come amministratore**.

1. Creare un nuovo progetto applicazione console. Fare clic sul menu **File** e selezionare **Nuovo**, quindi fare clic su **progetto**. Nella finestra di dialogo **Nuovo progetto** , fare clic su **Visual c#** (se **c#** non viene visualizzato, cercare in **Altre lingue**), fare clic sul modello **Applicazione Console** e denominarla **QueueSample**. Utilizzare il **percorso**predefinito. Fare clic su **OK** per creare il progetto.

1. Utilizzare Gestione pacchetto NuGet per aggiungere al progetto raccolte Bus di servizio:
    1. In Esplora soluzioni fare clic sul progetto **QueueSample** e quindi fare clic su **Gestisci pacchetti NuGet**.
    2. Nella finestra di dialogo **Gestisci pacchetti Nuget** , fare clic sulla scheda **Sfoglia** e cercare **Bus di servizio Azure**, quindi fare clic su **Installa**.
<br />
1. In Esplora soluzioni fare doppio clic sul file Program.cs per aprirlo nell'editor di Visual Studio. Modificare il nome dello spazio dei nomi da nome predefinito di `QueueSample` a `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modificare il `using` istruzioni come nell'esempio seguente.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Creare un file di testo denominato Data.csv, quindi copiare il testo delimitato da virgola seguente.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Salvare e chiudere il file Data.csv e prendere nota del percorso in cui è stato salvato.

1. In Esplora soluzioni fare doppio clic sul nome del progetto (in questo esempio **QueueSample**), fare clic su **Aggiungi**, quindi fare clic su **Elemento esistente**.

1. Passare al file Data.csv creato nel passaggio 6. Fare clic sul file, quindi fare clic su **Aggiungi**. Assicurarsi che * *tutti i file (*.*) * * sia selezionata nell'elenco dei tipi di file.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Creare un metodo per l'analisi di un elenco dei messaggi

1. Nel `Program` classe prima di `Main()` metodo, dichiarare due variabili: uno dei tipi di **DataTable**per contenere l'elenco dei messaggi in Data.csv. L'altro deve essere di tipo elenco object e tipizzata a [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Quest'ultimo è l'elenco dei messaggi gestiti utilizzate passaggi successivi nell'esercitazione.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Esterno `Main()`, definire un `ParseCSV()` metodo che consente di analizzare l'elenco dei messaggi in Data.csv e carica i messaggi in una tabella [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) , come illustrato di seguito. Viene restituito un oggetto **DataTable** .

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. Nel `Main()` metodo, aggiungere un'istruzione che chiama il `ParseCSVFile()` metodo:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Creare un metodo che carica l'elenco dei messaggi

1. Esterno `Main()`, definire un `GenerateMessages()` metodo che accetta l'oggetto **DataTable** restituito da `ParseCSVFile()` e carica la tabella in un elenco dei messaggi gestiti fortemente tipizzata. Il metodo restituirà l'oggetto **elenco** , come illustrato nell'esempio seguente. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. In `Main()`, subito dopo la chiamata a `ParseCSVFile()`, aggiungere un'istruzione che chiama il `GenerateMessages()` metodo con il valore restituito da `ParseCSVFile()` come argomento:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Ottenere le credenziali utente

1. Prima di tutto, creare tre variabili stringa globale per mettere in attesa questi valori. Dichiarare le variabili subito dopo le dichiarazioni di variabili precedente; Per esempio:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Creare una funzione che accetta e memorizza il servizio dello spazio dei nomi e il tasto SA. Aggiungere questo metodo all'esterno `Main()`. Per esempio: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. In `Main()`, subito dopo la chiamata a `GenerateMessages()`, aggiungere un'istruzione che chiama il `CollectUserInput()` metodo:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Compilare la soluzione

Dal menu **Compila** in Visual Studio, fare clic su **Compila soluzione** o premere **Ctrl + MAIUSC + B** per confermare l'accuratezza del proprio lavoro finora.

## <a name="create-management-credentials"></a>Creare le credenziali di gestione

In questo passaggio, è possibile definire le operazioni di gestione che verrà utilizzato per creare le credenziali di firma (SA) con cui verrà autorizzato applicazione accesso condiviso.

1. Per chiarezza, questa esercitazione inserisce tutte le operazioni di coda di un metodo separato. Creare un asincrono `Queue()` metodo il `Program` per la classe, dopo il `Main()` metodo. Per esempio:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. Il passaggio successivo consiste nel creare una credenziale SA utilizzando un oggetto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . Il metodo di creazione accetta il nome della chiave SA e ottenere il valore nel `CollectUserInput()` metodo. Aggiungere il codice seguente per il `Queue()` metodo:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Creare un nuovo oggetto di gestione dello spazio dei nomi, con un URI contenente il nome dello spazio dei nomi e le credenziali di gestione ottenute nel passaggio precedente, come argomenti. Aggiungere il codice subito dopo il codice aggiunto nel passaggio precedente. Assicurarsi di sostituire `<yourNamespace>` con il nome dello spazio dei nomi di servizio:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Esempio

A questo punto, il codice avrà un aspetto simile al seguente:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Inviare messaggi a coda

In questo passaggio, creare una coda, quindi inviare i messaggi contenuti nell'elenco dei messaggi gestiti a tale coda.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Coda di creare e inviare messaggi a coda

1. Prima di tutto, creare la coda. Ad esempio, chiamare `myQueue`e dichiarare direttamente dopo le operazioni di gestione aggiunte nel `Queue()` metodo nell'ultimo passaggio:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. Nel `Queue()` metodo, creare un oggetto factory messaggistica con un URI Bus di servizio appena creato come argomento. Aggiungere il codice seguente subito dopo le operazioni di gestione che aggiunto nell'ultimo passaggio. Assicurarsi di sostituire `<yourNamespace>` con il nome dello spazio dei nomi di servizio:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Creare l'oggetto di coda utilizzando la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Aggiungere il codice seguente subito dopo il codice che aggiunto nell'ultimo passaggio:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Aggiungere codice che consente di scorrere l'elenco dei messaggi gestiti che è stato creato in precedenza, l'invio di ogni alla coda. Aggiungere il codice seguente subito dopo il `CreateQueueClient()` istruzione nel passaggio precedente:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Ricevere i messaggi dalla coda

In questo passaggio si ottiene l'elenco dei messaggi da coda creata nel passaggio precedente.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Creare un ricevitore e ricevere messaggi dalla coda

Nel `Queue()` metodo, scorrere la coda e ricevere messaggi utilizzando il metodo [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , la stampa di ogni messaggio alla console. Aggiungere il codice seguente subito dopo il codice che aggiunto nel passaggio precedente:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Si noti che la `Thread.Sleep` viene utilizzato esclusivamente per simulare il messaggio elaborazione ed è probabile che non sarebbe pronto in un'applicazione di messaggistica reale.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Terminare il metodo di coda e pulire le risorse

Subito dopo il codice precedente, aggiungere il codice seguente per pulire le risorse di factory e coda messaggio:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Chiamare il metodo di coda

L'ultimo passaggio consiste nell'aggiungere un'istruzione che chiama il `Queue()` dei metodi descritti `Main()`. Aggiungere la riga di codice evidenziata seguente alla fine di Main ():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Esempio

Il codice seguente contiene l'applicazione **QueueSample** completa.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Creare ed eseguire l'applicazione QueueSample

Ora che è stata completata la procedura precedente, è possibile creare ed eseguire l'applicazione **QueueSample** .

### <a name="build-the-queuesample-application"></a>Creare l'applicazione QueueSample

In Visual Studio, dal menu **Compila** fare clic su **Compila soluzione**oppure premere **Ctrl + MAIUSC + B**. Se si verificano errori, verificare che il codice è corretto in base all'esempio completata al termine del passaggio precedente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione viene illustrato come creare un client Bus di servizio applicazione di servizio e utilizzando le funzionalità di messaggistica Bus di servizio negoziate. Per un'esercitazione simile che usa il servizio Bus [inoltro](service-bus-messaging-overview.md#Relayed-messaging), vedere [Bus di servizio inoltro dei messaggi esercitazione](../service-bus-relay/service-bus-relay-tutorial.md).

Per ulteriori informazioni su [Bus di servizio](https://azure.microsoft.com/services/service-bus/), vedere gli argomenti seguenti.

- [Cenni preliminari sulla messaggistica Bus di servizio](service-bus-messaging-overview.md)
- [Nozioni fondamentali su Bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Architettura di Bus di servizio](service-bus-architecture.md)

