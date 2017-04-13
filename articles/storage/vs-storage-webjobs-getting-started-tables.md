<properties
    pageTitle="Guida introduttiva di Azure dello spazio di archiviazione e Visual Studio connessi servizi (WebJob progetti)"
    description="Come iniziare a utilizzare archivio tabelle Azure in un progetto di Azure WebJobs in Visual Studio dopo la connessione a un account di archiviazione utilizzando Visual Studio connessi servizi"
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

# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Guida introduttiva di Azure lo spazio di archiviazione (WebJob Azure progetti)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

Questo articolo vengono forniti esempi di codice c# che mostrano viene illustrato come utilizzare la versione di Azure WebJobs SDK 1. x con il servizio di archiviazione tabelle Azure. Gli esempi di codice utilizzano la versione di [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1. x.

Il servizio di archiviazione tabella Azure consente di memorizzare grandi quantità di dati strutturati. Il servizio è un archivio dati NoSQL che accetta chiamate autenticate da interne ed esterne cloud Azure. Tabelle Azure sono ideali per l'archiviazione dei dati strutturati e non relazionali.  Per ulteriori informazioni, vedere [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md#create-a-table) .

Alcuni frammenti di codice mostra l'attributo **tabella** utilizzata in funzioni che vengono chiamate manualmente, vale a dire non utilizzando uno degli attributi trigger.

## <a name="how-to-add-entities-to-a-table"></a>Come aggiungere entità a una tabella

Per aggiungere entità a una tabella, utilizzare l'attributo **tabella** con un **ICollector<T> ** o **IAsyncCollector<T> ** parametro in cui **T** specifica lo schema di entità che si desidera aggiungere. Costruttore dell'attributo accetta un parametro stringa che specifica il nome della tabella.

Nell'esempio seguente viene aggiunge entità **persona** in una tabella denominata *in ingresso*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

In genere tipo da utilizzare con **ICollector** deriva da **TableEntity** o implementa **ITableEntity**, ma non è necessario. Uno dei seguenti classi di **persona che** funzionano con il codice riportato in metodo **ingresso** precedente.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Se si desidera utilizzare direttamente l'API dello spazio di archiviazione di Azure, è possibile aggiungere un parametro **CloudStorageAccount** alla firma del metodo.

## <a name="real-time-monitoring"></a>Monitoraggio in tempo reale

Poiché le funzioni di ingresso dei dati spesso elaborano volumi elevati di dati, il dashboard WebJobs SDK fornisce i dati di monitoraggio in tempo reale. La sezione **Chiamata Log** indica se la funzione è ancora in esecuzione.

![Funzione di ingresso in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La pagina dei **Dettagli chiamata** avanzamento della funzione (numero di entità scritto) durante l'esecuzione e consente di interromperlo.

![Funzione di ingresso in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Al termine, la funzione la pagina dei **Dettagli chiamata** riporta il numero di righe scritte.

![Funzione di ingresso termine](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Come leggere più entità da una tabella

Per leggere una tabella, utilizzare l'attributo **tabella** con un **IQueryable<T> ** parametro in tipo **T** deriva da **TableEntity** o implementa **ITableEntity**.

Nell'esempio seguente legge e registra tutte le righe della tabella di **ingresso** :

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Come leggere una singola entità da una tabella

Esiste un costruttore attributo **tabella** con due parametri aggiuntivi che consentono di specificare la chiave di partizione e chiave riga quando si desidera associare un'entità singola tabella.

Nell'esempio seguente legge una riga di tabella per **una persona in base a partizione chiave e riga valori chiave ricevuti in un messaggio di coda** :  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


In questo esempio la classe **persona** non è necessario implementare **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Come usare l'API di .NET direttamente con una tabella

È anche possibile utilizzare l'attributo **tabella** con un oggetto **CloudTable** per una maggiore flessibilità nell'utilizzo di una tabella.

Nell'esempio seguente viene utilizzato un oggetto **CloudTable** per aggiungere una singola entità alla tabella *in ingresso* .

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Per ulteriori informazioni sull'utilizzo dell'oggetto **CloudTable** , vedere [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Argomenti correlati rientranti l'articolo sulle procedure relative code.

Per informazioni su come gestire l'elaborazione di tabella attivate da un messaggio di coda o per gli scenari WebJobs SDK non specifici di elaborazione della tabella, vedere [che guida introduttiva a Visual Studio e lo spazio di archiviazione di Azure coda servizi connessi (WebJob progetti)](vs-storage-webjobs-getting-started-queues.md).



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di tabelle Azure. Per ulteriori informazioni sull'utilizzo di Azure WebJobs e WebJobs SDK, vedere [WebJobs Azure documentazione risorse](http://go.microsoft.com/fwlink/?linkid=390226).
