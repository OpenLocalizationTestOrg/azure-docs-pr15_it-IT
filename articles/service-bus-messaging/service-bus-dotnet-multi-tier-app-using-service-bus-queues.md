<properties
    pageTitle="Applicazione a più livelli .NET | Microsoft Azure"
    description="Esercitazione .NET che consente di sviluppare un'app di più livelli in Azure che utilizza code Bus di servizio per le comunicazioni tra livelli."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Applicazione di multilivello .NET utilizzando code Bus di servizio Azure

## <a name="introduction"></a>Introduzione

Lo sviluppo di Microsoft Azure è semplice con Visual Studio e SDK Azure gratuito per .NET. In questa esercitazione viene descritta la procedura per creare un'applicazione che utilizza più risorse Azure in esecuzione nell'ambiente locale. La procedura si suppone che nessun già esperienza nell'uso di Azure.

Si apprenderanno le operazioni seguenti:

-   Come attivare il computer per lo sviluppo di Azure con un singolo download e installazione.
-   Come utilizzare Visual Studio per sviluppare per Azure.
-   Informazioni su come creare un'applicazione a più livelli in Azure tramite i ruoli web e di lavoro.
-   Informazioni su come per le comunicazioni tra livelli utilizzando code Bus di servizio.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

In questa esercitazione si sarà compilare ed eseguire l'applicazione a più livelli in un servizio cloud Azure. Front-end sarà un ruolo web ASP.NET MVC e back-end sarà un ruolo di lavoro che utilizza una coda Bus di servizio. Come un progetto web che viene distribuito in un sito Web di Azure invece di un servizio cloud, è possibile creare la stessa applicazione a più livelli con front-end. Per ulteriori informazioni sulle operazioni da eseguire in modo leggermente diverso in un front-end Azure sito Web, vedere la sezione [passaggi successivi](#nextsteps) . È anche possibile provare il tutorial [dell'applicazione in locale/cloud ibrida .NET](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

Lo screenshot seguente mostra l'applicazione completata.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Panoramica dello scenario: comunicazioni tra dei ruoli

Per inviare un ordine di elaborazione, il componente UI front-end, in esecuzione in ruolo web deve interagire con la logica intermedio che esegue il ruolo di lavoro. Questo esempio viene usata Bus di servizio negoziate messaggistica per le comunicazioni tra i livelli.

Utilizzo della messaggistica gestito tra il livello centrale e web separa due componenti. A differenza di accesso diretto alla messaggistica (ovvero, TCP o HTTP), il livello di web non è connesso a livello intermedio direttamente. Se, tuttavia inserisce unità di lavoro, come messaggi, in Bus Service affidabile conservati fino a livello centrale è pronto per l'utilizzo e la loro elaborazione.

Servizio Bus fornisce due entità per supportare la messaggistica gestito: code e argomenti. Con le code, ogni messaggio inviato alla coda è utilizzata da un singolo destinatario. Gli argomenti supportano il modello di pubblicazione/sottoscrizione in cui ogni messaggio pubblicato viene resa disponibile per una sottoscrizione registrata con l'argomento. Ciascuna sottoscrizione mantiene in modo logico coda dei messaggi. Sottoscrizioni possono essere configurate anche con regole di filtro che limitano l'insieme di messaggi passati alla coda di abbonamento a quelli che corrispondono al filtro. Nell'esempio seguente usa code Bus di servizio.

![][1]

Questo meccanismo di comunicazione offre diversi vantaggi rispetto messaggistica diretto:

-   **Separazione temporale.** Con il criterio di messaggistica asincrono, produttori e consumatori non è necessario online nello stesso momento. Servizio Bus archivia i messaggi in modo affidabile fino a quando non la parte che è pronta per riceverle. In questo modo i componenti dell'applicazione distribuita la disconnessione, sia volontariamente, ad esempio, per la manutenzione o a causa di un arresto anomalo del componente, senza influire sull'intero sistema. Inoltre, l'applicazione che potrebbe essere solo in linea a determinati orari del giorno.

-   **Caricare il livellamento.** In molte applicazioni, caricamento del sistema varia nel tempo, mentre il tempo necessario per ogni unità di lavoro è in genere costante. Produttori di messaggio intermediating e clienti con una coda significa che l'applicazione (lavoro) che deve solo effettuare il provisioning adattato carico medio piuttosto che punta. Antero-posteriore della coda aumenta e contratti del carico in arrivo. In questo modo direttamente denaro per quanto riguarda l'importo dell'infrastruttura necessaria per soddisfare il carico dell'applicazione.

-   **Bilanciamento del carico.** Come caricare aumenta, più processi di lavoro possono essere aggiunti da leggere dalla coda. Ogni messaggio viene elaborato solo da uno dei processi di lavoro. Questo il pull bilanciamento del carico consente inoltre un uso ottimale macchine lavoro anche se il computer di lavoro differiscono in termini di potenza di elaborazione, come estraggono messaggi sua velocità massima. Questo modello è spesso detto motivo *concorrenti consumer* .

    ![][2]

Nelle sezioni che seguono il codice di implementazione questa architettura.

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Prima di sviluppo di applicazioni Azure, tutti gli strumenti e configurare l'ambiente di sviluppo.

1.  Installare SDK Azure per .NET [ottenere SDK e gli strumenti][].

2.  Fare clic su **Installa SDK** per la versione di Visual Studio in uso. La procedura descritta in questa esercitazione utilizza Visual Studio 2015.

4.  Quando viene richiesto di eseguire o salvare il programma di installazione, fare clic su **Esegui**.

5.  **Installazione guidata piattaforma Web**, fare clic su **installazione** e procedere con l'installazione.

6.  Al termine dell'installazione, sarà necessario tutto il necessario per iniziare a sviluppare l'app. il SDK include strumenti che consentono di semplificare lo sviluppo di applicazioni Azure in Visual Studio. Se non è installato Visual Studio, SDK installa anche la gratuito Visual Studio Express.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi

Il passaggio successivo consiste nel creare uno spazio dei nomi di servizio e ottenere una chiave di firma di Access condiviso (SA). Uno spazio dei nomi fornisce un limite di applicazione per ogni applicazione esposto tramite Bus di servizio. Una chiave SA viene generata dal sistema quando viene creato uno spazio dei nomi. La combinazione dei nomi e chiave SA fornisce le credenziali per Bus di servizio per l'accesso a un'applicazione di autenticazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Creare un ruolo web

In questa sezione, si creano front-end dell'applicazione. Creare innanzitutto le pagine che consente di visualizzare l'applicazione.
Successivamente, aggiungere il codice che invia gli elementi a una coda Bus di servizio e le informazioni di stato coda.

### <a name="create-the-project"></a>Creare il progetto

1.  Uso dei privilegi di amministratore, avviare Microsoft Visual Studio. Per avviare Visual Studio con privilegi di amministratore, pulsante destro del mouse sull'icona dell'applicazione di **Visual Studio** e quindi fare clic su **Esegui come amministratore**. Emulatore di calcolo Azure, descritto più avanti in questo articolo, è necessario avviare Visual Studio con privilegi di amministratore.

    In Visual Studio, nel menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.

2.  Dai **Modelli installati**in **c#**, fare clic su **Cloud** e quindi fare clic su **Servizio Cloud di Azure**. Nome del progetto **MultiTierApp**. Fare clic su **OK**.

    ![][9]

3.  Ruoli di **.NET Framework 4.5** doppio clic sul **Ruolo Web ASP.NET**.

    ![][10]

4.  Passare il mouse su **WebRole1** in **soluzione di servizio Cloud di Azure**, fare clic sull'icona matita e assegnare il ruolo web **FrontendWebRole**. Fare clic su **OK**. (Assicurarsi di inserire "Front-end" con un minuscoli 'e,"non"front-end").

    ![][11]

5.  Dall'elenco **Selezionare un modello** della finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **MVC**.

    ![][12]

6. Sempre nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic sul pulsante **Cambia autenticazione** . Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'app non è necessario un account di accesso utente.

    ![][16]

7. Nella casella della finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **OK** per creare il progetto.

6.  In **Esplora soluzioni**nel progetto **FrontendWebRole** rapida **riferimenti**e quindi fare clic su **Gestisci pacchetti NuGet**.

7.  Fare clic sulla scheda **Sfoglia** , quindi cercare `Microsoft Azure Service Bus`. Fare clic su **installazione**e accettare le condizioni di utilizzo.

    ![][13]

    Si noti che ora si desidera fare riferimento assembly client richiesto e alcuni nuovi file di codice sono stati aggiunti.

9.  In **Esplora soluzioni**rapida **modelli** e fare clic su **Aggiungi**, quindi fare clic su **classe**. Nella casella **nome** digitare il nome **OnlineOrder.cs**. Fare clic su **Aggiungi**.

### <a name="write-the-code-for-your-web-role"></a>Scrivere il codice per il proprio ruolo web

In questa sezione è creare le diverse pagine visualizzato dall'applicazione.

1.  Nel file OnlineOrder.cs in Visual Studio, sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  In **Esplora soluzioni**fare doppio clic su **Controllers \ HomeController.cs**. Aggiungere le seguenti istruzioni **using** nella parte superiore del file da includere gli spazi dei nomi per il modello che appena creato, oltre al tipo Bus di servizio.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Anche nel file HomeController.cs in Visual Studio, sostituire la definizione dello spazio dei nomi esistente con il codice riportato di seguito. Questo codice contiene metodi per la gestione di inoltro di elementi alla coda.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  Nel menu **Compila** , fare clic su **Compila soluzione** per verificare l'accuratezza del proprio lavoro finora.

5.  A questo punto, creare la visualizzazione per il `Submit()` metodo creata in precedenza. Pulsante destro del mouse all'interno di `Submit()` metodo (overload di `Submit()` che non accetta parametri) e quindi scegliere **Aggiungi visualizzazione**.

    ![][14]

6.  Viene visualizzata una finestra di dialogo per la creazione della visualizzazione. Nell'elenco **modello** scegliere **Crea**. Nell'elenco **classe modello** , fare clic sulla classe **OnlineOrder** .

    ![][15]

7.  Fare clic su **Aggiungi**.

8.  A questo punto, modificare il nome visualizzato dell'applicazione. In **Esplora soluzioni**fare doppio clic sul **Views\Shared\\cshtml** file da aprire nell'editor di Visual Studio.

9.  Sostituire tutte le occorrenze di **Applicazione ASP.NET personale** con **i prodotti di LITWARE**.

10. Rimuovere i collegamenti **Home**, **sulle**e **contatto** . Eliminare il codice evidenziato:

    ![][28]

11. Infine, modificare la pagina di invio per includere alcune informazioni sulla coda. In **Esplora soluzioni**fare doppio clic sul file **Views\Home\Submit.cshtml** per aprirlo nell'editor di Visual Studio. Aggiungere la riga seguente dopo `<h2>Submit</h2>`. Per il momento di `ViewBag.MessageCount` è vuoto. Si verrà compilarlo in un secondo momento.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. A questo punto è stata implementata l'interfaccia utente. È possibile premere **F5** per eseguire l'applicazione e verificare che l'aspetto come previsto.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Scrivere il codice per l'invio di elementi a una coda Bus di servizio

Aggiungere quindi il codice per l'invio di elementi a una coda. Creare innanzitutto una classe che contiene le informazioni di connessione coda Bus di servizio. Quindi, inizializzare la connessione da Global.aspx.cs. Infine, aggiornare il codice di presentazione creata in precedenza in HomeController.cs effettivamente inoltrare elementi a una coda Bus di servizio.

1.  In **Esplora soluzioni**fare clic **FrontendWebRole** (rapida del progetto, non il ruolo). Fare clic su **Aggiungi**e quindi fare clic su **classe**.

2.  Nome della classe **QueueConnector.cs**. Fare clic su **Aggiungi** per creare la classe.

3.  A questo punto, aggiungere il codice che include le informazioni di connessione e inizializza la connessione a una coda Bus di servizio. Sostituire l'intero contenuto QueueConnector.cs con il codice seguente, quindi immettere valori per `your Service Bus namespace` (nome utente dello spazio dei nomi) e `yourKey`, che corrisponde alla **chiave primaria** in precedenza ottenuta dal portale di Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  A questo punto, assicurarsi che venga chiamato il metodo **inizializzare** . In **Esplora soluzioni**fare doppio clic su **Global.asax\Global.asax.cs**.

5.  Aggiungere la riga di codice seguente alla fine del metodo **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Infine, aggiornare il codice di web creata in precedenza, per inviare gli elementi nella coda. In **Esplora soluzioni**fare doppio clic su **Controllers \ HomeController.cs**.

7.  Aggiornamento di `Submit()` metodo (overload che non accetta parametri) come indicato di seguito per ottenere il numero di messaggi per coda.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Aggiornamento di `Submit(OnlineOrder order)` metodo (overload che utilizza un parametro) come indicato di seguito per inviare informazioni sugli ordini alla coda.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  È ora possibile eseguire l'applicazione. Ogni volta che si invia un ordine, aumenta il numero di messaggi.

    ![][18]

## <a name="create-the-worker-role"></a>Creare il ruolo di lavoro

A questo punto è necessario creare il ruolo di lavoro che elabora l'invio dell'ordine. In questo esempio viene utilizzato il modello di progetto di Visual Studio **Ruolo di lavoro con coda Bus del servizio** . Già stato ottenuto le credenziali necessarie dal portale.

1. Verificare che Visual Studio connessi al proprio account Azure.

2.  In Visual Studio, in **Esplora soluzioni** fare clic sulla cartella **ruoli** sotto il progetto **MultiTierApp** .

3.  Fare clic su **Aggiungi**e quindi fare clic su **Nuovo progetto di ruolo di lavoro**. Viene visualizzata la finestra di dialogo **Aggiungi nuovo ruolo di progetto** .

    ![][26]

4.  Nella finestra di dialogo **Aggiungi nuovo progetto ruolo** fare clic su **Ruolo di lavoro con coda Bus del servizio**.

    ![][23]

5.  Nella casella **nome** assegnare un nome progetto **OrderProcessingRole**. Fare clic su **Aggiungi**.

6.  Copiare la stringa di connessione che è stato ottenuto nel passaggio 9 della sezione "Creare uno spazio dei nomi del servizio Bus" negli Appunti.

7.  In **Esplora soluzioni**, fare clic **OrderProcessingRole** creato nel passaggio 5 (accertarsi rapida **OrderProcessingRole** in **ruoli**e non la classe). Fare clic su **proprietà**.

8.  Nella scheda **Impostazioni** della finestra di dialogo **proprietà** , fare clic all'interno della casella **valore** per **Microsoft.ServiceBus.ConnectionString**e quindi incollare il valore di endpoint copiato nel passaggio 6.

    ![][25]

9.  Creare una classe **OnlineOrder** per rappresentare gli ordini, come le elaborerà dalla coda. È possibile riutilizzare una classe che è già stato creato. In **Esplora**, fare clic sul corso **OrderProcessingRole** (pulsante destro del mouse sull'icona di classe, non il ruolo). Fare clic su **Aggiungi**, quindi fare clic su **Elemento esistente**.

10. Individuare la sottocartella di **FrontendWebRole\Models**e quindi fare doppio clic su **OnlineOrder.cs** per aggiungerlo al progetto.

11. In **WorkerRole.cs**, modificare il valore della variabile **coda** dal `"ProcessingQueue"` a `"OrdersQueue"` come nell'esempio seguente.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Aggiungere la seguente istruzione nella parte superiore del file WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. Nel `Run()` funzione all'interno di `OnMessage()` chiamata, sostituire il contenuto del `try` clausola con il codice seguente.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. È stata completata l'applicazione. È possibile verificare l'applicazione completa tramite il pulsante destro del progetto MultiTierApp in Esplora soluzioni, selezionare **Imposta come progetto di avvio**, quindi premere F5. Si noti che non viene incrementato il numero di messaggi, perché il ruolo di lavoro elabora elementi dalla coda e li contrassegna come completata. È possibile visualizzare l'output di traccia del proprio ruolo lavoro visualizzando l'interfaccia utente emulatore calcolare Azure. È possibile eseguire questa operazione destro del mouse sull'icona emulatore nell'area di notifica della barra delle applicazioni e selezionando **Mostra interfaccia utente di emulatore calcolare**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Passaggi successivi  

Per ulteriori informazioni su Bus di servizio, vedere le risorse seguenti:  

* [Bus di servizio Azure][sbmsdn]  
* [Pagina del servizio Bus di servizio][sbwacom]  
* [Come usare servizio Bus code][sbwacomqhowto]  

Per ulteriori informazioni sugli scenari più livelli, vedere:  

* [Applicazione di multilivello .NET utilizzando BLOB, code e tabelle di archiviazione][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Ottenere gli strumenti e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  